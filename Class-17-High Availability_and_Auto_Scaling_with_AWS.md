# Class-17-High Availability_and_Auto_Scaling_with_AWS


# Creating a Three-Tier Architecture in AWS

## Introduction
A three-tier architecture is a software architecture pattern where the application is broken down into three logical tiers: the presentation layer, the business logic layer and the data storage layer.

This architecture is used in a client-server application such as a web application that has the frontend, the backend and the database. Each of these layers or tiers does a specific task and can be managed independently of each other.

This is a shift from the monolithic way of building an application where the frontend, the backend and the database are both sitting in one place.

![Alt text](../images/multi-tier/3tier-2.png?raw=true)

## What we got from it?

- **Modularity**: The essence of having a three-tier architecture is to modularize our application such that each part can be managed independently of each other. With modularity, teams can focus on different tiers of the application and changes made as quickly as possible. Also, modularization helps us recover quickly from an unexpected disaster by focusing solely on the faulty part.

- **Scalability**: Each tier of the architecture can scale horizontally to support the traffic and request demand coming to it. This can easily be done by adding more EC2 instances to each tier and load balancing across them. For instance, assuming we have two EC2 instances serving our backend application and each of the EC2 instances is working at 80% CPU utilization, we can easily scale the backend tier by adding more EC2 instances to it so that the load can be distributed. We can also automatically reduce the number of the EC2 instances when the load is less.

- **High Availability**: With the traditional data centre, our application is sitting in one geographical location. If there is an earthquake, flooding or even power outage in that location where our application is hosted, our application will not be available. With AWS, we can design our infrastructure to be highly available by hosting our application in different locations known as the availability zones.

- **Fault Tolerant**: We want our infrastructure to comfortably adapt to any unexpected change both to traffic and fault. This is usually done by adding a redundant system that will account for such a hike in traffic when it does occur. So instead of having two EC2 instances working at 50% each, such that when one instance goes bad, the other instance will be working at 100% capacity until a new instance is brought up by our Auto Scaling Group, we have extra instance making it three instances working at approximately 35% each. This is usually a tradeoff made against the cost of setting up a redundant system.

- **Security**: We want to design an infrastructure that is highly secured and protected from the prying eyes of hackers. As much as possible, we want to avoid exposing our interactions within the application over the internet. This simply means that the application will communicate within themselves with a private IP. The presentation (frontend) tier of the infrastructure will be in a private subnet (the subnet with no public IP assigned to its instances) within the VPC. Users can only reach the frontend through the application load balancer. The backend and the database tier will also be in the private subnet because we do not want to expose them over the internet. We will set up the Bastion host for remote SSH and a NAT gateway for our private subnets to access the internet. The AWS security group helps us limit access to our infrastructure setup.

## Workshop Purpose
- Get familiar with how to practise auto-scaling and availability
- Get your hand dirty with AWS fundamentals.

## Prerequisite
Please ensure you have an AWS account.

Warning:
- This is a very heavy workshop so you may get lost in the first time, but you will get used to it very soon.
- When select availability zone, select the one near to us, e.g. Asia Pacific (Sydney)ap-southeast-2.
- There may incur some cost in doing this workshop. So if you don't prepare to spend money, just watch the teacher doing it.
- At the end of this tutorial, you need to stop and delete all the resources such as the EC2 instances, Auto Scaling Group, Elastic Load Balancer etc you set up in case you get charged.

Let's get started!

## Prepare parameters for CFN

We are going to use CloudFormation to create the initial architecture from a dummy app in AWS.

Let us look at it in detail. Refer to `./hands_on/CFN-multi-tier-app.yaml`

Tip: Click View in Designer to check it out.

### Create one VPC and two subnets
- Setup the Virtual Private Cloud (VPC)
    https://console.aws.amazon.com/vpc/home?region=region=ap-southeast-2#vpcs:sort=desc:VpcId

    VPC stands for Virtual Private Cloud (VPC). It is a virtual network where you create and manage your AWS resource in a more secure and scalable manner. Go to the VPC section of the AWS services, and click on the Create VPC button.
    Give your VPC a name and a CIDR block of 10.0.0.0/16

    ![Alt text](../images/multi-tier/vpc.png?raw=true)

    also, we need to enable public dns domain (so later we can check the instances. see more https://docs.aws.amazon.com/vpc/latest/userguide/vpc-dns.html#vpc-dns-support)

    ![Alt text](../images/multi-tier/enable-dns.png?raw=true)

- Setup the Internet Gateway

    The Internet Gateway allows communication between the EC2 instances in the VPC and the internet. To create the Internet Gateway, navigate to the Internet Gateways page and then click on Create internet gateway button.

    We need to attach our VPC to the internet gateway. To do that:

    a. we select the internet gateway

    ![Alt text](../images/multi-tier/ig.png?raw=true)

    b. Click on the Actions button and then select Attach to VPC.

    ![Alt text](../images/multi-tier/attach-vpc.png?raw=true)

    c. Select the VPC to attach the internet gateway and click Attach

    ![Alt text](../images/multi-tier/attach-vpc2.png?raw=true)

- Create two subnets
    https://console.aws.amazon.com/vpc/home?region=region=ap-southeast-2#subnets:sort=desc:tag:Name

    Note: You can give any names, and both of them are public.
    But the CIDR must be within the range of VPC CIDR. So for example, one Subnet CIDR block is 10.0.1.0/24, and the other can be 10.0.0.0/24.
    Here is a tool to check https://www.ipaddressguide.com/cidr
    ![Alt text](../images/multi-tier/public-subnet.png?raw=true)

- Create a public Route Table

    We need to route the traffic to the internet through the internet gateway for our public route table.
    To do that we select the public route table and then choose the Routes tab. The rule should be similar to the one shown below:

    ![Alt text](../images/multi-tier/route-table.png?raw=true)

    After the public route table is created, associate this route table to your two public subnets. You can do this on the subnet config tab "Route Table".

Questions?

    What is VPC and Subnet?
    https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Subnets.html

    What is CIDR?
    Classless inter-domain routing (CIDR) is a set of Internet protocol (IP) standards that is used to create unique identifiers for networks and individual devices. The IP addresses allow particular information packets to be sent to specific computers.

    When you create a VPC, you must specify a range of IPv4 addresses for the VPC in the form of a Classless Inter-Domain Routing (CIDR) block; for example, 10.0.0.0/16. This is the primary CIDR block for your VPC.

    More questions about VPC - https://github.com/michaelsu2014/aws-solution-architect-associate-notes/blob/master/vpc/vpc-core.md

### Configure user key pair
Make sure you have a user key pair to access your EC2 instance
https://console.aws.amazon.com/ec2/v2/home?region=region=ap-southeast-2#KeyPairs:sort=keyName

You can create or import one. See options in https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html

### Configure Security Group

- Create Security Group for EC2 instances.

Make sure you have allowed ssh and http as the inbound rule.
![Alt text](../images/multi-tier/ec2-sg.png?raw=true)

### Configure CFN to use parameters.

- Create a stack in AWS CloudFormation
https://console.aws.amazon.com/cloudformation/home?region=region=ap-southeast-2#/stacks/create/template

use the template in `./hands_on/CFN-multi-tier-app.yaml`

![Alt text](../images/multi-tier/create-cfn.png?raw=true)

- Fill in the parameters required and create the stack

![Alt text](../images/multi-tier/cfn-parameters.png?raw=true)

### Exam what has been created by CFN

- Check the resources in CFN
![Alt text](../images/multi-tier/cfn-resources.png?raw=true)

- Inspect each resource in the console

- Access the LB in the browser.
![Alt text](../images/multi-tier/find-lb.png?raw=true)

  You should be able to see
![Alt text](../images/multi-tier/lb-result.png?raw=true)

- Access the web server in the browser

  Find the public ip from EC2 services.
![Alt text](../images/multi-tier/web-server.png?raw=true)

  You should be able to see
![Alt text](../images/multi-tier/welcom-msg.png?raw=true)

- Remove `multi-tier-ec2-sg` from the web EC2 instances to that they are not open to the public.

- Access the app server in the browser

  Since we have installed Tomcat in the app server, can you access to
  tomcat server `http://<app instance public ip>:8080`?

- Q: What is the result? Do you know why? Do you know how to access to it?

### Inspect the architecture

- What are some good highlights?
    - LB backed by 2 web-tier EC2 instances running Apache.
    - web-tier EC2 instances are created in different availability zones of the region.
    - Security:

       a. VPC and 2 subnets

       b. web-tier EC2 are only accessible via LB, but not exposed publicly.

       c. app-tier is not exposed publicly.

- What are some bad highlights?
    - two web servers may not be enough to handle the load if we have more users
    - single point failure in App server
    - App server lives in public subnet
    - No DB layer

## Add Auto Scaling Group in Web Tier
Right now we have 2 instances running, but it is not enough to handle user load. So we need to create ASG to handle the load automatically.

### Create Launch Template
Use new Launch template before creating ASG.
https://console.aws.amazon.com/ec2/v2/home?region=region=ap-southeast-2#CreateTemplate:

The good things about Launch Template over Launch Configuration are
1) you don't need to specify those again and again when you spin up a EC2
2) you have version control

More on launch template: https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-launch-templates.html

- Create a Launch template

    ami: use : a Free Tier AMI: Amazon Linux 2 AMI (HVM) - Kernel 5.10, SSD Volume Type

    instance type: t2.micro (free-tier)

    key pair name: same as before

    network: enable public ip and select ec2 private security group

    Note: it is only for the convenience of checking at instances in the browser

    ![Alt text](../images/multi-tier/network-interface.png?raw=true)

- Update Launch template

    Notice: the instances don't have apache installed and also we want to check its host and availability zone for testing.

    We have a way to use user data in EC2. Let us update it.
    ![Alt text](../images/multi-tier/add-user-data.png?raw=true)

    Add the following script to user data in Advanced section.

    Note: please copy the code below correctly.

    ```bash
    #!/bin/bash
    yum update -y
    yum install -y httpd
    systemctl start httpd.service
    systemctl enable httpd.service
    EC2_AVAIL_ZONE=$(curl -s http://169.254.169.254/latest/meta-data/placement/availability-zone)
    echo "<h1>Hello World From Multi-Tier Sample App at at $(hostname -f) in AZ $EC2_AVAIL_ZONE </h1>" > /var/www/html/index.html
    ```

    ![Alt text](../images/multi-tier/add-user-script.png?raw=true)

    Set the new template as the default
    ![Alt text](../images/multi-tier/add-user-template.png?raw=true)

### Create ASG

Note: ASG has launched new UI, but you should be able to figure out.

- Create ASG
    https://console.aws.amazon.com/ec2autoscaling/home?region=region=ap-southeast-2#/create

    Select the Launch Template just created
    ![Alt text](../images/multi-tier/asg-template.png?raw=true)

    Select VPC, subnets, healthcheck as ec2, target group
    ![Alt text](../images/multi-tier/It-subnets.png?raw=true)

    Set the intances between 1 and 3 to experiment
    ![Alt text](../images/multi-tier/asg-scale.png?raw=true)

    Review and make sure everything looks correct
    ![Alt text](../images/multi-tier/asg-review.png?raw=true)

- Inspect the instances created by ASG
    ![Alt text](../images/multi-tier/asg-check-instance.png?raw=true)

    You should be able to see
    ![Alt text](../images/multi-tier/It-result.png?raw=true)

- Inspect all instances from the target group directed from LB

![Alt text](../images/multi-tier/lb-all-instances.png?raw=true)

- Change the desired to 2 in ASG and inspect what's going on

![Alt text](../images/multi-tier/asg-desired.png?raw=true)

- Remove the two instances created from the CloudFormation template,
  because we have ASG to control the instance, we can remove the ones created from the CFN template


### Add ASG policy

#### Different policies:

• Target Tracking Scaling

    • Most simple and easy to set-up
    • Example: I want the average ASG CPU to stay at around 40%
• Simple / Step Scaling

    • When a CloudWatch alarm is triggered (example CPU > 70%), then add 2 units
    • When a CloudWatch alarm is triggered (example CPU < 30%), then remove 1
• Scheduled Actions

    • Anticipate a scaling based on known usage patterns
    • Example: increase the min capacity to 10 at 5 pm on Fridays

https://docs.aws.amazon.com/autoscaling/ec2/userguide/as-scale-based-on-demand.html#as-scaling-types

#### ASG Brain Dump
• Scaling policies can be on CPU, Network... and can even be on custom metrics or based on a schedule (if you know your visitors patterns)

• ASGs use Launch configurations or Launch Templates (newer)

• To update an ASG, you must provide a new launch configuration / launch template

• IAM roles attached to an ASG will get assigned to EC2 instances

• ASG are free.You pay for the underlying resources being launched

• Having instances under an ASG means that if they get terminated for whatever reason, the ASG will automatically create new ones as a replacement. Extra safety!

• ASG can terminate instances marked as unhealthy by an LB (and hence replace them)

Note: The following can be the homework if we don't have time.

## Recreate on App Tier

### Create 2 private subnets

A subnet can be public or private. EC2 instances within a public subnet have public IPs and can directly access the internet while those in the private subnet does not have public IPs and can only access the internet through a NAT gateway.

### Create a private route table

private route table will define which subnet goes through the NAT gateway (ie private subnet).

Associate the private route table with the private subnets above

### Create a NAT gateway

**Warning: NAT is Not included in Free Tier!**

The NAT gateway enables the EC2 instances in the private subnet to access the internet. The NAT Gateway is an AWS managed service for the NAT instance. To create the NAT gateway, navigate to the NAT Gateways page, and then click on the Create NAT Gateway.

Please ensure that you know the Subnet ID for a public subnet created before. This will be needed when creating the NAT gateway.

Now that we have the NAT gateway, we are going to edit the private route table to make use of the NAT gateway to access the internet.

### Create ALB in App Tier
https://console.aws.amazon.com/ec2/v2/home?region=region=ap-southeast-2#LoadBalancers:sort=loadBalancerName

- select the two private subnet.
- we only open the port that the backend runs on (eg: port 3000) and the make such port only open to the security group of the frontend. This will allow only the frontend to have access to that port within our architecture.

Reference: https://docs.aws.amazon.com/autoscaling/ec2/userguide/as-register-lbs-with-asg.html

Note:
- ALB relies on the target group to find which instances to redirect
- It takes a few mins to create ALB.

### Create ASG in App Tier
Can you please add ASG in the App Tier, similarly like what we did in Web Tier?

### Bastion Host

The bastion host is just an EC2 instance that sits in the public subnet.

The best practice is to only allow SSH to this instance from your trusted IP.
- Config the security group for the bastion host
To create a bastion host, navigate to the EC2 instance page and create an EC2 instance in the public subnet within our VPC. Also, ensure that it has public IP.
- Config the security group for instances in App Tier by only selecting bastion host ip

## Create DB Tier

### Create RDS in DB Tier
https://console.aws.amazon.com/rds/home?region=region=ap-southeast-2#launch-dbinstance:gdb=false;s3-import=false

Here are some interesting settings:
- Multi-AZ deployment
- Storage autoscaling
- Public Accessibility
- Security Group
    Create a security group with only access from App in our case. e.g.
    ![Alt text](../images/multi-tier/db-sg.png?raw=true)

See more:

https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_VPC.Scenarios.html

https://medium.com/@chamikakasun/an-aws-rds-db-instance-in-a-vpc-accessed-by-an-aws-ec2-instance-in-the-same-vpc-befa659b3dd8

## Other References
https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instancedata-data-retrieval.html#instance-metadata-ex-6

https://stackoverflow.com/questions/26706683/ec2-t2-micro-instance-has-no-public-dns

## CFN-multi-tier-app.yaml

```
AWSTemplateFormatVersion: 2010-09-09
Description: A multi-tier stack instance.
Parameters:
  DeploymentType:
    Description: The deployment type.
    Type: String
    AllowedValues:
      - Development
      - Production
    Default: Development
  VPC:
    Description: The VPC for the EC2 instances.
    Type: 'AWS::EC2::VPC::Id'
  Subnet1:
    Description: The subnet1 for the EC2 instances.
    Type: 'AWS::EC2::Subnet::Id'
  Subnet2:
    Description: The subnet2 for the EC2 instances.
    Type: 'AWS::EC2::Subnet::Id'
  SSHSecurityGroup:
    Description: The SSH Security Group for the EC2 instances.
    Type: 'AWS::EC2::SecurityGroup::Id'
  KeyPair:
    Description: The key pair name to use to connect to the EC2 instances.
    Type: String
Mappings:
  Globals:
    Constants:
#   Free Tier AMI: Amazon Linux 2 AMI (HVM), SSD Volume Type - ami-04f77aa5970939148 (64-bit x86)
      ImageId: ami-04f77aa5970939148
      AssignPublicIP: 'true'
      WebInstanceSuffix: web
      AppInstanceSuffix: app
  DeploymentTypes:
    Development:
      InstanceType: t2.micro
      StorageSize: '20'
#    Setting the production the same config as Dev, because it is for testing only.
#    In real production, we could use instances with a bigger type ans storage size
#    Production:
#      InstanceType: t2.medium
#      StorageSize: '50'
    Production:
      InstanceType: t2.micro
      StorageSize: '20'
Conditions:
  CreateMultipleInstances: !Not
    - !Equals
      - Development
      - !Ref DeploymentType
Resources:
  LoadBalancer:
    Type: 'AWS::ElasticLoadBalancing::LoadBalancer'
    Condition: CreateMultipleInstances
    Properties:
      Instances:
        - !Ref Web1EC2Instance
        - !Ref Web2EC2Instance
      Subnets:
        - !Ref Subnet1
        - !Ref Subnet2
      SecurityGroups:
        - !Ref PublicWebSecurityGroup
      Listeners:
        - LoadBalancerPort: 80
          InstancePort: 80
          Protocol: HTTP
      HealthCheck:
        Target: 'HTTP:80/'
        HealthyThreshold: '3'
        UnhealthyThreshold: '5'
        Interval: '10'
        Timeout: '3'
    Metadata:
      'AWS::CloudFormation::Designer':
        id: fbf8f065-5dc7-4850-bb4f-8c4287a8cb7b
  Web1EC2Instance:
    Type: 'AWS::EC2::Instance'
    Properties:
      ImageId: !FindInMap
        - Globals
        - Constants
        - ImageId
      InstanceType: !FindInMap
        - DeploymentTypes
        - !Ref DeploymentType
        - InstanceType
      NetworkInterfaces:
        - DeviceIndex: '0'
          SubnetId: !Ref Subnet1
          AssociatePublicIpAddress: !FindInMap
            - Globals
            - Constants
            - AssignPublicIP
          GroupSet:
            - !Ref SSHSecurityGroup
            - !If
              - CreateMultipleInstances
              - !Ref PrivateWebSecurityGroup
              - !Ref PublicWebSecurityGroup
      BlockDeviceMappings:
        - DeviceName: /dev/sdm
          Ebs:
            VolumeType: gp2
            VolumeSize: !FindInMap
              - DeploymentTypes
              - !Ref DeploymentType
              - StorageSize
            DeleteOnTermination: 'true'
      KeyName: !Ref KeyPair
      Tags:
        - Key: Name
          Value: !Join
            - '-'
            - - !Ref 'AWS::StackName'
              - !FindInMap
                - Globals
                - Constants
                - WebInstanceSuffix
              - '1'
      UserData: !Base64
        'Fn::Join':
          - ''
          - - |
              #!/bin/bash
            - |
              yum install -y aws-cfn-bootstrap
            - |+

            - |
              # Install the software
            - /opt/aws/bin/cfn-init -v
            - ' --stack '
            - !Ref 'AWS::StackName'
            - ' --resource Web1EC2Instance'
            - ' --configsets Install'
            - ' --region '
            - !Ref 'AWS::Region'
            - |+

            - |+

            - |
              # Signal resource creation completion
            - /opt/aws/bin/cfn-signal -e $?
            - ' --stack '
            - !Ref 'AWS::StackName'
            - ' --resource Web1EC2Instance'
            - ' --region '
            - !Ref 'AWS::Region'
            - |+

    CreationPolicy:
      ResourceSignal:
        Count: 1
        Timeout: PT5M
    DependsOn: AppEC2Instance
    Metadata:
      'AWS::CloudFormation::Designer':
        id: 83fb66e0-bfdf-4076-9d59-3f1077f47a2a
      'AWS::CloudFormation::Init':
        configSets:
          Install:
            - Install
        Install:
          packages:
            yum:
              httpd: []
          files:
            /var/www/html/index.html:
              content: !Join
                - ''
                - - |
                    <html>
                  - |2
                      <head>
                  - |2
                        <title>Welcome to a sample multi-tier app!</title>
                  - |2
                      </head>
                  - |2
                      <body>
                  - |2
                        <h1>Welcome to a sample multi-tier app in Web1!</h1>
                  - |2
                      </body>
                  - |
                    </html>
              mode: '0600'
              owner: apache
              group: apache
          services:
            sysvinit:
              httpd:
                enabled: 'true'
                ensureRunning: 'true'
  Web2EC2Instance:
    Type: 'AWS::EC2::Instance'
    Condition: CreateMultipleInstances
    Properties:
      ImageId: !FindInMap
        - Globals
        - Constants
        - ImageId
      InstanceType: !FindInMap
        - DeploymentTypes
        - !Ref DeploymentType
        - InstanceType
      NetworkInterfaces:
        - DeviceIndex: '0'
          SubnetId: !Ref Subnet2
          AssociatePublicIpAddress: !FindInMap
            - Globals
            - Constants
            - AssignPublicIP
          GroupSet:
            - !Ref SSHSecurityGroup
            - !Ref PrivateWebSecurityGroup
      BlockDeviceMappings:
        - DeviceName: /dev/sdm
          Ebs:
            VolumeType: gp2
            VolumeSize: !FindInMap
              - DeploymentTypes
              - !Ref DeploymentType
              - StorageSize
            DeleteOnTermination: 'true'
      KeyName: !Ref KeyPair
      Tags:
        - Key: Name
          Value: !Join
            - '-'
            - - !Ref 'AWS::StackName'
              - !FindInMap
                - Globals
                - Constants
                - WebInstanceSuffix
              - '2'
      UserData: !Base64
        'Fn::Join':
          - ''
          - - |
              #!/bin/bash
            - |
              yum install -y aws-cfn-bootstrap
            - |+

            - |
              # Install the software
            - /opt/aws/bin/cfn-init -v
            - ' --stack '
            - !Ref 'AWS::StackName'
            - ' --resource Web2EC2Instance'
            - ' --configsets Install'
            - ' --region '
            - !Ref 'AWS::Region'
            - |+

            - |+

            - |
              # Signal resource creation completion
            - /opt/aws/bin/cfn-signal -e $?
            - ' --stack '
            - !Ref 'AWS::StackName'
            - ' --resource Web2EC2Instance'
            - ' --region '
            - !Ref 'AWS::Region'
            - |+

    CreationPolicy:
      ResourceSignal:
        Count: 1
        Timeout: PT5M
    DependsOn: AppEC2Instance
    Metadata:
      'AWS::CloudFormation::Designer':
        id: 4e1f2401-833d-442d-be04-89fac2d74778
      'AWS::CloudFormation::Init':
        configSets:
          Install:
            - Install
        Install:
          packages:
            yum:
              httpd: []
          files:
            /var/www/html/index.html:
              content: !Join
                - ''
                - - |
                    <html>
                  - |2
                      <head>
                  - |2
                        <title>Welcome to a sample multi-tier app!</title>
                  - |2
                      </head>
                  - |2
                      <body>
                  - |2
                        <h1>Welcome to a sample multi-tier app in Web2!</h1>
                  - |2
                      </body>
                  - |
                    </html>
              mode: '0600'
              owner: apache
              group: apache
          services:
            sysvinit:
              httpd:
                enabled: 'true'
                ensureRunning: 'true'
  AppEC2Instance:
    Type: 'AWS::EC2::Instance'
    Properties:
      ImageId: !FindInMap
        - Globals
        - Constants
        - ImageId
      InstanceType: !FindInMap
        - DeploymentTypes
        - !Ref DeploymentType
        - InstanceType
      NetworkInterfaces:
        - DeviceIndex: '0'
          SubnetId: !Ref Subnet1
          AssociatePublicIpAddress: !FindInMap
            - Globals
            - Constants
            - AssignPublicIP
          GroupSet:
            - !Ref SSHSecurityGroup
            - !Ref AppSecurityGroup
      BlockDeviceMappings:
        - DeviceName: /dev/sdm
          Ebs:
            VolumeType: gp2
            VolumeSize: !FindInMap
              - DeploymentTypes
              - !Ref DeploymentType
              - StorageSize
            DeleteOnTermination: 'true'
      KeyName: !Ref KeyPair
      Tags:
        - Key: Name
          Value: !Join
            - '-'
            - - !Ref 'AWS::StackName'
              - !FindInMap
                - Globals
                - Constants
                - AppInstanceSuffix
              - '1'
      UserData: !Base64
        'Fn::Join':
          - ''
          - - |
              #!/bin/bash
            - |
              yum install -y aws-cfn-bootstrap
            - |+

            - |
              # Install the software
            - /opt/aws/bin/cfn-init -v
            - ' --stack '
            - !Ref 'AWS::StackName'
            - ' --resource AppEC2Instance'
            - ' --configsets Install'
            - ' --region '
            - !Ref 'AWS::Region'
            - |+

            - |+

            - |
              # Signal resource creation completion
            - /opt/aws/bin/cfn-signal -e $?
            - ' --stack '
            - !Ref 'AWS::StackName'
            - ' --resource AppEC2Instance'
            - ' --region '
            - !Ref 'AWS::Region'
            - |+

    CreationPolicy:
      ResourceSignal:
        Count: 1
        Timeout: PT5M
    Metadata:
      'AWS::CloudFormation::Designer':
        id: 4720705c-1add-4c63-abd6-d9fd4626a43d
      'AWS::CloudFormation::Init':
        configSets:
          Install:
            - Install
        Install:
          packages:
            yum:
              tomcat: []
              tomcat-webapps: []
          services:
            sysvinit:
              tomcat:
                enabled: 'true'
                ensureRunning: 'true'
  PublicWebSecurityGroup:
    Type: 'AWS::EC2::SecurityGroup'
    Properties:
      GroupName: !Join
        - '-'
        - - !Ref 'AWS::StackName'
          - public-web-sg
      GroupDescription: !Join
        - ''
        - - 'Enables public web access for '
          - !Ref 'AWS::StackName'
          - .
      VpcId: !Ref VPC
      SecurityGroupIngress:
        - IpProtocol: tcp
          FromPort: '80'
          ToPort: '80'
          CidrIp: 0.0.0.0/0
      Tags:
        - Key: Name
          Value: !Join
            - '-'
            - - !Ref 'AWS::StackName'
              - public-web-sg
    Metadata:
      'AWS::CloudFormation::Designer':
        id: bfef096b-3b53-4799-b880-0df21011e7ed
  PrivateWebSecurityGroup:
    Type: 'AWS::EC2::SecurityGroup'
    Properties:
      GroupName: !Join
        - '-'
        - - !Ref 'AWS::StackName'
          - private-web-sg
      GroupDescription: !Join
        - ''
        - - 'Enables private web access for '
          - !Ref 'AWS::StackName'
          - .
      VpcId: !Ref VPC
      SecurityGroupIngress:
        - IpProtocol: tcp
          FromPort: '80'
          ToPort: '80'
          SourceSecurityGroupId: !Ref PublicWebSecurityGroup
      Tags:
        - Key: Name
          Value: !Join
            - '-'
            - - !Ref 'AWS::StackName'
              - private-web-sg
    Metadata:
      'AWS::CloudFormation::Designer':
        id: f00bae41-3fe2-41c1-ad14-64680744f71f
  AppSecurityGroup:
    Type: 'AWS::EC2::SecurityGroup'
    Properties:
      GroupName: !Join
        - '-'
        - - !Ref 'AWS::StackName'
          - app-sg
      GroupDescription: !Join
        - ''
        - - 'Enables access to '
          - !Ref 'AWS::StackName'
          - ' app tier.'
      VpcId: !Ref VPC
      SecurityGroupIngress:
        - IpProtocol: tcp
          FromPort: '8080'
          ToPort: '8080'
          SourceSecurityGroupId: !If
            - CreateMultipleInstances
            - !Ref PrivateWebSecurityGroup
            - !Ref PublicWebSecurityGroup
      Tags:
        - Key: Name
          Value: !Join
            - '-'
            - - !Ref 'AWS::StackName'
              - app-sg
    Metadata:
      'AWS::CloudFormation::Designer':
        id: adf9b8b7-25c4-4ddb-9401-e5c34da55511
Outputs:
  StackURL:
    Description: The stack web URL.
    Value: !Join
      - ''
      - - 'http://'
        - !If
          - CreateMultipleInstances
          - !GetAtt
            - LoadBalancer
            - DNSName
          - !GetAtt
            - Web1EC2Instance
            - PublicIp
Metadata:
  'AWS::CloudFormation::Designer':
    fbf8f065-5dc7-4850-bb4f-8c4287a8cb7b:
      size:
        width: 60
        height: 60
      position:
        x: 560
        'y': 50
      z: 0
      embeds: []
      isassociatedwith:
        - 83fb66e0-bfdf-4076-9d59-3f1077f47a2a
        - 4e1f2401-833d-442d-be04-89fac2d74778
        - bfef096b-3b53-4799-b880-0df21011e7ed
    83fb66e0-bfdf-4076-9d59-3f1077f47a2a:
      size:
        width: 60
        height: 60
      position:
        x: 470
        'y': 150
      z: 0
      embeds: []
      dependson:
        - 4720705c-1add-4c63-abd6-d9fd4626a43d
    4e1f2401-833d-442d-be04-89fac2d74778:
      size:
        width: 60
        height: 60
      position:
        x: 650
        'y': 150
      z: 0
      embeds: []
      dependson:
        - 4720705c-1add-4c63-abd6-d9fd4626a43d
    bfef096b-3b53-4799-b880-0df21011e7ed:
      size:
        width: 60
        height: 60
      position:
        x: 210
        'y': 100
      z: 0
      embeds: []
    4720705c-1add-4c63-abd6-d9fd4626a43d:
      size:
        width: 60
        height: 60
      position:
        x: 553
        'y': 265
      z: 0
      embeds: []
    f00bae41-3fe2-41c1-ad14-64680744f71f:
      size:
        width: 60
        height: 60
      position:
        x: 660
        'y': 270
      z: 1
      embeds: []
    adf9b8b7-25c4-4ddb-9401-e5c34da55511:
      size:
        width: 60
        height: 60
      position:
        x: 540
        'y': 360
      z: 1
      embeds: []
```



