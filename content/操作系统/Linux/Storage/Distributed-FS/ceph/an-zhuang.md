# 安装

（一个ceph cluster集群，至少需要1个mon节点和2个osd节点）

```text
0）基本信息：
这里我只是测试环境，所以使用一个监控节点，两个存储节点，具体如下：

ip地址              主机名           ceph磁盘          备注    
192.168.10.200      ceph-node1       20G               作为mds、mon、osd0
192.168.10.201      ceph-node2       20G               作为osd1
192.168.10.202      ceph-node3       20G               作为osd2
192.168.10.203      ceph-client      挂载点：/cephfs   ceph客户端

Ceph的文件系统作为一个目录挂载到客户端cephclient的/cephfs目录下，可以像操作普通目录一样对此目录进行操作。
```

```text
1）安装前准备
分别在ceph的三个节点机（ceph-node1、ceph-node2、ceph-node3）上添加hosts
[root@ceph-node1 ~]# cat /etc/hosts
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
192.168.10.200 ceph-node1
192.168.10.201 ceph-node2
192.168.10.202 ceph-node3

添加完hosts后，做下测试，保证使用hosts中映射的主机名能ping通。
[root@ceph-node3 ~]# ping ceph-node1
PING ceph-node1 (192.168.10.200) 56(84) bytes of data.
64 bytes from ceph-node1 (192.168.10.200): icmp_seq=1 ttl=64 time=0.211 ms
64 bytes from ceph-node1 (192.168.10.200): icmp_seq=2 ttl=64 time=0.177 ms

[root@ceph-node3 ~]# ping ceph-node2
PING ceph-node2 (192.168.10.201) 56(84) bytes of data.
64 bytes from ceph-node2 (192.168.10.201): icmp_seq=1 ttl=64 time=1.27 ms
64 bytes from ceph-node2 (192.168.10.201): icmp_seq=2 ttl=64 time=0.169 ms

分别在ceph的三个节点机（ceph-node1、ceph-node2、ceph-node3）上创建用户ceph，密码统一设置为ceph
[root@ceph-node1 ~]# adduser ceph
[root@ceph-node1 ~]# echo "ceph"|passwd --stdin ceph
Changing password for user ceph.
passwd: all authentication tokens updated successfully.

在每个Ceph节点中为用户增加 root 权限
[root@ceph-node1 ~]# echo "ceph ALL = (root) NOPASSWD:ALL" | sudo tee /etc/sudoers.d/ceph
[root@ceph-node1 ~]# chmod 0440 /etc/sudoers.d/ceph

测试是否具有sudo权限
[root@ceph-node1 ~]# su - ceph
[ceph@ceph-node1 ~]$ sudo su -
[root@ceph-node1 ~]#

关闭防火墙
[root@ceph-node1 ~]# service iptables stop
[root@ceph-node1 ~]# chkconfig iptables off
[root@ceph-node1 ~]# setenforce 0
[root@ceph-node1 ~]# getenforce
Permissive
[root@ceph-node1 ~]# sed -i 's_SELINUX=enforcing_SELINUX=disabled_g' /etc/sysconfig/selinux
```

```text
2）Ceph管理节点部署设置（root用户操作）。这个只需要在ceph-node1监控节点操作即可。
增加Ceph资料库至 ceph-deploy 管理节点，之后安装 ceph-deploy。
[root@ceph-node1 ~]# rpm -Uvh http://download.ceph.com/rpm-hammer/el6/noarch/ceph-release-1-1.el6.noarch.rpm
[root@ceph-node1 ~]# yum install ceph-deploy -y
```

```text
3）数据节点磁盘挂载（root用户）
在ceph-node1、ceph-node2、ceph-node3上分别挂载了一块20G大小的磁盘作为ceph的数据存储测试使用。
需要将它们分区，创建xfs文件系统。

由于本案例中的四个虚拟机均是使用WebvirtMgr创建的kvm虚拟机
创建kvm虚拟机，具体参考：http://www.cnblogs.com/kevingrace/p/5737724.html

现在需要在宿主机上创建3个20G的盘，然后将这三个盘分别挂载到ceph-node1、ceph-node2、ceph-node3这三个虚拟机上
操作记录如下：

在宿主机上的操作
[root@kvm-server ~]# virsh list --all
 Id    Name                           State
----------------------------------------------------
 1     ceph-node1                     running
 2     ceph-node2                     running
 3     ceph-node3                     running
 4     centos6-04                     running

[root@zabbix-server ~]# ll /data/kvm/ios/*     #这里的/data/kvm/ios是webvirtmgr里创建虚拟机时定义的镜像存放地址
total 31751800
-rw-r--r--. 1 qemu qemu 3972005888 Jan 16 17:13 CentOS-6.9-x86_64-bin-DVD1.iso
-rw-r--r--. 1 root root 4521459712 Jan 16 17:14 CentOS-7-x86_64-DVD-1708.iso
-rw-------. 1 qemu qemu 4914610176 Feb  6 17:58 ceph-node1.img
-rw-------. 1 qemu qemu 4417716224 Feb  6 17:59 ceph-node2.img
-rw-------. 1 qemu qemu 4405723136 Feb  6 17:58 ceph-node4.img
-rw-------. 1 qemu qemu 3420389376 Feb  6 17:58 disk004.img

创建一个20G的新磁盘挂载到ceph-node1虚拟机上
[root@zabbix-server ~]# qemu-img create -f raw /data/kvm/ios/ceph01.img 20G
Formatting '/data/kvm/ios/ceph01.img', fmt=raw size=21474836480
[root@zabbix-server ~]# ll /data/kvm/ios/ceph01.img
-rw-r--r--. 1 root root 21474836480 Feb  6 18:00 /data/kvm/ios/ceph01.img
[root@zabbix-server ~]# du -sh /data/kvm/ios/ceph01.img
0  /data/kvm/ios/ceph01.img
[root@zabbix-server ~]# virsh attach-disk centos6-01 /data/kvm/ios/ceph01.img vdb --cache none          # 如果卸载，命令为：virsh detach-disk centos6-01 /data/kvm/ios/ceph01.img
Disk attached successfully

然后到ceph-node1虚拟机上查看，发现新建立的20G的磁盘/dev/vdb已经挂载上来了
[root@ceph-node1 ~]# fdisk -l
.........

Disk /dev/vdb: 21.5 GB, 21474836480 bytes
16 heads, 63 sectors/track, 41610 cylinders
Units = cylinders of 1008 * 512 = 516096 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x00000000

在虚拟机上对新挂载的磁盘进行创建xfs文件系统操作
[root@ceph-node1 ~]# parted /dev/vdb
GNU Parted 2.1
Using /dev/vdb
Welcome to GNU Parted! Type 'help' to view a list of commands.
(parted) mklabel gpt                                                  //在此输入mklabel gpt                                        
Warning: The existing disk label on /dev/vdb will be destroyed and all data on this disk will be lost. Do you want to continue?
Yes/No? yes                                                           //在此输入yes                                           
(parted) mkpart primary xfs 0% 100%                                   //在此输入mkpart primary xfs 0% 100%                         
(parted) quit                                                         //在此输入quit                                   
Information: You may need to update /etc/fstab.

[root@ceph-node1 ~]# fdisk -l
.......
Disk /dev/vdb: 21.5 GB, 21474836480 bytes
255 heads, 63 sectors/track, 2610 cylinders
Units = cylinders of 16065 * 512 = 8225280 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x00000000

   Device Boot      Start         End      Blocks   Id  System
/dev/vdb1               1        2611    20971519+  ee  GPT

[root@ceph-node1 ~]# mkfs.xfs /dev/vdb1
meta-data=/dev/vdb1              isize=256    agcount=4, agsize=1310592 blks
         =                       sectsz=512   attr=2, projid32bit=0
data     =                       bsize=4096   blocks=5242368, imaxpct=25
         =                       sunit=0      swidth=0 blks
naming   =version 2              bsize=4096   ascii-ci=0
log      =internal log           bsize=4096   blocks=2560, version=2
         =                       sectsz=512   sunit=0 blks, lazy-count=1
realtime =none                   extsz=4096   blocks=0, rtextents=0
————————————————————————————————————————————————————————————————————————————————————————————
报错：mkfs.xfs error: command not found.
解决办法：yum install -y xfsprogs kmod-xfs
————————————————————————————————————————————————————————————————————————————————————————————

另外两台虚拟机ceph-node2和ceph-node3挂载新磁盘以及创建xfs文件系统的方法和上面一样，这里不做赘录了。
```

```text
4）存储集群搭建（ceph用户）
集群规模：1个管理节点，1个监控节点，3个数据节点。

---------------首先完成ceph-node1、ceph-node2、ceph-node3三个节点机在ceph用户下的ssh无密码登陆的信任关系（ssh无密码操作过程省略）---------------
做完ceph用户之间的ssh信任关系后，可以简单测试下。
[ceph@ceph-node1 ~]$ ssh -p22 ceph@ceph-node2
[ceph@ceph-node1 ~]$ ssh -p22 ceph@ceph-node3

[ceph@ceph-node2 ~]$ ssh -p22 ceph@ceph-node1
[ceph@ceph-node2 ~]$ ssh -p22 ceph@ceph-node3

[ceph@ceph-node3 ~]$ ssh -p22 ceph@ceph-node1
[ceph@ceph-node3 ~]$ ssh -p22 ceph@ceph-node2

---------------接着在管理节点上使用ceph-deploy（管理节点上使用ceph-deploy，所以下面操作都要在ceph-node1机器上完成）---------------
[ceph@ceph-node1 ~]$ mkdir ceph-cluster
[ceph@ceph-node1 ~]$ cd ceph-cluster
[ceph@ceph-node1 ceph-cluster]$

创建一个ceph新集群（注意下面命令中ceph-node1是monit监控节点的主机名），设置ceph-node1为mon节点
[ceph@ceph-node1 ceph-cluster]$ ceph-deploy new ceph-node1
[ceph_deploy.conf][DEBUG ] found configuration file at: /home/ceph/.cephdeploy.conf
[ceph_deploy.cli][INFO  ] Invoked (1.5.37): /usr/bin/ceph-deploy new ceph-node1
[ceph_deploy.cli][INFO  ] ceph-deploy options:
[ceph_deploy.cli][INFO  ]  username                      : None
[ceph_deploy.cli][INFO  ]  func                          : <function new at 0x112b500>
[ceph_deploy.cli][INFO  ]  verbose                       : False
[ceph_deploy.cli][INFO  ]  overwrite_conf                : False
[ceph_deploy.cli][INFO  ]  quiet                         : False
[ceph_deploy.cli][INFO  ]  cd_conf                       : <ceph_deploy.conf.cephdeploy.Conf instance at 0xf26e18>
[ceph_deploy.cli][INFO  ]  cluster                       : ceph
[ceph_deploy.cli][INFO  ]  ssh_copykey                   : True
[ceph_deploy.cli][INFO  ]  mon                           : ['ceph-node1']
[ceph_deploy.cli][INFO  ]  public_network                : None
[ceph_deploy.cli][INFO  ]  ceph_conf                     : None
[ceph_deploy.cli][INFO  ]  cluster_network               : None
[ceph_deploy.cli][INFO  ]  default_release               : False
[ceph_deploy.cli][INFO  ]  fsid                          : None
[ceph_deploy.new][DEBUG ] Creating new cluster named ceph
[ceph_deploy.new][INFO  ] making sure passwordless SSH succeeds
[ceph-node1][DEBUG ] connection detected need for sudo
[ceph-node1][DEBUG ] connected to host: ceph-node1
[ceph-node1][DEBUG ] detect platform information from remote host
[ceph-node1][DEBUG ] detect machine type
[ceph-node1][DEBUG ] find the location of an executable
[ceph-node1][INFO  ] Running command: sudo /sbin/ip link show
[ceph-node1][INFO  ] Running command: sudo /sbin/ip addr show
[ceph-node1][DEBUG ] IP addresses found: [u'192.168.10.200']
[ceph_deploy.new][DEBUG ] Resolving host ceph-node1
[ceph_deploy.new][DEBUG ] Monitor ceph-node1 at 192.168.10.200
[ceph_deploy.new][DEBUG ] Monitor initial members are ['ceph-node1']
[ceph_deploy.new][DEBUG ] Monitor addrs are ['192.168.10.200']
[ceph_deploy.new][DEBUG ] Creating a random mon key...
[ceph_deploy.new][DEBUG ] Writing monitor keyring to ceph.mon.keyring...
[ceph_deploy.new][DEBUG ] Writing initial config to ceph.conf...
Error in sys.exitfunc:

[ceph@ceph-node1 ceph-cluster]$ ll
total 28
-rw-rw-r--. 1 ceph ceph   202 Feb  6 10:37 ceph.conf
-rw-rw-r--. 1 ceph ceph 16774 Feb  6 10:55 ceph-deploy-ceph.log
-rw-------. 1 ceph ceph    73 Feb  6 10:37 ceph.mon.keyring

---------------安装ceph。这个需要在3台存储节点机器上都要安装ceph（root用户操作）---------------
[ceph@ceph-node1 ~]# yum install -y yum-plugin-prioritie
[ceph@ceph-node1 ~]# rpm -Uvh http://download.ceph.com/rpm-hammer/el6/noarch/ceph-release-1-1.el6.noarch.rpm
[ceph@ceph-node1 ~]# yum -y install ceph

[ceph@ceph-node2 ~]# yum install -y yum-plugin-prioritie
[ceph@ceph-node2 ~]# rpm -Uvh http://download.ceph.com/rpm-hammer/el6/noarch/ceph-release-1-1.el6.noarch.rpm
[ceph@ceph-node2 ~]# yum -y install ceph

[ceph@ceph-node3 ~]# yum install -y yum-plugin-prioritie
[ceph@ceph-node3 ~]# rpm -Uvh http://download.ceph.com/rpm-hammer/el6/noarch/ceph-release-1-1.el6.noarch.rpm
[ceph@ceph-node3 ~]# yum -y install ceph
————————————————————————————————————————————————————————————————————————————————————————————————
报错报错：
安装ceph报错： 
warning: rpmts_HdrFromFdno: Header V4 RSA/SHA1 Signature, key ID 460f3994: NOKEY
Retrieving key from https://download.ceph.com/keys/release.asc
Importing GPG key 0x460F3994:
 Userid: "Ceph.com (release key) <security@ceph.com>"
 From  : https://download.ceph.com/keys/release.asc
warning: rpmts_HdrFromFdno: Header V3 RSA/SHA256 Signature, key ID 0608b895: NOKEY

Public key for libunwind-1.1-3.el6.x86_64.rpm is not installed

解决办法：
Centos6.x系统下的处理办法：
yum install -y yum-utils && yum-config-manager --add-repo https://dl.fedoraproject.org/pub/epel/6/x86_64/ && yum install --nogpgcheck -y epel-release && rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-EPEL-6 && rm -f /etc/yum.repos.d/dl.fedoraproject.org*

Centos7.x系统下的处理办法：
yum install -y yum-utils && yum-config-manager --add-repo https://dl.fedoraproject.org/pub/epel/7/x86_64/ && yum install --nogpgcheck -y epel-release && rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-EPEL-7 && rm -f /etc/yum.repos.d/dl.fedoraproject.org*
——————————————————————————————————————————————————————————————————————————————————————————————

---------------接着创建并初始化监控节点（ceph-deploy是管理节点上的操作，下面操作都要在ceph-node1机器上完成）---------------
[ceph@ceph-node1 ~]$ cd ceph-cluster/
[ceph@ceph-node1 ceph-cluster]$ ceph-deploy mon create-initial
.......
.......
[ceph-node1][INFO  ] Running command: sudo /usr/bin/ceph --connect-timeout=25 --cluster=ceph --admin-daemon=/var/run/ceph/ceph-mon.ceph-node1.asok mon_status
[ceph-node1][INFO  ] Running command: sudo /usr/bin/ceph --connect-timeout=25 --cluster=ceph --name mon. --keyring=/var/lib/ceph/mon/ceph-ceph-node1/keyring auth get client.admin
[ceph-node1][INFO  ] Running command: sudo /usr/bin/ceph --connect-timeout=25 --cluster=ceph --name mon. --keyring=/var/lib/ceph/mon/ceph-ceph-node1/keyring auth get client.bootstrap-mds
[ceph-node1][INFO  ] Running command: sudo /usr/bin/ceph --connect-timeout=25 --cluster=ceph --name mon. --keyring=/var/lib/ceph/mon/ceph-ceph-node1/keyring auth get client.bootstrap-osd
[ceph-node1][INFO  ] Running command: sudo /usr/bin/ceph --connect-timeout=25 --cluster=ceph --name mon. --keyring=/var/lib/ceph/mon/ceph-ceph-node1/keyring auth get client.bootstrap-rgw
[ceph_deploy.gatherkeys][INFO  ] Storing ceph.client.admin.keyring
[ceph_deploy.gatherkeys][INFO  ] Storing ceph.bootstrap-mds.keyring
[ceph_deploy.gatherkeys][INFO  ] keyring 'ceph.mon.keyring' already exists
[ceph_deploy.gatherkeys][INFO  ] Storing ceph.bootstrap-osd.keyring
[ceph_deploy.gatherkeys][INFO  ] Storing ceph.bootstrap-rgw.keyring
[ceph_deploy.gatherkeys][INFO  ] Destroy temp directory /tmp/tmp8sI0aU
Error in sys.exitfunc:

————————————————————————————————————————————————————————————————————————————————————————————————————
如果出现报错（如果有WARNIN告警信息，则不用理会，不影响结果）:
[ERROR] Failed to execute command: /usr/sbin/service ceph -c /etc/ceph/ceph.conf start mon.ceph01
[ERROR] GenericError: Failed to create 1 monitors

解决办法如下：
[ceph@ceph-node1 ~]$ sudo yum install redhat-lsb
————————————————————————————————————————————————————————————————————————————————————————————————————

执行此命令后会初始化mon结点，并且在mon结点生成ceph.conf等文件，ceph.conf文件中声明了mon结点等信息
[ceph@ceph-node1 ceph-cluster]$ ll
total 76
-rw-------. 1 ceph ceph   113 Feb  6 11:23 ceph.bootstrap-mds.keyring
-rw-------. 1 ceph ceph   113 Feb  6 11:23 ceph.bootstrap-osd.keyring
-rw-------. 1 ceph ceph   113 Feb  6 11:23 ceph.bootstrap-rgw.keyring
-rw-------. 1 ceph ceph   127 Feb  6 11:23 ceph.client.admin.keyring
-rw-rw-r--. 1 ceph ceph   202 Feb  6 10:37 ceph.conf
-rw-rw-r--. 1 ceph ceph 47659 Feb  6 11:23 ceph-deploy-ceph.log
-rw-------. 1 ceph ceph    73 Feb  6 10:37 ceph.mon.keyring
[ceph@ceph-node1 ceph-cluster]$ cat ceph.conf
[global]
fsid = 12d4f209-69ea-4ad9-9507-b00557b42815
mon_initial_members = ceph-node1
mon_host = 192.168.10.200
auth_cluster_required = cephx
auth_service_required = cephx
auth_client_required = cephx

查看一下Ceph存储节点的硬盘情况：
[ceph@ceph-node1 ceph-cluster]$ pwd
/home/ceph/ceph-cluster
[ceph@ceph-node1 ceph-cluster]$ ceph-deploy disk list ceph-node1
[ceph_deploy.conf][DEBUG ] found configuration file at: /home/ceph/.cephdeploy.conf
[ceph_deploy.cli][INFO  ] Invoked (1.5.37): /usr/bin/ceph-deploy disk list ceph-node1
[ceph_deploy.cli][INFO  ] ceph-deploy options:
[ceph_deploy.cli][INFO  ]  username                      : None
[ceph_deploy.cli][INFO  ]  verbose                       : False
[ceph_deploy.cli][INFO  ]  overwrite_conf                : False
[ceph_deploy.cli][INFO  ]  subcommand                    : list
[ceph_deploy.cli][INFO  ]  quiet                         : False
[ceph_deploy.cli][INFO  ]  cd_conf                       : <ceph_deploy.conf.cephdeploy.Conf instance at 0x18af8c0>
[ceph_deploy.cli][INFO  ]  cluster                       : ceph
[ceph_deploy.cli][INFO  ]  func                          : <function disk at 0x18a2488>
[ceph_deploy.cli][INFO  ]  ceph_conf                     : None
[ceph_deploy.cli][INFO  ]  default_release               : False
[ceph_deploy.cli][INFO  ]  disk                          : [('ceph-node1', None, None)]
[ceph-node1][DEBUG ] connection detected need for sudo
[ceph-node1][DEBUG ] connected to host: ceph-node1
[ceph-node1][DEBUG ] detect platform information from remote host
[ceph-node1][DEBUG ] detect machine type
[ceph-node1][DEBUG ] find the location of an executable
[ceph_deploy.osd][INFO  ] Distro info: CentOS 6.9 Final
[ceph_deploy.osd][DEBUG ] Listing disks on ceph-node1...
[ceph-node1][DEBUG ] find the location of an executable
[ceph-node1][INFO  ] Running command: sudo /usr/sbin/ceph-disk list
[ceph-node1][WARNIN] WARNING:ceph-disk:Old blkid does not support ID_PART_ENTRY_* fields, trying sgdisk; may not correctly identify ceph volumes with dmcrypt
[ceph-node1][DEBUG ] /dev/sr0 other, iso9660
[ceph-node1][DEBUG ] /dev/vda :
[ceph-node1][DEBUG ]  /dev/vda1 other, ext4, mounted on /boot
[ceph-node1][DEBUG ]  /dev/vda2 other, LVM2_member
[ceph-node1][DEBUG ] /dev/vdb :
[ceph-node1][DEBUG ]  /dev/vdb1 other, xfs
Error in sys.exitfunc:

同理查看其它两个ceph节点的硬盘情况
[ceph@ceph-node1 ceph-cluster]$ ceph-deploy disk list ceph-node2
[ceph@ceph-node1 ceph-cluster]$ ceph-deploy disk list ceph-node3

——————————————————————————————————————————————————————————————————————————————————————————————————
如果报错：
[ERROR ] RuntimeError: remote connection got closed, ensure ``requiretty`` is disabled for ceph02

解决办法：
需要在ceph-node1、ceph-node2、ceph-node3三个osd节点中设置sudo权限（root账号下），如下：
[root@ceph-node1 ~]# visudo
.......
# Defaults requiretty                     //如有有一行内容，就注释掉
Defaults:ceph !requiretty                 //添加这一行内容
——————————————————————————————————————————————————————————————————————————————————————————————————

---------------添加数据节点（在三个osd节点的root账号下操作）---------------
[root@ceph-node1 ~]# mkdir /data
[root@ceph-node1 ~]# mkdir /data/osd0
[root@ceph-node1 ~]# chmod -R 777 /data/

[root@ceph-node2 ~]# mkdir /data
[root@ceph-node2 ~]# mkdir /data/osd1
[root@ceph-node2 ~]# chmod -R 777 /data/

[root@ceph-node3 ~]# mkdir /data
[root@ceph-node3 ~]# mkdir /data/osd2
[root@ceph-node3 ~]# chmod -R 777 /data/

---------------挂载ceph磁盘---------------
在三个osd节点上将上面各个新建的ceph磁盘分别挂载到对应的目录
[root@ceph-node1 ~]# mount /dev/vdb1 /data/osd0

[root@ceph-node2 ~]# mount /dev/vdb1 /data/osd1

[root@ceph-node3 ~]# mount /dev/vdb1 /data/osd2

--------------在管理节点准备OSD（本案例中的管理节点就是ceph-node1节点），在ceph用户下操作--------------
[ceph@ceph-node1 ~]$ cd ceph-cluster/
[ceph@ceph-node1 ceph-cluster]$ pwd
/home/ceph/ceph-cluster
[ceph@ceph-node1 ceph-cluster]$ ceph-deploy osd prepare ceph-node1:/data/osd0 ceph-node2:/data/osd1 ceph-node3:/data/osd2
.......
.......
[ceph-node3][INFO  ] checking OSD status...
[ceph-node3][DEBUG ] find the location of an executable
[ceph-node3][INFO  ] Running command: sudo /usr/bin/ceph --cluster=ceph osd stat --format=json
[ceph_deploy.osd][DEBUG ] Host ceph-node3 is now ready for osd use.
Error in sys.exitfunc:

---------------激活OSD（仍然在管理节点ceph-node1上操作）-----------------
（注意如果osd的目录所在磁盘是ext4格式的文件系统会报错，需要进行额外的设置）
[ceph@ceph-node1 ceph-cluster]$ pwd
/home/ceph/ceph-cluster
[ceph@ceph-node1 ceph-cluster]$ ceph-deploy osd activate ceph-node1:/data/osd0 ceph-node2:/data/osd1 ceph-node3:/data/osd2
.......
.......
[ceph-node3][INFO  ] checking OSD status...
[ceph-node3][DEBUG ] find the location of an executable
[ceph-node3][INFO  ] Running command: sudo /usr/bin/ceph --cluster=ceph osd stat --format=json
[ceph-node3][INFO  ] Running command: sudo chkconfig ceph on
Error in sys.exitfunc:
_______________________________________________________________________________________________________________
如果报错：
[WARNIN] ceph disk: Error: No cluster conf fonud in /etc/ceph with fsid dobbb980-a7a0-4562-ab8b-8d423424234
[ERROR] RuntimeError: command returned non-zero exit status: 1
[ERROR] RuntimeError: Failed to execute command: ceph-disk -v activate --mark-init sysvinit --mount /osd0

解决办法：
这是是因为多次卸载和安装ceph造成磁盘的id和cluster的uuid不一致，需要将ceph-node1的/data/osd0下的文件全部清空
（每台osd节点机对应目录下的文件都要情况，即ceph-node2的/data/osd1下的文件、ceph-node3的/data/osd2下的文件）
——————————————————————————————————————————————————————————————————————————————————————————————————————————————

------------------开机挂载磁盘（三台osd节点都要操作）-------------------
挂载好的新磁盘会在下一次重启或开机的时候失效，因此需要在每台机上对新磁盘进行挂载，并编辑/etc/fstab文件
[root@ceph-node1 ~]# cat /etc/fstab
........
/dev/vdb1               /data/osd0                   xfs     defaults        0 0

[root@ceph-node2 ~]# cat /etc/fstab
........
/dev/vdb1               /data/osd1                   xfs     defaults        0 0

[root@ceph-node3 ~]# cat /etc/fstab
........
/dev/vdb1               /data/osd2                   xfs     defaults        0 0

----------------------------分发配置和密钥----------------------------
使用ceph-deploy命令将配置文件和管理密钥复制到管理节点和它的Ceph节点。
[ceph@ceph-node1 ~]$ cd ceph-cluster/
[ceph@ceph-node1 ceph-cluster]$ pwd
/home/ceph/ceph-cluster
[ceph@ceph-node1 ceph-cluster]$ ceph-deploy admin ceph-node1 ceph-node2 ceph-node3
[ceph_deploy.conf][DEBUG ] found configuration file at: /home/ceph/.cephdeploy.conf
[ceph_deploy.cli][INFO  ] Invoked (1.5.37): /usr/bin/ceph-deploy admin ceph-node1 ceph-node2 ceph-node3
[ceph_deploy.cli][INFO  ] ceph-deploy options:
[ceph_deploy.cli][INFO  ]  username                      : None
[ceph_deploy.cli][INFO  ]  verbose                       : False
[ceph_deploy.cli][INFO  ]  overwrite_conf                : False
[ceph_deploy.cli][INFO  ]  quiet                         : False
[ceph_deploy.cli][INFO  ]  cd_conf                       : <ceph_deploy.conf.cephdeploy.Conf instance at 0x230ca70>
[ceph_deploy.cli][INFO  ]  cluster                       : ceph
[ceph_deploy.cli][INFO  ]  client                        : ['ceph-node1', 'ceph-node2', 'ceph-node3']
[ceph_deploy.cli][INFO  ]  func                          : <function admin at 0x2263050>
[ceph_deploy.cli][INFO  ]  ceph_conf                     : None
[ceph_deploy.cli][INFO  ]  default_release               : False
[ceph_deploy.admin][DEBUG ] Pushing admin keys and conf to ceph-node1
[ceph-node1][DEBUG ] connection detected need for sudo
[ceph-node1][DEBUG ] connected to host: ceph-node1
[ceph-node1][DEBUG ] detect platform information from remote host
[ceph-node1][DEBUG ] detect machine type
[ceph-node1][DEBUG ] write cluster configuration to /etc/ceph/{cluster}.conf
[ceph_deploy.admin][DEBUG ] Pushing admin keys and conf to ceph-node2
[ceph-node2][DEBUG ] connection detected need for sudo
[ceph-node2][DEBUG ] connected to host: ceph-node2
[ceph-node2][DEBUG ] detect platform information from remote host
[ceph-node2][DEBUG ] detect machine type
[ceph-node2][DEBUG ] write cluster configuration to /etc/ceph/{cluster}.conf
[ceph_deploy.admin][DEBUG ] Pushing admin keys and conf to ceph-node3
[ceph-node3][DEBUG ] connection detected need for sudo
[ceph-node3][DEBUG ] connected to host: ceph-node3
[ceph-node3][DEBUG ] detect platform information from remote host
[ceph-node3][DEBUG ] detect machine type
[ceph-node3][DEBUG ] write cluster configuration to /etc/ceph/{cluster}.conf
Error in sys.exitfunc:
```

```text
-----------------------------------查看集群状态（ceph用户或root用户下都可以查看）------------------------------
[ceph@ceph-node1 ~]$ ceph health
HEALTH_OK
[ceph@ceph-node1 ~]$ ceph -s
    cluster 12d4f209-69ea-4ad9-9507-b00557b42815
     health HEALTH_OK
     monmap e1: 1 mons at {ceph-node1=192.168.10.200:6789/0}
            election epoch 2, quorum 0 ceph-node1
     osdmap e14: 3 osds: 3 up, 3 in
      pgmap v26: 64 pgs, 1 pools, 0 bytes data, 0 objects
            15459 MB used, 45944 MB / 61404 MB avail
                  64 active+clean

出现上面信息就表示ceph分布式存储集群环境已经成功了！！
——————————————————————————————————————————————————————————————————————————————————————————————————————————————————————————
======如果报错1：
2018-02-07 21:53:54.886552 7fdc5cf5a700 -1 monclient(hunting): ERROR: missing keyring, cannot use cephx for authentication
2016-02-07 21:53:54.886584 7fdc5cf5a700  0 librados: client.admin initialization error (2) No such file or directory
Error connecting to cluster: ObjectNotFound

是因为普通用户无法读取导致无法进行cephx认证，执行以下命令：
sudo chmod +r /etc/ceph/ceph.client.admin.keyring

======如果报错2：
HEALTH_WARN 64 pgs degraded; 64 pgs stuck degraded; 64 pgs stuck unclean; 64 pgs stuck undersized; 64 pgs undersized

是因为配置中默认osd=3，备份=2，与实际不符，更改配置文件ceph.conf，增加以下两行：
osd_pool_default_size = 2
osd_pool_default_min_size = 1
（不过没有生效，怀疑需要重启集群，待测试），或者添加一个osd节点（有些说法是要最少3个osd）

======如果报错3：
2018-02-07 15:21:05.597951 7fde9cb6b700 -1 monclient(hunting): ERROR: missing keyring, cannot use cephx for authentication
2018-02-07 15:21:05.597963 7fde9cb6b700  0 librados: client.admin initialization error (2) No such file or directory

是因为没有权限读取keyring，执行以下命令赋权限：
sudo chmod 777 /etc/ceph/*
——————————————————————————————————————————————————————————————————————————————————————————————————————————————————————————

通过上面操作步骤，三个ods节点的ceph服务默认是启动着的。
[root@ceph-node1 ~]# /etc/init.d/ceph status
=== mon.ceph-node1 ===
mon.ceph-node1: running {"version":"0.94.10"}
=== osd.0 ===
osd.0: running {"version":"0.94.10"}

[root@ceph-node2 ~]# /etc/init.d/ceph status
=== osd.1 ===
osd.1: running {"version":"0.94.10"}

[root@ceph-node3 ~]# /etc/init.d/ceph status
=== osd.2 ===
osd.2: running {"version":"0.94.10"}

========================================重新部署（温馨提示）==========================================
部署过程中如果出现任何奇怪的问题无法解决，可以简单的删除所有节点，一切从头再来：
# ceph-deploy purge ceph-mon1 ceph-mon2 ceph-mon3 ceph-osd1 ceph-osd2
# ceph-deploy purgedata ceph-mon1 ceph-mon2 ceph-mon3 ceph-osd1 ceph-osd2
# ceph-deploy forgetkey
======================================================================================================

5）创建文件系统
创建文件系统的步骤参考官网：http://docs.ceph.com/docs/master/cephfs/createfs/
对于一个刚创建的MDS服务，虽然服务是运行的，但是它的状态直到创建 pools 以及文件系统的时候才会变为Active.

还没有创建时候的状态
[ceph@ceph-node1 ~]$ ceph mds stat
e1: 0/0/0 up

-------------------创建管理节点（ceph-node1节点上）-------------------
[ceph@ceph-node1 ~]$ cd ceph-cluster/
[ceph@ceph-node1 ceph-cluster]$ pwd
/home/ceph/ceph-cluster
[ceph@ceph-node1 ceph-cluster]$ ceph-deploy mds create ceph-node1
[ceph_deploy.conf][DEBUG ] found configuration file at: /home/ceph/.cephdeploy.conf
[ceph_deploy.cli][INFO  ] Invoked (1.5.37): /usr/bin/ceph-deploy mds create ceph-node1
[ceph_deploy.cli][INFO  ] ceph-deploy options:
[ceph_deploy.cli][INFO  ]  username                      : None
[ceph_deploy.cli][INFO  ]  verbose                       : False
[ceph_deploy.cli][INFO  ]  overwrite_conf                : False
[ceph_deploy.cli][INFO  ]  subcommand                    : create
[ceph_deploy.cli][INFO  ]  quiet                         : False
[ceph_deploy.cli][INFO  ]  cd_conf                       : <ceph_deploy.conf.cephdeploy.Conf instance at 0xcf0290>
[ceph_deploy.cli][INFO  ]  cluster                       : ceph
[ceph_deploy.cli][INFO  ]  func                          : <function mds at 0xcd8398>
[ceph_deploy.cli][INFO  ]  ceph_conf                     : None
[ceph_deploy.cli][INFO  ]  mds                           : [('ceph-node1', 'ceph-node1')]
[ceph_deploy.cli][INFO  ]  default_release               : False
[ceph_deploy.mds][DEBUG ] Deploying mds, cluster ceph hosts ceph-node1:ceph-node1
[ceph-node1][DEBUG ] connection detected need for sudo
[ceph-node1][DEBUG ] connected to host: ceph-node1
[ceph-node1][DEBUG ] detect platform information from remote host
[ceph-node1][DEBUG ] detect machine type
[ceph_deploy.mds][INFO  ] Distro info: CentOS 6.9 Final
[ceph_deploy.mds][DEBUG ] remote host will use sysvinit
[ceph_deploy.mds][DEBUG ] deploying mds bootstrap to ceph-node1
[ceph-node1][DEBUG ] write cluster configuration to /etc/ceph/{cluster}.conf
[ceph-node1][DEBUG ] create path if it doesn't exist
[ceph-node1][INFO  ] Running command: sudo ceph --cluster ceph --name client.bootstrap-mds --keyring /var/lib/ceph/bootstrap-mds/ceph.keyring auth get-or-create mds.ceph-node1 osd allow rwx mds allow mon allow profile mds -o /var/lib/ceph/mds/ceph-ceph-node1/keyring
[ceph-node1][INFO  ] Running command: sudo service ceph start mds.ceph-node1
[ceph-node1][DEBUG ] === mds.ceph-node1 ===
[ceph-node1][DEBUG ] Starting Ceph mds.ceph-node1 on ceph-node1...
[ceph-node1][DEBUG ] starting mds.ceph-node1 at :/0
[ceph-node1][INFO  ] Running command: sudo chkconfig ceph on
Error in sys.exitfunc:

注意：如果不创建mds管理节点，client客户端将不能正常挂载到ceph集群！！

[ceph@ceph-node1 ceph-cluster]$ sudo /etc/init.d/ceph status
=== mon.ceph-node1 ===
mon.ceph-node1: running {"version":"0.94.10"}
=== osd.0 ===
osd.0: running {"version":"0.94.10"}
=== mds.ceph-node1 ===
mds.ceph-node1: running {"version":"0.94.10"}

[ceph@ceph-node1 ceph-cluster]$ ceph mds stat
e1: 0/0/0 up

--------------------创建pool------------------
查看pool。pool是ceph存储数据时的逻辑分区,它起到namespace的作用
[ceph@ceph-node1 ceph-cluster]$ ceph osd lspools
0 rbd,

新创建的ceph集群只有rdb一个pool。这时需要创建一个新的pool
新建pool，可参考官网：http://docs.ceph.com/docs/master/rados/operations/pools/ <br>
[ceph@ceph-node1 ceph-cluster]$ ceph osd pool create cephfs_data 10                //后面的数字是PG的数量
pool 'cephfs_data' created
[ceph@ceph-node1 ceph-cluster]$ ceph osd pool create cephfs_metadata 10            //创建pool的元数据
pool 'cephfs_metadata' created
[ceph@ceph-node1 ceph-cluster]$ ceph fs new myceph  cephfs_metadata cephfs_data
new fs with metadata pool 2 and data pool 1

再次查看pool
[ceph@ceph-node1 ceph-cluster]$ ceph osd lspools
0 rbd,1 cephfs_data,2 cephfs_metadata,

--------------------检验--------------------
[ceph@ceph-node1 ~]$ ceph mds stat
e5: 1/1/1 up {0=ceph-node1=up:active}

查看集群状态
[ceph@ceph-node1 ~]$ ceph -s
    cluster 12d4f209-69ea-4ad9-9507-b00557b42815
     health HEALTH_OK
     monmap e1: 1 mons at {ceph-node1=192.168.10.200:6789/0}
            election epoch 2, quorum 0 ceph-node1
     mdsmap e5: 1/1/1 up {0=ceph-node1=up:active}
     osdmap e19: 3 osds: 3 up, 3 in
      pgmap v37: 84 pgs, 3 pools, 1962 bytes data, 20 objects
            15460 MB used, 45943 MB / 61404 MB avail
                  84 active+clean

查看ceph集群端口
[root@ceph-node1 ~]# lsof -i:6789
COMMAND   PID USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
ceph-mon  568 root   13u  IPv4  65149      0t0  TCP ceph-node1:smc-https (LISTEN)
ceph-mon  568 root   23u  IPv4  73785      0t0  TCP ceph-node1:smc-https->ceph-node1:34280 (ESTABLISHED)
ceph-mon  568 root   24u  IPv4  73874      0t0  TCP ceph-node1:smc-https->ceph-node2:42094 (ESTABLISHED)
ceph-mon  568 root   25u  IPv4  73920      0t0  TCP ceph-node1:smc-https->ceph-node3:42080 (ESTABLISHED)
ceph-mon  568 root   26u  IPv4  76829      0t0  TCP ceph-node1:smc-https->ceph-node1:34354 (ESTABLISHED)
ceph-osd 4566 root   24u  IPv4  73784      0t0  TCP ceph-node1:34280->ceph-node1:smc-https (ESTABLISHED)
ceph-mds 7928 root    9u  IPv4  76828      0t0  TCP ceph-node1:34354->ceph-node1:smc-https (ESTABLISHED)

6）client挂载
client挂载方式有两种：内核kernal方式和fuse方式。由于我们使用的centos6.7内核版本太低，而升级内核时间太长，故直接采用fuse方式进行挂载。

特别需要注意的是：
client挂载前需要在client节点安装ceph-fuse，方法参考上面的操作即可！

安装ceph-fuse
[root@ceph-client ~]# rpm -Uvh https://dl.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm
[root@ceph-client ~]# yum install -y ceph-fuse

创建挂载目录
[root@ceph-client ~]# mkdir /cephfs

复制配置文件
将ceph配置文件ceph.conf从管理节点copy到client节点
[root@ceph-client ~]# rsync -e "ssh -p22" -avp root@192.168.10.200:/etc/ceph/ceph.conf /etc/ceph/
[root@ceph-client ~]# ll /etc/ceph/ceph.conf
-rw-r--r--. 1 root root 202 Feb  7 03:32 /etc/ceph/ceph.conf

复制密钥
将ceph的ceph.client.admin.keyring从管理节点copy到client节点
[root@ceph-client ~]# rsync -e "ssh -p22" -avp root@192.168.10.200:/etc/ceph/ceph.client.admin.keyring /etc/ceph/
[root@ceph-client ~]# ll /etc/ceph/ceph.client.admin.keyring
-rwxrwxrwx. 1 root root 127 Feb  7 02:55 /etc/ceph/ceph.client.admin.keyring

查看ceph授权
[root@ceph-client ~]# ceph auth list
installed auth entries:

mds.ceph-node1
   key: AQDV/3lasqfVARAAa/eOiCbWQ/ccFLkVD/9UCQ==
   caps: [mds] allow
   caps: [mon] allow profile mds
   caps: [osd] allow rwx
osd.0
   key: AQAB9XlapzMfOxAAfKLo3Z6FZMyqWUW9F5FU4Q==
   caps: [mon] allow profile osd
   caps: [osd] allow *
osd.1
   key: AQAL9XlaT06zLRAABQr9kweLPiHL2Icdgj8YsA==
   caps: [mon] allow profile osd
   caps: [osd] allow *
osd.2
   key: AQAV9XladRLjORAATbxZHpD34ztqJJ3vFGkVOg==
   caps: [mon] allow profile osd
   caps: [osd] allow *
client.admin
   key: AQCnH3lalDbeHhAAC6y7YERqDDGL+f8S+sP1bw==
   caps: [mds] allow
   caps: [mon] allow *
   caps: [osd] allow *
client.bootstrap-mds
   key: AQCoH3laUqT5JRAAiVMRF8ueWQXfgQjIPalNMQ==
   caps: [mon] allow profile bootstrap-mds
client.bootstrap-osd
   key: AQCnH3laBzU7MBAAnreE1I/0Egh8gvMseoy93w==
   caps: [mon] allow profile bootstrap-osd
client.bootstrap-rgw
   key: AQCoH3laziwXDxAAenU6cqw0gRcQ3Y0JtygtPQ==
   caps: [mon] allow profile bootstrap-rgw


挂载到指定目录
将ceph挂载到/cephfs
[root@ceph-client ~]# ceph-fuse -m 192.168.10.200:6789 /cephfs
ceph-fuse[10466]: starting ceph client
2018-02-07 04:08:47.227156 7fb6afd9e760 -1 init, newargv = 0x33d23e0 newargc=11
ceph-fuse[10466]: starting fuse

[root@ceph-client ~]# df -h
Filesystem      Size  Used Avail Use% Mounted on
/dev/vda2        48G  1.5G   45G   4% /
tmpfs           1.9G     0  1.9G   0% /dev/shm
/dev/vda1       190M   36M  145M  20% /boot
/dev/vda5        47G   52M   44G   1% /home
ceph-fuse        60G   16G   45G  26% /cephfs

如上可以看到，clinet节点已经将三个osd存储节点（每个20G）的ceph存储挂载上了，总共60G！
可以在/cephfs下存放数据，当ceph的部分osd节点挂掉后，不会影响client对ceph存储数据的读取操作！

取消挂载
[root@ceph-client ~]# umount /cephfs
```

如果client是ubuntu系统，则使用fuse挂载ceph存储的操作方法：

```text
安装ceph-fuse
# apt-get install -y ceph-fuse

或者手动安装
下载地址：http://mirrors.aliyun.com/ubuntu/pool/main/c/ceph/
下载包ceph-dbg_0.94.1-0ubuntu1_amd64.deb
# dpkg -i ceph-dbg_0.94.1-0ubuntu1_amd64.deb

创建挂载目录
# mkdir /cephfs

复制配置文件
将ceph配置文件ceph.conf从管理节点copy到client节点
# rsync -e "ssh -p22" -avp root@192.168.10.200:/etc/ceph/ceph.conf /etc/ceph/

复制密钥
将ceph的ceph.client.admin.keyring从管理节点copy到client节点
# rsync -e "ssh -p22" -avp root@192.168.10.200:/etc/ceph/ceph.client.admin.keyring /etc/ceph/

查看ceph授权
# ceph auth list

挂载到指定目录
将ceph挂载到/cephfs
# ceph-fuse -m 192.168.10.200:6789 /cephfs

取消挂载
# umount/cephfs
```

当有一半以上的OSD节点挂掉后，远程客户端挂载的Ceph存储就会使用异常了，即暂停使用。比如本案例中有3个OSD节点，当其中一个OSD节点挂掉后，客户端挂载的Ceph存储使用正常；但当有2个OSD节点挂掉后，客户端挂载的Ceph存储就不能正常使用了（表现为Ceph存储目录下的数据读写操作一直卡着状态），当OSD节点恢复后，Ceph存储也会恢复正常使用。

