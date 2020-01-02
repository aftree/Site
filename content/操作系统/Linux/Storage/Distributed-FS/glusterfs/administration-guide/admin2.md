# Admin2

## Gluster设置允许可信任客户端IP

```text
设置只允许192.168.1.*的访问
[root@GlusterFS-master ~]# gluster volume set gluster_share auth.allow 192.168.1.*
volume set: success

查看卷信息
[root@GlusterFS-master ~]# gluster volume info
Volume Name: gluster_share
Type: Distributed-Replicate
Volume ID: a9f989bd-7edd-4089-836a-d9f742b8d37a
Status: Started
Number of Bricks: 2 x 2 = 4
Transport-type: tcp
Bricks:
Brick1: 192.168.10.239:/data/gluster
Brick2: 192.168.10.212:/data/gluster
Brick3: 192.168.10.204:/data/gluster
Brick4: 192.168.10.220:/data/gluster
Options Reconfigured:
auth.allow: 192.168.1.*

注意上面最后一行卷信息，说明只允许客户端ip为192.168.10.*网段的机器挂载。

然后在192.168.10.213客户端进行挂载，发现就挂载不上了
[root@Client ~]# mount -t glusterfs 192.168.10.239:gluster_share /opt/gfsmount/
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

接着修改授权信息
[root@GlusterFS-master ~]# gluster volume set gluster_share auth.allow 192.168.10.*
volume set: success
[root@GlusterFS-master ~]# gluster volume info

Volume Name: gluster_share
Type: Distributed-Replicate
Volume ID: a9f989bd-7edd-4089-836a-d9f742b8d37a
Status: Started
Number of Bricks: 2 x 2 = 4
Transport-type: tcp
Bricks:
Brick1: 192.168.10.239:/data/gluster
Brick2: 192.168.10.212:/data/gluster
Brick3: 192.168.10.204:/data/gluster
Brick4: 192.168.10.220:/data/gluster
Options Reconfigured:
auth.allow: 192.168.10.*

然后再在192.168.10.213的客户端进行挂载测试，发现能挂载上了
[root@Client ~]# mount -t glusterfs 192.168.10.239:gluster_share /opt/gfsmount/
[root@Client ~]# df -h
Filesystem                    Size  Used Avail Use% Mounted on
/dev/mapper/centos-root        38G  4.3G   33G  12% /
devtmpfs                      1.9G     0  1.9G   0% /dev
tmpfs                         1.9G     0  1.9G   0% /dev/shm
tmpfs                         1.9G  8.6M  1.9G   1% /run
tmpfs                         1.9G     0  1.9G   0% /sys/fs/cgroup
/dev/vda1                    1014M  143M  872M  15% /boot
/dev/mapper/centos-home        19G   33M   19G   1% /home
tmpfs                         380M     0  380M   0% /run/user/0
overlay                        38G  4.3G   33G  12% /var/lib/docker/overlay2/9904ac8cbcba967de3262dc0d5e230c64ad3c1c53b588048e263767d36df8c1a/merged
shm                            64M     0   64M   0% /var/lib/docker/containers/222ec7f21b2495591613e0d1061e4405cd57f99ffaf41dbba1a98c350cd70f60/mounts/shm
192.168.10.239:gluster_share  2.0G   67M  2.0G   4% /opt/gfsmount
```

## Gluster的性能测试工具

```text
在一个节点起服务。比如这里在GlusterFS-master节点上启动：　　
[root@GlusterFS-master ~]# yum install -y iperf
[root@GlusterFS-master ~]# iperf -s
------------------------------------------------------------
Server listening on TCP port 5001
TCP window size: 85.3 KByte (default)
------------------------------------------------------------

客户端连接上面的GlusterFS-master节点服务器，测试网络速度
[root@Client ~]# yum install -y iperf
[root@Client ~]# iperf -c 192.168.10.239
------------------------------------------------------------
Client connecting to 192.168.10.239, TCP port 5001
TCP window size: 85.0 KByte (default)
------------------------------------------------------------
[  3] local 192.168.10.213 port 55276 connected with 192.168.10.239 port 5001
[ ID] Interval       Transfer     Bandwidth
[  3]  0.0-10.0 sec  19.5 GBytes  16.7 Gbits/sec


节点服务器上也能查看到信息。因为是虚拟机环境，这里虚高了。
[root@GlusterFS-master ~]# iperf -s
------------------------------------------------------------
Server listening on TCP port 5001
TCP window size: 85.3 KByte (default)
------------------------------------------------------------
[  4] local 192.168.10.239 port 5001 connected with 192.168.10.213 port 55276
[ ID] Interval       Transfer     Bandwidth
[  4]  0.0-10.0 sec  19.5 GBytes  16.7 Gbits/sec


如果觉得压力不够，可以客户端多个进程一起发包。使用-P参数 ，客户端结果如下
[root@Client ~]# iperf -c 192.168.10.239  -P 10
------------------------------------------------------------
Client connecting to 192.168.10.239, TCP port 5001
TCP window size: 85.0 KByte (default)
------------------------------------------------------------
[ 12] local 192.168.10.213 port 55296 connected with 192.168.10.239 port 5001
[  6] local 192.168.10.213 port 55284 connected with 192.168.10.239 port 5001
[  7] local 192.168.10.213 port 55286 connected with 192.168.10.239 port 5001
[  3] local 192.168.10.213 port 55278 connected with 192.168.10.239 port 5001
[  5] local 192.168.10.213 port 55282 connected with 192.168.10.239 port 5001
[  4] local 192.168.10.213 port 55280 connected with 192.168.10.239 port 5001
[  8] local 192.168.10.213 port 55288 connected with 192.168.10.239 port 5001
[  9] local 192.168.10.213 port 55290 connected with 192.168.10.239 port 5001
[ 11] local 192.168.10.213 port 55294 connected with 192.168.10.239 port 5001
[ 10] local 192.168.10.213 port 55292 connected with 192.168.10.239 port 5001
[ ID] Interval       Transfer     Bandwidth
[ 12]  0.0-10.0 sec  3.18 GBytes  2.73 Gbits/sec
[ 11]  0.0-10.0 sec   616 MBytes   516 Mbits/sec
[ 10]  0.0-10.0 sec  3.26 GBytes  2.80 Gbits/sec
[  6]  0.0-10.0 sec  3.18 GBytes  2.72 Gbits/sec
[  7]  0.0-10.0 sec  3.18 GBytes  2.73 Gbits/sec
[  3]  0.0-10.0 sec   616 MBytes   516 Mbits/sec
[  4]  0.0-10.0 sec  3.07 GBytes  2.63 Gbits/sec
[  8]  0.0-10.0 sec  2.90 GBytes  2.49 Gbits/sec
[  9]  0.0-10.0 sec  2.89 GBytes  2.48 Gbits/sec
[  5]  0.0-10.0 sec  3.06 GBytes  2.62 Gbits/sec
[SUM]  0.0-10.0 sec  25.9 GBytes  22.2 Gbits/sec


节点服务器上的信息：
[root@GlusterFS-master ~]# iperf -s
------------------------------------------------------------
Server listening on TCP port 5001
TCP window size: 85.3 KByte (default)
------------------------------------------------------------
[  4] local 192.168.10.239 port 5001 connected with 192.168.10.213 port 55276
[ ID] Interval       Transfer     Bandwidth
[  4]  0.0-10.0 sec  19.5 GBytes  16.7 Gbits/sec
[  4] local 192.168.10.239 port 5001 connected with 192.168.10.213 port 55278
[  5] local 192.168.10.239 port 5001 connected with 192.168.10.213 port 55280
[  7] local 192.168.10.239 port 5001 connected with 192.168.10.213 port 55284
[  6] local 192.168.10.239 port 5001 connected with 192.168.10.213 port 55282
[  8] local 192.168.10.239 port 5001 connected with 192.168.10.213 port 55286
[  9] local 192.168.10.239 port 5001 connected with 192.168.10.213 port 55288
[ 10] local 192.168.10.239 port 5001 connected with 192.168.10.213 port 55290
[ 13] local 192.168.10.239 port 5001 connected with 192.168.10.213 port 55294
[ 11] local 192.168.10.239 port 5001 connected with 192.168.10.213 port 55292
[ 12] local 192.168.10.239 port 5001 connected with 192.168.10.213 port 55296
[ 13]  0.0-10.5 sec   616 MBytes   491 Mbits/sec
[  4]  0.0-10.5 sec   616 MBytes   491 Mbits/sec
[  5]  0.0-10.5 sec  3.07 GBytes  2.50 Gbits/sec
[  7]  0.0-10.5 sec  3.18 GBytes  2.59 Gbits/sec
[  8]  0.0-10.5 sec  3.18 GBytes  2.59 Gbits/sec
[  9]  0.0-10.5 sec  2.90 GBytes  2.37 Gbits/sec
[  6]  0.0-10.5 sec  3.06 GBytes  2.49 Gbits/sec
[ 10]  0.0-10.5 sec  2.89 GBytes  2.35 Gbits/sec
[ 11]  0.0-10.5 sec  3.26 GBytes  2.65 Gbits/sec
[ 12]  0.0-10.5 sec  3.18 GBytes  2.59 Gbits/sec
[SUM]  0.0-10.5 sec  25.9 GBytes  21.1 Gbits/sec

-----------------------------------------------------
dd工具测试：客户端写入速度和读取速度测试

[root@Client ~]# mount -t glusterfs 192.168.10.239:gluster_share /opt/gfsmount

[root@Client ~]# df -h
.......
192.168.10.239:gluster_share  2.0G   68M  2.0G   4% /opt/gfsmount

测试写文件的速度（写入一个300M文件的速度）
[root@Client ~]# dd if=/dev/zero of=/opt/gfsmount/grace bs=1M count=500
500+0 records in
500+0 records out
524288000 bytes (524 MB) copied, 0.981631 s, 534 MB/s

[root@Client ~]# du -sh /opt/gfsmount/grace
500M  /opt/gfsmount/grace

测试读文件的速度
[root@Client ~]# dd if=/opt/gfsmount/grace of=/dev/null bs=1M count=500
500+0 records in
500+0 records out
524288000 bytes (524 MB) copied, 1.00698 s, 521 MB/s

再次测试，虚拟机不是很稳定
[root@Client ~]# dd if=/dev/zero of=/opt/gfsmount/grace1 bs=1M count=500
500+0 records in
500+0 records out
524288000 bytes (524 MB) copied, 1.15882 s, 452 MB/s

[root@Client ~]# dd if=/opt/gfsmount/grace1 of=/dev/null bs=1M count=500
500+0 records in
500+0 records out
524288000 bytes (524 MB) copied, 1.0682 s, 491 MB/s
```

## Gluster集群典型故障处理

```text
1）复制卷数据不一致
故障现象：双副本卷数据出现不一致
故障模拟：删除其中一个brick数据
修复方法：访问文件触发自修复：

[root@GlusterFS-master ~]# gluster volume info

Volume Name: gluster_share
Type: Distributed-Replicate
Volume ID: a9f989bd-7edd-4089-836a-d9f742b8d37a
Status: Started
Number of Bricks: 2 x 2 = 4
Transport-type: tcp
Bricks:
Brick1: 192.168.10.239:/data/gluster
Brick2: 192.168.10.212:/data/gluster
Brick3: 192.168.10.204:/data/gluster
Brick4: 192.168.10.220:/data/gluster
Options Reconfigured:

客户端操作：
[root@Client ~]# df -h
.......
192.168.10.239:gluster_share  2.0G  1.1G  961M  53% /opt/gfsmount
[root@Client ~]# rm -rf /opt/gfsmount/*
[root@Client ~]# cd /opt/gfsmount/
[root@Client gfsmount]# touch a b c d e f g

客户端写入的数据分为两个replica放在了四个节点上
[root@GlusterFS-master ~]# ls /data/gluster/
a  b  c  e
[root@GlusterFS-slave ~]# ls /data/gluster/
a  b  c  e

[root@GlusterFS-slave2 ~]# ls /data/gluster/
d  f  g
[root@GlusterFS-slave3 ~]# ls /data/gluster/
d  f  g

模拟问题：
在GlusterFS-slave2机器上删除文件
[root@GlusterFS-slave2 ~]# cd /data/gluster/
[root@GlusterFS-slave2 gluster]# ls
d  f  g
[root@GlusterFS-slave2 gluster]# rm -rf d
[root@GlusterFS-slave2 gluster]# rm -rf f
[root@GlusterFS-slave2 gluster]# ls
g

GlusterFS-slave2的备份节点GlusterFS-slave3上有上面删除的数据
[root@GlusterFS-slave3 ~]# cd /data/gluster/
[root@GlusterFS-slave3 gluster]# ls
d  f  g

客户端上访问文件可以触发文件的自动修复
[root@Client ~]# cd /opt/gfsmount/
[root@Client gfsmount]# ls
a  b  c  d  e  f  g
[root@Client gfsmount]# cat d
[root@Client gfsmount]# cat f

再次到GlusterFS-slave2节点上查看，删除的数据就自动修复了
[root@GlusterFS-slave2 ~]# ls /data/gluster/
d  f  g

2）glusterfs集群节点配置信息不正确
故障模拟
删除server2部分配置信息
配置信息位置：/usr/local/glusterfs/var/lib/glusterd

修复方法
触发自修复：通过Gluster工具同步配置信息
gluster volume sync server1 all

恢复复制卷 brick
故障现象：双副本卷中一个brick损坏
恢复流程：
a）重新建立故障brick目录
# setfattr -n trusted.gfid -v
0x00000000000000000000000000000001 /data2
# setfattr -n trusted.glusterfs.dht -v
0x000000010000000000000000ffffffff /data2
# setfattr -n trusted.glusterfs.volume-id -v
0xcc51d546c0af4215a72077ad9378c2ac /data2
-v 的参数设置成你的值
b）设置扩展属性(参考另一个复制 brick)
c）重启 glusterd服务
d）触发数据自修复
# find /data/glusterd -type f -print0 | xargs -0 head -c1 >/dev/null

模拟删除brick的操作　
[root@GlusterFS-master ~]# gluster volume info
Volume Name: gluster_share
Type: Distributed-Replicate
Volume ID: a9f989bd-7edd-4089-836a-d9f742b8d37a
Status: Started
Number of Bricks: 2 x 2 = 4
Transport-type: tcp
Bricks:
Brick1: 192.168.10.239:/data/gluster
Brick2: 192.168.10.212:/data/gluster
Brick3: 192.168.10.204:/data/gluster
Brick4: 192.168.10.220:/data/gluster
Options Reconfigured:

在GlusterFS-slave节点上删除brick数据
[root@GlusterFS-slave ~]# ls /data/gluster/
a  b  c  e
[root@GlusterFS-slave ~]# rm -rf /data/gluster
[root@GlusterFS-slave ~]# ll /data/gluster
ls: cannot access /data/gluster: No such file or directory

接在在GlusterFS-slave的备份节点GlusterFS-master上操作，获取扩展属性（使用"yum search getfattr"命令getfattr工具的安装途径）
[root@GlusterFS-master ~]# yum install -y attr.x86_64
[root@GlusterFS-master ~]# cd /data/
[root@GlusterFS-master data]# getfattr -d -m . -e hex gluster/
# file: gluster/
security.selinux=0x756e636f6e66696e65645f753a6f626a6563745f723a756e6c6162656c65645f743a733000
trusted.gfid=0x00000000000000000000000000000001
trusted.glusterfs.dht=0x0000000100000000000000007ffffc25
trusted.glusterfs.volume-id=0xa9f989bd7edd4089836ad9f742b8d37a

注意下面的操作将根据上面的属性信息中的id进行操作

重新建立故障brick目录
恢复操作，扩展属性可以从GlusterFS-master节点机上获取的复制（注意上面属性中的id：0xa9f989bd7edd4089836ad9f742b8d37a），执行顺序没关系
[root@GlusterFS-slave ~]# mkdir /data/gluster
[root@GlusterFS-slave ~]# yum install -y attr.x86_64
[root@GlusterFS-slave ~]# getfattr -d -m . -e hex /data/gluster
getfattr: Removing leading '/' from absolute path names
# file: data/gluster
security.selinux=0x756e636f6e66696e65645f753a6f626a6563745f723a756e6c6162656c65645f743a733000
[root@GlusterFS-slave ~]# setfattr -n trusted.glusterfs.volume-id -v 0xa9f989bd7edd4089836ad9f742b8d37a /data/gluster
[root@GlusterFS-slave ~]# getfattr -d -m . -e hex /data/gluster
getfattr: Removing leading '/' from absolute path names
# file: data/gluster
security.selinux=0x756e636f6e66696e65645f753a6f626a6563745f723a756e6c6162656c65645f743a733000
trusted.glusterfs.volume-id=0xa9f989bd7edd4089836ad9f742b8d37a
[root@GlusterFS-slave ~]# setfattr -n trusted.gfid -v 0x00000000000000000000000000000001 /data/gluster
[root@GlusterFS-slave ~]# setfattr -n trusted.glusterfs.dht -v 0x0000000100000000000000007ffffc25 /data/gluster
[root@GlusterFS-slave ~]# getfattr -d -m . -e hex /data/gluster
getfattr: Removing leading '/' from absolute path names
# file: data/gluster
security.selinux=0x756e636f6e66696e65645f753a6f626a6563745f723a756e6c6162656c65645f743a733000
trusted.gfid=0x00000000000000000000000000000001
trusted.glusterfs.dht=0x0000000100000000000000007ffffc25
trusted.glusterfs.volume-id=0xa9f989bd7edd4089836ad9f742b8d37a

重启gluster服务
[root@GlusterFS-slave ~]# ps -ef|grep gluster
root      4909     1  0 15:12 ?        00:00:01 /usr/local/glusterfs/sbin/glusterfs -s localhost --volfile-id gluster/glustershd -p /usr/local/glusterfs/var/lib/glusterd/glustershd/run/glustershd.pid -l /usr/local/glusterfs/var/log/glusterfs/glustershd.log -S /var/run/f9f65f2dbb0c193ecab167839d75699e.socket --xlator-option *replicate*.node-uuid=f8e69297-4690-488e-b765-c1c404810d6a
root      5069  5044  0 17:31 pts/0    00:00:00 grep --color=auto gluster
root     32450     1  0 Apr08 ?        00:00:26 /usr/local/glusterfs/sbin/glusterd
[root@GlusterFS-slave ~]# ps -ef|grep gluster|awk '{print $2}'|xargs kill -9
kill: sending signal to 5071 failed: No such process
[root@GlusterFS-slave ~]# ps -ef|grep gluster
root      5078  5044  0 17:32 pts/0    00:00:00 grep --color=auto gluster
[root@GlusterFS-slave ~]# /usr/local/glusterfs/sbin/glusterd
[root@GlusterFS-slave ~]# ps -ef|grep gluster
root      5080     1 14 17:32 ?        00:00:00 /usr/local/glusterfs/sbin/glusterd
root      5212  5044  0 17:32 pts/0    00:00:00 grep --color=auto gluster

[root@GlusterFS-slave ~]# ls /data/gluster/
[root@GlusterFS-slave ~]#

可以尝试通过下面三种方法进行数据恢复
a）重启gluster服务后，Gluster-slave节点的数据。如果没有恢复，那么就尝试第2种方法
b）在客户端的挂载点下cat那些删除的文件或者再写入新数据，去触发自动修复。如果还没有恢复。那么尝试第3种方法
c）重启gluster_share卷
[root@GlusterFS-master ~]# gluster volume stop gluster_share
Stopping volume will make its data inaccessible. Do you want to continue? (y/n) y
volume stop: gluster_share: success

[root@GlusterFS-master ~]# gluster volume start gluster_share
volume start: gluster_share: success

[root@GlusterFS-master ~]# gluster volume info

Volume Name: gluster_share
Type: Distributed-Replicate
Volume ID: a9f989bd-7edd-4089-836a-d9f742b8d37a
Status: Started
Number of Bricks: 2 x 2 = 4
Transport-type: tcp
Bricks:
Brick1: 192.168.10.239:/data/gluster
Brick2: 192.168.10.212:/data/gluster
Brick3: 192.168.10.204:/data/gluster
Brick4: 192.168.10.220:/data/gluster

[root@GlusterFS-master ~]# gluster volume status gluster_share
Status of volume: gluster_share
Gluster process           Port  Online  Pid
------------------------------------------------------------------------------
Brick 192.168.10.239:/data/gluster      49155 Y 15508
Brick 192.168.10.212:/data/gluster      49156 Y 5249
Brick 192.168.10.204:/data/gluster      49156 Y 12162
Brick 192.168.10.220:/data/gluster      49157 Y 17402
NFS Server on localhost         N/A N N/A
Self-heal Daemon on localhost       N/A Y 15527
NFS Server on 192.168.10.204        N/A N N/A
Self-heal Daemon on 192.168.10.204      N/A Y 12181
NFS Server on 192.168.10.220        N/A N N/A
Self-heal Daemon on 192.168.10.220      N/A Y 17421
NFS Server on 192.168.10.212        N/A N N/A
Self-heal Daemon on 192.168.10.212      N/A Y 5268

Task Status of Volume gluster_share
------------------------------------------------------------------------------
Task                 : Rebalance          
ID                   : 26035833-3c20-4822-b065-7a5e15d30b85
Status               : completed

[root@GlusterFS-master ~]# gluster volume heal gluster_share info
Brick GlusterFS-master:/data/gluster/
Number of entries: 0

Brick GlusterFS-slave:/data/gluster/
Number of entries: 0

Brick GlusterFS-slave2:/data/gluster/
Number of entries: 0

Brick GlusterFS-slave3:/data/gluster/
Number of entries: 0

查看下数据是否恢复了
[root@GlusterFS-slave ~]# cd /data/gluster/
[root@GlusterFS-slave gluster]# ls
a  b  c

注意：
如上在客户端挂载点写入新数据后，GlusterFS-slave节点的数据就会恢复，如果发现恢复后的数据跟它的备份节点
GlusterFS—master的数据不一致，这个时候只需要在客户端挂载点下cat那些不一致的文件，即触发自动修复机制，
则GlusterFS-slave节点就会自动恢复那些不一致的数据！
```

## glusterfs集群生产场景调优精要

```text
系统关键考虑
1）性能需求
2）Read/Write
3）吞吐量/IOPS/可用性
4）Workload
5）什么应用？
6）大文件？
7）小文件？
8）除了吞吐量之外的需求？

系统配置
1）根据Workload选择适当的 Volume类型
2）Volume类型
3）DHT – 高性能，无冗余
4）AFR – 高可用，读性能高
5）STP – 高并发读，写性能低，无冗余
6）协议/性能
7）Native – 性能最优
8）NFS – 特定应用下可获得最优性能
9）CIFS – 仅Windows平台使用
10）数据流
11）不同访问协议的数据流差异
12）hash+复制卷的模式是生产必须的

系统硬件配置
1）节点和集群配置
2）多 CPU－支持更多的并发线程
3）多 MEM－支持更大的 Cache
4）多网络端口－支持更高的吞吐量
5）专用后端网络用于集群内部通信
6）NFS/CIFS协议访问需要专用后端网络
7）推荐至少10GbE
8）Native协议用于内部节点通信

性能相关经验
1）GlusterFS性能很大程度上依赖硬件
2）充分理解应用基础上进行硬件配置
3）缺省参数主要用于通用目的
4）GlusterFS存在若干性能调优参数
5）性能问题应当优先排除磁盘和网络故障
6）建议6到8个磁盘最做一个raid

系统规模和架构
1）性能理论上由硬件配置决定
2）CPU/Mem/Disk/Network
3）系统规模由性能和容量需求决定
4）2U/4U存储服务器和JBOD适合构建Brick
5）三种典型应用部署
6）容量需求应用
7）2U/4U存储服务器+多个JBOD
8）CPU/RAM/Network要求低
9）性能和容量混合需求应用
10）2U/4U存储服务器+少数JBOD
11）高 CPU/RAM，低Network
12）性能需求应用
13）1U/2U存储服务器(无JBOD)
14）高 CPU/RAM，快DISK/Network

系统调优
1）关键调优参数
2）Performance.write-behind-window-size 65535 (字节)
3）Performance.cache-refresh-timeout 1 (秒)
4）Performance.cache-size 1073741824 (字节)
5）Performance.read-ahead off (仅1GbE)
6）Performance.io-thread-count 24 (CPU核数)
7）Performance.client-io-threads on (客户端)
8）performance.write-behind on
9）performance.flush-behind on
10）cluster.stripe-block-size 4MB (缺省 128KB)
11）Nfs.disable off (缺省打开)
12）缺省参数设置适用于混合workloads
13）不同应用调优
14）理解硬件/固件配置及对性能的影响
15）如CPU频率、 IB、 10GbE、 TCP offload

KVM优化
1）使用 QEMU-GlusterFS(libgfapi)整合方案
2）gluster volume set <volume> group virt
3）tuned-adm profile rhs-virtualization
4）KVM host: tuned-adm profile virtual-host
5）Images和应用数据使用不同的 volume
6）每个gluster节点不超过2个KVM Host (16 guest/host)
7）提高响应时间
8）减少/sys/block/vda/queue/nr_request
9）Server/Guest： 128/8 (缺省企值256/128)
10）提高读带宽
11）提高 /sys/block/vda/queue/read_ahead_kb
12）VM readahead： 4096 (缺省值128)
```

