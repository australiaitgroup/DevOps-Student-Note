# DevOps 初讲-Tutorial 1 - Fundamental

## 主要知识点
  - [老师介绍](#老师介绍)
  - [本节知识点](#本节知识点)
    - [1. Advices for New DevOps](#advices-for-new-devops)
    - [2. DevOps And SRE](#DevOps And SRE)
    - [3.Security](#security)
    - [4. Working platform](#working-platform)
    - [5. Termimal tools](#termimal-tools)
    - [6.常用命令](#常用命令)
    - [7.EDITOR](#editor)
    - [8.Package management](#package-management)
    - [9.Domain](#domain)
    - [10.IAM 了解基于⻆⾊的访问控制Role-Based Access Control (RBAC)] (#IAM and RBAC)
 - [相关问题](#相关问题)



# 老师介绍
 - William Wang
 - 研发--->售前(国内）--->NETWORK ENGINEER澳洲--->AMAZON NDE--->VMWARE PROFESSIONAL SERVICE--->ATLASSIAN 软件开发 
  - 转行或者换跑道的注意点
  - 会接触到网络工程师，软件工程师，需要知道了解他们说的术语等
  - 考证书的相关：
     考证---有证书CLOUD,LINUX等 可以加分

# 本节知识点
## 1. Advices for New DevOps

    -帮助找到关于error message:  除了用GOOGLE 还可以用 CHATGPT
    -handson---亲自做，复现，能照着做后能拓展
    -做笔记（obsidian 或者  GitHub），特别是踩过的坑
    -学习LINUX 的 基础命令，SHELL 命令（可以考Linux证书）
    -多用CLI,少用GUI
    -目录路径，要在正确的路径（相对路径，绝对路径，LINUX的 pwd 命令）

## 2.DevOps And SRE

   -SRE提⾼⼀个系统可伸缩性，降低流量，SRE注重于度量、监控和故障处理，强调预
    防和快速恢复。 
   -DevOps注重于流程改进、协作和⽂化变⾰，旨在消除开发与运维
    之间的障碍。 
   -总体⽽⾔，SRE和DevOps都在推动运维向⾃动化、可伸缩和⾼可⽤性
    ⽅向发展，以满⾜现代应⽤程序和服务的需求。
    https://spacelift.io/blog/sre-vs-devops

## 3.Security

    - 在公司上班的时候，密码管理（KEY MANAGEMENT) 不会有明文密码的， MFA
    - 怎么证明你是你
    - What you know, what you have   (关于two factors)
    - 公司的时候 不要在 CHROME 记录保存密码
    - 软件和OS 升级 等 

## 4. Working platform
     pc/laptop:win/Mac/Linux
     LOCAL VIRTUAL MACHINE 虚拟机 VM OR VIRTUAL BOX
     CLOUD VM:
        1. AWS
        2. AZURE
        3. GCP
     
## 5. Termimal tools
    terminals:
      shell
      power shell
    remote login tool:
      secureCRT 等
    CLI 的总要， 尽量少用GUI

    https://www.browserstack.com/guide/devops-lifecycle. Continue
    https://www.simform.com/blog/devops-lifecycle/. Tool list
    https://www.spiceworks.com/tech/devops/articles/what-is-devops-lifecycle/
    Best practices
    https://www.browserstack.com/guide/devops-lifecycle +Sec

## 6.常用命令
    WINDOWS VS  LINUX

## 7.EDITOR
    VSCODE, JETBRAIN, SUBLIME, vim/vi
    Devops相关话题：
    需要的是多面手，需要知道将来工作中的会使用到的东西有多广
    接触的东西比较广
    了解 JSON 和 YAML 文件  （对机器友好，对人友好，易于阅读）
    
    vim/vi 需要学习，是LINUX 自带的， 有时候需要编辑，大体知道一些命令

## 8.Package management

    安装各种各样的开发包，软件包，管理开发包，升级，改设置， 打补丁（ansible ,terraform）

## 9.Domain 域名

## 10. IAM and RBAC
    学习使⽤IAM，了解基于⻆⾊的访问控制Role-Based Access Control (RBAC),
    基于⻆⾊的访问控制（RBAC）是⼀种⽤于控制⽤户在公司 IT 系统中能够执⾏的操
    作的⽅法。RBAC 通过为每个⽤户分配⼀个或多个“⻆⾊”并为每个⻆⾊赋予不同权限
    来实现此⽬的。RBAC 可应⽤于单个软件应⽤程序，也可应⽤于多个应⽤程序。
    想像⼀间居住了⼏个⼈的房屋。每个居⺠都会得到⼀把可打开前⻔的钥匙：他们收
    到的钥匙不会有完全不同的设计，⽽且这些钥匙都可以打开前⻔。如果他们需要进
    ⼊这⼀物业的其他部分，如后院的仓库，他们可能会收到第⼆把钥匙。没有居⺠会
    获得仓库的唯⼀钥匙，或可同时打开仓库和前⻔的特殊钥匙。
    在 RBAC 中，⻆⾊是静态的，就像上例中房屋的钥匙⼀样。不论由谁拥有，它们都
    不会变化，⽽且需要更多访问权限的任何⼈都会被分配⼀个附加⻆⾊（或第⼆把钥
    匙），⽽不是获得⾃定义的权限。
    从理论上讲，这种基于⻆⾊的访问控制⽅法使管理⽤户权限变得相对简单，因为权
    限不是针对单个⽤户量身定制的。但是，在具有许多⻆⾊和许多应⽤程序的⼤型企
    业中，RBAC 有时会变得复杂且难以跟踪，并且最终⽤户可能会获得超出其需求的
    权限。
    
Role-Based Access Control (RBAC),
https://learn.microsoft.com/en-us/azure/well-architected/security/identityaccess
Recommendations for identity and access management - Microsoft Azure Well-
Architected Framework
Learn about recommendations for authenticating and authorizing identities that
are attempting to access workload resources.

https://docs.aws.amazon.com/cli/latest/userguide/cli-usage-output-format.html
Set the AWS CLI output format - AWS Command Line Interface
Control the format of the output from the AWS CLI.
 

# 相关问题

1. 找工作---网络上搜索 devops 关键字？还是搜索技术栈------建议搜索 技术栈
2. DEVOPS 是可以通过项目去学习的东西， 需要学习的东西特别杂。 还是需要实践中去学习和总结
3. DEVOPS--难-有需求-有机会-还没饱和--什么都需要会点，越是新的东西越有机会



    
