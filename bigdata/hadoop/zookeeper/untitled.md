# Untitled

```bash
[root@hadoop ~]# tree -L 2 apache-zookeeper-3.5.6-bin
apache-zookeeper-3.5.6-bin
├── bin
│   ├── README.txt
│   ├── zkCleanup.sh
│   ├── zkCli.cmd
│   ├── zkCli.sh
│   ├── zkEnv.cmd
│   ├── zkEnv.sh
│   ├── zkServer.cmd
│   ├── zkServer-initialize.sh
│   ├── zkServer.sh
│   ├── zkTxnLogToolkit.cmd
│   └── zkTxnLogToolkit.sh
├── conf
│   ├── configuration.xsl
│   ├── log4j.properties
│   └── zoo_sample.cfg
```

创建成功,进入到zookeeper的conf目录

cd zookeeper/conf/ 有三个文件,zookeeper的配置文件叫做zoo.cfg,这里边没有,我们把 zoo\_sample.cfg 拷贝一份出来

## zookeeper部署方式

* 单机模式
* 伪分布式集群
* 完全分布式集群

原始配置

{% tabs %}
{% tab title="Bash" %}
```bash
vim zoo.cfg   
# The number of milliseconds of each tick
tickTime=2000
# The number of ticks that the initial 
# synchronization phase can take
initLimit=10
# The number of ticks that can pass between 
# sending a request and getting an acknowledgement
syncLimit=5
# the directory where the snapshot is stored.
# do not use /tmp for storage, /tmp here is just 
# example sakes.
dataDir=/tmp/zookeeper
# the port at which the clients will connect
clientPort=2181
# the maximum number of client connections.
# increase this if you need to handle more clients
#maxClientCnxns=60
#
# Be sure to read the maintenance section of the 
# administrator guide before turning on autopurge.
#
# http://zookeeper.apache.org/doc/current/zookeeperAdmin.html#sc_maintenance
#
# The number of snapshots to retain in dataDir
#autopurge.snapRetainCount=3
# Purge task interval in hours
# Set to "0" to disable auto purge feature
#autopurge.purgeInterval=1
​
```
{% endtab %}
{% endtabs %}

### 单机

JAVA JDK 安装

下载，上传，解压zookeeper,修改配置文件

```text
我们需要更改的地方有以下几点
​
dataDir是zookeeper的数据目录,可以任意选择,但是这个目录必须要提前创建好,zookeeper是不会帮你创建的
​
dataDir=/export/servers/data/zookeeper
​
dataLogdir是zookeeper的日志目录,应该要谨慎的选择日志目录,如果将日志存放在比较繁忙的设备上,将大大影响系统性能
dataLogDir=/export/servers/logs/zookeeper
​
限制zookeeper的客户端链接数量,设置为0或者不设置表示取消对并发连接的设置
​
maxClientCnxns=0
​
最小会话超时时间和最大会话超时时间
minSessionTimeout=4000
maxSessionTimeout=10000
​
server.id 其中1表示这是第几号server,用来做集群中的区别的192.168.64.131是该server所在的ip地址
​
2888是该server和集群中的leader通信端口,3888是选举leader的端口
​
server.1=192.168.64.131:2888:3888
​
在之前设置的dataDir中新建myid文件,写入一个数字,该数字表示这是第几号server,该数字必须和zoo.cfg文件中的server.X中的X一 一对应
```

进入到zookeeper的安装目录 执行bin/zkServer.sh start

如图所示,使用jps命令查看进程是否启动成功

出现 QuorumPeerMain这个进程的时候就说明zookeeper已经安装成功了

接下里进入zookeeper的客户端查看

进入zookeeper的安装目录执行bin/zkCli.sh

执行ls / 查看当前根目录下的节点 发现有一个zookeeper的节点

至此zookeeper的单机版安装成功!

### 伪

所谓伪集群就是在单机模拟集群模式zookeeper的运行。

一个zookeeper,3份配置文件，配置各自的datadir，启动、查询时，指定配置文件

### 配置文件

下面是我配置的伪集群分布模式，分别通过zoo1.cfg、zoo2.cfg、zoo3.cfg来模拟三台机器的zookeeper集群:

zoo1.cfg内容如下:

```text
# The number of milliseconds of each tick
tickTime=2000
​
# The number of ticks that the initial
# synchronization phase can take
initLimit=10
​
# The number of ticks that can pass between
# sending a request and getting an acknowledgement
syncLimit=5
​
# the directory where the snapshot is stored.
# do not use /tmp for storage, /tmp here is just
# example sakes.
dataDir=/usr/local/zookeeper-3.4.9/data/data_1
dataLogDir=/usr/local/zookeeper-3.4.9/dataLog/dataLog_1
​
# the port at which the clients will connect
clientPort=2181
​
server.0=localhost:2287:3387
server.1=localhost:2288:3388
server.2=localhost:2289:3389
```

配置完成上述三个文件后，还需在dataDir对应路径下添加myid文件，内容对应server.n的n值:

```text
echo 1 > data_1/myid
echo 2 > data_2/myid
echo 0 > data_3/myid
```

### 启动

在集群为分布式下，我们只有一台机器，但是要运行三个Zookeeper实例。此时，如果在使用单机模式的启动命令是行不通的。此时，只要通过下面三条命令就能运行前面所配置的Zookeeper服务。如下所示：

```text
./zkServer.sh start zoo1.cfg
./zkServer.sh start zoo2.cfg
./zkServer.sh start zoo3.cfg
```

在运行完第一条指令之后，会出现一些错误异常，产生异常信息的原因是由于Zookeeper 服务的每个实例都拥有全局配置信息，他们在启动的时候会随时随地的进行Leader选举操作。此时，第一个启动的Zookeeper需要和另外两个 Zookeeper实例进行通信。但是，另外两个Zookeeper实例还没有启动起来，因此就产生了这的异样信息。我们直接将其忽略即可，待把图中“2 号”和“3号”Zookeeper实例启动起来之后，相应的异常信息自然会消失。此时，可以通过下面三条命令，来查询：

```text
./zkServer.sh status zoo1.cfg
./zkServer.sh status zoo2.cfg
./zkServer.sh status zoo3.cfg
```

上边我们已经搭建出来一台zookeeper,集群其实就是多划分了几台机器,将zookeeper从单机变成一个集群版

zookeeper安装包

虚拟机3台,

配置动态IP或者静态IP

保证三台机器之间的通信是畅通的,可以在/etc/hosts编辑

例如 我的三台机器的ip地址是192.168.64.137,192.168.64.138,192.168.64.139

在hosts文件中配置的就是

192.168.64.137 zk01

192.168.64.138 zk02

192.168.64.139 zk03

使用ping 命令分别测试机器是否可以连通

OK 可以ping通

修改zookeeper的配置文件在原来的基础上更改一点就可以的

server.id 的编号是根据机器的顺序来写的,当然也可以随便写,为了规范我们用顺序的数字来表示机器

更改后保存退出,

分发到其他机器 使用scp命令 我第二台机器的域名是zk02 存放zookeeper的位置是/export/servers/

 scp -r zookeeper zk02:/export/servers/

scp -r zookeeper zk03:/export/servers/

分别在zk02 和zk03上的zookeeper配置文件中指定的数据目录中新建文件myid,每台机器的myid文件的内容要和在第一台机器中的zoo.cfg 配置的一样

更改完毕后准备启动集群

先配置环境变量

分别在每台机器上之执行命令zkServer.sh start,使用jps分别在每台机器上查看进程

再执行zkServer.sh status

zk01

一台领导者,两台跟随者

集群安装完成!

