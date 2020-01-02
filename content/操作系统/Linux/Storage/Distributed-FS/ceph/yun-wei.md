# 运维

**一、Ceph简单介绍**  
1）OSDs:Ceph的OSD守护进程（OSD）存储数据，处理数据复制，恢复，回填，重新调整，并通过检查其它Ceph OSD守护程序作为一个心跳 向Ceph的监视器报告一些检测信息。Ceph的存储集群需要至少2个OSD守护进程来保持一个 active + clean状态.（Ceph默认制作2个备份，但可以调整它）

2）Monitors:Ceph的监控保持集群状态映射，包括OSD\(守护进程\)映射,分组\(PG\)映射，和CRUSH映射。 Ceph 保持一个在Ceph监视器, Ceph OSD 守护进程和 PG的每个状态改变的历史（称之为“epoch”）。

3）MDS:MDS是Ceph的元数据服务器，代表存储元数据的Ceph文件系统（即Ceph的块设备和Ceph的对象存储不使用MDS）。Ceph的元数据服务器使用POSIX文件系统，用户可以执行基本命令如 ls, find,等，并且不需要在Ceph的存储集群上造成巨大的负载。

Ceph把客户端的数据以对象的形式存储到了存储池里。利用CRUSH算法，Ceph可以计算出安置组所包含的对象，并能进一步计算出Ceph OSD集合所存储的安置组。CRUSH算法能够使Ceph存储集群拥有动态改变大小、再平衡和数据恢复的能力。

**二、Ceph存储特点**

* Object：有原生的API，而且也兼容Swift和S3的API
* Block：支持精简配置、快照、克隆
* File：Posix接口，支持快照

Ceph也是分布式存储系统，它的特点是：

* 高扩展性：使用普通x86服务器，支持10~1000台服务器，支持TB到PB级的扩展。
* 高可靠性：没有单点故障，多数据副本，自动管理，自动修复。
* 高性能：数据分布均衡，并行化度高。对于objects storage和block storage,不需要元数据服务器。

无论你是想使用Ceph对象存储或是以Ceph块设备服务至云平台 ，部署Ceph文件系统或者为了其他目的而使用Ceph，所有的 Ceph存储集群部署都是从设置每个Ceph节点，配置网络和Ceph存储集群开始的。一个Ceph存储集群要求至少有一个Ceph监视器和两个Ceph OSD守护进程。当运行Ceph文件系统客户端时，必须要有Ceph元数据服务器。

**Ceph提供了3种使用场景**

* 分布式文件系统CephFS。多个客户端mount CephFS到本地，CephFS遵循POSIX接口，使用体验类似于ext4等本地文件系统。类似于其他分布式文件系统，各个CephFS客户端共享同一命名空间。
* RadosGW（rgw）对象存储。rgw使用场景类似于Amazon S3，据个人理解也类似于七牛云存储。
* 块设备rbd（Rados Block Device）。Ceph提供虚拟的rbd块设备，用户像使用SATA盘那样的物理块设备一样使用rbd。rbd的使用是排他的，每个rbd块设备是用户私有的，相对的，CephFS的使用方式是共享的。虚拟化和云计算的发展正当盛年，IaaS结合rbd块设备这一使用方式有如干柴遇烈火，因此rbd是Ceph社区开发的重心之一。本文也主要从rbd的视角来了解Ceph。

