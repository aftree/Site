# 调度

常见的分布式文件系统有，GFS、HDFS、Lustre 、Ceph 、GridFS 、mogileFS、TFS、FastDFS等。各自适用于不同的领域。它们都不是系统级的分布式文件系统，而是应用级的分布式文件存储服务。

Google学术论文，这是众多分布式文件系统的起源

Google File System（大规模分散文件系统）  
MapReduce （大规模分散FrameWork）  
BigTable（大规模分散数据库）  
Chubby（分散锁服务）

搜索Google三大论文中文版\(Bigtable、 GFS、 Google MapReduce\)。

原版地址链接：  
[http://labs.google.com/papers/gfs.html](http://labs.google.com/papers/gfs.html)  
[http://labs.google.com/papers/bigtable.html](http://labs.google.com/papers/bigtable.html)  
[http://labs.google.com/papers/mapreduce.html](http://labs.google.com/papers/mapreduce.html)

### GFS（Google File System）

Google公司为了满足本公司需求而开发的基于Linux的专有分布式文件系统。。尽管Google公布了该系统的一些技术细节，但Google并没有将该系统的软件部分作为开源软件发布。  
下面分布式文件系统都是类 GFS的产品。

### HDFS

Hadoop 实现了一个分布式文件系统（Hadoop Distributed File System），简称HDFS。 Hadoop是Apache Lucene创始人Doug Cutting开发的使用广泛的文本搜索库。它起源于Apache Nutch，后者是一个开源的网络搜索引擎，本身也是Luene项目的一部分。Aapche Hadoop架构是MapReduce算法的一种开源应用，是Google开创其帝国的重要基石。

### Ceph

是加州大学圣克鲁兹分校的Sage weil攻读博士时开发的分布式文件系统。并使用Ceph完成了他的论文。  
说 ceph 性能最高，C++编写的代码，支持Fuse，并且没有单点故障依赖， 于是下载安装， 由于 ceph 使用 btrfs 文件系统， 而btrfs 文件系统需要 Linux 2.6.34 以上的内核才支持。  
可是ceph太不成熟了，它基于的btrfs本身就不成熟，它的官方网站上也明确指出不要把ceph用在生产环境中。

### Lustre

Lustre是一个大规模的、安全可靠的，具备高可用性的集群文件系统，它是由SUN公司开发和维护的。  
该项目主要的目的就是开发下一代的集群文件系统，可以支持超过10000个节点，数以PB的数据量存储系统。  
目前Lustre已经运用在一些领域，例如HP SFS产品等。

## 适合存储小文件、图片的分布文件系统研究

FastDFS分布文件系统 （我写的）

TFS（Taobao File System）安装方法 （我写的）

用于图片等小文件大规模存储的分布式文件系统调研  
架构高性能海量图片服务器的技术要素  
nginx性能改进一例（图片全部存入google的leveldb）  
动态生成图片 Nginx + GraphicsMagick

### MogileFS

由memcahed的开发公司danga一款perl开发的产品，目前国内使用mogielFS的有图片托管网站yupoo等。  
MogileFS是一套高效的文件自动备份组件，由Six Apart开发，广泛应用在包括LiveJournal等web2.0站点上。  
MogileFS由3个部分组成：  
第1个部分是server端，包括mogilefsd和mogstored两个程序。前者即是 mogilefsd的tracker，它将一些全局信息保存在数据库里，例如站点domain,class,host等。后者即是存储节点\(store node\)，它其实是个HTTP Daemon，默认侦听在7500端口，接受客户端的文件备份请求。在安装完后，要运行mogadm工具将所有的store node注册到mogilefsd的数据库里，mogilefsd会对这些节点进行管理和监控。  
第2个部分是utils（工具集），主要是MogileFS的一些管理工具，例如mogadm等。  
第3个部分是客户端API，目前只有Perl API\(MogileFS.pm\)、PHP，用这个模块可以编写客户端程序，实现文件的备份管理功能。

### mooseFS

持FUSE，相对比较轻量级，对master服务器有单点依赖，用perl编写，性能相对较差，国内用的人比较多

### FastDFS

是一款类似Google FS的开源分布式文件系统，是纯C语言开发的。  
FastDFS是一个开源的轻量级分布式文件系统，它对文件进行管理，功能包括：文件存储、文件同步、文件访问（文件上传、文件下载）等，解决了大容量存储和负载均衡的问题。特别适合以文件为载体的在线服务，如相册网站、视频网站等等。  
官方论坛 [http://bbs.chinaunix.net/forum-240-1.html](http://bbs.chinaunix.net/forum-240-1.html)  
FastDfs google Code [http://code.google.com/p/fastdfs/](http://code.google.com/p/fastdfs/)  
分布式文件系统FastDFS架构剖析 [http://www.programmer.com.cn/4380/](http://www.programmer.com.cn/4380/)

### TFS

TFS（Taobao !FileSystem）是一个高可扩展、高可用、高性能、面向互联网服务的分布式文件系统，主要针对海量的非结构化数据，它构筑在普通的Linux机器 集群上，可为外部提供高可靠和高并发的存储访问。TFS为淘宝提供海量小文件存储，通常文件大小不超过1M，满足了淘宝对小文件存储的需求，被广泛地应用 在淘宝各项应用中。它采用了HA架构和平滑扩容，保证了整个文件系统的可用性和扩展性。同时扁平化的数据组织结构，可将文件名映射到文件的物理地址，简化 了文件的访问流程，一定程度上为TFS提供了良好的读写性能。  
官网 ： [http://code.taobao.org/p/tfs/wiki/index/](http://code.taobao.org/p/tfs/wiki/index/)

### GridFS文件系统

MongoDB是一种知名的NoSql数据库，GridFS是MongoDB的一个内置功能，它提供一组文件操作的API以利用MongoDB存储文件，GridFS的基本原理是将文件保存在两个Collection中，一个保存文件索引，一个保存文件内容，文件内容按一定大小分成若干块，每一块存在一个Document中，这种方法不仅提供了文件存储，还提供了对文件相关的一些附加属性（比如MD5值，文件名等等）的存储。文件在GridFS中会按4MB为单位进行分块存储。

MongoDB GridFS 数据读取效率 benchmark  
[http://blog.nosqlfan.com/html/730.html](http://blog.nosqlfan.com/html/730.html)

nginx + gridfs 实现图片的分布式存储 安装（一年后出问题了）  
[http://www.cnblogs.com/zhangmiao-chp/archive/2011/05/05/2038285.html](http://www.cnblogs.com/zhangmiao-chp/archive/2011/05/05/2038285.html)

基于MongoDB GridFS的图片存储  
[http://liut.cc/blog/2010/12/about-imsto\_my-first-open-source-project.html](http://liut.cc/blog/2010/12/about-imsto_my-first-open-source-project.html)

nginx+mongodb-gridfs+squid  
[http://1008305.blog.51cto.com/998305/885340](http://1008305.blog.51cto.com/998305/885340)

