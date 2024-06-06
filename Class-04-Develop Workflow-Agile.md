# Class-04-Development workflow-Agile Methodology

# Agenda
• Agile Methodology & Agile Mindset
• Agile Principle & Values, Agile Frameworks, Agile in Practise
• Backlog / story / DFD / Breakdown / Estimation
• Agile best Practices & Agile Tools
• Scenario

DevOps - 要有accountability and responsibility 以及 ownership

# Waterfall ---> Agile
## Software Development Life Cycle (SDLC)

The software development lifecycle (SDLC) is the cost-effective and time-efficient process that development teams use to design and build high-quality software. The goal of SDLC is to minimize project risks through forward planning so that software meets customer expectations during production and beyond.

## SDLC Model – Waterfall & V Model

Majorly there were three models to choose or to adapt to your process's they are as follows：

Waterfall model - The waterfall model is a sequential design process, often used in software development processes, in which progress is seen as flowing steadily downwards (like a waterfall) through the phases. 

V model - The V-model represents a software development process which may be considered an extension of the waterfall model. Instead of moving down in a linear way, the process steps are bent upwards after the coding phase, to form the typical V shape.

作为DevOps员工，要思考我在DevOps的领域 和 Agile的环境中学到什么。分析团队的工作方式和优缺点，以及做的不好的内容和没有做的内容可以运用什么方法论和工具改进。注重方法论才知道接下来如何去做。不能只关注怎样做和用什么技术去做，回归理论知识上，要清楚自己要做什么，要知道团队需要什么。

## Waterfal
项目的Project Management容易失败，三要素：On time, On budget, within Scope，有一点没满足及失败。在传统PM中使用waterfal，遵循流程开发，步骤无法go back。

## Agile

敏捷开发是一种理念/精神/价值观，而并不提供具体的方法。

Agile project management is an iterative development methodology that values human communication and feedback, adapting to changes, and producing working results.

### SDLC Model - Agile

Agile model - Agile Modeling is a practice-based methodology for modelling and documentation of software-based systems. It is intended to be a collection of values, principles, and practices for modelling software that can be applied on a software development project in a more flexible manner than traditional Modelling methods

So, before getting down deeper to choose your model for the development process, you have to be aware of the technical processes involved in the complete lifecycle, that starts from the business analysis, requirements, and continues till providing the baseline information for the disposal process of the entire lifecycle.

每一个循环周期是一个iteration，被称为一个Sprint。一般的AU的Sprint是2 weeks - 3 weeks。
一次性的交付变成了频繁的小规模交付（Breakdown）
 收集反馈
 最小化Risk

### Agile (Modern) vs Waterfall (Traditional)
过去的员工职责非常清晰，项目中不同组别轮流进场，成员之间需要大量的Handover
注重流程，详细的工作文档
50% of knowledge gets lost in handoffs

敏捷开发要求不同的成员担任多个角色，比如需要devops做testing的工作，也需要团队成员有更广的知识面和更多的技能。



### Agile Values

  Individuals and Iteractions
  Working project
  Customer collaboration
  Resonding to change

### 12 Agile Principles Behind The Agile Manifesto
 1. Our highest priority is to satisfy the customer through early and continuous delivery of valuable software.
 2. Welcome changingrequirements, even late indevelopment. Agileprocesses harness change forthe customer's competitiveadvantage.
 3. Deliver working softwarefrequently, from a couple ofweeks to a couple of months,with a preference to theshorter timescale.
 4. Business people anddevelopers must worktogether daily throughoutthe project.
 5. Build projects aroundmotivated individuals. Givethem the environment andsupport they need, and trustthem to get the job done.
 6. Agile processes promotesustainable development. Thesponsors, developers, andusers should be able tomaintain a constant paceindefinitely.
 7. Working software is theprimary measure of progress.
 8. The most efficient andeffective method ofconveying information to andwithin a development team isface-to-face conversation.
 9. Continuous attention totechnical excellence andgood design enhances agility.
 10. Simplicity- the art ofmaximizing the amount ofwork not done -is essential.
 11. The best architectures,requirements, and designsemerge from self-organizingteams.
 12. At regular intervals, the teamreflects on how to becomemore effective, then tunesand adjusts its behavioraccordingly.

游戏理解不同的反馈模式对于结果的影响 (Agile - instant feedback)
delayed feedback & instant feedback 
https://www.boxuk.com/battleships/ 

### Why Agile & Agile Principal
从熟悉的领域入手向不熟悉的领域推进。

Agile的原则是：快速反馈，交付价值，迭代开发
减少浪费、快速迭代、不断迭代、交流沟通、响应变化

### Agile mindset
抛弃原先的思维方式，不要期望一开始就能交付一个完美的产品。
Frequent Delivery instead of Big Bang Project.
Iteration instead of trying to get it right the first time.
通过熟悉的领域和已经有的知识发现我能做什么，不断的尝试和收集feedback，不断改进才能交付更多的东西。
Continuous Improvement instead of maintaining the status quo
Teamwork instead of individual working in parallel
Cross-functional teams instead of functional silos
Team autonomy instead of command & control

## 面试常见问题
 1. Tell me how you like to work?
 2. Give me an example of a project that didn't go well.
 3. Why do you think it was a failure?
 4. Could there be anything done differently in order to turn it into success?

# Agile framework and practice

## Scrum

Scrum is an agile project management framework within which people can address complex problems, while productively and creatively delivering products of the highest possible value.

Scrum is:
• Lightweight tool for enabling business agility
• Simple to understand, yet difficult to master

挑战：
任务切分无法切分的非常好，会产生工作冲突。
任务breakdown成多少个球。
ticket的难度估计。
任务delay会产生连锁delay。

Product Backlog 总任务清单
Sprint Planning Meeting 决定提什么任务到该Sprint的任务清单中
Sprint Backlog  当前Sprint的任务清单 - 一般的，放进来的任务就是要完成的任务。
Dev daily scrum 从当前Sprint的任务清单中抢球，从任务清单中取任务，完成并交付
两周后做Sprint review 对成果和成员进行review，成果是否符合交付要求，团队成员需要改进的
Sprint Retrospective meeting 成员回顾，做的好的&不好的

### Scrum 3355

• 3 Roles: Product Owner, Scrum Master, Team Members
• 3 Artifacts: Product Backlog, Sprint Backlog, Increment
• 5 Events: Sprint(产品列表精化), Sprint Planning, Daily Scrum(daily stand up meeting), Sprint Review, Sprint Retrospective
• Value propositions: Commitment, Focus, Openness, Respect, Courage



### Scrum Master
Leader role
成员保护，不会收到团队外的challenge
QA Check
Team Building
根据需求招人
Run meeting

不是manager，而是帮助role

### Product Backlog
A wish list
An ordered list of features that need to be delivered to create or enhance the product
The highest value items and the items containing most risk should move towards the top.

### User Story

描述一个ticket的方法，
As (a user), I want(to do sth), So that (I can achieve some purpose)
以该方式描述业务需求

User Story 分为不同的级别 对于不同的级别有不同的story point

### Task Estimation
对任务投票，对task的难度达成内部共识。

### Scrum Meeting
- Sprint(产品列表精化) - 精化Backlog

- Sprint Planning -提取任务

- Daily Scrum(daily stand up meeting)
 1. 昨天做了什么
 2. 今天要做什么
 3. help needed
 No technical detials - not for problem solving
 Commitment in front of peers

- Sprint Review
现在做的东西需要给stakeholders展现一个demo

- Scrum Retrospective Meeting

Definition:A retrospective is a meeting held after a product ships to discuss what happened during the product development and release process, with the goal of improving things in the future based on those learnings and conversations.

What is the ideal outcome of a retrospective meeting?
Every retrospective should at a minimum result in a list of “things that went well” and “things that could use improvement.” Those lists may not be particularly long and exhaustive, but each project probably has a few standouts in each column.

白板 - 四个column
Whats Good？, Keep doing, Whats Bad?, stop doing

## Kanban
manage and track Sprint progress
从左到右有多个column，内容可自己定义
一般是单向的，特殊情况如 testing 和 review 没通过才会被拉回来。

## Other Agile Ceremonies
1:1
Demo
Test/QA Demo
Brainstorming / Spiking

## 常见工具
Azure DevOps
Jira Software
Trello
Github Project

## GoodBest Development Practices
CI/CD

Pair Programming
一个人写，一个人看，互补，现场review

Code Quality & Refactoring
代码审查 & 代码重构

Test-Driven Development (TDD)
更改开发顺序，先写test再写code。可提高项目质量

## Scenario Microsoft - Win10

### WasS
Windows as a Service - rings

Waterfall ---> Agile
Personal offices ---> team rooms
Long planning ---> continual planning & learninng
100 page spec doc ---> specs in PPT
4-6 month ---> 3 weeks sprint

### Teams
团队结构的变化
成员角色的变化

## Self Forming Teams
18个月可以换工作方向
保持团队的活跃度

## Spring Mail
邮件的形式开始或回顾每个Spring




### Reference Reading

What is Agile?
https://docs.microsoft.com/en-us/azure/devops/learn/agile/what-is-agile
What is Scrum?
https://docs.microsoft.com/en-us/azure/devops/learn/agile/what-is-scrum
What is Kanban?
https://docs.microsoft.com/en-us/azure/devops/learn/agile/what-is-kanban
What is Agile Development?
https://docs.microsoft.com/en-us/azure/devops/learn/agile/what-is-agile-development
How to choose a right agile process?
https://docs.microsoft.com/en-us/azure/devops/boards/work-items/guidance/chooseprocess?view=azure-devops&tabs=basic-process
Plan and track your work
https://docs.microsoft.com/en-us/azure/devops/boards/get-started/plan-track-work?view=azuredevops&tabs=agile-process
Agile and Scrum Questions
1. What is Agile? What’s the difference with Waterfall?
2. What are the three roles on the scrum team?
3. Scrum does not have a role called “Product Manager”. True or False?
4. What is retrospective meeting? What’s the expected outcome of it?
5. How to write a quality product backlog item?
6. When might a sprint be cancelled?
A. When the Development Team feels that the work is too hard.
B. When the sales department has an important new opportunity.
C. When the Sprint Goal becomes obsolete.
D. When it becomes clear that not everything will be finished by the end of the Sprint.
7. Which statement best describes a Product Owner's responsibility?
E. Managing the project and ensuring that the work meets the commitments to the
stakeholders.
F. Keeping stakeholders at bay.
G. Manage the product backlog. Set product priority and optimizing the value of the work
the Development Team does.
H. Directing the Development Team.
Azure DevOps Lab Practice
1. Open https://dev.azure.com
1. Login with your Microsoft Account or create a new account and login
2. Create a new organisation on your Azure DevOps
3. Configure your demo project environment (Task 1 only)
https://azuredevopslabs.com/labs/azuredevops/prereq/
4. Finish Azure DevOps Lab agile task https://azuredevopslabs.com/labs/azuredevops/agile/
