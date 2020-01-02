---
title: "管理ZooKeeper"
date: 2019-12-15T20:50:40+08:00
draft: true
#pre: "<b>1. </b>"
weight: 10
---

> 部署完ZooKeeper，开始可以使用API操作zk，运维可以使用命令行或者可视化客户端。

连接zk操作

```bash
bin/zkCli.sh -server 192.168.229.160:2181,192.168.229.161:2181,192.168.229.162:2181
```

成功连接后，可以看到如下输出：

```bash

```

客户端连接zk集群的输出日志

从日志输出可以看到，客户端连接的是192.168.229.162:2181进程（连接上哪台机器的zk进程是随机的），客户端已成功连接上zk集群。

连接成功后，便可以使用命令与zk服务进行交互。



help：输出zk支持的所有命令

```shell
[zk: 127.0.0.1:2182(CONNECTED) 0] help
ZooKeeper -server host:port cmd args
    stat path [watch]
    set path data [version]
    ls path [watch]
    delquota [-n|-b] path
    ls2 path [watch]
    setAcl path acl
    setquota -n|-b val path
    history 
    redo cmdno
    printwatches on|off
    delete path [version]
    sync path
    listquota path
    rmr path
    get path [watch]
    create [-s] [-e] path data acl
    addauth scheme auth
    quit 
    getAcl path
    close 
    connect host:port

```

ls：查看指定路径下包含的节点

```bash
[zk: localhost:2181(CONNECTED) 2] ls /
[zookeeper]

```

create：创建一个节点

```bash
[zk: localhost:2181(CONNECTED) 3] create /zk mydata
Created /zk
```

以上命令创建一个/zk节点，且其内容为 “myData”

get：显示指定路径下节点的信息，例如，我们检查一下上面的/zk节点最否创建成功

```bash
[zk: localhost:2181(CONNECTED) 4] get /zk
mydata
cZxid = 0xb59
ctime = Thu Jun 30 11:13:24 CST 2016
mZxid = 0xb59
mtime = Thu Jun 30 11:13:24 CST 2016
pZxid = 0xb59
cversion = 0
dataVersion = 0
aclVersion = 0
ephemeralOwner = 0x0
dataLength = 6
numChildren = 0

```

可以看到/zk节点的内容为”myData”，且输出包含了znode的其他信息。有关各个字段的具体含义，请参见了本博客对znode的介绍。

set：设置节点的内容

```bash
[zk: localhost:2181(CONNECTED) 6] set /zk "anotherData"
……
[zk: localhost:2181(CONNECTED) 7] get /zk
"anotherData"
……

```

delete：删除一个节点

```bash
[zk: localhost:2181(CONNECTED) 8] delete /zk
[zk: localhost:2181(CONNECTED) 9] get /zk
Node does not exist: /zk

```

以上就是zk客户端最常用的几个命令，从这几个命令我们也可以看到zk提供的API设计的简单。

## 四字母命令

ZooKeeper提供了多个由4个字母构成的命令，可以使用nc或者telnet来使用这些命令。例如：

```
telnet 127.0.0.1 2181
```

成功连接zk后，输入conf
会看到以下输出

```bash
clientPort=2181
dataDir=D:\Soft\zookeeper-3.4.6\data\version-2
dataLogDir=D:\Soft\zookeeper-3.4.6\data\version-2
tickTime=2000
maxClientCnxns=60
minSessionTimeout=4000
maxSessionTimeout=40000
serverId=0
```

或者使用nc来向zk发送4字母命令，例如：

```bash
echo conf | nc 192.168.229.161 2181
```

 其他常用的四字母命令如下表格所示：

表格：ZooKeeper提供的四字母命令

| 命令 | 描述                                                       |
| :--: | ---------------------------------------------------------- |
| conf | zk服务配置的详细信息                                       |
| stat | 客户端与zk连接的简要信息                                   |
| srvr | zk服务的详细信息                                           |
| cons | 客户端与zk连接的详细信息                                   |
| mntr | zk服务目前的性能状况                                       |
| wchs | watch的简要信息                                            |
| wchc | watch的详细信息，客户端 -> watch的映射，线上环境要小心使用 |
| wchp | watch的详细信息, znode -> 客户端的映射，线上环境要小心使用 |

例如，mntr 命令的输出：

```bash
echo mntr | nc 192.168.229.161 2181
zk_version  3.4.6-1569965, built on 02/20/2014 09:09 GMT
zk_avg_latency  0
zk_max_latency  565
zk_min_latency  0
zk_packets_received 95353
zk_packets_sent 95713
zk_num_alive_connections    3
zk_outstanding_requests 0
zk_server_state leader
zk_znode_count  20
zk_watch_count  12
zk_ephemerals_count 9
zk_approximate_data_size    1465
zk_open_file_descriptor_count   37
zk_max_file_descriptor_count    65535
zk_followers    2       - 只有leader进程才有此项输出
zk_synced_followers 2   - 只有leader进程才有此项输出
zk_pending_syncs    0   - 只有leader进程才有此项输出
```

