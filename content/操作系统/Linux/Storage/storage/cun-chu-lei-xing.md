# 存储类型

原文：[https://yq.aliyun.com/articles/85619](https://yq.aliyun.com/articles/85619)

## 1.存储引擎

### 1.1 Hash Table <a id="2"></a>

#### 1.1.1 dbm \(database manager\) <a id="3"></a>

[https://en.wikipedia.org/wiki/Dbm](https://en.wikipedia.org/wiki/Dbm)

The dbm library stores arbitrary data by use of a single key \(a primary key\) in fixed-size buckets and uses hashing techniques to enable fast retrieval of the data by key.

### 1.2 btree <a id="4"></a>

#### 1.2.1 berkerlydb <a id="5"></a>

[https://en.wikipedia.org/wiki/Berkeley\_DB](https://en.wikipedia.org/wiki/Berkeley_DB)

[http://baike.baidu.com/view/1281930.htm](http://baike.baidu.com/view/1281930.htm)

Key/value数据模型

Berkeley DB最初开发的目的是以新的HASH访问算法来代替旧的hsearch函数和大量的dbm实现（如AT&T的dbm，Berkeley的 ndbm，GNU项目的gdbm）

oracle

Written in C, java

BTREE, HASH, QUEUE, RECNO storage

[https://www.oracle.com/database/berkeley-db/db.html](https://www.oracle.com/database/berkeley-db/db.html)

[http://www.oracle.com/technetwork/database/database-technologies/berkeleydb/overview/index.html](http://www.oracle.com/technetwork/database/database-technologies/berkeleydb/overview/index.html)

#### 1.2.2 LMDB \(Lightning Memory-Mapped Database\) <a id="6"></a>

LMDB is a Btree-based database management library modeled loosely on the BerkeleyDB API, but much simplified. The entire database is exposed in a memory map, and all data fetches return data directly from the mapped memory, so no malloc's or memcpy's occur during data fetches.

[https://symas.com/products/lightning-memory-mapped-database/](https://symas.com/products/lightning-memory-mapped-database/)

[https://github.com/LMDB/lmdb](https://github.com/LMDB/lmdb)

[http://www.lmdb.tech/doc/](http://www.lmdb.tech/doc/)

#### 1.2.3 BoltDB <a id="7"></a>

Bolt is a pure Go key/value store inspired by Howard Chu's LMDB project.

[https://github.com/boltdb](https://github.com/boltdb)

[https://github.com/boltdb/bolt](https://github.com/boltdb/bolt)

### 1.3 LSM <a id="8"></a>

#### 1.3.1 LevelDB <a id="9"></a>

[https://github.com/google/leveldb](https://github.com/google/leveldb)

C++, google

#### 1.3.2 RocksDB <a id="10"></a>

[http://rocksdb.org/](http://rocksdb.org/)

[https://github.com/facebook/rocksdb/](https://github.com/facebook/rocksdb/)

C++/java

#### 1.3.3GoLevelDB <a id="11"></a>

[https://github.com/syndtr/goleveldb](https://github.com/syndtr/goleveldb)

#### 1.3.4gorocksdb <a id="12"></a>

[https://github.com/tecbot/gorocksdb](https://github.com/tecbot/gorocksdb)

Go wrapper for RocksDB

#### 1.3.5 levigo <a id="13"></a>

Go wrapper for LevelDB

[https://github.com/jmhodges/levigo](https://github.com/jmhodges/levigo)

#### 1.3.6 mongo-rocks <a id="14"></a>

RocksDB Storage Engine Module for MongoDB

[https://github.com/mongodb-partners/mongo-rocks](https://github.com/mongodb-partners/mongo-rocks)

C++

### 1.4 LSH <a id="15"></a>

#### 1.4.1 bitcask <a id="16"></a>

[https://github.com/basho/bitcask](https://github.com/basho/bitcask)

采用bitcask模型的有：beandb, Riak

erlang

日志结构的key/value存储系统Bitcas

[http://blog.chinaunix.net/uid-20196318-id-154750.html](http://blog.chinaunix.net/uid-20196318-id-154750.html)

Bitcask存储模型

[http://blog.csdn.net/qq910894904/article/details/37756377](http://blog.csdn.net/qq910894904/article/details/37756377)

### 1.5 FractalTree <a id="17"></a>

#### 1.5.1 PerconaFT <a id="18"></a>

[https://github.com/percona/PerconaFT](https://github.com/percona/PerconaFT)

[https://www.percona.com/doc/percona-server-for-mongodb/perconaft.html](https://www.percona.com/doc/percona-server-for-mongodb/perconaft.html)

[https://github.com/percona/PerconaFT/wiki](https://github.com/percona/PerconaFT/wiki)

Mysql存储引擎之TokuDB以及它的数据结构Fractal tree\(分形树\)

[http://www.fxysw.com/thread-5061-1-1.html](http://www.fxysw.com/thread-5061-1-1.html)

[https://en.wikipedia.org/wiki/Fractal\_tree\_index](https://en.wikipedia.org/wiki/Fractal_tree_index)

TokuDB的索引结构–分形树的实现

[http://www.cnblogs.com/chaosheng/p/5250037.html](http://www.cnblogs.com/chaosheng/p/5250037.html)

### 1.6 dbm系列 <a id="19"></a>

#### 1.6.1 QDBM \(Quick DataBase Manager\) <a id="20"></a>

[http://sourceforge.net/projects/qdbm/](http://sourceforge.net/projects/qdbm/)

[http://qdbm.sourceforge.net/](http://qdbm.sourceforge.net/)

[http://qdbm.sourceforge.net/benchmark.pdf](http://qdbm.sourceforge.net/benchmark.pdf)

used by nmdb

#### 1.6.2 ndbm \(New Database Manager\) <a id="21"></a>

[https://en.wikipedia.org/wiki/Ndbm](https://en.wikipedia.org/wiki/Ndbm)

[http://infolab.stanford.edu/~ullman/dbsi/win98/ndbm.html](http://infolab.stanford.edu/~ullman/dbsi/win98/ndbm.html)

ndbm \(standing for New Database Manager\) is a Berkeley produced version from 1986 of the AT&T dbm database.

ndbm stores arbitrary data by use of a single key in fixed-size buckets.

#### 1.6.3 SDBM \(Substitute Database Manager\) <a id="22"></a>

Substitute Database Manager

[https://github.com/davidar/sdbm](https://github.com/davidar/sdbm)

[http://www.cse.yorku.ca/~oz/sdbm.bun](http://www.cse.yorku.ca/~oz/sdbm.bun)

#### 1.6.4 GDBM （GNU Database Manager） <a id="23"></a>

GNU Database Manager

[http://www.gnu.org.ua/software/gdbm/](http://www.gnu.org.ua/software/gdbm/)

#### 1.6.5 tdb \(Trivial Database\) <a id="24"></a>

Trivial Database

[http://sourceforge.net/projects/tdb/](http://sourceforge.net/projects/tdb/)

[http://tdb.sourceforge.net/](http://tdb.sourceforge.net/)

#### 1.6.6 CDB <a id="25"></a>

[http://cr.yp.to/cdb.html](http://cr.yp.to/cdb.html)

#### 1.6.7 TinyCDB <a id="26"></a>

Tiny Constant Database

[http://www.corpit.ru/mjt/tinycdb.html](http://www.corpit.ru/mjt/tinycdb.html)

### 1.7 双类型 <a id="27"></a>

#### 1.7.1 Wiredtiger \(btree, LSM\) <a id="28"></a>

C语言的

WiredTiger 本身也支持 LSM option \(默认是 btree \)

mangodb

[https://github.com/wiredtiger/wiredtiger](https://github.com/wiredtiger/wiredtiger)

[http://www.wiredtiger.com/](http://www.wiredtiger.com/)

[http://source.wiredtiger.com/2.8.0/architecture.html\#cache](http://source.wiredtiger.com/2.8.0/architecture.html#cache)

#### 1.7.2 Tokyo Cabinet and Kyoto Cabinet \(B+tree,hash table\) <a id="29"></a>

[http://baike.baidu.com/view/2640411.htm](http://baike.baidu.com/view/2640411.htm)

[http://fallabs.com/tokyocabinet/](http://fallabs.com/tokyocabinet/)

[https://en.wikipedia.org/wiki/Tokyo\_Cabinet\_and\_Kyoto\_Cabinet](https://en.wikipedia.org/wiki/Tokyo_Cabinet_and_Kyoto_Cabinet)

Tokyo Cabinet and Kyoto Cabinet are two libraries of routines for managing key-value databases.

Kyoto Cabinet is the designated successor of Tokyo Cabinet

Tokyo Cabinet 是一个DBM的实现

Tokyo Cabinet features on-disk B+ trees and hash tables for key-value storage

Used by nmdb，Kyoto TreeDB

#### 1.7.3 RaptorDB key value store \(B+ 树 或者 MurMur 哈希索引\) <a id="30"></a>

一个很小的、快速的嵌入式 NoSQL 存储模块，使用 B+ 树 或者 MurMur 哈希索引

Implemented in .NET

[http://raptordbkv.codeplex.com/](http://raptordbkv.codeplex.com/)

[http://www.codeproject.com/Articles/190504/RaptorDB](http://www.codeproject.com/Articles/190504/RaptorDB)

[http://www.codeproject.com/Articles/316816/RaptorDB-The-Key-Value-Store-V](http://www.codeproject.com/Articles/316816/RaptorDB-The-Key-Value-Store-V)

### 1.8 SQL引擎类 <a id="31"></a>

#### 1.8.1 InnoDB <a id="32"></a>

[http://www.oschina.net/p/innodb/](http://www.oschina.net/p/innodb/)

[http://baike.baidu.com/view/1238935.htm](http://baike.baidu.com/view/1238935.htm)

[https://en.wikipedia.org/wiki/InnoDB](https://en.wikipedia.org/wiki/InnoDB)

### 1.9 document类 <a id="33"></a>

#### 1.9.1 RaptorDB document store <a id="34"></a>

[http://raptordb.codeplex.com/](http://raptordb.codeplex.com/)

[http://www.codeproject.com/Articles/375413/RaptorDB-the-Document-Store](http://www.codeproject.com/Articles/375413/RaptorDB-the-Document-Store)

## 2 嵌入式 <a id="35"></a>

[https://en.wikipedia.org/wiki/Embedded\_database](https://en.wikipedia.org/wiki/Embedded_database)

### 2.1 SQLite <a id="36"></a>

[https://en.wikipedia.org/wiki/SQLite](https://en.wikipedia.org/wiki/SQLite)

### 2.2 UnQLite <a id="37"></a>

盘点移动开发中最流行的5个数据库

[http://www.evget.com/article/2014/11/21/21843.html](http://www.evget.com/article/2014/11/21/21843.html)

## 3 单机存储 <a id="38"></a>

## 3.1 单值KV存储 <a id="39"></a>

#### 3.1.1 Memcache <a id="40"></a>

#### 3.1.2 nmdb <a id="41"></a>

[https://blitiri.com.ar/p/nmdb/](https://blitiri.com.ar/p/nmdb/)

use qdbm, berkeley db, tokyo cabinet or tdb as database backends

C语言

#### 3.1.3 Memcachedb <a id="42"></a>

C语言,新浪

[http://memcachedb.org/](http://memcachedb.org/)

a distributed key-value storage system designed for persistent  
It conforms to memcache protocol  
uses Berkeley DB as a storing backend

write 18868 w/s  
read 44444 r/s

using replication for master/slave  
6 policy for replication:

[http://memcachedb.org/memcachedb-guide-1.0.pdf](http://memcachedb.org/memcachedb-guide-1.0.pdf)

#### 3.1.4 Kyoto Tycoon <a id="43"></a>

[http://fallabs.com/kyototycoon/](http://fallabs.com/kyototycoon/)

C/C++，FAL Labs

Kyoto Tycoon is a lightweight database server with auto expiration mechanism, which is useful to handle cache data and persistent data of various applications. Kyoto Tycoon is also a package of network interface to the DBM called Kyoto Cabinet.

#### 3.1.5 ThruDB <a id="44"></a>

建立在Apache Thrift framework下的简单服务

支持多个数据存储后端，包括BerkeleyDB、Disk、MySQL,还拥有Memcache和Spread集成

[http://code.google.com/p/thrudb/](http://code.google.com/p/thrudb/)

Thrudb is a set of simple services built on top of the Apache Thrift framework that provides indexing and document storage services

### 3.2 结构化KV存储 <a id="45"></a>

#### 3.2.1 Redis <a id="46"></a>

#### 3.2.2 ssdb <a id="47"></a>

[https://github.com/ideawu/ssdb](https://github.com/ideawu/ssdb)

[http://ssdb.io/zh\_cn/](http://ssdb.io/zh_cn/)

[http://ssdb.io/docs/replication.html](http://ssdb.io/docs/replication.html)

[http://ssdb.io/docs/config.html](http://ssdb.io/docs/config.html)

[https://github.com/ideawu/ssdb/blob/master/ssdb.conf](https://github.com/ideawu/ssdb/blob/master/ssdb.conf)

采用ssd，使用leveldb作为存储引擎，兼容redis接口

C/C++

#### 3.2.3 ssdb-rocks <a id="48"></a>

[https://github.com/ideawu/ssdb-rocks](https://github.com/ideawu/ssdb-rocks)

ssdb的另一个版本，采用ssd，使用rocksdb作为存储引擎，兼容redis接口

#### 3.3.4 ardb <a id="49"></a>

[https://github.com/yinqiwen/ardb](https://github.com/yinqiwen/ardb)

redis-protocol compatible persistent nosql, it support multiple storage engines as backend like Google's LevelDB, Facebook's RocksDB, OpenLDAP's LMDB, WiredTiger, the default backend is Facebook's RocksDB.

C++

[http://yinqiwen.github.io/ardb/2014/08/23/ardbintroduction/](http://yinqiwen.github.io/ardb/2014/08/23/ardbintroduction/)

#### 3.2.5 \(reborndb\)QDB <a id="50"></a>

兼容redis协议

Rocksdb and LevelDB

[https://github.com/reborndb/qdb](https://github.com/reborndb/qdb)

#### 3.2.6 Pika <a id="51"></a>

Pika 的存储引擎, 基于Rocksdb 修改. 封装Hash, List, Set, Zset等数据结构

[https://github.com/Qihoo360/pika](https://github.com/Qihoo360/pika)

[http://git.oschina.net/baotiao/pika](http://git.oschina.net/baotiao/pika)

[http://www.jianshu.com/p/d4f23120cbe4](http://www.jianshu.com/p/d4f23120cbe4)

首发丨360开源的类Redis存储系统:Pika

[https://mp.weixin.qq.com/s?\_\_biz=MzAwMDU1MTE1OQ==∣=2653547160&idx=1&sn=befd195e2aa788775aaf1cc3b6f6fab3&scene=1&srcid=0512FqLKcLjVNH0zbKVlVBSO&key=b28b03434249256b1da1489d74564ea1a9d5b15207160026adc2a6ce0622b47c084d23b75e909dfe14f6173662cbdf5b&ascene=0&uin=MjMxMzM3NjIyMw%3D%3D&devicetype=iMac+MacBookPro12%2C1+OSX+OSX+10.11.4+build\(15E65\)&version=11020201&pass\_ticket=WU1aAnx4aVkuID0Quq0HGuKNQB68CvjQzaTnnIhjJFZwLcPqGk1zilYX4uRvF9sd](https://mp.weixin.qq.com/s?__biz=MzAwMDU1MTE1OQ==&mid=2653547160&idx=1&sn=befd195e2aa788775aaf1cc3b6f6fab3&scene=1&srcid=0512FqLKcLjVNH0zbKVlVBSO&key=b28b03434249256b1da1489d74564ea1a9d5b15207160026adc2a6ce0622b47c084d23b75e909dfe14f6173662cbdf5b&ascene=0&uin=MjMxMzM3NjIyMw%3D%3D&devicetype=iMac+MacBookPro12%2C1+OSX+OSX+10.11.4+build%2815E65%29&version=11020201&pass_ticket=WU1aAnx4aVkuID0Quq0HGuKNQB68CvjQzaTnnIhjJFZwLcPqGk1zilYX4uRvF9sd)

首发丨360开源的类Redis存储系统:Pika

[https://media.weibo.cn/article?id=2309403974295628970629](https://media.weibo.cn/article?id=2309403974295628970629)

#### 3.2.7 LedisDB <a id="52"></a>

A high performance NoSQL like Redis powered by Go

LevelDB, goleveldb, LMDB, RocksDB, BoltDB or Memory

[http://ledisdb.com/](http://ledisdb.com/)

[https://github.com/siddontang/ledisdb](https://github.com/siddontang/ledisdb)

### 3.3 文档型 <a id="53"></a>

#### 3.3.1 SisoDB <a id="54"></a>

C\#编写的，专门提供给SQL Server面向文档的db-provider

[http://www.sisodb.com](http://www.sisodb.com/)

### 3.4 SQL <a id="55"></a>

#### 3.4.1 MySQL <a id="56"></a>

#### 3.4.2 innostore <a id="57"></a>

[https://github.com/basho/innostore](https://github.com/basho/innostore)

Innostore is a simple Erlang API to Embedded InnoDB

## 4 单机存储的proxy集群方案 <a id="58"></a>

### 4.1 KV/Redis类 <a id="59"></a>

#### 4.1.1 Twenproxy <a id="60"></a>

[http://www.oschina.net/p/twemproxy](http://www.oschina.net/p/twemproxy)

[https://github.com/twitter/twemproxy](https://github.com/twitter/twemproxy)

静态的分布式Redis方案

#### 4.1.2 Reborndb <a id="61"></a>

[https://github.com/reborndb](https://github.com/reborndb)

[https://github.com/reborndb/reborn/blob/master/doc/tutorial\_zh.md](https://github.com/reborndb/reborn/blob/master/doc/tutorial_zh.md)

#### 4.1.3 Codis <a id="62"></a>

[http://www.oschina.net/p/codis](http://www.oschina.net/p/codis)

[https://github.com/wandoulabs/codis](https://github.com/wandoulabs/codis)

[https://github.com/CodisLabs/codis](https://github.com/CodisLabs/codis)

#### 4.1.4 Netflix Dynomite <a id="63"></a>

[https://github.com/Netflix/dynomite](https://github.com/Netflix/dynomite)

基于dynamo的思想

Dynomite: NetFlix对dynamo的开源通用实现

[http://www.infoq.com/cn/news/2014/11/dynomite-netflix-dynamo](http://www.infoq.com/cn/news/2014/11/dynomite-netflix-dynamo)

Netflix open sources Dynomite to make any datastore distributed

[https://gigaom.com/2014/11/03/netflix-open-sources-dynomite-to-make-any-datastore-distributed/](https://gigaom.com/2014/11/03/netflix-open-sources-dynomite-to-make-any-datastore-distributed/)

A generic dynamo implementation for different k-v storage engines  
inspired by Dynamo whitepaper

#### 4.1.5 dbcached <a id="64"></a>

[http://code.google.com/p/dbcached/](http://code.google.com/p/dbcached/)

a distributed key-value memory caching system for QDBM or Berkeley DB base on Memcached and NMDB

### 4.2 SQL类 <a id="65"></a>

#### 4.2.1 Mycat <a id="66"></a>

[http://www.mycat.org.cn/](http://www.mycat.org.cn/)

MyCat：开源分布式数据库中间件  
[http://mp.weixin.qq.com/s?\_\_biz=MzAwNjMxNjQzNA==∣=208187004&idx=1&sn=60aba39c148711e95f00ec7ca2e13bb1&scene=0\#rd](http://mp.weixin.qq.com/s?__biz=MzAwNjMxNjQzNA==&mid=208187004&idx=1&sn=60aba39c148711e95f00ec7ca2e13bb1&scene=0#rd)

#### 4.2.2 MySQL Fabric <a id="67"></a>

#### 4.2.3 TDDL <a id="68"></a>

#### 4.2.4 Cobar <a id="69"></a>

#### 4.2.5 Atlas <a id="70"></a>

#### 4.2.6 Heisenberg <a id="71"></a>

#### 4.2.7 Vitess <a id="72"></a>

## 5 KV存储 <a id="73"></a>

### 5.1 riak <a id="74"></a>

[http://www.oschina.net/p/riak/](http://www.oschina.net/p/riak/)

[https://github.com/basho/riak](https://github.com/basho/riak)

采用bitcask模型

Riak的实现是基于Amazon的Dynamo论文

erlang

Riak是以 Erlang 编写的一个高度可扩展的分布式数据存储，Riak的实现是基于Amazon的Dynamo论文，Riak的设计目标之一就是高可用。

[http://docs.basho.com/riak/kv/2.2.3/](http://docs.basho.com/riak/kv/2.2.3/)

### 5.2 beandb <a id="75"></a>

[https://github.com/douban/beansdb](https://github.com/douban/beansdb)

[https://github.com/douban/beanseye](https://github.com/douban/beanseye)

采用bitcask模型

distributed key-value storage system

took the ideas from Amazon's Dynamo

### 5.3 Project Voldemort <a id="76"></a>

[http://www.project-voldemort.com/voldemort/](http://www.project-voldemort.com/voldemort/)

Voldemort is a distributed key-value storage system

[http://www.project-voldemort.com/voldemort/design.html](http://www.project-voldemort.com/voldemort/design.html)

[https://github.com/voldemort/voldemort](https://github.com/voldemort/voldemort)

BDB-JE, MySQL, Read-Only

LinkedIn

### 5.4 Scalaris <a id="77"></a>

[http://scalaris.zib.de/](http://scalaris.zib.de/)

分布式key value

erlang

supported the ACID properties for multi-key transactions

[http://code.google.com/p/scalaris/](http://code.google.com/p/scalaris/)

### 5.5 Aeospike <a id="78"></a>

www.aerospike.com

[http://www.aerospike.com/technologies/](http://www.aerospike.com/technologies/)

[http://www.aerospike.com/docs/](http://www.aerospike.com/docs/)

[http://www.aerospike.com/docs/architecture/index.html](http://www.aerospike.com/docs/architecture/index.html)

### 5.6 Tair <a id="79"></a>

[http://code.taobao.org/p/tair/](http://code.taobao.org/p/tair/)

[http://www.oschina.net/p/tair/](http://www.oschina.net/p/tair/)

[https://github.com/alibaba/tair](https://github.com/alibaba/tair)

c/c++, 自研的mdb,fdb

### 5.7 dynomite <a id="80"></a>

[https://github.com/moonpolysoft/dynomite/wiki](https://github.com/moonpolysoft/dynomite/wiki)

[http://www.oschina.net/p/dynomite/](http://www.oschina.net/p/dynomite/)

## 6 文档型存储 <a id="81"></a>

### 6.1 MongoDB <a id="82"></a>

### 6.2 CouchDB <a id="83"></a>

CouchDB是文档型存储

[http://couchdb.apache.org/](http://couchdb.apache.org/)

### 6.3 Membase / Couchbase <a id="84"></a>

[http://www.couchbase.com/](http://www.couchbase.com/)

[http://www.oschina.net/p/membase](http://www.oschina.net/p/membase)

[http://www.oschina.net/p/couchbase-server](http://www.oschina.net/p/couchbase-server)

[https://en.wikipedia.org/wiki/Couchbase\_Server](https://en.wikipedia.org/wiki/Couchbase_Server)

[http://www.couchbase.com/wiki/display/couchbase/Home](http://www.couchbase.com/wiki/display/couchbase/Home)

面向文档的 NoSQL 数据库管理系统

### 6.4 SequoiaDB <a id="85"></a>

一个类mongodb的文档型存储  
[http://www.sequoiadb.com/cn/](http://www.sequoiadb.com/cn/)

### 6.5 RavenDB <a id="86"></a>

[http://ravendb.net/](http://ravendb.net/)  
a .net document database built on the Windows ESENT storage system

支持Linq,可以使用C\#的Linq语法查询数据

### 6.6 OrientDB <a id="87"></a>

虽然是文档型数据库，但是它的关系管理方式却和图形数据库相类似

[http://www.orientechnologies.com/](http://www.orientechnologies.com/)

[http://orientdb.com/orientdb/](http://orientdb.com/orientdb/)

Distributed Graph Database with the flexibility of Documents

是兼具文挡数据库的灵活性和图形数据库管理链接 能力的可深层次扩展的文档-图形数据库管理系统。可选无模式、全模式或混合模式下。支持许 多高级特性，诸如ACID事务、快速索引，原生和SQL查询功能。可以JSON格式导入、导出文档。若不执行昂贵的JOIN操作的话，如同关系数据库可在 几毫秒内可检索数以百记的链接文档图

Even if it is a document-based database, the relationships are managed as in graph databases with direct connections between records.

It supports schema-less, schema-full and schema-mixed modes.

### 6.7 RethinkDB <a id="88"></a>

[http://www.rethinkdb.com/](http://www.rethinkdb.com/)

[https://en.wikipedia.org/wiki/RethinkDB](https://en.wikipedia.org/wiki/RethinkDB)

[http://baike.baidu.com/link?url=IaJEn3OOWg3i\_q4HSIyqBfxRUATc2bpsoNfzcHdyAVa7vbs02R6PsIIyR4F2V4zZy9Wq3FJYW550B-d6IF6MV\_](http://baike.baidu.com/link?url=IaJEn3OOWg3i_q4HSIyqBfxRUATc2bpsoNfzcHdyAVa7vbs02R6PsIIyR4F2V4zZy9Wq3FJYW550B-d6IF6MV_)

RethinkDB 1.14 \(Brazil\) 发布，分布式数据库  
[http://www.oschina.net/news/54771/rethinkdb-1-14-brazil](http://www.oschina.net/news/54771/rethinkdb-1-14-brazil)

## 7 列式存储 <a id="89"></a>

### 7.1 HBase <a id="90"></a>

### 7.2 Cassandra <a id="91"></a>

### 7.3 Accumulo <a id="92"></a>

[https://accumulo.apache.org/](https://accumulo.apache.org/)  
Apache Accumulo is based on Google's BigTable design and is built on top of Apache Hadoop, Zookeeper, and Thrift.

### 7.4 Hypertable <a id="93"></a>

[http://hypertable.org/](http://hypertable.org/)  
an open source database system inspired by publications on the design of Google's BigTable.  
Hypertable runs on top of a distributed file system such as the Apache HDFS, GlusterFS or the Kosmos File System \(KFS\).

### 7.5 Scylla <a id="94"></a>

[http://www.scylladb.com/](http://www.scylladb.com/)  
[https://github.com/scylladb/scylla](https://github.com/scylladb/scylla)

## 8 NewSQL <a id="95"></a>

### 8.1 Actordb <a id="96"></a>

[http://m.oschina.net/p/actordb](http://m.oschina.net/p/actordb)

[http://www.actordb.com/](http://www.actordb.com/)

[https://github.com/biokoda/actordb](https://github.com/biokoda/actordb)

### 8.2 Cockroachdb <a id="97"></a>

Go语言

[https://www.cockroachlabs.com/](https://www.cockroachlabs.com/)

[https://github.com/cockroachdb/cockroach](https://github.com/cockroachdb/cockroach)

[https://groups.google.com/forum/\#!forum/cockroachdb-users](https://groups.google.com/forum/#!forum/cockroachdb-users)

[https://groups.google.com/forum/\#!forum/cockroach-db](https://groups.google.com/forum/#!forum/cockroach-db)

[https://www.cockroachlabs.com/docs/](https://www.cockroachlabs.com/docs/)

Design Documents  
[https://github.com/cockroachdb/cockroach/blob/master/docs/design.md](https://github.com/cockroachdb/cockroach/blob/master/docs/design.md)

### 8.3 FoundationDB <a id="98"></a>

[https://github.com/FoundationDB](https://github.com/FoundationDB)

[https://foundationdb.com/](https://foundationdb.com/)

### 8.4 Oceanbase <a id="99"></a>

[https://github.com/alibaba/oceanbase](https://github.com/alibaba/oceanbase)

OceanBase架构介绍  
[http://wenku.baidu.com/link?url=cknPHaARI1\_Z6tHGeFfF4Vn\_hDjAsfHElNu8yOX\_jX2qX4eIKaf4UrY2e02TcCf5ib7GOUf437IulU5lUPrixrrgMFOnEJUMUIkBDP\_\_jA7](http://wenku.baidu.com/link?url=cknPHaARI1_Z6tHGeFfF4Vn_hDjAsfHElNu8yOX_jX2qX4eIKaf4UrY2e02TcCf5ib7GOUf437IulU5lUPrixrrgMFOnEJUMUIkBDP__jA7)

### 8.5 SnappyData <a id="100"></a>

### 8.6 TiDB <a id="101"></a>

[https://github.com/pingcap/tidb](https://github.com/pingcap/tidb)  
Go 语言

[https://github.com/pingcap/tikv](https://github.com/pingcap/tikv)  
Rust语言

[https://github.com/pingcap/tidb/blob/master/docs/QUICKSTART.md](https://github.com/pingcap/tidb/blob/master/docs/QUICKSTART.md)

从零开始写分布式数据库

[https://github.com/ngaut/builddatabase](https://github.com/ngaut/builddatabase)

## 9 图数据库 <a id="102"></a>

### 9.1 Neo4j <a id="103"></a>

### 9.2 Infinite Graph <a id="104"></a>

[http://www.objectivity.com/products/infinitegraph/](http://www.objectivity.com/products/infinitegraph/)

an enterprise distributed graph database

## 10 File存储 <a id="105"></a>

### 10.1 Ceph <a id="106"></a>

[http://ceph.com/](http://ceph.com/)

[https://github.com/ceph](https://github.com/ceph)

Ceph：一个 Linux PB 级分布式文件系统

[http://www.ibm.com/developerworks/cn/linux/l-ceph/](http://www.ibm.com/developerworks/cn/linux/l-ceph/)

### 10.2 FastDFS <a id="107"></a>

[http://code.google.com/p/fastdfs/](http://code.google.com/p/fastdfs/)

### 10.3 HDFS <a id="108"></a>

### 10.4 MogileFs <a id="109"></a>

[https://github.com/mogilefs/](https://github.com/mogilefs/)

### 10.5 MooseFS <a id="110"></a>

[http://www.moosefs.org/](http://www.moosefs.org/)

a fault tolerant, network distributed file system

### 10.6 TFS <a id="111"></a>

[http://tfs.taobao.org/](http://tfs.taobao.org/)

### 10.7 GlusterFS <a id="112"></a>

[http://www.gluster.org/](http://www.gluster.org/)

GlusterFS:异地备份（Geo-replication）源码分析

[http://blog.chinaunix.net/uid-22166872-id-4392777.html](http://blog.chinaunix.net/uid-22166872-id-4392777.html)

Gluster Geo-replication工作原理\[转载\]

[http://blog.163.com/szy8706@yeah/blog/static/62713185201363163131800/](http://blog.163.com/szy8706@yeah/blog/static/62713185201363163131800/)

[http://disclu.blogspot.com/2012/11/gluster-geo-replication.html](http://disclu.blogspot.com/2012/11/gluster-geo-replication.html)

### 10.8 kosmosfs <a id="113"></a>

[http://code.google.com/p/kosmosfs/](http://code.google.com/p/kosmosfs/)

## 11 In-Memory 存储 <a id="114"></a>

### 11.1 Redis cluster <a id="115"></a>

### 11.2 Mysql cluster <a id="116"></a>

### 11.3 Gemfire/Gemde <a id="117"></a>

### 11.4 VoltDB <a id="118"></a>

[http://voltdb.com/](http://voltdb.com/)

内存数据库

## 12 私有存储 <a id="119"></a>

### 12.1 Amazon <a id="120"></a>

#### 12.1.1 Amazon Dynamo <a id="121"></a>

Key-value

[http://www.allthingsdistributed.com/files/amazon-dynamo-sosp2007.pdf](http://www.allthingsdistributed.com/files/amazon-dynamo-sosp2007.pdf)

Amazon’s Dynamo paper

Merkle trees

Gossiping of membership

Gossiped synchronization of partitions

### 12.2 Google <a id="122"></a>

#### 12.2.1 BigTable <a id="123"></a>

Bigtable: A Distributed Storage System for Structured Data

[http://static.googleusercontent.com/media/research.google.com/en//archive/bigtable-osdi06.pdf](http://static.googleusercontent.com/media/research.google.com/en//archive/bigtable-osdi06.pdf)

#### 12.2.2 F1 <a id="124"></a>

F1: A Distributed SQL Database That Scales

[http://research.google.com/pubs/pub41344.html](http://research.google.com/pubs/pub41344.html)

[http://static.googleusercontent.com/media/research.google.com/en//pubs/archive/41344.pdf](http://static.googleusercontent.com/media/research.google.com/en//pubs/archive/41344.pdf)

#### 12.2.3 Spanner <a id="125"></a>

[http://research.google.com/archive/spanner.html](http://research.google.com/archive/spanner.html)

Spanner: Google’s Globally-Distributed Database

[http://static.googleusercontent.com/external\_content/untrusted\_dlcp/research.google.com/zh-CN//archive/spanner-osdi2012.pdf](http://static.googleusercontent.com/external_content/untrusted_dlcp/research.google.com/zh-CN//archive/spanner-osdi2012.pdf)

Exclusive: Inside Google Spanner, the Largest Single Database on Earth

[http://www.wired.com/2012/11/google-spanner-time/all/](http://www.wired.com/2012/11/google-spanner-time/all/)

解析全球级分布式数据库Google Spanner

[http://www.csdn.net/article/2012-09-19/2810132-google-spanner-next-database-datacenter](http://www.csdn.net/article/2012-09-19/2810132-google-spanner-next-database-datacenter)

#### 12.2.4 Megastore <a id="126"></a>

Megastore: Providing Scalable, Highly Available Storage for Interactive Services

[https://research.google.com/pubs/pub36971.html](https://research.google.com/pubs/pub36971.html)

[https://static.googleusercontent.com/media/research.google.com/en//pubs/archive/36971.pdf](https://static.googleusercontent.com/media/research.google.com/en//pubs/archive/36971.pdf)

### 12.3 Baidu <a id="127"></a>

#### 12.3.1 Baidu Mola <a id="128"></a>

Key-value

#### 12.3.2 Baidu BDRP <a id="129"></a>

#### 12.3.3 Baidu DDBS <a id="130"></a>

### 12.4 腾讯 <a id="131"></a>

单机MySQL到NoSQL集群 腾讯存储进阶路

[http://tech.it168.com/a2017/0322/3105/000003105672.shtml](http://tech.it168.com/a2017/0322/3105/000003105672.shtml)

腾讯十多个人管理一万多台NoSQL存储服务器的秘密

[http://m.techweb.com.cn/article/2016-01-06/2253310.shtml](http://m.techweb.com.cn/article/2016-01-06/2253310.shtml)

#### 12.4.1 腾讯CKV <a id="132"></a>

腾讯CKV海量分布式存储系统

[http://www.csdn.net/article/2014-03-11/2818723](http://www.csdn.net/article/2014-03-11/2818723)

#### 12.4.2 QuorumKV <a id="133"></a>

微信PaxosStore内存云揭秘：十亿Paxos/分钟的挑战

[http://mp.weixin.qq.com/s?\_\_biz=MzI4NDMyNTU2Mw%3D%3D∣=2247483804&idx=1&sn=a6629ebdaefbc2470c2ecbf12577daff](http://mp.weixin.qq.com/s?__biz=MzI4NDMyNTU2Mw%3D%3D&mid=2247483804&idx=1&sn=a6629ebdaefbc2470c2ecbf12577daff)

### 12.6 京东 <a id="134"></a>

#### 12.6.1 京东JIMDB <a id="135"></a>

解读JIMDB 京东分布式缓存与高速KV存储

[http://m.chinabyte.com/storage/463/13344963\_mi.shtml](http://m.chinabyte.com/storage/463/13344963_mi.shtml)

解读JIMDB 京东分布式缓存与高速KV存储

[http://m.it168.com/article\_1720792.html](http://m.it168.com/article_1720792.html)

### 12.7 滴滴 <a id="136"></a>

#### 12.7.1 滴滴Rockstable <a id="137"></a>

滴滴高性能KV存储系统实践

[http://m.it168.com/article\_3091989.html](http://m.it168.com/article_3091989.html)

### 12.8 美团 <a id="138"></a>

### 12.8.1 Cellar <a id="139"></a>

基于tair研发的新一代KV存储服务

美团cellar讲座笔记

[http://blog.leanote.com/post/yuannight/cellar%E8%AE%B2%E5%BA%A7%E7%AC%94%E8%AE%B0](http://blog.leanote.com/post/yuannight/cellar讲座笔记)

### 12.9 360 <a id="140"></a>

#### 12.9.1 360 Bada <a id="141"></a>

Key-value

360自研分布式存储系统Bada的架构设计和应用

[http://www.chinacloud.cn/wap.aspx?cid=16&nid=21103](http://www.chinacloud.cn/wap.aspx?cid=16&nid=21103)

#### 1.9.2 HustStore <a id="142"></a>

[https://github.com/Qihoo360/huststore](https://github.com/Qihoo360/huststore)

HustStore 360高性能分布式存储服务

[http://www.oschina.net/p/huststore?fromerr=ug3CEPNP](http://www.oschina.net/p/huststore?fromerr=ug3CEPNP)

## 13 云产品 <a id="143"></a>

### 13.1 Amazon DynamoDB <a id="144"></a>

### 13.2 AWS Aurora <a id="145"></a>

[https://aws.amazon.com/cn/rds/aurora/](https://aws.amazon.com/cn/rds/aurora/)

### 2.3 Google云 <a id="146"></a>

### 2.4 阿里云 <a id="147"></a>

### 2.5 美团云 <a id="148"></a>

Mangix:分布式对象存储

Mangix: 美团云分布式对象存储系统

[http://docs.huihoo.com/infoq/qconbeijing/2016/day2/%E4%BA%91%E5%B9%B3%E5%8F%B0%E6%9E%B6%E6%9E%84%E4%B8%93%E9%A2%98/2-6-%E7%BE%8E%E5%9B%A2%E4%B8%87%E4%BA%BF%E7%BA%A7%E5%AF%B9%E8%B1%A1%E5%AD%98%E5%82%A8%E7%B3%BB%E7%BB%9F-%E6%9D%8E%E5%87%AF.pdf](http://docs.huihoo.com/infoq/qconbeijing/2016/day2/云平台架构专题/2-6-美团万亿级对象存储系统-李凯.pdf)

