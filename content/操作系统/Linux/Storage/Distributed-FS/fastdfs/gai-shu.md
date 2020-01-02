# 概述

**二、FastDFS分布式系统架构介绍**  
**FastDFS**：是一个开源的轻量级分布式文件系统，功能包括：文件存储、文件同步、文件访问（文件上传、文件下载）等，解决了大容量存储和负载均衡的问题。特别适合中小文件（建议范围：4KB &lt; file\_size &lt;500MB），对以文件为载体的在线服务，如相册网站、视频网站等。

FastDFS是为互联网应用量身定做的分布式文件系统，充分考虑了冗余备份、负载均衡、线性扩容等机制，并注重高可用、高性能等指标。和现有的类Google FS分布式文件系统相比，FastDFS的架构和设计理念有其独到之处，主要体现在轻量级、分组方式和对等结构三个方面。

**FastDFS架构图**  
FastDFS服务端有两个角色：跟踪器（tracker）和存储节点（storage）。跟踪器主要做调度工作，在访问上起负载均衡的作用。

![](https://github.com/aftree/A/tree/46ba3f3482b57318efea7ebcb4b9fbdafb25a241/fen-bu-shi-wen-jian-xi-tong/fastdfs/images/907596-20180403162629041-1710454764.png)

**FastDFS模块介绍**  
1）tracker server：跟踪服务器，用来调度来自客户端的请求，且在内存中记录所有存储组和存储服务器的信息状态。  
2）storage server：存储服务器，用来存储文件\(data\)和文件属性\(metadata\)。  
3）client：客户端，业务请求发起方，通过专用接口基于TCP协议与tracker server和storage server进行交互。  
4）group：组，也可称为卷，同组内上的文件是完全相同的。  
5）文件标识：包括两部分，组名（group）和文件名（含路径）  
6）文件相关属性：键值对\(Key Value Pair\)方式  
7）文件名：与原文件名并不相同。由storage server根据特定信息生成，并且可逆，文件名包含：源存储服务器的IP地址、文件创建时间戳、文件大小、随机数和文件扩展名等。

**FastDFS由跟踪服务器\(Tracker Server\)、存储服务器\(Storage Server\)和客户端\(Client\)构成，其中：**

**跟踪服务器Tracker Server**  
主要做调度工作，起到均衡的作用；负责管理所有的 storage server和 group，每个 storage 在启动后会连接 Tracker，告知自己所属 group 等信息，并保持周期性心跳。tracker根据storage的心跳信息，建立group==&gt;\[storage serverlist\]的映射表。  
Tracker需要管理的元信息很少，会全部存储在内存中；另外tracker上的元信息都是由storage汇报的信息生成的，本身不需要持久化任何数据，这样使得tracker非常容易扩展，直接增加tracker机器即可扩展为tracker cluster来服务，cluster里每个tracker之间是完全对等的，所有的tracker都接受stroage的心跳信息，生成元数据信息来提供读写服务。

**存储服务器Storage Server**  
主要提供容量和备份服务；以 group 为单位，每个 group 内可以有多台 storage server，数据互为备份。以group为单位组织存储能方便的进行应用隔离、负载均衡、副本数定制（group内storage server数量即为该group的副本数），比如将不同应用数据存到不同的group就能隔离应用数据，同时还可根据应用的访问特性来将应用分配到不同的group来做负载均衡；缺点是group的容量受单机存储容量的限制，同时当group内有机器坏掉时，数据恢复只能依赖group内地其他机器，使得恢复时间会很长。  
group内每个storage的存储依赖于本地文件系统，storage可配置多个数据存储目录，比如有10块磁盘，分别挂载在/data/disk1-/data/disk10，则可将这10个目录都配置为storage的数据存储目录。storage接受到写文件请求时，会根据配置好的规则选择其中一个存储目录来存储文件。为了避免单个目录下的文件数太多，在storage第一次启动时，会在每个数据存储目录里创建2级子目录，每级256个，总共65536个文件，新写的文件会以hash的方式被路由到其中某个子目录下，然后将文件数据作为本地文件存储到该目录中。

**客户端Client**  
主要是上传下载数据的服务器，也就是我们自己的项目所部署在的服务器。每个客户端服务器都需要安装Nginx  
基本架构如下图所示。

![](https://github.com/aftree/A/tree/46ba3f3482b57318efea7ebcb4b9fbdafb25a241/fen-bu-shi-wen-jian-xi-tong/fastdfs/images/907596-20180226101909101-1265852945.png)

**FastDFS的存储策略**  
为了支持大容量，存储节点（服务器）采用了分卷（或分组）的组织方式。存储系统由一个或多个卷组成，卷与卷之间的文件是相互独立的，所有卷的文件容量累加就是整个存储系统中的文件容量。一个卷可以由一台或多台存储服务器组成，一个卷下的存储服务器中的文件都是相同的，卷中的多台存储服务器起到了冗余备份和负载均衡的作用。  
在卷中增加服务器时，同步已有的文件由系统自动完成，同步完成后，系统自动将新增服务器切换到线上提供服务。当存储空间不足或即将耗尽时，可以动态添加卷。只需要增加一台或多台服务器，并将它们配置为一个新的卷，这样就扩大了存储系统的容量。

**FastDFS的上传过程**  
FastDFS向使用者提供基本文件访问接口，比如upload、download、append、delete等，以客户端库的方式提供给用户使用。

![](https://github.com/aftree/A/tree/46ba3f3482b57318efea7ebcb4b9fbdafb25a241/fen-bu-shi-wen-jian-xi-tong/fastdfs/images/907596-20180226102020258-184546115.png)

Storage Server会定期的向Tracker Server发送自己的存储信息。当Tracker Server Cluster中的Tracker Server不止一个时，各个Tracker之间的关系是对等的，所以客户端上传时可以选择任意一个Tracker。

当Tracker收到客户端上传文件的请求时，会为该文件分配一个可以存储文件的group，当选定了group后就要决定给客户端分配group中的哪一个storage server。当分配好storage server后，客户端向storage发送写文件请求，storage将会为文件分配一个数据存储目录。然后为文件分配一个fileid，最后根据以上的信息生成文件名存储文件。文件名的格式如下：

![](https://github.com/aftree/A/tree/46ba3f3482b57318efea7ebcb4b9fbdafb25a241/fen-bu-shi-wen-jian-xi-tong/fastdfs/images/907596-20180226102102554-394696218.png)

**FastDFS的文件同步**  
写文件时，客户端将文件写至group内一个storage server即认为写文件成功，storage server写完文件后，会由后台线程将文件同步至同group内其他的storage server。  
每个storage写文件后，同时会写一份binlog，binlog里不包含文件数据，只包含文件名等元信息，这份binlog用于后台同步，storage会记录向group内其他storage同步的进度，以便重启后能接上次的进度继续同步；进度以时间戳的方式进行记录，所以最好能保证集群内所有server的时钟保持同步。  
storage的同步进度会作为元数据的一部分汇报到tracker上，tracke在选择读storage的时候会以同步进度作为参考。

客户端上传文件后存储与服务器将文件ID返回给客户端，此文件ID用于以后访问该文件的索引信息。文件索引信息包括：组名，虚拟磁盘路径，数据两级目录，文件名。  
路径格式：

![](https://github.com/aftree/A/tree/46ba3f3482b57318efea7ebcb4b9fbdafb25a241/fen-bu-shi-wen-jian-xi-tong/fastdfs/images/907596-20180226105834768-331548978.png)

**FastDFS的文件下载**

客户端uploadfile成功后，会拿到一个storage生成的文件名，接下来客户端根据这个文件名即可访问到该文件。

![](https://github.com/aftree/A/tree/46ba3f3482b57318efea7ebcb4b9fbdafb25a241/fen-bu-shi-wen-jian-xi-tong/fastdfs/images/907596-20180226102020258-184546115.png)

跟upload file一样，在downloadfile时客户端可以选择任意tracker server。tracker发送download请求给某个tracker，必须带上文件名信息，tracke从文件名中解析出文件的group、大小、创建时间等信息，然后为该请求选择一个storage用来服务读请求。

![](https://github.com/aftree/A/tree/46ba3f3482b57318efea7ebcb4b9fbdafb25a241/fen-bu-shi-wen-jian-xi-tong/fastdfs/images/907596-20180226102551863-381660983.png)

