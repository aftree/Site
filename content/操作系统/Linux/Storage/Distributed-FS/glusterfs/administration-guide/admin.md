# Admin

接着上一篇[Centos7下GlusterFS分布式存储集群环境部署记录](http://www.cnblogs.com/kevingrace/p/8743812.html)文档，继续做一些补充记录，希望能加深对GlusterFS存储操作的理解和熟悉度。

## 清理glusterfs存储环境

```text
由上面可知，该glusterfs存储集群有四个节点：
[root@GlusterFS-master ~]# cat /etc/hosts
.......
192.168.10.239  GlusterFS-master
192.168.10.212  GlusterFS-slave
192.168.10.204  GlusterFS-slave2
192.168.10.220  GlusterFS-slave3

现将四个节点的存储目录/opt/gluster/data全部删除
[root@GlusterFS-master ~]# rm -rf /opt/gluster
[root@GlusterFS-slave ~]# rm -rf  /opt/gluster
[root@GlusterFS-slave2 ~]# rm -rf  /opt/gluster
[root@GlusterFS-slave3 ~]# rm -rf  /opt/gluster

[root@GlusterFS-master ~]# gluster volume list
models

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

接着删除之前创建的models卷
[root@GlusterFS-master ~]# gluster volume stop models
[root@GlusterFS-master ~]# gluster volume delete models

[root@GlusterFS-master ~]# gluster volume info
No volumes present

查看集群节点情况，如下发现glusterfs集群中有三个节点。
由于是在192.168.10.239机器上查看的，所以在集群中默认看不到自己的。
如果在其他集群上执行这个命令，就能查看到192.168.10.239这个节点了
[root@GlusterFS-master ~]# gluster peer status 
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

然后分别将节点从集群中删除（不能在节点机本机的gluster命令下删除自己）
[root@GlusterFS-master ~]# gluster                               //可以在gluster的交互界面里操作
gluster> peer detach 192.168.10.220
peer detach: success
gluster> peer detach 192.168.10.204
peer detach: success
gluster> peer detach 192.168.10.212
peer detach: success
gluster> peer detach 192.168.10.239
peer detach: failed: 192.168.10.239 is localhost                //默认在本机是删除不了自己的。需要在别的节点上删除它。
gluster>

登录另一台节点机上，执行将192.168.10.220节点从集群中移除的操作
[root@GlusterFS-slave ~]# gluster
gluster> peer detach 192.168.10.239
peer detach: success
gluster>

再次查看集群情况，发现没有节点了
[root@GlusterFS-master ~]# gluster peer status
Number of Peers: 0

可以在gluster交互界面里执行它的所有相关命令：
[root@GlusterFS-master ~]# gluster
gluster> volume info
No volumes present
gluster> peer status
Number of Peers: 0
gluster>
```

## dd命令创建虚拟分区，创建存储目录

```text
首先利用dd命令创建虚拟分区，创建存储目录
[root@GlusterFS-master ~]# df -h
Filesystem               Size  Used Avail Use% Mounted on
/dev/mapper/centos-root   36G  1.8G   34G   5% /
devtmpfs                 2.9G     0  2.9G   0% /dev
tmpfs                    2.9G     0  2.9G   0% /dev/shm
tmpfs                    2.9G  8.5M  2.9G   1% /run
tmpfs                    2.9G     0  2.9G   0% /sys/fs/cgroup
/dev/vda1               1014M  143M  872M  15% /boot
/dev/mapper/centos-home   18G   33M   18G   1% /home
tmpfs                    581M     0  581M   0% /run/user/0

dd命令创建一个虚拟分区出来，格式化并挂载到/data目录下
[root@GlusterFS-master ~]# dd if=/dev/vda1 of=/dev/vdb1
2097152+0 records in
2097152+0 records out
1073741824 bytes (1.1 GB) copied, 2.0979 s, 512 MB/s

[root@GlusterFS-master ~]# du -sh /dev/vdb1
1.0G  /dev/vdb1

[root@GlusterFS-master ~]# mkfs.xfs -f /dev/vdb1                        //这里格式成xfs格式文件，也可以格式化成ext4格式的。
meta-data=/dev/vdb1              isize=512    agcount=4, agsize=65536 blks
         =                       sectsz=512   attr=2, projid32bit=1
         =                       crc=1        finobt=0, sparse=0
data     =                       bsize=4096   blocks=262144, imaxpct=25
         =                       sunit=0      swidth=0 blks
naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
log      =internal log           bsize=4096   blocks=2560, version=2
         =                       sectsz=512   sunit=0 blks, lazy-count=1
realtime =none                   extsz=4096   blocks=0, rtextents=0


[root@GlusterFS-master ~]# mkdir /data

[root@GlusterFS-master ~]# mount /dev/vdb1 /data

[root@GlusterFS-master ~]# df -h
Filesystem               Size  Used Avail Use% Mounted on
/dev/mapper/centos-root   36G  1.8G   34G   5% /
devtmpfs                 2.9G   34M  2.8G   2% /dev
tmpfs                    2.9G     0  2.9G   0% /dev/shm
tmpfs                    2.9G  8.5M  2.9G   1% /run
tmpfs                    2.9G     0  2.9G   0% /sys/fs/cgroup
/dev/vda1               1014M  143M  872M  15% /boot
/dev/mapper/centos-home   18G   33M   18G   1% /home
tmpfs                    581M     0  581M   0% /run/user/0
/dev/loop0               976M  2.6M  907M   1% /data

[root@GlusterFS-master ~]# fdisk -l
.......
Disk /dev/loop0: 1073 MB, 1073741824 bytes, 2097152 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes

设置开机自动挂载
[root@GlusterFS-master ~]# echo '/dev/loop0 /data xfs defaults 1 2' >> /etc/fstab

然后创建gluster存储目录
[root@GlusterFS-master ~]# mkdir /data/gluster

以上操作要在四台节点机器上都要执行一遍，即创建存储目录环境！
```

## 创建分布式卷（即Hash卷）及其相关管理操作

```text
接着将节点添加到集群。这里选择在GlusterFS-master节点上执行：
[root@GlusterFS-master ~]# gluster peer probe 192.168.10.212
peer probe: success.
[root@GlusterFS-master ~]# gluster peer probe 192.168.10.204
peer probe: success.
[root@GlusterFS-master ~]# gluster peer probe 192.168.10.220
peer probe: success.

[root@GlusterFS-master ~]# gluster peer status
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


登录其他节点集群查看，就能看到GlusterFS-master节点（192.168.10.239）也在集群中了
[root@GlusterFS-slave ~]# gluster peer status
Number of Peers: 3

Hostname: GlusterFS-master
Uuid: 5dfd40e2-096b-40b5-bee3-003b57a39007
State: Peer in Cluster (Connected)

Hostname: 192.168.10.204
Uuid: a989394c-f64a-40c3-8bc5-820f623952c4
State: Peer in Cluster (Connected)

Hostname: 192.168.10.220
Uuid: dd99743a-285b-4aed-b3d6-e860f9efd965
State: Peer in Cluster (Connected)

----------------------------------------------------------
现在开始创建。这里在GlusterFS-master机器上执行的，默认这里创建的是哈希卷。
如下，它会自动在192.168.10.212节点的/data下面创建个gluster目录（这个目录不需要提前手动创建）
[root@GlusterFS-master ~]# gluster volume create gluster_data 192.168.10.212:/data/gluster force
volume create: gluster_data: success: please start the volume to access data

登录192.168.10.212节点查看，果然在/data目录下自动创建了gluster目录
[root@GlusterFS-slave ~]# ls /data/gluster
[root@GlusterFS-slave ~]# ls /data/                     //data分区大小为1G
gluster

启动卷，查看卷状态
[root@GlusterFS-master ~]# gluster volume start gluster_data
volume start: gluster_data: success

[root@GlusterFS-master ~]# gluster volume info

Volume Name: gluster_data
Type: Distribute
Volume ID: 0f8b2268-9d2f-4b5c-85df-13408825d6b3
Status: Started
Number of Bricks: 1
Transport-type: tcp
Bricks:
Brick1: 192.168.10.212:/data/gluster

挂载卷操作
在客户端机器上执行glusterfs存储挂载的操作
注意，由于上面添加的是192.168.10.212节点，所以客户端要挂载的也是192.168.10.212节点的存储
[root@Client ~]# mkdir /opt/gfsmount
[root@Client ~]# mount -t glusterfs 192.168.10.212:gluster_data /opt/gfsmount
[root@Client ~]# df -h
Filesystem                   Size  Used Avail Use% Mounted on
/dev/mapper/centos-root       38G  4.3G   33G  12% /
devtmpfs                     1.9G     0  1.9G   0% /dev
tmpfs                        1.9G     0  1.9G   0% /dev/shm
tmpfs                        1.9G  8.6M  1.9G   1% /run
tmpfs                        1.9G     0  1.9G   0% /sys/fs/cgroup
/dev/vda1                   1014M  143M  872M  15% /boot
/dev/mapper/centos-home       19G   33M   19G   1% /home
tmpfs                        380M     0  380M   0% /run/user/0
overlay                       38G  4.3G   33G  12% /var/lib/docker/overlay2/9904ac8cbcba967de3262dc0d5e230c64ad3c1c53b588048e263767d36df8c1a/merged
shm                           64M     0   64M   0% /var/lib/docker/containers/222ec7f21b2495591613e0d1061e4405cd57f99ffaf41dbba1a98c350cd70f60/mounts/shm
192.168.10.212:gluster_data 1014M   33M  982M   4% /opt/gfsmount

上面可知，已经挂载上了glusterfs存储，大小为1G（即是192.168.10.212的存储目录所在的/data分区的空间。
记住：存储目录在节点机的哪个分区下，客户端挂载后就享用这个分区空间。

在客户端挂载目录下测试写数据
[root@Client gfsmount]# mkdir test
[root@Client gfsmount]# touch kevin
[root@Client gfsmount]# ls
kevin  test

然后在192.168.10.212的存储目录下发现是正常同步过来的
[root@GlusterFS-slave ~]# cd /data/gluster/
[root@GlusterFS-slave gluster]# ls
kevin  test

----------------------------------------------------------
增加brick（即扩容卷）
[root@GlusterFS-master ~]# gluster volume add-brick gluster_data 192.168.10.239:/data/gluster force
volume add-brick: success
[root@GlusterFS-master ~]# gluster volume add-brick gluster_data 192.168.10.204:/data/gluster force
volume add-brick: success
[root@GlusterFS-master ~]# gluster volume add-brick gluster_data 192.168.10.220:/data/gluster force
volume add-brick: success

同样，上面三个节点的/data下会自动创建gluster目录！

查看卷状态
[root@GlusterFS-master ~]# gluster volume info

Volume Name: gluster_data
Type: Distribute
Volume ID: 0f8b2268-9d2f-4b5c-85df-13408825d6b3
Status: Started
Number of Bricks: 4
Transport-type: tcp
Bricks:
Brick1: 192.168.10.212:/data/gluster
Brick2: 192.168.10.239:/data/gluster
Brick3: 192.168.10.204:/data/gluster
Brick4: 192.168.10.220:/data/gluster

然后在到客户端，发现挂载点的容量已经由1G上升到了4G！！（即四个节点的存储目录所在分区空间之和）
[root@Client ~]# df -h
Filesystem                   Size  Used Avail Use% Mounted on
/dev/mapper/centos-root       38G  4.3G   33G  12% /
devtmpfs                     1.9G     0  1.9G   0% /dev
tmpfs                        1.9G     0  1.9G   0% /dev/shm
tmpfs                        1.9G  8.6M  1.9G   1% /run
tmpfs                        1.9G     0  1.9G   0% /sys/fs/cgroup
/dev/vda1                   1014M  143M  872M  15% /boot
/dev/mapper/centos-home       19G   33M   19G   1% /home
tmpfs                        380M     0  380M   0% /run/user/0
overlay                       38G  4.3G   33G  12% /var/lib/docker/overlay2/9904ac8cbcba967de3262dc0d5e230c64ad3c1c53b588048e263767d36df8c1a/merged
shm                           64M     0   64M   0% /var/lib/docker/containers/222ec7f21b2495591613e0d1061e4405cd57f99ffaf41dbba1a98c350cd70f60/mounts/shm
192.168.10.212:gluster_data  4.0G  130M  3.9G   4% /opt/gfsmount

如上操作后的总结
1）客户端挂载点的容量是集群中四个节点的存储目录所在分区总和。
2）在客户端挂载点下创建目录，所有节点的存储目录（即Brick）下都会同步到。
3）在客户端挂载点的目录下创建的文件，会在3个节点的存储目录内hash分布。
4）直接在客户端挂载点下创建的文件，则这些文件只会单独同步到所挂载的节点（如上是192.168.10.212的/data/gluster目录下），其他节点不会同步！
5）删除卷会造成一些数据丢失，因为被删除节点有数。

比如：
a）客户端在挂载点下创建目录kevin
[root@Client ~]# cd /opt/gfsmount/
[root@Client gfsmount]# mkdir kevin

节点查看
[root@GlusterFS-master ~]# ls /data/gluster/
kevin
[root@GlusterFS-slave ~]# ls /data/gluster/
kevin
[root@GlusterFS-slave2 ~]# ls /data/gluster/
kevin
[root@GlusterFS-slave3 ~]# ls /data/gluster/
kevin

b）客户端在上面挂载点下的目录里创建文件
[root@Client gfsmount]# for i in `seq -w 1 100`; do cp -rp /var/log/messages /opt/gfsmount/kevin/copy-test-$i; done
[root@Client gfsmount]# ls kevin/
copy-test-001  copy-test-014  copy-test-027  copy-test-040  copy-test-053  copy-test-066  copy-test-079  copy-test-092
copy-test-002  copy-test-015  copy-test-028  copy-test-041  copy-test-054  copy-test-067  copy-test-080  copy-test-093
copy-test-003  copy-test-016  copy-test-029  copy-test-042  copy-test-055  copy-test-068  copy-test-081  copy-test-094
copy-test-004  copy-test-017  copy-test-030  copy-test-043  copy-test-056  copy-test-069  copy-test-082  copy-test-095
copy-test-005  copy-test-018  copy-test-031  copy-test-044  copy-test-057  copy-test-070  copy-test-083  copy-test-096
copy-test-006  copy-test-019  copy-test-032  copy-test-045  copy-test-058  copy-test-071  copy-test-084  copy-test-097
copy-test-007  copy-test-020  copy-test-033  copy-test-046  copy-test-059  copy-test-072  copy-test-085  copy-test-098
copy-test-008  copy-test-021  copy-test-034  copy-test-047  copy-test-060  copy-test-073  copy-test-086  copy-test-099
copy-test-009  copy-test-022  copy-test-035  copy-test-048  copy-test-061  copy-test-074  copy-test-087  copy-test-100
copy-test-010  copy-test-023  copy-test-036  copy-test-049  copy-test-062  copy-test-075  copy-test-088
copy-test-011  copy-test-024  copy-test-037  copy-test-050  copy-test-063  copy-test-076  copy-test-089
copy-test-012  copy-test-025  copy-test-038  copy-test-051  copy-test-064  copy-test-077  copy-test-090
copy-test-013  copy-test-026  copy-test-039  copy-test-052  copy-test-065  copy-test-078  copy-test-091

节点查看。发现文件同步到挂载的192.168.10.212节点山上了
[root@GlusterFS-master ~]# ls /data/gluster/kevin/
copy-test-002  copy-test-014  copy-test-036  copy-test-045  copy-test-056  copy-test-070  copy-test-075  copy-test-097
copy-test-009  copy-test-020  copy-test-042  copy-test-047  copy-test-062  copy-test-071  copy-test-080
copy-test-010  copy-test-027  copy-test-043  copy-test-053  copy-test-064  copy-test-072  copy-test-084
copy-test-013  copy-test-035  copy-test-044  copy-test-055  copy-test-068  copy-test-074  copy-test-092
[root@GlusterFS-master ~]# ll /data/gluster/kevin/|wc -l
30

[root@GlusterFS-slave ~]# ls /data/gluster/kevin/
copy-test-003  copy-test-018  copy-test-037  copy-test-050  copy-test-061  copy-test-069  copy-test-089
copy-test-005  copy-test-025  copy-test-040  copy-test-058  copy-test-066  copy-test-076  copy-test-091
copy-test-007  copy-test-026  copy-test-049  copy-test-059  copy-test-067  copy-test-085  copy-test-096
[root@GlusterFS-slave ~]# ll /data/gluster/kevin/|wc -l
22

[root@GlusterFS-slave2 gluster]# ls /data/gluster/kevin/
copy-test-004  copy-test-016  copy-test-024  copy-test-046  copy-test-065  copy-test-082  copy-test-088  copy-test-099
copy-test-006  copy-test-017  copy-test-029  copy-test-048  copy-test-078  copy-test-086  copy-test-093
copy-test-015  copy-test-023  copy-test-033  copy-test-052  copy-test-079  copy-test-087  copy-test-095
[root@GlusterFS-slave2 gluster]# ll /data/gluster/kevin/|wc -l
23

[root@GlusterFS-slave3 ~]# ls /data/gluster/kevin/
copy-test-001  copy-test-019  copy-test-030  copy-test-038  copy-test-054  copy-test-073  copy-test-090
copy-test-008  copy-test-021  copy-test-031  copy-test-039  copy-test-057  copy-test-077  copy-test-094
copy-test-011  copy-test-022  copy-test-032  copy-test-041  copy-test-060  copy-test-081  copy-test-098
copy-test-012  copy-test-028  copy-test-034  copy-test-051  copy-test-063  copy-test-083  copy-test-100
[root@GlusterFS-slave3 ~]# ll /data/gluster/kevin/|wc -l
29

c）如果直接在客户端挂载点下创建文件，则这些文件只会单独同步到所挂载的节点
（如上是192.168.10.212的/data/gluster目录下），其他节点不会同步！
[root@Client gfsmount]# for i in `seq -w 1 30`; do cp -rp /var/log/messages /opt/gfsmount/haha-test-$i; done
[root@Client gfsmount]# ls
haha-test-01  haha-test-05  haha-test-09  haha-test-13  haha-test-17  haha-test-21  haha-test-25  haha-test-29
haha-test-02  haha-test-06  haha-test-10  haha-test-14  haha-test-18  haha-test-22  haha-test-26  haha-test-30
haha-test-03  haha-test-07  haha-test-11  haha-test-15  haha-test-19  haha-test-23  haha-test-27  kevin
haha-test-04  haha-test-08  haha-test-12  haha-test-16  haha-test-20  haha-test-24  haha-test-28

节点查看，发现只是在192.168.10.212节点上有上面创建的那30个文件，其他3个节点都没有
[root@GlusterFS-master ~]# ls /data/gluster/
kevin

[root@GlusterFS-slave ~]# ls /data/gluster/
haha-test-01  haha-test-05  haha-test-09  haha-test-13  haha-test-17  haha-test-21  haha-test-25  haha-test-29
haha-test-02  haha-test-06  haha-test-10  haha-test-14  haha-test-18  haha-test-22  haha-test-26  haha-test-30
haha-test-03  haha-test-07  haha-test-11  haha-test-15  haha-test-19  haha-test-23  haha-test-27  kevin
haha-test-04  haha-test-08  haha-test-12  haha-test-16  haha-test-20  haha-test-24  haha-test-28

[root@GlusterFS-slave2 ~]# ls /data/gluster/
kevin

[root@GlusterFS-slave3 ~]# ls /data/gluster/
kevin

d）删除卷会造成一些数据丢失，因为被删除节点有数。
从卷中删除其中一个节点的brick
[root@GlusterFS-master ~]# gluster volume remove-brick gluster_data 192.168.10.220:/data/gluster
Usage: volume remove-brick <VOLNAME> [replica <COUNT>] <BRICK> ... <start|stop|status|commit|force>
[root@GlusterFS-master ~]# gluster
gluster> volume remove-brick gluster_data 192.168.10.220:/data/gluster force
Removing brick(s) can result in data loss. Do you want to Continue? (y/n) y
volume remove-brick commit force: success
gluster>

查看卷的信息，mount点空间也下降了（少了192.168.10.220的存储空间）
[root@GlusterFS-master ~]# gluster volume info

Volume Name: gluster_data
Type: Distribute
Volume ID: 0f8b2268-9d2f-4b5c-85df-13408825d6b3
Status: Started
Number of Bricks: 3
Transport-type: tcp
Bricks:
Brick1: 192.168.10.212:/data/gluster
Brick2: 192.168.10.239:/data/gluster
Brick3: 192.168.10.204:/data/gluster

客户端的挂载点空间下降到3G了
[root@Client ~]# df -h
Filesystem                   Size  Used Avail Use% Mounted on
.......
192.168.10.212:gluster_data  3.0G   98M  2.9G   4% /opt/gfsmount

[root@Client ~]# ll /opt/gfsmount/kevin|wc -l
73

发现上面在客户端挂载点的kevin目录下创建的100个文件已经少了一部分，因为这部分数据在192.168.10.220节点上，该
节点的brick已经从卷中删除了，所以这部分数据就丢失了！

接着注意下面的操作！！
将上面删除的192.168.10.220的brick添加进去，发现添加失败！
[root@GlusterFS-master ~]# gluster volume add-brick gluster_data 192.168.10.220:/data/gluster
volume add-brick: failed: Staging failed on 192.168.10.220. Error: /data/gluster is already part of a volume

需要删除目录，才能加回来
[root@GlusterFS-slave3 ~]# rm -rf /data/gluster
[root@GlusterFS-slave3 ~]# ll /data
total 0

然后再添加就能成功了
[root@GlusterFS-master ~]# gluster volume add-brick gluster_data 192.168.10.220:/data/gluster
volume add-brick: success

[root@GlusterFS-master ~]# gluster volume info

Volume Name: gluster_data
Type: Distribute
Volume ID: 0f8b2268-9d2f-4b5c-85df-13408825d6b3
Status: Started
Number of Bricks: 4
Transport-type: tcp
Bricks:
Brick1: 192.168.10.212:/data/gluster
Brick2: 192.168.10.239:/data/gluster
Brick3: 192.168.10.204:/data/gluster
Brick4: 192.168.10.220:/data/gluster

添加回来后，客户端的挂载点的容量又上升到4G了
[root@Client ~]# df -h
.......
192.168.10.212:gluster_data  4.0G  131M  3.9G   4% /opt/gfsmount
---------------------------------------------------------------------------------

rebalance操作能够让文件按照之前的规则再分配

做下rebalance，看到新加的节点上分到了文件和目录。
注意，在实际生产环节中，做rebalance，最好在服务器空闲的时间操作

如上，新添加进去的192.168.10.220节点的存储目录下一开始是没有内容的
[root@GlusterFS-slave3 ~]# ls /data/gluster/
[root@GlusterFS-slave3 ~]#

[root@GlusterFS-master ~]# gluster volume rebalance gluster_data start
volume rebalance: gluster_data: success: Initiated rebalance on volume gluster_data.
Execute "gluster volume rebalance <volume-name> status" to check status.
ID: 49277bfa-df25-45c4-b1fb-cbcf8607a23e

再次查看新添加进去的192.168.10.220节点的存储目录，发现就有了数据
[root@GlusterFS-slave3 ~]# ls /data/gluster/
haha-test-03  haha-test-11  haha-test-14  haha-test-16  haha-test-25  haha-test-30  kevin

发现做了rebalance之后，客户端在挂载点下的数据就会均衡地分布到各节点上了
[root@Client ~]# ls /opt/gfsmount/
haha-test-01  haha-test-05  haha-test-09  haha-test-13  haha-test-17  haha-test-21  haha-test-25  haha-test-29
haha-test-02  haha-test-06  haha-test-10  haha-test-14  haha-test-18  haha-test-22  haha-test-26  haha-test-30
haha-test-03  haha-test-07  haha-test-11  haha-test-15  haha-test-19  haha-test-23  haha-test-27  kevin
haha-test-04  haha-test-08  haha-test-12  haha-test-16  haha-test-20  haha-test-24  haha-test-28
[root@Client ~]# ls /opt/gfsmount/kevin/
copy-test-002  copy-test-014  copy-test-026  copy-test-043  copy-test-053  copy-test-066  copy-test-076  copy-test-088
copy-test-003  copy-test-015  copy-test-027  copy-test-044  copy-test-055  copy-test-067  copy-test-078  copy-test-089
copy-test-004  copy-test-016  copy-test-029  copy-test-045  copy-test-056  copy-test-068  copy-test-079  copy-test-091
copy-test-005  copy-test-017  copy-test-033  copy-test-046  copy-test-058  copy-test-069  copy-test-080  copy-test-092
copy-test-006  copy-test-018  copy-test-035  copy-test-047  copy-test-059  copy-test-070  copy-test-082  copy-test-093
copy-test-007  copy-test-020  copy-test-036  copy-test-048  copy-test-061  copy-test-071  copy-test-084  copy-test-095
copy-test-009  copy-test-023  copy-test-037  copy-test-049  copy-test-062  copy-test-072  copy-test-085  copy-test-096
copy-test-010  copy-test-024  copy-test-040  copy-test-050  copy-test-064  copy-test-074  copy-test-086  copy-test-097
copy-test-013  copy-test-025  copy-test-042  copy-test-052  copy-test-065  copy-test-075  copy-test-087  copy-test-099

[root@GlusterFS-master ~]# ls /data/gluster/
haha-test-06  haha-test-07  haha-test-15  haha-test-19  haha-test-22  haha-test-28  kevin
[root@GlusterFS-master ~]# ls /data/gluster/kevin/
copy-test-002  copy-test-014  copy-test-036  copy-test-047  copy-test-059  copy-test-069  copy-test-080  copy-test-097
copy-test-003  copy-test-018  copy-test-037  copy-test-049  copy-test-061  copy-test-070  copy-test-084
copy-test-005  copy-test-020  copy-test-040  copy-test-050  copy-test-062  copy-test-071  copy-test-085
copy-test-007  copy-test-025  copy-test-042  copy-test-053  copy-test-064  copy-test-072  copy-test-089
copy-test-009  copy-test-026  copy-test-043  copy-test-055  copy-test-066  copy-test-074  copy-test-091
copy-test-010  copy-test-027  copy-test-044  copy-test-056  copy-test-067  copy-test-075  copy-test-092
copy-test-013  copy-test-035  copy-test-045  copy-test-058  copy-test-068  copy-test-076  copy-test-096

[root@GlusterFS-slave ~]# ls /data/gluster/
haha-test-01  haha-test-04  haha-test-08  haha-test-17  haha-test-20  haha-test-26  haha-test-27  haha-test-29  kevin
[root@GlusterFS-slave ~]# ls /data/gluster/kevin/
copy-test-002  copy-test-017  copy-test-040  copy-test-050  copy-test-064  copy-test-074  copy-test-086  copy-test-097
copy-test-004  copy-test-020  copy-test-042  copy-test-052  copy-test-065  copy-test-075  copy-test-087  copy-test-099
copy-test-006  copy-test-023  copy-test-043  copy-test-053  copy-test-066  copy-test-076  copy-test-088
copy-test-009  copy-test-024  copy-test-044  copy-test-055  copy-test-067  copy-test-078  copy-test-089
copy-test-010  copy-test-027  copy-test-045  copy-test-056  copy-test-068  copy-test-079  copy-test-091
copy-test-013  copy-test-029  copy-test-046  copy-test-058  copy-test-069  copy-test-080  copy-test-092
copy-test-014  copy-test-033  copy-test-047  copy-test-059  copy-test-070  copy-test-082  copy-test-093
copy-test-015  copy-test-035  copy-test-048  copy-test-061  copy-test-071  copy-test-084  copy-test-095
copy-test-016  copy-test-036  copy-test-049  copy-test-062  copy-test-072  copy-test-085  copy-test-096

[root@GlusterFS-slave2 ~]# ls /data/gluster/
haha-test-02  haha-test-09  haha-test-12  haha-test-18  haha-test-23  kevin
haha-test-05  haha-test-10  haha-test-13  haha-test-21  haha-test-24
[root@GlusterFS-slave2 ~]# ls /data/gluster/kevin/
copy-test-004  copy-test-016  copy-test-024  copy-test-046  copy-test-065  copy-test-082  copy-test-088  copy-test-099
copy-test-006  copy-test-017  copy-test-029  copy-test-048  copy-test-078  copy-test-086  copy-test-093
copy-test-015  copy-test-023  copy-test-033  copy-test-052  copy-test-079  copy-test-087  copy-test-095

[root@GlusterFS-slave3 ~]# ls /data/gluster/
haha-test-03  haha-test-11  haha-test-14  haha-test-16  haha-test-25  haha-test-30  kevin
[root@GlusterFS-slave3 ~]# ls /data/gluster/kevin/

查看下平衡状态
[root@GlusterFS-master ~]# gluster volume rebalance gluster_data status
                                    Node Rebalanced-files          size       scanned      failures       skipped               status   run time in secs
                               ---------      -----------   -----------   -----------   -----------   -----------         ------------     --------------
                               localhost               29       164.8KB           105             0             0            completed               1.00
                          192.168.10.212               43       248.7KB           131             0             0            completed               1.00
                          192.168.10.204                0        0Bytes           105             0             0            completed               0.00
                          192.168.10.220                0        0Bytes           105             0             0            completed               0.00
volume rebalance: gluster_data: success:

--------------------------------------------------------------------------
接着进行卸载挂载点，停止卷
这个操作很危险，但是卷删除了，下面的数据还在：

[root@Client ~]# umount /opt/gfsmount -lf

[root@GlusterFS-master ~]# gluster volume stop gluster_data
Stopping volume will make its data inaccessible. Do you want to continue? (y/n) y
volume stop: gluster_data: success

[root@GlusterFS-master ~]# gluster volume delete gluster_data
Deleting volume will erase all information about the volume. Do you want to continue? (y/n) y
volume delete: gluster_data: success

[root@GlusterFS-master ~]# gluster volume list
No volumes present in cluster

[root@GlusterFS-master ~]# gluster volume info
No volumes present

gluster_data卷删除了，但是各节点上存储目录下的数据还在
[root@GlusterFS-master ~]# ls /data/gluster/
haha-test-06  haha-test-07  haha-test-15  haha-test-19  haha-test-22  haha-test-28  kevin

[root@GlusterFS-slave ~]# ls /data/gluster/
haha-test-01  haha-test-04  haha-test-08  haha-test-17  haha-test-20  haha-test-26  haha-test-27  haha-test-29  kevin

[root@GlusterFS-slave2 ~]# ls /data/gluster/
haha-test-02  haha-test-09  haha-test-12  haha-test-18  haha-test-23  kevin
haha-test-05  haha-test-10  haha-test-13  haha-test-21  haha-test-24

[root@GlusterFS-slave3 ~]# ls /data/gluster/
haha-test-03  haha-test-11  haha-test-14  haha-test-16  haha-test-25  haha-test-30  kevin

客户端挂载点数据不显示
[root@Client ~]# ls /opt/gfsmount/
[root@Client ~]#

想要清除数据，可以登录到每个节点上删除brick下面的数据
[root@GlusterFS-master ~]# rm -rf /data/gluster
[root@GlusterFS-slave ~]# rm -rf /data/gluster
[root@GlusterFS-slave2 ~]# rm -rf /data/gluster
[root@GlusterFS-slave3 ~]# rm -rf /data/gluster
```

## 创建复制卷及其相关管理操作

```text
先删除各个节点在上面用到的存储目录
[root@GlusterFS-master ~]# rm -rf /data/gluster
[root@GlusterFS-slave ~]# rm -rf /data/gluster
[root@GlusterFS-slave2 ~]# rm -rf /data/gluster
[root@GlusterFS-slave3 ~]# rm -rf /data/gluster

[root@GlusterFS-master ~]# gluster volume info
No volumes present
[root@GlusterFS-master ~]# gluster volume status
No volumes present
[root@GlusterFS-master ~]# gluster volume list
No volumes present in cluster
[root@GlusterFS-master ~]# gluster peer status
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

删除集群中的节点
[root@GlusterFS-master ~]# gluster
gluster> peer detach 192.168.10.220
peer detach: success
gluster> peer detach 192.168.10.204  
peer detach: success
gluster> peer detach 192.168.10.212
peer detach: success
gluster> peer detach 192.168.10.239
peer detach: failed: 192.168.10.239 is localhost
gluster>

[root@GlusterFS-slave ~]# gluster
peer detach: success

查看集群，已没有节点信息了
[root@GlusterFS-master ~]# gluster peer status
Number of Peers: 0
----------------------------------------------------------------------

现在开始创建复制卷，操作如下：
先添加集群
[root@GlusterFS-master ~]# gluster peer probe 192.168.10.212
peer probe: success.
[root@GlusterFS-master ~]# gluster peer status
Number of Peers: 1

Hostname: 192.168.10.212
Uuid: f8e69297-4690-488e-b765-c1c404810d6a
State: Peer in Cluster (Connected)

创建复制卷，需要replica 2，表示两个副本
[root@GlusterFS-master ~]# gluster volume create gluster_share replica 2 192.168.10.239:/data/gluster 192.168.10.212:/data/gluster force
volume create: gluster_share: success: please start the volume to access data

[root@GlusterFS-master ~]# gluster volume info

Volume Name: gluster_share
Type: Replicate
Volume ID: a9f989bd-7edd-4089-836a-d9f742b8d37a
Status: Created
Number of Bricks: 1 x 2 = 2
Transport-type: tcp
Bricks:
Brick1: 192.168.10.239:/data/gluster
Brick2: 192.168.10.212:/data/gluster

以上两个节点会自动生成存储目录/data/gluster
[root@GlusterFS-master ~]# ll /data/
total 0
drwxr-xr-x. 2 root root 6 Apr 10 12:58 gluster

[root@GlusterFS-slave ~]# ll /data/
total 0
drwxr-xr-x. 2 root root 6 Apr 10 12:59 gluster

启动卷，查看卷状态　
[root@GlusterFS-master ~]# gluster volume list
gluster_share

[root@GlusterFS-master ~]# gluster volume start gluster_share
volume start: gluster_share: success

[root@GlusterFS-master ~]# gluster volume info

Volume Name: gluster_share
Type: Replicate
Volume ID: a9f989bd-7edd-4089-836a-d9f742b8d37a
Status: Started
Number of Bricks: 1 x 2 = 2
Transport-type: tcp
Bricks:
Brick1: 192.168.10.239:/data/gluster
Brick2: 192.168.10.212:/data/gluster

客户端挂载后操作创建文件文件，发现容量只有一个节点的容量，因为是复制卷
[root@Client ~]# mount -t glusterfs 192.168.10.239:gluster_share /opt/gfsmount/
[root@Client ~]# df -h
.......
192.168.10.239:gluster_share 1014M   33M  982M   4% /opt/gfsmount

客户端挂载点写入数据
[root@Client gfsmount]# mkdir test
[root@Client gfsmount]# touch kevin grace

两个节点由于是副本关系，内容一致
[root@GlusterFS-master ~]# ls /data/gluster/
grace  kevin  test

[root@GlusterFS-slave ~]# ls /data/gluster/
grace  kevin  test

------------------------------------------------------------------
模拟误删卷信息故障
删除卷信息，卷信息在下面路径下　
[root@GlusterFS-master ~]# ls /usr/local/glusterfs/var/lib/glusterd/vols/gluster_share/
bricks                                         gluster_share-rebalance.vol  rbstate
cksum                                          gluster_share.tcp-fuse.vol   run
gluster_share.192.168.10.212.data-gluster.vol  info                         snapd.info
gluster_share.192.168.10.239.data-gluster.vol  node_state.info              trusted-gluster_share.tcp-fuse.vol

[root@GlusterFS-slave ~]# ls /usr/local/glusterfs/var/lib/glusterd/vols/gluster_share/
bricks                                         gluster_share-rebalance.vol  rbstate
cksum                                          gluster_share.tcp-fuse.vol   run
gluster_share.192.168.10.212.data-gluster.vol  info                         snapd.info
gluster_share.192.168.10.239.data-gluster.vol  node_state.info              trusted-gluster_share.tcp-fuse.vol

这里以删除192.168.10.212（即GlusterFS-slave）节点的卷信息为例
[root@GlusterFS-slave ~]# rm -rf /usr/local/glusterfs/var/lib/glusterd/vols/gluster_share/

[root@GlusterFS-slave ~]# ls /usr/local/glusterfs/var/lib/glusterd/vols/gluster_share/
ls: cannot access /usr/local/glusterfs/var/lib/glusterd/vols/gluster_share/: No such file or directory

恢复卷信息
把卷信息同步过来，192.168.10.212节点上的卷信息删除了，但是它的备份节点192.168.10.239节点上的卷信息是正常的！
下面操作的all表示同步所有卷信息过来，这里也可以写成gluster_share卷

特别注意：这种卷信息要定期备份！！！！
[root@GlusterFS-master ~]# gluster volume sync 192.168.10.239 all     //不能在节点本机进行针对自己的备份
Sync volume may make data inaccessible while the sync is in progress. Do you want to continue? (y/n) y
volume sync: failed: sync from localhost not allowed

[root@GlusterFS-slave ~]# gluster volume sync 192.168.10.239 all      //所以要在集群中的其他节点上操作
Sync volume may make data inaccessible while the sync is in progress. Do you want to continue? (y/n) y
volume sync: success

然后再去192.168.10.212节点上查看卷信息，发现删除的卷信息已经恢复回来了！
[root@GlusterFS-slave ~]# ls /usr/local/glusterfs/var/lib/glusterd/vols/gluster_share/
bricks                                         gluster_share-rebalance.vol  rbstate
cksum                                          gluster_share.tcp-fuse.vol   run
gluster_share.192.168.10.212.data-gluster.vol  info                         snapd.info
gluster_share.192.168.10.239.data-gluster.vol  node_state.info              trusted-gluster_share.tcp-fuse.vol

------------------------------------------------------------------
追加节点操作
[root@GlusterFS-master ~]# gluster peer probe 192.168.10.204
peer probe: success.
[root@GlusterFS-master ~]# gluster peer probe 192.168.10.220
peer probe: success.
[root@GlusterFS-master ~]# gluster peer status
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

查看卷信息
[root@GlusterFS-master ~]# gluster volume info

Volume Name: gluster_share
Type: Replicate
Volume ID: a9f989bd-7edd-4089-836a-d9f742b8d37a
Status: Started
Number of Bricks: 1 x 2 = 2
Transport-type: tcp
Bricks:
Brick1: 192.168.10.239:/data/gluster
Brick2: 192.168.10.212:/data/gluster

卷扩容操作（即将新添加的两个节点的brick添加到上面的models磁盘里）

先将客户端的挂载卸载掉
[root@Client ~]# umount /opt/gfsmount/

接着关闭gluster_share卷
[root@GlusterFS-master ~]# gluster volume stop gluster_share
Stopping volume will make its data inaccessible. Do you want to continue? (y/n) y
volume stop: gluster_share: success
[root@GlusterFS-master ~]# gluster volume status gluster_share
Volume gluster_share is not started

然后执行卷扩容操作
[root@GlusterFS-master ~]# gluster volume add-brick gluster_share 192.168.10.204:/data/gluster 192.168.10.220:/data/gluster force
volume add-brick: success

再次查看卷信息，发现新节点已经加入进去了
[root@GlusterFS-master ~]# gluster volume info

Volume Name: gluster_share
Type: Distributed-Replicate
Volume ID: a9f989bd-7edd-4089-836a-d9f742b8d37a
Status: Stopped
Number of Bricks: 2 x 2 = 4
Transport-type: tcp
Bricks:
Brick1: 192.168.10.239:/data/gluster
Brick2: 192.168.10.212:/data/gluster
Brick3: 192.168.10.204:/data/gluster
Brick4: 192.168.10.220:/data/gluster

然后重新启动gluster_share卷
[root@GlusterFS-master ~]# gluster volume start gluster_share
volume start: gluster_share: success

[root@GlusterFS-master ~]# gluster volume status gluster_share
Status of volume: gluster_share
Gluster process                     Port    Online  Pid
------------------------------------------------------------------------------
Brick 192.168.10.239:/data/gluster          49155   Y   8907
Brick 192.168.10.212:/data/gluster          49156   Y   4049
Brick 192.168.10.204:/data/gluster          49156   Y   11447
Brick 192.168.10.220:/data/gluster          49157   Y   16714
NFS Server on localhost                 N/A N   N/A
Self-heal Daemon on localhost               N/A N   N/A
NFS Server on 192.168.10.212                N/A N   N/A
Self-heal Daemon on 192.168.10.212          N/A N   N/A
NFS Server on 192.168.10.220                N/A N   N/A
Self-heal Daemon on 192.168.10.220          N/A N   N/A
NFS Server on 192.168.10.204                N/A N   N/A
Self-heal Daemon on 192.168.10.204          N/A N   N/A

Task Status of Volume gluster_share
------------------------------------------------------------------------------
There are no active volume tasks

如上发现四个节点的Online项状态都是"Y"

接着在Client客户机重新挂载存储
[root@Client ~]# mount -t glusterfs 192.168.10.239:gluster_share /opt/gfsmount/
[root@Client ~]# df -h
.......
192.168.10.239:gluster_share  2.0G   65M  2.0G   4% /opt/gfsmount

如上发现客户端挂载点的容量是2G，即两组replica的分区大小

在客户机上写入数据做测试，发现：
1）新添加到models卷里的节点的存储目录里不会有之前其他节点的数据，只会有新写入的数据。
2）由于上面创建副本卷的时候，指定的副本是2个（即replica 2），如果不做rebalance，那么在客户端写入的文件数据都只会同步到原来的那个replica
   下的节点里，即新加入的两个节点里不会同步数据；在客户端创建目录，四个节点都会同步到；但是文件只会同步到之前的两个节点。

接着进行重新均衡。均衡卷的前提是至少有两个brick存储单元
[root@GlusterFS-master ~]# gluster volume rebalance gluster_share start
volume rebalance: gluster_share: success: Initiated rebalance on volume gluster_share.
Execute "gluster volume rebalance <volume-name> status" to check status.
ID: 26035833-3c20-4822-b065-7a5e15d30b85

[root@GlusterFS-master ~]# gluster volume rebalance gluster_share status
                                    Node Rebalanced-files          size       scanned      failures       skipped               status   run time in secs
                               ---------      -----------   -----------   -----------   -----------   -----------         ------------     --------------
                               localhost              103        10.3MB           305             0             0            completed               1.00
                          192.168.10.212                0        0Bytes           210             0             0            completed               0.00
                          192.168.10.204               11        58.9KB           213             0             0            completed               0.00
                          192.168.10.220                0        0Bytes           210             0             0            completed               0.00
volume rebalance: gluster_share: success:

然后删除客户端的数据，重新写入进行测试
[root@Client ~]# rm -rf /opt/gfsmount/*
[root@Client ~]# cd /opt/gfsmount/
[root@Client gfsmount]# ls
[root@Client gfsmount]# mkdir kevin

发现四个节点都有这个kevin目录
[root@GlusterFS-master ~]# ls /data/gluster/
kevin
[root@GlusterFS-slave ~]# ls /data/gluster/
kevin
[root@GlusterFS-slave2 ~]# ls /data/gluster/
kevin
[root@GlusterFS-slave3 ~]# ls /data/gluster/
kevin

然后批量写入数据
[root@Client gfsmount]# for i in `seq -w 1 100`; do cp -rp /var/log/messages /opt/gfsmount/haha-test-$i; done
[root@Client gfsmount]# ls
haha-test-001  haha-test-014  haha-test-027  haha-test-040  haha-test-053  haha-test-066  haha-test-079  haha-test-092
haha-test-002  haha-test-015  haha-test-028  haha-test-041  haha-test-054  haha-test-067  haha-test-080  haha-test-093
haha-test-003  haha-test-016  haha-test-029  haha-test-042  haha-test-055  haha-test-068  haha-test-081  haha-test-094
haha-test-004  haha-test-017  haha-test-030  haha-test-043  haha-test-056  haha-test-069  haha-test-082  haha-test-095
haha-test-005  haha-test-018  haha-test-031  haha-test-044  haha-test-057  haha-test-070  haha-test-083  haha-test-096
haha-test-006  haha-test-019  haha-test-032  haha-test-045  haha-test-058  haha-test-071  haha-test-084  haha-test-097
haha-test-007  haha-test-020  haha-test-033  haha-test-046  haha-test-059  haha-test-072  haha-test-085  haha-test-098
haha-test-008  haha-test-021  haha-test-034  haha-test-047  haha-test-060  haha-test-073  haha-test-086  haha-test-099
haha-test-009  haha-test-022  haha-test-035  haha-test-048  haha-test-061  haha-test-074  haha-test-087  haha-test-100
haha-test-010  haha-test-023  haha-test-036  haha-test-049  haha-test-062  haha-test-075  haha-test-088  kevin
haha-test-011  haha-test-024  haha-test-037  haha-test-050  haha-test-063  haha-test-076  haha-test-089
haha-test-012  haha-test-025  haha-test-038  haha-test-051  haha-test-064  haha-test-077  haha-test-090
haha-test-013  haha-test-026  haha-test-039  haha-test-052  haha-test-065  haha-test-078  haha-test-091

发现这些数据被分为两个replica副本，分到放到了192.168.10.239、192.168.10.212（副本关系）和192.168.10.204、192.168.10.220上了
[root@GlusterFS-master ~]# ls /data/gluster/
haha-test-004  haha-test-015  haha-test-028  haha-test-040  haha-test-050  haha-test-070  haha-test-080  haha-test-091
haha-test-007  haha-test-017  haha-test-029  haha-test-042  haha-test-051  haha-test-072  haha-test-081  haha-test-093
haha-test-009  haha-test-019  haha-test-032  haha-test-043  haha-test-052  haha-test-073  haha-test-084  haha-test-094
haha-test-010  haha-test-020  haha-test-033  haha-test-044  haha-test-056  haha-test-075  haha-test-087  haha-test-095
haha-test-011  haha-test-021  haha-test-034  haha-test-047  haha-test-059  haha-test-076  haha-test-088  haha-test-097
haha-test-012  haha-test-022  haha-test-036  haha-test-048  haha-test-061  haha-test-078  haha-test-089  haha-test-100
haha-test-014  haha-test-027  haha-test-037  haha-test-049  haha-test-069  haha-test-079  haha-test-090  kevin

[root@GlusterFS-slave ~]# ls /data/gluster/
haha-test-004  haha-test-015  haha-test-028  haha-test-040  haha-test-050  haha-test-070  haha-test-080  haha-test-091
haha-test-007  haha-test-017  haha-test-029  haha-test-042  haha-test-051  haha-test-072  haha-test-081  haha-test-093
haha-test-009  haha-test-019  haha-test-032  haha-test-043  haha-test-052  haha-test-073  haha-test-084  haha-test-094
haha-test-010  haha-test-020  haha-test-033  haha-test-044  haha-test-056  haha-test-075  haha-test-087  haha-test-095
haha-test-011  haha-test-021  haha-test-034  haha-test-047  haha-test-059  haha-test-076  haha-test-088  haha-test-097
haha-test-012  haha-test-022  haha-test-036  haha-test-048  haha-test-061  haha-test-078  haha-test-089  haha-test-100
haha-test-014  haha-test-027  haha-test-037  haha-test-049  haha-test-069  haha-test-079  haha-test-090  kevin

[root@GlusterFS-slave2 ~]# ls /data/gluster/
haha-test-001  haha-test-013  haha-test-026  haha-test-041  haha-test-057  haha-test-065  haha-test-077  haha-test-096
haha-test-002  haha-test-016  haha-test-030  haha-test-045  haha-test-058  haha-test-066  haha-test-082  haha-test-098
haha-test-003  haha-test-018  haha-test-031  haha-test-046  haha-test-060  haha-test-067  haha-test-083  haha-test-099
haha-test-005  haha-test-023  haha-test-035  haha-test-053  haha-test-062  haha-test-068  haha-test-085  kevin
haha-test-006  haha-test-024  haha-test-038  haha-test-054  haha-test-063  haha-test-071  haha-test-086
haha-test-008  haha-test-025  haha-test-039  haha-test-055  haha-test-064  haha-test-074  haha-test-092

[root@GlusterFS-slave3 ~]# ls /data/gluster/
haha-test-001  haha-test-013  haha-test-026  haha-test-041  haha-test-057  haha-test-065  haha-test-077  haha-test-096
haha-test-002  haha-test-016  haha-test-030  haha-test-045  haha-test-058  haha-test-066  haha-test-082  haha-test-098
haha-test-003  haha-test-018  haha-test-031  haha-test-046  haha-test-060  haha-test-067  haha-test-083  haha-test-099
haha-test-005  haha-test-023  haha-test-035  haha-test-053  haha-test-062  haha-test-068  haha-test-085  kevin
haha-test-006  haha-test-024  haha-test-038  haha-test-054  haha-test-063  haha-test-071  haha-test-086
haha-test-008  haha-test-025  haha-test-039  haha-test-055  haha-test-064  haha-test-074  haha-test-092

同样在客户端的挂载点的目录（即/opt/gfsmount/kevin）下创建的文件也是同样被分为两个replica副本，分到放到了
192.168.10.239、192.168.10.212（副本关系）和192.168.10.204、192.168.10.220上了
```

