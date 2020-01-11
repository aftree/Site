# Untitled



## Kafka简介 <a id="Kafka&#x7B80;&#x4ECB;"></a>

### Kafka概述： <a id="Kafka&#x6982;&#x8FF0;&#xFF1A;"></a>

Kafka由 linked-in 开源 。

kafka-高产出的分布式消息系统\(A high-throughput distributed messaging system\)。

Kafka是一个高吞吐、分布式、基于发布订阅的消息系统，利用Kafka技术可以在廉价的PC Server上搭建起大规模消息系统。

### Kafka的特性： <a id="Kafka&#x7684;&#x7279;&#x6027;&#xFF1A;"></a>

* 高吞吐量、低延迟：kafka每秒可以处理几十万条消息，它的延迟最低只有几毫秒，每个topic可以分多个partition, consumer group 对partition进行consume操作；
* 可扩展性：kafka集群支持热扩展；
* 持久性、可靠性：消息被持久化到本地磁盘，并且支持数据备份防止数据丢失；
* 容错性：允许集群中节点失败（若副本数量为n,则允许n-1个节点失败）；
* 高并发：支持数千个客户端同时读写；
* 支持实时在线处理和离线处理：可以使用Storm这种实时流处理系统对消息进行实时进行处理，同时还可以使用Hadoop这种批处理系统进行离线处理；

### Kafka应用场景： <a id="Kafka&#x5E94;&#x7528;&#x573A;&#x666F;&#xFF1A;"></a>

![&#x5E94;&#x7528;](https://cshihong.github.io/2018/06/02/Kafka%E6%8A%80%E6%9C%AF%E5%8E%9F%E7%90%86/%E5%BA%94%E7%94%A8.png)

图：Kafka应用场景

Kafka和其他组件比较，具有消息持久化、高吞吐、分布式、多客户端支持、实时等特性，适用于离线和在线的消息消费，如常规的消息收集、网站活性跟踪、聚合统计系统运营数据（监控数据）、日志收集等大量数据的互联网服务的数据收集场景。

1. 日志收集：一个公司可以用Kafka可以收集各种服务的log，通过kafka以统一接口服务的方式开放给各种consumer，例如Hadoop、Hbase、Solr等；
2. 消息系统：解耦和生产者和消费者、缓存消息等；
3. 用户活动跟踪：Kafka经常被用来记录web用户或者app用户的各种活动，如浏览网页、搜索、点击等活动，这些活动信息被各个服务器发布到kafka的topic中，然后订阅者通过订阅这些topic来做实时的监控分析，或者装载到Hadoop、数据仓库中做离线分析和挖掘；
4. 运营指标：Kafka也经常用来记录运营监控数据。包括收集各种分布式应用的数据，生产各种操作的集中反馈，比如报警和报告；
5. 流式处理：比如spark streaming和storm；
6. 事件源；
7. **kafka在FusionInsight中的位置：**

![&#x4F4D;&#x7F6E;](https://cshihong.github.io/2018/06/02/Kafka%E6%8A%80%E6%9C%AF%E5%8E%9F%E7%90%86/%E4%BD%8D%E7%BD%AE.png)

图：Kafka在FusionInsight中的位置

Kafka作为一个分布式消息系统，支持在线和离线消息处理，并提供了Java API以便其他组件对接使用。

## Kafka架构与功能 <a id="Kafka&#x67B6;&#x6784;&#x4E0E;&#x529F;&#x80FD;"></a>

### Kafka架构： <a id="Kafka&#x67B6;&#x6784;&#xFF1A;"></a>

![&#x67B6;&#x6784;](https://cshihong.github.io/2018/06/02/Kafka%E6%8A%80%E6%9C%AF%E5%8E%9F%E7%90%86/%E6%9E%B6%E6%9E%84.png)

图：Kafka架构图

基本概念：

* Broker：Kafka集群包含一个或多个服务实例，这些服务实例被称为Broker。是Kafka当中具体处理数据的单元。Kafka支持Broker的水平扩展。一般Broker数据越多，集群的吞吐力就越强。
* Topic：每条发布到Kafka集群的消息都有一个类别，这个类别被称为Topic。
* Partition：Kafka将Topic分成一个或多个Partition，每个Partition在物理上对应一个文件夹，该文件下存储这个Partition的所有消息。
* Producer：负责发布消息到Kafka Broker。
* Consumer：消息消费者，从Kafka Broker读取消息的客户端。
* Consumer Group：每个Consumer属于一个特定的Consumer Group（可为每个Consumer指定group name）。
* ZooKeeper：kafka与Zookeeper级联，通过Zookeeper管理级联配置，选举Leader。

#### Kafka Topics： <a id="Kafka-Topics&#xFF1A;"></a>

![Topics](https://cshihong.github.io/2018/06/02/Kafka%E6%8A%80%E6%9C%AF%E5%8E%9F%E7%90%86/Topics.png)

图；Kafka Topics

每条发布到Kafka的消息都有个类别，这个类别被称为Topic，也可以理解为一个存储消息的队列。例如：天气作为一个Topic，每天的温度消息就可以存储在“天气”这个队列里。数据总数先进先出。后来的数据追加到后面。

#### Kafka Partition： <a id="Kafka-Partition&#xFF1A;"></a>

![Partition](https://cshihong.github.io/2018/06/02/Kafka%E6%8A%80%E6%9C%AF%E5%8E%9F%E7%90%86/Partition.png)

图：Kafka Partition

每个Topic都有一个或者多个Partitions构成。每个Partition都是有序且不可变的消息队列。引入Partition机制，保证了Kafka的高吞吐能力。

在每个Partition当中，都会存储一个Log文件，Log文件中记录了所有的消息文件。一个Topic的多个Partition，它分布在不同的Kafka节点上，这样多个客户端包括Producer和Consumer就可以并发的访问不同节点，对同一个Topic进行消息的读取。

![1](https://cshihong.github.io/2018/06/02/Kafka%E6%8A%80%E6%9C%AF%E5%8E%9F%E7%90%86/1.png)

图：Partition

* Topic的Partition数量可以在创建时配置。
* Partition数据决定了每个Consumer group中并发消费者的最大数据。
* Consumer group A有两个消费者来读取4个Partition中数据；Consumer group B有四个消费者来读取4个partition中数据。

**Kafka Partition offset:**

![offset](https://cshihong.github.io/2018/06/02/Kafka%E6%8A%80%E6%9C%AF%E5%8E%9F%E7%90%86/offset.png)

图：Kafka Partition offset

* 任何发布到此Partition的消息都会被直接追加到log文件的尾部。
* 每条消息在文件中的位置称为offset（偏移量），offset是一个long型数字，它唯一标记一条消息。消费者通过（offset、partition、topic）跟踪记录。
* Kafka不支持消息的随机读取。

**Kafak Partition Replicas（副本）:**

![&#x526F;&#x672C;1](https://cshihong.github.io/2018/06/02/Kafka%E6%8A%80%E6%9C%AF%E5%8E%9F%E7%90%86/%E5%89%AF%E6%9C%AC1.png)

图：副本机制

* 副本以分区为单位。每个分区都有各自的主副本。
* 可以通过配置文件，配置副本的个数。
* 一个Kafka集群中，各个节点可能互为Leader和Follower。
* 主副本叫做Leader，从副本叫做Follower，处于同步状态的副本叫做In-Sync Replicas（ISR）。
* 如果Leader失效，那么将会有其他的Follower来接管成为新的Leader。如果由于Follower自身的原因，比如网络原因导致同步落后太多，那么当Leader失效后，就不会将这个Follower选为leader。
* 由于Leader的Server承载了全部的请求压力，因此从集群的整体考虑，Kafka会将Leader均衡的分散在每个实例上，来保持整体稳定。
* Follower通过**拉取**的方式从Leader中同步数据。消费者和生产这都是从Leader中读取数据，不与Follower交互。

**主副本和从副本的数据同步：**

![&#x526F;&#x672C;2](https://cshihong.github.io/2018/06/02/Kafka%E6%8A%80%E6%9C%AF%E5%8E%9F%E7%90%86/%E5%89%AF%E6%9C%AC2.png)

图：主副本和从副本的数据同步

从Partition的Leader复制数据到Follower，需要一个线程，实际上，复制数据的操作，是Follower主动从Leader上批量拉取数据，这就极大的提高了Kafka的吞吐量。Follower复制数据的线程叫做ReplicaFetcher Thread，而Kafka的Producer和Consumer只与Leader进行交互，不会与Follower进行交互。

![&#x526F;&#x672C;3](https://cshihong.github.io/2018/06/02/Kafka%E6%8A%80%E6%9C%AF%E5%8E%9F%E7%90%86/%E5%89%AF%E6%9C%AC3.png)

Kafka中每个Broker启动的时候，都会创建一个副本管理服务ReplicaManager，该服务负责维护ReplicaFether Thread与其他Broker链路连接关系。该服务中存在的Follower Partition对应的Leader Partition会分布在不同的Broker上，这些Broker创建相同数量的ReplicaFether Thread，同步对应Partition数据。Kafka中Partition间复制数据，是由Follower主动从Leader拉消息的。Follower每次读取消息都会更新HW状态，用于记录当前最新消息的标识。每当Follower的Partition发生变化而影响Leader所在的Broker时，ReplicaManager就会新建或者销毁相对应的ReplicaFether Thread。

### Kafka Logs： <a id="Kafka-Logs&#xFF1A;"></a>

为了使得Kafka的吞吐率可以线性提高，物理上把Topic分成一个或多个Partition，每个Partition在物理上对应一个文件夹，该文件夹下存储这个Partition的所有消息和索性文件。Kafka把Topic中一个Partition大文件分成多个小文件段通过多个小文件段，就容易定期清除或删除已经消费完的文件，减少磁盘占用。

Kafka的存储布局非常简单，Topic的每个分区对应一个逻辑日志，物理上一个日志为相同大小的一个分段文件。每次Producer发布一个消息到一个分区的时候，代理就将这些数据追加到最后一个段文件当中。当发布的消息数量达到消息设定的阈值，或者经过一定的时间后，段文件就会真正的写到磁盘当中。在写入完成以后，消息就会公开给Consumer。

同一个Topic下有不同的分区，每个分区会划分为多个文件，只有一个当前文件在写，其他文件是只读的。当写满一个文件（即达到某个设定的值）Kafka会新建一个空文件继续来写。而老文件切换为只读。

文件的命名以起始的偏移量来进行命名。Segment Files由两大部分组成，分别为Index file和data file，此两个文件一一对应成对出现。后缀 .index 和 .log 就分别表示为Segment的索引文件和数据文件。Segment文件的命名规则是：Partition全局的第一个Segment从0开始，后续每个segment文件为上一个全局Partition的最大offset，这个数据时64位的long型数据。如果没有数据就用0进行填充。通常把日志文件默认为1G，当达到1G就会创建新的Log文件和index文件。如果设置的参数过小，会产生大量的log文件和index文件，系统在启动的时候就需要加载大量的index到内存，占用大量的句柄。如果设置的太大，分段文件又比较少，不利于快速的查找。Kafka就是通过索引实现快速的定位message。

![log](https://cshihong.github.io/2018/06/02/Kafka%E6%8A%80%E6%9C%AF%E5%8E%9F%E7%90%86/log.png)

图：索引文件

* 通过索引信息可以快速定位message。
* 通过将index元数据全部映射到memory，可以避免segment file的index数据IO磁盘操作。
* 通过索引文件稀疏存储，可以大幅降低index文件元数据占用空间大小。
* 稀疏存储：将原来完整的数据，只间隔的选择多条数据进行存储。

**Kafka Log Cleanup:**

日志的清理方式有两种：delete和compact。

删除的阈值有两种：过期的时间和分区内总日志大小。

![&#x5220;&#x9664;](https://cshihong.github.io/2018/06/02/Kafka%E6%8A%80%E6%9C%AF%E5%8E%9F%E7%90%86/%E5%88%A0%E9%99%A4.png)

![cleanup](https://cshihong.github.io/2018/06/02/Kafka%E6%8A%80%E6%9C%AF%E5%8E%9F%E7%90%86/cleanup.png)

图：日志清理方式–compact

compact操作是保存每个消息的最新value值。消息时顺序存储的，offset大的为最新的数据。

### Kafka数据可靠性： <a id="Kafka&#x6570;&#x636E;&#x53EF;&#x9760;&#x6027;&#xFF1A;"></a>

Kafka所有消息都会被持久化到磁盘中，同时Kafka通过对Topic Partition设置Replication来保障数据可靠。

消息传输过程中保障通常有以下三种：

* 最多一次（At Most Once）：消息可能丢失；消息不会重复发送和处理。
* 最少一次（At Lease Once）：消息不会丢失；消息可能会重复发送和处理。
* 仅有一次（Exactly Once）：消息不会丢失；消息仅被处理一次。

Kafka消息传输保障机制，通过配置不同的消息发送模式来保障消息传输，进而满足不同的可靠性要求应用场景。

![&#x53EF;&#x9760;](https://cshihong.github.io/2018/06/02/Kafka%E6%8A%80%E6%9C%AF%E5%8E%9F%E7%90%86/%E5%8F%AF%E9%9D%A0.png)

## Kafka关键流程 <a id="Kafka&#x5173;&#x952E;&#x6D41;&#x7A0B;"></a>

### 写流程： <a id="&#x5199;&#x6D41;&#x7A0B;&#xFF1A;"></a>

![&#x5199;&#x6D41;&#x7A0B;](https://cshihong.github.io/2018/06/02/Kafka%E6%8A%80%E6%9C%AF%E5%8E%9F%E7%90%86/%E5%86%99%E6%B5%81%E7%A8%8B.png)

图：Kafka写流程–Producer写数据

总体流程：

* Producer连接任意存活的Broker，请求制定Topic、Partition的Leader元数据信息，然后直接与对应的Broker直接链接，发布数据。

开发分区接口：

* 用户可以指定分区函数，使得消息可以根据Key，发送到特定的Partition。

### Kafka读流程： <a id="Kafka&#x8BFB;&#x6D41;&#x7A0B;&#xFF1A;"></a>

![&#x8BFB;&#x6D41;&#x7A0B;](https://cshihong.github.io/2018/06/02/Kafka%E6%8A%80%E6%9C%AF%E5%8E%9F%E7%90%86/%E8%AF%BB%E6%B5%81%E7%A8%8B.png)

图：Kafka读流程–Consumer读数据

总体流程：

* Consumer连接指定Topic Partition所在的Leader Broker，用主动获取方式从Kafka中获取消息。

## Kafka在Zookeeper上的目录结构 <a id="Kafka&#x5728;Zookeeper&#x4E0A;&#x7684;&#x76EE;&#x5F55;&#x7ED3;&#x6784;"></a>

### Zookeeper在Kafka的作用： <a id="Zookeeper&#x5728;Kafka&#x7684;&#x4F5C;&#x7528;&#xFF1A;"></a>

1. 无论是kafka集群，还是producer和consumer都依赖于zookeeper来保证系统可用性集群保存一些meta信息。
2. Kafka使用zookeeper作为其分布式协调框架，很好的将消息生产、消息存储、消息消费的过程结合在一起。
3. 同时借助zookeeper，kafka能够生产者、消费者和broker在内的所以组件在无状态的情况下，建立起生产者和消费者的订阅关系，并实现生产者与消费者的负载均衡。

### Zookeeper Shell： <a id="Zookeeper-Shell&#xFF1A;"></a>

通过zkCli来连接正在运行的Zookeeper Shell客户端，可以通过ls和get命令来获取Kafka相关信息。

![&#x7528;&#x6CD5;](https://cshihong.github.io/2018/06/02/Kafka%E6%8A%80%E6%9C%AF%E5%8E%9F%E7%90%86/%E7%94%A8%E6%B3%95.png)

图：用法示例

### Kafka in ZooKeeper： <a id="Kafka-in-ZooKeeper&#xFF1A;"></a>

![&#x8D70;](https://cshihong.github.io/2018/06/02/Kafka%E6%8A%80%E6%9C%AF%E5%8E%9F%E7%90%86/%E8%B5%B0.png)

图：Kafka在ZooKeeper中的目录结构

## Kafka Cluster Mirroring <a id="Kafka-Cluster-Mirroring"></a>

![&#x955C;&#x50CF;](https://cshihong.github.io/2018/06/02/Kafka%E6%8A%80%E6%9C%AF%E5%8E%9F%E7%90%86/%E9%95%9C%E5%83%8F.png)

图：Kafka CLuster Mirroring  
Kafka Cluster Mirroring是Kafka跨集群数据同步方案，通过Kafka内置的MirrorMaker工具来实现。通过Mirror Maker工具中的consumer从源集群消费数据，然后再通过内置的Producer，将数据重新发布到目标集群。

> 以上内容为听华为大数据培训课程和大学MOOC上厦门大学 林子雨的《大数据技术原理与应用》课程而整理的笔记。

