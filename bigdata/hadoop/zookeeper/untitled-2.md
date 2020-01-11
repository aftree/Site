# Untitled



## ZooKeeper简介 <a id="ZooKeeper&#x7B80;&#x4ECB;"></a>

### ZooKeeper概述： <a id="ZooKeeper&#x6982;&#x8FF0;&#xFF1A;"></a>

ZooKeeper是一个分布式的，开放源码的分布式应用程序协调服务，是Google的Chubby一个开源的实现，它是集群的管理者，监视着集群中各个节点的状态根据节点提交的反馈进行下一步合理操作。最终，将简单易用的接口和性能高效、功能稳定的系统提供给用户。

ZooKeeper分布式服务框架主要是用来解决分布式应用中经常遇到的一些数据管理问题，提供分布式、高可用性的协调服务能力。

安全模式下ZooKeeper依赖于Kerberos和L搭配Server进行安全认证，非安全模式则不依赖与Kerberos与Ldap。ZooKeeper作为底层组件广泛被上层组件使用并依赖，如Kafka，HDFS，HBase，Storm等。

### ZooKeeper提供什么？ <a id="ZooKeeper&#x63D0;&#x4F9B;&#x4EC0;&#x4E48;&#xFF1F;"></a>

1. 文件系统
2. 通知机制：客户端注册监听它关心的目录节点，当目录节点发生变化（数据改变、被删除、子目录节点增加删除）时，zookeeper会通知客户端。
3. ZooKeeper文件系统

   每个子目录项如 NameService 都被称作为znode，和文件系统一样，我们能够自由的增加、删除znode，在一个znode下增加、删除子znode，唯一的不同在于znode是可以存储数据的。

有四种类型的znode：

1. PERSISTENT-持久化目录节点

   客户端与zookeeper断开连接后，该节点依旧存在 。

2. PERSISTENT\_SEQUENTIAL-持久化顺序编号目录节点

   客户端与zookeeper断开连接后，该节点依旧存在，只是Zookeeper给该节点名称进行顺序编号 。

3. EPHEMERAL-临时目录节点

   客户端与zookeeper断开连接后，该节点被删除 。

4. EPHEMERAL\_SEQUENTIAL-临时顺序编号目录节点

   客户端与zookeeper断开连接后，该节点被删除，只是Zookeeper给该节点名称进行顺序编号 。

### ZooKeeper做什么？ <a id="ZooKeeper&#x505A;&#x4EC0;&#x4E48;&#xFF1F;"></a>

1. 命名服务
2. 配置管理
3. 集群管理
4. 分布式锁
5. 队列管理

**Zookeeper命名服务**

> 在zookeeper的文件系统里创建一个目录，即有唯一的path。在我们使用tborg无法确定上游程序的部署机器时即可与下游程序约定好path，通过path即能互相探索发现。

**Zookeeper的配置管理**

> 程序总是需要配置的，如果程序分散部署在多台机器上，要逐个改变配置就变得困难。现在把这些配置全部放到zookeeper上去，保存在 Zookeeper 的某个目录节点中，然后所有相关应用程序对这个目录节点进行监听，一旦配置信息发生变化，每个应用程序就会收到 Zookeeper 的通知，然后从 Zookeeper 获取新的配置信息应用到系统中就好

**Zookeeper集群管理**

> 所谓集群管理无在乎两点：是否有机器退出和加入、选举master。
>
> 对于第一点，所有机器约定在父目录GroupMembers下创建临时目录节点，然后监听父目录节点的子节点变化消息。一旦有机器挂掉，该机器与 zookeeper的连接断开，其所创建的临时目录节点被删除，所有其他机器都收到通知：某个兄弟目录被删除，于是，所有人都知道：它上船了。
>
> 新机器加入也是类似，所有机器收到通知：新兄弟目录加入，highcount又有了，对于第二点，我们稍微改变一下，所有机器创建临时顺序编号目录节点，每次选取编号最小的机器作为master就好。

**Zookeeper分布式锁**

> 有了zookeeper的一致性文件系统，锁的问题变得容易。锁服务可以分为两类，一个是保持独占，另一个是控制时序。
>
> 对于第一类，我们将zookeeper上的一个znode看作是一把锁，通过createznode的方式来实现。所有客户端都去创建 /distribute\_lock 节点，最终成功创建的那个客户端也即拥有了这把锁。用完删除掉自己创建的distribute\_lock 节点就释放出锁。
>
> 对于第二类， /distribute\_lock 已经预先存在，所有客户端在它下面创建临时顺序编号目录节点，和选master一样，编号最小的获得锁，用完删除，依次方便。

**Zookeeper队列管理**

> 两种类型的队列：
>
> 1、同步队列，当一个队列的成员都聚齐时，这个队列才可用，否则一直等待所有成员到达。
>
> 2、队列按照 FIFO 方式进行入队和出队操作。
>
> 第一类，在约定目录下创建临时目录节点，监听节点数目是否是我们要求的数目。
>
> 第二类，和分布式锁服务中的控制时序场景基本原理一致，入列有编号，出列按编号。

### ZooKeeper在FusionInsight中的位置： <a id="ZooKeeper&#x5728;FusionInsight&#x4E2D;&#x7684;&#x4F4D;&#x7F6E;&#xFF1A;"></a>

![&#x4F4D;&#x7F6E;](https://cshihong.github.io/2018/06/03/ZooKeeper%E6%8A%80%E6%9C%AF%E5%8E%9F%E7%90%86/%E4%BD%8D%E7%BD%AE.png)

图：ZooKeeper在FusionInsight中的位置

ZooKeeper基于开源Apache ZooKeeper作为底层组件为上层组件提供服务，主要用于解决分布式应用中经常遇到的一些数据管理问题。

## ZooKeeper系统架构 <a id="ZooKeeper&#x7CFB;&#x7EDF;&#x67B6;&#x6784;"></a>

### ZooKeeper角色描述： <a id="ZooKeeper&#x89D2;&#x8272;&#x63CF;&#x8FF0;&#xFF1A;"></a>

![&#x63CF;&#x8FF0;](https://cshihong.github.io/2018/06/03/ZooKeeper%E6%8A%80%E6%9C%AF%E5%8E%9F%E7%90%86/%E6%8F%8F%E8%BF%B0.png)

### ZooKeeper服务架构 - 模型： <a id="ZooKeeper&#x670D;&#x52A1;&#x67B6;&#x6784;-&#x6A21;&#x578B;&#xFF1A;"></a>

![&#x67B6;&#x6784;](https://cshihong.github.io/2018/06/03/ZooKeeper%E6%8A%80%E6%9C%AF%E5%8E%9F%E7%90%86/%E6%9E%B6%E6%9E%84.png)

图：ZooKeeper服务架构

* ZooKeeper集群分布式地分布在一组机器中，由一组Server节点组成，这一组Server节点中只存在一个Leader节点，其他节点都为Follower。
* 所有节点存储整份数据（在内存也在硬盘）。
* 启动时选举出Leader。
* ZooKeeper使用自定义的原子消息协议，保证了整个系统中节点数据的一致性。
* Leader节点在接收到数据变更请求后，先写磁盘再写内存。

![&#x540D;](https://cshihong.github.io/2018/06/03/ZooKeeper%E6%8A%80%E6%9C%AF%E5%8E%9F%E7%90%86/%E5%90%8D.png)

图：节点分布命名

1. 分层命名空间。
2. 每个命名空间的节点都叫做 znode。
3. 每个znode被路径区分（如：/app1）。
4. znode节点类型 - 永久节点和临时节点。
5. 临时节点不能有子节点。
6. 每个znode节点有数据，也可以选择有子节点。
7. znode不能被重命名
8. 可以给znode增加或删除watchers。

### ZooKeeper分布式与数据复制： <a id="ZooKeeper&#x5206;&#x5E03;&#x5F0F;&#x4E0E;&#x6570;&#x636E;&#x590D;&#x5236;&#xFF1A;"></a>

Zookeeper作为一个集群提供一致的数据服务，自然，它要在所有机器间做数据复制。数据复制的好处：

1. 容错：一个节点出错，不致于让整个系统停止工作，别的节点可以接管它的工作；
2. 提高系统的扩展能力 ：把负载分布到多个节点上，或者增加节点来提高系统的负载能力；
3. 提高性能：让客户端本地访问就近的节点，提高用户访问速度。

从客户端读写访问的透明度来看，数据复制集群系统分下面两种：

1. 写主\(WriteMaster\) ：对数据的修改提交给指定的节点。读无此限制，可以读取任何一个节点。这种情况下客户端需要对读与写进行区别，俗称读写分离；
2. 写任意\(Write Any\)：对数据的修改可提交给任意的节点，跟读一样。这种情况下，客户端对集群节点的角色与变化透明。

对zookeeper来说，它采用的方式是写任意。通过增加机器，它的读吞吐能力和响应能力扩展性非常好，而写，随着机器的增多吞吐能力肯定下降（这也是它建立observer的原因），而响应能力则取决于具体实现方式，是延迟复制保持最终一致性，还是立即复制快速响应。

### ZooKeeper的工作原理： <a id="ZooKeeper&#x7684;&#x5DE5;&#x4F5C;&#x539F;&#x7406;&#xFF1A;"></a>

Zookeeper 的核心是原子广播，这个机制保证了各个Server之间的同步。实现这个机制的协议叫做Zab协议。Zab协议有两种模式，它们分别是恢复模式（选主）和广播模式（同步）。当服务启动或者在领导者崩溃后，Zab就进入了恢复模式，当领导者被选举出来，且大多数Server完成了和 leader的状态同步以后，恢复模式就结束了。状态同步保证了leader和Server具有相同的系统状态。

为了保证事务的顺序一致性，zookeeper采用了递增的事务id号（zxid）来标识事务。所有的提议（proposal）都在被提出的时候加上了zxid。实现中zxid是一个64位的数字，它高32位是epoch用来标识leader关系是否改变，每次一个leader被选出来，它都会有一个新的epoch，标识当前属于那个leader的统治时期。低32位用于递增计数。

### 容灾能力： <a id="&#x5BB9;&#x707E;&#x80FD;&#x529B;&#xFF1A;"></a>

一般情况下，ZooKeeper能够完成选举即能够正常对外提供服务。ZooKeeper选举时，当某个实例获得半数以上的票数时，则变为Leader。

对于n个示例，n可能为技术或偶数：

* n为奇数时，假定n=2x+1，则成为leader的节点需要获得x+1票。容灾能力为x。
* n为偶数时，假定n=2x+2，则成为leader的节点需要获得x+2票（大于一半），容灾能力为x。

由此可见，2x+1个节点与2x+2个节点的容灾能力相同（3个与4个相同，5个与6个相同…）,而考虑到选举以及完成写操作的速度与节点数的相关性，建议ZooKeeper部署奇数个节点。

## ZooKeeper关键特性介绍 <a id="ZooKeeper&#x5173;&#x952E;&#x7279;&#x6027;&#x4ECB;&#x7ECD;"></a>

### 关键特性： <a id="&#x5173;&#x952E;&#x7279;&#x6027;&#xFF1A;"></a>

* 最终一致性：无论哪个server，对外展示的均是同一个视图。
* 实时性：保证客户端将在一个时间间隔范围内获得服务器的更新信息，或者服务器失效的信息。
* 可靠性：一条消息被一个server接受，它将被所有server接受。
* 等待无关性：慢的或者失效的client不会干预快速的client的请求，使得每个client都能有效的等待。
* 原子性：更新只能成功或者失败，没有中间状态。
* 顺序一致性：客户端所发送的更新会安装它们被发送的顺序进行应用。

### 读特性： <a id="&#x8BFB;&#x7279;&#x6027;&#xFF1A;"></a>

![&#x8BFB;&#x7279;&#x6027;](https://cshihong.github.io/2018/06/03/ZooKeeper%E6%8A%80%E6%9C%AF%E5%8E%9F%E7%90%86/%E8%AF%BB%E7%89%B9%E6%80%A7.png)

图：读特性

由ZooKeeper的一致性可知，客户端无论连接哪个server，获取的均是同一个视图。所以，读操作可在客户端与任意节点间完成。

### 写特性： <a id="&#x5199;&#x7279;&#x6027;&#xFF1A;"></a>

![&#x5199;&#x7279;&#x6027;](https://cshihong.github.io/2018/06/03/ZooKeeper%E6%8A%80%E6%9C%AF%E5%8E%9F%E7%90%86/%E5%86%99%E7%89%B9%E6%80%A7.png)

图：写特性

客户端可以向任意一个Server提出写的请求，然后这个server会将这个请求发送给ZooKeeper 的 leader，Leader在获取写请求以后，会向所有节点发送这条写的请求，询问其他节点是否可以执行这个写的操作。Follower节点会根据自身的情况给出反馈信息，Leader根据收到的反馈信息，如果同意写的信息超过半数，那么就执行写操作。如果同意写的票数少于半数，那么就不执行写操作。如果要执行写操作,Leader最后会将投票结果反馈给各个Follower，然后完成写的操作，每个Follower节点会同步Leader节点的数据。最后整个写的操作就完成了。

### ACL\(访问控制列表\)： <a id="ACL-&#x8BBF;&#x95EE;&#x63A7;&#x5236;&#x5217;&#x8868;-&#xFF1A;"></a>

ACL可以控制访问ZooKeeper的节点，只能应用于特定的znode上，而不能应用于该znode的所有子节点上。设置ACL的命令为：setAcl /znode scheme : id : perm。

Scheme为认证方式，ZooKeeper内置了4种方式：

1. world:一个单独的ID，表示任何人都可以访问。
2. auth：不使用ID，只有认证的用户可以访问。
3. digest：使用username：passwrod生成MD5哈希值作为认证ID。
4. IP：使用客户端主机IP地址来进行认证。

id：用来认证的字段，用来判断认证信息是否合法，不同的scheme的认证方式不同。

Perm：即Permission，通过Acl认证的用户对节点可拥有的操作权限。

### 日志增强： <a id="&#x65E5;&#x5FD7;&#x589E;&#x5F3A;&#xFF1A;"></a>

Ephemeral node（临时节点）在session过期之后就会被系统删除。在审计日志中添加Ephemeral node被删除的审计日志，以便了解当时Ephemeral node的状态信息。

### ZooKeeper客户端常用命令使用： <a id="ZooKeeper&#x5BA2;&#x6237;&#x7AEF;&#x5E38;&#x7528;&#x547D;&#x4EE4;&#x4F7F;&#x7528;&#xFF1A;"></a>

* 调用ZooKeeper客户端，执行命令：

  `zkCli.sh -server 172.16.0.1:24002`

* 创建节点：`create /node`
* 列出节点子节点：`ls / node`
* 创建节点数据: `set /node data`
* 获取节点数据：`get /node`
* 删除节点：`delete /node`
* 删除节点及所有子节点：`deleteall /node`

## ZooKeeper与其他组件的关系 <a id="ZooKeeper&#x4E0E;&#x5176;&#x4ED6;&#x7EC4;&#x4EF6;&#x7684;&#x5173;&#x7CFB;"></a>

### ZooKeeper和Storm Nimbus HA的配合关系： <a id="ZooKeeper&#x548C;Storm-Nimbus-HA&#x7684;&#x914D;&#x5408;&#x5173;&#x7CFB;&#xFF1A;"></a>

![Nimbus](https://cshihong.github.io/2018/06/03/ZooKeeper%E6%8A%80%E6%9C%AF%E5%8E%9F%E7%90%86/Nimbus.png)

图：ZooKeeper和Storm Nimbus HA的配合关系

Storm Nimbus利用ZooKeeper来选举主备。

ZooKeeper提供了以下两种能力：

1. 分布式锁服务：

   多个Nimbus进程都会尝试去Zookeeper创建对应的节点，该节点只能被一个Nimbus进程创建成功，创建成功的Nimbus进程成为主Nimbus。

2. 时间侦听机制–watcher：

   备Nimbus侦听对应的ZooKeeper节点。主Nimbus进程宕掉之后，该节点会被删除，那么备Nimbus就可以接受到相应的消息。

### ZooKeeper和HDFS的配合关系： <a id="ZooKeeper&#x548C;HDFS&#x7684;&#x914D;&#x5408;&#x5173;&#x7CFB;&#xFF1A;"></a>

![HDFS](https://cshihong.github.io/2018/06/03/ZooKeeper%E6%8A%80%E6%9C%AF%E5%8E%9F%E7%90%86/HDFS.png)

图：ZooKeeper和HDFS的关系

ZKFC\(ZooKeeper Failover Controller\)作为一个ZooKeeper集群的客户端，用来监控NameNode的状态信息。ZKFC进程仅在部署了NameNode的节点中存在。HDFS NameNode的Active和Standby节点均部署有ZKFC进程：

* HDFS NameNode的ZKFC连接到Zookeeper，把主机名等信息保存到ZooKeeper中，即“/hadoop-ha”下的znode目录里。先创建znode目录的NmaeNode节点为主节点，另一个为备节点。HDFS NameNode Standby通过ZooKeeper定时读取NmaeNode信息。
* 当主节点进程异常结束时，HDFS NameNode Standby通过ZooKeeper感知“/hadoop-ha”目录下发生了变化，NmaeNode会进行主备切换。

### ZooKeeper和YARN的配合关系： <a id="ZooKeeper&#x548C;YARN&#x7684;&#x914D;&#x5408;&#x5173;&#x7CFB;&#xFF1A;"></a>

![YARN](https://cshihong.github.io/2018/06/03/ZooKeeper%E6%8A%80%E6%9C%AF%E5%8E%9F%E7%90%86/YARN.png)

图：ZooKeeper和YARN的配置关系

在系统启动时，ResourceManager会尝试把选举信息写入Zookeeper，第一个成功写入Zookeeper的ResourceManager被选举为Active ResourceManager，另一个为Standby ResourceManager。Standby ResourceManager定时去ZooKeeper监控Active ResourceManager选举信息。

Active ResourceManager还会在ZooKeeper中创建Statestore目录，存储Application相关信息。当Active ResourceManager产生故障时，Standby ResourceManager会从Statestore目录获取Application相关信息，恢复数据并升为Active。

### ZooKeeper和HBase的配合关系： <a id="ZooKeeper&#x548C;HBase&#x7684;&#x914D;&#x5408;&#x5173;&#x7CFB;&#xFF1A;"></a>

![HBase](https://cshihong.github.io/2018/06/03/ZooKeeper%E6%8A%80%E6%9C%AF%E5%8E%9F%E7%90%86/HBase.png)

图：ZooKeeper和HBase的关系

HRegionServer把自己以Ephemeral方式注册到Zookeeper中。其中ZooKeeper存储HBase的如下信息：HBase元数据、HMaster地址。

HMaster通过ZooKeeper随时感知各个HRegionServer的健康状况，以便进行控制管理。

HBase也可以部署多对HMaster，类似HDFS NameNode，当HMaster主节点出现故障时，HMaster备用节点会通过ZooKeeper获取主HMaster存储的整个HBase集群状态信息。即通过ZooKeeper实现避免HBase单点故障问题。

> 以

