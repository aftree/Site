# Untitled

## 大数据处理技术发展趋势 <a id="&#x5927;&#x6570;&#x636E;&#x5904;&#x7406;&#x6280;&#x672F;&#x53D1;&#x5C55;&#x8D8B;&#x52BF;"></a>

### 传统数据处理系统面临的问题： <a id="&#x4F20;&#x7EDF;&#x6570;&#x636E;&#x5904;&#x7406;&#x7CFB;&#x7EDF;&#x9762;&#x4E34;&#x7684;&#x95EE;&#x9898;&#xFF1A;"></a>

![&#x4F20;&#x7EDF;&#x6570;&#x636E;&#x5904;&#x7406;&#x7CFB;&#x7EDF;&#x9762;&#x4E34;&#x7684;&#x95EE;&#x9898;](https://cshihong.github.io/2018/04/07/Hadoop%E5%9F%BA%E7%A1%80%E6%8A%80%E6%9C%AF%E6%A6%82%E8%BF%B0/%E4%BC%A0%E7%BB%9F%E6%95%B0%E6%8D%AE%E5%A4%84%E7%90%86%E7%B3%BB%E7%BB%9F%E9%9D%A2%E4%B8%B4%E7%9A%84%E9%97%AE%E9%A2%98.png)

图：传统数据处理系统面临的问题

如上图：传统数据处理系统面临的问题主要有：

1. 海量数据的存储成本。
2. 有限的扩展能力。
3. 数据资产对外增值。
4. 大数据处理能力的不足。
5. 单一数据源。
6. 流式数据处理缺失。

### 数据处理技术演进趋势： <a id="&#x6570;&#x636E;&#x5904;&#x7406;&#x6280;&#x672F;&#x6F14;&#x8FDB;&#x8D8B;&#x52BF;&#xFF1A;"></a>

![&#x5927;&#x6570;&#x636E;&#x6F14;&#x8FDB;&#x8D8B;&#x52BF;1](https://cshihong.github.io/2018/04/07/Hadoop%E5%9F%BA%E7%A1%80%E6%8A%80%E6%9C%AF%E6%A6%82%E8%BF%B0/%E5%A4%A7%E6%95%B0%E6%8D%AE%E6%BC%94%E8%BF%9B%E8%B6%8B%E5%8A%BF1.png)

![&#x5927;&#x6570;&#x636E;&#x6F14;&#x8FDB;&#x8D8B;&#x52BF;2](https://cshihong.github.io/2018/04/07/Hadoop%E5%9F%BA%E7%A1%80%E6%8A%80%E6%9C%AF%E6%A6%82%E8%BF%B0/%E5%A4%A7%E6%95%B0%E6%8D%AE%E6%BC%94%E8%BF%9B%E8%B6%8B%E5%8A%BF2.png)

图：数据处理演进趋势

数据处理技术演进的趋势：

1. 完全共享模式

   存储方式：磁盘。

   特点：单机、Scale up。

   缺点：性能存在瓶颈、扩展性能差。

2. 数据库服务器

   存储方式：高性能网络存储。

   特点：集群、Share Everything、结构化、关系型。Flash Cache+分布式块存储+IB。

3. 完全不共享模式

   存储方式：磁盘阵列。

   特点：集群、Share Nothing、结构化、关系型。通用的硬件。

4. NameNode模式

   存储方式：DateNode。

   特点：集群、Share Nothing。开放、全球生态。结构化、半结构化、非结构化。高性能、实时。

### 大数据的特征： <a id="&#x5927;&#x6570;&#x636E;&#x7684;&#x7279;&#x5F81;&#xFF1A;"></a>

1. 数据量大：100T-PB级，几TB/天。
2. 格式复杂：结构化，非结构化。
3. 响应速度要求高：毫秒级。
4. 数据价值密度低：零点零几USD/KB。

## Hadoop概述 <a id="Hadoop&#x6982;&#x8FF0;"></a>

### Hadoop简介： <a id="Hadoop&#x7B80;&#x4ECB;&#xFF1A;"></a>

* Hadoop是Apache软件基金会旗下的一个开源分布式计算平台，为用户提供了系统底层细节透明的分布式基础架构
* Hadoop是基于Java语言开发的，具有很好的跨平台特性，并且可以部署在廉价的计算机集群中
* Hadoop的核心是分布式文件系统HDFS（Hadoop Distributed File System）和MapReduce
* Hadoop被公认为行业大数据标准开源软件，在分布式环境下提供了海量数据的处理能力
* 几乎所有主流厂商都围绕Hadoop提供开发工具、开源软件、商业化工具和技术服务，如谷歌、雅虎、微软、思科、淘宝等，都支持Hadoop

### Hadoop发展简史： <a id="Hadoop&#x53D1;&#x5C55;&#x7B80;&#x53F2;&#xFF1A;"></a>

![hadoop](https://cshihong.github.io/2018/04/07/Hadoop%E5%9F%BA%E7%A1%80%E6%8A%80%E6%9C%AF%E6%A6%82%E8%BF%B0/hadoop.png)

图：Hadoop的标志

* Hadoop最初是由Apache Lucene项目的创始人Doug Cutting开发的文本搜索库。Hadoop源自始于2002年的Apache Nutch项目——一个开源的网络搜索引擎并且也是Lucene项目的一部分。
* 在2004年，Nutch项目也模仿GFS开发了自己的分布式文件系统NDFS（Nutch Distributed File System），也就是HDFS的前身。
* 2004年，谷歌公司又发表了另一篇具有深远影响的论文，阐述了MapReduce分布式编程思想。
* 2005年，Nutch开源实现了谷歌的MapReduce。
* 到了2006年2月，Nutch中的NDFS和MapReduce开始独立出来，成为Lucene项目的一个子项目，称为Hadoop，同时，Doug Cutting加盟雅虎。
* 2008年1月，Hadoop正式成为Apache顶级项目，Hadoop也逐渐开始被雅虎之外的其他公司使用。
* 2008年4月，Hadoop打破世界纪录，成为最快排序1TB数据的系统，它采用一个由910个节点构成的集群进行运算，排序时间只用了209秒。
* 在2009年5月，Hadoop更是把1TB数据排序时间缩短到62秒。Hadoop从此名声大震，迅速发展成为大数据时代最具影响力的开源分布式开发平台，并成为事实上的大数据处理标准。

### Hadoop的特性： <a id="Hadoop&#x7684;&#x7279;&#x6027;&#xFF1A;"></a>

Hadoop是一个能够对大量数据进行分布式处理的软件框架，并且是以一种可靠、高效、可伸缩的方式进行处理的，它具有以下几个方面的特性：

* 高可靠性
* 高效性
* 高可扩展性
* 高容错性
* 成本低
* 运行在Linux平台上
* 支持多种编程语言

### Hadoop的应用现状： <a id="Hadoop&#x7684;&#x5E94;&#x7528;&#x73B0;&#x72B6;&#xFF1A;"></a>

* Hadoop凭借其突出的优势，已经在各个领域得到了广泛的应用，而互联网领域是其应用的主阵地
* 2007年，雅虎在Sunnyvale总部建立了M45——一个包含了4000个处理器和1.5PB容量的Hadoop集群系统
* Facebook作为全球知名的社交网站，Hadoop是非常理想的选择，Facebook主要将Hadoop平台用于日志处理、推荐系统和数据仓库等方面
* 国内采用Hadoop的公司主要有百度、淘宝、网易、华为、中国移动等，其中，淘宝的Hadoop集群比较大

**Hadoop在企业中的应用架构：**

![Hadoop&#x5728;&#x4F01;&#x4E1A;&#x4E2D;&#x7684;&#x5E94;&#x7528;&#x67B6;&#x6784;](https://cshihong.github.io/2018/04/07/Hadoop%E5%9F%BA%E7%A1%80%E6%8A%80%E6%9C%AF%E6%A6%82%E8%BF%B0/Hadoop%E5%9C%A8%E4%BC%81%E4%B8%9A%E4%B8%AD%E7%9A%84%E5%BA%94%E7%94%A8%E6%9E%B6%E6%9E%84.jpg)

图：Hadoop在企业中的应用架构

### Hadoop版本演变： <a id="Hadoop&#x7248;&#x672C;&#x6F14;&#x53D8;&#xFF1A;"></a>

* Apache Hadoop版本分为两代，我们将第一代Hadoop称为Hadoop 1.0，第二代Hadoop称为Hadoop 2.0。
* 第一代Hadoop包含三个大版本，分别是0.20.x，0.21.x和0.22.x，其中，0.20.x最后演化成1.0.x，变成了稳定版，而0.21.x和0.22.x则增加了NameNode HA等新的重大特性。
* 第二代Hadoop包含两个版本，分别是0.23.x和2.x，它们完全不同于Hadoop 1.0，是一套全新的架构，均包含HDFS Federation和YARN两个系统，相比于0.23.x，2.x增加了NameNode HA和Wire-compatibility两个重大特性。

**选择 Hadoop版本的考虑因素：**

* 是否开源（即是否免费）
* 是否有稳定版
* 是否经实践检验
* 是否有强大的社区支持

### Hadoop项目结构： <a id="Hadoop&#x9879;&#x76EE;&#x7ED3;&#x6784;&#xFF1A;"></a>

![hodoop&#x9879;&#x76EE;&#x7ED3;&#x6784;](https://cshihong.github.io/2018/04/07/Hadoop%E5%9F%BA%E7%A1%80%E6%8A%80%E6%9C%AF%E6%A6%82%E8%BF%B0/hodoop%E9%A1%B9%E7%9B%AE%E7%BB%93%E6%9E%84.jpg)

图：Hadoop项目结构

| 组件 | 功能 |
| :--- | :--- |
| HDFS | 分布式文件系统 |
| MapReduce | 分布式并行编程模型 |
| YARN | 资源管理和调度器 |
| Tez | 运行在YARN之上的下一代Hadoop查询处理框架 |
| Hive | Hadoop上的数据仓库 |
| HBase | Hadoop上的非关系型的不是数据库 |
| Pig | 一个基于Hadoop的大规模数据分析平台，提供类似SQL的查询语言Pig Latin |
| Sqoop | 用于在Hadoop与传统数据库之间进行数据传递 |
|  | Hadoop上的工作流管理系统 |
| Zookeeper | 提供分布式协调一致性服务 |
|  | 流计算框架 |
| Flume | 一个高可用，高可靠，分布式的海量日志采集、聚合和传输的系统 |
| Ambari | Hadoop快速部署工具，支持Apache Hadoop集群的供应、管理和监控 |
| Kafka | 一种高吞吐量的根不是发布订阅消息系统，可以处理消费者规模的网络的所有动作流数据 |
| Spark | 类似于Hadoop MapReduce的通用并行框架 |

## 大数据主要存储技术介绍 <a id="&#x5927;&#x6570;&#x636E;&#x4E3B;&#x8981;&#x5B58;&#x50A8;&#x6280;&#x672F;&#x4ECB;&#x7ECD;"></a>

### HDFS简介： <a id="HDFS&#x7B80;&#x4ECB;&#xFF1A;"></a>

HDFS（Hadoop Distributed File System）基于谷歌发布的GFS\(Google File System\)论文设计开发，其除具备其他分布式文件系统相同的特性外，还有自己特有的特性：

* **高容错**：认为硬件总是不可靠的。
* **高吞吐量**：为有大量数据访问的应用提供高吞吐量的支持。
* **大文件存储**：支持存储TB-PB级别的数据。
* HDFS适合用于：大文件存储，流失数据访问。
* HDFS不适合用于：大量小文件处理，随机写入，低延迟读写。

### YARN简介： <a id="YARN&#x7B80;&#x4ECB;&#xFF1A;"></a>

Yarn是Hadoop2.0中的资源管理系统，它是一个通用的资源管理模块，可为各类应用程序进行资源管理和调度。可支持MapReduce，Spark，Storm等多种框架。

![yarn](https://cshihong.github.io/2018/04/07/Hadoop%E5%9F%BA%E7%A1%80%E6%8A%80%E6%9C%AF%E6%A6%82%E8%BF%B0/yarn.png)

**Yarn的优势有：**

1. 资源利用率高
2. 运维成本低
3. 数据共享方便

### MapReduce简介： <a id="MapReduce&#x7B80;&#x4ECB;&#xFF1A;"></a>

MapReduce是基于Google发布的分布式计算框架MapReduce论文设计开发，用于大规模数据集（大于1TB）的并行计算。

MapReduce的特点：

1. **易于编程**：开发者仅需编写简单的程序，系统的执行框架会处理细节。
2. **良好的扩展性**：可以通过添加主机以达到扩展集群能力的目的。
3. **高容错性**：通过开算迁移或者数据迁移提高集群的可用性与容错性。

* MapReduce适合用于：大规模数据离线批处理，子任务相对独立。
* MapReduce不适合用于：实时交互计算，流失计算、实时分析，子任务相互依赖。

### Hive简介： <a id="Hive&#x7B80;&#x4ECB;&#xFF1A;"></a>

Hive提供数据提取、转换、加载功能，并可用类似于SQL的语法，对HDFS好了数据库中的数据进行查询统计等操作。

Hive可用于：

1. 数据汇总：每天/每周用户点击数，点击排行。
2. 非实时分析：日子分析，统计分析。
3. 数据挖掘：用户行为分析，兴趣分析、区域展示。

### HBase简介： <a id="HBase&#x7B80;&#x4ECB;&#xFF1A;"></a>

> HBase是一个分布式的、面向列的开源数据库，该技术来源于 Fay Chang 所撰写的Google论文“Bigtable：一个结构化数据的分布式存储系统”。就像Bigtable利用了Google文件系统（File System）所提供的分布式数据存储一样，HBase在Hadoop之上提供了类似于Bigtable的能力。HBase是Apache的Hadoop项目的子项目。HBase不同于一般的关系数据库，它是一个适合于非结构化数据存储的数据库。另一个不同的是HBase基于列的而不是基于行的模式。

HBase–Hadoop Datebase，是一个**高可靠性、高性能、面向列、可伸缩的分布式存储系统**，利用HBase技术可以在廉价的PC Server上搭建起大规模结构化存储集群。

**HBase的用途：**

1. 海量的数据存储
2. 准实时查询

**HBase的特点：**

1. 容量大

   HBase单表可以有上百亿行、百万列，数据矩阵横向和纵向两个维度所支持的数据量级都非常具有弹性。

2. 面向列

   HBase是面向列的存储和权限控制，并支持独立检索。列式存储，其数据在表中是按照某列存储的，这样在查询只需要少数几个字段的时候，能大大减少读取的数据量。

3. 多版本

   HBase每一个列的数据存储有多个Version（version）。

4. 稀疏性

   为空的列并不占用存储空间，表可以设计的非常稀疏。

5. 扩展性

   底层依赖于HDFS

6. 高可靠性

   WAL机制保证了数据写入时不会因集群异常而导致写入数据丢失：Replication机制保证了在集群出现严重的问题时，数据不会发生丢失或损坏。而且HBase底层使用HDFS，HDFS本身也有备份。

7. 高性能

   底层的LSM数据结构和Rowkey有序排列等架构上的独特设计，使得HBase具有非常高的写入性能。region切分、主键索引和缓存机制使得HBase在海量数据下具备一定的随机读取性能，该性能针对Rowkey的查询能够达到毫秒级别。

### Spark简介： <a id="Spark&#x7B80;&#x4ECB;&#xFF1A;"></a>

Spark是基于内存计算的大数据分布式计算框架。Spark基于内存计算，提高了在大数据环境下数据处理的实时性，同时保证了高容错性和高可伸缩性，允许用户将Spark部署在大量廉价硬件之上，形成集群。

Spark系统是分布式批处理系统和分析挖掘引擎。Spark可以用来快速处理数据，并支持迭代计算，有效应对多步的数据处理逻辑。

![Spark](https://cshihong.github.io/2018/04/07/Hadoop%E5%9F%BA%E7%A1%80%E6%8A%80%E6%9C%AF%E6%A6%82%E8%BF%B0/Spark.png)

图：Spark

**Spark特点：**

1. 提供分布式计算功能，将分布式存储的数据读入，同时将任务分发到各个节点进行计算；
2. 基于内存计算，将磁盘数据读入内存，将计算的中间结果保存在内存，这样可以很好的进行迭代运算；
3. 支持高容错；
4. 提供多计算范式

### Flume简介： <a id="Flume&#x7B80;&#x4ECB;&#xFF1A;"></a>

Flume是由Cloudera公司**开源**的，**分布式可靠**，**高可用**的海量日志聚和的系统，支持在系统中定制各类数据发送方，用于收集数据。同时，Flume提供对数据进行简单处理，并写到各种数据接收方（可定制）的能力。

### Kafka简介： <a id="Kafka&#x7B80;&#x4ECB;&#xFF1A;"></a>

Kafka是Linkedin开源的分布式的，基于发布/订阅的日志系统。Kafka可以在消息队列中保存大量的开销很想的数据，且支持大量的消费者订阅。

## Hadoop电仪应用场景 <a id="Hadoop&#x7535;&#x4EEA;&#x5E94;&#x7528;&#x573A;&#x666F;"></a>

### 离线统计分析： <a id="&#x79BB;&#x7EBF;&#x7EDF;&#x8BA1;&#x5206;&#x6790;&#xFF1A;"></a>

将海量的原始数据存储到HDFS中，定期离线做汇总统计，按分钟、手机号、地域、业务类型等维度导出到OLAP系统用于分析或报表。

![&#x79BB;&#x7EBF;&#x7EDF;&#x8BA1;&#x5206;&#x6790;](https://cshihong.github.io/2018/04/07/Hadoop%E5%9F%BA%E7%A1%80%E6%8A%80%E6%9C%AF%E6%A6%82%E8%BF%B0/%E7%A6%BB%E7%BA%BF%E7%BB%9F%E8%AE%A1%E5%88%86%E6%9E%90.png)

图：离线数据分析示例

### 详单查询： <a id="&#x8BE6;&#x5355;&#x67E5;&#x8BE2;&#xFF1A;"></a>

将海量的原始XDR，加载如库并转换位半结构化的格式，用于低延时查询。

![&#x8BE6;&#x5355;&#x67E5;&#x8BE2;](https://cshihong.github.io/2018/04/07/Hadoop%E5%9F%BA%E7%A1%80%E6%8A%80%E6%9C%AF%E6%A6%82%E8%BF%B0/%E8%AF%A6%E5%8D%95%E6%9F%A5%E8%AF%A2.png)

图：详单查询示例

### 云化ETL： <a id="&#x4E91;&#x5316;ETL&#xFF1A;"></a>

移动数据业务和流量的爆炸式增长，带来了网络建设和维护费用的成倍增加。要求将海量数据存储在分布式存储且能够进行汇总等计算。

![&#x4E91;&#x5316;ETL](https://cshihong.github.io/2018/04/07/Hadoop%E5%9F%BA%E7%A1%80%E6%8A%80%E6%9C%AF%E6%A6%82%E8%BF%B0/%E4%BA%91%E5%8C%96ETL.png)

图：云化ETL示例

