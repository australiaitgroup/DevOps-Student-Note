# Class-06-CICD-1
- [Class-06-CICD-1](#class-06-cicd-1)
- [Concept](#concept)
  * [CI/CD  （核心内容）](#ci-cd--------)
  * [解决方案](#----)
  * [Examine code quality](#examine-code-quality)
  * [Technical debt](#technical-debt)
  * [Azure pipelines in DevOps](#azure-pipelines-in-devops)
- [Azure DevOps - Hands On](#azure-devops)
  * [Visual Designer](#visual-designer)
- [Homework](#homework)
- [补充资料](#----)

# Concept
concept是基础，不能只学会用工具，要了解工作流程和可以提供的价值，SRE和DevOps的工作很多都是自己Defined，学会看overall的流程，自己给自己create job。面试时在介绍自己的项目的时候要注重整体流程，不要局限在中间的某一个环节。

## CI/CD  （核心内容）
### Dev
 - CI Continuous Integration
    Continuous integration drives the ongoing merging and testing of code, which leads to finding defects early.
    
    把dev的code拿来进行自动的build，compile，test，再送入下个环节

 - CD Continuous Delivery
    Continuous delivery of software solutions to production and testing environments helps organizations quickly fix bugs and respond to ever-changing business requirements. 

    出场环节，要进行检测，校验，审查，批准，小规模的发布，慢慢全面发布，收集feedback，error message和running performance。

- Version Control
    Version Control, usually with a Gt-based Repository, enables teams located anywhere in the world to communicate effectively during daily development acivities.

    大多数情况，上线的app是master/main branch。有些公司会有专门的release branch。

- Kanban 和dev沟通的工具且需要devops维护
    配合自动化维护Kanban上的任务。用azure hands on labs 练习Kanban配置

- Agile/Lean
    - Plan and isolate work into sprints.
    - Manage team capacity and help teams quickly adapt to changing business needs.
    - A DevOps Definition of Done is working software collecting telemetry against the intended business goals.

### Ops
- Monitoring and logging
    Monitoring and logging of running applications.
    
- Cloud
    Public and Hybrid Clouds have made the impossible easy.
    
    Cloud可简化monitoring and logging的工作。


如何简化自己的工作，提升产品的可靠性，弹性，可扩展性，性能和表现，模版化和使用自动化流水线。

- Infrastructure as Code(IaC) (模版化)
    Enables the automation and validation of the creation and teardown of environments to help with delivering secure and stable application hosting platforms
        AWS - Terraform
        Azure - Azure Resource Manager, Bicep,Terraform

- Microservices
    Isolate business use cases into small reusable services that communicate via interface contracts

    根据客户的需求将组件拆开，易更新，易部署，但需要dev和架构师高度配合，架构重新设计。

- Containers - 虚拟化更高层次的细化，在更小的构件上开发程序，在更小的范围对程序重构。
    Containers are the next evolution in virtualization
    对于DevOps的挑战是需要学习containers和K8S。containers是个中间的解决方案，最终可能会被cloud原生服务取代。

## 解决方案 
- Azure Devops
使用微软服务的公司大概率使用该服务。
All in one的产品
针对企业的流程管理
    1. Azure Boards: Agile planning, work item tacking, visualization and reporting tool
    2. Azure Pipelines: A language, platform and cloud agnostic Cl/CD platform with support for containers or Kubernetes
    3. Azure Repos: Provides cloud-hosted private repos
    4. Azure Artifacts: Provides integrated package management with support for Maven, npm, Python andNuGet package feeds from public or private sources
    5. Azure Test Plans: Provides an integrated panned and exploratory testing solution

- Github
针对项目的collaboration
 1. Codespaces: Provide cloud-hosted collaborative development environments
 2. Repos: Provide cloud-hosted and on-premises git repos for both public and private projects
 3. Actions: Create automation workflows with environment variables and customized scripts (Azure Pipelines 相同的内容)
 4. Packages: Ease integration with numerous existing packages and open-source repositories
 5. Security: Review code and identity vulnerabilities early in the development cycle （Codespaces - 远端虚拟开发，保证代码的安全，防止开发者因将code拉到本地而泄漏。https://github.com/features/codespaces）

- Monorepo
全部的内容，前端，后段，中间件, API等都放在一个repo中。
Monorepos - Source control pattern where all the source code is kept in a single epository
好处：Share Source code和接口定义信息。

- Multiple repositories （大公司，代码库很庞大）- Refer to organizing your projects each into their separate repository

- Change log
记录版本变化之后，内容的变化，功能的添加，功能的修改，功能的移除，版本号的变化等，小公司会和release log/release note混用。change log要更详细的文档。

- Feature branching - All feature development should take place in a dedicated branch instead of the main branch,
- Branch workflow
dev一般都在新的branch上工作，有的小公司dev会使用fork的repo工作(fork workflow)
- Forking Workflow - Every developer uses a server-side repository
- Evaluate the workflow
    - Does this workflow scale with team size?
    - Is it easy to undo mistakes and errors with this workflow?
    - Does this workflow impose any new unnecessary cognitive overhead to the team?

## Examine code quality
DevOps and SRE 需要做监控，两个角度，Dev & Ops
Ops   cpu，memory使用率，traffic的变化，网站可靠性等
Dev   Code scanning，code scanning tools（检测可读性，注释，命名规则，公司规则等），Security tools

## Technical debt
The future penalty that you incur today by making easy or quick choices in software development practices.

当年为了多快好省采用了简单的，不够完善的东西，需要在后续的工作中再次提出改进和升级。
技术债要纳入到日常的sprint中作为正常的ticket去开发的，虽然不会带来新的feature和business value，但是架构的调整改进是为了未来做准备。

### code quality tools
微软用reshaper and SonarQube

## Azure pipelines in DevOps
a cloud service that you can use to automativally build and test your code project and make it available to other users
- 自动化构建
    - Work with any language or platform
    - Deploy to different types of targets at the same time
    - Integrate with Azure deployments
    - Build on Windows, Linux or macOS
    - Integrate with GitHub
    - Work with open-source projects
- 自动化测试
- 自动化部署

Pipeline本身是要执行的一系列的任务，在任务中可以分成多个stage运行，并且按照设置好的顺序，顺序需要根据应用程序的具体架构。运行所有的东西都需要环境，环境是一台代操作系统的机器（Agent）。Pipeline需要Trigger，可能需要手动执行，或者是repo中master/main的变化。
Agent需要时间运行，如果team有很多人提交，maintan a balance, 找出最合适，性价比最高的agent的数量

# Azure DevOps - Hands On
## Visual Designer
由于现在做模版，version control，因此现在流行用yaml，但学习门槛高。yaml和classic的本质上都一样。

organization setting - pipelines settings - Disable creation of classic build pupelines / Disable creation of classic release pipelines

注意organization level setting and project level setting

### Pipelines (Pipelines)
可以build template，也可以build一个empty pipeline, 修改添加Agent job，每个job中都可以添加task。

yaml 的pipeline 不是可视化界面，是一个file，类似一个template，yaml逐渐成为主流，本质和classic一样，classic的每一步也可以查看对应的yaml contents。

具体的参数设置与dev有关，都是dev提供的，很多时候要跟dev合作。

- Tasks
    有些参数是$开始后接括号 - environment variable
    创建的新的directory，有的用于存放source code，有的用于存放build出来的binaries
- Variables
    出了build-in的variables，也可以创建自己的variables
- Triggers
    pipeline是怎么run的，可以创建schedule，设定branch，设置continuous integration，连续构建，只要有新的code进到branch，就trigger自动执行，也可以根据path（路径）改变设置。
- Options
    定义版本号 $(date:yyyyMMdd)$(rev:.r)
    github badge badge的URL
- History
    修改的历史
- Save & Queue
    Queue是放到Agent上运行

### 创建自己的Agent pools

添加Agen - ’New agent‘ 运行script

### Pipelines - Releases

只用于CD，界面针对于release
提取build好的binary,之前某一个pipeline build出来的结果。（publish Arifact uploading的latest version）

Deploy可以有多个Stage，可定义每个Stage的名字
Deployment中每一步都根据环境和需求设置

### Release Approval, Gates and Quality Control
确保没有任何的bug，issue，人工进行了验证等

每个stage都有button，用于设定如何被trigger的， 在run当前pipeline之前是否需要授权，需要有人approval之后才可运行
Quality Gate - 发送一个requires，check Policy， run requires 查看server是否healthy，或者当前是否有用户在使用，也可以限定critical bugs的数量。每个stage执行结束 后执行approval

这样设置的原因是符合企业的架构，check healthy and quality,需要对环境负责的人approve。

### Deployment Patterns
针对大型企业的应用
Dev --> Test --> Staging --> Production

四个环境，Staging与Production通常是Swap，两个环境名称对调后验证。

#### Modern Deployment Patterns
- Blue-green
    temp.production and production --> swap
    load balancer(Traffic Distribution)
    两个环境分别称之为Blue 和 Green
    当部署的时候调整Load Balancer的百分比，调换两个环境的百分比完成一次Swap

    slots & feature flags

- Canary （金丝雀）
    新功能完成后，我们先部署给一小部分用户，如先分配5%的流量做测试，效果不错再扩大范围。

- Dark Launching
    后段没压力的情况，想看用户的反馈，收集用户的行为，比如页面停留时间，找功能所消耗的时间等。

- A/B Testing
    两种功能或两个界面，做两个版本，用random的方式发放给用户，根据用户体验的反馈再决定用A方案还是B方案。

- Ring-based
    CI build好之后，由于有很多的环境，每个CD stage 都往对应的Env上deployment，每个用户都被分配到不同的ring里面，新版本在Env1中做测试，修改漏洞，待足够稳定后部署到Env2中测试，再次稳定后才会发给全部用户；如果Env1中的版本一直不够稳定，新的版本就会一直叠加在Env1中，可能会很频繁的更新，可能更新10次才可能有一个版本被approve去Env2. （参考之前学习的Microsoft Windows 10的例子）

# Homework
- Azure DevOps Labs
    https://azuredevopslabs.com/
    - Enabling Continuous Integration
    - Embracing Continuous Delivery(Optional)

# 补充资料


## Continuous Integration 
Successful Continuous Integration means code changes to a web-app are regularly built, tested, and merged to a shared repository automatically via integration tools like TravisCI, Bitbucket pipeline. It’s a solution to the problem of

1. having too many developers writing code changes to a web-app that might conflict with each other.
2. code testing and validation before merging.

## Continuous Delivery
Continuous delivery usually means a developer’s changes to an application are automatically bug tested and uploaded to a repository (like GitHub or a container registry), where they can then be deployed to a live production environment by the operations team. It’s an answer to the problem of poor visibility and communication between dev and business teams. To that end, the purpose of continuous delivery is to ensure that it takes minimal effort to deploy new code. 

## Continuous Deployment
Continuous deployment (the other possible “CD”) can refer to automatically releasing a developer’s changes from the repository to production, where it is usable by customers. It addresses the problem of overloading operations teams with manual processes that slow down app delivery. It builds on the benefits of continuous delivery by automating the next stage in the pipeline.



## Benefits of CI/CD
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

## Unittest

Unit tests test individual components, it can be as small as a function.

Unit tests are very low level, close to the source of your application. They consist in testing individual methods
and functions of the classes, components or modules used by your software. Unit tests are in general quite cheap to
automate and can be run very quickly by a continuous integration server.

## Integration Test

Integration Test is a level of software testing where individual units are combined and tested as a group.

## Performance/System Testing

Load Testing - is necessary to know that a software solution will perform under real-life loads. Gatling/Locust

## Acceptance/End-to-end Testing---blackbox test

User Acceptance Testing (UAT) is a type of testing performed by the end user or the client to verify/accept the
software system before moving the software application to the production environment.

## Responsibilities

### What are Devs responsibilities?
* Write the code for the project and write its unit-tests
* Add metrics/logs in the code to monitor the critical changes
* Dockerise existing repo with DevOps
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