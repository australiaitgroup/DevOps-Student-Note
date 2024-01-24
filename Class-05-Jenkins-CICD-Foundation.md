

- [课堂笔记](#课堂笔记)
  - [GOAL of this class](#goal-of-this-class)
  - [1 CI / CD](#1-ci--cd)
    - [1.1 Continuous Integration(CI)](#11-continuous-integrationci)
    - [1.2 Continuous Delivery (CD)](#12-continuous-delivery-cd)
  - [2 Benefits of CI/CD](#2-benefits-of-cicd)
  - [3 CI/CD Tools](#3-cicd-tools)
  - [4 Jenkins History](#4-jenkins-history)
  - [5 Jenkins Core Concept](#5-jenkins-core-concept)
  - [6 Jenkins Pipeline](#6-jenkins-pipeline)
  - [7 Jenkins Configuration Management](#7-jenkins-configuration-management)
  - [8 Install a Jenkins Server](#8-install-a-jenkins-server)
  - [9.Homework and Project Works](#9homework-and-project-works)
 
# 课堂笔记
## GOAL of this class
 Setup Jenkins CI pipeline
## 1 CI / CD
### 1.1 Continuous Integration(CI)
Continuous Integration is a practice where development teams frequently commit
application code changes to a shared repository. These changes automatically
trigger new builds which are then validated by automated testing to ensure that
they do not break any functionality
![CI image](image/c0601.png)
- Software build tools
  
  https://www.plutora.com/ci-cd-tools/software-build-tools

- Traditional way of testing
  ![manual testing](image/c0602.png)

- Automated testing
  ![automated tetsing](image/c0603.png)

- benefits of automated testing
  ![benefits](image/c0604.png)

- relative cost of fixing defects in different stage of software developement
  ![cost](image/c0605.png)

- Testing Pyramid
  ![testing](image/c0606.png)

- Testing process
  ![process](image/c0607.png)

### 1.2 Continuous Delivery (CD)
CD is also used to describe Continuous Deployment which focuses on the
automation process to release what is now a fully functional build into production.
![CD](image/c0608.png)
- Deployment
  ![Deployment](image/c0609.png)
- Deployment environments
  ![Deployment environments](image/c0610.png)
## 2 Benefits of CI/CD
1. Smaller Code Changes
2. FaultIsolations
3. Faster Mean TimeToResolution (MTTR)
4. MoreTestReliability
5. FasterRelease Rate
6. Smaller Backlog
7. CustomerSatisfaction
8. IncreaseTeamTransparency andAccountability
9. Reduce Costs
10. Easy Maintenance andUpdates

    Ref: https://www.katalon.com/resources-center/blog/benefits-continuous-integration-delivery
## 3 CI/CD Tools

![tools](image/c0611.png)

Some big companys build their own tools for internal use.

## 4 Jenkins History
  The leading open source automation server, Jenkins provides hundreds of plugins to support building, deploying and automating any project.
- 2004 - Kohsuke Kawaguchi created Hudson in
  Sun Microsystem
- 2005 - Hudson is released
- 2011 - Hudson renamed to Jenkins after Oracle
  purchased Sun Microsystem
- 2010 - CloudBees is created
- 2014 - CloudBees shifts focus to Enterprise
  Jenkins and provide commercial support to Jenkins.
- 2014 - Kawaguchi becomes CTO of CloudBees
## 5 Jenkins Core Concept
  ![Core Concept](image/c0612.png)

    Jenkins Job
    Jenkins Build
    Jenkins Plugin
    Jenkins View (Dashboard View)
    Jenkins Node: Master/Agent
    Jenkins Credential
    Jenkins Configuration
    Jenkins Logs
    Jenkins SSO and Global Security
    Jenkins Pipeline


## 6 Jenkins Pipeline
What is Jenkins Pipeline?

Jenkins Pipeline is a combination of plugins that supports integration and implementation of continuous delivery pipelines. It has an extensible automation server to create simple and complex delivery pipelines as code via pipeline DSL. A Pipeline is a group of events interlinked with each other in a sequence.

![pipeline](image/c0614.png)
- Jenkins 有两类config.xml
  
  在Jenkins home之下的config.xml是Jenkins server的配置文件。
  另外在每个job或project自己的目录下，还有一个config.xml。它包含了是这个job的配置信息。
  ![pipeline](image/c0618.png)

- Jekinsfile

  Jenkins pipelines can be defined using a text file called JenkinsFile. You can implement pipeline as code using JenkinsFile, and this can be defined by using a domain specific language (DSL). With JenkinsFile, you can write the steps needed for running a Jenkins pipeline.
  ![pipeline](image/c0619.png)
  ![pipeline](image/c0616.png)
  ![pipeline](image/c0615.png)
  ![pipeline](image/c0613.png)
  ![pipeline](image/c0617.png)

- Jenkins Pipeline - in real world
  
![real world pipeline](image/c0620.png)

## 7 Jenkins Configuration Management
We configured Github Pipeline and Plugins manually in Jenkins. This should be
automated.
There are a few ways to automate this.
1. Dockerise Jenkins and add the configuration into Dockerfile - https://github.com/
jenkinsci/docker/blob/master/README.md
2. Use configuration management tool to configure Jenkins
Jenkins doesn’t use any database and uses plain file system. We should backup
Jenkins configuration files and logs as well.
There are also a few ways.
1. Use backup plugins to backup
2. Backup file system regularly
## 8 Install a Jenkins Server
- Common options to install Jenkins
  1. Official - https://jenkins.io - java -jar jenkins.war
  2. Docker - https://hub.docker.com/r/jenkins/jenkins/
  3. Kubernetes: for large scale paralell tasks
      - Kubernetes application
      - helm
        
        helm install --name jenkins stable/jenkins --set rbac.install=true \
        --set Persistence.Enabled=true \
        --set Persistence.StorageClass=jenkins-pv
  4. Configuration Management Tool: Chef, Puppet,Ansible or Saltstack
  5. Use Jenkins in the Cloud: CloudBees, bitnami

- Hands-on:install Jenkins in docker locally

  repo:  https://github.com/sean4wsome/jrcms
  - Handson 1 - Jenkins installation: 
    https://github.com/JiangRenDevOps/DevOpsLectureNotesV4/tree/main/WK3_CI-CD-Jekins/1.Install-Jenkins-Docker
  - Handson 2 - integrate Jenkins with a Github Orgnisation:
    https://github.com/JiangRenDevOps/DevOpsLectureNotesV4/tree/main/WK3_CI-CD-Jekins/3.Integrate-with-Github
  - Handson 3 - exposing Jekins to internet:
    https://github.com/JiangRenDevOps/DevOpsLectureNotesV4/tree/main/WK3_CI-CD-Jekins/2.Expose-Jenkins-to-Public-Internet
  - Handson 4 - build Jenkins pipeline using Blue Ocean interface：
    https://github.com/JiangRenDevOps/DevOpsLectureNotesV4/tree/main/WK3_CI-CD-Jekins/5.Jenkins-BlueOcean-Pipeline

  - Handson for next lesson - Install-Jenkins-Kubernetes
    https://github.com/JiangRenDevOps/DevOpsLectureNotesV4/tree/main/WK3_CI-CD-Jekins/4.Install-Jenkins-Kubernetes

- more reference for jenkins pipeline 
  Jenkins pipeline uses Groovy language. (similarly in Teamcity and Bamboo) 
  https://jenkins.io/doc/book/pipeline/
  https://jenkins.io/doc/book/pipeline/pipeline-best-practices/
  https://github.com/davisliu11/jenkins-pipeline-examples

## 9.Homework and Project Works
To get the most out of this course,
please do take some efforts to complete the homework and project works. Try to do more practice with the examples from the reference links.
It will help you to understand the content better and
some tasks may be a prerequisite of the following contents.





 -----------------------------------------------------------------------------------


 # WK4.1
 
 ## 1.Install-Jenkins-Docker



 # Jenkins Basic

## About Jenkins

Jenkins is a powerful application that allows continuous integration and continuous delivery of projects, regardless of the platform you are working on. It's a popular tool for performing continuous integration of software projects.

![Alt text](images/why_jenkins.jpg?raw=true)

Jenkins is a free source that can handle any kind of automated build or continuous integration. You can integrate Jenkins with a number of testing and deployment technologies.

## About CI/CD

First of all, let's review the advantages of continuous integration:

1. reduce the labor force
2. avoid human error
3. improve efficiency
4. continuous feedback on quality
5. quality assurance

Second, to use Jenkins for continuously integration, you should have knowledge including:

* Linux
* Git
* Jenkins
* Maven
* JDK
* Other programming tools

### Continuous Integration

Continuous Integration (CI) helps developers integrate code into a shared repository by automatically verifying the build using unit tests and packaging the solution each time new code changes are submitted.

For example, setting up a CI pipeline for Continuous Integration with a SharePoint Framework solution requires the following steps:

1. Creating the Build Definition
2. Installing NodeJS
3. Restoring dependencies
4. Executing Unit Tests
5. Importing test results
6. Importing code coverage information
7. Bundling the solution
8. Packaging the solution
9. Preparing the artifacts
10. Publishing the artifacts

![Alt text](images/jenkins_ci.webp?raw=true)

### Continuous Deployment

Continuous Deployment (CD) takes validated code packages from build process and deploys them into a staging or production environment. Developers can track which deployments were successful or not and narrow down issues to specific package versions.

Setting up a CD pipeline for Continuous Deployments with a SharePoint Framework solution requires the following steps:

1. Creating the Release Definition
2. Linking the Build Artifact
3. Creating the Environment
4. Installing NodeJS
5. Installing the CLI for Microsoft 365
6. Connecting to the App Catalog
7. Adding the Solution Package to the App Catalog
8. Deploying the Application
9. Setting the Variables for the Environment

![Alt text](images/jenkins_cd.webp?raw=true)

### CI/CD with third-party solutions/services

You can also use the Jenkins open-source automation server to deploy AWS CodeBuild artifacts with AWS CodeDeploy, creating a functioning CI/CD pipeline. When properly implemented, the CI/CD pipeline is triggered by code changes pushed to your GitHub repo, automatically fed into CodeBuild, then the output is deployed on CodeDeploy.

![Alt text](images/jenkins_aws_cicd.png?raw=true)

## Install Jenkins on Docker

This is to guide how to install Jenkins and run it in a Docker container.

### Pre-requisite

* Software: Docker

### Steps

#### 1. Create a folder to hold Jenkins data

```bash
mkdir jenkins_home
cd jenkins_home
```

#### 2. Start a Jenkins container

```bash
docker run --name jenkins \
           -u root \
           -d \
           -v $(pwd):/var/jenkins_home \
           -v /var/run/docker.sock:/var/run/docker.sock \
           -p 80:8080 \
           -p 50000:50000 \
           jenkins/jenkins:lts-jdk11
```

> Note:
>
> * `-u root` configures to run Jenkins by root user.
> * `-d` detaches the container
> * `-v $(pwd):/var/jenkins_home` mounts the current directory to Jenkins home inside the container
> * `-v /var/run/docker.sock:/var/run/docker.sock` mounts Docker socks
> * `-p 80:8080` maps port 80 in the host to port 8080 inside the container
> * `-p 50000:50000` maps ports 50000 which is the default port for angent registration
> * `jenkins/jenkins:lts-jdk11` is the image maintained by `jenkin`.
> * If you have error with port 80, please change it to 8080 or other port numbers.

#### 3. Open <http://127.0.0.1> and you should see screen below

![Alt text](images/docker-install-01.png?raw=true)

#### 4. Get your password by opening `secrets/initialAdminPassword` file in the directory you created in the first step

```bash
sudo cat secrets/initialAdminPassword
```
You can also get the initial password from your Docker container log, or you can bash into your Docker container and get it from inside:

To see the docker container id

```bash
docker container ls 
```
Then enter in bash inside a container.

```bash
docker exec -it CONTAINER_ID bash
```

#### 5. Continue installation in step #3 with password from step #4

You should see screen below after full installation.
![Alt text](images/docker-install-02.png?raw=true)


## 2.Expose-Jenkins-to-Public-Internet

# Description

This is to demo how to expose a server inside the firewall to public internet via ngrok

## Pre-requisite

- A server to expose (Jenkins in this example)

## Steps

### Step 1: Login to ngrok via Github or Google

<https://dashboard.ngrok.com/login>

![Alt text](images/expose-jenkins-to-public-internet-01.png?raw=true)

### Step 2: Follow docs to install ngrok client and setup authtoken

To download in Ubuntu, you can use following commnad:

```bash
curl -O <url>
```

Then grant execute permission to the downloaded file:

```bash
chmod +x ngrok-stable-linux-amd64.tgz
```

Next, let's unzip it (Ubuntu):

```bash
tar zxvf ngrok-stable-linux-amd64.tgz
```

Now we need to authenticate:

```bash
ngrok authtoken {YOUR_TOKEN}
```

![Alt text](images/expose-jenkins-to-public-internet-02.png?raw=true)

### Step 3: Expose Jenkins to public internet

```bash
ngrok http 80
```

> NOTE:
> You should specify the actual port used for your Docker container.

![Alt text](images/expose-jenkins-to-public-internet-03.png?raw=true)

### Step 4: You should be able to access Jenkins in the public internet

![Alt text](images/expose-jenkins-to-public-internet-04.png?raw=true)

### Step 5: Change "Jenkins URL" to the public URL

![Alt text](images/expose-jenkins-to-public-internet-05.png?raw=true)


## 3.Integrate-with-Github

# Description

This is to demo how to integrate Jenkins with a Github Orgnisation

## Pre-requisite

- Github Account
- Jenkins Server
- Fork <https://github.com/JiangRenDevOps/hello-Jenkinsfile> into your github account.

## Task

### Step 1: Go to Jenkins Dashboard and click on the “New Item ” link to create a new job highlighted in the red rectangle

![Alt text](images/integrate-with-github-org-01.png?raw=true)

### Step 2: Now do the following steps further for a selection of project

![Alt text](images/6-Selection-of-Project.jpeg?raw=true)

### Step 3: Now just scroll down and go to the Source Code Management section. Now, select the “Git ” option

![Alt text](images/8-Jenkins-github-integration-Source-Code-Management-section-using-Git-Plugin.jpeg?raw=true)

### Step 4: Now enter the repository URL as shown in the below image

![Alt text](images/9-Jenkins-github-integration-Configure-GitHub-repository-in-Jenkins-Build.png?raw=true)

### Step 5:  Now, go to the Build triggers section and select the option “GitHub hook trigger for GITScm polling”

![Alt text](images/gitscm.png?raw=true)

## How do I trigger a build automatically in Jenkins?

### Step 1: Go to the GitHub repository and click on the Settings link highlighted by the red rectangle

![Alt text](images/13-click-on-settings-button.png?raw=true)

### Step 2: Click on the Webhooks option listed and as highlighted below

![Alt text](images/14-Jenkins-github-integration-Click-on-webhooks.png?raw=true)

### Step 3:  As soon as we will click on Webhooks, we will redirect to the Webhooks page. Now, click on the “Add webhook”  button highlighted in the below image

This webhook will notify Jenkins when there is a push, PR or repository created.
![Alt text](images/15-Jenkins-github-integration-Add-webhook-in-GitHub.png?raw=true)

### Step 4: Now perform the below steps to setup webhooks in GitHub

- Put the Payload URL in the textbox. Kindly note that doesn’t forget to append text `/github-webhook/` at the last.
- Click on the “Just the push event” option.
- Please make sure that you check the “Active” checkbox.
- Click on the “Add webhook”  button.
![Alt text](images/19-Jenkins-github-integration-Webhook-configuration.png?raw=true)

### Step 5: Create a new branch

![Alt text](images/integrate-with-github-org-11.png?raw=true)

### Step 6: Modify README.md or add a file to the repo

![Alt text](images/integrate-with-github-org-13.png?raw=true)

### Step 7: In Jenkins, you should see a new build is triggerred

![Alt text](images/integrate-with-github-org-14.png?raw=true)

### Step 8: Create a PR

![Alt text](images/integrate-with-github-org-15.png?raw=true)

### Step 9: In Jenkins, you should see a new build is triggerred

![Alt text](images/21-Build-automatically-triggered.png?raw=true)

### Step 10: Use Jenkins Pipeline rather than Freestyle so you can execute the Jenkinsfile

> Note: any branch is */*.




# 1.use freestyle project

-Jenkins will build your project combining any SCM(SOURCE CODE MANAGEMENT Like github) with any build system

Jenkins pipeline <---------> github

Two options:

-1. click 'Build Now' , manually trigger
-2. automatically trigger--- go to configure--->build triggers--> tick 'Git hub hook trigger for gitscm polling'
   -github---------> Jenkins  (using ngrok, xxx.ngrok-free-app)
  note: in Jenkins there is github hook plugin already installed,  if someone hit and visit this url : jenkins_url/webhook, automatically set up a switch
  in github we setup switch,  go to github setting ----> general ----> webhooks--->add webhook-->put jenkins url into payload URL( jenkins ngrok url+ /github-webhook/)
  for testing ----add some changes in test file and commit ----we can see triggers automatically at my-first-pipeline in Jenkins

  source code management:  listen to all branchs or  only listen to main/master 
  
  ## setting up schedule: every day /every night/ add comments

  ## console output (what has been done): workspace files in jenkins <------> in CLI under path workspace  inside (Jenkins-----jenkins_home----my-first-pipeline)
  1. jenkins ---get ---all source code from github
  2. Jenkinsfile : CICD PIPELINE DEFINITION ----SIMILAR TO JSON format
    -  agent: any   (angents: windows, macos,linux,... )
    -  stage 1,2,3    build(CI)---test(Test)---deploy (CD)
    -  echo  is linux command  for example: echo "building"  (similart to command print)
  3. as a Devops we often write scripts

  # 2. use Pipeline
      - long-running activities
      - multiple build agents for example:  Microsoft Teams  ----> windows/web/macOS/ios/android   
      - suitable for building  pipelines (formerly known as workflows)
## My-First-Pipeline
# Getting started with Jenkinsfile

A `Jenkinsfile` is a text file that contains the definition of a Jenkins Pipeline and is checked into source control. Consider the following Pipeline which implements a basic three-stage continuous delivery pipeline.


### This is script file shown below  or you put it into inline section in github

```jenkinsfile
pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                echo 'Building..'
            }
        }
        stage('Test') {
            steps {
                echo 'Testing..'
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying....'
            }
        }
    }
}
```

Not all Pipelines will have these same three stages, but it is a good starting point to define them for most projects.

Using a text editor, ideally one which supports Groovy syntax highlighting, create a new `Jenkinsfile` in the root directory of the project.

![Alt text](images/groovy-vscode.png)

## Create your frist pipeline

A Pipeline can be created in one of the following ways:

- Through Blue Ocean - after setting up a Pipeline project in Blue Ocean, the Blue Ocean UI helps you write your Pipeline’s `Jenkinsfile` and commit it to source control.

- Through the classic UI - you can enter a basic Pipeline directly in Jenkins through the classic UI.

- In SCM - you can write a `Jenkinsfile` manually, which you can commit to your project’s source control repository.

Here is our first Pipeline created through the Jenkins classic UI:

![Alt text](images/first-pipeline.png)

Complex Pipelines are difficult to write and maintain within the classic UI’s Script text area of the Pipeline configuration page.

To make this easier, your Pipeline’s `Jenkinsfile` can be written in a text editor or integrated development environment (IDE) and committed to source control. This is usually done by developers. Jenkins can then check out your Jenkinsfile from source control as part of your Pipeline project’s build process and then proceed to execute your Pipeline.

![Alt text](images/first-scm-pipeline.png)

When you update the designated repository, a new build is triggered, as long as the Pipeline is configured with an SCM polling trigger.

## How to workout my Jenkinsfile?

Read documents is an essential skill to DevOps Engineer. Please read this doc to learn more about `Jenkinsfile`: <https://www.jenkins.io/doc/book/pipeline/jenkinsfile/>

To learn more details about Jenkins pipeline, please read: <https://www.jenkins.io/doc/book/pipeline/getting-started/>





  
   
