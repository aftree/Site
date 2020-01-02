# Install Guide

之前已经简单地对

[GlusterFS分布式文件系统](http://www.cnblogs.com/kevingrace/p/8709544.html)

做了介绍，下面就该环境部署做一记录：

## 环境说明

```text
GlusterFS至少需要两台服务器搭建，服务器配置最好相同，每个服务器两块磁盘，一块是用于安装系统，一块是用于GlusterFS。

192.168.10.10    GlusterFS-master（主节点）    Centos7.4
192.168.10.20    GlusterFS-slave （从节点）    Centos7.4
192.168.10.30    Client          （客户端）
```

## 环境准备

由于GlusterFS需要使用网络，因此还必须事先根据环境设置防火墙规则，关闭SELinux。  
这里我将上面三台服务器的防火墙和Selinux全部关闭

```bash
[root@GlusterFS-master ~]# setenforce 0
[root@GlusterFS-master ~]# getenforce
[root@GlusterFS-master ~]# sed -i 's_SELINUX=enforcing_SELINUX=disabled_g' /etc/sysconfig/selinux
[root@GlusterFS-master ~]# cat /etc/sysconfig/selinux |grep "SELINUX=disabled"
SELINUX=disabled

[root@GlusterFS-master ~]# systemctl stop firewalld
[root@GlusterFS-master ~]# systemctl disable firewalld
[root@GlusterFS-master ~]# firewall-cmd --state
not running
```

```text
由于GlusterFS并没有服务器与元数据等概念，因此所有服务器的设置都相同。首先要做主机名的设置（如果操作时都用ip地址，不使用主机名，那么就不需要做hosts绑定）：
[root@GlusterFS-master ~]# hostnamectl --static set-hostname GlusterFS-master
[root@GlusterFS-master ~]# cat /etc/hostname
GlusterFS-master
[root@GlusterFS-master ~]# vim /etc/hosts
.....
192.168.10.10  GlusterFS-master
192.168.10.20  GlusterFS-slave

[root@GlusterFS-slave ~]# hostnamectl --static set-hostname GlusterFS-slave
[root@GlusterFS-slave ~]# cat /etc/hostname
GlusterFS-slave
[root@GlusterFS-slave ~]# vim /etc/hosts
......
192.168.10.10  GlusterFS-master
192.168.10.20  GlusterFS-slave
```

```text
时钟同步
这个问题是集群内部的时间非常重要，如果服务器间的时间有误差，可能会给集群间的通信带来麻烦，
进而导致集群失效。这里采用网络同步时钟的方法，确保两台服务器的时间一致（时区和时间都要标准、一致）：
[root@GlusterFS-master ~]# yum install -y ntpdate
[root@GlusterFS-master ~]# ntpdate ntp1.aliyun.com
[root@GlusterFS-master ~]# date

[root@GlusterFS-slave ~]# yum install -y ntpdate
[root@GlusterFS-slave ~]# ntpdate ntp1.aliyun.com
[root@GlusterFS-slave ~]# date
```

### 1）安装依赖

（在GlusterFS-master和GlusterFS-slave两台机器上都要操作）

```text
[root@GlusterFS-master ~]# yum install -y flex bison openssl openssl-devel acl libacl libacl-devel sqlite-devel libxml2-devel python-devel make cmake gcc gcc-c++ autoconf automake libtool unzip zip
```

### 2）安装userspace-rcu-master和userspace-rcu-master

\(在GlusterFS-master和GlusterFS-slave两台机器上都要操作）

```text
1）下载glusterfs-3.6.9.tar.gz和userspace-rcu-master.zip
百度云盘下载地址：https://pan.baidu.com/s/1DyKxt0TnO3aNx59mVfJCZA
提取密码：ywq8

将这两个安装包放到/usr/local/src目录下
[root@GlusterFS-master ~]# cd /usr/local/src/
[root@GlusterFS-master src]# ll
total 6444
-rw-r--r--. 1 root root 6106554 Feb 29  2016 glusterfs-3.6.9.tar.gz
-rw-r--r--. 1 root root  490091 Apr  8 09:58 userspace-rcu-master.zip

2）安装userspace-rcu-master
[root@GlusterFS-master src]# unzip /usr/local/src/userspace-rcu-master.zip -d /usr/local/
[root@GlusterFS-master src]# cd /usr/local/userspace-rcu-master/
[root@GlusterFS-master userspace-rcu-master]# ./bootstrap
[root@GlusterFS-master userspace-rcu-master]# ./configure
[root@GlusterFS-master userspace-rcu-master]# make && make install
[root@GlusterFS-master userspace-rcu-master]# ldconfig

3）安装userspace-rcu-master
[root@GlusterFS-master userspace-rcu-master]# tar -zxvf /usr/local/src/glusterfs-3.6.9.tar.gz -C /usr/local/
[root@GlusterFS-master userspace-rcu-master]# cd /usr/local/glusterfs-3.6.9/
[root@GlusterFS-master glusterfs-3.6.9]# ./configure --prefix=/usr/local/glusterfs
[root@GlusterFS-master glusterfs-3.6.9]# make && make install

添加环境变量
[root@GlusterFS-master glusterfs-3.6.9]# vim /etc/profile       //在文件最底部添加如下内容
......
export GLUSTERFS_HOME=/usr/local/glusterfs
export PATH=$PATH:$GLUSTERFS_HOME/sbin

[root@GlusterFS-master glusterfs-3.6.9]# source /etc/profile

4）启动glusterfs
[root@GlusterFS-master ~]# /usr/local/glusterfs/sbin/glusterd
[root@GlusterFS-master ~]# ps -ef|grep glusterd
root       852     1  0 10:14 ?        00:00:00 /usr/local/glusterfs/sbin/glusterd
root       984 26217  0 10:14 pts/1    00:00:00 grep --color=auto glusterd
[root@GlusterFS-master ~]# lsof -i:24007
COMMAND  PID USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
glusterd 852 root    9u  IPv4 123605      0t0  TCP *:24007 (LISTEN)
```

### 3）建立GlusterFS分布式存储集群

（这里选择在GlusterFS-master上操作。其实在任意一个节点上操作都可以）

```text
1）执行以下命令，将192.168.10.20（可以使用ip地址，也可以使用节点的主机名）节点加入到集群，有多少个节点需要加入集群，就执行多少个下面的命令：
[root@GlusterFS-master ~]# gluster peer probe 192.168.10.20
peer probe: success.

2）查看集群状态：
[root@GlusterFS-master ~]# gluster peer status
Number of Peers: 1
Hostname: 192.168.10.20
Uuid: f8e69297-4690-488e-b765-c1c404810d6a
State: Peer in Cluster (Connected)

3）查看 volume 信息（由于还没有创建volume所以显示的是暂无信息）：
[root@GlusterFS-master ~]# gluster volume info
No volumes present

4）创建数据存储目录（在GlusterFS-master和GlusterFS-slave节点上都要操作）
[root@GlusterFS-master ~]# mkdir -p /opt/gluster/data

5）创建复制卷 models，指定刚刚创建的目录（replica 2表明存储2个备份，即有多少个节点就存储多少个备份；后面指定服务器的存储目录）。这里选择创建的是副本卷。
[root@GlusterFS-master ~]# gluster volume create models replica 2 192.168.10.10:/opt/gluster/data 192.168.10.20:/opt/gluster/data force

6）再次查看 volume 信息
[root@GlusterFS-master ~]# gluster volume info

Volume Name: models
Type: Replicate
Volume ID: f1945b0b-67d6-4202-9198-639244ab0a6a
Status: Created
Number of Bricks: 1 x 2 = 2
Transport-type: tcp
Bricks:
Brick1: 192.168.10.10:/opt/gluster/data
Brick2: 192.168.10.20:/opt/gluster/data

7）启动 models
[root@GlusterFS-master ~]# gluster volume start models
```

gluster 性能调优

```text
a）首先开启指定volume的配额
[root@GlusterFS-master ~]# gluster volume quota models enable

b）限制 models 总目录最大使用 5GB 空间（5GB并非绝对，需要根据实际硬盘大小配置）
[root@GlusterFS-master ~]# gluster volume quota models limit-usage / 5GB

c）设置 cache 大小（128MB并非绝对，需要根据实际硬盘大小配置）
[root@GlusterFS-master ~]# gluster volume set models performance.cache-size 128MB

d）开启异步，后台操作
[root@GlusterFS-master ~]# gluster volume set models performance.flush-behind on

e）设置 io 线程 32
[root@GlusterFS-master ~]# gluster volume set models performance.io-thread-count 32

f）设置 回写 (写数据时间，先写入缓存内，再写入硬盘)
[root@GlusterFS-master ~]# gluster volume set models performance.write-behind on

g）查看调优之后的volume信息
[root@GlusterFS-master ~]# gluster volume info

Volume Name: models
Type: Replicate
Volume ID: f1945b0b-67d6-4202-9198-639244ab0a6a
Status: Started
Number of Bricks: 1 x 2 = 2
Transport-type: tcp
Bricks:
Brick1: 192.168.10.10:/opt/gluster/data
Brick2: 192.168.10.20:/opt/gluster/data
Options Reconfigured:
performance.write-behind: on
performance.io-thread-count: 32
performance.flush-behind: on
performance.cache-size: 128MB
features.quota: on
```

### **4）部署客户端并挂载GlusterFS文件系统的bricks（存储单元）**

（在_**Client**_机器上操作）

```bash
到目前为止，GlusterFS分布式存储集群的大部分工作已经做完了，接下来就是挂载一个目录，然后通过对这个挂载目录操作，
实现数据同步至文件系统，然后写文件测试下。

注意一点：
客户端挂载的glusterfs存储里写入的数据都是保存到各节点服务器的存储目录下。
如果节点机故障或其存储目录发生损坏，但是其备份节点正常，则客户端挂载点下的数据就不会损失。
但如果发生故障的节点没有备份节点或者备份节点都发生了故障，则客户端挂载点下的数据就损失了。
如果创建的是分布式卷（即Hash卷，没有备份节点），则如果有一个节点挂了，那么客户端的挂载点下的数据就会损失一部分。

1）安装gluster-client
[root@Client ~]# yum install -y glusterfs glusterfs-fuse

2）建立挂载点目录
[root@Client ~]# mkdir -p /opt/gfsmount

3）挂载GlusterFS
[root@Client ~]# mount -t glusterfs 192.168.10.10:models /opt/gfsmount/

4）检查挂载情况
[root@Client ~]# df -h
Filesystem               Size  Used Avail Use% Mounted on
/dev/mapper/centos-root   38G  4.3G   33G  12% /
devtmpfs                 1.9G     0  1.9G   0% /dev
tmpfs                    1.9G     0  1.9G   0% /dev/shm
tmpfs                    1.9G  8.6M  1.9G   1% /run
tmpfs                    1.9G     0  1.9G   0% /sys/fs/cgroup
/dev/vda1               1014M  143M  872M  15% /boot
/dev/mapper/centos-home   19G   33M   19G   1% /home
tmpfs                    380M     0  380M   0% /run/user/0
overlay                   38G  4.3G   33G  12% /var/lib/docker/overlay2/9904ac8cbcba967de3262dc0d5e230c64ad3c1c53b588048e263767d36df8c1a/merged
shm                       64M     0   64M   0% /var/lib/docker/containers/222ec7f21b2495591613e0d1061e4405cd57f99ffaf41dbba1a98c350cd70f60/mounts/shm
192.168.10.10:models     38G  3.9G   34G  11% /opt/gfsmount

5）测试。分别创建30M、300M的两个大文件，发现速度很快。
[root@Client ~]# time dd if=/dev/zero of=/opt/gfsmount/kevin bs=30M count=1
1+0 records in
1+0 records out
31457280 bytes (31 MB) copied, 0.140109 s, 225 MB/s

real    0m0.152s
user    0m0.001s
sys 0m0.036s

[root@Client ~]# time dd if=/dev/zero of=/opt/gfsmount/grace bs=300M count=1
1+0 records in
1+0 records out
314572800 bytes (315 MB) copied, 1.07577 s, 292 MB/s

real    0m1.106s
user    0m0.001s
sys 0m0.351s

[root@Client ~]# cd /opt/gfsmount/
[root@Client gfsmount]# du -sh *
300M    grace
30M     kevin
[root@Client gfsmount]# mkdir test
[root@Client gfsmount]# ll
total 337924
-rw-r--r--. 1 root root 314572800 Apr  7 22:41 grace
-rw-r--r--. 1 root root  31457280 Apr  7 22:41 kevin
drwxr-xr-x. 2 root root      4096 Apr  7 22:43 test

6）查看集群存储情况（在GlusterFS-master和GlusterFS-slave节点上操作）
[root@GlusterFS-master ~]# cd /opt/gluster/data/
[root@GlusterFS-master data]# ll
total 337920
-rw-r--r--. 2 root root 314572800 Apr  8 10:41 grace
-rw-r--r--. 2 root root  31457280 Apr  8 10:41 kevin
drwxr-xr-x. 2 root root         6 Apr  8 10:43 test

[root@GlusterFS-slave ~]# cd /opt/gluster/data/
[root@GlusterFS-slave data]# ll
total 337920
-rw-r--r--. 2 root root 314572800 Apr  7 22:41 grace
-rw-r--r--. 2 root root  31457280 Apr  7 22:41 kevin
drwxr-xr-x. 2 root root         6 Apr  7 22:43 test
```

**备注**：查看得知gluster服务器的每个节点上都有备份，符合上面步骤，即：创建复制卷 models，指定刚刚创建的目录（replica 2表明存储2个备份）

### **5）GlusterFS相关命令**

```text
1）查看GlusterFS中所有的volume
[root@GlusterFS-master ~]# gluster volume list
models

2）启动磁盘。比如启动名字为 models 的磁盘
[root@GlusterFS-master ~]# gluster volume start models

3）停止磁盘。比如停止名字为 models 的磁盘
[root@GlusterFS-master ~]# gluster volume stop models

4）删除磁盘。比如删除名字为 models 的磁盘
[root@GlusterFS-master ~]# gluster volume delete models

5）验证GlusterFS集群。可以使用下面三个命令
[root@GlusterFS-master ~]# gluster peer status
Number of Peers: 1

Hostname: 192.168.10.20
Uuid: f8e69297-4690-488e-b765-c1c404810d6a
State: Peer in Cluster (Connected)

[root@GlusterFS-master ~]# gluster pool list
UUID                    Hostname        State
f8e69297-4690-488e-b765-c1c404810d6a    192.168.10.20   Connected
5dfd40e2-096b-40b5-bee3-003b57a39007    localhost       Connected

[root@GlusterFS-master ~]# gluster volume status
Status of volume: models
Gluster process                     Port    Online  Pid
------------------------------------------------------------------------------
Brick 192.168.10.10:/opt/gluster/data          49152   Y   1055
Brick 192.168.10.20:/opt/gluster/data          49152   Y   32586
NFS Server on localhost                 N/A N   N/A
Self-heal Daemon on localhost               N/A Y   1074
Quota Daemon on localhost               N/A Y   1108
NFS Server on 192.168.10.20                N/A N   N/A
Self-heal Daemon on 192.168.10.20          N/A Y   32605
Quota Daemon on 192.168.10.20              N/A Y   32614

Task Status of Volume models
------------------------------------------------------------------------------
There are no active volume tasks


6）将节点移出GlusterFS集群，可以批量移除。如下将glusterfs3和glusterfs4两个节点移除集群。
如果是副本卷，移除的节点需要时replica的整数倍。
默认情况下节点是移除不了的,可以使用force强制移除（不建议强制移除节点）。
前提是移除的节点上的brick要移除。
[root@GlusterFS-master ~]# gluster peer detach glusterfs3 glusterfs4  force
或者通过gluster命令的交互模式进行操作：
[root@GlusterFS-master ~]# gluster
gluster> peer detach glusterfs3 glusterfs4  force

7）卷扩容（由于副本数设置为2,至少要添加2（4、6、8..）台机器）。
需要特别注意：如果复制卷或者条带卷，每次添加的Brick节点数必须是replica或者stripe的整数倍。
比如添加glusterfs3、glusterfs4两个节点，并将这两个节点的卷（即）合并，合并后的卷名称为glusterfs_data。
[root@GlusterFS-master ~]# gluster peer probe glusterfs3  
[root@GlusterFS-master ~]# gluster peer probe glusterfs4  
[root@GlusterFS-master ~]# gluster volume add-brick glusterfs_data glusterfs3:/opt/gluster/data glusterfs4:/opt/gluster/data force

8）重新均衡卷（glusterfs_data为卷名）
[root@GlusterFS-master ~]# gluster volume rebalance glusterfs_data start
[root@GlusterFS-master ~]# gluster volume rebalance glusterfs_data status
[root@GlusterFS-master ~]# gluster volume rebalance glusterfs_data stop

均衡卷的前提是至少有两个brick存储单元（即至少3个节点集群）。
上面的例子中，models卷中只有一个brick存储单元，故不能进行均衡卷操作：
[root@GlusterFS-master ~]# gluster volume list
models
[root@GlusterFS-master ~]# gluster volume rebalance models start
volume rebalance: models: failed: Volume models is not a distribute volume or contains only 1 brick.
Not performing rebalance
[root@GlusterFS-master ~]#

9）收缩卷（收缩卷前gluster需要先移动数据到其他位置）（gv0为卷名）。注意，如果是复制卷或者条带卷，则每次移除的Brick数必须是replica或者stripe的整数倍。
[root@GlusterFS-master ~]# gluster volume remove-brick gv0 glusterfs3:/data/brick1/gv0 glusterfs4:/data/brick1/gv0 start      //开始迁移 
[root@GlusterFS-master ~]# gluster volume remove-brick gv0 glusterfs3:/data/brick1/gv0 glusterfs4:/data/brick1/gv0 status     //查看迁移状态 
[root@GlusterFS-master ~]# gluster volume remove-brick gv0 glusterfs3:/data/brick1/gv0 glusterfs4:/data/brick1/gv0 commit     //迁移完成后提交 

10）迁移卷

#将glusterfs3的数据迁移到glusterfs5,先将glusterfs5加入集群
[root@GlusterFS-master ~]# gluster peer probe glusterfs5 

#开始迁移
[root@GlusterFS-master ~]# gluster volume replace-brick gv0 glusterfs3:/data/brick1/gv0 glusterfs5:/data/brick1/gv0 start

#查看迁移状态
[root@GlusterFS-master ~]# gluster volume replace-brick gv0 glusterfs3:/data/brick1/gv0 glusterfs5:/data/brick1/gv0 status

#数据迁移完毕后提交 
[root@GlusterFS-master ~]# gluster volume replace-brick gv0 glusterfs3:/data/brick1/gv0 glusterfs5:/data/brick1/gv0 commit  

#如果机器glusterfs3出现故障已经不能运行,执行强制提交 
[root@GlusterFS-master ~]# gluster volume replace-brick gv0 glusterfs3:/data/brick1/gv0 glusterfs5:/data/brick1/gv0 commit force

#同步整个卷 
[root@GlusterFS-master ~]# gluster volume heal gfs full

11）授权访问。如下授权192.168网段的客户机能访问此glusterfs存储卷。
[root@GlusterFS-master ~]# gluster volume set gfs auth.allow 192.168.*
```

### **6）总结几点**

```text
如上操作后，GlusterFS的分布式存储集群环境已经搭建完成。这里总结几点如下：
1）如果Glusterfs节点机器重启，那么重启后：
   a）glusterFS服务需要启动
   b）磁盘models（即存储卷）需要启动
   c）目录/opt/gfsmount/需要重新挂载
   d）挂载完目录/opt/gfsmount/需要重新进入

2）注意：
两个分区挂到同一个分区，第一个挂的那个不是被覆盖，而是被暂时隐藏。比如：
先挂载的"mount /dev/sda1 /opt/gfsmount/"，接着又挂载的"mount /dev/sda2 /opt/gfsmount/"，
那么/dev/sda1内的就暂时被隐藏，只要"umount /dev/sda2"，把第二个分区卸载了，
在"cd /opt/gfsmount/"就可以看到挂的第一个分区的内容了。

3）gluster的端口
glusterd进程的端口号是24007
glusterfsd进程的端口号是49153

4）创建的六种存储卷
---------------创建复制卷---------------
语法：gluster volume create NEW-VOLNAME [replica COUNT] [transport tcp | rdma | tcp, rdma] NEW-BRICK
示例
[root@GlusterFS-master ~]# gluster volume create test-volume replica 2 transport tcp server1:/exp1/brick server2:/exp2/brick

---------------创建条带卷---------------
语法：gluster volume create NEW-VOLNAME [stripe COUNT] [transport tcp | rdma | tcp, rdma] NEW-BRICK...
示例：
[root@GlusterFS-master ~]# gluster volume create test-volume stripe 2 transport tcp server1:/exp1/brick server2:/exp2/brick

---------------创建分布式卷（即Hash哈希卷）---------------
语法：gluster volume create NEW-VOLNAME [transport tcp | rdma | tcp, rdma] NEW-BRICK
示例1
[root@GlusterFS-master ~]# gluster volume create test-volume server1:/exp1/brick server2:/exp2/brick
示例2
[root@GlusterFS-master ~]# gluster volume create test-volume transport rdma server1:/exp1/brick server2:/exp2/brick server3:/exp3/brick server4:/exp4/brick

---------------分布式复制卷---------------
语法：gluster volume create NEW-VOLNAME [replica COUNT] [transport tcp | rdma | tcp, rdma] NEW-BRICK...
示例：
[root@GlusterFS-master ~]# gluster volume create test-volume replica 2 transport tcp server1:/exp1/brick server2:/exp2/brick server3:/exp3/brick server4:/exp4/brick

---------------分布式条带卷---------------
语法：gluster volume create NEW-VOLNAME [stripe COUNT] [transport tcp | rdma | tcp, rdma] NEW-BRICK...
示例：
[root@GlusterFS-master ~]# gluster volume create test-volume stripe 2 transport tcp server1:/exp1/brick server2:/exp2/brick server3:/exp3/brick server4:/exp4/brick

---------------条带复制卷---------------
语法：gluster volume create NEW-VOLNAME [stripe COUNT] [replica COUNT] [transport tcp | rdma | tcp, rdma] NEW-BRICK...
示例：
[root@GlusterFS-master ~]# gluster volume create test-volume stripe 2 replica 2 transport tcp server1:/exp1/brick server2:/exp2/brick server3:/exp3/brick server4:/exp4/brick


5）查看卷
[root@GlusterFS-master ~]# gluster volume list              //列出集群中的所有卷*/
[root@GlusterFS-master ~]# gluster volume info [all]        //查看集群中的卷信息*/
[root@GlusterFS-master ~]# gluster volume status [all]      //查看集群中的卷状态*/

6）Brick管理
这里以一个例子来说明：把192.168.10.151:/mnt/brick0 替换为192.168.10.151:/mnt/brick2

6.1）开始替换
[root@GlusterFS-slave ~]# gluster volume replace-brick test-volume 192.168.10.151:/mnt/brick0 192.168.10.152:/mnt/brick2 start
异常信息：volume replace-brick: failed: /data/share2 or a prefix of it is already part of a volume

说明 /mnt/brick2 曾经是一个Brick。具体解决方法
[root@GlusterFS-slave ~]# rm -rf /mnt/brick2/.glusterfs

[root@GlusterFS-slave ~]# setfattr -x trusted.glusterfs.volume-id /mnt/brick2
[root@GlusterFS-slave ~]# setfattr -x trusted.gfid  /mnt/brick2

//如上，执行replcace-brick卷替换启动命令，使用start启动命令后，开始将原始Brick的数据迁移到即将需要替换的Brick上。

6.2）查看是否替换完
[root@GlusterFS-slave ~]# gluster volume replace-brick test-volume 192.168.10.151:/mnt/brick0 192.168.10.152:/mnt/brick2 status

6.3）在数据迁移的过程中，可以执行abort命令终止Brick替换。
[root@GlusterFS-slave ~]# gluster volume replace-brick test-volume 192.168.10.151:/mnt/brick0 192.168.10.152:/mnt/brick2 abort

6.4）在数据迁移结束之后，执行commit命令结束任务，则进行Brick替换。使用volume info命令可以查看到Brick已经被替换。
[root@GlusterFS-slave ~]# gluster volume replace-brick test-volume 192.168.10.151:/mnt/brick0 192.168.10.152:/mnt/brick2 commit
此时再往 /sf/data/vs/gfs/rep2上添加数据的话，数据会同步到 192.168.10.152:/mnt/brick0和192.168.10.152:/mnt/brick2上。而不会同步到
192.168.10.151:/mnt/brick0 上。

7）收缩卷
先将数据迁移到其它可用的Brick，迁移结束后才将该Brick移除：
[root@GlusterFS-slave ~]# gluster volume remove-brick <VOLNAME> <BRICK> start

在执行了start之后，可以使用status命令查看移除进度：
[root@GlusterFS-slave ~]# gluster volume remove-brick <VOLNAME> <BRICK> status

不进行数据迁移，直接删除该Brick：
[root@GlusterFS-slave ~]# gluster volume remove-brick <VOLNAME> <BRICK> commit

注意，如果是复制卷或者条带卷，则每次移除的Brick数必须是replica或者stripe的整数倍。

8）迁移卷
使用start命令开始进行迁移：
[root@GlusterFS-slave ~]# gluster volume replace-brick <VOLNAME> <BRICK> <NEW-BRICK> start

在数据迁移过程中，可以使用pause命令暂停迁移：
[root@GlusterFS-slave ~]# gluster volume replace-brick <VOLNAME> <BRICK> <NEW-BRICK> pause

在数据迁移过程中，可以使用abort命令终止迁移：
[root@GlusterFS-slave ~]# gluster volume replace-brick <VOLNAME> <BRICK> <NEW-BRICK> abort

在数据迁移过程中，可以使用status命令查看迁移进度：
[root@GlusterFS-slave ~]# gluster volume replace-brick <VOLNAME> <BRICK> <NEW-BRICK> status

在数据迁移结束后，执行commit命令来进行Brick替换：
[root@GlusterFS-slave ~]# gluster volume replace-brick <VOLNAME> <BRICK> <NEW-BRICK> commit
```

### 7.几个问题

```text
1）重新添加一个曾经交付的brick，报错：
异常信息：volume replace-brick: failed: /data/share2 or a prefix of it is already part of a volume

说明 /mnt/brick2 曾经是一个Brick。具体解决方法　
[root@GlusterFS-slave ~]# rm -rf /mnt/brick2/.glusterfs
[root@GlusterFS-slave ~]# setfattr -x trusted.glusterfs.volume-id /mnt/brick2  // 移除目录的扩展属性
[root@GlusterFS-slave ~]# setfattr -x trusted.gfid  /mnt/brick2

2）在glusterfs挂载点上，操作，出现：传输端点尚未连接
通过 volume info 查看正常。
[root@GlusterFS-slave ~]# ls
ls: 无法打开目录.: 传输端点尚未连接
[root@GlusterFS-slave ~]# df
文件系统             1K-块      已用      可用 已用% 挂载点
/dev/mapper/vg_vclassftydc-lv_root
                      13286512   3914004   8674552  32% /
tmpfs                  2013148         4   2013144   1% /dev/shm
/dev/sda1               487652     31759    426197   7% /boot
/dev/mapper/vg_vclassftydc-lv_var
                      20511356    496752  18949644   3% /var
/dev/mapper/ssd-ssd   20836352   9829584  11006768  48% /mnt/787fe74d9bef93c17a7aa195a05245b3
/dev/mapper/defaultlocal-defaultlocal
                      25567232  10059680  15507552  40% /mnt/ab796251092bf7c1d7657e728940927b
df: "/mnt/glusterfs-mnt": 传输端点尚未连接
[root@GlusterFS-slave ~]# cd ..
-bash: cd: ..: 传输端点尚未连接
[root@GlusterFS-slave ~]#

原因：glusterfs的挂载目录已经umount了。
挂载glusterfs后，一段时间发现被umount，挂载日志如下：
[2017-06-28 05:45:51.861186] I [dht-layout.c:726:dht_layout_dir_mismatch] 0-test-vol2-dht: / - disk layout missing
[2017-06-28 05:45:51.861332] I [dht-common.c:623:dht_revalidate_cbk] 0-test-vol2-dht: mismatching layouts for /
[2017-06-28 05:46:02.499449] I [dht-layout.c:726:dht_layout_dir_mismatch] 0-test-vol2-dht: / - disk layout missing
[2017-06-28 05:46:02.499520] I [dht-common.c:623:dht_revalidate_cbk] 0-test-vol2-dht: mismatching layouts for /
[2017-06-28 05:46:05.701259] I [fuse-bridge.c:4628:fuse_thread_proc] 0-fuse: unmounting /mnt/glusterfs-mnt/
[2017-06-28 05:46:05.701898] W [glusterfsd.c:1002:cleanup_and_exit] (-->/lib64/libc.so.6(clone+0x6d) [0x7f09e546090d] (-->/lib64/libpthread.so.0(+0x7851) [0x7f09e5aff851] (-->/usr/sbin/glusterfs(glusterfs_sigwaiter+0xcd) [0x40533d]))) 0-: received signum (15), shutting down
[2017-06-28 05:46:05.701911] I [fuse-bridge.c:5260:fini] 0-fuse: Unmounting '/mnt/glusterfs-mnt/'.

3）恢复节点配置信息
故障现象: 其中一个节点配置信息不正确
故障模拟:删除server2部分配置信息，配置信息位置:/var/lib/glusterd/
修复方法：触发自修复:通过Gluster工具同步配置信息
[root@GlusterFS-slave ~]# gluster volume sync server1 all

4）复制卷数据不一致
故障现象: 双副本卷数据出现不一致
故障模拟: 删除其中一个brick数据
修复方法：重新mount一下

5）复制卷的目录删除了
修复方法：先替换brick
[root@GlusterFS-slave ~]# gluster volume replace-brick bbs_img 10.20.0.201:/brick1/share2 10.20.0.201:/brick1/share start
[root@GlusterFS-slave ~]# gluster volume replace-brick bbs_img 10.20.0.201:/brick1/share2 10.20.0.201:/brick1/share commit

还原的时候注意会报错
解决办法
[root@GlusterFS-slave ~]# rm -rf /data/share2/.glusterfs
[root@GlusterFS-slave ~]# setfattr -x  trusted.glusterfs.volume-id /data/share2
[root@GlusterFS-slave ~]# setfattr -x trusted.gfid /data/share2

6）移除brick出现如下问题
[root@GlusterFS-slave ~]# volume remove-brick test_vol01 192.168.10.186:/mnt/63ef41a63399e6640a3c4abefa725497 192.168.10.186:/mnt/ad242fbe177ba330a0ea75a9d23fc936 force
Removing brick(s) can result in data loss. Do you want to Continue? (y/n) y
volume remove-brick commit force: failed: One or more nodes do not support the required op-version. Cluster op-version must atleast be 30600.
原因：节点版本不一致造成的

7）gluster 挂载点无法使用，无法umount
[root@GlusterFS-slave ~]# umount /mnt/d1f561a32ac1bf17cf183f36baac34d4
umount: /mnt/d1f561a32ac1bf17cf183f36baac34d4: device is busy.
        (In some cases useful info about processes that use
         the device is found by lsof(8) or fuser(1))
原因：有一个glusterfs  进程正在使用。通过ps 查看 参数中有mount的，kill掉后，即可umount操作。
```

> ### **===========================添加节点操作===========================**

```text
上面案例只是用到了两个节点机，在实际生产环境中，可能用到的节点会更多。下面简单说下追加节点的操作：
由于上面创建的是副本卷，新增加的节点数必须是replica的整数倍，故新增加的节点数至少是两个。

这里新增加两个节点机的信息如下：
192.168.10.204  GlusterFS-slave2
192.168.10.220  GlusterFS-slave3

1）首先在四个几点机器的/etc/hosts里面做好hosts绑定
192.168.10.239  GlusterFS-master
192.168.10.212  GlusterFS-slave
192.168.10.204  GlusterFS-slave2
192.168.10.220  GlusterFS-slave3

2）关闭防火墙，以及同步好系统时间

3）安装上面的操作步骤在新增加的两个节点机上安装安装userspace-rcu-master和userspace-rcu-master

4）在新增加的两个节点机上创建存储目录
# mkdir -p /opt/gluster/data

5）建立GlusterFS集群（在GlusterFS-master机器上操作）
[root@GlusterFS-master ~]# gluster peer probe 192.168.10.204
[root@GlusterFS-master ~]# gluster peer probe 192.168.10.220
---------------------------------------------------------------------
如果出现下面报错：
peer probe: failed: Probe returned with unknown errno 107
产生原因：
目标服务器上的防火墙是否关闭？、glusterd是否启动运行？能否ping的通？
---------------------------------------------------------------------

[root@GlusterFS-master ~]# gluster peer status         //在每个节点机上都可以执行这个查看命令
Number of Peers: 3

Hostname: 192.168.10.212
Uuid: f8e69297-4690-488e-b765-c1c404810d6a
State: Peer in Cluster (Connected)

Hostname: 192.168.10.204
Uuid: a989394c-f64a-40c3-8bc5-820f623952c4
State: Peer in Cluster (Connected)

Hostname: 192.168.10.220
Uuid: dd99743a-285b-4aed-b3d6-e860f9efd965
State: Peer in Cluster (Connected)

[root@GlusterFS-master ~]# gluster volume info        //在每个节点机上都可以执行这个查看命令

Volume Name: models
Type: Replicate
Volume ID: f1945b0b-67d6-4202-9198-639244ab0a6a
Status: Started
Number of Bricks: 1 x 2 = 2
Transport-type: tcp
Bricks:
Brick1: 192.168.10.239:/opt/gluster/data
Brick2: 192.168.10.212:/opt/gluster/data
Options Reconfigured:
performance.write-behind: on
performance.io-thread-count: 32
performance.flush-behind: on
performance.cache-size: 128MB
features.quota: on


6）卷扩容（即将新添加的两个节点的brick添加到上面的models磁盘里）

首先将Client客户机上之前的glusterfs挂载卸载掉
[root@Client ~]# umount /opt/gfsmount

然后将GlusterFS-master节点上的models关闭
[root@GlusterFS-master ~]# gluster volume stop models
[root@GlusterFS-master ~]# gluster volume status models

然后执行卷扩容操作
[root@GlusterFS-master ~]# gluster volume add-brick models 192.168.10.204:/opt/gluster/data 192.168.10.220:/opt/gluster/data force

查看volume 信息，发现新节点已经加入进去了
[root@GlusterFS-master ~]# gluster volume info
Volume Name: models
Type: Distributed-Replicate
Volume ID: f1945b0b-67d6-4202-9198-639244ab0a6a
Status: Stopped
Number of Bricks: 2 x 2 = 4
Transport-type: tcp
Bricks:
Brick1: 192.168.10.239:/opt/gluster/data
Brick2: 192.168.10.212:/opt/gluster/data
Brick3: 192.168.10.204:/opt/gluster/data
Brick4: 192.168.10.220:/opt/gluster/data
Options Reconfigured:
performance.write-behind: on
performance.io-thread-count: 32
performance.flush-behind: on
performance.cache-size: 128MB
features.quota: on

然后在GlusterFS-master重新启动models磁盘
[root@GlusterFS-master ~]# gluster volume start models
volume start: models: success
[root@GlusterFS-master ~]# gluster volume status models
Status of volume: models
Gluster process           Port  Online  Pid
------------------------------------------------------------------------------
Brick 192.168.10.239:/opt/gluster/data      49152 Y 5405
Brick 192.168.10.212:/opt/gluster/data      49152 Y 2665
Brick 192.168.10.204:/opt/gluster/data      49152 Y 8788
Brick 192.168.10.220:/opt/gluster/data      49152 Y 12117
NFS Server on localhost         N/A N N/A
Self-heal Daemon on localhost       N/A Y 5426
Quota Daemon on localhost       N/A Y 5431
NFS Server on 192.168.10.212        N/A N N/A
Self-heal Daemon on 192.168.10.212      N/A Y 2684
Quota Daemon on 192.168.10.212        N/A Y 2691
NFS Server on 192.168.10.204        N/A N N/A
Self-heal Daemon on 192.168.10.204      N/A Y 8807
Quota Daemon on 192.168.10.204        N/A Y 8814
NFS Server on 192.168.10.220        N/A N N/A
Self-heal Daemon on 192.168.10.220      N/A Y 12136
Quota Daemon on 192.168.10.220        N/A Y 12143

Task Status of Volume models
------------------------------------------------------------------------------
There are no active volume tasks


接着在Client客户机重新挂载glusterfs
[root@Client ~]# mount -t glusterfs 192.168.10.239:models /opt/gfsmount/
[root@Client ~]# df -h
Filesystem               Size  Used Avail Use% Mounted on
.......
192.168.10.239:models     75G  6.0G   69G   8% /opt/gfsmount

然后进行重新均衡。均衡卷的前提是至少有两个brick存储单元（即至少3个节点集群）。
[root@GlusterFS-master data]# gluster volume rebalance models start
volume rebalance: models: success: Initiated rebalance on volume models.
Execute "gluster volume rebalance <volume-name> status" to check status.
ID: 2ff550ae-043d-457a-8af6-873d9f4ce7ee

最后再Client机器上进行测试：
注意：
1）新添加到models卷里的节点的存储目录里不会有之前其他节点的数据，只会有新写入的数据。
2）由于上面创建副本卷的时候，指定的副本是2个（即replica 2），所以新写入的数据只会均衡地写入到其中的两个节点的存储目录下，不会四个节点的存储目录都写入的。

在客户机上做测试，发现：
1）在glusterfs挂载目录里创建的文件，只会同步到其中的两个节点上。如果是小文件，会明显固定同步到两个节点上；如果是大文件，每次会同步到不同的两个节点上。
2）在glusterfs挂载目录里创建的目录，会同步到所有的节点上。但是在该目录下创建文件，只会同步到其中的两个节点上。如果是小文件，会明显固定同步到两个节点上；
   如果是大文件，每次会同步到不同的两个节点上。

也可以在GlusterFS-master上执行"重新均衡卷"的操作。均衡卷执行后，首先会将所有节点的存储目录数据保持一致（以最多数据的那个节点为准，进行同步一致）。然后重新测试数据，测试的效果跟上面描述的一样。
[root@GlusterFS-master data]# gluster volume rebalance models start
```

