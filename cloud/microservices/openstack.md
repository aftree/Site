# OpenStack

## OpenStack概念

目前最流行的开源操作系统内核。

1. 资源抽象

   OpenStack将各类硬件资源，通过虚拟化与软件定义的方式，抽象成资源池。

2. 资源分配与负载调度

   OpenStack根据管理员/用户的需求，将资源池中的资源分配给不同的用户，承载不同的应用。

3. 应用声明周期管理

   OpenStack已经可以提供初步的应用部署/撤除、自动规模调整能力。

4. 系统运维

   OpenStack已经可以提供一定的系统监控能力。

5. 人机交互

   OpenStack提供人机接口，外界可以通过API、命令行或图形界面的方式参与OpenStack进行交互。

### OpenStack不是什么？ <a id="OpenStack&#x4E0D;&#x662F;&#x4EC0;&#x4E48;&#xFF1F;"></a>

**OpenStack不是虚拟化：**

* OpenStack的架构定位与技术范畴
  1. OpenStack只是系统的控制面。
  2. OpenStack不包括系统的数据面组件，如hypervisor、存储和网络设备等。
* 云和虚拟化有着关键的区别

  云计算：IT能力服务化； 按需使用，按量计费； 多租户隔离；

  虚拟化：环境隔离，资源复用； 降低隔离损耗，提升运行效率； 提供高级虚拟化特性。

  **虚拟化是实现云计算的技术支撑手段之一，但并非云计算的核心关注点。**

**OpenStack不是云：**

* OpenStack只是构建云的关键组件。

  内核、骨干、框架、总线。

* 为了构建一个云，还需要许多东西：

  ![&#x4E91;](https://cshihong.github.io/2018/03/08/OpenStack%E7%AE%80%E4%BB%8B/%E4%BA%91.png)

  图：云的架构

### OpenStack的设计与开发： <a id="OpenStack&#x7684;&#x8BBE;&#x8BA1;&#x4E0E;&#x5F00;&#x53D1;&#xFF1A;"></a>

基本设计思想：

1. 开放

   开源,并尽最大可能重用已有的开源项目。

   不要“重复发明轮子”，而要“站在巨人的肩膀上”。

2. 灵活

   不使用任何不可太低的私有/商有组件。

   大量使用插件化方式进行架构设计与实现。

3. 可扩展

   由多个相互独立的项目组成。

   每个项目包含多个独立的组件

   无中心架构。

   无状态架构。

### OpenStack服务简介： <a id="OpenStack&#x670D;&#x52A1;&#x7B80;&#x4ECB;&#xFF1A;"></a>

| 服务 | 项目名称 | 描述 |
| :---: | :---: | :--- |
| Dashboard | Horizon | 提供了一个基于web的自主服务门户，与OpenStack底层服务交互，诸如启动一个实例，分配IP地址以及配置访问控制。 |
| Computer | Nova | 在OpenStack环境中计算实例的生命周期管理。按需响应包括生成、调度、回收虚拟机等操作。 |
| Network | Neutron | 确保为其它OpenStack服务提供网络连接即服务，比如OpenStack计算。为用户提供API定义网络和使用。基于插件的架构其支持众多的网络提供商和技术。 |
| Objecet Storage | Swift | 通过一个 RESTful,基于HTTP的应用程序接口存储和任意检索的非结构化数据对象。它拥有高容错机制，基于数据复制和可扩展架构。它的实现并像是一个文件服务器需要挂载目录。在此种方式下，它写入对象和文件到多个硬盘中，以确保数据是在集群内跨服务器的多份复制。 |
| Block Storage | Cinder | 为运行实例而提供的持久性块存储。它的可插拔驱动架构的功能有助于创建和管理块存储设备。 |
| Identity Service | Keystone | 为其他OpenStack服务提供认证和授权服务，为所有的OpenStack服务提供一个端点目录。 |
| Image Service | Glance | 存储和检索虚拟机磁盘镜像，OpenStack计算会在实例部署时使用此服务。 |
| Telemetry Service | Ceilmeter | 为OpenStack云的计费、基准、扩展性以及统计等目的提供监测和计量。 |
| Orchestration Service | Heat | 既可以使用本地模板格式，亦可使用AWS CloudFormation模板格式，来编排多个综合的云应用，通过OpenStack本地REST API或者是CloudFormation相兼容的队列API。 |

### OpenStack的项目分层： <a id="OpenStack&#x7684;&#x9879;&#x76EE;&#x5206;&#x5C42;&#xFF1A;"></a>

![OpenStack&#x5206;&#x5C42;](https://cshihong.github.io/2018/03/08/OpenStack%E7%AE%80%E4%BB%8B/OpenStack%E5%88%86%E5%B1%82.png)

图：OpenStack的项目分层

OpenStack项目涵盖了IaaS层常用的服务类型、部分系统管理及自动化相关服务，和一些重要的IaaS+服务。

## OpenStack各组件详细介绍 <a id="OpenStack&#x5404;&#x7EC4;&#x4EF6;&#x8BE6;&#x7EC6;&#x4ECB;&#x7ECD;"></a>

### OpenStack新建云主机流程： <a id="OpenStack&#x65B0;&#x5EFA;&#x4E91;&#x4E3B;&#x673A;&#x6D41;&#x7A0B;&#xFF1A;"></a>

![&#x5404;&#x7EC4;&#x4EF6;](https://cshihong.github.io/2018/03/08/OpenStack%E7%AE%80%E4%BB%8B/%E5%90%84%E7%BB%84%E4%BB%B6.png)

图：各组件逻辑关系图

![&#x65B0;&#x5EFA;&#x4E3B;&#x673A;](https://cshihong.github.io/2018/03/08/OpenStack%E7%AE%80%E4%BB%8B/%E6%96%B0%E5%BB%BA%E4%B8%BB%E6%9C%BA.png)

图：OpenStack新建云主机流程图

虚拟机启动过程如下：

1. 界面或命令行通过RESTful API向keystone获取认证信息。
2. keystone通过用户请求认证信息，并生成auth-token返回给对应的认证请求。
3. 界面或命令行通过RESTful API向nova-api发送一个boot instance的请求（携带auth-token）。
4. nova-api接受请求后向keystone发送认证请求，查看token是否为有效用户和token。
5. keystone验证token是否有效，如有效则返回有效的认证和对应的角色（注：有些操作需要有角色权限才能操作）。
6. 通过认证后nova-api和数据库通讯。
7. 初始化新建虚拟机的数据库记录。
8. nova-api通过rpc.call向nova-scheduler请求是否有创建虚拟机的资源\(Host ID\)。
9. nova-scheduler进程侦听消息队列，获取nova-api的请求。
10. nova-scheduler通过查询nova数据库中计算资源的情况，并通过调度算法计算符合虚拟机创建需要的主机。
11. 对于有符合虚拟机创建的主机，nova-scheduler更新数据库中虚拟机对应的物理主机信息。
12. nova-scheduler通过rpc.cast向nova-compute发送对应的创建虚拟机请求的消息。
13. nova-compute会从对应的消息队列中获取创建虚拟机请求的消息。
14. nova-compute通过rpc.call向nova-conductor请求获取虚拟机消息。（Flavor）
15. nova-conductor从消息队队列中拿到nova-compute请求消息。
16. nova-conductor根据消息查询虚拟机对应的信息。
17. nova-conductor从数据库中获得虚拟机对应信息。
18. nova-conductor把虚拟机信息通过消息的方式发送到消息队列中。
19. nova-compute从对应的消息队列中获取虚拟机信息消息。
20. nova-compute通过keystone的RESTfull API拿到认证的token，并通过HTTP请求glance-api获取创建虚拟机所需要镜像。
21. glance-api向keystone认证token是否有效，并返回验证结果。
22. token验证通过，nova-compute获得虚拟机镜像信息\(URL\)。
23. nova-compute通过keystone的RESTfull API拿到认证k的token，并通过HTTP请求neutron-server获取创建虚拟机所需要的网络信息。
24. neutron-server向keystone认证token是否有效，并返回验证结果。
25. token验证通过，nova-compute获得虚拟机网络信息。
26. nova-compute通过keystone的RESTfull API拿到认证的token，并通过HTTP请求cinder-api获取创建虚拟机所需要的持久化存储信息。
27. cinder-api向keystone认证token是否有效，并返回验证结果。
28. token验证通过，nova-compute获得虚拟机持久化存储信息。
29. nova-compute根据instance的信息调用配置的虚拟化驱动来创建虚拟机。

### Keystone: <a id="Keystone"></a>

#### Keystone简介： <a id="Keystone&#x7B80;&#x4ECB;&#xFF1A;"></a>

1. 提供身份验证、服务规则和服务令牌功能。
2. 任何服务之间相互调用，都需要经过Keystone的身份验证。

#### Keystone基本概念介绍： <a id="Keystone&#x57FA;&#x672C;&#x6982;&#x5FF5;&#x4ECB;&#x7ECD;&#xFF1A;"></a>

![&#x57DF;](https://cshihong.github.io/2018/03/08/OpenStack%E7%AE%80%E4%BB%8B/%E5%9F%9F.png)

图：常用术语之间的逻辑关系

1. **User**

   OpenStack最基本的用户。

   User即用户，他们代表可以通过keystone进行访问的人或程序。Users通过认证信息（credentials，如密码、API  
   Keys等）进行验证。

2. **Project（Tenant）**

   指分配给使用者的资源的集合。

   Tenant即租户，它是各个服务中的一些可以访问的资源集合。例如，在Nova中一个tenant可以是一些机器，在Swift和Glance中一个tenant可以是一些镜像存储，在Neutron中一个tenant可以是一些网络资源。Users默认的总是绑定到某些tenant上。

3. **Role**

   Role即角色，Roles代表一组用户可以访问的资源权限，例如Nova中的虚拟机、Glance中的镜像。Users可以被添加到任意一个全局的或租户的角色中。在全局的role中，用户的role权限作用于所有的租户，即可以对所有的租户执行role规定的权限；在租户内的role中，用户仅能在当前租户内执行role规定的权限。

4. **Service**

   Service即服务，如Nova、Glance、Swift。根据前三个概念（User，Tenant和Role）一个服务可以确认当前用户是否具有访问其资源的权限。但是当一个user尝试着访问其租户内的service时，他必须知道这个service是否存在以及如何访问这个service，这里通常使用一些不同的名称表示不同的服务。

5. **Endpoint**

   服务的URL路径，暴露出来的访问点。

   Endpoint，翻译为“端点”，我们可以理解它是一个服务暴露出来的访问点，如果需要访问一个服务，则必须知道他的endpoint。因此，在keystone中包含一个endpoint模板，这个模板提供了所有存在的服务endpoints信息。一个endpoint template包含一个URLs列表，列表中的每个URL都对应一个服务实例的访问地址，并且具有public、private和admin这三种权限。public url可以被全局访问，private url只能被局域网访问，admin url被从常规的访问中分离。

6. **Domain**

   定义管理边界，可以包含多个project,user,role.

7. **Token**

   Token是访问资源的钥匙。它是通过Keystone验证后的返回值，在之后的与其他服务交互中只需要携带Token值即可。每个Token都有一个有效期，Token只在有效期内是有效的。

8. **Policy**

   OpenStack对User的验证除了OpenStack的身份验证以外，还需要鉴别User对某个Service是否有访问权限。Policy机制就是用来控制User对Tenant中资源\(包括Services\)的操作权限。对于Keystone service来说，Policy就是一个JSON文件，默认是`/etc/keystone/policy.json`。通过配置这个文件，Keystone Service实现了对User基于Role的权限管理。

9. **Credentials**

   用于确认用户身份的凭证。

10. **Authentication**：

    确定用户身份的过程。

#### 各种概念之间的关系解释： <a id="&#x5404;&#x79CD;&#x6982;&#x5FF5;&#x4E4B;&#x95F4;&#x7684;&#x5173;&#x7CFB;&#x89E3;&#x91CA;&#xFF1A;"></a>

![key](https://cshihong.github.io/2018/03/08/OpenStack%E7%AE%80%E4%BB%8B/key.png)

图：各种概念之间的关系解释

1. 租户下，管理着一堆用户（人，或程序）。
2. 每个用户都有自己的credentials（凭证）用户名+密码或者用户名+APIkey，或其他凭证。
3. 用户在访问其他资源（计算、存储）之前，需要用自己的credential去请求keystone服务，获得验证信息（主要是Token信息）和服务信息（服务目录和它们的endpoint）。
4. 用户拿着Token信息，就可以去访问特点的资源了。

#### Keystone在OpenStack中的访问流程范例： <a id="Keystone&#x5728;OpenStack&#x4E2D;&#x7684;&#x8BBF;&#x95EE;&#x6D41;&#x7A0B;&#x8303;&#x4F8B;&#xFF1A;"></a>

![&#x6D41;&#x7A0B;](https://cshihong.github.io/2018/03/08/OpenStack%E7%AE%80%E4%BB%8B/%E6%B5%81%E7%A8%8B.png)

图：Keystone在OpenStack中的访问流程范例

### Nova: <a id="Nova"></a>

1. OpenStack云中的计算组织控制器。
2. 管理OpenStack云中示例的生命周期。
3. 管理计算资源资源、网络、认证所需的可扩展平台。

计算管理\(codenamed “Nova”\)基于用户需求为VM提供计算资源管理. 基于Python语言编写。

计算服务：计算节点–运行虚拟机的Hypervisor。

分布式控制器：负责处理器调度策略及API调用等。

#### Nova常用术语： <a id="Nova&#x5E38;&#x7528;&#x672F;&#x8BED;&#xFF1A;"></a>

1. KVM

   内核虚拟化，OpenStack中默认的Hypersvisor。

2. Qemu

   KVM的替补角色，没有KVM执行效率高，不支持虚拟化。

3. Flavor

   新建虚拟机的配置列表，虚拟机模板。

4. 安全组

   用来控制实例访问策略的容器。

5. 安全组规则

   用来控制实例访问的具体策略。

#### Nova中的一些基本概念： <a id="Nova&#x4E2D;&#x7684;&#x4E00;&#x4E9B;&#x57FA;&#x672C;&#x6982;&#x5FF5;&#xFF1A;"></a>

1. Nova-API

   对外统一提供标准化接口，接受和响应最终用户Compute  
   API的请求,同时还实现与Openstack其他各逻辑模块的通讯与服务提供。

2. Nova-Scheduler

   从队列上得到一个虚拟机实例请求并且决定它应该在哪里运行\(使用多种过滤器或算法调度\)。即负责调度将实例分配到具体的计算节点。

3. Queue

   提供了一个守护进程之间传递消息的中央枢纽。消息队列系统作用还可以实现与Openstack其他各逻辑模块之间的通信建立连接枢纽。

4. Nova-Datebase

   存储云基础设施的编译时和运行时的状态,从理论上讲，OpenStack Nova可以支持任何SQL-Alchemy支持的数据库，但是目前被广泛使用的数据库有sqlite3（只适用于测试和开发工作），MySQL和PostgreSQL。

5. Nova-Compute

   主要是一个人工守护进程，它可以通过虚拟机管理程序的API（XenAPIfor XenServer/XCP,libvirtfor KVM or QEMU, VMwareAPIfor VMware等）来创建和终止虚拟机实例。支持多种虚拟化平台。

6. Nova-conductor

   主要负责与Nova数据库进行交互。

7. Nova还提供控制台的服务

   让最终用户通过代理服务器访问他们的虚拟实例的控制台。这涉及到多个守护进程\(nova-console，nova-novncproxy、nova-xvpnvncproxy和nova-consoleauth\)

#### Nova功能特性： <a id="Nova&#x529F;&#x80FD;&#x7279;&#x6027;&#xFF1A;"></a>

1. 实例的生命周期管理。
2. 管理平台的计算资源。
3. 统一风格的RestAPI。
4. 支持透明的hypervisor。
5. 各个模块通过消息队列实现交互。

### Cinder： <a id="Cinder&#xFF1A;"></a>

#### Cinder简介： <a id="Cinder&#x7B80;&#x4ECB;&#xFF1A;"></a>

1. 为虚拟机示例提供volume卷的块存储服务。
2. 一个volume可以同时挂载到多个实例上。
3. 共享的卷同时只能被一个示例进行写操作。

Cinder主要核心是对卷的管理，允许对卷、卷的类型、卷的快照进行处理。它并没有实现对块设备的管理和实际服务，而是为后端不同的存储结构提供了统一的接口，不同的块设备服务厂商在Cinder中实现其驱动支持以与OpenStack进行整合。

* 块存储管理模块\(codenamed “Cinder”\)提供到虚拟机的永久性块存储卷.类似AWS的EBS块存储服务。
* 多个卷可以被挂载到单一虚拟机实例，同时卷可以在虚拟机实例间移动，单个卷在同一时刻只能被挂载到一个虚拟机实例。
* 块存储系统管理块设备到虚拟机的创建，挂载以及卸载。
* 块设备卷完全与OpenstackCompute集成,并支持云用户在Dashboard中管理数据自己的存储。
* 除了支持简单的Linux服务器本地存储之外，还支持众多的存储平台,包括Ceph,NetApp, Nexenta,SolidFire,Zadara。
* 快照管理提供了强大的在块存储上实现数据备份的功能可以用来作为引导卷使用。
* 块存储适合性能敏感性业务场景,例如数据库存储大规模可扩展的文件系统或服务器需要访问到块级裸设备存储。

#### 常用术语： <a id="&#x5E38;&#x7528;&#x672F;&#x8BED;&#xFF1A;"></a>

1. Volume备份：volume卷的备份。
2. Volume快照：卷在某个时间点的状态。
3. Cinder API：为Cinder请求提供的统一风格的Rest API服务。
4. Cinder Schedule：负责为新建卷指定块存储设备。
5. Cinder Volume：负责与存储的块设备交互，实现卷的创建、删除、修改等操作。
6. Cinder Backup：备份服务负责通过驱动和后端的备份设备打交道。

#### Cinder的架构： <a id="Cinder&#x7684;&#x67B6;&#x6784;&#xFF1A;"></a>

![Cinder](https://cshihong.github.io/2018/03/08/OpenStack%E7%AE%80%E4%BB%8B/Cinder.png)

图：Cinder的架构

1. API Service：负责接受和处理 Rest请求，并将请求放入 RabbitMQ对列。
2. Scheduler Service:处理任务队列的任务，并根据预定策略选择合适的 Volume Service 节点来执行任务。目前版本的Cinder仅仅提供了一个 Simple Scheduler, 该调度器选择卷数量最少的一个活跃节点来创建卷。
3. Volume Service: 该服务运行在存储节点上，管理存储空间。每个存储节点都有一个 Volume Service，若干个这样的存储节点联合起来可以构成一个存储资源池。为了支持不同类型和型号的存储，均通过Drivers的形式为Cinder的 Volume Service 提供相应的Cinder-Volume。
4. Volume-Resize：在可用情况下调整卷大小。
5. Volume-Backup-To-Ceph：现在卷可以备份到Ceph集群中。
6. Volume-Migration：现在不同用户间可以透明地转移和交换卷。
7. QoS：增加限速相关的元信息供Nova和其 Hypervisor使用。
8. More-Drivers：更多的存储厂商加入和完善了自己的Cinder驱动。

Cinder目前支持的存储类型：

1. 本地存储：LVM,Sheepdog
2. 网络存储：NFS,RBD\(Ceph\)
3. HP:3PAR \(iSCSI/FC\),LeftHand \(iSCSI\)
4. IBM: Storwize family/SVC \(iSCSI/FC\)，XIV\(iSCSI\)， GPFS，zVM
5. Netapp: NetApp\(iSCSI/NFS\)
6. EMC: VMAX/VNX \(iSCSI\)，Isilon\(iSCSI\)
7. Solidfire:Solidfire cluster\(iSCSI\)

### Swift： <a id="Swift&#xFF1A;"></a>

#### Swift简介： <a id="Swift&#x7B80;&#x4ECB;&#xFF1A;"></a>

1. **高可用分布式式对象存储。**
2. **为nova组件提供虚拟化的镜像存储。**
3. **使用与互联网应用场景下非结构化的数据存储。**

* 分布式对象存储系统，类似于AWS的S3。
* 通过采用基于标准化服务器的集群架构提供冗余,可扩展的对象存储。
* 具有良好的扩展性,可以实现PB级别数据的存储。
* 支持存储对象写入的多份拷贝,并且支持当拷贝丢失后的自我修复功能.确保数据的一致性。
* 提供每GB高性价比的极佳的可用性和数据耐久性。
* 支持原生的 OpenStack™ API 以及S3 compatible API。

#### Swift常用术语： <a id="Swift&#x5E38;&#x7528;&#x672F;&#x8BED;&#xFF1A;"></a>

1. Account：

   用户定义的管理存储区域。

2. Container：

   存储间隔，类似于文件夹或目录。

3. Object：

   包含了基本的存储实体和它自身的元数据。

4. Ring：

   记录了磁盘存储的实体名称和物理位置的映射关系。

Account包含Container，Container包含Object。

1. Region：

   地域，从地理位置上划分的一个概念。

2. Zone：

   可用区，按照独立的供网、供电基础设置划分。

3. Node：

   节点，存储服务器。

4. Disk：

   磁盘，物理服务器上的存储设备。

5. Cluster：

   群集，为冗余考虑的部署架构。

Cluster包含Region，Region包含Zone；Zone包含Node。

#### Swift功能： <a id="Swift&#x529F;&#x80FD;&#xFF1A;"></a>

Swift在物理结构上往往会存储对象的多个副本，通常按照物理位置的特点，将对象拷贝到不同的物理位置上，来保证数据的可靠性。

#### Swift数据模型： <a id="Swift&#x6570;&#x636E;&#x6A21;&#x578B;&#xFF1A;"></a>

Swift采用层次数据模型，共设三层逻辑结构：Account/Container/Object（即账户/容器/对象\)，每层节点数均没有限制，可以任意扩展。数据存储基于NWR理论。

1. Account对应租户,用于隔离。
2. Container对应某个租户数据的存储区域。
3. Object对应存储区域中具体的block。

#### Swift系统架构： <a id="Swift&#x7CFB;&#x7EDF;&#x67B6;&#x6784;&#xFF1A;"></a>

* Swift 采用完全对称、面向资源的分布式系统架构设计。
* 所有组件都可扩展，避免因单点失效而扩散并影响整个系统运转。
* 通信方式采用非阻塞式I/O 模式，提高了系统吞吐和响应能力。

![swift](https://cshihong.github.io/2018/03/08/OpenStack%E7%AE%80%E4%BB%8B/swift.png)

![Swift&#x67B6;&#x6784;](https://cshihong.github.io/2018/03/08/OpenStack%E7%AE%80%E4%BB%8B/Swift%E6%9E%B6%E6%9E%84.png)

图：Swift系统架构

#### Swift系统组件： <a id="Swift&#x7CFB;&#x7EDF;&#x7EC4;&#x4EF6;&#xFF1A;"></a>

1. 代理服务（Proxy Server）：对外提供对象服务API，会根据环的信息来查找服务地址并转发用户请求至相应的账户、容器或者对象服务；由于采用无状态的REST请求协议，可以进行横向扩展来均衡负载。
2. 认证服务（Authentication Server）：验证访问用户的身份信息，并获得一个对象访问令牌（Token），在一定的时间内会一直有效；验证访问令牌的有效性并缓存下来直至过期时间。
3. 缓存服务（Cache Server）：缓存的内容包括对象服务令牌，账户和容器的存在信息，但不会缓存对象本身的数据；缓存服务可采用Memcached集群，Swift会使用一致性散列算法来分配缓存地址。
4. 账户服务（Account Server）：提供账户元数据和统计信息，并维护所含容器列表的服务，每个账户的信息被存储在一个SQLite数据库中。
5. 容器服务（Container Server）：提供容器元数据和统计信息，并维护所含对象列表的服务，每个容器的信息也存储在一个SQLite数据库中。
6. 对象服务（Object Server）：提供对象元数据和内容服务，每个对象的内容会以文件的形式存储在文件系统中，元数据会作为文件属性来存储，建议采用支持扩展属性的XFS文件系统。
7. 复制服务（Replicator）：会检测本地分区副本和远程副本是否一致，具体是通过对比散列文件和高级水印来完成，发现不一致时会采用推式（Push）更新远程副本，例如对象复制服务会使用远程文件拷贝工具rsync来同步；另外一个任务是确保被标记删除的对象从文件系统中移除。
8. 更新服务（Updater）：当对象由于高负载的原因而无法立即更新时，任务将会被序列化到在本地文件系统中进行排队，以便服务恢复后进行异步更新；例如成功创建对象后容器服务器没有及时更新对象列表，这个时候容器的更新操作就会进入排队中，更新服务会在系统恢复正常后扫描队列并进行相应的更新处理。
9. 审计服务（Auditor）：检查对象，容器和账户的完整性，如果发现比特级的错误，文件将被隔离，并复制其他的副本以覆盖本地损坏的副本；其他类型的错误会被记录到日志中。
10. 账户清理服务（Account Reaper）：移除被标记为删除的账户，删除其所包含的所有容器和对象。

#### 对Swift的补充-SINA的经验： <a id="&#x5BF9;Swift&#x7684;&#x8865;&#x5145;-SINA&#x7684;&#x7ECF;&#x9A8C;&#xFF1A;"></a>

主要组件

* Proxy Server

  ProxyServer是提供SwiftAPI的服务器进程，负责Swift其余组件间的相互通信。对于每个客户端的请求，它将在Ring中查询Account、Container或Object的位置，并且相应地转发请求。Proxy提供了Rest-fullAPI，并且符合标准的HTTP协议规范，这使得开发者可以快捷构建定制的Client与Swift交互。

* Storage Server

  StorageServer提供了磁盘设备上的存储服务。在Swift中有三类存储服务器：Account、Container和Object。其中Container服务器负责处理Object的列表，Container服务器并不知道对象存放位置，只知道指定Container里存的哪些Object。这些Object信息以sqlite数据库文件的形式存储。Container服务器也做一些跟踪统计，例如Object的总数、Container的使用情况。

* Consistency Servers

  在磁盘上存储数据并向外提供Rest-fulAPI并不是难以解决的问题，最主要的问题在于故障处理。Swift的Consistency Servers的目的是查找并解决由数据损坏和硬件故障引起的错误。主要存在三个Server：Auditor、Updater和Replicator。 Auditor运行在每个Swift服务器的后台持续地扫描磁盘来检测对象、Container和账号的完整性。如果发现数据损坏，Auditor就会将该文件移动到隔离区域，然后由Replicator负责用一个完好的拷贝来替代该数据。图2给出了隔离对象的处理流图。在系统高负荷或者发生故障的情况下，Container或账号中的数据不会被立即更新。如果更新失败，该次更新在本地文件系统上会被加入队列，然后Updaters会继续处理这些失败了的更新工作，其中由AccountUpdater和ContainerUpdater分别负责Account和Object列表的更新。Replicator的功能是处理数据的存放位置是否正确并且保持数据的合理拷贝数，它的设计目的是Swift服务器在面临如网络中断或者驱动器故障等临时性故障情况时可以保持系统的一致性。

* Ring

  Ring是Swift最重要的组件，用于记录存储对象与物理位置间的映射关系。在涉及查询Account、Container、Object信息时，就需要查询集群的Ring信息。 Ring使用Zone、Device、Partition和Replica来维护这些映射信息。Ring中每个Partition在集群中都（默认）有3个Replica。每个Partition的位置由Ring来维护，并存储在映射中。Ring文件在系统初始化时创建，之后每次增减存储节点时，需要重新平衡一下Ring文件中的项目，以保证增减节点时，系统因此而发生迁移的文件数量最少。

* Replica

  如果集群中的数据在本地节点上只有一份，一旦发生故障就可能会造成数据的永久性丢失。因此，需要有冗余的副本来保证数据安全。Swift中引入了Replica的概念，其默认值为3，理论依据主要来源于NWR策略（也叫Quorum协议）。 NWR是一种在分布式存储系统中用于控制一致性级别的策略。在Amazon的Dynamo云存储系统中，使用了NWR来控制一致性。其中，N代表同一份数据的Replica的份数，W是更新一个数据对象时需要确保成功更新的份数；R代表读取一个数据需要读取的Replica的份数。公式W+R&gt;N，保证某个数据不被两个不同的事务同时读和写；公式W&gt;N/2保证两个事务不能并发写某一个数据。在分布式系统中，数据的单点是不允许存在的。即线上正常存在的Replica数量为1的情况是非常危险的，因为一旦这个Replica再次出错，就可能发生数据的永久性错误。假如我们把N设置成为2，那么只要有一个存储节点发生损坏，就会有单点的存在，所以N必须大于2。N越高，系统的维护成本和整体成本就越高。工业界通常把N设置为3。例如，对于MySQL主从结构，其NWR数值分别是N=2, W = 1, R = 1，没有满足NWR策略。而Swift的N=3,W=2, R=2，完全符合NWR策略，因此Swift系统是可靠的，没有单点故

* Zone

  如果所有的Node都在一个机架或一个机房中，那么一旦发生断电、网络故障等，都将造成用户无法访问。因此需要一种机制对机器的物理位置进行隔离，以满足分区容忍性（CAP理论中的P）。因此，Ring中引入了Zone的概念，把集群的Node分配到每个Zone中。其中同一个Partition的Replica不能同时放在同一个Node上或同一个Zone内。注意，Zone的大小可以根据业务需求和硬件条件自定义，可以是一块磁盘、一台存储服务器，也可以是一个机架甚至一个IDC。

* Weight

  Ring引入Weight的目的是解决未来添加存储能力更大的Node时，分配到更多的Partition。例如，2TB容量的Node的Partition数为1TB的两倍，那么就可以设置2TB的Weight为200，而1TB的为100。

### Glance： <a id="Glance&#xFF1A;"></a>

#### Glance简介： <a id="Glance&#x7B80;&#x4ECB;&#xFF1A;"></a>

1. 为Nova提供镜像服务。
2. 通常不负责镜像的本地存储。
3. 实现对镜像的管理。

Glance是OpenStack镜像服务,用来注册、登陆和检索虚拟机镜像。Glance服务提供了一个REST API，使你能够查询虚拟机镜像元数据和检索的实际镜像。通过镜像服务提供的虚拟机镜像可以存储在不同的位置，从简单的文件系统对象存储到类似OpeenStack对象存储系统。

#### 镜像服务组件: <a id="&#x955C;&#x50CF;&#x670D;&#x52A1;&#x7EC4;&#x4EF6;"></a>

1. Glance-API ：

   负责提供镜像服务的rest api。

   接收最终用户或Noav对镜像的请求,检索和存储镜像的相关API调用。

2. Glance-registry： 存储,处理和检索有关镜像的元数据,元数据大小、类型等等。
3. Database ：存储镜像元数据,可以支持多种数据库,现在使用比较广泛的是mysql和sqlite.

#### Glance与其他模块的关系: <a id="Glance&#x4E0E;&#x5176;&#x4ED6;&#x6A21;&#x5757;&#x7684;&#x5173;&#x7CFB;"></a>

![Glace](https://cshihong.github.io/2018/03/08/OpenStack%E7%AE%80%E4%BB%8B/Glace.png)

图： Glance与其他模块的关系

#### Glance支持的镜像格式： <a id="Glance&#x652F;&#x6301;&#x7684;&#x955C;&#x50CF;&#x683C;&#x5F0F;&#xFF1A;"></a>

1. raw –非结构化的镜像格式
2. vhd– 一种通用的虚拟机磁盘格式，可用于Vmware、Xen、Microsoft Virtual PC/VirtualServer/Hyper-V、VirtualBox等
3. vmdk– Vmware的虚拟机磁盘格式，同样也支持多种Hypervisor
4. vdi– VirtualBox、QEMU等支持的虚拟机磁盘格式
5. qcow2 –一种支持QEMU并且可以动态扩展的磁盘格式
6. aki– Amazon Kernel 镜像
7. ari– Amazon Ramdisk 镜像
8. ami– Amazon 虚拟机镜像

### Neutron: <a id="Neutron"></a>

1. **提供网络服务的核心组件。**
2. **基于软件定义网络的的思想。**

* 网络服务\(codenamed “Quantum/Neutron”\)提供在被管理设备之间的网络连接服务.
* 允许用户自己创建自己的网络并attach端口使用.
* 通过开发的Plugins支持SDN和OpenFlow
* 用户自定义子网地址,私有网络/公有网络以及FloatingIP分配规则
* 基于插件的模型。

#### 常用术语： <a id="&#x5E38;&#x7528;&#x672F;&#x8BED;&#xFF1A;-1"></a>

1. Bridge-int

   实现内部网络功能的网桥。

2. Br-ex：

   跟外部网络通信的网桥。

3. Neutron-server：

   提供API接口。

4. Neutron-L2-agent：

   实现二层网络通信的代理。

5. Neutron-DHCP-agent：

   为子网自动分发IP地址。

6. Neutron-L3-agent：

   租户网络和floating IP间地址的转换。

7. Neutron-metadate-agent：

   响应Nova的metadate请求。

8. LBaas agent：

   为多台实例和Open vswitch agent提供负载均衡服务。

#### 服务组件： <a id="&#x670D;&#x52A1;&#x7EC4;&#x4EF6;&#xFF1A;"></a>

1. Neutron API

   l提供Openstack其他服务或管理员及用户访问的接口.

2. 抽相层

   网络资源抽象化,包括网络，子网，端口及路由。将底层各种各样的网络资源按照openstack定义的网络描述规则化。

3. Plug-in

   通过Plug-in方式可以整合更多的高级的功能以及与底层网络厂商的设备实现更好的集成。

#### 网络Agent： <a id="&#x7F51;&#x7EDC;Agent&#xFF1A;"></a>

* Plug-in agent:本地虚拟交换配置。
* Dhcp agent:为tenant网络提供DHCP服务。
* L3agent:提供l3/NAT转发功能，为tenant网络中的vm提供外网访问。
* Meteringagent:提供l3流量监控和计量。

#### 网络描述： <a id="&#x7F51;&#x7EDC;&#x63CF;&#x8FF0;&#xFF1A;"></a>

* 管理网络：用于OpenStack各组件之间的内部通信。
* 数据网络：用于云部署中虚拟数据之间的通信。
* 外部网络：公共网络，外部或internet可以访问的网络。
* API网络：暴露所有的OpenStackAPIs,包括OpenStack网络 API给租户

#### Neutron服务网络管理的三种模式，两种IP： <a id="Neutron&#x670D;&#x52A1;&#x7F51;&#x7EDC;&#x7BA1;&#x7406;&#x7684;&#x4E09;&#x79CD;&#x6A21;&#x5F0F;&#xFF0C;&#x4E24;&#x79CD;IP&#xFF1A;"></a>

**两种IP：**

1. 固定IP\(Fixed-IP\)：分配给虚拟机实例使用
2. 浮动IP\(FloatingIP\)：分配给虚拟机实例的外网地址,通过NAT方式实现。

**三种模式：**

1. Flat模式

   Flat 模式是最简单的一种联网模式。每个实例接收一个来自池的固定IP。所有实例均默认附加到相同的桥  
   \(br100\)。桥必须进行手动配置。联网配置在实例引导前插入到实例中。在这种模式中，没有浮动IP特性。

2. Flat DHCP模式

   用于测试环境。

   这种模式下与Flat模式不同的地方在于有一个DHCP进程，每一个运行nova-network进程的节点（网络控制节点/nove-network主机）就是一个单独的网络。Nova会在 nova-network主机建立网桥（默认名称br100，配置项 flat\_network\_bridge=br100），并给该网桥指定该网络的网关IP，同时 Nova在网桥处起一个DHCP进程，最后，会建立iptables规则（SNAT/DNAT）使虚拟机能够与外界通信，同时与一个metadata服务器通信以取得cloud内的信息。

   计算节点负责创建对应节点的网桥，此时的计算节点网卡可以不需要IP地址，因为网桥把虚拟机与nove-network主机连接在一个逻辑网络内。虚拟机启动时会发送dhcpdiscover以获取 IP地址。虚拟机通往外界的数据都要通过nova-network主机，DHCP在网桥处监听，分配fixed\_range指定的 IP段。

   **这种部署方式的缺点—-单节点故障、无二层隔离（即所有的虚拟机都在一个广播域）。**

3. VLAN模式

   用于生产环境。

   VLAN模式的目的是为每个项目提供受保护的网段，具有以下特点：

   1. NAT实现public ip。
   2. 除了public NAT外没有其它途径进入每个lan。
   3. 受限的流出网络，project-admin可以控制。
   4. 受限的项目之间的访问，同样project-admin控制。
   5. 所以实例和api的连接通过vpn。

### Ceilometer： <a id="Ceilometer&#xFF1A;"></a>

1. OpenStack中的数据监控器。
2. 为流量计费提供数据支持。

#### 核心概念： <a id="&#x6838;&#x5FC3;&#x6982;&#x5FF5;&#xFF1A;"></a>

1. Ceilometer-agent-computer：

   收集计算节点上的信息的代理。

2. Ceilometer-agent-central：

   运行在控制节点上，轮询服务的非持续化数据。

3. Ceilometer-collector：

   运行在控制节点上，监听Message Bus，将收到的消息写入到数据库中。

4. Storage：

   数据存储，支持MySQL等，用于存储收到的样本数据。

5. API server:

   运行在控制节点上，提供对数据节点的访问。

6. Message Bus：

   计量数据的消息总线，收集数据提供给collector。

### Heat： <a id="Heat&#xFF1A;"></a>

1. OpenStack的核心项目之一。
2. 提供基于模板的编排服务。

#### 常用术语： <a id="&#x5E38;&#x7528;&#x672F;&#x8BED;&#xFF1A;-2"></a>

1. Stack：指的是Heat要用到的所有设施和资源的集合。
2. Heat template：是以 .yaml结尾的文件，用于创建Stack。
3. heat-api：提供rest-api服务，将api请求发送给heat engine去执行。
4. haet-api-cfn：支持亚马逊格式访问的Rest-api。
5. Heat-engine：Heat的核心模块，接收请求API请求在OpenStack中创建资源。
6. Heat-cfntools、Heat-init：在镜像中安装完成虚拟化实例操作任务的工具。
7. Heat-api-cloudwatch：监控编排服务。
8. Resource：底层各种服务抽象的集合。
9. Heat-Client：调用访问其他各个组件的Client工具。

