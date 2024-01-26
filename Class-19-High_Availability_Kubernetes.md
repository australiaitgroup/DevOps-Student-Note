# Class-19-High_Availability_Kubernetes

# High Availability
## Definition
High availability is a quality of computing infrastructure that allows it to continue functioning, even when some of 
its components fail. This is important for mission-critical systems that cannot tolerate interruption in service, and
any downtime can cause damage or result in financial loss.

Highly available systems guarantee a certain percentage of uptime—for example, a system that has 99.9% uptime will be
down only 0.1% of the time—0.365 days or 8.76 hours per year. The number of “nines” is commonly used to indicate the
degree of high availability. For example, “five nines” indicates a system that is up 99.999% of the time.

## The basic elements of high availability
The following three elements are essential to a highly available system:

* Redundancy
   * ensuring that any elements critical to system operations have an additional, redundant component that can take
     over in case of failure. We need redundancy to avoid single points of failure.
* Monitoring
   * collecting data from a running system and detecting when a component fails or stops responding.
     
* Failover
   * a mechanism that can switch automatically from the currently active component to a redundant component, 
     if monitoring shows a failure of the active component.

## Technical Components that can help with high availability

### Frontend Related
* DNS
  * Domain Name Server is the URL to IP address mapping and is the key for external customers to access your service.
  * In AWS, you can configure multiple DNS resources in case of emergency
    * https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/dns-failover-configuring.html
* CDN 
  * Content Delivery Network can hold frontend statics files and help fast delivery your content to your customers. 
    This service typically have good HA guarantees.
  * In AWS, you can configure failover to respond on CDN failures 
    * https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/high_availability_origin_failover.html
    
### Backend Related
* Load balancing
  * a load balancer manages traffic, routing it between more than one system that can serve that traffic. 
    The load balancer can be aware that one of the target systems has failed, and redirect traffic to another
    available system, thus implementing monitoring and failover.
  * In AWS, you can use load balancer to help with achieving blue/green deployment to achieve zero downtime
    * https://docs.aws.amazon.com/AmazonECS/latest/developerguide/service-create-loadbalancer-bluegreen.html

* Clustering
  * a cluster contains several nodes that serve a similar purpose, and users typically access and view the
    entire cluster as one unit. Each node in the cluster can potentially failover to another node if failure occurs. 
    By setting up replication within the cluster, you can create redundancy between cluster nodes.
  * In AWS, there are few things that you can consider:
    * multiple ec2 + autoscaling policy
    * multiple caches 
    * persistent queues
    * multiple  
    * multi-AZ
    * multi-region

* Data backup and recovery
  * a system that automatically backs up data to a secondary location, and recovers back to
    the source. This can be used to set up redundancy and failover.
  * In AWS, we apply https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Concepts.MultiAZ.html for RDS

### Open question to discuss
Although there are many things that you can do to improve your product, you may not be able to achieve everything in a
short term/limited money resources. So, which one do we improve first?

## A 5-Step High Availability Checklist
### 1. Define Availability Requirements
   * Identify the cloud workloads that require high availability and their usage patterns.
   
#### Define your availability metrics.

These can include:
* Percentage of Uptime
* Mean Time to Recovery (MTTR)
* Mean Time between Failures (MTBR)
* Recovery Time Objective (RTO)
* Recovery Point Objective (RPO)
  
![Alt text](../images/difference-between-recovery-points.png?raw=true)  
![Alt text](../images/recovery-point-objective-diagram-min.png?raw=true)
  
### 2. Plan your High Availability Architecture

#### Start with a Failure Mode Analysis (FMA)
Identify the types of failure you might experience, the implication of each type of failure, and recovery strategies. 
Based on your FMA, identify the level of redundancy required for each component. Avoid single points of failure and use
load balancing to distribute requests between redundant components.

#### Consider costs
Remember that each redundant layer effectively doubles your cloud costs (at least for the period the redundant 
component is active). Ensure you have licenses and infrastructure to support the additional, redundant instances, 
including storage, networking and bandwidth. 

#### Consider resiliency
Ensure that systems can fail gracefully and restore operations without disruption of service. Isolate critical resources
, use compensating transactions and use asynchronous operations to ensure that if a component fails, business operations
can continue and be applied to a redundant component.

#### Replicate data
Ensure application data is replicated in such a way that supports your redundancy strategy and your RTO and RPO. 
It is not possible to failover or recover if you did not replicate fresh data to the redundant component prior to 
failure. 

#### Document everything
Document the steps that should take place—whether automatic or manual—to failover to a redundant component and recover
or “failback” to the original component. Instructions should be short and clear enough to use in case of emergency.

### 3. Perform End-to-End Testing
To ensure reliability you should test the system under realistic failure conditions. Use fault injection testing to
test different failure scenarios, including a combination of failures, and measure recovery time. 
Test both failover and failback.

#### Additional tests you can conduct to increase your level of confidence
* Identify failures under load
  * perform realistic load testing until a system fails, and observe how failure mechanisms behave. 
* Run disaster recovery exercises
  * conduct a planned or unplanned experiment where systems go down and your team must quickly operate according to
    your disaster recovery plan.
* Test health probes/checks
  * the load balancer uses health probes/checks to identify component failure. Test your probes to ensure they respond
    correctly in case of failure. 
* Test monitoring systems
  * periodically check that data from monitoring systems is accurate, to ensure you can detect failure in time.

### 4. Deploy Applications Consistently
#### Any change can result in failure
   When you provision VMs or other services, deploy new application code and apply configuration changes, the changes
   introduced could result in failure. Having an automated, consistent deployment process can minimize the chance
   of errors and failures, and help you recover more easily. 

#### Consider availability in your release process
   Design your release process to enable updates with minimum disruption of service—try to achieve rolling updates
   that do not require downtime of critical components. You can use blue-green releases or similar strategies
   to have several versions of your production environment available simultaneously and switch between them to move to
   a new version.

#### Plan for rollback
   Design a rollback process that can help you automatically restore systems to a previous working version. 
   Deployments should be automated to allow you to spin up a complete environment representing your “last known good” 
   configuration.

### 5. Monitor Application Health
#### Use probes and check functions to detect failure in time
   Detecting failures in time is critical to high availability. Implement health probes/checks and use check functions
   to get fresh data about service availability. You should always aim to run check functions from outside an
   application. 
#### Watch degrading health metrics
   Don’t only pay attention to complete failure. Degrading health metrics can provide a warning signal that failure is
   about to happen. Create an early warning system by identifying key indicators of application health and alerting
   operators when a system reaches a problematic threshold value.
#### Leverage logging and auditing
   Leverage Azure’s extensive logging and auditing capabilities: use semantic and asynchronous logging, separate
   application logs from audit logs and measure remote call statistics such as latency, throughput and percentage of
   errors.
#### Know your limits
   If you go over the allowed limits of one of your services, you may experience failures. Ensure you are aware of the
   storage, compute, throughput and other limitations of each service you use, monitor for the limited metrics and act
   before you go into overage.
   
ref: https://cloud.netapp.com/blog/azure-high-availability-basic-concepts-and-a-checklist


## SRE fundamentals: SLIs, SLAs and SLOs

### 1. Service-Level Objective (SLO)

####Background
SRE's goal: make sure our service is up and running 24/7 -> Key to success: make sure our availability is 24/7

`Availability`, in SRE terms, 
* defines whether a system is able to fulfill its intended function at a point in time. 
* In addition to being used as a reporting tool, the historical availability measurement can also describe the probability that your system will perform as expected in the future.

We need -> quantify/ set a numerical target for our availability. That target is called SLO.


### 2. Service-Level Agreement (SLA)

An SLA normally involves a promise to someone using your service that its availability SLO should meet a certain level over a certain period, and if it fails to do so then some kind of penalty will be paid. This might be a partial refund of the service subscription fee paid by customers for that period, or additional subscription time added for free. The concept is that going out of SLO is going to hurt the service team, so they will push hard to stay within SLO.

Let us look at a few example

AWS: https://aws.amazon.com/compute/sla/
Google Cloud: https://cloud.google.com/terms/sla/
Jira: https://www.atlassian.com/legal/sla/service-credits


### 3. Service-Level Indicator

We need precise measurements on the key metrics that could help us decide on the service availability percentage.

If it goes below a SLO, we have a problem.
If it goes beyond a SLO too much, we also have a problem.


### 4. How should we set SLI SLO SLA?

Well, you may think shouldn't we set the SLO to 100% then? 

However the fact is:

* The cost for maintaining a 100% SLO is way too high. 

* Even if cost is not an issue, every module in the service need to have redundancy and a way to failover, 
including hardware, networks etc, which makes the maintenance not easy.

Here is a short recipe:
1. Identify the system boundaries within our platform.
2. Identify the customer-facing capabilities that exist at each system boundary.
3. Articulate a plain-language definition of what it means for each capability to be available. ![Alt text](../images/plain_text.png?raw=true)
4. Define one or more SLIs for that definition.
5. Start measuring to get a baseline.
6. Define an SLO for each capability, and track how we perform against it.
7. Iterate and refine our system, and fine tune the SLOs over time.

Leave SLA to the legals or management teams.


## Examples:

###Legacy:

![Alt text](../images/legacy.png?raw=true)

![Alt text](../images/sharevsnonshare.png?raw=true)

###Frontend:

![Alt text](../images/frontend.png?raw=true)




###Hard dependency

![Alt text](../images/network.png?raw=true)

* If something goes wrong in the UI tier, it’ll be an isolated failure that should be easy for us to recover from.
* If our service tier goes down, the UI will be impacted—but we can implement some UI caching to reduce that impact.
* If the data tier goes down, the service tier and UI tier also go down, and the UI can’t recover until both the data tier and service tier come back online.
* If the network tier goes down, everything goes down, and we’ll need recovery time before the system is back online. And since systems don’t come back the instant a dependency recovers, our mean time to recovery (MTTR) increases per tier.

![Alt text](../images/hard_dependency.png?raw=true)


##Managing Risk 
Ref: https://landing.google.com/sre/sre-book/chapters/embracing-risk/

Unreliable systems can quickly erode users’ confidence, so we want to reduce the chance of system failure. However, experience shows that as we build systems, cost does not increase linearly as reliability increments—an incremental improvement in reliability may cost 100x more than the previous increment. The costliness has two dimensions:

###The cost of redundant machine/compute resources
The cost associated with redundant equipment that, for example, allows us to take systems offline for routine or unforeseen maintenance, or provides space for us to store parity code blocks that provide a minimum data durability guarantee.

###The opportunity cost
The cost borne by an organization when it allocates engineering resources to build systems or features that diminish risk instead of features that are directly visible to or usable by end users. These engineers no longer work on new features and products for end users.

In SRE, we manage service reliability largely by managing risk. We conceptualize risk as a continuum. We give equal importance to figuring out how to engineer greater reliability into Google systems and identifying the appropriate level of tolerance for the services we run. Doing so allows us to perform a cost/benefit analysis to determine, for example, where on the (nonlinear) risk continuum we should place Search, Ads, Gmail, or Photos. Our goal is to explicitly align the risk taken by a given service with the risk the business is willing to bear. We strive to make a service reliable enough, but no more reliable than it needs to be. That is, when we set an availability target of 99.99%,we want to exceed it, but not by much: that would waste opportunities to add features to the system, clean up technical debt, or reduce its operational costs. In a sense, we view the availability target as both a minimum and a maximum. The key advantage of this framing is that it unlocks explicit, thoughtful risktaking.

##Measuring Service Risk
As standard practice, we are often best served by identifying an objective metric to represent the property of a system we want to optimize. By setting a target, we can assess our current performance and track improvements or degradations over time. For service risk, it is not immediately clear how to reduce all of the potential factors into a single metric. Service failures can have many potential effects, including user dissatisfaction, harm, or loss of trust; direct or indirect revenue loss; brand or reputational impact; and undesirable press coverage. Clearly, some of these factors are very hard to measure. To make this problem tractable and consistent across many types of systems we run, we focus on unplanned downtime.

For most services, the most straightforward way of representing risk tolerance is in terms of the acceptable level of unplanned downtime. Unplanned downtime is captured by the desired level of service availability, usually expressed in terms of the number of "nines" we would like to provide: 99.9%, 99.99%, or 99.999% availability. Each additional nine corresponds to an order of magnitude improvement toward 100% availability. For serving systems, this metric is traditionally calculated based on the proportion of system uptime (see Time-based availability).

###Time-based availability

```
availability = uptime / ( uptime + downtime )
```

Using this formula over the period of a year, we can calculate the acceptable number of minutes of downtime to reach a given number of nines of availability. For example, a system with an availability target of 99.99% can be down for up to 52.56 minutes in a year and stay within its availability target; see Availability Table for a table.

A time-based metric for availability is usually not meaningful because we are looking across globally distributed services. Our approach to fault isolation makes it very likely that we are serving at least a subset of traffic for a given service somewhere in the world at any given time (i.e., we are at least partially "up" at all times). Therefore, instead of using metrics around uptime, we define availability in terms of the request success rate. Aggregate availability shows how this yield-based metric is calculated over a rolling window (i.e., proportion of successful requests over a one-day window).

###Aggregate availability

```
availability = successful requests / total requests
```
For example, a system that serves 2.5M requests in a day with a daily availability target of 99.99% can serve up to 250 errors and still hit its target for that given day.

In a typical application, not all requests are equal: failing a new user sign-up request is different from failing a request polling for new email in the background. In many cases, however, availability calculated as the request success rate over all requests is a reasonable approximation of unplanned downtime, as viewed from the end-user perspective.

Quantifying unplanned downtime as a request success rate also makes this availability metric more amenable for use in systems that do not typically serve end users directly. Most nonserving systems (e.g., batch, pipeline, storage, and transactional systems) have a well-defined notion of successful and unsuccessful units of work. Indeed, while the systems discussed in this chapter are primarily consumer and infrastructure serving systems, many of the same principles also apply to nonserving systems with minimal modification.

For example, a batch process that extracts, transforms, and inserts the contents of one of our customer databases into a data warehouse to enable further analysis may be set to run periodically. Using a request success rate defined in terms of records successfully and unsuccessfully processed, we can calculate a useful availability metric despite the fact that the batch system does not run constantly.

Most often, we set quarterly availability targets for a service and track our performance against those targets on a weekly, or even daily, basis. This strategy lets us manage the service to a high-level availability objective by looking for, tracking down, and fixing meaningful deviations as they inevitably arise. See Service Level Objectives for more details.


## Error budget and cost

Cost is often the key factor in determining the appropriate availability target for a service. In determining the availability target for each service, we ask questions such as:

If we were to build and operate these systems at one more nine of availability, what would our incremental increase in revenue be?
Does this additional revenue offset the cost of reaching that level of reliability?
To make this trade-off equation more concrete, consider the following cost/benefit for an example service where each request has equal value:

Proposed improvement in availability target: 99.9% → 99.99%
Proposed increase in availability: 0.09%
Service revenue: $1M
Value of improved availability: $1M * 0.0009 = $900


## Auto Scaling Basics
### What is auto scaling?
Auto Scaling helps you ensure that you have the correct number of resources available to handle the load for your application.

### Kubernetes Autoscaling
![Alt text](../images/autoscaling.png?raw=true)

#### 3 autoscaling methods for Kubernetes
* Horizontal Pod Autoscaler
  * Horizontal scaling, which is sometimes referred to as “scaling out,” allows Kubernetes administrators to 
    dynamically (i.e., automatically) increase or decrease the number of running pods as your application’s usage
    changes.
  * With a Horizontal Pod Autoscaler, a cluster operator declares their target usage for metrics, such as CPU or
    memory utilization, number of queued messages, as well their desired maximum and minimum desired number of replicas
* Vertical Pod Autoscaler 
  * “scaling up” sometimes refers to adding more resources (such as CPU or memory) to an existing machine.
  * While scaling horizontally is typically considered a best practice, there are some services that you may want to
    run in your cluster where this is either not possible or not ideal due to some constraint
* Cluster Autoscaler
  * “What happens when load is at a peak and the nodes in the cluster are getting too overloaded with all the newly
    scaled pods?”  
  * As the name indicates, it’s what allows for the autoscaling of the cluster itself, increasing and decreasing the
    number of nodes available for your pods to run on. (A node in Kubernetes lingo is a physical or virtual machine.)


## Solution
### Orchestration

Potential solutions: 
* Kubernetes
* Swarm
* AWS Auto Scaling



### Best Practices

https://techbeacon.com/enterprise-it/5-best-practices-container-orchestration-it-production


#Chaos Engineering

With the rise of microservices and distributed cloud architectures, the web has grown increasingly complex. We all depend on these systems more than ever, yet failures have become much harder to predict.

These failures cause costly outages for companies. The outages hurt customers trying to shop, transact business, and get work done. Even brief outages can impact a company's bottom line, so the cost of downtime is becoming a KPI for many engineering teams. For example, in 2017, 98% of organizations said a single hour of downtime would cost their business over $100,000. One outage can cost a single company millions of dollars. The CEO of British Airways recently explained how one failure that stranded tens of thousands of British Airways (BA) passengers in May 2017 cost the company 80 million pounds ($102.19 million USD).

Companies need a solution to this challenge—waiting for the next costly outage is not an option. To meet the challenge head on, more and more companies are turning to Chaos Engineering.

#Chaos Engineering is Preventive Medicine

Chaos Engineering is a disciplined approach to identifying failures before they become outages. By proactively testing how a system responds under stress, you can identify and fix failures before they end up in the news.

Chaos Engineering lets you compare what you think will happen to what actually happens in your systems. You literally “break things on purpose” to learn how to build more resilient systems.

#A Brief History
Chaos Engineering first became relevant at internet companies that were pioneering large scale, distributed systems. These systems were so complex that they required a new approach to test for failure.

##2010
The Netflix Eng Tools team created Chaos Monkey. Chaos Monkey was created in response to Netflix’s move from physical infrastructure to cloud infrastructure provided by Amazon Web Services, and the need to be sure that a loss of an Amazon instance wouldn’t affect the Netflix streaming experience.

![Alt text](../images/chaos_money.png?raw=true)

##2011
The Simian Army was born. The Simian Army added additional failure injection modes on top of Chaos Monkey that would allow testing of a more complete suite of failure states, and thus build resilience to those as well. “The cloud is all about redundancy and fault-tolerance. Since no single component can guarantee 100% uptime (and even the most expensive hardware eventually fails), we have to design a cloud architecture where individual components can fail without affecting the availability of the entire system” (Netflix, 2011).

##2012
Netflix shared the source code for Chaos Monkey on Github, saying that they “have found that the best defense against major unexpected failures is to fail often. By frequently causing failures, we force our services to be built in a way that is more resilient” (Netflix, 2012).

##2014
Netflix decided they would create a new role: the Chaos Engineer. Bruce Wong coined the term, and Dan Woods shared it with the greater engineering community via Twitter. Dan Woods explained, “I learned more about Chaos Engineering from Kolton Andrus than anyone else, he called it failure injection testing".

In October of 2014, while Gremlin co-founder Kolton Andrus was at Netflix, his team announced Failure Injection Testing (FIT), a new tool that built on the concepts of the Simian Army, but gave developers more granular control over the “blast radius” of their failure injection. The Simian Army tools had been so effective that in some instances they created painful outages, causing many Netflix developers to grow wary of them. FIT gave developers control over the scope of their failure so they could realize the insights of Chaos Engineering, but mitigate potential downside.

#What are the Principles of Chaos Engineering?
Chaos Engineering involves running thoughtful, planned experiments that teach us how our systems behave in the face of failure.

These experiments follow three steps:



1. You start by forming a hypothesis about how a system should behave when something goes wrong.

2. Then, you design the smallest possible experiment to test it in your system.

3. Finally, you measure the impact of the failure at each step, looking for signs of success or failure. When the experiment is over, you have a better understanding of your system's real-world behavior.

#Which companies practice Chaos Engineering?
Many larger tech companies practice Chaos Engineering to better understand their distributed systems and microservice architectures. The list includes Twilio, Netflix, LinkedIn, Facebook, Google, Microsoft, Amazon, and many others. The list is always growing.

But more traditional industries, like banking and finance, have caught on to Chaos Engineering, too. For example, in 2014, the National Australia Bank migrated from physical infrastructure to Amazon Web Services and used Chaos Engineering to dramatically reduce incident counts.

The Chaos Engineering Slack Community has created a diagram that tracks known Chaos Engineering tools and known engineers working on Chaos Engineering.


#How do you plan for your first chaos experiments?
##Planning your First Experiment
One of the most powerful questions in Chaos Engineering is “What could go wrong?”. By asking this question about our services and environments, we can review potential weaknesses and discuss expected outcomes. Similar to a risk assessment, this informs priorities about which scenarios are more likely (or more frightening) and should be tested first. By sitting down as a team and whiteboarding your service(s), dependencies (both internal and external), and data stores, you can formulate a picture of “What could go wrong?”. When in doubt, injecting a failure or a delay into each of your dependencies is a great place to start.

##Creating a Hypothesis
You have an idea of what can go wrong. You have chosen the exact failure to inject. What happens next? This is an excellent thought exercise to work through as a team. By discussing the scenario, you can hypothesize on the expected outcome before running it live. What will be the impact to customers, to your service, or to your dependencies?

##Measuring the Impact
To understand how your system behaves under stress, you need to measure your system’s availability and durability. It is good to have a key performance metric that correlates to customer success (such as orders per minute, or stream starts per second). As a rule of thumb, if you ever see an impact to these metrics, you want to halt the experiment immediately. Next is measuring the failure itself where you want to verify (or disprove) your hypothesis. This could be the impact on latency, requests per second, or system resources. Lastly, you want to survey your dashboards and alarms for unintended side effects.

##Have a Rollback Plan
Always have a backup plan in case things go wrong, but accept that sometimes even the backup plan can fail. Talk through how you’re going to revert the impact. If you’re running commands by hand, be thoughtful not to break ssh or control plane access to your instances. One of the core aspects of Gremlin is safety. All of our attacks can be reverted immediately, allowing you to safely abort and return to steady state if things go wrong.

##Go fix it!
After running your first experiment, hopefully, there is one of two outcomes: either you’ve verified that your system is resilient to the failure you introduced, or you’ve found a problem you need to fix. Both of these are good outcomes. On one hand, you’ve increased your confidence in the system and its behavior, and on the other you’ve found a problem before it caused an outage.

##Have Fun
Chaos Engineering is a tool to make your job easier. By proactively testing and validating your system’s failure modes you will reduce your operational burden, increase your availability, and sleep better at night.

#Why would you break things on purpose?
It's helpful to think of a vaccine or a flu shot where you inject yourself with a small amount of a potentially harmful foreign body in order to prevent illness. Chaos Engineering is a tool we use to build such an immunity in our technical systems by injecting harm (like latency, CPU failure, or network black holes) in order to find and mitigate potential weaknesses.

These experiments have the added benefit of helping teams build muscle memory in resolving outages, akin to a fire drill (or changing a flat tire, in the Netflix analogy). By breaking things on purpose we surface unknown issues that could impact our systems and customers.


# hands_on

link :<https://github.com/JiangRenDevOps/DevOpsNotes/tree/master/WK9_High_Availability_Scaling_K8S/hands_on>


# homework

# EKS
This is the handson to deploy a simple app to EKS. So you can get a feel.

# Prerequisite
Have AWS account

Warning:
- It is not free to use EKS. You pay $0.10 per hour for each Amazon EKS cluster that you create. Also, there is extra pricing if use Fargate.
  https://aws.amazon.com/eks/pricing/

## How to get started

https://docs.aws.amazon.com/eks/latest/userguide/getting-started.html

1. Choose a way to get started:
- Getting started with eksctl (fastest and easiest)
  https://docs.aws.amazon.com/eks/latest/userguide/getting-started-eksctl.html 
  
- Getting started with the AWS Management Console
  https://docs.aws.amazon.com/eks/latest/userguide/getting-started-console.html 

2. Create a cluster using eksctl (Note it may take ~10mins)

3. Deploy app using kubectl

## Install and config ekscli
https://eksctl.io/

- Install https://docs.aws.amazon.com/eks/latest/userguide/eksctl.html
- Config `aws configure` with your own access id and key.
  https://console.aws.amazon.com/iam/home?region=ap-southeast-2#/security_credentials 

## Create a cluster in EKS

1. execute in terminal
```
eksctl create cluster \
--name eks-cluster \
--version 1.17 \
--region ap-southeast-2 \
--nodegroup-name linux-nodes \
--node-type t2.micro \
--nodes 3 \
--ssh-access \
--ssh-public-key <<your own key pair>> \
--managed
```

2. Take a break and wait for about 10mins

3. check EKS cluster after success

## Deploy the app

1. Create a namespace and deploy the sample app
```
kubectl create namespace eks-demo
kubectl apply -f https://k8s.io/examples/application/php-apache.yaml -n eks-demo
```

2. Get the resources
```
kubectl get all -n eks-demo

NAME                              READY   STATUS    RESTARTS   AGE
pod/php-apache-79544c9bd9-7j4nv   1/1     Running   0          77s

NAME                 TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)   AGE
service/php-apache   ClusterIP   10.100.59.226   <none>        80/TCP    77s

NAME                         READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/php-apache   1/1     1            1           77s

NAME                                    DESIRED   CURRENT   READY   AGE
replicaset.apps/php-apache-79544c9bd9   1         1         1       77s
```

3. Inspect there is not external Ip, so we can patch the service with a load balancer

```
kubectl -n eks-demo patch svc php-apache -p '{"spec": {"type": "LoadBalancer"}}'
```

4. Check the service again

```
kubectl get service -n eks-demo
NAME         TYPE           CLUSTER-IP      EXTERNAL-IP                                                                   PORT(S)        AGE
php-apache   LoadBalancer   10.100.59.226   a5ee699eec62a49178cd880a1cc131ab-223434949.ap-southeast-2.elb.amazonaws.com   80:32620/TCP   9m40s
```

5. Check the load balancer link

Q: Why it does not load?

6. Remove the resources and cluster if you do not want to continue.

```
kubectl delete deployment.apps/php-apache -n eks-demo
eksctl delete cluster eks-cluster
```

## What is next?
So the following can be worked as homework if you are interested.

### Install Metrics Server
https://docs.aws.amazon.com/eks/latest/userguide/metrics-server.html 

### Horizontal Pod Autoscaler 
https://docs.aws.amazon.com/eks/latest/userguide/horizontal-pod-autoscaler.html 

### Vertical Pod Autoscaler
https://docs.aws.amazon.com/eks/latest/userguide/vertical-pod-autoscaler.html 

### Cluster Autoscaler
https://docs.aws.amazon.com/eks/latest/userguide/cluster-autoscaler.html

# reading materials

link:<https://github.com/JiangRenDevOps/DevOpsNotes/tree/master/WK9_High_Availability_Scaling_K8S/reading_materials>
