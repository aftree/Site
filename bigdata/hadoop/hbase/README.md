# Hbase



## HBase基本介绍 <a id="HBase&#x57FA;&#x672C;&#x4ECB;&#x7ECD;"></a>

### BigTable简介： <a id="BigTable&#x7B80;&#x4ECB;&#xFF1A;"></a>

BigTable是一个分布式存储系统，BigTable起初用于解决典型的互联网搜索问题。

* BigTable是一个分布式存储系统。
* 利用谷歌提出的MapReduce分布式并行计算模型来处理海量数据。
* 使用谷歌分布式文件系统GFS作为底层数据存储。
* 采用Chubby提供协同服务管理。
* 可以扩展到PB级别的数据和上千台机器，具备广泛应用性、可扩展性、高性能和高可用性等特点。
* 谷歌的许多项目都存储在BigTable中，包括搜索、地图、财经。

### HBase简介： <a id="HBase&#x7B80;&#x4ECB;&#xFF1A;"></a>

HBase是一个高可靠、高性能、面向列、可伸缩的分布式数据库，是谷歌BigTable的开源实现，主要用来存储非结构化和半结构化的松散数据。HBase的目标是处理非常庞大的表，可以通过水平扩展的方式，利用廉价计算机集群处理由超过10亿行数据和数百万列元素组成的数据表。

![hadoop&#x5173;&#x7CFB;](https://cshihong.github.io/2018/05/17/HBase%E6%8A%80%E6%9C%AF%E5%8E%9F%E7%90%86/hadoop%E5%85%B3%E7%B3%BB.png)

图：Hadoop生态系统中HBase与其他部分的关系

* 适合用于大表数据（表的规模可以达到数十亿行以及数百万列），并且对大表数据的读写访问可以达到实时级别。
* 利用Hadoop HDFS作为其文件存储系统，提供实时读写的分布式数据库系统。
* 利用ZooKeeper作为协同服务。

### HBase和BigTable的底层技术对应关系： <a id="HBase&#x548C;BigTable&#x7684;&#x5E95;&#x5C42;&#x6280;&#x672F;&#x5BF9;&#x5E94;&#x5173;&#x7CFB;&#xFF1A;"></a>

|  | BIgTable | hbase |
| :--- | :--- | :--- |
|  | GFS | HDFS |
|  | MapReduce | Hadoop MapReduce |
|  | Chubby | Zookeeper |

### HBase出现的原因： <a id="HBase&#x51FA;&#x73B0;&#x7684;&#x539F;&#x56E0;&#xFF1A;"></a>

关系数据库已经流行很多年，并且Hadoop已经有了HDFS和MapReduce，为什么需要HBase?

* Hadoop可以很好地解决大规模数据的离线批量处理问题，但是，受限于Hadoop MapReduce编程框架的高延迟数据处理机制，使得Hadoop无法满足大规模数据实时处理应用的需求。
* HDFS面向批量访问模式，不是随机访问模式。
* 传统的通用关系型数据库无法应对在数据规模剧增时导致的系统扩展性和性能问题（分库分表也不能很好解决）。
* 传统关系数据库在数据结构变化时一般需要停机维护；空列浪费存储空间。
* 因此，业界出现了一类面向半结构化数据存储和处理的高可扩展、低写入/查询延迟的系统，例如，键值数据库、文档数据库和列族数据库（如BigTable和HBase等）。
* HBase已经成功应用于互联网服务领域和传统行业的众多在线式数据分析处理系统中。

### HBase与传统关系数据库对比分析： <a id="HBase&#x4E0E;&#x4F20;&#x7EDF;&#x5173;&#x7CFB;&#x6570;&#x636E;&#x5E93;&#x5BF9;&#x6BD4;&#x5206;&#x6790;&#xFF1A;"></a>

HBase与传统的关系数据库的区别主要体现在以下几个方面：

1. 数据类型：关系数据库采用关系模型，具有丰富的数据类型和存储方式，HBase则采用了更加简单的数据模型，它把数据存储为未经解释的字符串。
2. 数据操作：关系数据库中包含了丰富的操作，其中会涉及复杂的多表连接。HBase操作则不存在复杂的表与表之间的关系，只有简单的插入、查询、删除、清空等，因为HBase在设计上就避免了复杂的表和表之间的关系。
3. 存储模式：关系数据库是基于行模式存储的。HBase是基于列存储的，每个列族都由几个文件保存，不同列族的文件是分离的。
4. 数据索引：关系数据库通常可以针对不同列构建复杂的多个索引，以提高数据访问性能。HBase只有一个索引——行键，通过巧妙的设计，HBase中的所有访问方法，或者通过行键访问，或者通过行键扫描，从而使得整个系统不会慢下来。
5. 数据维护：在关系数据库中，更新操作会用最新的当前值去替换记录中原来的旧值，旧值被覆盖后就不会存在。而在HBase中执行更新操作时，并不会删除数据旧的版本，而是生成一个新的版本，旧有的版本仍然保留。
6. 可伸缩性：关系数据库很难实现横向扩展，纵向扩展的空间也比较有限。相反，HBase和BigTable这些分布式数据库就是为了实现灵活的水平扩展而开发的，能够轻易地通过在集群中增加或者减少硬件数量来实现性能的伸缩

### HBase应用场景： <a id="HBase&#x5E94;&#x7528;&#x573A;&#x666F;&#xFF1A;"></a>

HBase适合具有如下需求的应用：

* 海量数据（TB，PB）。
* 不需要完全拥有传统关系型数据库所具备的ACID特性。
* 高吞吐量。
* 需要在海量数据中实现高效的随机读取。
* 需要很好的性能伸缩性。
* 能够同时处理结构化和非结构化数据。

### HBase访问接口： <a id="HBase&#x8BBF;&#x95EE;&#x63A5;&#x53E3;&#xFF1A;"></a>

![&#x8BBF;&#x95EE;&#x5373;&#x53EF;](https://cshihong.github.io/2018/05/17/HBase%E6%8A%80%E6%9C%AF%E5%8E%9F%E7%90%86/%E8%AE%BF%E9%97%AE%E5%8D%B3%E5%8F%AF.png)

### HBase在FusionInsight中的位置： <a id="HBase&#x5728;FusionInsight&#x4E2D;&#x7684;&#x4F4D;&#x7F6E;&#xFF1A;"></a>

HBase作为一个高可靠性、高性能、面向列、可伸缩的分布式数据库，提供海量数据存储功能，用来解决关系型数据库在海量数据时的局限性。

![&#x4F4D;&#x7F6E;](https://cshihong.github.io/2018/05/17/HBase%E6%8A%80%E6%9C%AF%E5%8E%9F%E7%90%86/%E4%BD%8D%E7%BD%AE.png)

图： HBase在FusionInsight中的位置

## HBase数据模型 <a id="HBase&#x6570;&#x636E;&#x6A21;&#x578B;"></a>

### 数据模型概述： <a id="&#x6570;&#x636E;&#x6A21;&#x578B;&#x6982;&#x8FF0;&#xFF1A;"></a>

* HBase是一个稀疏、多维度、排序的映射表，这张表的索引是行键、列族、列限定符和时间戳。
* 每个值是一个未经解释的字符串，没有数据类型。
* 用户在表中存储数据，每一行都有一个可排序的行键和任意多的列。
* 表在水平方向由一个或者多个列族组成，一个列族中可以包含任意多个列，同一个列族里面的数据存储在一起。
* 列族支持动态扩展，可以很轻松地添加一个列族或列，无需预先定义列的数量以及类型，所有列均以字符串形式存储，用户需要自行进行数据类型转换。
* HBase中执行更新操作时，并不会删除数据旧的版本，而是生成一个新的版本，旧有的版本仍然保留（这是和**HDFS只允许追加不允许修改的特性**相关的）。

### 数据模型相关概念： <a id="&#x6570;&#x636E;&#x6A21;&#x578B;&#x76F8;&#x5173;&#x6982;&#x5FF5;&#xFF1A;"></a>

* 表：HBase采用表来组织数据，表由行和列组成，列划分为若干个列族。
* 行：每个HBase表都由若干行组成，每个行由行键（row key）来标识。
* 列族：一个HBase表被分组成许多“列族”（Column Family）的集合，它是基本的访问控制单元。
* 列限定符：列族里的数据通过列限定符（或列）来定位。
* 单元格：在HBase表中，通过行、列族和列限定符确定一个“单元格”（cell），单元格中存储的数据没有数据类型，总被视为字节数组byte\[\]。
* 时间戳：每个单元格都保存着同一份数据的多个版本，这些版本采用时间戳进行索引。

![&#x793A;&#x4F8B;](https://cshihong.github.io/2018/05/17/HBase%E6%8A%80%E6%9C%AF%E5%8E%9F%E7%90%86/%E7%A4%BA%E4%BE%8B.png)

图：表示例

如上图：该单元格有2个时间戳ts1和ts2，每个时间戳对应一个数据版本。

### 数据坐标： <a id="&#x6570;&#x636E;&#x5750;&#x6807;&#xFF1A;"></a>

HBase中需要根据行键、列族、列限定符和时间戳来确定一个单元格，因此，可以视为一个“四维坐标”，即\[行键, 列族, 列限定符, 时间戳\]。

![&#x793A;&#x4F8B;2](https://cshihong.github.io/2018/05/17/HBase%E6%8A%80%E6%9C%AF%E5%8E%9F%E7%90%86/%E7%A4%BA%E4%BE%8B2.png)

图：数据表示法示例

### 概念视图： <a id="&#x6982;&#x5FF5;&#x89C6;&#x56FE;&#xFF1A;"></a>

![&#x6982;&#x5FF5;&#x89C6;&#x56FE;](https://cshihong.github.io/2018/05/17/HBase%E6%8A%80%E6%9C%AF%E5%8E%9F%E7%90%86/%E6%A6%82%E5%BF%B5%E8%A7%86%E5%9B%BE.png)

图：HBase数据的概念视图

### 物理视图: <a id="&#x7269;&#x7406;&#x89C6;&#x56FE;"></a>

![&#x7269;&#x7406;&#x89C6;&#x56FE;](https://cshihong.github.io/2018/05/17/HBase%E6%8A%80%E6%9C%AF%E5%8E%9F%E7%90%86/%E7%89%A9%E7%90%86%E8%A7%86%E5%9B%BE.png)

图：HBase数据的物理视图

### 面向列的存储： <a id="&#x9762;&#x5411;&#x5217;&#x7684;&#x5B58;&#x50A8;&#xFF1A;"></a>

![&#x9762;&#x5411;&#x5217;](https://cshihong.github.io/2018/05/17/HBase%E6%8A%80%E6%9C%AF%E5%8E%9F%E7%90%86/%E9%9D%A2%E5%90%91%E5%88%97.png)

图：夯实数据库和列式数据库示意图

**行存储**：数据按行存储在底层文件系统中。通常，每一行会被分配固定的空间。

* 优点：有利于增加、修改整行记录等操作；有理由数据的读取操作。
* 缺点：单列查询时，会读取一些不必要的数据。

**列存储：**数据以列为单位，存储在底层文件系统中。

* 优点：有利于面向单列数据的读取、统计等操作。
* 缺点：整行读取时，可能需要多次I/O操作。

### KeyValue存储模型： <a id="KeyValue&#x5B58;&#x50A8;&#x6A21;&#x578B;&#xFF1A;"></a>

![keyvalue](https://cshihong.github.io/2018/05/17/HBase%E6%8A%80%E6%9C%AF%E5%8E%9F%E7%90%86/keyvalue.png)

图：KeyValue存储模型

* KeyValue具有特性的结构。Key部分被用来快速检索一条数据记录，Value部分用来存储实际的用户数据信息。
* KeyValue作为承载用户数据的基本单元，需要保存一些对自身的描述信息。例如，时间戳，类型等等。那么势必会有一定的结构化空间开销。
* 支持动态增加列，容易适应数据类型和结构的变化。以块为单元操作数据，列间、表间并无关联关系。
* KeyValue型数据库数据分区方式–按Key值连续范围分区。如下图：

  ![key&#x5206;&#x533A;](https://cshihong.github.io/2018/05/17/HBase%E6%8A%80%E6%9C%AF%E5%8E%9F%E7%90%86/key%E5%88%86%E5%8C%BA.png)

* 数据按照RowKey的范围（按RowKey的字典顺序），划分为一个个的子区间。每一个子区间都是一个分布式存储的基本单元。
* **HBase的底层数据以KeyValue的形式存在，KeyValue具有特定的格式。**
* KeyValue中拥有时间戳、类型等关键信息。
* 同一个Key值可以关联多个Value，每一个KeyValue都拥有一个Qualifier标识。
* 即使是Key值相同，Qualifier也相同的多个KeyValue，也可能有多个，此时使用时间戳来区分，这就是同一条数据记录的多版本。

## HBase实现原理 <a id="HBase&#x5B9E;&#x73B0;&#x539F;&#x7406;"></a>

### HBase功能组件： <a id="HBase&#x529F;&#x80FD;&#x7EC4;&#x4EF6;&#xFF1A;"></a>

HBase的实现包括三个主要的功能组件：

1. 库函数：链接到每个客户端
2. 一个Master主服务器
3. 许多个Region服务器

主服务器Master负责管理和维护HBase表的分区信息，维护Region服务器列表，分配Region，负载均衡。

Region服务器负责存储和维护分配给自己的Region，处理来自客户端的读写请求。

客户端并不是直接从Master主服务器上读取数据，而是在获得Region的存储位置信息后，直接从Region服务器上读取数据。

客户端并不依赖Master，而是通过Zookeeper来获得Region位置信息，大多数客户端甚至从来不和Master通信，这种设计方式使得Master负载很小。

### Region基本概念： <a id="Region&#x57FA;&#x672C;&#x6982;&#x5FF5;&#xFF1A;"></a>

![Region](https://cshihong.github.io/2018/05/17/HBase%E6%8A%80%E6%9C%AF%E5%8E%9F%E7%90%86/Region.png)

图：Region的来源

* 将一个数据表按Key值范围横向划分为一个个的子表，实现分布式存储。
* 这个子表，在HBase中被称作“Region”。
* 每一个Region都关联一个Key值范围，即一个使用StartKey和EndKey描述的区间，事实上，每一个Region仅仅记录StartKey就可以，因为它的EndKey就是下一个Region的StartKey。
* **Region是HBase分布式存储的最基本单元**。

![Region&#x5206;&#x7C7B;](https://cshihong.github.io/2018/05/17/HBase%E6%8A%80%E6%9C%AF%E5%8E%9F%E7%90%86/Region%E5%88%86%E7%B1%BB.png)

图：Region分类

* Region分为元数据Region以及用户Region两类。
* Meta Region记录了每一个User Region的路由信息。
* 读写Region数据的路由，包括如下几步：
  1. 寻找Meta Region地址。
  2. 再由Meta Region寻找User Region地址。

### Column Family\(列族\)： <a id="Column-Family-&#x5217;&#x65CF;-&#xFF1A;"></a>

![&#x5217;&#x65CF;](https://cshihong.github.io/2018/05/17/HBase%E6%8A%80%E6%9C%AF%E5%8E%9F%E7%90%86/%E5%88%97%E6%97%8F.png)

图：列族

ColumnFamily是Region的一个物理存储单元。同一个Region下面的多个ColumnFamily，位于不同的路径下面。

ColumnFamily信息是表级别的配置。也就是说，同一个表的多个Region，都拥有相同的ColumnFamily信息。（例如，都有两个ColumnFamily，且不同Region的同一个ColumnFamily配置信息相同）。

### 表和Region： <a id="&#x8868;&#x548C;Region&#xFF1A;"></a>

开始只有一个Region，后来不断分裂Region拆分操作非常快，接近瞬间，因为拆分之后的Region读取的仍然是原存储文件，直到“合并”过程把存储文件异步地写到独立的文件之后，才会读取新文件。

每个Region默认大小是100MB到200MB（2006年以前的硬件配置）

* 每个Region的最佳大小取决于单台服务器的有效处理能力。
* 目前每个Region最佳大小建议1GB-2GB（2013年以后的硬件配置）。

同一个Region不会被分拆到多个Region服务器.

每个Region服务器存储10-1000个Region.

### Region的定位: <a id="Region&#x7684;&#x5B9A;&#x4F4D;"></a>

* 元数据表

  又名.META.表，存储了Region和Region服务器的映射关系.用来帮助Client定位到具体的Region。

  当HBase表很大时，元数据也会被切分为多个Regina，Region的元数据信息保存在Zookeeper中。

* 根数据表，又名-ROOT-表，记录所有元数据的具体位置。
* -ROOT-表只有唯一一个Region，名字是在程序中被写死的。
* Zookeeper文件记录了-ROOT-表的位置。

![HBase&#x7684;&#x4E09;&#x5C42;&#x7ED3;&#x6784;](https://cshihong.github.io/2018/05/17/HBase%E6%8A%80%E6%9C%AF%E5%8E%9F%E7%90%86/HBase%E7%9A%84%E4%B8%89%E5%B1%82%E7%BB%93%E6%9E%84.png)

图：HBase的三层结构

HBase的三层结构中各层次的名称和作用：

1. 第一层：Zookeeper文件，记录以-ROOt-表的位置信息。
2. 第二层：-ROOT-表，记录了.META.表的Region位置信息。-ROOT-表只能一个Region。通过-ROOT-表，就可以访问.META。表中的数据。
3. 第三层：.META.表，记录了用户数据表的Region位置息，.META.表可以有多个Region，保存了HBase中所有用户数据表的Region位置信息。

**为了加快访问速度，.META.表的全部Region都会被保存在内存中。**

假设.META.表的每行（一个映射条目）在内存中大约占用1KB，并且每个Region限制为128MB，那么，上面的三层结构可以保存的用户数据表的Region数目的计算方法是：

1. -ROOT-表能够寻址的.META.表的Region个数）×（每个.META.表的 Region可以寻址的用户数据表的Region个数）
2. 一个-ROOT-表最多只能有一个Region，也就是最多只能有128MB，按照每行（一个映射条目）占用1KB内存计算，128MB空间可以容纳128MB/1KB=**2的17次方** 行。

   行，也就是说，一个-ROOT-表可以寻址**2的17次方**个.META.表的Region。

3. 同理，每个.META.表的 Region可以寻址的用户数据表的Region个数是128MB/1KB=**2的17次方**。
4. 最终，三层结构可以保存的Region数目是\(128MB/1KB\) × \(128MB/1KB\) = **2的34次方**个Region。

客户端访问数据时的“三级寻址”:

* 为了加速寻址，客户端会缓存位置信息，同时，需要解决缓存失效问题.
* 寻址过程客户端只需要询问Zookeeper服务器，不需要连接Master服务器.

### Zookeeper为HBase提供： <a id="Zookeeper&#x4E3A;HBase&#x63D0;&#x4F9B;&#xFF1A;"></a>

1. 分布式锁的服务：

   多个HMaster进程都会尝试着去Zookeeper中写入一个对应的节点，该结点只能被一个HMaster进程创建成功，创建成功的HMaster进程就是Active。

2. 事件监听机制：

   主HMaster进程宕掉之后，备HMaster在监听对应的Zookeeper节点。主HMaster进程宕掉之后，该节点会被删除，其他的备HMaste就可以收到响应的消息。

3. 微型数据库角色：

   Zookeeper中存放了Region Server的地址，此时，可以将它理解成一个微型数据库。

## HBase系统架构 <a id="HBase&#x7CFB;&#x7EDF;&#x67B6;&#x6784;"></a>

### HBase系统架构： <a id="HBase&#x7CFB;&#x7EDF;&#x67B6;&#x6784;&#xFF1A;"></a>

![HBase&#x7CFB;&#x7EDF;&#x67B6;&#x6784;](https://cshihong.github.io/2018/05/17/HBase%E6%8A%80%E6%9C%AF%E5%8E%9F%E7%90%86/HBase%E7%B3%BB%E7%BB%9F%E6%9E%B6%E6%9E%84.png)

图：HBase系统架构图

各组件功能：

1. 客户端 客户端包含访问HBase的接口，同时在缓存中维护着已经访问过的Region位置信息，用来加快后续数据访问过程。
2. Zookeeper服务器

   Zookeeper可以帮助选举出一个Master作为集群的总管，并保证在任何时刻总有唯一一个Master在运行，这就避免了Master的“单点失效”问题 。

   Zookeeper是一个很好的集群管理工具，被大量用于分布式计算，提供配置维护、域名服务、分布式同步、组服务等。

3. Master（HMaster）  
   主服务器Master主要负责表和Region的管理工作：  
   – 管理用户对表的增加、删除、修改、查询等操作。  
   – 实现不同Region服务器之间的负载均衡。  
   – 在Region分裂或合并后，负责重新调整Region的分布。  
   – 对发生故障失效的Region服务器上的Region进行迁移。

   **-HMaster进程负责所有Region的转移操作：**

   1. **新表创建时的Region分配。**
   2. **运行期间的负载均衡保证。**
   3. **RegionServer Failover后的Region接管。**

   HMaster进程有主备角色。集群可以配置两个HMaster角色，集群启动时，这些HMaster角色通过竞争获得主HMaster角色。主HMaster只能有一个，备HMaster进程在集群运行期间处于休眠状态，不干涉任何集群事务。

4. RegionServer：

   **Region服务器是HBase中最核心的模块**，是HBase的数据服务进程负责处理用户的数据的读写请求。

   Region由RegionServer管理。所有用户数据的读写请求，都是和RegionServer上的Region进行交互。

   **Region可以在RegionServer之间迁移。**

### RegionServer工作原理： <a id="RegionServer&#x5DE5;&#x4F5C;&#x539F;&#x7406;&#xFF1A;"></a>

\#\#\#RegionServer的架构：

![RegionServer](https://cshihong.github.io/2018/05/17/HBase%E6%8A%80%E6%9C%AF%E5%8E%9F%E7%90%86/RegionServer.png)

图：HBase的RegionServer架构

* Store:一个Region由一个或多个Store组成。每个Store对应图中的一个Column Family。
* MemStore：一个Store包含一个MemStore，MemStore缓存客户端向Region插入的数据。
* StoreFile：MemStore的数据Flush到HDFS后成为StoreFile。
* Hfile：Hfile定义了StoreFile在文件系统中的存储格式，它是当前HBase系统汇总StoreFile的具体实现。
* Hlog：HLog日志保证了当RegionServer故障的情况下用户写入的数据不丢失。

  RegionServer的多个Region共享一个相同的Hlog。

#### Region服务器Region服务器向HDFS文件系统中读写数据： <a id="Region&#x670D;&#x52A1;&#x5668;Region&#x670D;&#x52A1;&#x5668;&#x5411;HDFS&#x6587;&#x4EF6;&#x7CFB;&#x7EDF;&#x4E2D;&#x8BFB;&#x5199;&#x6570;&#x636E;&#xFF1A;"></a>

![Regionserver&#x5DE5;&#x4F5C;&#x539F;&#x7406;](https://cshihong.github.io/2018/05/17/HBase%E6%8A%80%E6%9C%AF%E5%8E%9F%E7%90%86/Regionserver%E5%B7%A5%E4%BD%9C%E5%8E%9F%E7%90%86.png)

图：Region服务器向HDFS文件系统中读写数据

\(1\)用户读写数据过程：

* 用户写入数据时，被分配到相应Region服务器去执行。
* 用户数据首先被写入到MemStore和Hlog中。
* 只有当操作写入Hlog之后，commit\(\)调用才会将其返回给客户端。
* 当用户读取数据时，Region服务器会首先访问MemStore缓存，如果找不到，再去磁盘上面的StoreFile中寻找。

（2）缓存的刷新：

* 系统会周期性地把MemStore缓存里的内容刷写到磁盘的StoreFile文件中，清空缓存，并在Hlog里面写入一个标记。
* 每次刷写都生成一个新的StoreFile文件，因此，每个Store包含多个StoreFile文件。
* 每个Region服务器都有一个自己的HLog 文件，每次启动都检查该文件，确认最近一次执行缓存刷新操作之后是否发生新的写入操作；如果发现更新，则先写入MemStore，再刷写到StoreFile，最后删除旧的Hlog文件，开始为用户提供服务。·

（3）StoreFile的合并（Compaction）：

* **Hfile文件数目越来越多，读取时延也越来越大。**
* 每次刷写都生成一个新的StoreFile，数量太多，影响查找速度。
* 调用Store.compact\(\)把多个合并成一个。
* 合并操作比较耗费资源，只有数量达到一个阈值才启动合并。

### Store工作原理： <a id="Store&#x5DE5;&#x4F5C;&#x539F;&#x7406;&#xFF1A;"></a>

* Store是Region服务器的核心。
* 多个StoreFile合并成一个。
* 单个StoreFile过大时，又触发分裂操作，1个父Region被分裂成两个子Region。

![Store](https://cshihong.github.io/2018/05/17/HBase%E6%8A%80%E6%9C%AF%E5%8E%9F%E7%90%86/Store.png)

图：StoreFile的合并和分裂过程

\#\#HLog工作原理：

* 分布式环境必须要考虑系统出错。HBase采用HLog保证系统恢复。
* HBase系统为每个Region服务器配置了一个HLog文件，它是一种预写式日志（Write Ahead Log）。
* 用户更新数据必须首先写入日志后，才能写入MemStore缓存，并且，直到MemStore缓存内容对应的日志已经写入磁盘，该缓存内容才能被刷写到磁盘。
* Zookeeper会实时监测每个Region服务器的状态，当某个Region服务器发生故障时，Zookeeper会通知Master。
* Master首先会处理该故障Region服务器上面遗留的HLog文件，这个遗留的HLog文件中包含了来自多个Region对象的日志记录。
* 系统会根据每条日志记录所属的Region对象对HLog数据进行拆分，分别放到相应Region对象的目录下，然后，再将失效的Region重新分配到可用的Region服务器中，并把与该Region对象相关的HLog日志记录也发送给相应的Region服务器。
* Region服务器领取到分配给自己的Region对象以及与之相关的HLog日志记录以后，会重新做一遍日志记录中的各种操作，把日志记录中的数据写入到MemStore缓存中，然后，刷新到磁盘的StoreFile文件中，完成数据恢复。
* 共用日志优点：提高对表的写操作性能；

  ​ 缺点：恢复时需要分拆日志。

### 写流程： <a id="&#x5199;&#x6D41;&#x7A0B;&#xFF1A;"></a>

（1）客户端发起写数据请求。

（2）定位Regoin。定位到需要写到的RegionServer，Region，Rowkey。

（3）数据分组：整个数据分组，涉及到两步。

1. “分篮子”操作：

   根据mete表找到标的Region信息，此时也得到了对应的RegionServer信息。

   根据rowkey，将数据写到指定的region中。

2. 每个RegionServer上的数据会一起发送。发送数据中，都是已经按照Region分好组了。

（4）往RegionServer发送请求。

* 利用HBase自身封装的RPC框架，来完成数据发送操作。
* 往多个RegionServer发送请求是并行的。
* 客户端发送完写数据请求后，会自动等待请求处理结果。
* 如果客户端没有捕获到任何的异常，则认为所有数据都已经被写入成功。如果全部写入失败，或者部分写入失败，客户端能过获知详细的失败key值列表。

（5）Region写数据流程。

1. 获取Region操作锁。（读写锁）
2. 一次获取各行行锁。
3. 写入到MemStore中。（一个内存排序集合）
4. 释放以获取的行锁。
5. 写数据到WAL中。（Write-Ahead-Log）
6. 释放Region锁。

> 既然是Write-Ahead-Log，为何先写内存再写WAL？
>
> 先写内存的原因：HBase提供了一个MVCC机制，来保障些数据阶段的数据可见性。写写MemStore再写WAL，是为了一些特殊场景下，内存中的数据能够更及时的可将。如果先写WAL失败的话，MemStore助攻的数据会被回滚。

![&#x5199;&#x6D41;&#x7A0B;](https://cshihong.github.io/2018/05/17/HBase%E6%8A%80%E6%9C%AF%E5%8E%9F%E7%90%86/%E5%86%99%E6%B5%81%E7%A8%8B.png)

* 将需要写入或删除的数据暂时保存在每个Region的内存中，即MemStore中。
* 写内存，避免多Region情形下带来的过多的分散IO操作。
* 数据在写入到MemStore之后，也会顺序写到HLog中，以保障数据的安全。

（6）Flush。

以下多个场景，会触发Memstore的Flush操作：

1. Region中的MemStore的总大小，达到了预设的Flush Size阈值。
2. MemStore占用内存的总量和RegionServer总内存的比值超出来了预设的阈值大小。
3. 当WALs中文件数量达到阈值时。
4. HBase定期刷新MemStore，默认周期为1小时。
5. 用户可以通过shell命令分别对一个表或者一个Region进行Flush。

（7）Compaction。

Compaction的目的，是为了减少同一个Region中的同一个ColumnFamily下面的小文件（HFile）数目，从而提升读取的性能。

Compaction分为Minor、Major两类：

* Minor：小范围的Compaction。有最少和最大文件数目的限制。通常会选择一些连续时间范围的小文件进行合并。
* Major：涉及该Region该ColumnFamily下面的所有HFile文件。
* Minor Compaction选取文件时，遵循一定的算法。

![Compaction](https://cshihong.github.io/2018/05/17/HBase%E6%8A%80%E6%9C%AF%E5%8E%9F%E7%90%86/Compaction.png)

图：Compaction操作示例

（8）Region Split。

* Region Split是指集群期间，某一个Region的大小超出了预设的阈值，则需要将该Region自动分裂成为两个REgion。
* **分裂过程中，被分裂的Region会暂停读写服务**。由于分裂过程中，父Region的数据文件并不会真正的分裂，而是仅仅通过在新的Region中创建引用文件的方式，来实现快速分裂。因此，Region暂停服务的时间比较短暂。
* 客户端册所缓存的父Region的路由信息需要被更新。

### 读流程： <a id="&#x8BFB;&#x6D41;&#x7A0B;&#xFF1A;"></a>

（1）客户端发起读数据请求：

1. Get操作在精准的Key值的情形下，读取单行用户数据。
2. Sacn操作时为了批量扫描限定KEy值范围的用户数据。

（2）定位Region。定位到该数据所在的RegionServer，Region，Rowkey。

（3）OpenScanner：OpenScanner过程中，会创建两种不同的Scanner来读取Hfile、MemStore的数据。

1. HFile 对应的Scanner为StoreFileScanner。
2. MemStore对应的Scanner为对应的MemStoreScanner。

（4）Next：

![next](https://cshihong.github.io/2018/05/17/HBase%E6%8A%80%E6%9C%AF%E5%8E%9F%E7%90%86/next.png)

* 每一个Scanner中，都有一个指针，指向接下来要读取的用户数据KeyValue是哪一个。
* 同一级的Scanner，被放在同一个优先级队列中。通过不断的对比每一个Scanner的指针所指向的KeyValue，将这些Scanner进行排序。
* 每一次next请求，都是从该优先级队列中，Poll出一个Scanner，然后，读取该Scanner的当前指针所指向的KeyValue即可。
* 每读一个Scanner，指针都会我那个下移一个KeyValue。而后，该Scanner被返还到队列中。如果已经读完，则直接关闭。

（5）Filter允许在Scan过程或只能怪，设定一定的过滤条件。符合条件的用户数据才返回。当前包含的一些典型的Filter有：

1. RowFilter
2. SingleColumnValueFilter
3. KeyOnlyFilter
4. FilterList

使用Filter时，可能会扫描大量的用户数据，才可以找到所期望的满足条件的数据。因此，一些场景下性能是不可预估的。

（6）BloomFilter：

* BloomFilter用来优化一些随机读取的场景，即Get场景。它可以被用来快速的判断一条用户数据在一个大的数据集合（该数据集合的大部分数据都没法加载到内存中）中是否存在。
* BloomFilter在判断一个数据是否存在时，拥有一定的误判率。但对于“用户数据XXX不存在”的判断结果是可信的。
* HBase的BLoomFilter的相关数据，被保存在HFile中。

![Bloom](https://cshihong.github.io/2018/05/17/HBase%E6%8A%80%E6%9C%AF%E5%8E%9F%E7%90%86/Bloom.png)

## HBase应用方案 <a id="HBase&#x5E94;&#x7528;&#x65B9;&#x6848;"></a>

### HBase实际应用中的性能优化方法： <a id="HBase&#x5B9E;&#x9645;&#x5E94;&#x7528;&#x4E2D;&#x7684;&#x6027;&#x80FD;&#x4F18;&#x5316;&#x65B9;&#x6CD5;&#xFF1A;"></a>

* **行键（Row Key）** 行键是按照字典序存储，因此，设计行键时，要充分利用这个排序特点，将经常一起读取的数据存储到一块，将最近可能会被访问的数据放在一块。 举个例子：如果最近写入HBase表中的数据是最可能被访问的，可以考虑将时间戳作为行键的一部分，由于是字典序排序，所以可以使用Long.MAX\_VALUE - timestamp作为行键，这样能保证新写入的数据在读取时可以被快速命中。
* **InMemory**

  创建表的时候，可以通过HColumnDescriptor.setInMemory\(true\)将表放到Region服务器的缓存中，保证在读取的时候被cache命中。

* **Max Version**

  创建表的时候，可以通过HColumnDescriptor.setMaxVersions\(int maxVersions\)设置表中数据的最大版本，如果只需要保存最新版本的数据，那么可以设置setMaxVersions\(1\)。

* **Time TO Live：**

  创建表的时候，可以通过HColumnDescriptor.setTimeToLive\(int timeToLive\)设置表中数据的存储生命期，过期数据将自动被删除，例如如果只需要存储最近两天的数据，那么可以设置setTimeToLive\(2 _24_ 60 \* 60\)。

### HBase性能监视： <a id="HBase&#x6027;&#x80FD;&#x76D1;&#x89C6;&#xFF1A;"></a>

#### Master-status\(自带\)： <a id="Master-status-&#x81EA;&#x5E26;-&#xFF1A;"></a>

HBase Master默认基于Web的UI服务端口为60010，HBase region服务器默认基于Web的UI服务端口为60030.如果master运行在名为master.foo.com的主机中，mater的主页地址就是[http://master.foo.com:60010，用户可以通过Web浏览器输入这个地址查看该页面可以查看HBase集群的当前状态。](http://master.foo.com:60010%EF%BC%8C%E7%94%A8%E6%88%B7%E5%8F%AF%E4%BB%A5%E9%80%9A%E8%BF%87Web%E6%B5%8F%E8%A7%88%E5%99%A8%E8%BE%93%E5%85%A5%E8%BF%99%E4%B8%AA%E5%9C%B0%E5%9D%80%E6%9F%A5%E7%9C%8B%E8%AF%A5%E9%A1%B5%E9%9D%A2%E5%8F%AF%E4%BB%A5%E6%9F%A5%E7%9C%8BHBase%E9%9B%86%E7%BE%A4%E7%9A%84%E5%BD%93%E5%89%8D%E7%8A%B6%E6%80%81%E3%80%82/)

#### Ganglia： <a id="Ganglia&#xFF1A;"></a>

Ganglia是UC Berkeley发起的一个开源集群监视项目，用于监控系统性能。

#### OpenTSDB： <a id="OpenTSDB&#xFF1A;"></a>

OpenTSDB可以从大规模的集群（包括集群中的网络设备、操作系统、应用程序）中获取相应的metrics并进行存储、索引以及服务，从而使得这些数据更容易让人理解，如web化，图形化等。

#### Ambari： <a id="Ambari&#xFF1A;"></a>

Ambari 的作用就是创建、管理、监视 Hadoop 的集群。

### 在HBase之上构建SQL引擎： <a id="&#x5728;HBase&#x4E4B;&#x4E0A;&#x6784;&#x5EFA;SQL&#x5F15;&#x64CE;&#xFF1A;"></a>

NoSQL区别于关系型数据库的一点就是NoSQL不使用SQL作为查询语言，至于为何在NoSQL数据存储HBase上提供SQL接口，有如下原因：

1. 易使用。使用诸如SQL这样易于理解的语言，使人们能够更加轻松地使用HBase。
2. 减少编码。使用诸如SQL这样更高层次的语言来编写，减少了编写的代码量。

   方案： 1.Hive整合HBase 2.Phoenix

**1.Hive整合HBase**

Hive与HBase的整合功能从Hive0.6.0版本已经开始出现，利用两者对外的API接口互相通信，通信主要依靠hive\_hbase-handler.jar工具包\(Hive Storage Handlers\)。由于HBase有一次比较大的版本变动，所以并不是每个版本的Hive都能和现有的HBase版本进行整合，所以在使用过程中特别注意的就是两者版本的一致性。

**2.Phoenix**

Phoenix由Salesforce.com开源，是构建在Apache HBase之上的一个SQL中间层，可以让开发者在HBase上执行SQL查询。

### 构建HBase二级索引： <a id="&#x6784;&#x5EFA;HBase&#x4E8C;&#x7EA7;&#x7D22;&#x5F15;&#xFF1A;"></a>

HBase只有一个针对行健的索引 访问HBase表中的行，只有三种方式：

1. 通过单个行健访问
2. 通过一个行健的区间来访问
3. 全表扫描

使用其他产品为HBase行健提供索引功能：

* Hindex二级索引
* HBase+Redis
* HBase+solr

原理：采用HBase0.92版本之后引入的Coprocessor特性。

#### **Coprocessor构建二级索引：** <a id="Coprocessor&#x6784;&#x5EFA;&#x4E8C;&#x7EA7;&#x7D22;&#x5F15;&#xFF1A;"></a>

Coprocessor（协处理器）提供了两个实现：endpoint和observer，endpoint相当于关系型数据库的存储过程，而observer则相当于触发器。

observer允许我们在记录put前后做一些处理，因此，而我们可以在插入数据时同步写入索引表。

优点： 非侵入性：引擎构建在HBase之上，既没有对HBase进行任何改动，也不需要上层应用做任何妥协。

缺点：每插入一条数据需要向索引表插入数据，即耗时是双倍的，对HBase的集群的压力也是双倍的。

![&#x4E8C;&#x7EA7;&#x7D22;&#x5F15;](https://cshihong.github.io/2018/05/17/HBase%E6%8A%80%E6%9C%AF%E5%8E%9F%E7%90%86/%E4%BA%8C%E7%BA%A7%E7%B4%A2%E5%BC%95.png)

图：二级索引示意图

\#\#\#Hindex:

Hindex 是华为公司开发的纯 Java 编写的HBase二级索引，兼容 Apache HBase 0.94.8。当前的特性如下：

* 多个表索引
* 多个列索引
* 基于部分列值的索引

#### HBase+Redis: <a id="HBase-Redis"></a>

* Redis+HBase方案
* Coprocessor构建二级索引
* Redis做客户端缓存
* 将索引实时更新到Redis等KV系统中，定时从KV更新索引到HBase的索引表中.

![Hbase+Redis](https://cshihong.github.io/2018/05/17/HBase%E6%8A%80%E6%9C%AF%E5%8E%9F%E7%90%86/Hbase+Redis.png)

图：HBase+Redis二级索引

#### HBase+solr: <a id="HBase-solr"></a>

Solr是一个高性能，采用Java5开发，基于Lucene的全文搜索服务器。同时对其进行了扩展，提供了比Lucene更为丰富的查询语言，同时实现了可配置、可扩展并对查询性能进行了优化，并且提供了一个完善的功能管理界面，是一款非常优秀的全文搜索引擎。

![Solr](https://cshihong.github.io/2018/05/17/HBase%E6%8A%80%E6%9C%AF%E5%8E%9F%E7%90%86/Solr.png)

图：HBase+Solr

## HBase华为增强特性 <a id="HBase&#x534E;&#x4E3A;&#x589E;&#x5F3A;&#x7279;&#x6027;"></a>

### 支持二级索引： <a id="&#x652F;&#x6301;&#x4E8C;&#x7EA7;&#x7D22;&#x5F15;&#xFF1A;"></a>

二级索引为HBase提供了按照某些列的值进行索引的能力。

![&#x7D22;&#x5F15;](https://cshihong.github.io/2018/05/17/HBase%E6%8A%80%E6%9C%AF%E5%8E%9F%E7%90%86/%E7%B4%A2%E5%BC%95.png)

### HFS（HBase FileStream）： <a id="HFS&#xFF08;HBase-FileStream&#xFF09;&#xFF1A;"></a>

HBase文件存储模块（HBase FileStream，简称HFS）是HBase的独立模块，它作为对HBase与HDFS接口的封装，应用在FusionInsight的上层应用，为上层应用提供文件的存储、读取、删除等功能。

HFS的出现解决了需要在HDFS中存储海量小文件，同时也要存储一些大文件的混合的场景。简单来说，就是在HBase表中，需要存放大量的小文件（10MB以下），同时又需要存放一些比较大的文件（10MB以上。）

### HBase MOB： <a id="HBase-MOB&#xFF1A;"></a>

MOB数据（即100KB到10MB大小的数据）直接以HFile的格式存储在文件系统上（例如HDFS文件系统），然后把这个文件的地址信息及大小信息作为value存储在普通HBase的store上，通过攻击集中管理这些文件。这样就可以大大降低HBase的compation和split频率，提升性能。

> 以

