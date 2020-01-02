# 更换故障Brick

前面已经介绍了[GlusterFS分布式存储集群环境部署记录](http://www.cnblogs.com/kevingrace/p/8743812.html)，现在模拟下更换故障Brick的操作：

**1）GlusterFS集群系统一共有4个节点，集群信息如下：**

```text
分别在各个节点上配置hosts、同步好系统时间，关闭防火墙和selinux
[root@GlusterFS-slave data]# cat /etc/hosts
192.168.10.239  GlusterFS-master
192.168.10.212  GlusterFS-slave
192.168.10.204  GlusterFS-slave2
192.168.10.220  GlusterFS-slave3

------------------------------------------------------------------------------------
分别在四个节点机上使用df创建一个虚拟分区，然后在这个分区上创建存储目录
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

记住：以上操作要在四台节点机器上都要执行一遍，即创建存储目录环境！
----------------------------------------------------------------------------------

部署glusterfs集群的中间部分操作在此省略，具体可参考：http://www.cnblogs.com/kevingrace/p/8743812.html

创建集群，在GlusterFS-master节点上操作：
[root@GlusterFS-master ~]# gluster peer probe 192.168.10.212
peer probe: success.
[root@GlusterFS-master ~]# gluster peer probe 192.168.10.204
peer probe: success.
[root@GlusterFS-master ~]# gluster peer probe 192.168.10.220
peer probe: success.

查看集群情况
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

在其他节点上查看集群情况，就能看到GlusterFS-master节点了
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

创建副本卷
[root@GlusterFS-master ~]# gluster volume info
No volumes present

[root@GlusterFS-master ~]# gluster volume create models replica 2 192.168.10.239:/data/gluster 192.168.10.212:/data/gluster force
volume create: models: success: please start the volume to access data

[root@GlusterFS-master ~]# gluster volume list
models

[root@GlusterFS-master ~]# gluster volume info

Volume Name: models
Type: Replicate
Volume ID: 8eafb261-e0d2-4f3b-8e09-05475c63dcc6
Status: Created
Number of Bricks: 1 x 2 = 2
Transport-type: tcp
Bricks:
Brick1: 192.168.10.239:/data/gluster
Brick2: 192.168.10.212:/data/gluster

启动models卷
[root@GlusterFS-master ~]# gluster volume start models
volume start: models: success

[root@GlusterFS-master ~]# gluster volume status models
Status of volume: models
Gluster process           Port  Online  Pid
------------------------------------------------------------------------------
Brick 192.168.10.239:/data/gluster      49156 Y 16040
Brick 192.168.10.212:/data/gluster      49157 Y 5544
NFS Server on localhost         N/A N N/A
Self-heal Daemon on localhost       N/A Y 16059
NFS Server on 192.168.10.204        N/A N N/A
Self-heal Daemon on 192.168.10.204      N/A Y 12412
NFS Server on 192.168.10.220        N/A N N/A
Self-heal Daemon on 192.168.10.220      N/A Y 17656
NFS Server on 192.168.10.212        N/A N N/A
Self-heal Daemon on 192.168.10.212      N/A Y 5563

Task Status of Volume models
------------------------------------------------------------------------------
There are no active volume tasks

将另外两个节点追加到集群中。即卷扩容
[root@GlusterFS-master ~]# gluster volume add-brick models 192.168.10.204:/data/gluster 192.168.10.220:/data/gluster force
volume add-brick: success

[root@GlusterFS-master ~]# gluster volume info

Volume Name: models
Type: Distributed-Replicate
Volume ID: 8eafb261-e0d2-4f3b-8e09-05475c63dcc6
Status: Started
Number of Bricks: 2 x 2 = 4
Transport-type: tcp
Bricks:
Brick1: 192.168.10.239:/data/gluster
Brick2: 192.168.10.212:/data/gluster
Brick3: 192.168.10.204:/data/gluster
Brick4: 192.168.10.220:/data/gluster
------------------------------------------------------------------------------
```

**2）测试Gluster卷**

```text
客户端挂载glusterfs
[root@Client ~]# mount -t glusterfs 192.168.10.239:models /opt/gfsmount
[root@Client gfsmount]# df -h
........
192.168.10.239:models    2.0G   65M  2.0G   4% /opt/gfsmount

[root@Client ~]# cd /opt/gfsmount/
[root@Client gfsmount]# ls
[root@Client gfsmount]#

写入测试数据
[root@Client gfsmount]# for i in `seq -w 1 100`; do cp -rp /var/log/messages /opt/gfsmount/copy-test-$i; done
[root@Client gfsmount]# ls /opt/gfsmount/
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

[root@Client gfsmount]# ls -lA /opt/gfsmount|wc -l
101

在各节点机器上也确认下，发现这100个文件随机地各自分为了两个50份的文件（均衡），分别同步到了第1-2节点和第3-4节点上了。
[root@GlusterFS-master ~]# ls /data/gluster
copy-test-001  copy-test-016  copy-test-028  copy-test-038  copy-test-054  copy-test-078  copy-test-088  copy-test-100
copy-test-004  copy-test-017  copy-test-029  copy-test-039  copy-test-057  copy-test-079  copy-test-090
copy-test-006  copy-test-019  copy-test-030  copy-test-041  copy-test-060  copy-test-081  copy-test-093
copy-test-008  copy-test-021  copy-test-031  copy-test-046  copy-test-063  copy-test-082  copy-test-094
copy-test-011  copy-test-022  copy-test-032  copy-test-048  copy-test-065  copy-test-083  copy-test-095
copy-test-012  copy-test-023  copy-test-033  copy-test-051  copy-test-073  copy-test-086  copy-test-098
copy-test-015  copy-test-024  copy-test-034  copy-test-052  copy-test-077  copy-test-087  copy-test-099
[root@GlusterFS-master ~]# ll /data/gluster|wc -l
51

[root@GlusterFS-slave ~]# ls /data/gluster/
copy-test-001  copy-test-016  copy-test-028  copy-test-038  copy-test-054  copy-test-078  copy-test-088  copy-test-100
copy-test-004  copy-test-017  copy-test-029  copy-test-039  copy-test-057  copy-test-079  copy-test-090
copy-test-006  copy-test-019  copy-test-030  copy-test-041  copy-test-060  copy-test-081  copy-test-093
copy-test-008  copy-test-021  copy-test-031  copy-test-046  copy-test-063  copy-test-082  copy-test-094
copy-test-011  copy-test-022  copy-test-032  copy-test-048  copy-test-065  copy-test-083  copy-test-095
copy-test-012  copy-test-023  copy-test-033  copy-test-051  copy-test-073  copy-test-086  copy-test-098
copy-test-015  copy-test-024  copy-test-034  copy-test-052  copy-test-077  copy-test-087  copy-test-099
[root@GlusterFS-slave ~]# ll /data/gluster/|wc -l
51

[root@GlusterFS-slave2 ~]# ls /data/gluster/
copy-test-002  copy-test-014  copy-test-036  copy-test-047  copy-test-059  copy-test-069  copy-test-080  copy-test-097
copy-test-003  copy-test-018  copy-test-037  copy-test-049  copy-test-061  copy-test-070  copy-test-084
copy-test-005  copy-test-020  copy-test-040  copy-test-050  copy-test-062  copy-test-071  copy-test-085
copy-test-007  copy-test-025  copy-test-042  copy-test-053  copy-test-064  copy-test-072  copy-test-089
copy-test-009  copy-test-026  copy-test-043  copy-test-055  copy-test-066  copy-test-074  copy-test-091
copy-test-010  copy-test-027  copy-test-044  copy-test-056  copy-test-067  copy-test-075  copy-test-092
copy-test-013  copy-test-035  copy-test-045  copy-test-058  copy-test-068  copy-test-076  copy-test-096
[root@GlusterFS-slave2 ~]# ll /data/gluster/|wc -l
51

[root@GlusterFS-slave3 ~]# ls /data/gluster/
copy-test-002  copy-test-014  copy-test-036  copy-test-047  copy-test-059  copy-test-069  copy-test-080  copy-test-097
copy-test-003  copy-test-018  copy-test-037  copy-test-049  copy-test-061  copy-test-070  copy-test-084
copy-test-005  copy-test-020  copy-test-040  copy-test-050  copy-test-062  copy-test-071  copy-test-085
copy-test-007  copy-test-025  copy-test-042  copy-test-053  copy-test-064  copy-test-072  copy-test-089
copy-test-009  copy-test-026  copy-test-043  copy-test-055  copy-test-066  copy-test-074  copy-test-091
copy-test-010  copy-test-027  copy-test-044  copy-test-056  copy-test-067  copy-test-075  copy-test-092
copy-test-013  copy-test-035  copy-test-045  copy-test-058  copy-test-068  copy-test-076  copy-test-096
[root@GlusterFS-slave3 ~]# ll /data/gluster/|wc -l
51
```

**3）模拟brick故障**

```text
1）查看当前存储状态
在GlusterFS-slave3节点机器上操作
[root@GlusterFS-slave3 ~]# gluster volume status
Status of volume: models
Gluster process           Port  Online  Pid
------------------------------------------------------------------------------
Brick 192.168.10.239:/data/gluster      49156 Y 16040
Brick 192.168.10.212:/data/gluster      49157 Y 5544
Brick 192.168.10.204:/data/gluster      49157 Y 12432
Brick 192.168.10.220:/data/gluster      49158 Y 17678
NFS Server on localhost         N/A N N/A
Self-heal Daemon on localhost       N/A Y 17697
NFS Server on GlusterFS-master        N/A N N/A
Self-heal Daemon on GlusterFS-master      N/A Y 16104
NFS Server on 192.168.10.204        N/A N N/A
Self-heal Daemon on 192.168.10.204      N/A Y 12451
NFS Server on 192.168.10.212        N/A N N/A
Self-heal Daemon on 192.168.10.212      N/A Y 5593

Task Status of Volume models
------------------------------------------------------------------------------
There are no active volume tasks


注：注意到Online项全部为"Y"

2）制造故障（注意这里模拟的是文件系统故障，假设物理硬盘没有问题或已经更换阵列中的硬盘）
在GlusterFS-slave3节点机器上操作
[root@GlusterFS-slave3 ~]# vim /etc/fstab     //注释掉如下行
......
#/dev/loop0 /data xfs defaults 1 2

重启服务器
[root@GlusterFS-slave3 ~]# reboot

重启后，发现GlusterFS-slave3节点的/data没有挂载上
[root@GlusterFS-slave3 ~]# df -h

重启后，发现GlusterFS-slave3节点的存储目录不在了，数据没有了。
[root@GlusterFS-slave3 ~]# ls /data/
[root@GlusterFS-slave3 ~]#

重启服务器后，记得启动glusterd服务
[root@GlusterFS-slave3 ~]# /usr/local/glusterfs/sbin/glusterd
[root@GlusterFS-slave3 ~]# ps -ef|grep gluster
root     11122     1  4 23:13 ?        00:00:00 /usr/local/glusterfs/sbin/glusterd
root     11269     1  2 23:13 ?        00:00:00 /usr/local/glusterfs/sbin/glusterfs -s localhost --volfile-id gluster/glustershd -p /usr/local/glusterfs/var/lib/glusterd/glustershd/run/glustershd.pid -l /usr/local/glusterfs/var/log/glusterfs/glustershd.log -S /var/run/98e3200bc6620c9d920e9dc65624dbe0.socket --xlator-option *replicate*.node-uuid=dd99743a-285b-4aed-b3d6-e860f9efd965
root     11280  5978  0 23:13 pts/0    00:00:00 grep --color=auto gluster


3）查看当前存储状态
[root@GlusterFS-slave3 ~]# gluster volume status
Status of volume: models
Gluster process           Port  Online  Pid
------------------------------------------------------------------------------
Brick 192.168.10.239:/data/gluster      49156 Y 16040
Brick 192.168.10.212:/data/gluster      49157 Y 5544
Brick 192.168.10.204:/data/gluster      49157 Y 12432
Brick 192.168.10.220:/data/gluster      N/A N N/A
NFS Server on localhost         N/A N N/A
Self-heal Daemon on localhost       N/A Y 11269
NFS Server on GlusterFS-master        N/A N N/A
Self-heal Daemon on GlusterFS-master      N/A Y 16104
NFS Server on 192.168.10.212        N/A N N/A
Self-heal Daemon on 192.168.10.212      N/A Y 5593
NFS Server on 192.168.10.204        N/A N N/A
Self-heal Daemon on 192.168.10.204      N/A Y 12451

Task Status of Volume models
------------------------------------------------------------------------------
There are no active volume tasks

注意：发现GlusterFS-slave3节点（192.168.10.220）的Online项状态为"N"了！


4）恢复故障brick方法

4.1）结束故障brick的进程
如上通过"gluster volume status"命令，如果查看到状态Online项为"N"的GlusterFS-slave3节点存在PID号（不显示N/A）,则应当使用"kill -15 pid"杀死它！
一般当Online项为"N"时就不显示pid号了。

4.2）创建新的数据目录（注意绝不可以与之前目录一样）
[root@GlusterFS-slave3 ~]# dd if=/dev/vda1 of=/dev/vdb1
2097152+0 records in
2097152+0 records out
1073741824 bytes (1.1 GB) copied, 2.05684 s, 522 MB/s
[root@GlusterFS-slave3 ~]# du -sh /dev/vdb1
1.0G  /dev/vdb1
[root@GlusterFS-slave3 ~]# mkfs.xfs -f /dev/vdb1
meta-data=/dev/vdb1              isize=512    agcount=4, agsize=65536 blks
         =                       sectsz=512   attr=2, projid32bit=1
         =                       crc=1        finobt=0, sparse=0
data     =                       bsize=4096   blocks=262144, imaxpct=25
         =                       sunit=0      swidth=0 blks
naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
log      =internal log           bsize=4096   blocks=2560, version=2
         =                       sectsz=512   sunit=0 blks, lazy-count=1
realtime =none                   extsz=4096   blocks=0, rtextents=0

重新挂载
[root@GlusterFS-slave3 ~]# mount /dev/vdb1 /data


[root@GlusterFS-slave3 ~]# vim /etc/fstab       //去掉下面注释
......
/dev/loop0 /data xfs defaults 1 2

4.3）查询故障节点的备份节点(GlusterFS-slave2)目录的扩展属性（使用"yum search getfattr"命令getfattr工具的安装途径）
[root@GlusterFS-slave2 ~]# yum install -y attr.x86_64
[root@GlusterFS-slave2 ~]# getfattr -d -m. -e hex /data/gluster
getfattr: Removing leading '/' from absolute path names
# file: data/gluster
trusted.gfid=0x00000000000000000000000000000001
trusted.glusterfs.dht=0x00000001000000007fffffffffffffff
trusted.glusterfs.volume-id=0x8eafb261e0d24f3b8e0905475c63dcc6

4.4）挂载卷并触发自愈
在客户端先卸载掉之前的挂载
[root@Client ~]# umount /data/gluster

然后重新挂载GlusterFS-slave3（其实挂载哪一个节点的都可以）
[root@Client ~]# mount -t glusterfs 192.168.10.220:models /opt/gfsmount
[root@Client ~]# df -h
.......
192.168.10.220:models    2.0G   74M  2.0G   4% /opt/gfsmount

新建一个卷中不存在的目录并删除
[root@Client ~]# cd /opt/gfsmount/
[root@Client gfsmount]# mkdir testDir001
[root@Client gfsmount]# rm -rf testDir001

设置扩展属性触发自愈
[root@Client gfsmount]# setfattr -n trusted.non-existent-key -v abc /opt/gfsmount
[root@Client gfsmount]# setfattr -x trusted.non-existent-key /opt/gfsmount

4.5）检查当前节点是否挂起xattrs
再次查询故障节点的备份节点(GlusterFS-slave2)目录的扩展属性
[root@GlusterFS-slave2 ~]# getfattr -d -m. -e hex /data/gluster
getfattr: Removing leading '/' from absolute path names
# file: data/gluster
trusted.afr.dirty=0x000000000000000000000000
trusted.afr.models-client-2=0x000000000000000000000000
trusted.afr.models-client-3=0x000000000000000200000002
trusted.gfid=0x00000000000000000000000000000001
trusted.glusterfs.dht=0x00000001000000007fffffffffffffff
trusted.glusterfs.volume-id=0x8eafb261e0d24f3b8e0905475c63dcc6

注意：留意第5行,表示xattrs已经将源标记为GlusterFS-slave3:/data/gluster

4.6）检查卷的状态是否显示需要替换
[root@GlusterFS-slave3 ~]# gluster volume heal models info
Brick GlusterFS-master:/data/gluster/
Number of entries: 0

Brick GlusterFS-slave:/data/gluster/
Number of entries: 0

Brick GlusterFS-slave2:/data/gluster/
/
Number of entries: 1

Brick 192.168.10.220:/data/gluster
Status: Transport endpoint is not connected

注：状态提示传输端点未连接（最后一行）

4.7）使用强制提交完成操作
[root@GlusterFS-slave3 ~]# gluster volume replace-brick models 192.168.10.220:/data/gluster 192.168.10.220:/data/gluster1 commit force
提示如下表示正常完成：
volume replace-brick: success: replace-brick commit force operation successful

-------------------------------------------------------------------------------------
注意：也可以将数据恢复到另外一台服务器，详细命令如下（192.168.10.230为新增的另一个glusterfs节点）（可选）：
# gluster peer probe 192.168.10.230
# gluster volume replace-brick models 192.168.10.220:/data/gluster 192.168.10.230:/data/gluster commit force
-------------------------------------------------------------------------------------

4.8）检查存储的在线状态
[root@GlusterFS-slave3 ~]# gluster volume status
Status of volume: models
Gluster process           Port  Online  Pid
------------------------------------------------------------------------------
Brick 192.168.10.239:/data/gluster      49156 Y 16040
Brick 192.168.10.212:/data/gluster      49157 Y 5544
Brick 192.168.10.204:/data/gluster      49157 Y 12432
Brick 192.168.10.220:/data/gluster1     49159 Y 11363
NFS Server on localhost         N/A N N/A
Self-heal Daemon on localhost       N/A Y 11375
NFS Server on 192.168.10.204        N/A N N/A
Self-heal Daemon on 192.168.10.204      N/A Y 12494
NFS Server on 192.168.10.212        N/A N N/A
Self-heal Daemon on 192.168.10.212      N/A Y 5625
NFS Server on GlusterFS-master        N/A N N/A
Self-heal Daemon on GlusterFS-master      N/A Y 16161

Task Status of Volume models
------------------------------------------------------------------------------
There are no active volume tasks


从上面信息可以看出，192.168.10.220（GlusterFS-slave3）节点的Online项的状态为"Y"了，不过存储目录是/data/gluster1

这个时候，查看GlusterFS-slave3节点的存储目录，发现数据已经恢复了
[root@GlusterFS-slave3 ~]# ls /data/gluster/
copy-test-002  copy-test-014  copy-test-036  copy-test-047  copy-test-059  copy-test-069  copy-test-080  copy-test-097
copy-test-003  copy-test-018  copy-test-037  copy-test-049  copy-test-061  copy-test-070  copy-test-084
copy-test-005  copy-test-020  copy-test-040  copy-test-050  copy-test-062  copy-test-071  copy-test-085
copy-test-007  copy-test-025  copy-test-042  copy-test-053  copy-test-064  copy-test-072  copy-test-089
copy-test-009  copy-test-026  copy-test-043  copy-test-055  copy-test-066  copy-test-074  copy-test-091
copy-test-010  copy-test-027  copy-test-044  copy-test-056  copy-test-067  copy-test-075  copy-test-092
copy-test-013  copy-test-035  copy-test-045  copy-test-058  copy-test-068  copy-test-076  copy-test-096
[root@GlusterFS-slave3 ~]# ll /data/gluster/|wc -l
51

温馨提示：
上面模拟的故障是gluster节点的存储目录所在的分区挂载失败，导致存储目录不在的数据修复方法。
如果存储目录删除了，还可以根据文档：http://www.cnblogs.com/kevingrace/p/8778123.html中介绍的复制卷数据故障的相关方法进行数据恢复。
```

