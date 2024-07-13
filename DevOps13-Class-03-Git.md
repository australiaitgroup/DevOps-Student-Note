# Class-03 Git
## 主要知识点
- [Git Basics with Practices](#git-basics-with-practices)
  * [DevOps为什么要使用Git？](#devops------git-)
  * [What is a Version Control System?](#what-is-a-version-control-system-)
  * [Why are we using version control？](#why-are-we-using-version-control-)
  * [Version Control Glossary](#version-control-glossary)
  * [Centralized & Distributed](#centralized---distributed)
  * [What is Git?](#what-is-git-)
- [Step 0: 一个点的历史 – Git Setup](#step-0-----------git-setup)
  * [Git Global Setup](#git-global-setup)
  * [Git local – hands on](#git-local---hands-on)
  * [Setting up a repository 代码仓库](#setting-up-a-repository-----)
  * [git init initializes a brand new Git repository and begins tracking](#git-init-initializes-a-brand-new-git-repository-and-begins-tracking)
  * [Ignoring Files](#ignoring-files)
  * [Git Cloud Repository](#git-cloud-repository)
- [Step 1: 一条线的历史 – commit (一个人操作的时候)](#step-1-----------commit-----------)
  * [Git工作原理：](#git-----)
  * [Commit changes](#commit-changes)
  * [Undoing things](#undoing-things)
  * [Git undo changes](#git-undo-changes)
  * [Git status and history](#git-status-and-history)
- [Step 2: 两条线的历史 – branch & merge](#step-2-----------branch---merge)
  * [Git with branching](#git-with-branching)
  * [Merge a branch (git merge)](#merge-a-branch--git-merge-)
  * [Merge conflicts](#merge-conflicts)
  * [两种workflow: merge 和 rebase (无区别)](#--workflow--merge---rebase------)
  * [graph ---use git history plugin in VSCode ----you can get better visualazaiton](#graph----use-git-history-plugin-in-vscode-----you-can-get-better-visualazaiton)
- [Step 3: 多条线的历史 – 远程协作](#step-3---------------)
  * [Connect to remote repo](#connect-to-remote-repo)
  * [Set up tracking branch](#set-up-tracking-branch)
  * [Update local from remote](#update-local-from-remote)
  * [Update remote from local](#update-remote-from-local)
  * [Sync (VS Code)](#sync--vs-code-)
  * [Delete a remote branch](#delete-a-remote-branch)
  * [Pull Request (PR)](#pull-request--pr-)
  * [Ask git to ignore files](#ask-git-to-ignore-files)
  * [Takeaways](#takeaways)
  * [Force push](#force-push)
- [Homework / Challenges](#homework---challenges)
 
# 课堂笔记

# Git Basics with Practices

Lecturer: Ray Ma

今天上课内容是Git
• DevOps为什么要使用 Git？
• 如何设置 Git？
• Git 的基本工作方法
• 使用 Git branch 进行创建与合并分支
• 使用 GitHub 进行团队协作
• 使用 merge 和 rebase 进行日常协作

## DevOps为什么要使用Git？
需要一个工具串接Dev team和Operation team，用git拿Developer的Source Code，在auto test和deployment的过程中发现Code有问题无法通过要求，需要通过Git将code打回去。
作为DevOps需要写Script和Template，写出来的文件需要使用Git管理。
在某些公司DevOps role需要管理复杂的deployment pipeline，trim branch需要使用Git

## What is a Version Control System?
用于记录版本变更的系统，Git是一个version control 的工具。管理source code的版本。版本是用来记录下文本文件的修改，每次的修改，都会产生新的版本。

## Why are we using version control？
• 撤销改动或者回滚版本(snapshot)
文档产生修改之后，修改的内容希望被记录下来，因为删除的内容可能会希望找回，或者修改的内容经过若干版本后可能想要将文本改回到之前的某个状态。它会为每个版本提供一个snapshot，即记录每一次发生变化的部分，并形成修改的timeline，成线性可回溯。

• 回溯历史(A complete long-term history of every file that provides traceability)
在团队中，修改文档会有很多人，当发现文件有变化时，想要查看历史记录搞清楚为什么文件会变成今天的样子的时候，可以用版本控制回溯历史的任意一个snapshot，也可查找到版本修改的 who,when,why，以及修改的具体内容。

• 协同合作(via branching strategies)
协助团队共同修改文件，当两个人同时修改一个文件中的不同段落后，git可以帮助将不同的修改内容merge到一起。

• 备份（Backup）
git会记录下最新的版本，当本地的内容不小心被删除，可以使用git拉取最新的内容。


## Version Control Glossary
• Repository: Your project.
I created a new repository (repo) for my school project so we can collaborate more easily.
• Diff: The delta (additions and deletions) between two states of a project.
• Commit: A snapshot of your project’s state at a point in history. Records
the difference between two points in history with a diff.
• Branch: Modifications to a project (main branch = trunk) made in parallel
with the main branch, but not affecting the main branch
• Merge: Introducing changes from one branch into another.
• Clone: Downloading a local copy of a project.
• Fork: Your own version of somebody else’s project where you take the
original code-base and make modifications. May include many changes or
just a few bug-fixes. Sometimes you end up merging those changes back upstream



## Centralized & Distributed
Distributed version control: The key to rapid software development. Unlike a centralized version control system, a distributed version control doesn't have a single point of failure, because developers clone repositories on their distributed version control workstations, creating multiple backup copies.

Centralized version control: A relic from the past
A centralized version control system relies on a central server where developers commit changes. Users like centralized systems, because they’re simple to set up and provide admins with workflow controls. Centralized vcs, like Subversion, CVS, and Perforce, solve the age-old problem of manually storing multiple copies on a hard drive, but the few benefits don’t outweigh what’s at risk from relying on a single server.

If the only copy of a project becomes corrupted or goes down, developers are unable to access the code or retrieve previous versions. Also, remote commits are extremely slow, because users must commit through a network to the central repository, which can slow down systems. Users must also be in network to push changes, limiting where and when developers can commit. Merging and branching are also difficult and confusing, since contributors have to track merges and branch as a single check-in.

Distributed version control: The key to rapid software development
Unlike a centralized version control system, a distributed version control doesn’t have a single point of failure, because developers clone repositories on their distributed version control workstations, creating multiple backup copies. If the source code is corrupted, teams can use any developer’s clone as a backup, increasing security since there’s little risk of losing a project’s entire history.

Also, because there are local copies, developers can commit offline, which offers flexibility in their personal workflow and prevents having to commit as a giant changeset. Distributed version control, such as Git, Bazaar, and Mercurial, offers fast branching, because there’s no communication with a remote server - everything is done on a local drive.

## What is Git?
Git is a distributed version-control system for tracking changes in
source code during software development.
It is designed for coordinating work among programmers, but it
can be used to track changes in any set of files. Its goals include
speed, data integrity, and support for distributed, non-linear
workflows

# Step 0: 一个点的历史 – Git Setup
Git 是CLI 工具，用命令确认Git是否已经安装，打开任意一个command line工具输入git --version

Git官网有提供GUI，图形化界面。

## Git Global Setup  
```
git config --global user.name "your name"
git config --global user.email "your email add"
git config --global core.editor "code --wait"
```
Note: we use VSCode as our editor, with global it affects your whole computer setting, without global, it only affects current repo


## Git local – hands on
  不加--global就可以设置local config，local config的优先级高于global config

## Setting up a repository 代码仓库
只有文本文件才能被Git管理，word不可以，word是复杂文件。

## git init initializes a brand new Git repository and begins tracking
an existing directory. It adds a hidden subfolder within the
existing directory that houses the internal data structure required
for version control.

```
mkdir learngit
cd learngit
pwd
/Users/jonny/learngit
git init
```
or we use git clone
```
git clone 

```

Let’s change README.md file and practice commands again


## Ignoring Files
• Often, you’ll have a class of files that you don’t want Git
to automatically add or even show you as being
untracked. These are generally automatically generated
files such as log files or files produced by your build
system. In such cases, you can create a file listing
patterns to match them named .gitignore


A collection of .gitignore templates:
<https://github.com/github/gitignore>


## Git Cloud Repository

Git remote – hands on
"Create a "repository
https://github.com/new
NOTE: you must have a github account


# Step 1: 一条线的历史 – commit (一个人操作的时候)

## Git工作原理：
git 需要两步 commit
working directory 有些修改只是为了帮助手头工作，不需要track，需要被track的文件需要放在stage里面
```
- git add <file>...
- git add . 提交所有文件到stage（. 表示该目录下的所有文件）
```
当所有的修改任务都完成了，double check后需要将修改作为新的版本提交
```
- git commit
```
修改的内容会形成一个snapshot进入 git的repo里面（.git)

用vscode编辑新的文件后，文件后面的 'U' 是untracked, 'A' 是Add to stage(点击加号或用git add + <文件名>)
点击commit之前要添加message信息记录版本和修改，点击commit后文件将tracked
```
- git restore <file>...  to discard changes in working directory)
- git rm <file>... 将文件删掉，git将不再track该文件
- git stash  把当时的变更暂存，给一个新的stash名字保存，修改的内容放在了stash中，文件恢复到原来的状态，在vscode中你可以运用apply stash恢复刚刚修改的内容
```
## Commit changes
Commit message 要有意义
回顾commit history的时候只能通过message判断commit的用处和意义,格式是有convention的（ticekt_id+description）
  
## Undoing things
• Undoing the last commit/comment: git commit --amend
• git rm test.txt
• git clean Remove untracked files from the working tree
• git stash Stash the changes in a dirty working directory away
• git stash
• git stash list
• git stash apply
• git stash pop stash@{0}

## Git undo changes
  checkout   删除的东西想找回了，可以用checkout找回来
  clean      建立的很多测试文件可以用clean命令批量清理掉，由于是删除命令，需要强制(-f)执行
  revert     当需要版本回滚到之前某个版本。（git log 拿到 版本的SHA）用一个新的commit对历史记录进行回滚
             revert可能会有冲突（之后的commit可能会对本次的修改有依赖，需要专门处理冲突）
  reset      回滚到某一个之前的版本，该版本之后的所有commit都会被删掉。分soft（时间点后的所有变更都放入  
             working folder里面）和 hard（直接删除时间点后的所有变更）
  
```
git checkout .
git clean -f
git revert <SHA>
git reset <SHA>
```

## Git status and history

commands we use:

```
git status
git diff
git log(--all --decorate --oneline --graph)

```

# Step 2: 两条线的历史 – branch & merge
main or master branch 是主分支 production environment
your branch  ---> dev environment

合并代码到主分支是your branch合并到main or master branch

- 创建新的分支feat1
```
git checkout -b feat1
```
- 选取分支
```
git checkout <分支名>
```

- branching commands:
```
git branch     (查看分支情况）
git branch -d （删除branch）
git checkout -b（建立branch）
```

## Git with branching
• Create a branch (git branch)
• Checkout a branch (git checkout)

• Merge a branch (git merge)
• Rebase a branch (git rebase)

• Create a branch (git branch)
• Checkout a branch (git checkout)


git branch crazy-experiment

Note that this only creates the new branch. To start adding commits to it, you need to select
it with git checkout, and then use the standard git add and git commit commands.
git checkout –b crazy-experiment

## Merge a branch (git merge)
Git merge lets you take the independent lines of development created by git branch and integrate them into a single branch. Git merge will combine multiple sequences of commits into one unified history. In the most frequent use cases, git merge is used to combine two branches.
```
- Start a new feature
git checkout -b feat1
- Edit some files
git add <file>
git commit -m "Start a feature"
- Edit some files
git add <file>
git commit -m "Finish a feature"
- Merge in the feat1 branch
git checkout main
git merge new-feature
git branch -d new-feature
```
## Merge conflicts

如果不想解决冲突：
```
git merge <branch name> --abort
git merge <branch name> --overwrite-ignore
git merge <branch name> --no-overwrite-ignore
```

## 两种workflow: merge 和 rebase (无区别)
branch多了之后历史记录会非常复杂，所以需要用rebase，
rebase - A clean, linear history free of unnecessary merge commits

Merging vs. Rebasing
Rebase a branch (git rebase)

## graph ---use git history plugin in VSCode ----you can get better visualazaiton 
   useful visualazaiton link:  <http://git-school.github.io/visualizing-git/>
   https://git-school.github.io/visualizing-git/#free


# Step 3: 多条线的历史 – 远程协作

## Connect to remote repo
用于手动建立或去除两个repo的联系（一般情况用不到）
```
git clone
git remote add <name> <url>
git remote rm <name>
git remote rename <old-name> <new-name>
```

## Set up tracking branch
也可以在push的时候建立这种联系
```
git branch -u <name>/<branch>
```

## Update local from remote
当 remote repo发生变化，本地会提示有更新，fetch命令用于比对本地和remote的repo是否有差异，发生了多少变更。
```
git fetch   (check if codes in both ends are the same)
git pull
```

## Update remote from local
```
git push <name> <branch>
git push <name> <local_branch>:<remote_branch>
```

## Sync (VS Code)
pull+push

## Delete a remote branch
```
git push -d <name> <branch>
git push <name> :<branch>

```

## Pull Request (PR) 

Pull requests let you tell others about changes you've pushed to a
GitHub repository. Once a pull request is sent, interested parties can
review the set of changes, discuss potential modifications, and even push follow-up commits if necessary

Note: PR--->加入一个人工的REVIEW， 一般实际生产环境 MAIN是受到保护的，需要权限。  in github ---> click "compare & pull request" button

Benefits:
• Peer Review
• Sufficient testing and better stability
• Reducing conflicts Continuous Delivery
• Clearer responsibility

When you push your branch, got the github repo

Before you create Pull Request, please check:
• Follow coding standard (code style, naming convention, etc. )
• Have tests
• No conflict
• DO NOT leave comment blank – add summary of this PR

Code Review：
• Two ways to show diff
• Single comment vs.multiple

Comments for Code
Review:
• LGTM – look good to me
• :+1
• Markdown

The reviewer should identify errors that will cause an issue in production.
• The reviewer should verify that the stated goal of the code change aligns with the changes
being made.
• The reviewer should verify that any changes align with the team’s coding standards.
• The reviewer should look for anything they personally disagree with


Pull Request (PR) merge strategy – github flow:
```
create a branch--> ADD commits -->open a pull request--> discuss & review--> Merge and Deploy

```
## Ask git to ignore files

- 各种文件 不属于 CODE， 如 MP3，MP4，JPEG，.exe 音频视频文件等
- 二进制文件（BINARY FILES）
- 路径 path and directory
- 需要添加 COMMIT进去 .gitignore 这个文件进去 （hidden file）
- GIT 只处理 文本格式 和 SOURCE CODE 文件


## Takeaways

• Never forget .gitignore
• Fix conflicts before PR
• Try to put all code for one function/subfunction/one bug fix in one git
commit, which also means the commit should be small enough
• Try to make sure every commit will not break the build pipeline, which
means it should pass code style check and unit tests

## Force push
(偶尔遇到的情况 用本地的时间线强行rewrite remote的时间线)
```
git push -f
git push <name> + <branch_name>
git push --force-with-lease
```
# Homework / Challenges

<https://github.com/JiangRenDevOps/DevOpsLectureNotesV6/blob/master/WK2_Git/git_exercise_1.md>
<https://github.com/JiangRenDevOps/DevOpsLectureNotesV6/blob/master/WK2_Git/git_exercise_2.md>
<https://learngitbranching.js.org/>


 
