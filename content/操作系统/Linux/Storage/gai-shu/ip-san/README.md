# SAN

**简单介绍**  
SAN，即存储区域网络（storage area network and SAN protocols），它是一种高速网络实现计算机与存储系统之间的数据传输。常见的分类是FC-SAN和IP-SAN两种。FC-SAN通过光纤通道协议转发scsi协议；IP-SAN通过TCP协议转发scsi协议，也就是IP 地址。存储设备是指一台或多台用以存储计算机数据的磁盘设备，通常指磁盘阵列，主要厂商EMC、日立等。

iSCSI（internet SCSI）技术由IBM公司研究开发，是一个供硬件设备使用的、可以在IP协议的上层运行的SCSI指令集，这种指令集合可以实现在IP网络上运行SCSI协议，使其能够在诸如高速千兆以太网上进行路由选择。iSCSI是一种新储存技术，它是将现有SCSI接口与以太网络\(Ethernet\)技术结合，使服务器可与使用IP网络的储存装置互相交换资料。

iSCSI是一种基于TCP/IP 的协议，用来建立和管理IP存储设备、主机和客户机等之间的相互连接，并创建存储区域网络（SAN）。SAN 使得SCSI 协议应用于高速数据传输网络成为可能，这种传输以数据块级别（block-level）在多个数据存储网络间进行。SCSI 结构基于C/S模式，其通常应用环境是：设备互相靠近，并且这些设备由SCSI 总线连接。

iSCSI 的主要功能是在TCP/IP 网络上的主机系统（启动器 initiator）和存储设备（目标器 target）之间进行大量数据的封装和可靠传输过程。  
完整的iSCSI系统的拓扑结构如下：

![](https://github.com/aftree/A/tree/46ba3f3482b57318efea7ebcb4b9fbdafb25a241/wang-luo-cun-chu-ji-zhu/ip-san/images/907596-20180224173116119-1133907791.png)

iSCSI简单来说，就是把SCSI指令通过TCP/IP协议封装起来，在以太网中传输。iSCSI 可以实现在IP网络上传递和运行SCSI协议，使其能够在诸如高速千兆以太网上进行数据存取，实现了数据的网际传递和管理。基于iSCSI建立的存储区域网（SAN）与基于光纤的FC-SAN相比，具有很好的性价比。

iSCSI属于端到端的会话层协议，它定义的是SCSI到TCP/IP的映射（如下图），即Initiator将SCSI指令和数据封装成iSCSI协议数据单元，向下提交给TCP层，最后封装成IP数据包在IP网络上传输，到达Target后通过解封装还原成SCSI指令和数据，再由存储控制器发送到指定的驱动器，从而实现SCSI命令和数据在IP网络上的透明传输。它整合了现有的存储协议SCSI和网络协议TCP/IP，实现了存储与TCP/IP网络的无缝融合。在本篇中，将把发起器Initiator称为客户端，将目标器Target称为服务端以方便理解。

![](https://github.com/aftree/A/tree/46ba3f3482b57318efea7ebcb4b9fbdafb25a241/wang-luo-cun-chu-ji-zhu/ip-san/images/907596-20180224173240882-115840536.png)

存储网络（SAN）：SAN 是指存储设备相互连接且与一台服务器或一个服务器群相连的网络。其中的服务器用作 SAN 的接入点。在有些配置中，SAN 也与网络相连。SAN 中将特殊

[交换机](https://baike.baidu.com/item/交换机)

当作连接设备。它们看起来很像常规的以太网络交换机，是 SAN 中的连通点。SAN 使得在各自网络上实现相互通信成为可能，同时并带来了很多有利条件。

SAN英文全称：Storage Area Network，即

[存储区域网络](https://baike.baidu.com/item/存储区域网络)

。它是一种通过光纤

[集线器](https://baike.baidu.com/item/集线器)

、光纤

[路由器](https://baike.baidu.com/item/路由器)

、

[光纤交换机](https://baike.baidu.com/item/光纤交换机)

等连接设备将

[磁盘阵列](https://baike.baidu.com/item/磁盘阵列)

、磁带等存储设备与相关服务器连接起来的高速专用子网。

SAN由三个基本的组件构成：接口（如SCSI、

[光纤通道](https://baike.baidu.com/item/光纤通道)

、ESCON等）、连接设备（交换设备、

[网关](https://baike.baidu.com/item/网关)

、路由器、集线器等）和通信控制协议（如IP和SCSI等）。这三个组件再加上附加的存储设备和独立的SAN服务器，就构成一个SAN系统。SAN提供一个专用的、高可靠性的基于光通道的存储网络，SAN允许独立地增加它们的存储容量，也使得管理及集中控制（特别是对于全部存储设备都集群在一起的时候）更加简化。而且，光纤接口提供了10 km的连接长度，这使得物理上分离的远距离存储变得更容易.

网络存储通信中使用到的相关技术和协议包括 SCSI 、RAID 、iSCSI 以及光纤信道。一直以来 SCSI 支持高速、可靠的

[数据存储](https://baike.baidu.com/item/数据存储)

。RAID（[独立磁盘冗余阵列](https://baike.baidu.com/item/独立磁盘冗余阵列)）指的是一组标准，提供改进的性能和/或磁盘容错能力。光纤信道是一种提供存储设备相互连接的技术，支持高速通信（将来可以达到 10Gbps ）。与传统存储技术，如 SCSI 相比，光纤信道也支持较远距离的设备相互连接。iSCSI 技术支持通过 IP 网络实现存储设备间双向的数据传输。其实质是使 SCSI 连接中的数据连续化。通过 iSCSI，网络存储器可以应用于包含 IP 的任何位置。而作为 Internet 的主要元素，IP 几乎无所不在。

