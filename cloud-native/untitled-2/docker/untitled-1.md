# Docker



## Docker发展历程 <a id="Docker&#x53D1;&#x5C55;&#x5386;&#x7A0B;"></a>

### 容器技术的演进： <a id="&#x5BB9;&#x5668;&#x6280;&#x672F;&#x7684;&#x6F14;&#x8FDB;&#xFF1A;"></a>

1. 1979年：Unix V7 chroot技术的引进开启了进程隔离的大门。
2. 2000年：FreeBSD Jails将计算机分为多个独立的小型计算机系统。
3. 2006年：Process Containers 进程的资源使用限制。
4. 2008年：LXC 第一个完整的容器管理工具。
5. 2013年：LMCTFY Libcontainer的重要组成。
6. 2013年：Docker。

### 容器迅猛发展的背景： <a id="&#x5BB9;&#x5668;&#x8FC5;&#x731B;&#x53D1;&#x5C55;&#x7684;&#x80CC;&#x666F;&#xFF1A;"></a>

1. 应用架构正在发生变革—–微服务化。
2. 基础架构系统也在发生变革—虚拟化、混合云。

### 容器技术： <a id="&#x5BB9;&#x5668;&#x6280;&#x672F;&#xFF1A;"></a>

面临越多的应用数据和种类，各种底层环境， 容器技术可以完美的将各种组件封装起来，应用开发完成以后，一次开发，多次部署，随时迁移。完全不需要关注底层结构，从而更好的支持微服务架构的实现。

### 定义标准+服务应用： <a id="&#x5B9A;&#x4E49;&#x6807;&#x51C6;-&#x670D;&#x52A1;&#x5E94;&#x7528;&#xFF1A;"></a>

* 基础设施标准化—Docker Engine。

  当前所有的Linux系统均已支持Docker Engine，有了Docker Engine就可以使Docker容器运行起来。

* 应用交付标准化—Docker Image。

  提供了一套可以快速打包为轻量级Docker Image的方法。开发人员在代码完成以后，竟可以将其打包为镜像。运维人员也不在需要准备应用、准备系统、运行环境、组件和基础软件包。

* 运维管理标准化—Docker Container。

  运维将关注在这些标准的容器中，而不再是关注底层的复杂系统环境。

* 分发部署标准化—Docker Registry。

  指的是容器化以后，不同版本的应用镜像都存储在镜像仓库中，运维人员可以将镜像仓库中特定版本的镜像一键部署到开发环境，测试环境或者是生产环境中。也可以实现快速的版本升级，或者回退。

### 史无前例的发展速度： <a id="&#x53F2;&#x65E0;&#x524D;&#x4F8B;&#x7684;&#x53D1;&#x5C55;&#x901F;&#x5EA6;&#xFF1A;"></a>

1. 2016年6月，已经有46万的应用部署在容器中。容器化应用在两年内增长了3000%。
2. 截止2016年1月为止，容器镜像下载数次达16亿次。2016年6月，次数飙升到41亿次。

## 容器技术精髓剖析 <a id="&#x5BB9;&#x5668;&#x6280;&#x672F;&#x7CBE;&#x9AD3;&#x5256;&#x6790;"></a>

### namespace： <a id="namespace&#xFF1A;"></a>

Namespace的6项隔离：

| namespace | 隔离的内容 |
| :--- | :--- |
| UTS | 主机名与域名 |
| IPC | 信号量、消息队列和共享内容 |
| PID | 进程编号 |
| Network | 网络设备、网络栈、端口 |
| Mount | 文件系统 |
| User | 用户和用户组 |

利用Namespace可以构建一个相对隔离的容器。

### cgroups： <a id="cgroups&#xFF1A;"></a>

通过Cgroups可以实现为容器设置系统资源的限额。

包括CPU，内存，I/O等等。

### 其他相关的Linux Kernel技术： <a id="&#x5176;&#x4ED6;&#x76F8;&#x5173;&#x7684;Linux-Kernel&#x6280;&#x672F;&#xFF1A;"></a>

![Linux Kernel&#x6280;&#x672F;](https://cshihong.github.io/2018/04/02/Docker%E5%9F%BA%E7%A1%80%E5%8E%9F%E7%90%86/Linux%20Kernel%E6%8A%80%E6%9C%AF.png)

图：Linux Kernel

* selinux和apparmor可以增强对容器的访问控制。
* capabilitise的主要实现在于将超级用户root的主要权限分割成各种不同的capabilities。从而更严格的控制容器的权限。
* netlink技术可以完成Docker容器的网络环境配置和创建。

这些Linux Kernel技术从安全、隔离、防火墙、访问等方面为容器的成熟和落地打下了坚实的基础。

### 容器管理： <a id="&#x5BB9;&#x5668;&#x7BA1;&#x7406;&#xFF1A;"></a>

![Linux](https://cshihong.github.io/2018/04/02/Docker%E5%9F%BA%E7%A1%80%E5%8E%9F%E7%90%86/Linux.png)

图：Lxc和Libcontainer

* Lxc是第一个完整意义上的容器管理技术，通过Lxc可以方便的创建、启动和停止一个容器。还可以通过它来操作容器中的应用，也可以查看容器的运行状态。
* Libcontainer用于容器管理的包，管理`namespaces`、`cgroups`、`capabilities`以及文件系统来对容器控制。可用Libcontainer创建容器，并对容器进行管理。`pivot_root` 用于改变进程的根目录，可以将进程控制在`rootfs`中。如果`rootfs`是基于`ramfs`的（不支持`pivot_root`），那会在`mount`时使用`MS_MOVE`标志位加上`chroot`来顶替。

  `Libcontainer`通过接口的方式定义了一系列容器管理的操作，包括处理容器的创建（Factory）、容器生命周期管理（Container）、进程生命周期管理（Process）等一系列接口。

### Docker： <a id="Docker&#xFF1A;"></a>

![docker](https://cshihong.github.io/2018/04/02/Docker%E5%9F%BA%E7%A1%80%E5%8E%9F%E7%90%86/docker.png)

图：Docker构造架构

Docker是一个传统的Client-Server架构，装好了Docker工具后，同时也就装好了Docker Cilent端和Server端。Client是Docker命令行工具，也可以是GitHub上开源的图形化工具。通过Client工具可以发起创建、管理容器的指令至Server端进行处理。

### Docker技术原理： <a id="Docker&#x6280;&#x672F;&#x539F;&#x7406;&#xFF1A;"></a>

![docker&#x6280;&#x672F;&#x539F;&#x7406;](https://cshihong.github.io/2018/04/02/Docker%E5%9F%BA%E7%A1%80%E5%8E%9F%E7%90%86/docker%E6%8A%80%E6%9C%AF%E5%8E%9F%E7%90%86.png)

图：Docker技术原理

Docker是依赖Linux中一些核心技术来实现的。

Docker Daemon\(守护进程\)通过libcontainer、lxc等容器管理工具来接收Client端的指令完成容器管理操作。

Docker三个重要的组件：

1. execdriver：

   存储了容器定义的配置信息了，libcontainer拿到这些配置信息后，将会调用底层的namespace和cgroups等技术来完成容器的创建和管理。

2. networkdirver：

   主要作用是完成Docker容器的网络环境配置，包括容器的IP地址，端口，防火墙策略，以及与主机的端口映射等。

3. graphdriver：

   主要负责对容器镜像的管理。

## Docker核心技术 <a id="Docker&#x6838;&#x5FC3;&#x6280;&#x672F;"></a>

### VM vs Docker： <a id="VM-vs-Docker&#xFF1A;"></a>

![vm](https://cshihong.github.io/2018/04/02/Docker%E5%9F%BA%E7%A1%80%E5%8E%9F%E7%90%86/vm.png)

图：VM 和 容器的对比

通过对比，容器的运行时不需要安装虚拟机的操作系统，是比虚拟机更加轻量型的虚拟化技术。

### Docker的优势： <a id="Docker&#x7684;&#x4F18;&#x52BF;&#xFF1A;"></a>

| 对比项 | VM | Docker |
| :--- | :--- | :--- |
| 隔离性 | 强 | 较弱 |
| 计算资源开销 | 大 | 小 |
| 镜像大小 | 几百MB至几GB | 可小至几MB |
| 启动速度 | 数秒至数分钟 | 毫秒级 |
| 快速扩展能力 | 一般 | 强 |
| 跨平台迁移能力 | 一般 | 强 |
| 对微服务架构的支持 | 一般 | 强 |
| 对Devops的支持 | 一般 | 强 |

### 容器、镜像、仓库： <a id="&#x5BB9;&#x5668;&#x3001;&#x955C;&#x50CF;&#x3001;&#x4ED3;&#x5E93;&#xFF1A;"></a>

1. Docker Container，容器与虚拟机一样，都是承载相关应用的载体。
2. Docker Image，镜像可以与虚拟机模板进行类比，将安装好的应用打包成一个镜像，需要的时候可以直接快速安装。
3. Docker Registry，仓库是存储镜像的地方。

### Docker容器： <a id="Docker&#x5BB9;&#x5668;&#xFF1A;"></a>

#### Docker容器的三种状态： <a id="Docker&#x5BB9;&#x5668;&#x7684;&#x4E09;&#x79CD;&#x72B6;&#x6001;&#xFF1A;"></a>

![Docker&#x5BB9;&#x5668;&#x72B6;&#x6001;](https://cshihong.github.io/2018/04/02/Docker%E5%9F%BA%E7%A1%80%E5%8E%9F%E7%90%86/Docker%E5%AE%B9%E5%99%A8%E7%8A%B6%E6%80%81.png)

图：Docker容器的状态

通过`docker` 命令可以将容器重启、停止，挂起或恢复启动。

### Docker镜像： <a id="Docker&#x955C;&#x50CF;&#xFF1A;"></a>

![docker&#x955C;&#x50CF;](https://cshihong.github.io/2018/04/02/Docker%E5%9F%BA%E7%A1%80%E5%8E%9F%E7%90%86/docker%E9%95%9C%E5%83%8F.png)

图：Docker 镜像构建

将镜像下载到本地可通过`docker run` 命令就可以启动基于镜像的容器。当对镜像做一些修改以后，可以将容器`commit` 回去，这时会生成一个新版本的镜像。

镜像的生成除了使用commit置位，还有一种更为标准，更常用的方式。使用`dockerfile` 来build镜像。这种方式build出来的镜像，更新干净，透明。

![&#x955C;&#x50CF;](https://cshihong.github.io/2018/04/02/Docker%E5%9F%BA%E7%A1%80%E5%8E%9F%E7%90%86/%E9%95%9C%E5%83%8F.png)

图：Docker层级文件系统

Docker镜像是一种层级结构的文件系统，镜像的最上层往往是可写的，存储着已经运行容器的修改信息，当我们对容器进行`kill` 时，这些信息就会被删掉。当将容器`commit` 称为新的镜像后，这些修改信息也会保存成新的层级。

### Docker仓库： <a id="Docker&#x4ED3;&#x5E93;&#xFF1A;"></a>

![docker&#x4ED3;&#x5E93;](https://cshihong.github.io/2018/04/02/Docker%E5%9F%BA%E7%A1%80%E5%8E%9F%E7%90%86/docker%E4%BB%93%E5%BA%93.png)

图：Docker仓库结构

Dockerhub是官方的镜像仓库，仓库中存放了各种标准化的镜像。可以使用`pull` 命令直接从Docker仓库中下载镜像到本地进行使用。

还可以构建企业自己的私有仓库，用于存放常用的镜像，以及企业自定义的镜像。可以通过`pull` 下载镜像，也可以通过`push` 上传镜像到私有仓库中。

### Docker的核心技术Build，Ship，Run： <a id="Docker&#x7684;&#x6838;&#x5FC3;&#x6280;&#x672F;Build&#xFF0C;Ship&#xFF0C;Run&#xFF1A;"></a>

![docker&#x6838;&#x5FC3;&#x6280;&#x672F;](https://cshihong.github.io/2018/04/02/Docker%E5%9F%BA%E7%A1%80%E5%8E%9F%E7%90%86/docker%E6%A0%B8%E5%BF%83%E6%8A%80%E6%9C%AF.png)

图；Docker的Build，ship，run流程

实现Docker一次构建，在任何地方能够运行的构想。

### Docker数据卷： <a id="Docker&#x6570;&#x636E;&#x5377;&#xFF1A;"></a>

数据卷存在的意义：

1. 容器运行时可使用数据卷中的文件。
2. 数据卷可在多个容器间共享。
3. 存储容器运行过程中产生的数据。
4. 方便主机对容器数据的访问。

### Docker网络： <a id="Docker&#x7F51;&#x7EDC;&#xFF1A;"></a>

![&#x7F51;&#x7EDC;](https://cshihong.github.io/2018/04/02/Docker%E5%9F%BA%E7%A1%80%E5%8E%9F%E7%90%86/%E7%BD%91%E7%BB%9C.png)

图：Docker网络架构

Docker网络的四种网络模式：

1. Bridged:表示容器可以与主机上的容器，主机，外部进行通信。
2. Host：表示容器只能与主机镜像通信。
3. Container：表示容器只能与容器之间进行通信，而无法与主机进行通信。
4. None：表示没有网络连接。

比较常用的是Bridged模式，安装完Docker之后，主机会生成一个Docker网桥，每个容器拥有自己的虚拟网卡，容器网卡通过网桥连接到主机的物理网卡与外部进行通信。

## Docker平台架构 <a id="Docker&#x5E73;&#x53F0;&#x67B6;&#x6784;"></a>

### Orchestration： <a id="Orchestration&#xFF1A;"></a>

![&#x7F16;&#x6392;](https://cshihong.github.io/2018/04/02/Docker%E5%9F%BA%E7%A1%80%E5%8E%9F%E7%90%86/%E7%BC%96%E6%8E%92.png)

图：容器的广义编排

* 集群管理：容器平台是由一系列的主机来提供计算资源，通过集群，管理技术，将这些主机管理起来，获取这些主机的配置信息。实时的资源使用情况，并可以快速增加主机节点，并且需要保证整个集群的高可用性。
* 容器调度：集群管理能够让我们知道每一个主机的资源使用情况，我们可以将容器平均的部署到每个主机上，并且可以在某个主机资源紧张的情况下，可以让容器在其他主机上跑起来。还可以设定调度策略，实现将某几个通信紧密的容器在同样一个主机上运行，或者某些资源消耗大的容器，总是运行在不同的主机上。这就是所谓的互斥。
* 故障恢复：容器平台需要具备对主机和容器的健康性检查。当主机出现问题的时候，需要在其他主机上将入容器启动起来，并做好相关的服务注册。当容器出现问题是，需要将容器进行重启，或者重新部署来保证应用的健康运行。
* 应用编排：容器时代，应用是由一个个细微的容器来组成。每一个容器之间，具有一定的逻辑关系，我们需要使用简单明了的编排语句将这些容器管理起来。比如，容器之间的端口访问，容器的启动顺序等等，从而实现整个应用的逻辑架构。

### 三大Orchestration工具： <a id="&#x4E09;&#x5927;Orchestration&#x5DE5;&#x5177;&#xFF1A;"></a>

| 编排工具 | 厂商 | 发布时间 | 说明 |
| :--- | :--- | :--- | :--- |
| Swarm | Docker | 2014年 | 内置于Docker，和Compose一起使用 |
| Mesos | Apache | 2007年 | 一般会结合marathon、Zookeeper一起使用 |
| Kubernets | Google | 2014年 | 结合Etcd一起使用 |

### 负载均衡与服务发现： <a id="&#x8D1F;&#x8F7D;&#x5747;&#x8861;&#x4E0E;&#x670D;&#x52A1;&#x53D1;&#x73B0;&#xFF1A;"></a>

![&#x8D1F;&#x8F7D;&#x5747;&#x8861;](https://cshihong.github.io/2018/04/02/Docker%E5%9F%BA%E7%A1%80%E5%8E%9F%E7%90%86/%E8%B4%9F%E8%BD%BD%E5%9D%87%E8%A1%A1.png)

图：负载均衡与服务发现

* 负载均衡：指的是请求到达负载均衡器以后，负载均衡器将请求平均分配到后面的每个容器上。

  常用的负载均衡技术有：haproxy、LVS、F5、Nginx。

* 服务发现：服务发现会自动将容器的配置信息发送给配置中心，包括IP、端口、对外的域名等。负载均衡器会周期性的从配置中心获取相关配置信息。并且将容器加入到相关的负载均衡器访问架构中。

  常用的服务发现技术有：Etcd、Zookeeper、Consul。

### 日志管理： <a id="&#x65E5;&#x5FD7;&#x7BA1;&#x7406;&#xFF1A;"></a>

容器时代，对日志处理平台的要求是，集中化、海量存储、灵活过滤、快速查询、伸缩性架构、高可用、强大的UI。![&#x65E5;&#x5FD7;&#x7BA1;&#x7406;](https://cshihong.github.io/2018/04/02/Docker%E5%9F%BA%E7%A1%80%E5%8E%9F%E7%90%86/%E6%97%A5%E5%BF%97%E7%AE%A1%E7%90%86.png)

图：日志管理平台EOK架构

日志管理平台EOK包含的三个组件：

1. LogStash Shipper：主要用于收集各种各样的日志。
2. ElasticSearch：主要用于存储和搜索日志。
3. Kibana：是一个用于界面展示的管理工具。

### Docker监控： <a id="Docker&#x76D1;&#x63A7;&#xFF1A;"></a>

![docker&#x76D1;&#x63A7;](https://cshihong.github.io/2018/04/02/Docker%E5%9F%BA%E7%A1%80%E5%8E%9F%E7%90%86/docker%E7%9B%91%E6%8E%A7.png)

图：Docker监控架构

从主机，应用，镜像，容器的维度进行监控。并构建监控相关的告警，跟踪相关的监控，这样的一个流程体系。即WANT原则。

当前常用的监控工具有：Zabbix、Nagios、cAdvisor、Datedog、Scout。

### Docker平台架构： <a id="Docker&#x5E73;&#x53F0;&#x67B6;&#x6784;&#xFF1A;"></a>

![docker&#x5E73;&#x53F0;&#x67B6;&#x6784;](https://cshihong.github.io/2018/04/02/Docker%E5%9F%BA%E7%A1%80%E5%8E%9F%E7%90%86/docker%E5%B9%B3%E5%8F%B0%E6%9E%B6%E6%9E%84.png)

图：Docker平台架构

* 基础平台有：集群管理，网络存储，镜像管理，容器调度，应用编排。
* 集成功能有：弹性架构，负载均衡，性能监控，日志管理，用户中心，租户管理，持续交付，版本控制。
* 同时需要提供强大的Web UI和开放的API服务。

### Docker平台技术体系： <a id="Docker&#x5E73;&#x53F0;&#x6280;&#x672F;&#x4F53;&#x7CFB;&#xFF1A;"></a>

![docker&#x5E73;&#x53F0;&#x6280;&#x672F;&#x4F53;&#x7CFB;](https://cshihong.github.io/2018/04/02/Docker%E5%9F%BA%E7%A1%80%E5%8E%9F%E7%90%86/docker%E5%B9%B3%E5%8F%B0%E6%8A%80%E6%9C%AF%E4%BD%93%E7%B3%BB.png)

图：Docker平台技术体系

## Docker平台的对比 <a id="Docker&#x5E73;&#x53F0;&#x7684;&#x5BF9;&#x6BD4;"></a>

### 三大编排工具之—Mesos： <a id="&#x4E09;&#x5927;&#x7F16;&#x6392;&#x5DE5;&#x5177;&#x4E4B;&#x2014;Mesos&#xFF1A;"></a>

Mesos Slave安装在集群节点上的，Mesos Master作为集群的管理节点。Slave会将资源的使用情况周期性的报告给Master节点。Mesos需要配合计算架构的Framework进行资源的调度。

Mesos配合Framework的工作流程如下：

![Mesos](https://cshihong.github.io/2018/04/02/Docker%E5%9F%BA%E7%A1%80%E5%8E%9F%E7%90%86/Mesos.png)

图：Mesos配合Framework的工作流程

1. Master会定期将所有主机的计算机资源使用情况报告给Framework Scheduler。
2. Scheduler进行调配后，下发部署的任务给集群节点。
3. 节点上的Framework Executor获取任务，进行容器的部署，在容器架构中，Executor就是容器的引擎。
4. 节点会将部署情况反馈给Master。
5. Master会更新最新的主机资源状态给Scheduler。

#### Mesos+Marathon+Zookeeper来构架Docker平台。 <a id="Mesos-Marathon-Zookeeper&#x6765;&#x6784;&#x67B6;Docker&#x5E73;&#x53F0;&#x3002;"></a>

![mesos+Marathon](https://cshihong.github.io/2018/04/02/Docker%E5%9F%BA%E7%A1%80%E5%8E%9F%E7%90%86/mesos+Marathon.png)

图：Mesos+Marathon+Zookeeper

* Marathon是一个可调用Docker引擎的Framework。Marathon可以将容器按照一定的调度策略部署到合适的主机上。
* Zookeeper主要是用来保证Mesos和Marathon来管理节点的高可用性。即当Master节点宕机以后，可以快速的选举中新的Master节点，从而不影响整个逻辑架构。Zookeeper本身也是一个高可用性的分布式架构。

### 三大编排工具之—Kubernetes： <a id="&#x4E09;&#x5927;&#x7F16;&#x6392;&#x5DE5;&#x5177;&#x4E4B;&#x2014;Kubernetes&#xFF1A;"></a>

Kubernetes是Google开源的容器集群管理系统，其提供应用部署、维护、 扩展机制等功能，利用Kubernetes能方便地管理跨机器运行容器化的应用，其主要功能如下：

1. 使用Docker对应用程序包装\(package\)、实例化\(instantiate\)、运行\(run\)。
2. 以集群的方式运行、管理跨机器的容器。
3. 解决Docker跨机器容器之间的通讯问题。
4. Kubernetes的自我修复机制使得容器集群总是运行在用户期望的状态。

#### Kubernetes中的pod： <a id="Kubernetes&#x4E2D;&#x7684;pod&#xFF1A;"></a>

![pod](https://cshihong.github.io/2018/04/02/Docker%E5%9F%BA%E7%A1%80%E5%8E%9F%E7%90%86/pod.png)

图：Kubernetes中的pod

* **Mesos构造的平台中容器是最小的单元。**
* **Kubernetes构建的平台中Pod是最小的单元**。容器是被封装在Pod中的。一个Pod可以拥有一个或多个Docker容器。
* Kubernetes设计Pod的目的主要是为了将需要紧密联系的容器放置到一个独立的空间内，即Pod。
* **同个pod中的容器可以方便的共享存储。**
* **同个pod中的容器可直接访问另一个容器。**

#### Kubernetes的整体架构： <a id="Kubernetes&#x7684;&#x6574;&#x4F53;&#x67B6;&#x6784;&#xFF1A;"></a>

![Kubernetes&#x5E73;&#x53F0;&#x67B6;&#x6784;](https://cshihong.github.io/2018/04/02/Docker%E5%9F%BA%E7%A1%80%E5%8E%9F%E7%90%86/Kubernetes%E5%B9%B3%E5%8F%B0%E6%9E%B6%E6%9E%84.png)

图：Kubernetes的整体架构

### 三大编排工具之—Swarm： <a id="&#x4E09;&#x5927;&#x7F16;&#x6392;&#x5DE5;&#x5177;&#x4E4B;&#x2014;Swarm&#xFF1A;"></a>

#### Swarm特点： <a id="Swarm&#x7279;&#x70B9;&#xFF1A;"></a>

* 对外以Docker API接口呈现，这样带来的好处是，如果现有系统使用Docker Engine，则可以平滑将Docker Engine切到Swarm上，无需改动现有系统。
* Swarm对用户来说，之前使用Docker的经验可以继承过来。非常容易上手，学习成本和二次开发成本都比较低。同时Swarm本身专注于Docker集群管理，非常轻量，占用资源也非常少。 \*“Batteries included but swappable”，简单说，就是插件化机制，Swarm中的各个模块都抽象出了API，可以根据自己一些特点进行定制实现。
* Swarm自身对Docker命令参数支持的比较完善，Swarm目前与Docker是同步发布的。Docker的新功能，都会第一时间在Swarm中体现。

Docker开源容器管理项目Swarm.

![Swarm](https://cshihong.github.io/2018/04/02/Docker%E5%9F%BA%E7%A1%80%E5%8E%9F%E7%90%86/Swarm.png)

图：Swarm架构

### Docker平台的对比： <a id="Docker&#x5E73;&#x53F0;&#x7684;&#x5BF9;&#x6BD4;&#xFF1A;"></a>

|  | Mesos | Swarm | Kubernetes |
| :--- | :--- | :--- | :--- |
| 应用定义 | 应用组定义的容器集合 | Compose中定义的容器逻辑集合 | Pods的集合 |
| 应用扩展 | 可以以容器或应用组为单位进行扩展 | 以容器为单位进行扩展 | 以pod为单位进行扩展 |
| 高可用 | 一般依赖于Zookeeper | 依赖依赖于自己的etcd等工具 | 依赖于Etcd |
| 负载均衡 | 可依赖于Marathon-lib | 自身继承lvs | 可依赖于haproxy |
| 日志和监控 | 使用外部工具 | 使用外部工具 | 可使用自带工具，也可使用外部工具 |

## Docker生态圈及企业应用案例 <a id="Docker&#x751F;&#x6001;&#x5708;&#x53CA;&#x4F01;&#x4E1A;&#x5E94;&#x7528;&#x6848;&#x4F8B;"></a>

### 应用场景：快速交付与CICD： <a id="&#x5E94;&#x7528;&#x573A;&#x666F;&#xFF1A;&#x5FEB;&#x901F;&#x4EA4;&#x4ED8;&#x4E0E;CICD&#xFF1A;"></a>

![&#x5E94;&#x7528;&#x573A;&#x666F;](https://cshihong.github.io/2018/04/02/Docker%E5%9F%BA%E7%A1%80%E5%8E%9F%E7%90%86/%E5%BA%94%E7%94%A8%E5%9C%BA%E6%99%AF.png)

图：Docker应用，快速交付

### 应用场景：弹性扩展： <a id="&#x5E94;&#x7528;&#x573A;&#x666F;&#xFF1A;&#x5F39;&#x6027;&#x6269;&#x5C55;&#xFF1A;"></a>

![&#x5F39;&#x6027;&#x6269;&#x5C55;](https://cshihong.github.io/2018/04/02/Docker%E5%9F%BA%E7%A1%80%E5%8E%9F%E7%90%86/%E5%BC%B9%E6%80%A7%E6%89%A9%E5%B1%95.png)

### 图：弹性扩展 <a id="&#x56FE;&#xFF1A;&#x5F39;&#x6027;&#x6269;&#x5C55;"></a>

## Docker自测题 <a id="Docker&#x81EA;&#x6D4B;&#x9898;"></a>

1. （判断题）Docker容器本质上是宿主机上的进程，宿主机只能是硬件服务器。 答案：错。
2. 下列哪个不是Docker引擎的组件？

   A.Docker daemon

   B.Rest API接口

   C.Docker CLI

   **D.Docker registries**

3. 下列关于Docker的namespace特征特性描述中哪一项是不正确的？

   A.不同用户的进程通过pid namespace隔离开

   B.mnt namespace允许不同namespace的进程看到的文件结构不同

   C.通过net namespace实现网络隔离

   **D.namespace不允许每个container拥有独立的hostname和domain name**

4. 在Docker中，关于容器的描述中不正确的哪项？

   A.Docker 利用容器来运行应用

   B.容器看做是一个简易版的 Linux 环境

   C.每个容器都是相互隔离的

   **D.Docker容器比较适合运行多个进程**

5. （多选）Docker 作为成功的容器解决方案，具有下列哪些特性？

   **A.可移植**

   **B.简单易用**

   **C.跨平台**

   D.无需维护

