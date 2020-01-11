# Hive



## [https://cshihong.github.io/2018/06/19/Hive%E5%B8%B8%E7%94%A8Shell%E6%93%8D%E4%BD%9C%E5%92%8C%E5%9F%BA%E7%A1%80%E5%BC%80%E5%8F%91/](https://cshihong.github.io/2018/06/19/Hive%E5%B8%B8%E7%94%A8Shell%E6%93%8D%E4%BD%9C%E5%92%8C%E5%9F%BA%E7%A1%80%E5%BC%80%E5%8F%91/) <a id="Hive&#x6982;&#x8FF0;"></a>

## Hive概述 <a id="Hive&#x6982;&#x8FF0;"></a>

### 数据仓库的概念： <a id="&#x6570;&#x636E;&#x4ED3;&#x5E93;&#x7684;&#x6982;&#x5FF5;&#xFF1A;"></a>

数据仓库（Data Warehouse）是一个面向主题的（Subject Oriented）、集成的（Integrated）、相对稳定的（Non-Volatile）、反映历史变化（Time Variant）的数据集合，用于支持管理决策。

### 传统数据仓库面临的挑战： <a id="&#x4F20;&#x7EDF;&#x6570;&#x636E;&#x4ED3;&#x5E93;&#x9762;&#x4E34;&#x7684;&#x6311;&#x6218;&#xFF1A;"></a>

1. 无法满足快速增长的海量数据存储需求。
2. 无法有效处理不同类型的数据。
3. 计算和处理能力不足。

### Hive简介： <a id="Hive&#x7B80;&#x4ECB;&#xFF1A;"></a>

* Hive是一个构建于Hadoop顶层的**数据仓库工具**，可以查询和管理PB级别的分布式数据。
* 支持大规模数据存储、分析，具有良好的可扩展性
* 某种程度上可以看作是**用户编程接口**，本身不存储和处理数据。
* 依赖分布式文件系统HDFS存储数据。
* 依赖分布式并行计算模型MapReduce处理数据。
* 定义了简单的类似SQL 的查询语言——HiveQL。
* 用户可以通过编写的HiveQL语句运行MapReduce任务。
* 可以很容易把原来构建在关系数据库上的数据仓库应用程序移植到Hadoop平台上。
* 是一个可以提供有效、合理、直观组织和使用数据的分析工具。

Hive具有的特点非常适用于数据仓库。

**（1）采用批处理方式处理海量数据**

* Hive需要把HiveQL语句转换成MapReduce任务进行运行。
* 数据仓库存储的是静态数据，对静态数据的分析适合采用批处理方式，不需要快速响应给出结果，而且数据本身也不会频繁变化。

**（2）提供适合数据仓库操作的工具**

* Hive本身提供了一系列对数据进行**提取、转换、加载（ETL）**的工具，可以存储、查询和分析存储在Hadoop中的大规模数据。
* 这些工具能够很好地满足数据仓库各种应用场景。

**（3）支持MapReduce，Tez，Spark等多种计算引擎。**

**（4）可以直接访问HDFS文件以及HBase。**

**（5）易用易编程**。

### Hive与Hadoop生态系统中其他组件的关系： <a id="Hive&#x4E0E;Hadoop&#x751F;&#x6001;&#x7CFB;&#x7EDF;&#x4E2D;&#x5176;&#x4ED6;&#x7EC4;&#x4EF6;&#x7684;&#x5173;&#x7CFB;&#xFF1A;"></a>

![Hadoop](https://cshihong.github.io/2018/05/22/Hive%E6%8A%80%E6%9C%AF%E5%8E%9F%E7%90%86/Hadoop.png)

图：Hadoop生态系统

* Hive依赖于HDFS 存储数据
* Hive依赖于MapReduce 处理数据
* 在某些场景下Pig可以作为Hive的替代工具
* HBase 提供数据的实时访问

### Hive的优缺点： <a id="Hive&#x7684;&#x4F18;&#x7F3A;&#x70B9;&#xFF1A;"></a>

Hive的优点：

1. 高可靠、高容错：HiveServer采用集群模式。双MetaStor。超时重试机制。
2. 类SQL：类似SQL语法，内置大量函数。
3. 可扩展：自定义存储格式，自定义函数。
4. 多接口：Beeline，JDBC，ODBC，Python，Thrift。

Hive的缺点：

1. 延迟较高：默认MR为执行引擎，MR延迟较高。
2. 不支持雾化视图：Hive支持普通视图，不支持雾化视图。Hive不能再视图上更新、插入、删除数据。
3. 不适用OLTP：暂不支持列级别的数据添加、更新、删除操作。
4. 暂不支持存储过程：当前版本不支持存储过程，只能通过UDF来实现一些逻辑处理。

### Hive与传统数据库的对比分析： <a id="Hive&#x4E0E;&#x4F20;&#x7EDF;&#x6570;&#x636E;&#x5E93;&#x7684;&#x5BF9;&#x6BD4;&#x5206;&#x6790;&#xFF1A;"></a>

Hive在很多方面和传统的关系数据库类似，但是它的底层依赖的是HDFS和MapReduce，所以在很多方面又有别于传统数据库。

![&#x5BF9;&#x6BD4;1](https://cshihong.github.io/2018/05/22/Hive%E6%8A%80%E6%9C%AF%E5%8E%9F%E7%90%86/%E5%AF%B9%E6%AF%941.png)

![&#x5BF9;&#x6BD4;2](https://cshihong.github.io/2018/05/22/Hive%E6%8A%80%E6%9C%AF%E5%8E%9F%E7%90%86/%E5%AF%B9%E6%AF%942.png)

### Hive在企业中的部署和作用： <a id="Hive&#x5728;&#x4F01;&#x4E1A;&#x4E2D;&#x7684;&#x90E8;&#x7F72;&#x548C;&#x4F5C;&#x7528;&#xFF1A;"></a>

#### Hive在企业大数据分析平台中的应用： <a id="Hive&#x5728;&#x4F01;&#x4E1A;&#x5927;&#x6570;&#x636E;&#x5206;&#x6790;&#x5E73;&#x53F0;&#x4E2D;&#x7684;&#x5E94;&#x7528;&#xFF1A;"></a>

![Hive](https://cshihong.github.io/2018/05/22/Hive%E6%8A%80%E6%9C%AF%E5%8E%9F%E7%90%86/Hive.png)

图：企业中一种常见的大数据分析平台部署框架

#### Hive在Facebook公司中的应用： <a id="Hive&#x5728;Facebook&#x516C;&#x53F8;&#x4E2D;&#x7684;&#x5E94;&#x7528;&#xFF1A;"></a>

* 基于Oracle的数据仓库系统已经无法满足激增的业务需求
* Facebook公司开发了数据仓库工具Hive，并在企业内部进行了大量部署

#### Hive在FusionInsight中的位置： <a id="Hive&#x5728;FusionInsight&#x4E2D;&#x7684;&#x4F4D;&#x7F6E;&#xFF1A;"></a>

![&#x4F4D;&#x7F6E;](https://cshihong.github.io/2018/05/22/Hive%E6%8A%80%E6%9C%AF%E5%8E%9F%E7%90%86/%E4%BD%8D%E7%BD%AE.png)

图：Hive在FusionInsight中的位置

Hive是一种数据仓库处理工具，使用类SQL的HiveQL语言实现数据查询功能，所有的Hive数据都存储在HDFS中。

### Hive应用场景： <a id="Hive&#x5E94;&#x7528;&#x573A;&#x666F;&#xFF1A;"></a>

1. 数据挖掘：用户行为分析；兴趣分区；区域展示；
2. 非实时分析：日志分析；文本分析。
3. 数据汇总：每天/每周用户点击数，流量统计。
4. 数据仓库：数据抽取，加载，转换（ETL）。

## Hive功能与架构 <a id="Hive&#x529F;&#x80FD;&#x4E0E;&#x67B6;&#x6784;"></a>

### Hive系统架构： <a id="Hive&#x7CFB;&#x7EDF;&#x67B6;&#x6784;&#xFF1A;"></a>

![&#x67B6;&#x6784;](https://cshihong.github.io/2018/05/22/Hive%E6%8A%80%E6%9C%AF%E5%8E%9F%E7%90%86/%E6%9E%B6%E6%9E%84.png)

![Hive&#x67B6;&#x6784;](https://cshihong.github.io/2018/05/22/Hive%E6%8A%80%E6%9C%AF%E5%8E%9F%E7%90%86/Hive%E6%9E%B6%E6%9E%84.png)

图：Hive系统架构

用户接口模块包括CLI、HWI、JDBC、ODBC、Thrift Server。

驱动模块（Driver）包括编译器、优化器、执行器等，负责把HiveSQL语句转换成一系列MapReduce作业。

元数据存储模块（Metastore）是一个独立的关系型数据库（自带derby数据库，或MySQL数据库）。

### FusionInsight HD中Hive的架构： <a id="FusionInsight-HD&#x4E2D;Hive&#x7684;&#x67B6;&#x6784;&#xFF1A;"></a>

![Fusion](https://cshihong.github.io/2018/05/22/Hive%E6%8A%80%E6%9C%AF%E5%8E%9F%E7%90%86/Fusion.png)

图：FusionInsight中Hive的架构

Hive分为三个角色：HiveServer、MetaStore、WebHcat。

* HiveServer：将用户提交的HQL语句进行编译，解析成对应的Yarn任务，Spark任务或者HDFS操作，从而完成数据的提取，转换，分析。
* MetaStroe：提供元数据服务。
* WebHcat：对外提供基于Htpps洗衣的元数据访问、DDL查询等服务。

### HCatalog架构： <a id="HCatalog&#x67B6;&#x6784;&#xFF1A;"></a>

![HCatalog](https://cshihong.github.io/2018/05/22/Hive%E6%8A%80%E6%9C%AF%E5%8E%9F%E7%90%86/HCatalog.png)

图：HCatalog架构

HCatalog包括Hcatalog Client和Hcatalog Server：

* HCatalog CLient包括命令行工具CLI和Clent jar包（用于给Pig， MR提供元数据读写支持）。
* HCatalog通过Hive提供的HiveMetaStoreClent对象来间接访问MetaStore。
* HCatalog对外提供Hcatloader，HCatinputFormat来读取数据；提供HCatStore,HCatOutputFormat来写入数据。

### WebHCat架构： <a id="WebHCat&#x67B6;&#x6784;&#xFF1A;"></a>

![WebHCat&#x67B6;&#x6784;](https://cshihong.github.io/2018/05/22/Hive%E6%8A%80%E6%9C%AF%E5%8E%9F%E7%90%86/WebHCat%E6%9E%B6%E6%9E%84.png)

图：WebHCat架构

WebHCat提供Rest接口，是用户能够通过安全的HTTPS协议执行以下操作：

1. 执行Hive DDL操作。
2. 运行Hive HQL任务。
3. 运行MapReduce任务。

### Hive数据存储模型： <a id="Hive&#x6570;&#x636E;&#x5B58;&#x50A8;&#x6A21;&#x578B;&#xFF1A;"></a>

![&#x6570;&#x636E;&#x5B58;&#x50A8;&#x6A21;&#x578B;](https://cshihong.github.io/2018/05/22/Hive%E6%8A%80%E6%9C%AF%E5%8E%9F%E7%90%86/%E6%95%B0%E6%8D%AE%E5%AD%98%E5%82%A8%E6%A8%A1%E5%9E%8B.png)

图：Hive数据存储模型

**分区：数据表可以按照某个字段的值划分分区。**

* 每个分区是一个目录。
* 分区数量不固定。
* 分区下可再有分区或者桶。

**桶：数据可以根据桶的方式将不同数据放入不同的桶中。**

* 每个桶是一个文件。
* 建表时指定桶个数，桶内可排序。
* 数据按照某个字段的值Hash后放入某个桶中。

**Hive可以创建托管表和外部表：**

* 默认创建托管表，Hiva会将数据移动到数据仓库的目录。
* 创建外部表，这时Hiva会到仓库目录以外的位置访问数据。
* 如果所有处理都由Hive完成，建议使用托管表。
* 如果要用Hive和其他工具来处理同一个数据集，建议使用外部表。
* ![&#x533A;&#x522B;](https://cshihong.github.io/2018/05/22/Hive%E6%8A%80%E6%9C%AF%E5%8E%9F%E7%90%86/%E5%8C%BA%E5%88%AB.png)

**Hive支持的函数：**

Hive内置函数：

1. 数据函数：如round\(\),fllor\(\), abs\(\), rand\(\)等。
2. 日期函数：如to\_date\(\), month\(\), day\(\).
3. 字符串函数,如trim\(\), length\(\), substr\(\)等。

UDF（User-Defined Funcation）用户自定义函数。

### Hive工作原理： <a id="Hive&#x5DE5;&#x4F5C;&#x539F;&#x7406;&#xFF1A;"></a>

#### （1）SQL语句转换成MapReduce作业的基本原理： <a id="&#xFF08;1&#xFF09;SQL&#x8BED;&#x53E5;&#x8F6C;&#x6362;&#x6210;MapReduce&#x4F5C;&#x4E1A;&#x7684;&#x57FA;&#x672C;&#x539F;&#x7406;&#xFF1A;"></a>

![jion](https://cshihong.github.io/2018/05/22/Hive%E6%8A%80%E6%9C%AF%E5%8E%9F%E7%90%86/jion.png)

图：join的实现原理

![group](https://cshihong.github.io/2018/05/22/Hive%E6%8A%80%E6%9C%AF%E5%8E%9F%E7%90%86/group.png)

图：group by实现原理

存在一个分组（Group By）操作，其功能是把表Score的不同片段按照rank和level的组合值进行合并，计算不同rank和level的组和值分别有几条记录。

#### （2）Hive中SQL查询转换成MapReduce作用的过程： <a id="&#xFF08;2&#xFF09;Hive&#x4E2D;SQL&#x67E5;&#x8BE2;&#x8F6C;&#x6362;&#x6210;MapReduce&#x4F5C;&#x7528;&#x7684;&#x8FC7;&#x7A0B;&#xFF1A;"></a>

当用户向Hive输入一段命令或查询时，Hive需要与Hadoop交互工作来完成该操作：

1. 驱动模块接收该命令或查询编译器
2. 对该命令或查询进行解析编译
3. 由优化器对该命令或查询进行优化计算
4. 该命令或查询通过执行器进行执行

详细如果如下：

![&#x8FC7;&#x7A0B;](https://cshihong.github.io/2018/05/22/Hive%E6%8A%80%E6%9C%AF%E5%8E%9F%E7%90%86/%E8%BF%87%E7%A8%8B.png)

图：SQL查询转换成MapReduce作业的过程

1. 第1步：由Hive驱动模块中的编译器对用户输入的SQL语言进行词法和语法解析，将SQL语句转化为抽象语法树的形式。
2. 第2步：抽象语法树的结构仍很复杂，不方便直接翻译为MapReduce算法程序，因此，把抽象语法书转化为查询块。
3. 第3步：把查询块转换成逻辑查询计划，里面包含了许多逻辑操作符。
4. 第4步：重写逻辑查询计划，进行优化，合并多余操作，减少MapReduce任务数量。
5. 第5步：将逻辑操作符转换成需要执行的具体MapReduce任务。
6. 第6步：对生成的MapReduce任务进行优化，生成最终的MapReduce任务执行计划。
7. 第7步：由Hive驱动模块中的执行器，对最终的MapReduce任务进行执行输出。

**几点说明：**

* 当启动MapReduce程序时，Hive本身是不会生成MapReduce算法程序的。
* 需要通过一个表示“Job执行计划”的XML文件驱动执行内置的、原生的Mapper和Reducer模块。
* Hive通过和JobTracker通信来初始化MapReduce任务，不必直接部署在JobTracker所在的管理节点上执行。
* 通常在大型集群上，会有专门的网关机来部署Hive工具。网关机的作用主要是远程操作和管理节点上的JobTracker通信来执行任务。
* 数据文件通常存储在HDFS上，HDFS由名称节点管理。

\#Hive增强特性

### Hive增强特性-Colocation： <a id="Hive&#x589E;&#x5F3A;&#x7279;&#x6027;-Colocation&#xFF1A;"></a>

Colocation（同分布）：将存在关联关系的数据或可能进行管理操作的数据存储在相同的存储节点上。

文件级同分布实现文件的快速访问，避免了因数据搬迁带来的大量网络开销。

### Hive增强特性–Hbase记录批量删除： <a id="Hive&#x589E;&#x5F3A;&#x7279;&#x6027;&#x2013;Hbase&#x8BB0;&#x5F55;&#x6279;&#x91CF;&#x5220;&#x9664;&#xFF1A;"></a>

在Hive on HBase功能汇总，FusionInsight HD Hive提供了对HBase表的单条数据的删除功能，通过特定的语法，Hive可以将HBase表中符合条件的一条或多条数据批量清除。

### Hive增强特性–流控特性： <a id="Hive&#x589E;&#x5F3A;&#x7279;&#x6027;&#x2013;&#x6D41;&#x63A7;&#x7279;&#x6027;&#xFF1A;"></a>

通过流控特性，可以实现：

* 当前已经建立连接数据阈值控制。
* 每个用户已经建立的连接数阈值控制。
* 单位时间内所有建立的连接数阈值控制。

> 以上

