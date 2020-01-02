# 集群管理

[Ceph常规操作及常见问题梳理](https://www.cnblogs.com/kevingrace/p/5569737.html)

**Ceph集群管理**

每次用命令启动、重启、停止Ceph守护进程（或整个集群）时,必须指定至少一个选项和一个命令,还可能要指定守护进程类型或具体例程。

**命令格式如**

```text
{commandline} [options] [commands] [daemons]
```

**常用的commandline为"ceph",对应的options如下表:**

![](https://github.com/aftree/A/tree/46ba3f3482b57318efea7ebcb4b9fbdafb25a241/fen-bu-shi-wen-jian-xi-tong/ceph/images/907596-20180209155137482-1888041102.png)

**对应的commands如下表:**

![](https://github.com/aftree/A/tree/46ba3f3482b57318efea7ebcb4b9fbdafb25a241/fen-bu-shi-wen-jian-xi-tong/ceph/images/907596-20180209155219545-1473481343.png)

能指定的daemons\(守护进程\)类型包括mon,osd及mds。

**通过SysVinit机制运行ceph:**

```text
在 CentOS、Redhat、发行版上可以通过传统的SysVinit运行Ceph，Debian/Ubuntu的较老的版本也可以用此方法。
```

**使用SysVinit管理Ceph守护进程的语法如下:**

```text
[root@ceph ~] sudo /etc/init.d/ceph [options] [start|restart] [daemonType|daemonID]
```

**1）管理Ceph集群内所有类型的守护进程：**  
通过缺省\[daemonType\|daemonID\],并添加"-a" options,就可以达到同时对集群内所有类型的守护进程进行启动、关闭、重启等操作目的。

* 启动默认集群\(ceph\)所有守护进程:

```text
[root@ceph ~] sudo /etc/init.d/ceph -a start
```

停止默认集群\(ceph\)所有守护进程:

```text
[root@ceph ~] sudo /etc/init.d/ceph -a stop
```

如果未使用"-a"选项,以上命令只会对当前节点内的守护进程生效。

**2）管理Ceph集群内指定类型的守护进程：**  
根据命令语法,要启动当前节点上某一类的守护进程,只需指定对应类型及ID即可。

* 启动进程,以OSD进程为例：

```text
#启动当前节点内所有OSD进程
[root@ceph ~] sudo /etc/init.d/ceph start osd
#启动当前节点内某一个OSD进程,以osd.0为例
[root@ceph ~] sudo /etc/init.d/ceph start osd.0
```

重启及关闭进程,以OSD进程为例:

```text
#关闭当前节点内所有OSD进程
[root@ceph ~] sudo /etc/init.d/ceph stop osd
#关闭当前节点内某一个OSD进程,以osd.0为例
[root@ceph ~] sudo /etc/init.d/ceph stop osd.0
#重启当前节点内所有OSD进程
[root@ceph ~] sudo /etc/init.d/ceph restart osd
#重启当前节点内某一个OSD进程,以osd.0为例
[root@ceph ~] sudo /etc/init.d/ceph restart osd.0
```

**Ceph集群状态监控**

**1）检查集群健康状况**

* 检查Ceph集群状态

```text
[root@ceph ~] ceph health [detail]
```

如果集群处于健康状态,会输出HEALTH\_OK,如果输出HEALTH\_WARN甚至HEALTH\_ERR,表明Ceph处于一个不正常状态,可以加上"detail"选项帮助排查问题。

快速了解Ceph集群概况:

```text
[root@ceph ~] sudo ceph -s
#输出的内容大致如下:
cluster b370a29d-xxxx-xxxx-xxxx-3d824f65e339
health HEALTH_OK
monmap e1: 1 mons at {ceph1=10.x.x.8:6789/0}, election epoch 2, quorum  0 ceph1
osdmap e63: 2 osds: 2 up, 2 in
pgmap v41338: 952 pgs, 20 pools, 17130 MB data, 2199 objects
  115 GB used, 167 GB / 297 GB avail
          952 active+clean
```

通过以上命令,可以快速了解Ceph集群的clusterID,health状况,以及monitor、OSD、PG的map概况。

如果需要实时观察Ceph集群状态变化，可使用如下命令：

```text
[root@ceph ~] sudo ceph -w
```

**2）检查集群容量使用情况**

```text
[root@ceph ~] sudo ceph df
#输出的内容大致如下
GLOBAL:
     SIZE      AVAIL     RAW USED     %RAW USED
     1356G     1284G       73943M          5.32
POOLS:
     NAME        ID     USED       %USED     MAX AVAIL     OBJECTS
     images      1      24983M      1.80          421G        3158
     volumes     2      32768k         0          421G          20
     vms         3       3251M      0.23          421G         434
```

输出的GLOBAL段显示了数据所占用集群存储空间概况。

* SIZE: 集群的总容量
* AVAIL: 集群的总空闲容量
* RAW USED: 已用存储空间总量
* %RAW USED: 已用存储空间百分比

输出的POOLS段展示了存储池列表及各存储池的大致使用率。本段没有展示副本、克隆品和快照占用情况。 例如,把1MB的数据存储为对象,理论使用量将是1MB,但考虑到副本数、克隆数、和快照数,实际使用量可能是2MB或更多。

* NAME: 存储池名
* ID: 存储池唯一标识符
* USED: 使用量,单位可为KB、MB或GB,以输出结果为准
* %USED: 存储池的使用率
* MAX AVAIL: 存储池的最大可用空间
* OBJECTS: 存储池内的object个数

注：POOLS 段内的数字是理论值,它们不包含副本、快照或克隆。因此,它与USED和%USED数量之和不会达到GLOBAL段中的RAW USED和 %RAW USED数量。

**PG管理操作**

PG\(归置组\)是多个object的逻辑存储集合,每个PG会根据副本级别而被复制多份。一个POOL的PG个数可以在创建时指定,也可以在之后进行扩大。**但是需要注意的是，目前Ceph尚不支持减少POOL中的PG个数。**

**1）预定义PG个数**  
Ceph对于集群内PG的总个数有如下公式:

```text
（OSD个数\*100）/ 副本数 = PGs
```

以上公式计算得出结果后,再取一个与之较大的2的幂的值,便可作为集群的总PG数。例如,一个配置了200个OSD且副本数为3的集群,计算过程如下:

```text
(200\*100)/3 = 6667. Nearest power of 2 : 8192
```

得到8192后,可以根据集群内所需建立的POOL的个数及用途等要素,进行PG划分。具体划分细则请参考官 方计算工具 PGcalc:[http://ceph.com/pgcalc/](http://ceph.com/pgcalc/)

**2）设置PG数量**  
要设置某个POOL的PG数量\(pg\_num\),必须在创建POOL时便指定,命令如下:

```text
[root@ceph ~] sudo ceph osd pool create "pool-name" pg_num [pgp_num]
[root@ceph ~] sudo ceph osd pool create image 256 256
```

需要注意的是,在后续增加PG数量时,还必须增加用于归置PG的PGP数量\(pgp\_num\),PGP的数量应该与PG的数量相等。但在新增POOL时可以不指定pgp\_num,默认会与pg\_num保持一致。

新增PG数量:

```text
[root@ceph ~] sudo ceph osd pool set "pool-name" pg_num [pgp_num]
[root@ceph ~] sudo ceph osd pool set image 512 512
```

**3）查看PG信息**

若需要获取某个POOL的PG数量或PGP数量,可以使用如下命令:

```text
[root@ceph ~] sudo ceph osd pool get "pool-name" pg_num/pgp_num
[root@ceph ~] sudo ceph osd pool get image pg_num
pg_num : 512
[root@ceph ~] sudo ceph osd pool get image pgp_num
pgp_num : 512
```

若要获取集群里PG的统计信息,可以使用如下命令,并指定输出格式:

```text
#不指定输出格式的情况下,会输出纯文本内容,可指定格式为json
[root@ceph ~] sudo ceph pg dump [--format json]
```

若要获取状态不正常的PG的状态,可以使用如下命令:

```text
[root@ceph ~] sudo ceph pg dump_stuck  inactive|unclean|stale|undersized|degraded [--format <format>]
```

**4）PG状态概述**  
一个PG在它的生命周期的不同时刻可能会处于以下几种状态中:

Creating\(创建中\)  
在创建POOL时,需要指定PG的数量,此时PG的状态便处于creating,意思是Ceph正在创建PG。

Peering\(互联中\)  
peering的作用主要是在PG及其副本所在的OSD之间建立互联,并使得OSD之间就这些PG中的object及其元数据达成一致。

Active\(活跃的\)  
处于该状态意味着数据已经完好的保存到了主PG及副本PG中,并且Ceph已经完成了peering工作。

Clean\(整洁的\)  
当某个PG处于clean状态时,则说明对应的主OSD及副本OSD已经成功互联,并且没有偏离的PG。也意味着Ceph已经将该PG中的对象按照规定的副本数进行了复制操作。

Degraded\(降级的\)  
当某个PG的副本数未达到规定个数时,该PG便处于degraded状态,例如:

在客户端向主OSD写入object的过程,object的副本是由主OSD负责向副本OSD写入的,直到副本OSD在创建object副本完成,并向主OSD发出完成信息前,该PG的状态都会一直处于degraded状态。又或者是某个OSD的状态变成了down,那么该OSD上的所有PG都会被标记为degraded。  
当Ceph因为某些原因无法找到某个PG内的一个或多个object时,该PG也会被标记为degraded状态。此时客户端不能读写找不到的对象,但是仍然能访问位于该PG内的其他object。

Recovering\(恢复中\)  
当某个OSD因为某些原因down了,该OSD内PG的object会落后于它所对应的PG副本。而在该OSD重新up之后,该OSD中的内容必须更新到当前状态,处于此过程中的PG状态便是recovering。

Backfilling\(回填\)  
当有新的OSD加入集群时,CRUSH会把现有集群内的部分PG分配给它。这些被重新分配到新OSD的PG状态便处于backfilling。

Remapped\(重映射\)  
当负责维护某个PG的acting set变更时,PG需要从原来的acting set迁移至新的acting set。这个过程需要一段时间,所以在此期间,相关PG的状态便会标记为remapped。

Stale\(陈旧的\)  
默认情况下,OSD守护进程每半秒钟便会向Monitor报告其PG等相关状态,如果某个PG的主OSD所在acting set没能向Monitor发送报告,或者其他的Monitor已经报告该OSD为down时,该PG便会被标记为stale。

**Monitor管理操作**

**1）检查集群内Monitor状态**

```text
如果你有多个监视器(很可能),你启动集群后、读写数据前应该检查监视器法定人数状态。运行着多个监视器时必须形成法定人数,最好周期性地检查监视器状态来确定它们在运行。
```

要查看monmap,可以执行如下命令:

```text
[root@ceph ~] sudo ceph mon stat
#输出内容大致如下:
e3: 3 mons at {controller-21=172.x.x.21:6789/0,controller-22=172.x.x.22:6789/0,
controller-23=172.x.x.23:6789/0}, election epoch 48710,
quorum 0,1,2 controller-21,controller-22,controller-23
```

通过以上信息可以了解到集群内monmap版本为3,共有3个Monitor守护进程,分别处于哪些主机\( 主机名、IP地址、端口号\)上,当前的Monitor选举版本为48710,Monitor集群内的法定监视器共有3个\(显示的qourumID个数总和\),以及它们的MonitorID。

如果希望进一步了解monmap,可以通过如下命令查看:

```text
[root@ceph ~] sudo ceph mon dump
#输出内容大致如下:
dumped monmap epoch 3
epoch 3
fsid 86673d4c-xxxx-xxxx-xxxx-b61e6681305d
last_changed 2016-09-02 16:05:02.120629
created 2016-09-02 16:03:39.311083
0: 172.16.130.21:6789/0 mon.controller-21
1: 172.16.130.22:6789/0 mon.controller-22
2: 172.16.130.23:6789/0 mon.controller-23
```

通过以上信息可以额外了解到monmap创建时间及最近一次修改时间。

要获知Ceph集群内Monitor集群法定监视器的情况,可以使用如下命令查看:

```text
[root@ceph ~] sudo ceph quorum_status
#输出内容大致如下:
{"election_epoch":48710,"quorum":[0,1,2],
     "quorum_names":["controller-21","controller-22","controller-23"],
     "quorum_leader_name":"controller-22",
     "monmap":{"epoch":3,
     "fsid":"86673d4c-xxx-xxxx-xxxxx-b61e6681305d",
     "modified":"2016-09-02 16:05:02.120629",
     "created":"2016-09-0216:03:39.311083",
     "mons":[{"rank":0,"name":"controller-21","addr":"172.16.130.21:6789\ /   0"},
     {"rank":1,"name":"controller-22","addr":"172.16.130.22:6789\/0"},
     {"rank":2,"name":"controller-23","addr":"172.16.130.23:6789\/0"}]}}
```

通过以上信息,可以了解到Monitor集群法定监视器的个数,以及监视器leader。

**2）实际业务场景**

**场景一、使用ceph-deploy新增mon节点**  
需求：产品标准部署完成时，ceph mon一般会部署在某些OSD节点上，需要将mon拆到其他节点上。

**操作步骤：**  
-&gt; 使用ceph-deploy新建mon

```text
[root@host-name ~]#ceph-deploy mon create {host-name [host-name]...}
[root@host-name ~]#ceph-deploy mon create newhostname
```

注意事项：

* 使用ceph-deploy命令的节点上必须有相应权限，可以使用ceph-deploy gatherkeys命令分配权限
* 使用ceph-deploy新增的monitor默认会使用ceph public网络

-&gt; 停止原本在计算节点上的mon进程，验证集群是否正常，如果正常则进行下一步。

```text
[root@host-name ~]# /etc/init.d/ceph stop mon
```

-&gt; 删除原本在计算节点上的monitor。

```text
[root@host-name ~]# ceph-deploy mon destroy {host-name [host-name]...}
[root@host-name ~]# ceph-deploy mon destroy oldhostname
```

-&gt; 修改配置文件中关于mon的配置，不要使用主机名，应直接使用IP（public网络），之后同步到所有ceph节点上并重启所有mon进程。

注意事项：  
由于默认情况下，主机名和IP的对应关系是使用的管理网络，而使用ceph-deploy新增的monitor默认会使用ceph public网络所以需要修改配置文件中"mon\_intial\_members"及"mon\_host"中的主机名为ip地址。

**场景二、从一个monitor状态异常的Ceph集群中获取monmap**  
需求：当一个Ceph集群的monitor集群状态出现异常时，集群的基本命令都无法使用，这个时候可以尝试提取出monmap，帮助排查问题。

**操作步骤：**  
-&gt; 导出集群monmap

```text
[root@host-name ~]# ceph-mon -i mon-host-name --extract-monmap /tmp/monmap-file
```

注意：以上命令在mon状态正常的节点上无法使用。会报如下错误：

```text
IO error: lock /var/lib/ceph/mon/ceph-cont01/store.db/LOCK: Resource temporarily unavailable
```

-&gt; 使用monmaptool查看

```text
[root@host-name ~]# monmaptool --print /tmp/monmap-file
monmaptool: monmap file /tmp/monmap
epoch 3
fsid 86673d4c-xxxx-xxxx-xxxx-b61e6681305d
last_changed 2016-10-13 16:17:33.590245
created 2016-10-13 16:16:33.801453
0: 172.16.50.136:6789/0 mon.cont01
1: 172.16.50.137:6789/0 mon.cont02
2: 172.16.50.138:6789/0 mon.cont03
```

**OSD管理操作**

**1）OSD状态**  
单个OSD有两组状态需要关注,其中一组使用in/out标记该OSD是否在集群内,另一组使用up/down标记该OSD是否处于运行中状态。两组状态之间并不互斥,换句话说,当一个OSD处于“in”状态时,它仍然可以处于up或down的状态。

OSD状态为in且up  
这是一个OSD正常的状态,说明该OSD处于集群内,并且运行正常。

OSD状态为in且down  
此时该OSD尚处于集群中,但是守护进程状态已经不正常,默认在300秒后会被踢出集群,状态进而变为out且down,之后处于该OSD上的PG会迁移至其它OSD。

OSD状态为out且up  
这种状态一般会出现在新增OSD时,意味着该OSD守护进程正常,但是尚未加入集群。

OSD状态为out且down  
在该状态下的OSD不在集群内,并且守护进程运行不正常,CRUSH不会再分配PG到该OSD上。

**2）检查OSD状态**  
在执行ceph health、ceph -s或ceph -w等命令时,也许会发现集群并未处于HEALTH状态,就OSD而言,应该关注它是否处于集群内,以及是否处于运行中状态。我们可以通过以下命令查看集群内所有OSD的状态:

```text
[root@ceph ~] sudo ceph osd stat
#输出内容大致如下：
osdmap e3921: 5 osds: 5 up, 5 in;
```

命令的结果显示,当前osdmap的版本号为e3921,集群内共有5个OSD,其中处于“up”状态的OSD为5个,处于“in”状态的OSD也为5个。这说明集群中OSD的状态处于正常情况。

如果要启动一个OSD守护进程,请参考前文"集群管理操作"内容

**3）查看集群OSD配置**  
要了解集群OSD的配置情况,可以使用下列命令进行查看。

查看OSD容量的使用情况

```text
[root@ceph ~] sudo ceph osd df
#输出内容大致如下：
ID WEIGHT  REWEIGHT SIZE  USE    AVAIL %USE VAR
0 0.25999  1.00000  269G 21378M  248G 7.75 1.38
3 0.25999  1.00000  269G 19027M  250G 6.90 1.23
4 0.25999  1.00000  269G 14207M  255G 5.15 0.92
1 0.53999  1.00000  548G 23328M  525G 4.15 0.74
     TOTAL 1356G 77942M 1280G 5.61
MIN/MAX VAR: 0/1.38  STDDEV: 1.47
```

从输出结果可以看到每个OSD的总容量、当前使用量以及可用容量等信息。

查看OSD在集群布局中的设计分布

```text
[root@ceph ~] sudo ceph osd tree
#输出内容大致如下：
ID WEIGHT  TYPE NAME       UP/DOWN REWEIGHT PRIMARY-AFFINITY
-1 0.08995 root default
-2 0.02998     host ceph01
 0 0.00999         osd.0        up  1.00000          1.00000
 1 0.00999         osd.1        up  1.00000          1.00000
 2 0.00999         osd.2        up  1.00000          1.00000
-3 0.02998     host ceph02
 3 0.00999         osd.3        up  1.00000          1.00000
 4 0.00999         osd.4        up  1.00000          1.00000
 5 0.00999         osd.5        up  1.00000          1.00000
-4 0.02998     host ceph03
 6 0.00999         osd.6        up  1.00000          1.00000
 7 0.00999         osd.7        up  1.00000          1.00000
 8 0.00999         osd.8        up  1.00000          1.00000
```

从输出结果可以看到每个OSD的位置分布情况,默认的CRUSHMAP中,OSD按照所在的主机节点分布,可以通过修改CRUSHMAP进行定制化分布设计。同时可以看到每个OSD的WEIGHT值,WEIGHT值与OSD的容量相关,1TB容量换算WEIGHT值为1.0。

查看OSD的dump概况

```text
[root@ceph ~] sudo ceph osd dump
```

OSD dump输出的条目较多,基本可以分为三个部分：  
输出OSDmap信息,包括版本号、集群ID以及map相关的时间;  
POOL的相关信息,包括POOL ID、POOL名称、副本数、最小副本数、ruleset ID等信息;  
列出所有OSD的状态等信息,包括OSD ID、状态、状态版本记录以及被监听的IP地址及端口等信息。

**4）实际业务场景**

**场景一、使用ceph-deploy新增OSD节点**  
需求：由于某些原因无法使用salt进行扩容Ceph集群时，可以考虑使用ceph-deploy工具扩容Ceph集群。

**操作步骤：**  
-&gt; 任选一个monitor节点，安装ceph-deploy。

```text
[root@host-name ~]# yum install ceph-deploy
```

-&gt; 切换至Ceph集群配置文件所在目录,如使用默认名称ceph,则切换至如下目录。

```text
[root@host-name ~]# cd /etc/ceph
```

-&gt; 编辑/etc/hosts目录，将新增节点的主机名及IP加入该文件中。

```text
[root@host-name ~]# vim /etc/hosts
```

-&gt; 在新增节点上安装ceph软件，并解决依赖关系，也许需要安装redhat-lsb。

```text
[root@new-node ~]# yum install ceph
[root@new-node ~]# yum install redhat-lsb
```

-&gt; 推送相关密钥及配置文件至新增节点。

\[root@host-name ceph\]\# ceph-deploy admin new-node

-&gt; 创建集群关系key。

```text
[root@host-name ceph]# ceph-deploy gatherkeys 当前节点
[root@host-name ceph]# ceph-deploy gatherkeys new-node
```

-&gt; 检查新增OSD节点的磁盘。

\[root@host-name ceph\]\# ceph-deploy disk list new-node

-&gt; 创建所要新增OSD节点上的osd。

```text
root@host-name ceph]# ceph-deploy osd create new-node:new-disk
```

-&gt; 少数情况下，需要手动激活新增的osd后，集群才能正常识别新增的osd。

```text
[root@new-node ~]# ceph-disk activate-all
```

**场景二、完全删除osd**  
需求：需要删除Ceph集群中一个或多个osd时，可以参考以下做法实现。

**操作步骤：**

```text
-> 停止需要删除的osd进程。
[root@host-name ~]# /etc/init.d/ceph stop osd.x
->  将该osd的集群标记为out。
[root@host-name ~]# ceph osd out osd.x
->  将该osd从Ceph crush中移除。
[root@host-name ~]# ceph osd crush remove osd.x
->  从集群中完全删除该osd的记录。
[root@host-name ~]# ceph osd rm osd.x
->  删除该osd的认证信息，否则该osd的编号不会释放。
[root@host-name ~]# ceph auth del osd.x
```

**POOL管理操作**

**1）获取POOL概况**  
在部署一个Ceph集群时,会创建一个默认名为rbd的POOL,使用以下命令,可以获取集群内所有POOL的概况信息。

```text
[root@ceph ~] sudo ceph osd pool ls detail
```

使用该命令你可以了解到集群内POOL的个数、对应的POOL id、POOL名称、副本数、最小副本数，ruleset及POOL snap等信息。

**2）创建POOL**  
在创建一个新的POOL前,可先查看配置文件中是否有关于POOL的默认参数,同时了解集群内CRUSHMAP的设计，之后再新建POOL。

例如,配置文件中有关于pg\_num,pgp\_num等默认参数,那么在使用ceph-deploy自动化部署工具,便会以此参数创建指定POOL。

要手动创建一个POOL的命令语法如下:

```text
#创建一个副本类型的POOL
[root@ceph ~] sudo ceph osd pool create {pool-name} {pg-num} [{pgp-num}] [replicated] \
              [ruleset]
#创建一个纠删码类型的POOL
[root@ceph ~] sudo ceph osd pool create {pool-name} {pg-num} {pgp-num} erasure \
              [erasure-code-profile] [ruleset]
```

在{}内的参数为必选项,\[\]内的参数均设有默认值,如果没有更改设计,可以不添加。  
参数的含义如下:

* pool-name: POOL的名字；必须添加。
* pg-num: POOL拥有的PG总数；必须添加。具体内容可参考前文:PG管理操作
* pgp-num: POOL拥有的PGP总数；非必须添加。默认与pg-num相同。
* replicated\|erasure: POOL类型；非必须添加。如不指定为erasure,则默认为replicated类型。
* ruleset: POOL所用的CRUSH规则ID。非必须添加。默认为0,若需指定其他ruleset,需确保ruleset必须存在。
* erasure-code-profile: 仅用于纠删码类型的POOL。指定纠删码配置框架,此配置必须已由osd erasure-code-profile set 定义

**3）重命名POOL**  
如果需要重命名存储池,可以使用以下命令:

```text
[root@ceph ~] sudo ceph osd pool rename {current-pool-name}    {new-pool-name}
```

需要注意的是,在POOL被重命名后,需要用新的POOL名更新对应的认证用户权限。此部分内容请参考:用户管理操作

**4）删除POOL**  
删除存储池,可以使用以下命令:

```text
[root@ceph ~] sudo ceph osd pool delete {pool-name} [{pool-name} --yes-i-really-really-mean-it]
```

如果有某个认证用户拥有该池的某些权限,那么你应该确认该认证用户是否还有其他作用,确认完毕后,或更 新,或将该用户删除。  
此部分内容请参考:用户管理操作

**5）设置POOL的配置**  
可以为每个POOL进行配额,可以设置最大字节数及最大object数,命令如下:

```text
[root@ceph ~] sudo ceph osd pool set-quota {pool-name} [max_objects {obj-count}] [max_bytes {bytes}]

例如:
[root@ceph ~] sudo ceph osd pool set-quota data max_objects 10000
[root@ceph ~] sudo ceph osd pool set-quota data max_bytes 10240
```

如果要取消配额,只需要将值设置为0即可。

**6）查看POOL的统计信息**  
查看集群内POOL的使用情况,可以使用以下命令:

```text
[root@ceph ~] sudo rados df
```

**7）POOL快照操作**

要拍下某个POOL的快照,可以使用以下命令:

```text
[root@ceph ~] sudo ceph osd pool mksnap {pool-name} {snap-name}

例如：
[root@ceph ~] sudo ceph osd pool mksnap snappool snap1
```

要删除某个POOL的快照,可以使用以下命令:

```text
root@ceph ~] sudo ceph osd pool rmsnap {pool-name} {snap-name}

例如:
[root@ceph ~] sudo ceph osd pool rmsnap snappool snap1
```

要查看集群中POOL的快照信息,暂时未提供ls-snap相关的命令,但可以借助前文提到的命令查看:

```text
[root@ceph ~] sudo ceph osd pool ls detail
```

**8）置object副本数量**

要设置副本类型POOL的对象副本数,可以使用以下命令:

```text
[root@ceph ~] sudo ceph osd pool set {pool-name} size {num-replicas}

例如:
[root@ceph ~] sudo ceph osd pool set replpool size 3
```

当一个object的副本数小于规定值时,仍然可以接受I/O请求。为了保证I/O正常,可以为POOL设置最低副本数,如:

```text
[root@ceph ~] sudo ceph osd pool set replpool min_size 3
```

这确保了该POOL内任何副本数小于min\_size的对象都不会再进行I/O

