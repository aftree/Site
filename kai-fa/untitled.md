# 软件开发模式

四个开发模式意思:

TDD：测试驱动开发（Test-Driven Development）

BDD：行为驱动开发（Behavior Driven Development）

ATDD：验收测试驱动开发（Acceptance Test Driven Development）

DDD：领域驱动开发（Domain Drive Design）

1、TDD：测试驱动开发（Test-Driven Development）

测试驱动开发是敏捷开发中的一项核心实践和技术，也是一种设计方法论，TDD首先考虑使用需求（对象、功能、过程、接口等）

主要是编写测试用例框架对功能的过程和接口进行设计，而测试框架可以持续进行验证。大行其道的一些模式对TDD的支持都非常不错，比如MVC和MVP等

2、BDD：行为驱动开发（Behavior Driven Development）

也就是行为驱动开发。这里的B并非指的是Business，实际上BDD可以看作是对TDD的一种补充，让开发、测试、BA以及客户都能在这个基础上达成一致，JBehave之类的BDD框架

3、ATDD：验收测试驱动开发（Acceptance Test Driven Development）

通过单元测试用例来驱动功能代码的实现，团队需要定义出期望的质量标准和验收细则，以明确而且达成共识的验收测试计划（包含一系列测试场景）来驱动开发人员的TDD实践和测试人员的测试脚本开发。面向开发人员，强调如何实现系统以及如何检验

4、DDD：领域驱动开发（Domain Drive Design）

DDD指的是Domain Drive Design，也就是领域驱动开发,DDD实际上也是建立在这个基础之上，因为它关注的是Service层的设计，着重于业务的实现,将分析和设计结合起来，不再使他们处于分裂的状态，这对于我们正确完整的实现客户的需求，以及建立一个具有业务伸缩性的模型

