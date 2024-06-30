# Class-11-Serverless&AWS-SDK
# Lecturer: Holly

- [Introduction](#introduction)
- [Serverless](#serverless)
  * [Serverless Architecture Overview](#serverless-architecture-overview)
  * [AWS Lambda with](#aws-lambda-with)
  * [Build a Serverless “Hello, World!”](#build-a-serverless--hello--world--)
  * [Use Lambda with other AWS services](#use-lambda-with-other-aws-services)
- [AWS SDK](#aws-sdk)
  * [AWS CLI](#aws-cli)
  * [AWS SDK for Node.js](#aws-sdk-for-nodejs)
  * [AWS SDK QuickStart](#aws-sdk-quickstart)
    + [Lambda Permissions](#lambda-permissions)
  * [3.Homework](#3homework)

# Introduction
本次课程的内容是和开发代码最接近的。
内容分两大块：

- Serverless(Lambda 完成computing部分，当下流行的无服务器架构)
  - ○ Overview
  - ○ AWS Lambda: run your code on cloud
  - ○ Build a Serverless “Hello World！” 
  - ○ Use Lambda with other AWS services
- AWS SDK(用代码的方式向AWS发请求)
  - ○ AWS CLI
  - ○ AWS SDK for Node.js

- Why are we learning this?
1. 在当下的工作中一定会用到 Lambda function
2. 对个人的项目有很大帮助。对于刚入门来说，无服务器架构是back hosting的最好选择，不需要管理很多底层繁杂的细节，可以专注于代码本身。

# Serverless
## Serverless Architecture Overview

- Build and run applications without having to manage its infrastructure

Traditional Infrastructure
- Team purchase servers to build and run applications
- Managing servers take time and resources 
  - server hardware cost 
  - software and security patching 
  - create backups in case of failure

Serverless Pros&Cons
- Pros
  - Low cost: charge per invocation, no cost for unused server 
  - Scalability (有warm up time)
  - Productivity
- Cons
  - Loss of control (主要原因拒绝选择Cloud)
  - Security: cloud provider might run several customer’s code on the same server at the same time 
  - Performance Impact 
  - Testing 
  - Vendor lock-in

Lambda的运行时间上限是15分钟。

Serverless: **Function** as a Service
Developers write application code as a set of discrete functions that has following attributes:
- Invocation: a single function execution (触发条件)
- Duration: time it takes to execute this function. 执行该函数所需的时间(如果设计了一个function需要用20分钟完成，可以进一步拆分，拆分成不同的小步骤，每个步骤由一个function担任；另一个常见方式是重构成异步项目(Asynchronous))。
- Cold start: latency that occurs when a function is triggered for the first time, or after a period of inactivity. 当一个功能第一次被触发时，或在一段时间的不活动之后，发生的延迟
- Concurrency limit 并发限制: number of function instances that can run simultaneously in one region. 一个区域内可以同时运行的函数实例数
- Timeout: amount of time that cloud provider allows a function to run before terminating it. 云提供商允许一个函数在终止之前运行的时间

Serverless Use Cases
- Trigger-based tasks (event-based invocation [EDA])
  - any activity that triggers an event or a series of events 
  - e.g. user sign up on website, might trigger a database change, which in turn triggers a welcome email
- Building RESTful APIs
- Asynchronous processing 
  - behind-the scenes application tasks 
  - e.g. render product info, transcoding videos after upload
  - 同步：系统在最大程度上保证发出的request能最快程度的被系统处理
  - 异步：请求可能没法第一时间处理，但也没有关系，不需要第一时间处理
- Security checks
- CI/CD: e.g. code commit trigger a function to build and run automate test

Serverless on Cloud
![Serverless on Cloud](image/class-6-serverless-on-cloud.png)
 
## AWS Lambda with
- Write and upload code as a .zip file or container
- Automatically respond to request at any scale, up to 10k/s
  > https://docs.aws.amazon.com/lambda/latest/dg/gettingstarted-limits.html
- Pay only the compute time you use, per-milisecond
- Respond in milliseconds with provisioned concurrency
 ![lambda](image/class-6-lambda.png)

## Build a Serverless “Hello, World!”
>https://github.com/australiaitgroup/DevOpsNotes/blob/main/WK6_Serverless_AWS_SDK/serverless_hello_world_api.md

- 任何一个Lambda 都是由Event(事件)唤起的
- Layers是用来管理依赖的
- 在测试阶段，如果没有敏感信息，建议先把request打印出来，方便做调试。
- 测试完成，deploy之后才会保存
- 方便设置和部署，但是对于代码的管理，如在什么样的架构上运行等，是很有限的
- 设置了resource之后，要在选中resource下选择创建method
- 创建时勾选proxy integration
- API的method后，要doply之后api才会部署，并生成Invoke URL
- 浏览器只是一种访问形式，也可以用terminal访问，curl命令，或者其他的浏览器

## Use Lambda with other AWS services
Invoke your Lambda function on schedule

Invoke your Lambda function when object is uploaded to a S3

Invoke your Lambda function for incoming HTTP request

- 成熟的团队都在使用框架，AWS CDK - Typescript

# AWS SDK
支持的语言很多，CLI最常用，windows站可以用powershell。

所有AWS中的资源，包括所有用GUI实现的功能，甚至是GUI不包括的资源和功能也能用SDK和CLI来调用和设置。

功能范围大小： API > SDK&CLI > GUI

- Tools for developing and managing applications on AWS
> https://aws.amazon.com/tools/
- SDK: Software Development Kit. A collection of software development tools in one installable package.
- SDK - Software Development Kit, 是用来帮助devloper更快捷来开发应用的. 从这个层面上说, sdk, framework都是工具, 不同语言的sdk是不同的工具.

- in a world without SDK…
Each action on AWS is implemented via API calls. Without SDK, you need to sign your API request by generating signatures.
AWS上的每个操作都是通过API调用实现的。在没有SDK的情况下，您需要通过生成签名来为API请求签名
![sdk](image/class-6-sdk.png)
> https://docs.aws.amazon.com/general/latest/gr/signing_aws_api_requests.html

## AWS CLI
AWS Command Line Interface (AWS CLI) is an open source tool that enables you to interact with AWS services using commands in your command-line shell.

Installation, configure and authentication: refer to
> https://github.com/australiaitgroup/DevOpsNotes/blob/main/WK0_General/devops-initial-setup.md
> https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-quickstart.html

- QuickStart
 - 在terminal中查询需要使用的命令

 ```
 aws help

 aws lambda help

 aws ec2 help
 ```
必要的参数是‘--’开头的，非必要的参数用是方括号内的，outfile是CLI的一个格式。

如果着急使用可以之间到最后看Example

- Lambda: invoke a function
```
aws lambda invoke --function-name GetStartedLambdaProxyIntegration --payload '{"key1": "value1"}' response.json
```
- CloudWatch: check Lambda function's latest logs
```
aws logs describe-log-streams --log-group-name /aws/lambda/GetStartedLambdaProxyIntegration | tail -30

aws logs get-log-events --log-group-name /aws/lambda/GetStartedLambdaProxyIntegration --log-stream-name '2024/02/25/[$LATEST]d57757b167544784b92c391cc905a870'
```

## AWS SDK for Node.js

Use Javascript to create, configure and manage AWS resources.

Installation, configure and authentication: refer to
https://docs.aws.amazon.com/sdk-for-javascript/v2/developer-guide/getting-started-nodejs.html

## AWS SDK QuickStart
https://github.com/australiaitgroup/DevOpsNotes/blob/main/WK6_Serverless_AWS_SDK/sdk_dynamodb.md


### Lambda Permissions
Use Lambda execution role (IAM policy) to manage Lambda permissions
> https://docs.aws.amazon.com/lambda/latest/dg/lambda-intro-execution-role.html
Develop AWS Lambda using Serverless Framework
> https://www.serverless.com/framework/docs/getting-started

为Lambda的role添加permissions，最好根据最小化权限原则设置，只设置角色需要的权限，权限设置要更精细化，不能过大。

- 要确认dynamoDB设置的region
- PutItemCommand会创建新数据，若主键存在的话数据会将之前的内容复写

## 3.Homework
> https://github.com/australiaitgroup/DevOpsNotes/blob/main/WK6_Serverless_AWS_SDK/homework.md

有时间的话可以研究一下CDK，以及typescript，因为其兼容性更高，业界流行。