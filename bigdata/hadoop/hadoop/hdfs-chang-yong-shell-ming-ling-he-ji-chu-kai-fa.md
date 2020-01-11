# HDFS常用Shell命令和基础开发



## [https://cshihong.github.io/2018/06/08/HDFS%E5%B8%B8%E7%94%A8Shell%E5%91%BD%E4%BB%A4%E5%92%8C%E5%9F%BA%E7%A1%80%E5%BC%80%E5%8F%91/](https://cshihong.github.io/2018/06/08/HDFS%E5%B8%B8%E7%94%A8Shell%E5%91%BD%E4%BB%A4%E5%92%8C%E5%9F%BA%E7%A1%80%E5%BC%80%E5%8F%91/) <a id="HDFS&#x5E38;&#x7528;Shell&#x547D;&#x4EE4;"></a>

## HDFS常用Shell命令 <a id="HDFS&#x5E38;&#x7528;Shell&#x547D;&#x4EE4;"></a>

Hadoop支持很多Shell命令，其中fs是HDFS最常用的命令，利用fs可以查看HDFS文件系统的目录结构、上传和下载数据、创建文件等。

HDFS有三种shell命令方式：

1. hadoop fs :适用于任何不同的文件系统，比如本地文件系统和HDFS文件系统。
2. Hadoop dfs:只能适用与HDFS文件系统。
3. hdfs dfs：跟hadoop dfs命令作用一样，也只能适用与HDfS文件系统。

> 我这里的的命令用的都是第三种，hdfs dfs。

### 对文件和文件夹的操作： <a id="&#x5BF9;&#x6587;&#x4EF6;&#x548C;&#x6587;&#x4EF6;&#x5939;&#x7684;&#x64CD;&#x4F5C;&#xFF1A;"></a>

复制

|  |  |
| :--- | :--- |


![shell](https://cshihong.github.io/2018/06/08/HDFS%E5%B8%B8%E7%94%A8Shell%E5%91%BD%E4%BB%A4%E5%92%8C%E5%9F%BA%E7%A1%80%E5%BC%80%E5%8F%91/HDFS%E5%B8%B8%E7%94%A8Shell%E5%91%BD%E4%BB%A4%E5%92%8C%E5%9F%BA%E7%A1%80%E7%BC%96%E7%A8%8B%E5%BC%80%E5%8F%91/shell.png)

图；操作示例

### HDFS dfsadmin管理命令： <a id="HDFS-dfsadmin&#x7BA1;&#x7406;&#x547D;&#x4EE4;&#xFF1A;"></a>

1. `hdfs dfsadmin -report`

   查看文件系统的基本信息和统计信息。

2. `hdfs dfsadmin -safemode get/enter`

   enter \| leave \| get \| wait：安全模式命令。安全模式是NameNode的一种状态，在这种状态下，NameNode不接受对名字空间的更改（只读）；不复制或删除块。NameNode在启动时自动进入安全模式，当配置块的最小百分数满足最小副本数的条件时，会自动离开安全模式。enter是进入，leave是离开。

3. `hdfs dfsadmin -refreshNodes`

   重新读取hosts和exclude文件，使新的节点或需要退出集群的节点能够被NameNode重新识别。这个命令在新增节点或注销节点时用到。

4. `hdfs dfsadmin -finalizeUpgrade`

   终结HDFS的升级操作。DataNode删除前一个版本的工作目录，之后NameNode也这样做。

5. `hdfs dfsadmin -fupgradeProgress`

   status\| details \| force：请求当前系统的升级状态 \| 升级状态的细节\| 强制升级操作

6. `hdfs dfsadmin -metasave filename`

   保存NameNode的主要数据结构到hadoop.log.dir属性指定的目录下的文件中。

![&#x7BA1;&#x7406;&#x547D;&#x4EE4;](https://cshihong.github.io/2018/06/08/HDFS%E5%B8%B8%E7%94%A8Shell%E5%91%BD%E4%BB%A4%E5%92%8C%E5%9F%BA%E7%A1%80%E5%BC%80%E5%8F%91/HDFS%E5%B8%B8%E7%94%A8Shell%E5%91%BD%E4%BB%A4%E5%92%8C%E5%9F%BA%E7%A1%80%E7%BC%96%E7%A8%8B%E5%BC%80%E5%8F%91/%E7%AE%A1%E7%90%86%E5%91%BD%E4%BB%A4.png)

图：操作示例

## HDFS API详解 <a id="HDFS-API&#x8BE6;&#x89E3;"></a>

Hadoop中关于文件操作类基本上全部是在”org.apache.hadoop.fs”包中，这些API能够支持的操作包含：打开文件，读写文件，删除文件等。

Hadoop类库中最终面向用户提供的接口类是FileSystem，该类是个抽象类，只能通过来类的get方法得到具体类。get方法存在几个重载版本，常用的是这个：复制

|  |  |
| :--- | :--- |


该类封装了几乎所有的文件操作，例如mkdir，delete等。综上基本上可以得出操作文件的程序库框架：复制

|  |  |
| :--- | :--- |


为了编写一个能够与HDFS交互的Java应用程序，一般需要向Java工程中添加以下JAR包：  
（1）”/usr/local/hadoop/share/hadoop/common”目录下的hadoop-common-2.7.1.jar和haoop-nfs-2.7.1.jar；  
（2）/usr/local/hadoop/share/hadoop/common/lib”目录下的所有JAR包；  
（3）“/usr/local/hadoop/share/hadoop/hdfs”目录下的haoop-hdfs-2.7.1.jar和haoop-hdfs-nfs-2.7.1.jar；  
（4）“/usr/local/hadoop/share/hadoop/hdfs/lib”目录下的所有JAR包。

### 上传本地文件： <a id="&#x4E0A;&#x4F20;&#x672C;&#x5730;&#x6587;&#x4EF6;&#xFF1A;"></a>

通过”`FileSystem.copyFromLocalFile（Path src，Patch dst）` “可将本地文件上传到HDFS的制定位置上，其中src和dst均为文件的完整路径。具体代码如下：复制

|  |  |
| :--- | :--- |


程序运行结果：复制

|  |  |
| :--- | :--- |


如果遇到因为文件权限不够，程序运行失败，解决方法如下：

> 可能出现问题的原因有三种：
>
> 1. hdfs 中的文件或文件夹 没有读取权限；
> 2. hdfs 的配置中未允许拷出文件；
> 3. linux 文件夹没有写入权限；
>
> 针对上述三个原因，解决方法如下：
>
> 1. 增加hdfs文件夹权限
>
>    `hdfs dfs -chmod 777 /`
>
> 2. 修改hdfs配置文件：复制
>
>    |  |  |
>    | :--- | :--- |

> 1. 增加Linux文件夹权限：
>
>    `sudo chmod 777 /`

### 创建HDFS文件： <a id="&#x521B;&#x5EFA;HDFS&#x6587;&#x4EF6;&#xFF1A;"></a>

通过”FileSystem.create（Path f）”可在HDFS上创建文件，其中f为文件的完整路径。具体代码如下：复制

|  |  |
| :--- | :--- |


### 写入文件和读取文件： <a id="&#x5199;&#x5165;&#x6587;&#x4EF6;&#x548C;&#x8BFB;&#x53D6;&#x6587;&#x4EF6;&#xFF1A;"></a>

复制

|  |  |
| :--- | :--- |


程序运行结果：复制

|  |  |
| :--- | :--- |


### 创建HDFS目录： <a id="&#x521B;&#x5EFA;HDFS&#x76EE;&#x5F55;&#xFF1A;"></a>

通过”FileSystem.mkdirs（Path f）”可在HDFS上创建文件夹，其中f为文件夹的完整路径。具体实现如下：复制

|  |  |
| :--- | :--- |


### 重命名HDFS文件： <a id="&#x91CD;&#x547D;&#x540D;HDFS&#x6587;&#x4EF6;&#xFF1A;"></a>

通过”FileSystem.rename（Path src，Path dst）”可为指定的HDFS文件重命名，其中src和dst均为文件的完整路径。具体实现如下：复制

|  |  |
| :--- | :--- |


### 删除HDFS上的文件： <a id="&#x5220;&#x9664;HDFS&#x4E0A;&#x7684;&#x6587;&#x4EF6;&#xFF1A;"></a>

通过”FileSystem.delete（Path f，Boolean recursive）”可删除指定的HDFS文件，其中f为需要删除文件的完整路径，recuresive用来确定是否进行递归删除。具体实现如下：复制

|  |  |
| :--- | :--- |


> 删除目录和删除文件代码一样，换成路径就行，如果目录下有文件，递归删除。

### 查看某个HDFS文件是否存在： <a id="&#x67E5;&#x770B;&#x67D0;&#x4E2A;HDFS&#x6587;&#x4EF6;&#x662F;&#x5426;&#x5B58;&#x5728;&#xFF1A;"></a>

通过”FileSystem.exists（Path f）”可查看指定HDFS文件是否存在，其中f为文件的完整路径。具体实现如下：复制

|  |  |
| :--- | :--- |


![&#x662F;&#x5426;&#x5B58;&#x5728;](https://cshihong.github.io/2018/06/08/HDFS%E5%B8%B8%E7%94%A8Shell%E5%91%BD%E4%BB%A4%E5%92%8C%E5%9F%BA%E7%A1%80%E5%BC%80%E5%8F%91/HDFS%E5%B8%B8%E7%94%A8Shell%E5%91%BD%E4%BB%A4%E5%92%8C%E5%9F%BA%E7%A1%80%E7%BC%96%E7%A8%8B%E5%BC%80%E5%8F%91/%E6%98%AF%E5%90%A6%E5%AD%98%E5%9C%A8.png)

图：HDFS文件系统结构图

程序运行结果：复制

|  |  |
| :--- | :--- |


### 查看HDFS文件的信息状态： <a id="&#x67E5;&#x770B;HDFS&#x6587;&#x4EF6;&#x7684;&#x4FE1;&#x606F;&#x72B6;&#x6001;&#xFF1A;"></a>

通过”FileSystem.getModificationTime\(\)”可查看指定HDFS文件的修改时间。具体实现如下：复制

|  |  |
| :--- | :--- |


程序运行结果：复制

|  |  |
| :--- | :--- |


### 读取HDFS某个目录下的所有文件： <a id="&#x8BFB;&#x53D6;HDFS&#x67D0;&#x4E2A;&#x76EE;&#x5F55;&#x4E0B;&#x7684;&#x6240;&#x6709;&#x6587;&#x4EF6;&#xFF1A;"></a>

通过”FileStatus.getPath（）”可查看指定HDFS中某个目录下所有文件。具体实现如下：复制

|  |  |
| :--- | :--- |


![&#x8BFB;&#x53D6;&#x6587;&#x4EF6;](https://cshihong.github.io/2018/06/08/HDFS%E5%B8%B8%E7%94%A8Shell%E5%91%BD%E4%BB%A4%E5%92%8C%E5%9F%BA%E7%A1%80%E5%BC%80%E5%8F%91/HDFS%E5%B8%B8%E7%94%A8Shell%E5%91%BD%E4%BB%A4%E5%92%8C%E5%9F%BA%E7%A1%80%E7%BC%96%E7%A8%8B%E5%BC%80%E5%8F%91/%E8%AF%BB%E5%8F%96%E6%96%87%E4%BB%B6.png)

图：input目录下有两文件

程序运行结果如下：复制

|  |  |
| :--- | :--- |


### 查找某个文件在HDFS集群的位置： <a id="&#x67E5;&#x627E;&#x67D0;&#x4E2A;&#x6587;&#x4EF6;&#x5728;HDFS&#x96C6;&#x7FA4;&#x7684;&#x4F4D;&#x7F6E;&#xFF1A;"></a>

通过”FileSystem.getFileBlockLocation（FileStatus file，long start，long len）”可查找指定文件在HDFS集群上的位置，其中file为文件的完整路径，start和len来标识查找文件的路径。具体实现如下：复制

|  |  |
| :--- | :--- |


程序运行结果：复制

|  |  |
| :--- | :--- |


### 获取HDFS集群上所有节点名称信息： <a id="&#x83B7;&#x53D6;HDFS&#x96C6;&#x7FA4;&#x4E0A;&#x6240;&#x6709;&#x8282;&#x70B9;&#x540D;&#x79F0;&#x4FE1;&#x606F;&#xFF1A;"></a>

通过”DatanodeInfo.getHostName（）”可获取HDFS集群上的所有节点名称。具体实现如下：复制

|  |  |
| :--- | :--- |


程序运行结果：复制

|  |  |
| :--- | :--- |


> 以

