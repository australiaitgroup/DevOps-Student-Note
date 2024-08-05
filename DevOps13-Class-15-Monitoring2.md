# Class-15-Monitoring2
# Lecturer: Kewei Zhang

# 学习内容
- [Monitoring L01 Recap](#monitoring-l01-recap)
- [Monitoring L02](#monitoring-l02)
- [Tracing(追踪)](#tracing----)
  * [Tracing 的作用](#tracing----)
    + [性能监控和瓶颈分析](#---------)
    + [故障排查和错误诊断](#---------)
    + [**依赖关系分析**](#----------)
    + [请求流分析](#-----)
    + [用户体验优化(代码优化)](#------------)
  * [例子](#--)
- [Dashboard 仪表盘](#dashboard----)
  * [Dashboard的不同类型](#dashboard-----)
    + [业务指标 Error Budget](#-----error-budget)
- [Hands on 1](#hands-on-1)
- [Hands on 2](#hands-on-2)
  * [负载测试 Load Testing](#-----load-testing)
    + [负载测试的关键因素](#---------)
    + [负载测试的应用场景](#---------)
- [Hands on 3](#hands-on-3)
- [Trouble shooting 策略](#trouble-shooting---)
- [Interview Questions - Monitoring的面试量不大，一般是有一，两道题](#interview-questions---monitoring----------------)
- [Homework](#homework)

# Monitoring L01 Recap

- Monitoring的重要性
  - 分析长期趋势;时间或实验组间的比较;警报;构建仪表板;调试
- 什么是 metrics 指标
  - metrics 是数值测量 通常以时间序列存储
- 了解 Prometheus 的配置和基本原理.
- 了解 Developer 如何在app client端设置测量工具(app instrumentation)
- 了解如何用 StatsD 收集和整合指标数据
- 了解四种 prometheus的指标数据类型-counter,gauge,histogram,summary熟悉如何使用PromQL回答基本的问题-比如吞吐量throughput，延迟latency，和分位数Quantile
- SRE Golden Signals 概念与原理。

# Monitoring L02

- Tracing 追踪
- Dashboard 仪表盘
- 开发我们第-个Dashboard-Grafana Hands-on·用Terraform 来管理Dashboard
- Load Testing 负载测试
- Setup locust traffic generator-Hands-on
- 面试题模拟

# Tracing(追踪)

Tracing 是一种收集、记录和分析软件运行时行为的技术，特别是在处理单个请求或事务时。通过追踪请求从开始到结束的完整生命周期，可以获取到细粒度的性能数据，这包括服务**调用顺序**、响应时间、资源消耗等信息。(更细颗粒度的分析)

Tracing 对于微服务和分布式服务系统的记录和分析尤为重要。

- Trace(追踪):表示一个请求在系统中的完整生命周期。一个 Trace由多个 Span 组成。

- Span(跨度):表示一个请求在系统中某个组件或服务中的执行片段。每个 Span 包含开始时间、结束时间、操作名称和相关的元数据。

- Context(上下文):用于在服务之间传播追踪信息，确保跨服务的追踪数据能够关联起来的metadata。比如 Trace lD, Span lD, ParentSpan lD.

为什么有效？有Trace ID，ID有context，可通过关系找到具体问题。

## Tracing 的作用
### 性能监控和瓶颈分析
通过追踪一个请求在系统中的传播路径，可以识别出响应时间长的服务或操作，从而定位性能瓶颈。

帮助开发者优化系统性能，提高用户请求的响应速度。

### 故障排查和错误诊断
当系统出现故障时，分布式追踪可以帮助快速定位问题所在的服务或代码段，缩短故障排查时间。

记录错误和异常的上下文信息，帮助开发者理解问题的根本原因。
- 这里可以通过服务名称，自定义的标签或注释或者和日志相结合来定位问题代码。

### **依赖关系分析**
分布式系统中，服务之间的依赖关系复杂。通过分布式追踪，可以清晰地展示各个服务之间的调用关系。

帮助识别和管理服务间的依赖，确保系统的稳定性和可靠性。

### 请求流分析
分析请求在系统中的流转路径，了解系统的工作负载和流量模式。

帮助进行容量规划和资源优化。

### 用户体验优化(代码优化)
通过追踪用户请求的整个生命周期，识别出用户体验中的瓶颈和延迟，进行针对性的优化。

提高用户满意度和系统的可用性。

## 例子
```
trace_id:7890
|-- span_id:a1 (User Service)       20ms
  |-- span_id:b1 (Order Service)    5000ms(超时)
    ｜-- span_id:c1 (Inventory Service)    50ms
    ｜-- span_id:d1 (Payment Service)      60ms
```
- 假设你运行一个电子商务平台，由多个微服务组成，包括
- 用户服务(User Service)、
  - 订单服务(Order Service)O
  - 库存服务(Inventory Service)、
  - 支付服务(Payment Service)等。

- 最近，你注意到用户在提交订单时经常遇到超时问题，导致用户体验不佳

- 首先，你的服务已经集成了分布式追踪系统.

- 在追踪系统的 U1中搜索与超时请求相关的 trace。假设你找到一个 trace，ID为 trace id:7890。打开这个
trace，你会看到如下的瀑布图结构

- 依赖关系故障排查:
  - 点击订单服务的 span(span_id: b1)，查看更详细的信息，包括以下内容:开始时间和结束时间:确认 span 确实花费了 5000 毫秒。标签和注释:检查是否有任何错误信息或异常。日志:如果 span 包含日志记录，可以查看详细的日志信息。
  - 在 span 的详细信息中，你发现订单服务在调用支付服务后执行了一些数据库操作，导致了超时。
  - 根据 span 信息中的服务名称和操作名称，定位到具体的代码位置。例如，订单服务的操作名称为processOrder，在代码库中搜索OrderService类的processOrder 方法并优化。
  - 将优化后的代码部署到测试环境，验证是否解决了超时问题。通过分布式追踪系统再次查看相关的trace，确保订单服务的执行时间在预期范围内。

- 请求流分析用户体验优化:
  - 通过在高峰期和非高峰期的tracing 对比可以了解系统在高峰期时的工作负载和流量模式。
  - 例如:在高峰期，大量请求集中在用户服务和订单服务，这些服务处理的请求数远高于其他服务。
  - 支付服务的处理时间相对较长，可能是系统响应变慢的一个原因。
  - 通过增加用户服务和订单服务的实例或者对该服务进行扩容从而实现用户体验优化。


# Dashboard 仪表盘
Dashboard 是一个图形界面工具，用于展示**关键信息和指标**，以便**快速查看和分析**。它**汇聚了来自多个源的数据**，以图表、指标和汇总信息的形式展示，帮助用户实时了解系统、应用程序或业务流程的状态和性能。

- 在 DevOps 实践中，Dashboard 是重要的工具，因为它提供了一个**集中化的平台**，让团队成员可以实时监控和了解软件开发和部署的各个阶段。

- 通过 Dashboard，团队可以快速识别问题、监控部署的效果、衡量改进的成效，并确保服务的稳定性和可靠性。

- 此外，Dashboard 促进了跨功能团队之间的透明度和沟通，帮助团队成员基于共享的数据做出决策，从而提高了整体的协作效率和软件交付的质量。

## Dashboard的不同类型

1. **实时监控**
  - CPU使用率、内存消耗、响应时间、流量数据

2. **性能分析**
 - 如响应时间的长期趋势、Error的比率等。

3. **部署和运维**
 - Docker image build time, Mean time to deployment。

    监控CICD使用的时间

4. **业务指标**
 - 云服务成本，SLA, SLO, SLI (产品本身)

  - **SLA（服务级别协议）** - 和客户之间的**约定**
    - 服务级别协议（Service Level Agreement, SLA）是服务提供商与客户之间达成的正式协议，定义了服务的具体性能和可用性标准。SLA 通常包括以下内容：
      - 服务可用性：例如，服务每月的正常运行时间应达到 99.9%。
      - 响应时间：例如，客服支持在收到问题报告后 24 小时内回复。
      - 处罚条款：如果服务提供商未达到 SLA 中的标准，可能需要支付罚金或提供服务折扣。
   - SLA 的目的是明确服务提供商和客户之间的期望，确保服务质量。

  - **SLO（服务级别目标）** - 团队设定的**目标**来确保达到SLA
  - 服务级别目标（Service Level Objective, SLO）是SLA的组成部分，定义了实现SLA所需的具体目标。SLO是服务性能的内部目标，用于帮助团队衡量和管理服务质量。示例如下：
    - 可用性目标：服务的正常运行时间应达到99.9%。
    - 性能目标：API请求的平均响应时间应低于200毫秒。
    - 错误率目标：每月的请求错误率应低于0.1%。
  - SLO帮助服务提供商内部团队设定明确的性能和可靠性目标，以便实现SLA的要求。

  - **SLI（服务级别指标）** - 用于测量服务表现的**指标**，来评估是否达到SLO
  - 服务级别指标（Service Level Indicator, SLI）是用于衡量服务性能的具体指标。这些指标为团队提供了有关服务健康状况和性能的数据。SLI是实现SLO的基础。例如：
    - 可用性指标：测量服务在一段时间内的正常运行时间。
    - 延迟指标：测量请求的响应时间。
    - 错误率指标：测量在特定时间段内失败请求的比例。
  - SLI提供了量化的数据，帮助团队监控服务并确保达到SLO目标。

### 业务指标 Error Budget

- **错误预算（Error Budget）** 是服务级别目标（SLO）中的一个关键概念，用于管理和容忍一定范围内的服务故障和不稳定性。它为团队提供了一个可以容忍的错误和停机时间的限度，以确保服务的高可用性和稳定性。

- **错误预算的计算**
  - 错误预算通常是基于 SLO 的可用性目标来计算的。例如，如果一个服务的可用性 SLO 是 99.9%，那么每个月有 0.1% 的时间可以容忍服务故障或不稳定。
    - 每月错误预算：假设一个月有 30 天，每天 24 小时，每小时 60 分钟，总共有 43,200 分钟。如果 SLO 的可用性目标是 99.9%，那么允许的停机时间是 0.1%。
      - 每月的错误预算 = 总时间 * (1 - SLO)
      - 每月的错误预算 = 43,200 分钟 * (1 - 0.999) = 43.2 分钟

# Hands on 1
使用Grafana创建仪表盘
  - Error rate
  - Throughput
  - Average Request Duration
  - Latency
  - Saturation : CPU, Memory, Disk Usage

  Hands on: https://github.com/australiaitgroup/DevOpsNotes/blob/main/WK8_Monitoring_2/docs/01.build_dashboards.md 

# Hands on 2
使用Terraform 管理Grafana Dashboard
  - Source Control Grafana Dashboard
  - Backup Grafana Dashboard

Hands on: https://github.com/australiaitgroup/DevOpsNotes/blob/main/WK8_Monitoring_2/docs/02.terraform_grafana.md

Startup公司可能会用Grafana，因为开源，成本低，有现成包。
tf管理Grafana的目的是 source control。 做的dashboard的version control，可随时roll back。不一样的团队需要的dashboard不一样， back up的计划。

tf在工作中更多用于backup


## 负载测试 Load Testing
- 旨在评估应用程序或系统在特定负载条件下的表现。
- 这种测试主要关注于系统能够承受的用户或交易量，以及在这种压力下系统的响应时间和稳定性。
- 负载测试帮助识别性能瓶颈，确保软件在高负载下仍能正常运行，并验证系统的可扩展性。

### 负载测试的关键因素
- **目标和指标**： 做测试之前，要根据以往的经验确定预期的指标和目标，如响应时间，并发用户数，吞吐量等。
- **测试环境**： 最好选择非高并发环境测试，并且最好是与production env相似的测试环境。
- **测试工具**： 与dev讨论，哪些endpoint是我们在意的，重要的，有选择的测试。
- **执行和监控**：执行负载测试时，实时监控应用程序和基础设施的性能指标，以便及时捕捉任何潜在问题。
- **结果分析**：测试完成后，分析结果数据，识别任何性能瓶颈或稳定性问题，并提出优化建议。

### 负载测试的应用场景
1. **软件发布前**
   - **性能基准测试**：在软件发布前，确定系统在正常和高峰负载下的性能基准。
   - **架构验证**：验证系统架构的设计是否能够支持预期的负载水平。
   - **容量规划**：帮助确定在预期的负载增长下，系统需要多少资源才能维持性能目标。
   - **可靠性测试**：确保系统在长时间运行和处理大量请求的情况下仍然稳定可靠。

2. **软件发布后**
   - 更具需求周期性地进行以上测试

# Hands on 3
Setup traffic generator

python locust

# Trouble shooting 策略
识别**原因**和**symptom症状**，是一个诊断的过程，需要问对问题：

1. What makes you think there is a performance problem?  
2. Has this system ever performed well?  
3. What has changed recently? (Software? Hardware? Load?)  
4. Can the performance degradation be expressed in terms of latency or run time?  
5. Does the problem affect other people or applications (or is it just you)?  
6. What is the environment? Software, hardware, instance types? Versions? Configuration?  

# Interview Questions - Monitoring的面试量不大，一般是有一，两道题

- 描述您如何在过去的项目中实施监控解决方案
- 请描述一次您利用监控指标逐步定位并解决问题的经历


在一个分布式服务部署在云环境中的项目中，我实施了一套全面的监控解决方案。以下是具体步骤和细节：

1. **定义关键性能指标（KPIs）**：我们首先定义了关键性能指标（Google SRE Golden Signals），包括系统的延迟、吞吐量、错误率和系统资源利用率（如CPU和内存使用情况）。这些指标帮助我们准确了解系统的健康状况和性能表现。

2. **选择监控工具**：我们选择了Prometheus作为我们的监控工具，因为它支持多维数据模型，适合收集时间序列数据，且具有强大的查询语言。这使得我们能够灵活地定义和查询各种指标。

3. **自动发现（Auto Discovery）**：为了简化监控配置，我们实施了自动发现机制。通过定义统一的标签（label），Prometheus能够自动发现和监控新部署的服务实例。这不仅减少了手动配置的工作量，还确保了监控覆盖的全面性和及时性。

4. **可视化仪表板**：我们配置了Grafana来创建和可视化仪表板。通过这些仪表板，我们能够实时观察到系统的状态和性能指标，并在需要时深入分析具体问题。

5. **配置警报规则**：为了及时处理潜在的系统故障和性能问题，我们设置了基于阈值的警报规则。一旦某个指标超过正常范围，我们的团队就会通过邮件和Slack收到警报。这确保了我们能够在问题发生的早期阶段进行干预，避免更严重的后果。

6. **安全性考虑**：在监控系统的实施过程中，我们特别关注了数据的安全性。我们使用了加密协议来保护数据传输，并严格控制了访问权限，确保只有授权人员可以访问敏感的监控数据。

7. **基础设施即代码（IaC）**：我们确保所有的监控配置都在版本控制系统中进行管理。这不仅提高了配置的可重复性和可追溯性，还支持了基于基础设施即代码（IaC）的框架，使我们能够快速地进行环境的搭建和变更管理。

8. **定期复审和改进**：为了持续改进监控策略，我们定期进行后置复盘会议。我们讨论监控数据揭示的问题，并根据这些信息调整我们的监控配置和应用架构。这帮助我们不断优化系统的性能和稳定性。

# Homework
- Instruction：
  
  Build and run traffic generator from docs/03.setup_traffic_generator.md

https://github.com/australiaitgroup/DevOpsNotes/blob/main/WK8_Monitoring_2/docs/03.setup_traffic_generator.md

- ***在 Grafana 上设置 Alert 并与 Opsgenie 整合***

- Instruction: `docs/04.integrate_with_OpsGenie.md`

https://github.com/australiaitgroup/DevOpsNotes/blob/main/WK8_Monitoring_2/docs/04.integrate_with_OpsGenie.md

