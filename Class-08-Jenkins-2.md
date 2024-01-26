# Class-08-Jenkins-2

# Topics:
## 1.Installation on Ubuntu
## 2.Jenkins Basic Configurations
## 3.Jenkins Basic Management
## 4.Hands on a Simple Pipeline
## 5.Hands-On
## 6.Solutions
## 7.Multibranch Pipeline



#  1.Installation on Ubuntu

Using Azure Cloud service

## Prerequisites

A physical/virtual machine meeting with minimum hardware requirements:

* **256 MB** of RAM

* **1 GB** of drive space (although **10 GB** is a recommended minimum if running Jenkins as a Docker container)

Recommended hardware configuration for a small team:

* **4 GB+** of RAM

* **50 GB+** of drive space

Comprehensive hardware recommendations:

* Hardware: see the [Hardware Recommendations](https://www.jenkins.io/doc/book/scaling/hardware-recommendations) page

> NOTE: Before proceeding with this tutorial, make sure that you are logged in as a user with sudo privileges.

To install Jenkins on an Ubuntu system, perform the following steps:

### Installation of Java

Since Jenkins is a Java application, the first step is to install Java.

There are multiple Java implementations which you can use. OpenJDK is the most popular one at the moment, we will use it in this guide.

Update the package index and install the  Open Java Development Kit (OpenJDK) package using the following command:

```bash
sudo apt update
sudo apt install openjdk-11-jdk
```

You need to hit **``Y``** to confirm and continue.

If you have more than one java version installed on your computer, make sure your default Java package version is supported by Jenkins.

## Add apt repository

Jenkins has two release for Ubuntu: **LTS (Long-Term Support) release** and **Weekly release**. Both can be installed through ``apt``.

**A LTS (Long-Term Support) release** is chosen every 12 weeks from the stream of regular releases as the stable release for that time period. It can be installed from the ``debian-stable apt`` [repository](https://pkg.jenkins.io/debian-stable/).

**Weekly release** is produced weekly to deliver bug fixes and features to users and plugin developers. It can be installed from the ``debian apt`` [repository](https://pkg.jenkins.io/debian/).

You need to add the Jenkins Debian repository to your local apt list.

First, use the following ``wget`` command to import the GPG key for the Jenkins repository:

```bash
wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key | sudo apt-key add -
```

The output of above command should be **``OK``**, which means that the key was successfully imported and that packages from this repository will be considered trusted.

Next, add the Jenkins repository to the system using the following command:

```bash
sudo sh -c 'echo deb http://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'
```

There is no output from above command.

## Install Jenkins

Once the Jenkins repository is added and enabled, you can update the package list and install the latest version of Jenkins by typing: ``apt``

```bash
sudo apt update
```

From the output you should see the package lists read from jenkins.io.

```bash
[Output]
...
Ign:4 https://pkg.jenkins.io/debian-stable binary/ InRelease
Get:6 https://pkg.jenkins.io/debian-stable binary/ Release [2044 B]
Get:7 https://pkg.jenkins.io/debian-stable binary/ Release.gpg [833 B]
Get:8 https://pkg.jenkins.io/debian-stable binary/ Packages [21.2 kB]
...
```

Now, let's install it. Don't forget to hit **``Y``** to confirm:

```bash
sudo apt install jenkins
```

The Jenkins service will start automatically after the installation is complete. You can verify the service status:

```bash
systemctl status jenkins
```

You should see something like this:

```bash
● jenkins.service - LSB: Start Jenkins at boot time
     Loaded: loaded (/etc/init.d/jenkins; generated)
     Active: active (exited) since Wed 2022-02-02 05:08:56 UTC; 1min 3s ago
       Docs: man:systemd-sysv-generator(8)
      Tasks: 0 (limit: 38522)
     Memory: 0B
     CGroup: /system.slice/jenkins.service

Feb 02 05:08:55 jenkins-ubuntu systemd[1]: Starting LSB: Start Jenkins at boot time...
Feb 02 05:08:55 jenkins-ubuntu jenkins[7934]: Correct java version found
Feb 02 05:08:55 jenkins-ubuntu jenkins[7934]:  * Starting Jenkins Automation Server jenkins
Feb 02 05:08:55 jenkins-ubuntu su[7973]: (to jenkins) root on none
Feb 02 05:08:55 jenkins-ubuntu su[7973]: pam_unix(su-l:session): session opened for user jenkins by (uid=0)
Feb 02 05:08:55 jenkins-ubuntu su[7973]: pam_unix(su-l:session): session closed for user jenkins
Feb 02 05:08:56 jenkins-ubuntu jenkins[7934]:    ...done.
Feb 02 05:08:56 jenkins-ubuntu systemd[1]: Started LSB: Start Jenkins at boot time.
```

## Update the Firewall Configuration

If your Jenkins is installed on a remote Ubuntu server and protected by OS firewall, you need to allow the inbound traffic to port ``8080`` so you can access Jenkins server from Web browser.

Assuming your Ubuntu uses ``UFW`` firewall  management, you can allow the port with the following command:

```bash
sudo ufw allow 8080
```

Outputs should be like:

```bash
Rules updated
Rules updated (v6)
```

Verify the changes by:

```bash
sudo ufw status

Status: active

To                         Action      From
--                         ------      ----
8080                       ALLOW       Anywhere
22                         ALLOW       Anywhere
8080 (v6)                  ALLOW       Anywhere (v6)
22 (v6)                    ALLOW       Anywhere (v6)
```

## Azure Network Security Group Setting

By default you will have Azure NSG to protect your network connections to and from your VM. The NSG is a white/black list to allow or deny some traffic by rules.

To secure your VM, please set up the NSG rules as below:

![Alt text](images/az-nsg.png)

## Jenkins Setup

Continue in a Web browser to complete the new Jenkins installation. Type your domain or IP address in address bar, then type port ``http://<your_ip_or_hostname>:8080``, and then a screen similar to the following will appear:

![Alt text](images/jenkins_web_install.png?raw=true)

The Jenkins installer creates an initial 32-character alphanumeric Administrator password. Before getting started, you need to use the following command to get the password from your terminal to unlock Jenkins server:

```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword

0000173xxxxf4e99xxxxxe99a87xxxxx
```

Copy the password from your terminal, paste it into the administrator password field, and click ``Continue``.

On the next screen, the setup wizard will ask if you want to install the recommended plugin or if you want to select a specific plugin.

![Alt text](images/jenkins_web_install_plugin.png?raw=true)

Click on the box ``Install suggested plugins`` and the installation process will begin immediately.

![Alt text](images/jenkins_web_install_plugin_installing.png?raw=true)

After the plug-in is installed, you will be prompted to set up your first admin user. fill in all the required information, and then click ``Save and Continue``.

![Alt text](images/jenkins_web_install_create_admin.png?raw=true)

> NOTE: You can also skip this step to continue with Admin user. However, this is not recommended.

The next page will ask you to set the URL of the Jenkins instance. The field will be populated with the auto-generated URL.

> NOTE: Please keep this URL as we will use it later for tools integration.

Then confirm the URL by clicking the ``Save and Finish`` button and the setup will be completed.

Then click on the ``Start using Jenkins`` button and you will be redirected to the **Jenkins dashboard** logged in as the admin user you created in one of the previous steps.

![Alt text](images/jenkins_web_welcome.png?raw=true)

At this point, you have successfully installed Jenkins on your Ubuntu system.

# 2.Jenkins Basic Configurations

## Jenkins Home Directory

Jenkins needs some disk space to perform builds and keep archives. You can check this location from the configuration screen of Jenkins. By default, this is set to **~/.jenkins**, and this location will initially be stored within your user profile location. In a proper environment, you need to change this location to an adequate location to store all relevant builds and archives. You can do this in the following ways:

* Set "``JENKINS_HOME``" environment variable to the new home directory before launching the servlet container.

* Set "``JENKINS_HOME``" system property to the servlet container.

* Set JNDI environment entry "``JENKINS_HOME``" to the new directory.

The following example will use the first option of setting the "JENKINS_HOME" environment variable.

First create a new folder E:\Apps\Jenkins. Copy all the contents from the existing ~/.jenkins to this new directory.

Set the ``JENKINS_HOME`` environment variable to point to the base directory location where Java is installed on your machine. For example,

| OS      | Output |
| ------- | ------ |
| Windows | Set Environmental variable **JENKINS_HOME** to you’re the location you desire. As an example you can set it to E:\Apps\Jenkins |
| Linux   | export JENKINS_HOME =/usr/local/Jenkins or the location you desire. |

In the Jenkins dashboard, click ``Manage Jenkins`` from the left hand side menu. Then click on ``Configure System`` from the right hand side.

In the Home directory, you will now see the new directory which has been configured.

![Alt text](images/jenkins_config_home.png?raw=true)

To change your Jenkins home directory, please follow this doc: https://dzone.com/articles/jenkins-02-changing-home-directory.

## # of executors

This refers to the total number of concurrent job executions that can take place on the Jenkins machine. This can be changed based on requirements. Sometimes the recommendation is to keep this number the same as the number of CPU on the machines for better performance.

## Jenkins Location

By default, the Jenkins URL points to localhost. If you have a domain name setup for your machine, set this to the domain name else overwrite localhost with IP of machine. This will help in setting up slaves and while sending out links using the email as you can directly access the Jenkins URL using the environment variable **JENKINS_URL** which can be accessed as ``${JENKINS_URL}``.

## Global properties -> Environment Variables

This is used to add custom environment variables which will apply to all the jobs. These are key-value pairs and can be accessed and used in Builds wherever required.

![Alt text](images/jenkins_config_global_env.png?raw=true)

For example, if you want to add JDK configs, first, you need to find out the Java installation paths using the ``update-alternatives`` command:

```bash
sudo update-alternatives --config java
```

In our case, the installation paths are as follows:

* OpenJDK 11 is located at /usr/lib/jvm/java-11-openjdk-amd64/bin/java
* OpenJDK 8 is located at /usr/lib/jvm/java-8-openjdk-amd64/jre/bin/java

Copy the installation path of your preferred installation. Next, open the ``/etc/environment`` file:

```bash
sudo nano /etc/environment
```

Add the following line, at the end of the file:

```bash
JAVA_HOME="/usr/lib/jvm/java-11-openjdk-amd64"
```

Make sure you replace the path with the path to your preferred Java version.

You can either log out and log in or run the following source command to apply the changes to your current session:

```bash
source /etc/environment
```

To verify that the JAVA_HOME environment variable is correctly set, run the following echo command :

```bash
echo $JAVA_HOME
```

```
[Output]
/usr/lib/jvm/java-11-openjdk-amd64
```

Alternatively, you can also add this path to Jenkins as it's Environment variables. Below screenshot is just an example:

![Alt text](images/jenkins_config_global_env_sample.png?raw=true)

## Email Notification

In the email Notification area, you can configure the SMTP settings for sending out emails. This is required for Jenkins to connect to the SMTP mail server and send out emails to the recipient list.

## 1.Jenkins-BlueOcean-Pipeline

Description
This is to demo how to build Jenkins pipeline using Blue Ocean interface

About Blue Ocean
Blue Ocean is a new user experience for Jenkins based on a personalizable, modern design that allows users to graphically create, visualize and diagnose Continuous Delivery (CD) Pipelines. For more info please refer to https://www.jenkins.io/projects/blueocean/about/

Pre-requisite
Github Account
Jenkins Server
Hands-On
Install Blue Ocean
If you use standard Jenkins installation, rather than the image jenkinsci/blueocean, Blue Ocean is not installed by default. You need to install it from Jenkins Plug-in Manager.

Navigate to Jenkins Dashboard -> Manage Jenkins -> Manage Plugins.

Search "Blue Ocean" from the "Available" tab.

install it and it will show left side of Jenkins GUI webpage


# Jenkins Basic Management

## System Jenkins -- Configure Global Security

After Jenkins is installed, you should properly assign permissions to your accounts/groups, otherwise the accounts may not able to perform the task.

Under **Manage Jenkins** -> **Configure Global Security** -> **Authorization**, select ``Matrix-based security``, add your admin account as a user, and then grant all permissions.

![Alt text](images/jenkins_config_matrix_permission.png?raw=true)

Select **Apply** and then **Save**.

> NOTE: You must first click Apply (Application), and then click Save (Save), otherwise the permissions set will not work.

There is no group support for the built-in Jenkins user database. Group support is only usable when integrating Jenkins with LDAP or Active Directory.

## Plug-in Management

Jenkins can be seen as a framework that is able to integrate with anything you want, enabling the company's entire continuous integration system. Plugins can be installed as needed, or through scripts.

Under **Manage Jenkins** -> **Manage Plugins**, you can manage the updates and installs.

![Alt text](images/jenkins_config_manage_plugins.png?raw=true)

Let's try to install **Azure CLI** to your Jenkins as a plugin.

Search "**Azure CLI**" from the search bar and select **Available**, then tick the only one from the result list, click "**Install without restart**".

The plugin can be easily installed.

![Alt text](images/jenkins_config_manage_plugins_install.png?raw=true)

## Global Tool Configuration

Maven, JDK, Git, etc. are the most common and popular tools and installed with your Jenkins by default.

Navigate to **Manage Jenkins** -> **Global Tool Configuration**, here you will be able to manage them.

![Alt text](images/jenkins_config_global_tool_config.png?raw=true)

Take Java as an example, you may configure it like below example:

> NOTE: This is just an example to demostrate how to configure. You may not need it.

![Alt text](images/jenkins_config_global_tool_config_java_example.png?raw=true)


# Getting started with Jenkinsfile

A `Jenkinsfile` is a text file that contains the definition of a Jenkins Pipeline and is checked into source control. Consider the following Pipeline which implements a basic three-stage continuous delivery pipeline.

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


# Hands on a Simple Pipeline

Now, let's try to create a pipeline to build an application. Jenkins is the Continuous Integration and Continuous Delivery (CI/CD) solution for any language, application, or platform. However, you need to remeber that different languages and platforms you working on have their specific requirements on building tools, and you need to configure your Jenkins yourself.

In this hands-on, we are going to configure your Jenkins environment to support and run a simple CI pipeline for an application.

## Prerequisites

* Your Jenkins

## Common Programming Languages

To set up a build pipeline, you need to know what is the programming language and it's runtime.

Below are some examples of languages and their extensions:

|Language|Code File|Project/Solution File|
|---|---|---|
|Java|.java, .do, .action, .jsp|.pom|
|DotNet|.cs, .aspx|.csproj, .sln|
|Go|.go|go.mod|
|NodeJS|.js|package.json|
|Bash|.sh||
|PowerShell|.ps||
|Python|.py||
|Web|.html, .css, .js||
|PHP|.php, .phtml||
|Ruby|.rb, .rhtml||

## Create Your Build Pipeline

We use this project to build a pipeline for one of it's API applications.

* Repo: https://github.com/RayMaAU/openhack-devops-team

Under the ``apis``, there are 4 applications. In this task, you are required to set up a build pipeline for one of them as you like:

* poi
* trips
* user-java
* userprofile

![Alt text](./images/github-apis.jpg?raw=true)

## Hands-On

Now, let's do hands on task. The goal is to select a API application from the above four as you like, and set up a **BUILD** pipeline in your Jenkins to have a successful build.

> NOTE:
>
> * Please ignore the Dockerfile under each API folder.
> * If you are `NOT` familiar with code test, you can ignore them.

### Task 1

Figure out the language used by the API you choosed and answering following questions:

1. What is the language?
2. What framework/SDK/runtime I may need?
3. What version?
4. How is the process to build this language?

### Task 2

Configure your Jenkins pipeline running environment, by installing any required:

* SDK?
* Framework?
* Build tool?
* Test tool (if you want to run tests)?
* other tools you may need?

### Task 3

Create a Jenkins **Pipeline**, and author your pipeline script.

![Alt text](./images/create-pipeline.jpg?raw=true)

Remember to pull the source code from GitHub first.

You are NOT required to deploy/publish the artifacts to anywhere, but please print the file list at the end of your pipeline task.

Generally, your pipeline should follow below stages and steps, however, you may `NOT` need them all for some programming languages:

```sh
pipeline {
    agent any

    stages {
        stage('Git checkout') {
            steps{
                // Get source code from a GitHub repository
            }
        }
        
        stage('Build') {
            steps{
                // Do your build task
            }
        }
        
        stage('Test') {
            steps{
                // Run unit tests, integration tests, and/or e2e tests
            }
        }
        
        stage('Publish') {
            steps{
                // Publish your artifacts to somewhere.
                // However, in our hands-on, you just need to print the artifact list by Linux command 'ls -la'
            }
        }
    }

    post {
        always { 
            echo 'I will always say Hello again!'
        }
    }
}
```

## Expected Result

The expected result for your pipeline(s) is/are:

If you build...

### **poi**

Build

```cmd
Build succeeded.
0 Warning(s)
0 Error(s)
```

UnitTest

```cmd
Passed!  - Failed:     0, Passed:    17, Skipped:     0, Total:    17, Duration: 1 s - /var/lib/jenkins/workspace/api-poi/apis/poi/tests/UnitTests/bin/Release/netcoreapp3.1/UnitTests.dll (netcoreapp3.1)
```

IntegrationTest

```cmd
Failed!  - Failed:     1, Passed:     0, Skipped:     0, Total:     1, Duration: < 1 ms - /var/lib/jenkins/workspace/api-poi/apis/poi/tests/IntegrationTests/bin/Release/netcoreapp3.1/IntegrationTests.dll (netcoreapp3.1)
```

> NOTE
>
> * Integration test is failed as expected as we don't have backend database and other components running.
> * Therefore, please don't include the IntegrationTest in your pipeline.

### **user-java**

Test

```cmd
Results :

Tests run: 7, Failures: 0, Errors: 0, Skipped: 0
```

Build

```cmd
[INFO] --------------
[INFO] BUILD SUCCESS
[INFO] --------------
```

### **trips**

Build

```sh
NO OUTPUT
```

UnitTest

```sh
ok    github.com/Azure-Samples/openhack-devops-team/apis/trips/tripsgo    0.014s
```

IntegrationTest

```sh
FAIL    github.com/Azure-Samples/openhack-devops-team/apis/trips/tripsgo    0.018s
```

> NOTE
>
> * Integration test is failed as expected as we don't have backend database and other components running.
> * Therefore, please don't include the IntegrationTest in your pipeline.

### **userprofile**

npm install

```sh
up to date, audited 448 packages in 20s
```

Tests

```sh
> mydriving-user-api@1.0.0 test
> tape 'tests/**/*.js' | tap-junit --output reports --name userprofile-report

Tap-Junit: Finished! userprofile-report.xml created at -- reports
```

npm coverage

```sh
> mydriving-user-api@1.0.0 cover
> nyc tape -- 'tests/**/*.js' --cov

TAP version 13
# /healthcheck/user
ok 1 No parse error
ok 2 Valid swagger api
# test get operation
ok 3 null
ok 4 should be truthy
ok 5 should be truthy
ok 6 No error
ok 7 Ok response status
ok 8 Valid response
ok 9 No validation errors

1..9
# tests 9
# pass  9

# ok
```
# APIs Jenkins Build Pipeline Solution

## poi

This is a DotNet Core application, the framework version is .netcore 3.1. You need to install the SDK on your Jenkins node.

Download link: https://docs.microsoft.com/en-us/dotnet/core/install/linux-ubuntu

First, you need to run the following commands to add the Microsoft package signing key to your list of trusted keys and add the package repository.

```sh
wget https://packages.microsoft.com/config/ubuntu/20.04/packages-microsoft-prod.deb -O packages-microsoft-prod.deb
sudo dpkg -i packages-microsoft-prod.deb
rm packages-microsoft-prod.deb
```

> NOTE
>
> You need to specify the ubuntu version of your Jenkins.

Then, the .NET SDK allows you to develop apps with .NET. If you install the .NET SDK, you don't need to install the corresponding runtime. To install the .NET SDK, run the following commands:

```sh
sudo apt-get update; \
  sudo apt-get install -y apt-transport-https && \
  sudo apt-get update && \
  sudo apt-get install -y dotnet-sdk-6.0
```

> NOTE
>
> Please note, you need to have the v3.1 to build this application.

**Build Pipeline**

```sh
pipeline {
    agent any

    stages {
        stage('Git checkout') {
            steps{
                // Get source code from a GitHub repository
                git branch:'main', url:'https://github.com/RayMaAU/openhack-devops-team.git'
            }
        }
        
        stage('Build') {
            steps{
                sh 'dotnet restore ./apis/poi/poi.sln'
                sh 'dotnet clean ./apis/poi/poi.sln --configuration Release'
                sh 'dotnet build ./apis/poi/poi.sln'
            }
        }
        
        stage('Test') {
            steps{
                sh 'dotnet test ./apis/poi/tests/UnitTests/UnitTests.csproj  --configuration Release --no-restore'
                sh 'dotnet test ./apis/poi/tests/IntegrationTests/IntegrationTests.csproj  --configuration Release --no-restore'
            }
        }
        
        stage('Publish') {
            steps{
                sh 'dotnet publish ./apis/poi/web/poi.csproj --configuration Release --no-restore'
                sh 'ls -la ./apis/poi/web/bin/Release/netcoreapp3.1/publish/'
            }
        }
    }
}
```

## user-java

As a Maven Java application it uses Spring framework, this is all you need:

```sh
sudo apt install maven
```

The latest v3.3.9 should work for this application.

**Build Pipeline**

```sh
pipeline {
    agent any

    stages {
        stage('Git checkout') {
            steps{
                // Get source code from a GitHub repository
                git branch:'main', url:'https://github.com/RayMaAU/openhack-devops-team.git'
            }
        }
        
        stage('Build') {
            steps{
                sh 'mvn -f ./apis/user-java/pom.xml package'
            }
        }
        
        stage('Test') {
            steps{
                dir('./apis/user-java/') {
                  sh 'mvn test'
                }
            }
        }
        
        stage('Publish') {
            steps{
                sh 'ls -la ./apis/user-java/target/'
            }
        }
    }
}
```

## trips

Obviously, this is a Go application, so you need to install Golang SDK. You can read details from https://go.dev/doc/install.

Install it by running:

```sh
sudo wget https://go.dev/dl/go1.17.6.linux-amd64.tar.gz
sudo rm -rf /usr/local/go
sudo tar -C /usr/local -xzf go1.17.6.linux-amd64.tar.gz
export PATH=$PATH:/usr/local/go/bin
```

Verify that you've installed Go by opening a command prompt and typing the following command:

```sh
go version
```

To run tests, you also need some essential build tools, including GCC. Install by this command:

```sh
sudo apt install build-essential
```

**Build Pipeline**

```sh
pipeline {
    agent any

    stages {
        stage('Git checkout') {
            steps{
                // Get source code from a GitHub repository
                git branch:'main', url:'https://github.com/RayMaAU/openhack-devops-team.git'
            }
        }
        
        stage('Get') {
            steps{
                dir("./apis/trips/") {
                    sh '/usr/local/go/bin/go get -d'
                }
            }
        }
        
        stage('Build') {
            steps{
                dir("./apis/trips/") {
                    sh '/usr/local/go/bin/go build -o main .'
                }
            }
        }
        
        stage('Tests') {
            steps{
                dir("./apis/trips/") {
                    sh '/usr/local/go/bin/go test ./tripsgo -run Unit'
                    sh '/usr/local/go/bin/go test ./tripsgo'
                }
            }
        }
        
        stage('Publish') {
            steps{
                sh 'ls -la ./apis/trips/'
            }
        }
    }
}
```

## userprofile

As a NodeJS application you need to:

```sh
sudo apt-get update
sudo apt install nodejs
sudo apt install npm
```

Verify that you've installed them by executing the following command:

```sh
nodejs -v
npm -v
```

> NOTE
>
> Make sure you installed the Latest NodeJS LTS Version: 16.13.2 (includes npm 8.1.2).

Alternatively, you could follow this [instruction](https://github.com/nodesource/distributions#deb) to install them by executing the following command:

```sh
curl -fsSL https://deb.nodesource.com/setup_16.x | sudo -E bash -
sudo apt-get install -y nodejs

node -v
npm -v
```

In some cases, you may  need to update your Jenkins PATH by configuring it's environment variables:

```sh
PATH+EXTRA=/usr/local/nodejs/node-v16.13.2-linux-x64/bin/
```

![Alt text](./images/jenkins-add-path.jpg?raw=true)

**Build Pipeline**

```sh
pipeline {
    agent any

    stages {
        stage('Git checkout') {
            steps{
                // Get source code from a GitHub repository
                git branch:'main', url:'https://github.com/RayMaAU/openhack-devops-team.git'
            }
        }
        
        stage('npm install') {
            steps{
                dir("./apis/userprofile/") {
                    sh 'npm install'
                }
            }
        }
        
        stage('Tests') {
            steps{
                dir("./apis/userprofile/") {
                    sh 'npm test'
                }
            }
        }
        
        stage('npm coverage') {
            steps{
                dir("./apis/userprofile/") {
                    sh 'npm run cover'
                }
            }
        }

        stage('Publish') {
            steps{
                sh 'ls -la ./apis/userprofile/'
            }
        }
    }
}
```

## Install DotNet SDK 3.1

Open a terminal and run the following commands to setup the 20.04 repositories

```bash
 sudo wget https://packages.microsoft.com/config/ubuntu/20.04/packages-microsoft-prod.deb -O packages-microsoft-prod.deb
 sudo dpkg -i packages-microsoft-prod.deb
```

Install the .NET Core SDK

```bash
 sudo add-apt-repository universe
 sudo apt-get update
 sudo apt-get install apt-transport-https
 sudo apt-get update
 sudo apt-get install dotnet-sdk-3.1
```


# Multibranch Pipeline

Please fork this repo to your GitHub and create a Blue Ocean pipeline: https://github.com/RayMaAU/building-a-multibranch-pipeline-project/tree/master

## Dependency

* NodeJS/NPM

## building-a-multibranch-pipeline-project

This repository is for the [Build a multibranch Pipeline project](https://jenkins.io/doc/tutorials/build-a-multibranch-pipeline-project/) tutorial in the [Jenkins User Documentation](https://jenkins.io/doc/).

This tutorial uses the same application that the [Build a Node.js and React app with npm](https://jenkins.io/doc/tutorials/build-a-node-js-and-react-app-with-npm/) tutorial is based on. Therefore, you'll be building and testing the same application but this time, its delivery will be different depending on the Git branch that Jenkins builds from. That is, the branch being built determines which delivery stage of your Pipeline is executed.

The `jenkins` directory contains an example of the `Jenkinsfile` (i.e. Pipeline) you'll be creating yourself during the tutorial and the scripts` subdirectory contains shell scripts with commands that are executed when Jenkins processes either the "Deliver for development" or "Deploy for production" stages of your Pipeline (depending on the branch that Jenkins builds from).
