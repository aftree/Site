# 诗

一、各种分布式文件系统对比

1.1 表格对比

| 技术 | 优点 | 缺点 | 总结 |
| :--- | :--- | :--- | :--- |
| HDFS | 1.大数据批量读写，吞吐量高;2.一次写入，多次读取，顺序读写； | 1、交互式应用，低延迟很难满足；  2、不支持多用户并发写相同文件。 | 如果是很多小文件，nameNode压力大 |
| googleFs | 1、成本低，运行在廉价的普通硬件上 | 、不开源 | 不开源，使用困难 |
| Tfs | 1、    开源 | 1、小于1M的文件  2、TFS内部是没有任何数据的内存缓冲的 | 适合单个文件比较小的系统 |
| Lustre | 1、  开源  2、  支持POSIX  3、  文件被分割成若干的Chunk，每个chunk是一般为1MB－4MB |  |  |
| Ceph | 1、支持POSIX  2、开源 |  | 1、  在Linux主流内核中找到ceph  2、不成熟，处于测试推广阶段 |
| MogileFs | 1、开源 |  | 比FastDFS 差 |
| FastDFS | 1、  开源  2、  适合以文件为载体的在线服务  3、  FastDFS没有对文件做分块存储  4、  不需要二次开发即可直接使用  5、  比mogileFS更易维护和使用  6、  直接使用socket通信方式，相对于MogileFS的HTTP方式，效率更高。 | 1、文件访问方式使用专有API，不支持POSIX |  |
| swiftfs |  |  | 1、基于HDFS |
| NFS | 1、用户和程序可以象访问本地文件一样访问远端系统上的文件 |  |  |

开源的分布式文件/对象系统比较有名的包括Lustre（HPC）GlusterFS（NAS NFS）、HDFS（hadoop）、ceph（虚机块存储）、swift（restful对象存储），各有不同的领域。

1.2 相关链接

1.2.1 Hadoop

[http://tech.it168.com/a2012/1029/1414/000001414776.shtml](http://tech.it168.com/a2012/1029/1414/000001414776.shtml)

hadoop文档 ：[http://hadoop.apache.org/](http://hadoop.apache.org/)

[https://hadoop.apache.org/docs/r1.0.4/cn/hdfs\_shell.html](https://hadoop.apache.org/docs/r1.0.4/cn/hdfs_shell.html)

1.2.2 Googlefs 不开源

1.2.3 Tfs

开源首页：[http://tfs.taobao.org/](http://tfs.taobao.org/)

1.2.4 Lustre

[http://lustre.org/](http://lustre.org/)

[http://lustre.org/getting-started-with-lustre/](http://lustre.org/getting-started-with-lustre/)

1.2.5 ceph：

社区网站地址：  
[http://ceph.org.cn/](http://ceph.org.cn/)  
中文文档：  
[http://docs.ceph.org.cn/start/intro/](http://docs.ceph.org.cn/start/intro/)

源码：[https://github.com/ceph/ceph](https://github.com/ceph/ceph)

[https://www.oschina.net/translate/ceph](https://www.oschina.net/translate/ceph)

[http://dockone.io/article/307](http://dockone.io/article/307)

1.2.6 MogileFs

1.2.7 fastdfs

[http://elf8848.iteye.com/blog/1739596](http://elf8848.iteye.com/blog/1739596)

MogileFs 和FastDFS 对比：[http://blog.csdn.net/wishfly/article/details/6940504](http://blog.csdn.net/wishfly/article/details/6940504)

[http://www.sunnyu.com/?p=105](http://www.sunnyu.com/?p=105)

[http://bbs.chinaunix.net/thread-2001101-1-1.html](http://bbs.chinaunix.net/thread-2001101-1-1.html)

1.2.8 swiftfs

[https://github.com/ovh/svfs](https://github.com/ovh/svfs)

[https://wiki.openstack.org/wiki/Swift](https://wiki.openstack.org/wiki/Swift)

[https://support.rackspace.com/how-to/swift-filesystem-for-hadoop/](https://support.rackspace.com/how-to/swift-filesystem-for-hadoop/)

1.2.9 综合

现在主流的开源分布式文件系统架构有哪些？

[https://www.zhihu.com/question/19832447](https://www.zhihu.com/question/19832447)

如何选择分布式文件系统？

[https://segmentfault.com/q/1010000000449947](https://segmentfault.com/q/1010000000449947)

各种分布式文件系统

[http://os.51cto.com/art/201007/212689\_all.htm](http://os.51cto.com/art/201007/212689_all.htm)

索引树：[http://bbs.chinaunix.net/tree/index\_304\_1/](http://bbs.chinaunix.net/tree/index_304_1/)

当下流行的分布式文件系统大阅兵

[http://os.51cto.com/art/201007/212689\_all.htm](http://os.51cto.com/art/201007/212689_all.htm)

普通存储方案：Rsync、DAS\(IDE/SATA/SAS/SCSI等块\)、NAS\(NFS、CIFS、SAMBA等文件系统\)、SAN\(FibreChannel, iSCSI, FoE存储网络块\)，Openfiler、FreeNas\(ZFS快照复制\)由于生产环境中往往由于对存储数据量很大，而SAN存储价格又比较昂贵，因此大多会选择分布式  
存储来解决一下问题：  
海量数据存储问题  
数据高可用问题\(冗余备份\)问题  
较高的读写性能和负载均衡问题  
支持多平台多语言问题  
高并发问题

| 指标 | 适合类型 | 文件分布 | 系统性能 | 复杂度 | FUSE | POSIX | 备份机制 | 通讯协议接口 | 社区支持 | 去重 | 开发语言 |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| FastDFS | 4KB~500MB | 小文件合并存储不分片处理 | 很高 | 简单 | 不支持 | 不支持 | 组内冗余备份 | Api  HTTP | 国内用户群 |  | C语言 |
| TFS | 所有文件 | 小文件合并，以block组织分片 |  | 复杂 | 不支持 | 不支持 | Block存储多份,主辅灾备 | API  http | 少 |  | C++ |
| MFS | 大于64K | 分片存储 | Master占内存多 |  | 支持 | 支持 | 多点备份动态冗余 | 使用fuse挂在 | 较多 |  | Perl |
| HDFS | 大文件 | 大文件分片分块存储 |  | 简单 | 支持 | 支持 | 多副本 | 原生api | 较多 |  | java |
| Ceph | 对象文件块 | OSD一主多从 |  | 复杂 | 支持 | 支持 | 多副本 | 原生api | 较少 |  | C++ |
| MogileFS | 海量小图片 |  | 高 | 复杂 | 可以支持 | 不支持 | 动态冗余 | 原生api | 文档少 |  | Perl |
| ClusterFS | 大文件 |  |  | 简单 | 支持 | 支持 | 镜像 |  | 多 |  | C |

