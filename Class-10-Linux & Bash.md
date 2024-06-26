# Class-10-Linux & Bash
# Lecturer: Sai Li
- ANZX
- Certificate: CKA, CKS, Google Cloud Certified ect.
- 用日历本记录每天的工作日历，用于记录工作经验和每天工作内容汇报

- [Introduction](#introduction)
  * [What is Linux](#what-is-linux)
  * [Creation of Linux](#creation-of-linux)
  * [Benefits](#benefits)
- [Linux for DevOps](#linux-for-devops)
  * [Alpine](#alpine)
  * [Ubuntu is the the worlds most popular Linux-based desktop operating system](#ubuntu-is-the-the-worlds-most-popular-linux-based-desktop-operating-system)
- [What is CVE](#what-is-cve)
  * [Package management](#package-management)
  * [Quickstart](#quickstart)
  * [Directories](#directories)
  * [Files](#files)
  * [Getting help](#getting-help)
  * [Common tools](#common-tools)
- [Bash in day-to-day DevOps](#bash-in-day-to-day-devops)
  * [Cloud shell](#cloud-shell)
  * [Bash in day-to-day DevOps](#bash-in-day-to-day-devops-1)
  * [Top level directories](#top-level-directories)
  * [color code](#color-code)
    + [Scenario #2](#scenario--2)
    + [Scenario #3](#scenario--3)
- [Regex](#regex)
  * [Regex basic](#regex-basic)
  * [Regex practice](#regex-practice)
  * [Bash Script](#bash-script)
    + [Bash script error handling](#bash-script-error-handling)
    + [Bash script loop](#bash-script-loop)
- [Homework](#homework)

# Introduction

Linux的优势：开源，免费，代码透明。
Linux使用的很普遍。

## What is Linux
- operating system
- Kernal distribution under an open-source license

(Kernel: heart of the operating system thhat takes care of fundamentals, for example, letting hardware communicate with software)

## Creation of Linux
Linux is an operating system or a kernel which germinated as an idea in the mind of young computer science student. He used to work onand bright Linus Torvalds when he was a cothe UNlx os (proprietary software) and tholght that it needed improvements.

## Benefits

- Free
    you can install Linux on as many computers as you like without paying a cent forserver licensing
- Highly secure
    almost non-vulnerable and most secure due to its inherent design0
    does not require commercial anti-virus packages
- Flexible
    lets you control every aspect of the OS
    let you modify its source (even source code of applications)

# Linux for DevOps

- Tools and Platforms
  - a lot of open source projects run on Linux from the start
  - e.g. Git, Docker, Ansible, Kubernetes
- As a DevOps engineer, it’s perhaps unlikely that you’ll be working exclusively on Linux, or exclusively on Windows or Mac. In practice, you’ll probably work with a mix of operating systems. But Linux will be one important piece in your puzzle.

作为一名DevOps工程师，你不太可能只在Linux、Windows或Mac上工作。在实践中，你可能会使用多种操作系统。但是Linux将是您的拼图中一个重要的部分。

- cloud shell, 减小对设备的依赖，文档存云。

## Alpine
Alpine Linux is an independent, non-commercial,general purpose Linux distribution designed for power users who appreciate security, simplicity and resource efficiency.
    - small 8MB
    - SIMPLE
    - SECURE

## Ubuntu is the the worlds most popular Linux-based desktop operating system

• Easy Integration
• Large Community
• Wide Range of Programming Tools
• Certified Hardware--Most mainstream PC and hardware manufacturers such as Dell, Lenovo, HP, and the Raspberry Pi Foundation certify their machines for Ubuntu
• Comprehensive Software Support--With Ubuntu, LTS version users get free software updates and security patches for a minimum of five years after the release.

# What is CVE
CVE, short for Common Vulnerabilities and Exposures, is a list of publicly disclosed computer security flaws. When someone refers to a CVE, they mean a security flaw that's been assigned a CVE ID number.

## Package management
- Debian or Ubuntu              apt-get
- Fedora, CentOS, or Red Hat    yum
- Mac OS                        homebrew

YUM and APT offer the same core functionalities when it comes to installing packages. Both tools keep the information in a database and provide the same basic command-line features. However, some key differences set the two package managers apart.

##  Quickstart
For Linux (OS X, Ubuntu etc) users
- open Terminal on your machine
- (optional) make your shell easier to use: install zsh
> https://www.freecodecamp.org/news/how-to-configure-your-macos-terminal-with-zsh-like-a-pro-c0ab3f3c1156/

For Windows users
- install Windows Subsystem for Linux (WSL)
> https://docs.microsoft.com/en-us/windows/wsl/install
- (optional) make your shell easier to use: install zsh
> https://blog.joaograssi.com/windows-subsystem-for-linux-with-oh-my-zsh-conemu/

动手做NAS，Raspberry OS，练习Bash， 看comic学习(https://www.turnoff.us)。

## Directories
- checking which directory you’re in，查看你现在在哪个目录
```bash
    pwd
```
- checking current directory contents, 列出当前文件夹的详细信息，包括隐含文件

```bash
    ls -la
```
- going into a directory 进入文件夹

```bash
    cd
```
## Files

- create a new file in /tmp/file with content “hello world”
```bash
    echo “hello world” > /tmp/file
```

- check the file 打印文件
```bash
    cat /tmp/file
```
- change the file content，也可以查看文件，还可以编辑文件
```bash
    vim /tmp/file
    #退出
    :q
    #保存退出
    :wq
```
## Getting help
- read manual of vim command
```bash
    man vim
```
- read manual of less command
```bash
    man less
```
与--help的区别，man更详细的解释，help提供本步骤的简单帮助。

- read information of passwd command
```bash
    info passwd
```

## Common tools
- grep
    > https://www.geeksforgeeks.org/grep-command-in-unixlinux/?ref=lbp
- sed
    > https://www.geeksforgeeks.org/sed-command-in-linux-unix-with-examples/?ref=lbp
- awk 
    > https://www.geeksforgeeks.org/awk-command-unixlinux-examples/?ref=lbp
- sort 
    > https://www.geeksforgeeks.org/sort-command-linuxunix-examples/?ref=lbp
- diff 
    > https://www.geeksforgeeks.org/diff-command-linux-examples/?ref=lbp
- uniq 
    > https://www.geeksforgeeks.org/uniq-command-in-linux-with-examples/?ref=lbp

# Bash in day-to-day DevOps
- Scenario 1: Securely connect to a remote Linux machine and check the logs
> https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/launching-instance.html
> https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AccessingInstancesLinux.html
> https://docs.aws.amazon.com/managedservices/latest/userguide/access-to-logs-ec2.html

- Scenario 2: Download/Upload file from/to a remote Linux server
> https://docs.aws.amazon.com/zh_cn/AWSEC2/latest/UserGuide/AccessingInstancesLinux.html#AccessingInstancesLinuxSCP

- Scenario 3: Check network connectivity on a linux machine
> https://geekflare.com/linux-test-network-connectivity/

- Scenario 4: Setup a daily job on a Linux machine
> https://phoenixnap.com/kb/set-up-cron-job-linux

## Cloud shell
AWS的Cloud Shell
- 基本上免费使用
- 安全，login之后
- 该环境属于使用者，可以自己设置和安装软件，权限不一样，预装不一样。（AWS 1G免费使用， Azure 5G免费）
但是对某些企业存在安全隐患

## Bash in day-to-day DevOps

- Scenario 1: Securely connect to a remote Linux machine and check the logs
> https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/launching-instance.html
> https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AccessingInstancesLinux.html
> https://docs.aws.amazon.com/managedservices/latest/userguide/access-to-logs-ec2.html

- Scenario 2: Download/Upload file from/to a remote Linux server
> https://docs.aws.amazon.com/zh_cn/AWSEC2/latest/UserGuide/AccessingInstancesLinux.html#AccessingInstancesLinuxSCP

- Scenario 3: Check network connectivity on a linux machine
> https://geekflare.com/linux-test-network-connectivity/

- Scenario 4: Setup a daily job on a Linux machine
> https://phoenixnap.com/kb/set-up-cron-job-linux

## Top level directories

- /bin – binary or executable programs.
- /etc – system configuration files.
- /home – home directory. It is the default current directory.
- /opt – optional or third-party software.
- /tmp – temporary space, typically cleared on reboot.
- /usr – User related programs.
- /var – log files.

## color code
bashrc file is a script file that's executed when a user logs in

PS1 represents the primary prompt string (hence the “PS”) - which is what you see most of the time before typing a new command in your terminal.

kube ps1 K8S命令行的颜色标注。

production用一个完全不一样的颜色，可以提示路径。
最好production不允许从本地访问，设置在另外一个地方，需要额外的login才可以访问，为了安全。

function parse_git_dirty {
         [[ $(git status 2> /dev/null | tail -n1) != "nothing to commit, working tree clean" ]] && echo "*"
}

function parse_git_branch {
        git branch --no-color 2> /dev/null | sed -e '/^[^*]/d' -e "s/* \(.*\)/(\1$(parse_git_dirty))/"
}

export PS1='\[\e[\033[01;34m\]\u@\h \[\e[38;5;211m\]\W\[\e[\033[38;5;48m\]$(parse_git_branch)\[\e[\033[00m\]\$'

### Scenario #2

Download/Upload file from/to a remote Linux server

Ref:https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AccessingInstancesLinux.html#AccessingInstancesLinuxSCP

### Scenario #3

Setup a daily job on a Linux machine

Ref: https://phoenixnap.com/kb/set-up-cron-job-linux

Chronos->Cronus->Cron

# Regex
## Regex basic
Regular Expressions (regex or regexp) are a very useful tool to identify specific patterns in any text, which helps to extract information regardless the format of the text.
Regex can be used to validate inputs, web scrapping, finding specific strings in documents, syntax validation for compilers, and so many others examples.
Regex is widely used in multiple programming languages using almost the same syntax, so this article pretends to show the basic regex operators.
> https://github.com/JiangRenDevOps/DevOpsLectureNotesV6/blob/master/WK5_Linux_Bash_Basics/regex_basics.md

## Regex practice
> https://github.com/JiangRenDevOps/DevOpsLectureNotesV6/blob/master/WK5_Linux_Bash_Basics/regex_practice.md
> https://regexr.com/

## Bash Script
Bash scripting is a powerful and versatile tool for automating systemadministration tasks, managing system resources, and performing otherroutine tasks in Unix/Linux systems. Some advantages of shell scripting are.
- Automation - Shell scripts allow you to automate repetitive tasksand processes, saving time and reducing the risk of errors that canoccur with manual execution.

    1. 版本检查
    2. login auth
    3. VM 有-交给程序员； 没有-创建一个

    一个简单的自动登录能帮助公司程序员节省时间，用量大。

- Portability: Shell scripts can be run on various platforms andoperating systems, including Unix, Linux, macOS, and even Windowsthrough the use of emulators or virtual machines.
- Flexibility: Shell scripts are highly customizable and can be easilymodifed to suit specifc requirements.They can also be combinedwith other programming languages or utilities to create morepowerful scripts.
- Accessibility: Shell scripts are easy to write and don't require anyspecial tools or software.They can be edited using any text editorand most operating systems have a built-in shell interpreter
- Integration: Shell scripts can be integrated with other tools andapplications, such as databases, web servers, and cloud servicesallowing for more complex automation and system managementtasks.
- Debugging: Shell scripts are easy to debug, and most shells havebuilt-in debugging and error-reporting tools that can help identifyand fx issues quickly.

### Bash script error handling

set -e is used to enable automatic error handling. If any command in the script exits with a non-zero status, the script will terminate immediately.

The trap command is used to catch the ERR signal (which indicates an error) and call the handle_error function.

### Bash script loop

- The for loop iterates over a sequence of values, in this case, the numbers from 1 to 5. Inside the loop, it prints out each iteration.
- The while loop executes a block of code as long as a specified condition is true. In this example, the loop continues as long as the value of the $counter variable is less than or equal to 5. Inside the loop, it prints out each iteration and increments the counter variable.

# Homework
- Add regex validator for pull request merge / squash to make sure the pull
request tittle is start with the jiangren jira issue format “jiangren-1234”
- Play the game bashcrawl （ git clone https://gitlab.com/slackermedia/bashcrawl.git bashcrawl）
- Use at least five diff commands/ways to write “Hello Jiangren” to a txt file
- **学习和记忆Special Characters很重要**
