# Loader



## Loader简介 <a id="Loader&#x7B80;&#x4ECB;"></a>

### 什么是Loader： <a id="&#x4EC0;&#x4E48;&#x662F;Loader&#xFF1A;"></a>

Loader是实现FusionInsight HD与关系型数据库、文件系统之间交互数据和文件的数据加载工具。基于开源Sqoop研发，做了大量优化和扩展。提供可视化向导式的作业配置管理界面；提供定时调度任务，周期性执行Loader作业；在界面中可指定多种不同的数据源、配置数据的清洗和转换步骤、配置集群存储系统等。

### Loader的特点： <a id="Loader&#x7684;&#x7279;&#x70B9;&#xFF1A;"></a>

* 图形化：提供图形化配置、监控界面，操作简便。
* 高性能：利用MapReduce并行处理数据。
* 高可靠：Loader Server采用主备双机；作业通过MapReduce执行，支持失败重试；作业失败后，不会残留数据。
* 安全：Kerberos认证；作业权限管理。

### Loader的应用场景： <a id="Loader&#x7684;&#x5E94;&#x7528;&#x573A;&#x666F;&#xFF1A;"></a>

![&#x5E94;&#x7528;&#x573A;&#x666F;](https://cshihong.github.io/2018/06/01/Loader%E6%8A%80%E6%9C%AF%E5%8E%9F%E7%90%86/%E5%BA%94%E7%94%A8%E5%9C%BA%E6%99%AF.png)

图：Loader的应用场景

通过Loader，我们可以从关系型数据库或文件系统中把数据导入HBase或者Hive，HDFS中。反过来，Loader也可以从HDFS和HBase、Hive中导出数据。

### Loader在FusionInsight产品中的位置： <a id="Loader&#x5728;FusionInsight&#x4EA7;&#x54C1;&#x4E2D;&#x7684;&#x4F4D;&#x7F6E;&#xFF1A;"></a>

![&#x4F4D;&#x7F6E;](https://cshihong.github.io/2018/06/01/Loader%E6%8A%80%E6%9C%AF%E5%8E%9F%E7%90%86/%E4%BD%8D%E7%BD%AE.png)

图：Loader在FusionInsight中的位置

FusionInsight HD提供大数据处理环境，基于社区开源软件增强，安装场景选择业界最佳实践；Porter是FusionInsight HD的数据集成服务，提供与Hadoop集群多种交换数据方式（包括Loader，Flume，SFTP）及Hadoop图形界面（Hue）。

**Loader是实现FusionInsight HD与关系型数据库、文件系统之间交换数据和文件的数据加载工具。**

## Loader系统架构 <a id="Loader&#x7CFB;&#x7EDF;&#x67B6;&#x6784;"></a>

### Loader模块架构： <a id="Loader&#x6A21;&#x5757;&#x67B6;&#x6784;&#xFF1A;"></a>

![&#x67B6;&#x6784;](https://cshihong.github.io/2018/06/01/Loader%E6%8A%80%E6%9C%AF%E5%8E%9F%E7%90%86/%E6%9E%B6%E6%9E%84.png)

图：Loader模块架构图

模块说明：

| 名称 | 描述 |
| :--- | :--- |
| Loader Client | Loader的客户端，包括WebUI和CLI两种交互界面。 |
| Loader Server | Loader的服务端，主要功能包括：处理客户端请求，管理连接器和元数据，提交MapReduce作业和监控MapReduce作业状态等。 |
| REST API | 实现RESTful（HTTP+JSON）接口，处理来自客户端的请求。 |
| Job Scheduler | 简单的作业调度模块，支持周期ing的执行Loader作业。 |
| Transform Engine | 数据转换处理引擎，支持字段合并、字符串剪切、字符串反序等。 |
| Execution Engine | Loader作业执行引擎，包含MapReduce作业的详细处理逻辑。 |
| Submission Engine | Loader作业提交引擎，支持将作业提交给MapReduce执行。 |
| Job Manager | 管理Loader作业，包括创建作业、查询作业、更新作业、删除作业、激活作业、去激活作业、启动作业、停止作业。 |
| Metadata Repository | 元数据仓库，存储和管理Loader的连接器、转换步骤、作业等数据。 |
| HA Manager | 管理Loader Server进程的主备状态，Loader Server包含2个节点，以主备方式部署。 |

## Loader作业管理 <a id="Loader&#x4F5C;&#x4E1A;&#x7BA1;&#x7406;"></a>

### 作业： <a id="&#x4F5C;&#x4E1A;&#xFF1A;"></a>

​ **作业用来描述将数据从数据源经过抽取、转换和加载至目的端的过程**。包括数据源位置及数据源属性、从源数据到目标数据的转换规则、目标端属性。

​ Loader提供了诸多功能，用于管理与作业相关的操作。包括创建作业、导入作业、导出作业、迁移作业分组、批量删除作业、启动作业、停止作业、查看作业历史记录、复制作业和删除指定作业等功能。

> 脏数据：是指不符和Loader转换规则的数据。

### 作业转换规则： <a id="&#x4F5C;&#x4E1A;&#x8F6C;&#x6362;&#x89C4;&#x5219;&#xFF1A;"></a>

​ Loader提供了丰富的作用转换规则，能将数据按照不同的业务场景进行转换和清洗，转换成目标数据结构，实际应用中，如果不需要转换，可以不指定转换规则。

Loader提供了14中转换算子，描述如下：

1. 长整型时间转换：实现长整型数值与日期类型的互换。
2. 空值转换：将空值替换成指定值。
3. 增加常量字段：生成常量字段。
4. 随机值转换：生成羧基数据字段。
5. 拼接转换：拼接已有字段，生成新字段。
6. 分割转换：将已有字段，按指定分隔符，分割出新字段。
7. 取模转换：对已有字段取模，生成新字段。
8. 剪切字符串：通过指定起止位置，截取已有字符串类型的字段，生成新字段。
9. EL操作转换：指定算法，对字段值进行运算，目前支持的算法有：MD5sum、sha1sum、sha256sum和sha512sum等。
10. 字符串大小写转换：对已有的字符串类型字典，切换大小写，生成新字段。
11. 字符串逆序转换：对已有的字符串类型字段，做逆序变换，生成新字段。
12. 字符串空格清除转换：对已有的字符串类型字段，清除左右空格，生成新字段。
13. 过滤行转换：配置逻辑条件过滤掉含触发条件的行。
14. 更新域：当满足某些条件时，更新字段的值。

### 客户端脚本介绍： <a id="&#x5BA2;&#x6237;&#x7AEF;&#x811A;&#x672C;&#x4ECB;&#x7ECD;&#xFF1A;"></a>

Loader除了提供图形化操作界面外，还体用了一套完整的shell脚本，通过这些脚本，可实现数据源的增删查改，作业的增删查改、启动作业、停止作业，查看作业状态，判断作业是否正在运行等功能。

脚本介绍如下：

* lt-ctl：简称作业控制工具，用于查询作业状态、启动作业，停止作业以及判断作业是否在运行中。
* lt-ucj：简称作业管理工具，用于查询、创建、修改和删除作业。
* lt-ucc：简称数据源管理工具，用于查询、创建、修改和删除数据源连接信息。

> 参考文档：华为培训文档

