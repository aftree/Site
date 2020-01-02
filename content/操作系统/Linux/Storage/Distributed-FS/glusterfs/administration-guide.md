**GlusterFS分布式系统维护管理手册**

**1）管理说明**

```
在解释系统管理时会提供实例，首先提供一个环境说明。
系统节点：
IP            别名     Brick
192.168.2.100 server0 /mnt/sdb1  /mnt/sdc1  /mnt/sdd1
192.168.2.101 server1 /mnt/sdb1  /mnt/sdc1  /mnt/sdd1
192.168.2.102 server2 /mnt/sdb1  /mnt/sdc1  /mnt/sdd1
  
创建了三个节点，并每台虚拟机 mount 三块磁盘作为 Brick 使用，每个 brick 分配了 30G 的虚拟容量。
  
实例约定
AFR 卷名：     afr_vol
DHT 卷名：     dht_vol
Stripe 卷名：  str_vol
客户端挂载点： /mnt/gluster
```

**2）系统部署**

```
2.1) 在每个节点上启动glusterd服务
[root@localhost ~]# service glusterd start
  
2.2) 添加节点到存储池，在其中一个节点上操作 ，如 server0
[root@localhost ~]# gluster peer probe server1
[root@localhost ~]# gluster peer probe server2
//可以使用 gluster peer status 查看当前有多少个节点，显示不包括该节点
  
2.3) 创建系统卷, 部署最常见的分布式卷，在 server0上操作
[root@localhost ~]# gluster volume create dht_vol 192.168.2.{100,101,102}:/mnt/sdb1
//分别使用 server0/1/2 的磁盘挂载目录/mnt/sdb1 作为 brick
  
2.4) 启动系统卷，在 server0上操作
[root@localhost ~]# gluster volume start dht_vol
  
2.5) 挂载客户端，例如在server2上
[root@localhost ~]# mount.glusterfs server0:/dht_vol /mnt/gluster
//将系统卷挂载到 server2 上的/mnt/gluster 目录下就可以正常使用了。该目录聚合了三个不同主机上的三块磁盘。
//从启动服务到提供全局名字空间，整个部署流程如上。
```

**3）基本系统管理**

**3.1）节点管理**

```
# gluster peer command
  
1）节点状态
[root@localhost ~]# gluster peer status      //在serser0上操作，只能看到其他节点与 server0 的连接状态
Number of Peers: 2
Hostname: server1
Uuid: 5e987bda-16dd-43c2-835b-08b7d55e94e5
State: Peer in Cluster (Connected)
Hostname: server2
Uuid: 1e0ca3aa-9ef7-4f66-8f15-cbc348f29ff7
State: Peer in Cluster (Connected)
  
2）添加节点
[root@localhost ~]# gluster peer probe HOSTNAME
#gluster peer probe server2                //将server2添加到存储池中
  
3）删除节点
[root@localhost ~]# gluster peer detach HOSTNAME
[root@localhost ~]# gluster peer detach server2        //将server2从存储池中移除
//移除节点时，需要确保该节点上没有 brick ，需要提前将 brick 移除
```

**3.2）卷管理**

```
1）创建卷
[root@localhost ~]# gluster volume create NEW-VOLNAME [transport [tcp | rdma | tcp,rdma]]
NEW-BRICK...
  
-------------创建分布式卷(DHT)-------------
[root@localhost ~]# gluster volume create dht_vol 192.168.2.{100,101,102}:/mnt/sdb1
//DHT 卷将数据以哈希计算方式分布到各个 brick 上，数据是以文件为单位存取，基本达到分布均衡，提供的容量和为各个 brick 的总和。
  
-------------创建副本卷(AFR)-------------
[root@localhost ~]# gluster volume create afr_vol replica 3 192.168.2.{100,101,102}:/mnt/sdb1
//AFR 卷提供数据副本，副本数为 replica，即每个文件存储 replica 份数，文件不分割，以文件为单位存储；副本数需要等于brick数；当brick数是副本的倍数时，则自动变化为
Replicated-Distributed卷。
  
[root@localhost ~]# gluster  volume  create  afr_vol  replica  2  192.168.2.{100,101,102}:/mnt/sdb1 192.168.2.{100,101,102}:/mnt/sdc1
//每两个 brick 组成一组，每组两个副本，文件又以 DHT 分布在三个组上，是副本卷与分布式卷的组合。
  
-------------创建条带化卷(Stripe)-------------
[root@localhost ~]# gluster volume create str_vol stripe 3 192.168.2.{100,101,102}:/mnt/sdb1
//Stripe 卷类似 RAID0，将数据条带化，分布在不同的 brick，该方式将文件分块，将文件分成stripe块，分别进行存储，在大文件读取时有优势；stripe 需要等于 brick 数；当 brick 数等于 stripe 数的倍数时，则自动变化为 Stripe-Distributed 卷。
  
[root@localhost ~]# gluster  volume  create  str_vol  stripe  3  192.168.2.{100,101,102}:/mnt/sdb1 192.168.2.{100,101,102}:/mnt/sdc1
//没三个 brick 组成一个组，每组三个 brick，文件以 DHT 分布在两个组中，每个组中将文件条带化成 3 块。
  
-------------创建Replicated-Stripe-Distributed卷-------------
[root@localhost ~]# gluster volume create str_afr_dht_vol stripe 2 replica 2 192.168.2.{100,101,102}:/mnt/sdb1 192.168.2.{100,101,102}:/mnt/sdc1 192.168.2.{100,101}:/mnt/sdd1
//使用8个 brick 创建一个组合卷，即 brick 数是 stripe*replica 的倍数，则创建三种基本卷的组合卷，若刚好等于 stripe*replica 则为 stripe-Distrbuted 卷。
  
2）卷 信息
[root@localhost ~]# gluster volume info
//该命令能够查看存储池中的当前卷的信息，包括卷方式、包涵的 brick、卷的当前状态、卷名及 UUID 等。
  
3）卷 状态
[root@localhost ~]# gluster volume status
//该命令能够查看当前卷的状态，包括其中各个 brick 的状态，NFS 的服务状态及当前 task执行情况，和一些系统设置状态等。
  
4）启动/ 停止 卷
[root@localhost ~]# gluster volume start/stop VOLNAME
//将创建的卷启动，才能进行客户端挂载；stop 能够将系统卷停止，无法使用；此外 gluster未提供 restart 的重启命令
  
5）删除卷
[root@localhost ~]# gluster volume delete VOLNAME
//删除卷操作能够将整个卷删除，操作前提是需要将卷先停止
```

**3.3）Brick 管理**

```
1）添加 Brick
若是副本卷，则一次添加的 Bricks  数是 replica  的整数倍；stripe  具有同样的要求。
# gluster volume add-brick VOLNAME NEW-BRICK
# gluster volume add-brick dht_vol server3:/mnt/sdc1
//添加 server3 上的/mnt/sdc1 到卷 dht_vol 上。
  
2）移除 Brick
若是副本卷，则移除的 Bricks  数是 replica  的整数倍；stripe  具有同样的要求。
[root@localhost ~]# gluster volume remove-brick VOLNAME BRICK start/status/commit
[root@localhost ~]# gluster volume remove-brick dht_vol server3:/mnt/sdc1 start
//GlusterFS_3.4.1 版本在执行移除 Brick 的时候会将数据迁移到其他可用的 Brick 上，当数据迁移结束之后才将 Brick 移除。执行 start 命令，开始迁移数据，正常移除 Brick。
  
[root@localhost ~]# gluster volume remove-brick dht_vol server3:/mnt/sdc1 status
//在执行开始移除 task 之后，可以使用 status 命令进行 task 状态查看。
  
[root@localhost ~]# gluster volume remove-brick dht_vol server3:/mnt/sdc1 commit
//使用 commit 命令执行 Brick 移除，则不会进行数据迁移而直接删除 Brick，符合不需要数据迁移的用户需求。
  
PS ：系统的扩容及缩容可以通过如上节点管理、Brick  管理组合达到目的。
(1) 扩容时，可以先增加系统节点，然后 添加新增节点上的 Brick  即可。
(2) 缩容时，先移除 Brick ，然后再。 进行节点删除则达到缩容的目的，且可以保证数据不丢失。
  
3）替换 Brick
[root@localhost ~]# gluster volume replace-brick VOLNAME BRICKNEW-BRICK start/pause/abort/status/commit
[root@localhost ~]# gluster volume replace-brick dht_vol server0:/mnt/sdb1 server0:/mnt/sdc1 start
//如上，执行 replcace-brick 卷替换启动命令，使用 start 启动命令后，开始将原始 Brick 的数据迁移到即将需要替换的 Brick 上。
  
[root@localhost ~]# gluster volume replace-brick dht_vol server0:/mnt/sdb1 server0:/mnt/sdc1 status
//在数据迁移的过程中，可以查看替换任务是否完成。
  
[root@localhost ~]# gluster volume replace-brick dht_vol server0:/mnt/sdb1 server0:/mnt/sdc1 abort
//在数据迁移的过程中，可以执行 abort 命令终止 Brick 替换。
  
[root@localhost ~]# gluster volume replace-brick dht_vol server0:/mnt/sdb1 server0:/mnt/sdc1 commit
//在数据迁移结束之后，执行 commit 命令结束任务，则进行Brick替换。使用volume info命令可以查看到 Brick 已经被替换。
```

**4）GlusterFS系统扩展维护**

**4.1）系统配额**

```
1）开启/关闭系统配额
[root@localhost ~]# gluster volume quota VOLNAME enable/disable
//在使用系统配额功能时，需要使用 enable 将其开启；disable 为关闭配额功能命令。
  
2）设置( 重置) 目录配额
[root@localhost ~]# gluster volume quota VOLNAME limit-usage /directory limit-value
[root@localhost ~]# gluster volume quota dht_vol limit-usage /quota 10GB
//如上，设置 dht_vol 卷下的 quota 子目录的限额为 10GB。
  
PS：这个目录是以系统挂载目录为根目录”/”,所以/quota 即客户端挂载目录下的子目录 quota
  
3）配额查看
[root@localhost ~]# gluster volume quota VOLNAME list
[root@localhost ~]# gluster volume quota VOLNAME list /directory name
//可以使用如上两个命令进行系统卷的配额查看，第一个命令查看目的卷的所有配额设置，第二个命令则是执行目录进行查看。
//可以显示配额大小及当前使用容量，若无使用容量(最小 0KB)则说明设置的目录可能是错误的(不存在)。
```

**4.2）地域复制（geo-replication）**

```
# gluster volume geo-replication MASTER SLAVE start/status/stop
地域复制 是系统提供的灾备功能，能够将系统的全部数据进行异步的增量备份到另外的磁盘中。
  
[root@localhost ~]# gluster volume geo-replication dht_vol 192.168.2.104:/mnt/sdb1 start
//如上，开始执行将 dht_vol 卷的所有内容备份到 2.104 下的/mnt/sdb1 中的 task，需要注意的是，这个备份目标不能是系统中的 Brick。
```

**4.3）I/O 信息查看**

```
Profile Command 提供接口查看一个卷中的每一个brick的IO信息。
  
[root@localhost ~]# gluster volume profile VOLNAME start
//启动 profiling，之后则可以进行 IO 信息查看
  
[root@localhost ~]# gluster volume profile VOLNAME info
//查看 IO 信息，可以查看到每一个 Brick 的 IO 信息
  
[root@localhost ~]# gluster volume profile VOLNAME stop
//查看结束之后关闭 profiling 功能
```

**4.4）Top 监控**

```
Top command 允许你查看bricks的性能例如：read, write, file open calls, file read calls, file write calls, directory open calls,
and directory real calls所有的查看都可以设置 top数，默认100
  
[root@localhost ~]# gluster volume top VOLNAME open [brick BRICK-NAME] [list-cnt cnt]
//查看打开的 fd
  
[root@localhost ~]# gluster volume top VOLNAME read [brick BRICK-NAME] [list-cnt cnt]
//查看调用次数最多的读调用
  
[root@localhost ~]# gluster volume top VOLNAME write [brick BRICK-NAME] [list-cnt cnt]
//查看调用次数最多的写调用
  
[root@localhost ~]# gluster volume top VOLNAME opendir [brick BRICK-NAME] [list-cnt cnt]
[root@localhost ~]# gluster volume top VOLNAME readdir [brick BRICK-NAME] [list-cnt cnt]
//查看次数最多的目录调用
  
[root@localhost ~]# gluster volume top VOLNAME read-perf [bs blk-size count count] [brick BRICK-NAME] [list-cnt]
//查看每个 Brick 的读性能
  
[root@localhost ~]# gluster volume top VOLNAME write-perf [bs blk-size count count] [brick BRICK-NAME] [list-cnt cnt]
//查看每个 Brick 的写性能
```



