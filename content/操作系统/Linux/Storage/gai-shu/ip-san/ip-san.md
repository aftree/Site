# IP-SAN

[Linux下IP SAN共享存储操作记录](https://www.cnblogs.com/kevingrace/p/8467141.html)  
![](https://github.com/aftree/A/tree/46ba3f3482b57318efea7ebcb4b9fbdafb25a241/wang-luo-cun-chu-ji-zhu/ip-san/images/907596-20180224173417276-440734321.png)

操作需求：

公司之前在阿里云上购买了6台机器，磁盘空间大小不一致，后续IDC建设好后，又将业务从阿里云上迁移到IDC机器上了。

为了不浪费阿里云上的这几台机器资源，打算将这其中的5台机器做成IP SAN共享存储，另一台机器共享这5台的SAN存储，然后跟自己的磁盘一起做成LVM逻辑卷，最后统一作为备份磁盘使用！

**1）服务器信息如下：**

```text
ip地址              数据盘空间      主机名           系统版本
192.168.10.17       200G         ipsan-node01     centos7.3
192.168.10.18       500G         ipsan-node02     centos7.3
192.168.10.5        500G         ipsan-node03     centos7.3
192.168.10.6        200G         ipsan-node04     centos7.3
192.168.10.20       100G         ipsan-node05     centos7.3
192.168.10.10       100G         ipsan-node06     centos7.3

前5个node节点作为IP-SAN存储的服务端，第6个node节点作为客户端，用来共享前5个节点的IP-SAN存储，然后第6个node节点利用这5个共享过来的IP-SAN存储和
自己的100G存储做lvm逻辑卷，最终组成一个大的存储池来使用！

首先将这6个node节点机对应的盘做格式化(6台机器的数据盘都是挂载到/data下的，需要先卸载/data，然后格式化磁盘)
接着关闭各节点服务器的iptables防火墙服务（若打开了iptables，则需要开通3260端口）。selinux也要关闭！！
```

**2）服务端的操作记录（即ipsan-node01、ipsan-node02、ipsan-node03、ipsan-node04、ipsan-node05）**

```text
关闭iptbales防火墙
[root@ipsan-node01 ~]# systemctl stop firewalld.service
[root@ipsan-node01 ~]# systemctl disable firewalld.service

关闭selinux
[root@ipsan-node01 ~]# setenforce 0
setenforce: SELinux is disabled
[root@ipsan-node01 ~]# getenforce
Disabled
[root@ipsan-node01 ~]# cat /etc/sysconfig/selinux
.......
SELINUX=disabled

卸载之前挂载到/data下的数据盘，并重新格式化
[root@ipsan-node01 ~]# fdisk -l

Disk /dev/vda: 42.9 GB, 42949672960 bytes, 83886080 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk label type: dos
Disk identifier: 0x0008d207

   Device Boot      Start         End      Blocks   Id  System
/dev/vda1   *        2048    83886079    41942016   83  Linux

Disk /dev/vdb: 214.7 GB, 214748364800 bytes, 419430400 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk label type: dos
Disk identifier: 0xda936a6f

   Device Boot      Start         End      Blocks   Id  System
/dev/vdb1            2048   419430399   209714176   83  Linux
[root@ipsan-node01 ~]# umount /data
[root@ipsan-node01 ~]# mkfs.ext4 /dev/vdb1

安装配置iSCSI Target服务端
[root@ipsan-node01 ~]# yum install -y scsi-target-utils

启动target服务，通过ss -tnl可以看到3260端口已开启
[root@ipsan-node01 ~]# systemctl enable tgtd
[root@ipsan-node01 ~]# systemctl enable tgtd
[root@ipsan-node01 ~]# systemctl status tgtd
[root@ipsan-node01 ~]# ss -tnl
State       Recv-Q Send-Q                     Local Address:Port                                    Peer Address:Port             
LISTEN      0      128                                    *:22                                                 *:*                 
LISTEN      0      128                                    *:3260                                               *:*                 
LISTEN      0      128                                   :::3260                                              :::*

服务端配置管理工具tgtadm的使用
创建一个target id 为1 name为iqn.2018-02.com.node01.san:1
[root@ipsan-node01 ~]# tgtadm -L iscsi -o new -m target -t 1 -T iqn.2018-02.com.node01.san:1

显示所有target
[root@ipsan-node01 ~]# tgtadm -L iscsi -o show -m target

向某ID为1的设备上添加一个新的LUN，其号码为1，且此设备提供给initiator使用。
/dev/vdb1是某"块设备"的路径，此块设备也可以是raid或lvm设备。lun0已经被系统预留
[root@ipsan-node01 ~]# tgtadm -L iscsi -o new -m logicalunit -t 1 -l 1 -b /dev/vdb1
[root@ipsan-node01 ~]# tgtadm -L iscsi -o show -m target

定义某target的基于主机的访问控制列表，192.168.10.0/24表示允许访问此target的initiator客户端的列表
[root@ipsan-node01 ~]# tgtadm -L iscsi -o bind -m target -t 1 -I 192.168.10.0/24

++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
温馨提示：

如果该节点还有一个块设备/dev/vdb2需要添加到san存储里,可以再次向ID为1的设备上添加一个新的LUN，号码为2
# tgtadm -L iscsi -o new -m logicalunit -t 1 -l 2 -b /dev/vdb2
# tgtadm -L iscsi -o show -m target

解除target的基于主机的访问控制列表权限
# tgtadm -L iscsi -o unbind -m target -t 1 -I 192.168.10.0/24
# tgtadm -L iscsi -o show -m target

删除target中的LUN
# tgtadm -L iscsi -o delete -m target -t 1
# tgtadm -L iscsi -o show -m target
++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++

同理，另外四个节点的操作如上一致：
同样需要关闭iptables和selinux
需要卸载之前挂载到/data下的数据盘，并重新格式化

另外四个节点服务端配置管理工具tgtadm的使用分别如下：
[root@ipsan-node02 ~]# yum -y install scsi-target-utils
[root@ipsan-node02 ~]# systemctl enable tgtd
[root@ipsan-node02 ~]# systemctl enable tgtd
[root@ipsan-node02 ~]# systemctl status tgtd
[root@ipsan-node02 ~]# ss -tnl
[root@ipsan-node02 ~]# tgtadm -L iscsi -o new -m target -t 1 -T iqn.2018-02.com.node02.san:1
[root@ipsan-node02 ~]# tgtadm -L iscsi -o show -m target
[root@ipsan-node02 ~]# tgtadm -L iscsi -o new -m logicalunit -t 1 -l 1 -b /dev/vdb1
[root@ipsan-node02 ~]# tgtadm -L iscsi -o show -m target
[root@ipsan-node02 ~]# tgtadm -L iscsi -o bind -m target -t 1 -I 192.168.10.0/24
[root@ipsan-node02 ~]# tgtadm -L iscsi -o show -m target

[root@ipsan-node03 ~]# yum -y install scsi-target-utils
[root@ipsan-node03 ~]# systemctl enable tgtd
[root@ipsan-node03 ~]# systemctl enable tgtd
[root@ipsan-node03 ~]# systemctl status tgtd
[root@ipsan-node03 ~]# ss -tnl
[root@ipsan-node03 ~]# tgtadm -L iscsi -o new -m target -t 1 -T iqn.2018-02.com.node03.san:1
[root@ipsan-node03 ~]# tgtadm -L iscsi -o show -m target
[root@ipsan-node03 ~]# tgtadm -L iscsi -o new -m logicalunit -t 1 -l 1 -b /dev/vdb1
[root@ipsan-node03 ~]# tgtadm -L iscsi -o show -m target
[root@ipsan-node03 ~]# tgtadm -L iscsi -o bind -m target -t 1 -I 192.168.10.0/24
[root@ipsan-node03 ~]# tgtadm -L iscsi -o show -m target

[root@ipsan-node04 ~]# yum -y install scsi-target-utils
[root@ipsan-node04 ~]# systemctl enable tgtd
[root@ipsan-node04 ~]# systemctl enable tgtd
[root@ipsan-node04 ~]# systemctl status tgtd
[root@ipsan-node04 ~]# ss -tnl
[root@ipsan-node04 ~]# tgtadm -L iscsi -o new -m target -t 1 -T iqn.2018-02.com.node04.san:1
[root@ipsan-node04 ~]# tgtadm -L iscsi -o show -m target
[root@ipsan-node04 ~]# tgtadm -L iscsi -o new -m logicalunit -t 1 -l 1 -b /dev/vdb1
[root@ipsan-node04 ~]# tgtadm -L iscsi -o show -m target
[root@ipsan-node04 ~]# tgtadm -L iscsi -o bind -m target -t 1 -I 192.168.10.0/24
[root@ipsan-node04 ~]# tgtadm -L iscsi -o show -m target

[root@ipsan-node05 ~]# yum -y install scsi-target-utils
[root@ipsan-node05 ~]# systemctl enable tgtd
[root@ipsan-node05 ~]# systemctl enable tgtd
[root@ipsan-node05 ~]# systemctl status tgtd
[root@ipsan-node05 ~]# ss -tnl
[root@ipsan-node05 ~]# tgtadm -L iscsi -o new -m target -t 1 -T iqn.2018-02.com.node05.san:1
[root@ipsan-node05 ~]# tgtadm -L iscsi -o show -m target
[root@ipsan-node05 ~]# tgtadm -L iscsi -o new -m logicalunit -t 1 -l 1 -b /dev/vdb1
[root@ipsan-node05 ~]# tgtadm -L iscsi -o show -m target
[root@ipsan-node05 ~]# tgtadm -L iscsi -o bind -m target -t 1 -I 192.168.10.0/24
[root@ipsan-node05 ~]# tgtadm -L iscsi -o show -m target

++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
温馨提示：

上面5个节点的target服务端是通过命令行进行配置的，其实除此之外，也可以通过编辑文件的方式定义target服务端。
方法如下：
[root@ipsan-node01 ~]# cd /etc/tgt/
[root@ipsan-node01 tgt]# ls targets.conf
targets.conf
[root@ipsan-node01 tgt]# vim targets.conf       //添加定义内容如下：
.......
<target iqn.2018-02.com.node01.san:1>          //命令
    backing-store /dev/vdb1                    //共享的设备分区
    initiator-address 192.168.10.0/24          //允许访问的ip地址段。（也可以允许某个具体的ip地址，如果是多个具体的ip地址，就写多行initiator-address的配置）
</target>

如果该节点还有一个块设备/dev/vdb2需要添加到san存储里,则再添加定义如下：
<target iqn.2018-02.com.node01.san:2>
    backing-store /dev/vdb2
    initiator-address 192.168.10.0/24
</target>

重启tgtd
[root@ipsan-node01 tgt]# systemctl restart tgtd
[root@ipsan-node01 tgt]# tgtadm -L iscsi -o show -m target

其他4个节点的配置同理
++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
```

**3）客户端的操作记录（即ipsan-node06）**

```text
关闭iptbales防火墙
[root@ipsan-node06 ~]# systemctl stop firewalld.service
[root@ipsan-node06 ~]# systemctl disable firewalld.service

关闭selinux
[root@ipsan-node06 ~]# setenforce 0
setenforce: SELinux is disabled
[root@ipsan-node06 ~]# getenforce
Disabled
[root@ipsan-node06 ~]# cat /etc/sysconfig/selinux
.......
SELINUX=disabled

卸载之前挂载到/data下的数据盘，并重新格式化
[root@ipsan-node01 ~]# fdisk -l
Disk /dev/vda: 42.9 GB, 42949672960 bytes, 83886080 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk label type: dos
Disk identifier: 0x0008e3b4

   Device Boot      Start         End      Blocks   Id  System
/dev/vda1   *        2048    83886079    41942016   83  Linux

Disk /dev/vdb: 107.4 GB, 107374182400 bytes, 209715200 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk label type: dos
Disk identifier: 0xf450445d

   Device Boot      Start         End      Blocks   Id  System
/dev/vdb1            2048   209715199   104856576   83  Linux

[root@ipsan-node06 ~]# umount /data
[root@ipsan-node06 ~]# mkfs.ext4 /dev/vdb1

安装iscsi-initiator-utils工具
[root@ipsan-node06 ~]# yum install -y iscsi-initiator-utils 
[root@ipsan-node06 ~]# cat /etc/iscsi/initiatorname.iscsi 
[root@ipsan-node06 ~]# echo "InitiatorName=`iscsi-iname -p iqn.2018-02.com.node01`" > /etc/iscsi/initiatorname.iscsi 
[root@ipsan-node06 ~]# echo "InitiatorAlias=initiator1" >> /etc/iscsi/initiatorname.iscsi 
[root@ipsan-node06 ~]# cat /etc/iscsi/initiatorname.iscsi

[root@ipsan-node06 ~]# systemctl enable iscsi
[root@ipsan-node06 ~]# systemctl start iscsi
[root@ipsan-node06 ~]# systemctl status iscsi
[root@ipsan-node06 ~]# ss -tnl
State       Recv-Q Send-Q                     Local Address:Port                                    Peer Address:Port             
LISTEN      0      128                                    *:22                                                 *:*                 
LISTEN      0      128                        192.168.10.10:3128                                               *:*                 
LISTEN      0      1                              127.0.0.1:32000                                              *:*  

iscsiadm是个模式化的工具，其模式可通过-m或--mode选项指定，常见的模式有discovery、node、fw、session、host、iface几个，
如果没有额外指定其它选项，则discovery和node会显示其相关的所有记录；session用于显示所有的活动会话和连接，fw显示所有的启动固件值，
host显示所有的iSCSI主机，iface显示/var/lib/iscsi/ifaces目录中的所有ifaces设定。

++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
温馨提示：
iscsiadm命令参数如下：
iscsiadm -m discovery [ -d debug_level ] [ -P printlevel ] [ -I iface -t type -p ip:port [ -l ] ]  
iscsiadm -m node [ -d debug_level ] [ -P printlevel ] [ -L all,manual,automatic ] [ -U all,manual,automatic ] [ [ -T tar-getname -p ip:port -I iface ] [ -l | -u | -R | -s] ] [ [ -o operation ]  


-d, --debug=debug_level   显示debug信息，级别为0-8； 
-l, --login 
-t, --type=type  这里可以使用的类型为sendtargets(可简写为st)、slp、fw和 isns，此选项仅用于discovery模式，且目前仅支持st、fw和isns；其中st表示允许每个iSCSI target发送一个可用target列表给initiator； 
-p, --portal=ip[:port]  指定target服务的IP和端口； 
-m, --mode op  可用的mode有discovery, node, fw, host iface 和 session 
-T, --targetname=targetname  用于指定target的名字 
-u, --logout  
-o, --op=OPEARTION：指定针对discoverydb数据库的操作，其仅能为new、delete、update、show和nonpersistent其中之一； 
-I, --interface=[iface]：指定执行操作的iSCSI接口，这些接口定义在/var/lib/iscsi/ifaces中；
++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++

依次发现服务端的san存储设备（只要能发现设备即可）
[root@ipsan-node06 ~]# iscsiadm -m discovery -t st -p 192.168.10.17
Starting iscsid:
192.168.10.17:3260,1 iqn.2018-02.com.node01.san:1

[root@ipsan-node06 ~]# iscsiadm -m discovery -t st -p 192.168.10.18
Starting iscsid:
192.168.10.18:3260,1 iqn.2018-02.com.node02.san:1

[root@ipsan-node06 ~]# iscsiadm -m discovery -t st -p 192.168.10.5
Starting iscsid:
192.168.10.5:3260,1 iqn.2018-02.com.node03.san:1

[root@ipsan-node06 ~]# iscsiadm -m discovery -t st -p 192.168.10.6
Starting iscsid:
192.168.10.6:3260,1 iqn.2018-02.com.node04.san:1

[root@ipsan-node06 ~]# iscsiadm -m discovery -t st -p 192.168.10.20
Starting iscsid:
192.168.10.20:3260,1 iqn.2018-02.com.node05.san:1

[root@ipsan-node06 ~]# ls /var/lib/iscsi/send_targets/
192.168.10.10,3260  192.168.10.17,3260  192.168.10.18,3260  192.168.10.20,3260  192.168.10.5,3260  192.168.10.6,3260
[root@ipsan-node06 ~]# ls /var/lib/iscsi/send_targets/
192.168.10.10,3260  192.168.10.17,3260  192.168.10.18,3260  192.168.10.20,3260  192.168.10.5,3260  192.168.10.6,3260

依次登陆发现的san存储设备
[root@ipsan-node06 ~]# iscsiadm -m node -T iqn.2018-02.com.node01.san:1 -p 192.168.10.17 -l
[root@ipsan-node06 ~]# iscsiadm -m node -T iqn.2018-02.com.node02.san:1 -p 192.168.10.18 -l
[root@ipsan-node06 ~]# iscsiadm -m node -T iqn.2018-02.com.node03.san:1 -p 192.168.10.5 -l
[root@ipsan-node06 ~]# iscsiadm -m node -T iqn.2018-02.com.node04.san:1 -p 192.168.10.6 -l
[root@ipsan-node06 ~]# iscsiadm -m node -T iqn.2018-02.com.node05.san:1 -p 192.168.10.20 -l

++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
温馨提示：

关闭iSCSI服务器端
关闭iSCSI在开机重启或重启iscsi服务时自动对target进行重新连接，就需要在该客户机彻底将该target条目信息删除：
退出target会话或卸载iscsi设备（例如退出ipsan-node01节点的target会话）：
# iscsiadm -m node -T node -T iqn.2018-02.com.node01.san:1 -p 192.168.10.17 -u

删除target条目的记录（例如退出ipsan-node01节点的target条目的记录）：
# iscsiadm -m node -T node -T iqn.2018-02.com.node01.san:1 -p 192.168.10.17 -o delete

特别注意：
在客户端删除了之前discovery发现的可用的target条目，则重启或重启服务后将不会自动进行重连接。
# ls /var/lib/iscsi/send_targets/
# ls /var/lib/iscsi/
# rm -rf /var/lib/iscsi/*
# ls /var/lib/iscsi/
++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++

验证能否看到服务端设备
[root@ipsan-node06 ~]# fdisk -l /dev/sd[a-z]          //或者直接使用命令"fdisk -l"

Disk /dev/vda: 42.9 GB, 42949672960 bytes, 83886080 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk label type: dos
Disk identifier: 0x0008e3b4

   Device Boot      Start         End      Blocks   Id  System
/dev/vda1   *        2048    83886079    41942016   83  Linux

Disk /dev/vdb: 107.4 GB, 107374182400 bytes, 209715200 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk label type: dos
Disk identifier: 0xf450445d

   Device Boot      Start         End      Blocks   Id  System
/dev/vdb1            2048   209715199   104856576   83  Linux

Disk /dev/sda: 214.7 GB, 214747316224 bytes, 419428352 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk label type: dos
Disk identifier: 0x149fdfec

Disk /dev/sdb: 536.9 GB, 536869863424 bytes, 1048573952 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk label type: dos
Disk identifier: 0xc74ea52c

Disk /dev/sdc: 536.9 GB, 536869863424 bytes, 1048573952 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk label type: dos
Disk identifier: 0x6663eaa6

Disk /dev/sdd: 214.7 GB, 214747316224 bytes, 419428352 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk label type: dos
Disk identifier: 0x46738964

Disk /dev/sde: 107.4 GB, 107373133824 bytes, 209713152 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk label type: dos
Disk identifier: 0xe78614cc

如上信息，可知客户端节点ipsan-node06上已经发现了其他5个服务端节点的存储设置了！
接着对发现的存储设置依次进行分区
[root@ipsan-node06 ~]# fdisk /dev/sda
依次输入p->n->p->回车->回车->回车->w
[root@ipsan-node06 ~]# partprobe

[root@ipsan-node06 ~]# fdisk /dev/sdb
依次输入p->n->p->回车->回车->回车->w
[root@ipsan-node06 ~]# partprobe

[root@ipsan-node06 ~]# fdisk /dev/sdc
依次输入p->n->p->回车->回车->回车->w
[root@ipsan-node06 ~]# partprobe

[root@ipsan-node06 ~]# fdisk /dev/sdd
依次输入p->n->p->回车->回车->回车->w
[root@ipsan-node06 ~]# partprobe

[root@ipsan-node06 ~]# fdisk /dev/sde
依次输入p->n->p->回车->回车->回车->w
[root@ipsan-node06 ~]# partprobe

再次查看设备分区情况
[root@ipsan-node06 backup]# fdisk -l /dev/sd[a-z]

Disk /dev/sda: 214.7 GB, 214747316224 bytes, 419428352 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk label type: dos
Disk identifier: 0x149fdfec

   Device Boot      Start         End      Blocks   Id  System
/dev/sda1            2048   419428351   209713152   83  Linux

Disk /dev/sdb: 536.9 GB, 536869863424 bytes, 1048573952 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk label type: dos
Disk identifier: 0xc74ea52c

   Device Boot      Start         End      Blocks   Id  System
/dev/sdb1            2048  1048573951   524285952   83  Linux

Disk /dev/sdc: 536.9 GB, 536869863424 bytes, 1048573952 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk label type: dos
Disk identifier: 0x6663eaa6

   Device Boot      Start         End      Blocks   Id  System
/dev/sdc1            2048  1048573951   524285952   83  Linux

Disk /dev/sdd: 214.7 GB, 214747316224 bytes, 419428352 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk label type: dos
Disk identifier: 0x46738964

   Device Boot      Start         End      Blocks   Id  System
/dev/sdd1            2048   419428351   209713152   83  Linux

Disk /dev/sde: 107.4 GB, 107373133824 bytes, 209713152 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk label type: dos
Disk identifier: 0xe78614cc

   Device Boot      Start         End      Blocks   Id  System
/dev/sde1            2048   209713151   104855552   83  Linux

在服务端节点ipsan-node02上，使用自己的数据磁盘/dev/vdb1和上面发现的5个客户端节点的san存储设备创建lvm逻辑卷
创建pv（如果没有pvcreate等命令，可以使用"yum install -y lvm2"进行安装）
[root@ipsan-node06 ~]# pvcreate /dev/{vdb1,sda1,sdb1,sdc1,sdd1,sde1}
[root@ipsan-node06 ~]# pvs                   //或者使用命令"pvdisplay"
  PV         VG  Fmt  Attr PSize    PFree
  /dev/sda1  vg0 lvm2 a--  <200.00g     0
  /dev/sdb1  vg0 lvm2 a--  <500.00g     0
  /dev/sdc1  vg0 lvm2 a--  <500.00g     0
  /dev/sdd1  vg0 lvm2 a--  <200.00g     0
  /dev/sde1  vg0 lvm2 a--  <100.00g <2.54g
  /dev/vdb1  vg0 lvm2 a--  <100.00g     0

创建vg
[root@ipsan-node06 ~]# vgcreate vg0 /dev/{vdb1,sda1,sdb1,sdc1,sdd1,sde1}
[root@ipsan-node06 ~]# vgs                //或者使用命令"vgdisplay"
  VG  #PV #LV #SN Attr   VSize VFree
  vg0   6   1   0 wz--n- 1.56t <2.54g

创建lv
[root@ipsan-node06 ~]# lvcreate -L +1.56t -n lv01 vg0
[root@ipsan-node06 ~]# lvs              //或者使用命令"lvdisplay"
  LV   VG  Attr       LSize Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert
  lv01 vg0 -wi-ao---- 1.56t

格式化lvm逻辑卷磁盘
[root@ipsan-node06 ~]# mkfs.ext4 /dev/vg0/lv01

挂载lvm逻辑卷磁盘
[root@ipsan-node06 ~]# mkdir /backup
[root@ipsan-node06 ~]# mount /dev/vg0/lv01 /backup

检查lvm逻辑卷磁盘是否挂载上
[root@ipsan-node06 ~]# df -h
Filesystem            Size  Used Avail Use% Mounted on
/dev/vda1              40G  3.4G   34G  10% /
devtmpfs              7.8G   78M  7.7G   1% /dev
tmpfs                 7.8G   12K  7.8G   1% /dev/shm
tmpfs                 7.8G  440K  7.8G   1% /run
tmpfs                 7.8G     0  7.8G   0% /sys/fs/cgroup
/dev/mapper/vg0-lv01  1.6T   60k  1.6T   0% /backup

使用lsblk检查设备信息
[root@ipsan-node06 ~]# lsblk
NAME         MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda            8:0    0  200G  0 disk
└─sda1         8:1    0  200G  0 part
  └─vg0-lv01 252:0    0  1.6T  0 lvm  /backup
sdb            8:16   0  500G  0 disk
└─sdb1         8:17   0  500G  0 part
  └─vg0-lv01 252:0    0  1.6T  0 lvm  /backup
sdc            8:32   0  500G  0 disk
└─sdc1         8:33   0  500G  0 part
  └─vg0-lv01 252:0    0  1.6T  0 lvm  /backup
sdd            8:48   0  200G  0 disk
└─sdd1         8:49   0  200G  0 part
  └─vg0-lv01 252:0    0  1.6T  0 lvm  /backup
sde            8:64   0  100G  0 disk
└─sde1         8:65   0  100G  0 part
  └─vg0-lv01 252:0    0  1.6T  0 lvm  /backup
sr0           11:0    1 1024M  0 rom 
vda          253:0    0   40G  0 disk
└─vda1       253:1    0   40G  0 part /
vdb          253:16   0  100G  0 disk
└─vdb1       253:17   0  100G  0 part
  └─vg0-lv01 252:0    0  1.6T  0 lvm  /backup
```

