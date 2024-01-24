

  - [1st part:CI/CD & DEV PROCESS OVERVIEW](#1st-part-ci-cd-dev-process-overview)
      - [High level overview](#high-level-overview)
  - [2nd part:--- DEVOPS(PLAN/CODE) and tests](#2nd-part-DEVOPS-PLAN-CODE-and-tests)
  - [3rd part: hands-on](#3rd-part-hands-on)

 
# 1st part:CI/CD & DEV PROCESS OVERVIEW
## High level overview
## 1.Devops high level overview
![Devops overview](image/c1001.png)
Ref: https://github.com/JiangRenDevOps/DevOpsLectureNotesV4/blob/main/WK4_Travis_CI_CD/dev_process_review.md
## 2.What is CI/ CD 
![what is CI/ CD](image/c1002.png)
### 2.1 Continuous Integration 
Successful Continuous Integration means code changes to a web-app are regularly built, tested, and merged to a shared repository automatically via integration tools like TravisCI, Bitbucket pipeline. It’s a solution to the problem of

1. having too many developers writing code changes to a web-app that might conflict with each other.
2. code testing and validation before merging.

### 2.2 Continuous delivery
Continuous delivery usually means a developer’s changes to an application are automatically bug tested and uploaded to a repository (like GitHub or a container registry), where they can then be deployed to a live production environment by the operations team. It’s an answer to the problem of poor visibility and communication between dev and business teams. To that end, the purpose of continuous delivery is to ensure that it takes minimal effort to deploy new code. 

### 2.3 Continuous deployment
Continuous deployment (the other possible “CD”) can refer to automatically releasing a developer’s changes from the repository to production, where it is usable by customers. It addresses the problem of overloading operations teams with manual processes that slow down app delivery. It builds on the benefits of continuous delivery by automating the next stage in the pipeline.


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

# 2nd part:--- DEVOPS(PLAN/CODE) and tests

# 1.Before coding

## using Jira software to plan----> using github as a repo(clone/create the repo/branch off branches)
In the whole CICD process PLAN/CODE----> Mainly dev's jobs(it is in POC(proof of concepts) status)
devs discuss the projects and break it down into tasks and even sub-tasks
for example:
In a typical team with 4-5 team memebers, we use Jira sofeware to plan the project and do the breakdown:
 Task 1. dockerization python code
   -  Definition of Done
       - expose a code
       - only neccessary lib are installed
       - uWSGI instead of Python
 Task 2. Add a user API
      - xxxx
      - xxxx

 Task 3. Add a organisation API
       - xxx
       - xxx
      
 ...

 AND THEN: team members do story point estimation for each task, each team member can pick up their own tasks and assigned to themselves, remember not to spend too much time on some tasks especially when you are stuck with, ask your team mates to discuss and get problems solved quickly. effecient is important.

 # Using github to do the coding stuff and CICD pipeline
 ## understand some git command and use them
   -git commands used:
   
   "git clone <repo>"
   "git checkout -b issue/XXXXXXXX"
   "git status"
   "git add"
   "git push"

   Note:normally devs do their own coding tasks in branches instead of main/master 

   Mainly devs do PLAN jobs in this step
 

# 2.Start coding
  - preparation jobs:
      get IDE ready--->isntall Dependencies and setup the project--->write test--->write code--->add monitoring

# 3.After coding
   -Validate--->commit the code-->CI tests should be trigged-->find peers to review the PR---> merge the PR
  - double  check what has been committed
     git commands:
    "git status"
    "git diff"
# understand tests in CI/CD
Picture 1 shows 4 common tests:
## Tests in the CI/CD

Before you submit for code review by peers, make sure that you have created some tests to test your code

Also, please make sure you have the CI pipeline to auto run the tests.

What are the common tests

![Alt text](images/test_basics.jpg?raw=true)

### 1.Unittest

Unit tests test individual components, it can be as small as a function.

Unit tests are very low level, close to the source of your application. They consist in testing individual methods
and functions of the classes, components or modules used by your software. Unit tests are in general quite cheap to
automate and can be run very quickly by a continuous integration server.

Unittest handson example:unit_test_demo
ref link:<https://github.com/JiangRenDevOps/DevOpsNotes/tree/master/WK4_Overview_CI_CD/hands_on/unit_test_demo>
it is a simpe calculator python coding: 
calculator.py is :
```
class Calculator:
    def __init__(self, a: int, b: int):
        self.a = a
        self.b = b
        print("this is init")

    def add(self):
        return self.a + self.b

    def minus(self):
        return self.a - self.b
    
    def multiply(self):
        return self.a * self.b

    def divide(self):
        return self.a / self.b



c = Calculator(12, 2)

print(c.add())
print(c.minus())
print(c.multiply())
print(c.divide())
```

test_calculator.py is:
```
import unittest

from .calculator import Calculator


class TestCalculator(unittest.TestCase):
    def setUp(self):
        self.calculator = Calculator(3, 2)

    @unittest.skip("demonstrating skipping")
    def test_add(self):
        self.assertEqual(5, self.calculator.add())
        self.assertNotEqual(3, self.calculator.add())

    def test_minus(self):
        self.assertEqual(1, self.calculator.minus())

    def test_multiply(self):
        self.assertEqual(6, self.calculator.multiply())

    def test_divide(self):
        self.assertEqual(2, self.calculator.divide())
        self.assertEqual(1.5, self.calculator.divide())


if __name__ == '__main__':
    unittest.main()
```

![Alt text](images/function.jpg?raw=true)

In python, you can run the test like this:

```
python -m unittest tests/test_something.py
```

### 2.Integration Test

Integration Test is a level of software testing where individual units are combined and tested as a group.

For example, we try to hit API and run it in the cloud

For example, if you have a controller with REST APIs which is connected back to your database, sending REST requests to
the controller would be considered as integration test. 

for example, we could use docker to run easy-CRM image file,get the test username and password as shown below:


You may use curl to call an endpoint like this:
```
curl --header "Content-Type: application/json" \
  --request POST \
  --data '{"username":"test@gmail.com","password":"shh"}' \
  http://0.0.0.0:8089/api/login
```
alternatively we could try a better GUI software called POSTMAN to check the API call and results. Normally we just use CLI in real life at work.


### 3.Performance/System Testing

Load Testing - is necessary to know that a software solution will perform under real-life loads. Gatling/Locust

handson: load_test_demo:
ref link:<https://github.com/JiangRenDevOps/DevOpsNotes/tree/master/WK4_Overview_CI_CD/hands_on/load_test_demo>

we use Locust to simulate real customers to use the systems
load_test_easy_crm.py is:

```
`from locust import HttpUser, task, between


class QuickstartUser(HttpUser):
    wait_time = between(5, 9)
    cookies = {}

    @task
    def index_page(self):
        self.client.get("/")

    @task(3)
    def view_organisation(self):
        self.client.get("/organisation/1", cookies=self.cookies)

    def on_start(self):
        files = {
            'username': (None, 'test@gmail.com'),
            'password': (None, 'shh'),
        }
        response = self.client.post("/login/", {"username":"test@gmail.com", "password":"shh"})
        self.cookies = response.cookies``

```
or we can dockerise the locust dockfile and run it in docker, it might be easier for us, just remember this option let two dockers communicate with each other:


### 4.Acceptance/End-to-end Testing---blackbox test

User Acceptance Testing (UAT) is a type of testing performed by the end user or the client to verify/accept the
software system before moving the software application to the production environment.

Automation Test - blackbox monitoring from the customer perspective. Webdriver/Cypress
handson: webdriver_demo
ref link:<https://github.com/JiangRenDevOps/DevOpsNotes/tree/master/WK4_Overview_CI_CD/hands_on/webdriver_demo>

Reference: https://www.atlassian.com/continuous-delivery/software-testing/types-of-software-testing



## What is CI?
Continuous integration (CI) is the practice of frequently building and testing each change done to your code
automatically and as early as possible.


## Why CI ?
The whole point of CI is to have everyone working on a known stable base.

![Alt text](images/CI_DEV.png?raw=true)

Using CI, you’ll spend less time:

* Worrying about introducing a bug every time you make changes
* Fixing the mess someone else made so you can integrate your code
* Making sure the code works on every machine, operating system, and browser

![Alt text](images/CI_BUILD_TEST_SUCCESS.png?raw=true)

Conversely, you’ll spend more time:

* Solving interesting problems
* Writing awesome code with your team
* Co-creating amazing products that provide value to users

Ref: https://realpython.com/python-continuous-integration/#single-source-repository

## What CD pipeline can help with?
![Alt text](images/CI_DEV_PROD.png?raw=true)
Continuously delivery of the feature that developers wrote to production (customers).
We can use CD Pipeline to:
* Do Canary Deployment/Run Internal Test
* Run Integration Test/Regression Test/Performance Test etc...
* If issues identified, rollback before having customer impact

### 1.Wait for dev/staging deployment kicks off

Typically, in CI/CD pipeline, the staging branch will fast forward to master which should trigger the staging deployment

If everything is going well with staging deployment, there will be another few hours for soaking.

Engineers can take this opportunity to do more tests on staging environment and monitor the staging environment

### 2.Wait for prod deployment kicks off

Once the soak is done and all tests passed, the prod deployment will kick off.

Keep an eye on the monitors to make sure nothing is saturated.


## Post Deployment/Production Operation - things to do after the CI/CD

Once the code reach the production environment, the real challenge will arise. In production environment, the system 
has a more complex setup, dependencies and many moving parts. In general, in a software lifecycle, software development
only takes up to 20% of total cost (work/time/money etc...), but operation can eat up to 80%. 

Things/Questions often need to consider:

* [Deployment] How do we make sure the App have zero downtime during deployment?
* [Deployment] To reduce the blast of radius that can caused by an incident, what deployment strategy do we need to
  apply?
* [Monitoring & Alerting] How do we know if our software is not working as expected in production? How can we know where
  the bottleneck is?
* [Monitoring & Alerting] Which dashboards to check when problem occurs? How to read the dashboards?
* [Monitoring & Alerting] I don't wanna watch the monitors, which key metrics should we alert on?  
* [Capacity Management] Is our scaling policy set correctly(e.g. can handle the traffic load and also not too expensive)?
* [Operation] How to recycle the nodes without affecting customers?  
* [Operation] How do we rollback if there is a problem? and how fast can we rollback?
* [Operation] How do we recover fast/reduce Mean Time To Recover (MTTR) when there is a disaster/incident(e.g. one 
  server is down)?

## Responsibilities

### What are Devs responsibilities?
* Write the code for the project and write its unit-tests
* Add metrics/logs in the code to monitor the critical changes
* Dockerise existing repo with DevOps


### What are DevOps responsibilities?
* Dockerise existing repo with Devs
* Setup CI/CD pipeline
* Come up with Integration/System Testing/End-to-end testing plan
* Setup monitoring system for the Pipelines
* Setup automated rollback and blockers

### What are SRE responsibilities?
* Solving post deployment operational problems
* Improve the monitoring dashboards and alerts
* Tuning system parameters
* Develop software tools to automate operational steps
* Facilitate incident mitigation and investigation

# 3rd part: hands-on

# at this handson, we use AWS Elastic beanstalk,set up AWS IAM user, github (include gitaction),we need understand yml file

---
NOTE: you might need set Elastic Beanstalk service role yourself, because AWS won't automatically do it for you for its new security guideline: 

1.make sue you include thoes below in your role setup:
AWSElasticBeanstalkEnhancedHealth and AWSElasticBeanstalkManagedUpdatesCustomerRolePolicy
2.for instance profile, include thoes three policies:

    AWSElasticBeanstalkWebTier

    AWSElasticBeanstalkWorkerTier

    AWSElasticBeanstalkMulticontainerDocker
---



## Step 1. Create a repo

1) Create a repo on Github UI and let us call it `docker-intro`, 

2) Add a README file and make sure the MAIN branch is available

3) Git clone this repo to your local repo


## Step 2. Commit your code as a developer

1) Check out a new branch `git checkout -b "issue/MP-1-init-the-folder"`
2) Copy file `Dockerfile` and folder `src/` over from `WK3_Dockerisation/docker-intro`
3) Add the files and folders `git add .`
4) Commit `git commit -m "MP-1 Initialise the folder"`
5) Git push `git push --set-upstream origin issue/MP-1-init-the-folder`
6) Create a Pull Request and Merge the Pull Request
7) git checkout main
8) git pull


## Step 3. Create an application on Elastic beanstalk

Create an Elastic beanstalk application on `ap-southeast-2` (Sydney). Let us name it `sample-docker-react`.

Elastic beanstalk will create an environment for us called `Sample-docker-react-env`

Choose Docker as the engine and sample application code and submit


## Step 4. Create IAM user

In IAM -> users, create a user, attach policy with 
* AdministratorElasticBeanstalk
* EC2FullAccess
* VPCFullAccess


## Step 5. Create Access Credentials and set up github action
Once the user is created, click on security credentials -> access credentials -> create access

Choose the first option `using AWS cli to access AWS services` and note down the credentials (download the .csv file)

And then go to your git repo -> Settings -> Secrets and variables -> Actions -> New Repository Secrets add the AWS_ACCESS_KEY_ID and AWS_SECRET_ACCESS_KEY respectively with their values.


## Step 6. Create another PR to add a CD yml file
1) Create another branch
2) Create a folder `mkdir -p .github/workflows/` and a yaml file`touch .github/workflows/cd-elasticbeanstalk.yml`
3) Check the S3 bucket name and note it down
4) Update the S3 bucket name in the script below. 
5) Commit, push, review and merge PR

(Note if you did not use ap-southeast-2 (Sydney) region, you need to update the field in the script as well) 

```
name: Deploy to Elastic Beanstalk

on:
  pull_request:
    types: [closed]
  workflow_dispatch:


jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v2

      - name: Create ZIP deployment package
        run: zip -r deploy_package.zip ./  

      - name: Install AWS CLI
        run: |
          sudo apt-get update
          sudo apt-get install -y python3-pip
          pip3 install --user awscli

      - name: Configure AWS credentials
        uses: aws-actions/configure-aws-credentials@v1
        with:
          aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
          aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          aws-region: ap-southeast-2

      - name: Upload package to S3 bucket
        run: aws s3 cp deploy_package.zip s3://elasticbeanstalk-ap-southeast-2-482739392776

      - name: Deploy to Elastic Beanstalk
        run: |
          aws elasticbeanstalk create-application-version \
            --application-name sample-docker-react \
            --version-label ${{ github.sha }} \
            --source-bundle S3Bucket="elasticbeanstalk-ap-southeast-2-482739392776",S3Key="deploy_package.zip"
          
          aws elasticbeanstalk update-environment \
            --environment-name Sample-docker-react-env \
            --version-label ${{ github.sha }}
```

## Step 7 Viola

The deployment will now be triggered everytime you merge a PR.



get AWS_ACCESS_KEY_ID and AWS_SECRET_ACCESS_KEY in AWS and put into a safe place and record into github settings and let github can do the trigger everytime when coding is pushed or after pull request 






