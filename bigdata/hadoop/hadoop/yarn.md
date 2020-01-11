# Yarn



## Yarn的基本介绍 <a id="Yarn&#x7684;&#x57FA;&#x672C;&#x4ECB;&#x7ECD;"></a>

### Yarn基本定义： <a id="Yarn&#x57FA;&#x672C;&#x5B9A;&#x4E49;&#xFF1A;"></a>

Apache Hadoop YARN（Yet Another Resource Negotiator,另一种资源协调者）是一种新的Hadoop资源管理器，它是一个通用资源管理系统，可为上层应用提供统一的资源管理和调度，它的引入为集群在利用率、资源统一管理和数据共享等方面带来的巨大的好处。

### YARN在产品中的位置： <a id="YARN&#x5728;&#x4EA7;&#x54C1;&#x4E2D;&#x7684;&#x4F4D;&#x7F6E;&#xFF1A;"></a>

![yarn&#x4F4D;&#x7F6E;](https://cshihong.github.io/2018/05/11/Yarn%E6%8A%80%E6%9C%AF%E5%8E%9F%E7%90%86/yarn%E4%BD%8D%E7%BD%AE.png)

图：Yarn在FusionInsight产品中的位置

YARN是Hadoop2.0中的资源管理系统，它是一个通用的资源管理模块，可为各类应用程序提供资源管理和调度功能。

Yarn是轻量级弹性计算平台，除了MapReduce框架，还可以支持其他框架，如Spark、Storm等。

多种框架统一管理，共享集群资源的优点：

1. 资源利用率高
2. 运维成本低
3. 数据共享方便

### YARN的设计思路： <a id="YARN&#x7684;&#x8BBE;&#x8BA1;&#x601D;&#x8DEF;&#xFF1A;"></a>

YARN的设计是为了解决MapReduce1.0中的一些缺陷。

1. 存在单点故障。
2. JobTracker“大包大揽”导致任务过重（任务多时内存开销大，上限4000节点）。
3. 容易出现内存溢出（分配资源只考虑MapReduce任务数，不考虑CPU、内存）。
4. 资源划分不合理（强制划分为slot ，包括Map slot和Reduce slot）。

Yarn架构思路：

![&#x8BBE;&#x8BA1;](https://cshihong.github.io/2018/05/11/Yarn%E6%8A%80%E6%9C%AF%E5%8E%9F%E7%90%86/%E8%AE%BE%E8%AE%A1.png)

图：Yarn的架构思路：将原Job Tacker三大功能拆分

## Yarn的组件架构 <a id="Yarn&#x7684;&#x7EC4;&#x4EF6;&#x67B6;&#x6784;"></a>

### Yarn的组件架构： <a id="Yarn&#x7684;&#x7EC4;&#x4EF6;&#x67B6;&#x6784;&#xFF1A;"></a>

![Yarn&#x7EC4;&#x4EF6;&#x67B6;&#x6784;](https://cshihong.github.io/2018/05/11/Yarn%E6%8A%80%E6%9C%AF%E5%8E%9F%E7%90%86/Yarn%E7%BB%84%E4%BB%B6%E6%9E%B6%E6%9E%84.png)

图：YARN的组件架构图

三大组件的主要功能介绍：

**（1）ResourceManager：**

* 处理客户端请求
* 启动/监控ApplicationMaster
* 监控NodeManager
* 资源分配与调度

**（2）ApplicationMaster：**

* 为应用程序申请资源，并分配给内部任务
* 任务调度、监控与容错

**（3）NodeManager：**

* 单个节点上的资源管理
* 处理来自ResourceManger的命令
* 处理来自ApplicationMaster的命令

### ResourceManager： <a id="ResourceManager&#xFF1A;"></a>

ResourceManager（RM）是一个全局的资源管理器，负责整个系统的资源管理和分配，主要包括两个组件，即调度器（Scheduler）和应用程序管理器（Applications Manager）。

调度器接收来自ApplicationMaster的应用程序资源请求，把集群中的资源以“**容器**”的形式分配给提出申请的应用程序，容器的选择通常会考虑应用程序所要处理的数据的位置，进行就近选择，从而实现“计算向数据靠拢”。

容器（Container）作为动态资源分配单位，每个容器中都封装了一定数量的CPU、内存、磁盘等资源，从而限定每个应用程序可以使用的资源量。

调度器被设计成是一个可插拔的组件，YARN不仅自身提供了许多种直接可用的调度器，也允许用户根据自己的需求重新设计调度器。

应用程序管理器（Applications Manager）负责系统中所有应用程序的管理工作，主要包括应用程序提交、与调度器协商资源以启动ApplicationMaster、监控ApplicationMaster运行状态并在失败时重新启动等。

### ApplicationMaster： <a id="ApplicationMaster&#xFF1A;"></a>

ResourceManager接收用户提交的作业，按照作业的上下文信息以及从NodeManager收集来的容器状态信息，启动调度过程，为用户作业启动一个ApplicationMaster。

ApplicationMaster的主要功能是：

（1）当用户作业提交时，ApplicationMaster与ResourceManager协商获取资源，ResourceManager会以容器的形式为ApplicationMaster分配资源；

（2）把获得的资源进一步分配给内部的各个任务（Map任务或Reduce任务），实现资源的“二次分配”；

（3）与NodeManager保持交互通信进行应用程序的启动、运行、监控和停止，监控申请到的资源的使用情况，对所有任务的执行进度和状态进行监控，并在任务发生失败时执行失败恢复（即重新申请资源重启任务）；

（4）定时向ResourceManager发送“心跳”消息，报告资源的使用情况和应用的进度信息；

（5）当作业完成时，ApplicationMaster向ResourceManager注销容器，执行周期完成。

### NodeManager： <a id="NodeManager&#xFF1A;"></a>

NodeManager是驻留在一个YARN集群中的每个节点上的代理，主要负责：

* 容器生命周期管理。
* 监控每个容器的资源（CPU、内存等）使用情况。
* 跟踪节点健康状况。
* 以“心跳”的方式与ResourceManager保持通信。
* 向ResourceManager汇报作业的资源使用情况和每个容器的运行状态。
* 接收来自ApplicationMaster的启动/停止容器的各种请求 。

需要说明的是，NodeManager主要负责管理抽象的容器，只处理与容器相关的事情，而不具体负责每个任务（Map任务或Reduce任务）自身状态的管理，因为这些管理工作是由ApplicationMaster完成的，ApplicationMaster会通过不断与NodeManager通信来掌握各个任务的执行状态。

### YARN的部署： <a id="YARN&#x7684;&#x90E8;&#x7F72;&#xFF1A;"></a>

![Yarn&#x7684;&#x90E8;&#x7F72;](https://cshihong.github.io/2018/05/11/Yarn%E6%8A%80%E6%9C%AF%E5%8E%9F%E7%90%86/Yarn%E7%9A%84%E9%83%A8%E7%BD%B2.png)

图：Yarn和Hadoop平台其他组件的统一部署

在集群部署方面，YARN的各个组件是和Hadoop集群中的其他组件进行统一部署的.

### Yarn的工作流程： <a id="Yarn&#x7684;&#x5DE5;&#x4F5C;&#x6D41;&#x7A0B;&#xFF1A;"></a>

![&#x5DE5;&#x4F5C;&#x6D41;&#x7A0B;](https://cshihong.github.io/2018/05/11/Yarn%E6%8A%80%E6%9C%AF%E5%8E%9F%E7%90%86/%E5%B7%A5%E4%BD%9C%E6%B5%81%E7%A8%8B.png)

图：YARN的工作流程图

Yarn的工作流程如下：

1. 用户编写客户端应用程序，向YARN提交应用程序，提交的内容包括ApplicationMaster程序，启动ApplicationMaster的命令、用户程序等。
2. YARN中的ResourceManager负责接收和处理来自客户端的请求，为应用程序分配一个容器，在该容器中启动一个ApplicationMaster。
3. ApplicationMaster被创建后会首先向ResourceManager注册。
4. ApplicationMaster采用轮询的方式向ResourceManager申请资源。
5. ResourceManager以“容器”的形式向提出申请的ApplicationMaster分配资源。
6. 在容器中启动任务（运行环境、脚本）。
7. 各个任务向ApplicationMaster汇报自己的状态和进度。
8. 应用程序运行完成后，ApplicationMaster向ResourceManager的应用程序管理器注销并关闭自己。

### Yarn框架与MapReduce1.0框架的对比分析 <a id="Yarn&#x6846;&#x67B6;&#x4E0E;MapReduce1-0&#x6846;&#x67B6;&#x7684;&#x5BF9;&#x6BD4;&#x5206;&#x6790;"></a>

从MapReduce1.0框架发展到YARN框架，客户端并没有发生变化，其大部分调用API及接口都保持兼容，因此，原来针对Hadoop1.0开发的代码不用做大的改动，就可以直接放到Hadoop2.0平台上运行。

总体而言，YARN相对于MapReduce1.0来说具有以下优势：

* 大大减少了承担中心服务功能的ResourceManager的资源消耗。

  ApplicationMaster来完成需要大量资源消耗的任务调度和监控。

  多个作业对应多个ApplicationMaster，实现了监控分布化。

* MapReduce1.0既是一个计算框架，又是一个资源管理调度框架，但是，只能支持MapReduce编程模型。而YARN则是一个纯粹的资源调度管理框架，在它上面可以运行包括MapReduce在内的不同类型的计算框架，只要编程实现相应的ApplicationMaster。
* YARN中的资源管理比MapReduce1.0更加高效。

  **以容器为单位，而不是以slot为单位**。

### Yarn的发展目标： <a id="Yarn&#x7684;&#x53D1;&#x5C55;&#x76EE;&#x6807;&#xFF1A;"></a>

**YARN的目标就是实现“一个集群多个框架”。**

一个企业当中同时存在各种不同的业务应用场景，需要采用不同的计算框架。

* MapReduce实现离线批处理
* 使用Impala实现实时交互式查询分析
* 使用Storm实现流式数据实时分析
* 使用Spark实现迭代计算

这些产品通常来自不同的开发团队，具有各自的资源调度管理机制。

为了避免不同类型应用之间互相干扰，企业就需要把内部的服务器拆分成多个集群，分别安装运行不同的计算框架，即“一个框架一个集群”。

导致问题

* 集群资源利用率低
* 数据无法共享
* 维护代价高

![&#x53D1;&#x5C55;](https://cshihong.github.io/2018/05/11/Yarn%E6%8A%80%E6%9C%AF%E5%8E%9F%E7%90%86/%E5%8F%91%E5%B1%95.png)

图：在Yarn上部署各种计算框架

* YARN的目标就是实现“一个集群多个框架”，即在一个集群上部署一个统一的资源。调度管理框架YARN，在YARN之上可以部署其他各种计算框架
* 由YARN为这些计算框架提供统一的资源调度管理服务，并且能够根据各种计算框架的负载需求，调整各自占用的资源，实现集群资源共享和资源弹性收缩。
* 可以实现一个集群上的不同应用负载混搭，有效提高了集群的利用率。
* 不同计算框架可以共享底层存储，避免了数据集跨集群移动。

\#\#Yarn HA：

![Yarn HA](https://cshihong.github.io/2018/05/11/Yarn%E6%8A%80%E6%9C%AF%E5%8E%9F%E7%90%86/Yarn%20HA.png)

图：YARN HA方案

Yarn中的ResourceManager负责整个集群的资源管理和任务调度，Yarn高可用性方案通过引入冗余的ResourceManager节点的方式，解决了ResourceManager单点故障问题。

### Yarn APPMaster容错机制： <a id="Yarn-APPMaster&#x5BB9;&#x9519;&#x673A;&#x5236;&#xFF1A;"></a>

![APP&#x5BB9;&#x9519;](https://cshihong.github.io/2018/05/11/Yarn%E6%8A%80%E6%9C%AF%E5%8E%9F%E7%90%86/APP%E5%AE%B9%E9%94%99.png)

图：Yarn APPMaster容错机制

## Yarn的资源管理和任务调度 <a id="Yarn&#x7684;&#x8D44;&#x6E90;&#x7BA1;&#x7406;&#x548C;&#x4EFB;&#x52A1;&#x8C03;&#x5EA6;"></a>

### 资源管理： <a id="&#x8D44;&#x6E90;&#x7BA1;&#x7406;&#xFF1A;"></a>

当前Yarn支持内存和CPU两种资源类型的管理和分配。

每个NodeManager可分配的内存和CPU的数量可以通过配置选项设置（可在Yarn服务配置页面配置）。

* yarn.nodemanager.resource.memory-mb
* yarn.nodemanager.vmem-pmem-ratio
* yarn.nodemanager.resource.cpu-vcore

### 资源分配模型： <a id="&#x8D44;&#x6E90;&#x5206;&#x914D;&#x6A21;&#x578B;&#xFF1A;"></a>

![&#x8D44;&#x6E90;&#x5206;&#x914D;&#x6A21;&#x578B;](https://cshihong.github.io/2018/05/11/Yarn%E6%8A%80%E6%9C%AF%E5%8E%9F%E7%90%86/%E8%B5%84%E6%BA%90%E5%88%86%E9%85%8D%E6%A8%A1%E5%9E%8B.png)

图：资源分配模型

* 调度器维护一群队列的信息、用户可以向一个或多个队列提交应用。
* 每次NM心跳的时候，调度器根据一定的规则选择一个队列，再在队列上选择一个应用，尝试在这个应用上分配资源。
* 调度器会优先匹配本地资源的申请请求，其次是同机架的，最后是任意机器的。

### 容量调度器： <a id="&#x5BB9;&#x91CF;&#x8C03;&#x5EA6;&#x5668;&#xFF1A;"></a>

#### 容量调度器的介绍： <a id="&#x5BB9;&#x91CF;&#x8C03;&#x5EA6;&#x5668;&#x7684;&#x4ECB;&#x7ECD;&#xFF1A;"></a>

容量调度器使得Hadoop应用能够共享的、多用户的、操作简便的运行在集群上，同时最大化集群的吞吐量和利用率。

**容量调度器以队列为单位划分资源**，每个队列都有资源使用的下限和上限。每个用户可以设定资源使用上限。管理员可以约束单个队列、用户或者作业的资源使用、支持作业优先级，但不支持抢占。

#### **容量调度器的特点：** <a id="&#x5BB9;&#x91CF;&#x8C03;&#x5EA6;&#x5668;&#x7684;&#x7279;&#x70B9;&#xFF1A;"></a>

* 容量保证：管理员可为每个队列设置资源最低保证和资源使用上限，所有提交到该队列的应用程序共享这些资源。
* 灵活性：如果一个队列中的资源有剩余，可以暂时共享给那些需要资源的队列，当该队列有新的应用程序提交，则其他队列释放的资源会归还给该队列。
* 支持优先级：队列支持任务优先级调度（默认是FIFO）。
* 多重租赁：支持多用户共享集群和多应用程序同时运行。为防止单个应用程序、用户或者队列独占集群资源，管理员可以为之增加多重约束。
* 动态更新配置参数：管理员可以根据需要动态修改配置参数，以实现在线集群管理。

#### 容量调度器的任务选择： <a id="&#x5BB9;&#x91CF;&#x8C03;&#x5EA6;&#x5668;&#x7684;&#x4EFB;&#x52A1;&#x9009;&#x62E9;&#xFF1A;"></a>

调度时，首先按照以下策略选择一个合适队列：

1. 资源利用最低的队列优先，比如同级的两个队列Q1和Q2，它们的容量均为30，而Q1已经使用10，Q2已使用12，则会优先将资源分配给Q1.
2. 最小队列层级优先。例如：QueueA与QueueB.childQueueB，则QueueA优先。
3. 资源回收请求队列优先。

然后按以下策略选择该任务中一个任务：

* 按照任务优先级和提交时间顺序选择，同时考虑用户资源量限制和内存限制。

### 队列资源限制： <a id="&#x961F;&#x5217;&#x8D44;&#x6E90;&#x9650;&#x5236;&#xFF1A;"></a>

队列的创建是在多租户页面，当创建一个租户关联Yarn服务时，会创建同名的队列。比如先创建QueueA，QueueB两个租户即对应Yarn两个队列。

**队列的资源容量（百分比）**：

例如，有default，QueueA，QueueB三个队列，每个队列都有一个\[队列名\].capacity配置。

* Default队列容量为整个集群资源的20%。
* QueueA队列容量为整个集群资源的10%。
* QueueB队列容量为整个集群资源的10%，后台有个影子队列root-default使队列之和达到100%。

**共享空闲资源：**

* 由于存在资源共享，因此一个队列使用的资源可能超过其容量，而最多使用资源量可通过该参数限制。
* 如果某个队列任务较少，可将剩余资源共享给其他队列。例如QueueA的maximum-capacity配置为100，假设当前只有QueueA在运行任务，理论上QueueA可以占用整个集群100%的资源。

### 用户限制： <a id="&#x7528;&#x6237;&#x9650;&#x5236;&#xFF1A;"></a>

**每个用户最低资源保障（百分比）**

任何时刻，一个队列中每个用户可使用的资源量均由一定的限制，当一个队列中同时运行多个用户的任务时，每个用户的可使用资源量在一个最小值与最大值之间浮动，其中，最大值取决于正在运行的任务数据，而最小值则由minimum-user-limit-percent决定。

例如：设置队列A的这个值为25，即yarn.scheduler.capacity.root.AueueA.minimum-user-limit-percent=25，那么随着任务的用户增加，队列资源的调整如下：

![&#x7528;&#x6237;](https://cshihong.github.io/2018/05/11/Yarn%E6%8A%80%E6%9C%AF%E5%8E%9F%E7%90%86/%E7%94%A8%E6%88%B7.png)

**每个用户最多可使用的资源量（所在队列容量的倍数）**

queue容量的倍数，用来设置一个user可以获取更多的资源。yarn.scheduler.capacity.root.QueueD.user-limit-factor=1.默认值为1，表示一个user获取的资源容量不能超过queue配置的capacity，无论集群有多少空闲字眼，最多不超过maximum-capacity。

### 任务限制： <a id="&#x4EFB;&#x52A1;&#x9650;&#x5236;&#xFF1A;"></a>

* 最大活跃任务数：整个集群中允许的最大活跃任务数，包括运行或挂起状态的所有任务，当提交的任务申请数据达到限制以后，新提交的任务将会被拒绝。默认10000。yarn.scheduler.capacity.maximum-applications=10000.
* 每个队列最大任务数：对于每个队列，可以提交的最大任务数，以QueueA为例，可以在队列配置页面配置，默认是1000，即此队列允许最多1000个活跃任务。
* 每个用户可以提交的最大任务数：这个数值依赖每个队列最大任务数。假设根据上面的结果，QueueA最多可以提交1000个任务，那么对于每个用户而言，可以向QueueA提交的最大任务数为：1000 _yarn.scheduler.capacity.root.QueueA.minimum-user-limit-percent_ yarn.scheduler.capacity.root.QueueA.user-limit-factor.

## Yarn的增强特性 <a id="Yarn&#x7684;&#x589E;&#x5F3A;&#x7279;&#x6027;"></a>

### Yarn动态内存管理： <a id="Yarn&#x52A8;&#x6001;&#x5185;&#x5B58;&#x7BA1;&#x7406;&#xFF1A;"></a>

![&#x52A8;&#x6001;&#x5185;&#x5B58;&#x7BA1;&#x7406;](https://cshihong.github.io/2018/05/11/Yarn%E6%8A%80%E6%9C%AF%E5%8E%9F%E7%90%86/%E5%8A%A8%E6%80%81%E5%86%85%E5%AD%98%E7%AE%A1%E7%90%86.png)

图：Yarn动态内存管理

### Yarn基于标签调度： <a id="Yarn&#x57FA;&#x4E8E;&#x6807;&#x7B7E;&#x8C03;&#x5EA6;&#xFF1A;"></a>

![&#x6807;&#x7B7E;&#x8C03;&#x5EA6;](https://cshihong.github.io/2018/05/11/Yarn%E6%8A%80%E6%9C%AF%E5%8E%9F%E7%90%86/%E6%A0%87%E7%AD%BE%E8%B0%83%E5%BA%A6.png)

图：Yarn基于标签调度

## 常用维护命令 <a id="&#x5E38;&#x7528;&#x7EF4;&#x62A4;&#x547D;&#x4EE4;"></a>

![&#x547D;&#x4EE4;](https://cshihong.github.io/2018/05/11/Yarn%E6%8A%80%E6%9C%AF%E5%8E%9F%E7%90%86/%E5%91%BD%E4%BB%A4.png)

常用性能调优参数：

![1](https://cshihong.github.io/2018/05/11/Yarn%E6%8A%80%E6%9C%AF%E5%8E%9F%E7%90%86/1.png)

![2](https://cshihong.github.io/2018/05/11/Yarn%E6%8A%80%E6%9C%AF%E5%8E%9F%E7%90%86/2.png)

> 以

