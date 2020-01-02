# 部署

**1）服务器信息（centos6.9）**

```text
描述          ip地址              主机名称                分组
跟踪服务器1    192.168.10.200     Fastdfs_tracker_t1    
跟踪服务器2    192.168.10.201     Fastdfs_tracker_t2    
存储服务器1    192.168.10.202     Fastdfs_storage_s1     group1
存储服务器2    192.168.10.203     Fastdfs_storage_s2     group2
存储服务器3    192.168.10.204     Fastdfs_storage_s3     group3
客户机1        192.168.10.205     Fastdfs_client
```

**2）FastDFS安装（所有tracker服务器、storage服务器、客户端机器都要有如下操作。这里以Fastdfs\_tracker\_t1服务器操作为例**

```text
1）编译和安装所需的依赖包
[root@Fastdfs_tracker_t1 ~]# yum install make cmake gcc gcc-c++

2）安装libfastcommon
[root@Fastdfs_tracker_t1 ~]# cd /usr/local/src/
[root@Fastdfs_tracker_t1 src]# wget https://codeload.github.com/happyfish100/libfastcommon/tar.gz/V1.0.7 -O libfastcommon-1.0.7.tar.gz
[root@Fastdfs_tracker_t1 src]# tar zxf libfastcommon-1.0.7.tar.gz
[root@Fastdfs_tracker_t1 src]# cd libfastcommon-1.0.7
[root@Fastdfs_tracker_t1 libfastcommon-1.0.7]# ./make.sh
[root@Fastdfs_tracker_t1 libfastcommon-1.0.7]# ./make.sh install

libfastcommon 默认安装到了/usr/lib64/libfastcommon.so和/usr/lib64/libfdfsclient.so

因为FastDFS主程序设置的lib目录是/usr/local/lib，所以需要创建软链接
[root@Fastdfs_tracker_t1 libfastcommon-1.0.7]# ln -s /usr/lib64/libfastcommon.so /usr/local/lib/libfastcommon.so
[root@Fastdfs_tracker_t1 libfastcommon-1.0.7]# ln -s /usr/lib64/libfastcommon.so /usr/lib/libfastcommon.so
[root@Fastdfs_tracker_t1 libfastcommon-1.0.7]# ln -s /usr/lib64/libfdfsclient.so /usr/local/lib/libfdfsclient.so
[root@Fastdfs_tracker_t1 libfastcommon-1.0.7]# ln -s /usr/lib64/libfdfsclient.so /usr/lib/libfdfsclient.so

3）安装FastDFS
先提前安装依赖
[root@Fastdfs_tracker_t1 ~]# yum install gettext gettext-devel libXft libXft-devel libXpm libXpm-devel autoconf libxtst-devel zlib-devel libpng-devel  glib-devel
[root@Fastdfs_tracker_t1 ~]# cd /usr/local/src/
[root@Fastdfs_tracker_t1 src]# wget https://github.s3.amazonaws.com/downloads/libevent/libevent/libevent-2.0.20-stable.tar.gz
[root@Fastdfs_tracker_t1 src]# tar -zvxf libevent-2.0.20-stable.tar.gz
[root@Fastdfs_tracker_t1 src]# cd libevent-2.0.20-stable
[root@Fastdfs_tracker_t1 libevent-2.0.20-stable]# ./configure && make && make install

接着编译安装FastDFS（编译前要确保已经成功安装了 libfastcommon）。百度云盘下载：https://pan.baidu.com/s/1x_CTcRqZr_BTptXF9kHOng   提取密码：9ku2
[root@Fastdfs_tracker_t1 src]# pwd
/usr/local/src
[root@Fastdfs_tracker_t1 src]# ls FastDFS_v5.05.tar.gz
FastDFS_v5.05.tar.gz
[root@Fastdfs_tracker_t1 src]# tar -zvxf FastDFS_v5.05.tar.gz
[root@Fastdfs_tracker_t1 src]# cd FastDFS
[root@Fastdfs_tracker_t1 FastDFS]# ./make.sh
[root@Fastdfs_tracker_t1 FastDFS]# ./make.sh install

采用默认安装的方式安装,安装后的相应文件与目录：
服务脚本在：
/etc/init.d/fdfs_storaged
/etc/init.d/fdfs_trackerd

配置文件在（样例配置文件）:
/etc/fdfs/client.conf.sample
/etc/fdfs/storage.conf.sample
/etc/fdfs/tracker.conf.sample

命令工具在/usr/bin/目录下的：
fdfs_appender_test
fdfs_appender_test1
fdfs_append_file
fdfs_crc32
fdfs_delete_file
fdfs_download_file
fdfs_file_info
fdfs_monitor
fdfs_storaged
fdfs_test
fdfs_test1
fdfs_trackerd
fdfs_upload_appender
fdfs_upload_file
stop.sh
restart.sh

因为FastDFS服务脚本设置的bin目录是/usr/local/bin，但实际命令安装在/usr/bin，可以进入/user/bin 目录使用以下命令查看 fdfs 的相关命令：
[root@Fastdfs_tracker_t1 ~]# cd /usr/bin/
[root@Fastdfs_tracker_t1 bin]# ls | grep fdfs

因此需要修改FastDFS服务脚本中相应的命令路径，也就是把/etc/init.d/fdfs_storaged和/etc/init.d/fdfs_trackerd 两个脚本中的/usr/local/bin修改成/usr/bin：
[root@Fastdfs_tracker_t1 bin]# vim /etc/init.d/fdfs_trackerd
使用查找替换命令进统一修改：:%s+/usr/local/bin+/usr/bin

[root@Fastdfs_tracker_t1 bin]# vim /etc/init.d/fdfs_storaged
使用查找替换命令进统一修改：:%s+/usr/local/bin+/usr/bin
```

**3）配置FastDFS跟踪器Tracker（两台机器Fastdfs\_tracker\_t1和Fastdfs\_tracker\_t2都需要操作）**

```text
1）复制 FastDFS 跟踪器样例配置文件,并重命名
[root@Fastdfs_tracker_t1 ~]# cd /etc/fdfs/
[root@Fastdfs_tracker_t1 fdfs]# cp tracker.conf.sample tracker.conf

2）编辑跟踪器配置文件
[root@Fastdfs_tracker_t1 fdfs]# vim /etc/fdfs/tracker.conf      // 修改的内容如下（其它参数保留默认配置）
......
disabled=false               //启用配置文件
port=22122                   //tracker 的端口号，一般采用 22122 这个默认端口
base_path=/fastdfs/tracker   //tracker 的数据文件和日志目录

# the method of selecting group to upload files
# 0: round robin
# 1: specify group
# 2: load balance, select the max free space group to upload file
store_lookup=0              //采取轮巡方式选择要上传的组进行存储，默认2 选择最大空闲空间的组

3）创建基础数据目录（参考基础目录 base_path 配置）
[root@Fastdfs_tracker_t1 fdfs]# mkdir -p /fastdfs/tracker

4）关闭iptables防火墙
[root@Fastdfs_tracker_t1 fdfs]# /etc/init.d/iptables stop
++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
温馨提示：
如果iptables防火墙中开启的话，则需要打开跟踪器端口（默认为 22122）
[root@Fastdfs_tracker_t1 fdfs]# vim /etc/sysconfig/iptables
## FastDFS Tracker Port
-A INPUT -m state --state NEW -m tcp -p tcp --dport 22122 -j ACCEPT

[root@Fastdfs_tracker_t1 fdfs]# service iptables restart
++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++

5）启动Tracker服务
[root@Fastdfs_tracker_t1 fdfs]# /etc/init.d/fdfs_trackerd start
Starting FastDFS tracker server:

初次成功启动，会在/fastdfs/tracker 目录下创建 data、logs 两个目录；可以查看tracker是否启动成功:
[root@Fastdfs_tracker_t1 fdfs]# ls /fastdfs/tracker/
data  logs
[root@Fastdfs_tracker_t1 fdfs]# netstat -unltp|grep fdfs
tcp        0      0 0.0.0.0:22122               0.0.0.0:*                   LISTEN      10469/fdfs_trackerd
[root@Fastdfs_tracker_t1 fdfs]# lsof -i:22122
COMMAND     PID USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
fdfs_trac 10469 root    5u  IPv4 214098      0t0  TCP *:22122 (LISTEN)

[root@Fastdfs_tracker_t1 fdfs]# tail -100f /fastdfs/tracker/logs/trackerd.log

关闭Tracker
[root@Fastdfs_tracker_t2 fdfs]# /etc/init.d/fdfs_trackerd stop

设置FastDFS跟踪器开机启动
[root@Fastdfs_tracker_t1 fdfs]# chkconfig fdfs_trackerd on
[root@Fastdfs_tracker_t1 fdfs]# chkconfig --list|grep fdfs_trackerd
fdfs_trackerd   0:off 1:off 2:on  3:on  4:on  5:on  6:off
[root@Fastdfs_tracker_t1 fdfs]# vim /etc/rc.d/rc.local
[root@Fastdfs_tracker_t1 fdfs]# cat /etc/rc.d/rc.local
.......
/etc/init.d/fdfs_trackerd start
```

**4）配置FastDFS存储服务（三台机器Fastdfs\_storage\_s1、Fastdfs\_storage\_s2、Fastdfs\_storage\_s3都需要操作）**

```text
1）复制FastDFS存储器样例配置文件,并重命名。
[root@Fastdfs_storage_s1 FastDFS]# cd /etc/fdfs/
[root@Fastdfs_storage_s1 fdfs]# cp storage.conf.sample storage.conf
[root@Fastdfs_storage_s1 fdfs]# vim /etc/fdfs/storage.conf             //修改的内容如下（其它参数保留默认配置）:
.......
disabled=false                          //启用配置文件
group_name=group1                       //组名（第一组为 group1，第二组为 group2）
port=23000                              //storage 的端口号,同一个组的 storage 端口号必须相同
base_path=/fastdfs/storage              //设置 storage 的日志目录
store_path0=/fastdfs/storage            //存储路径
store_path_count=1                      //存储路径个数，需要和 store_path 个数匹配
tracker_server=192.168.10.200:22122     //tracker 服务器的 IP 地址和端口
tracker_server=192.168.1.201:22122      //多个 tracker 直接添加多条配置
http.server_port=8888                   //设置 http 端口号

2）创建基础数据目录（参考基础目录 base_path 配置）
[root@Fastdfs_storage_s1 fdfs]# mkdir -p /fastdfs/storage

4）关闭iptables防火墙
[root@Fastdfs_storage_s1 fdfs]# /etc/init.d/iptables stop
++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
温馨提示：
如果iptables防火墙中开启的话，则需要打开23000端口
[root@Fastdfs_tracker_t1 fdfs]# vim /etc/sysconfig/iptables
## FastDFS Tracker Port
-A INPUT -m state --state NEW -m tcp -p tcp --dport 23000 -j ACCEPT

[root@Fastdfs_storage_s1 fdfs]# service iptables restart
++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++

5）启动 Storage
[root@Fastdfs_storage_s1 fdfs]# /etc/init.d/fdfs_storaged start
Starting FastDFS storage server:

初次成功启动，会在/fastdfs/storage 目录下创建数据目录 data 和日志目录 logs）各节点启动动，
使用 tail -f /fastdfs/storage/logs/storaged.log 命令监听存储节点日志，可以看到存储节点链接到跟踪器，
并提示哪一个为 leader 跟踪器。同时也会看到同一组中的其他节点加入进来的日志信息。

查看23000端口监听情况：
[root@Fastdfs_storage_s1 fdfs]# netstat -unltp|grep fdfs
tcp        0      0 0.0.0.0:23000               0.0.0.0:*                   LISTEN      28347/fdfs_storaged
[root@Fastdfs_storage_s1 fdfs]# lsof -i:23000
COMMAND     PID USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
fdfs_stor 28347 root    5u  IPv4 211801      0t0  TCP *:inovaport1 (LISTEN)

[root@Fastdfs_storage_s1 fdfs]# tail -f /fastdfs/storage/logs/storaged.log
mkdir data path: FD ...
mkdir data path: FE ...
mkdir data path: FF ...
data path: /fastdfs/storage/data, mkdir sub dir done.
[2018-02-26 14:27:46] INFO - file: storage_param_getter.c, line: 191, use_storage_id=0, id_type_in_filename=ip, storage_ip_changed_auto_adjust=1, store_path=0, reserved_storage_space=10.00%, use_trunk_file=0, slot_min_size=256, slot_max_size=16 MB, trunk_file_size=64 MB, trunk_create_file_advance=0, trunk_create_file_time_base=02:00, trunk_create_file_interval=86400, trunk_create_file_space_threshold=20 GB, trunk_init_check_occupying=0, trunk_init_reload_from_binlog=0, trunk_compress_binlog_min_interval=0, store_slave_file_use_link=0
[2018-02-26 14:27:46] INFO - file: storage_func.c, line: 254, tracker_client_ip: 192.168.10.202, my_server_id_str: 192.168.10.202, g_server_id_in_filename: -905271104
[2018-02-26 14:27:51] INFO - local_host_ip_count: 2,  127.0.0.1  192.168.10.202
[2018-02-26 14:27:51] INFO - file: tracker_client_thread.c, line: 310, successfully connect to tracker server 192.168.10.201:22122, as a tracker client, my ip is 192.168.10.202
[2018-02-26 14:27:51] INFO - file: tracker_client_thread.c, line: 1235, tracker server 192.168.10.201:22122, set tracker leader: 192.168.10.201:22122
[2018-02-26 14:27:52] INFO - file: tracker_client_thread.c, line: 310, successfully connect to tracker server 192.168.10.200:22122, as a tracker client, my ip is 192.168.10.202
.......

关闭storage
[root@Fastdfs_storage_s1 fdfs]# /etc/init.d/fdfs_storaged stop

设置storage开机启动
[root@Fastdfs_storage_s1 fdfs]# chkconfig fdfs_storaged on
[root@Fastdfs_storage_s1 fdfs]# chkconfig --list|grep fdfs_storaged
fdfs_storaged   0:off 1:off 2:on  3:on  4:on  5:on  6:off
[root@Fastdfs_storage_s1 fdfs]# vim /etc/rc.d/rc.local
......
/etc/init.d/fdfs_storaged start

所有Storage节点都启动之后，可以在任一 Storage 节点上使用如下命令查看集群信息：
[root@Fastdfs_storage_s1 fdfs]# /usr/bin/fdfs_monitor /etc/fdfs/storage.conf
[2018-02-26 14:30:22] DEBUG - base_path=/fastdfs/storage, connect_timeout=30, network_timeout=60, tracker_server_count=2, anti_steal_token=0, anti_steal_secret_key length=0, use_connection_pool=0, g_connection_pool_max_idle_time=3600s, use_storage_id=0, storage server id count: 0

server_count=2, server_index=0

tracker server is 192.168.10.200:22122

group count: 3

Group 1:
group name = group1
disk total space = 50268 MB
disk free space = 45029 MB
trunk free space = 0 MB
storage server count = 1
active server count = 1
storage server port = 23000
storage HTTP port = 8888
store path count = 1
subdir count per path = 256
current write server index = 0
current trunk file id = 0

  Storage 1:
    id = 192.168.10.202
    ip_addr = 192.168.10.202  ACTIVE
    http domain =
    version = 5.05
    join time = 2018-02-26 14:27:07
    up time = 2018-02-26 14:27:07
    total storage = 50268 MB
    free storage = 45029 MB
    upload priority = 10
    store_path_count = 1
    subdir_count_per_path = 256
    storage_port = 23000
    storage_http_port = 8888
    current_write_path = 0
    source storage id =
    if_trunk_server = 0
    connection.alloc_count = 256
    connection.current_count = 0
    connection.max_count = 0
    total_upload_count = 0
    success_upload_count = 0
    total_append_count = 0
    success_append_count = 0
    total_modify_count = 0
    success_modify_count = 0
    total_truncate_count = 0
    success_truncate_count = 0
    total_set_meta_count = 0
    success_set_meta_count = 0
    total_delete_count = 0
    success_delete_count = 0
    total_download_count = 0
    success_download_count = 0
    total_get_meta_count = 0
    success_get_meta_count = 0
    total_create_link_count = 0
    success_create_link_count = 0
    total_delete_link_count = 0
    success_delete_link_count = 0
    total_upload_bytes = 0
    success_upload_bytes = 0
    total_append_bytes = 0
    success_append_bytes = 0
    total_modify_bytes = 0
    success_modify_bytes = 0
    stotal_download_bytes = 0
    success_download_bytes = 0
    total_sync_in_bytes = 0
    success_sync_in_bytes = 0
    total_sync_out_bytes = 0
    success_sync_out_bytes = 0
    total_file_open_count = 0
    success_file_open_count = 0
    total_file_read_count = 0
    success_file_read_count = 0
    total_file_write_count = 0
    success_file_write_count = 0
    last_heart_beat_time = 2018-02-26 14:29:52
    last_source_update = 1970-01-01 08:00:00
    last_sync_update = 1970-01-01 08:00:00
    last_synced_timestamp = 1970-01-01 08:00:00

Group 2:
group name = group2
disk total space = 49086 MB
disk free space = 44696 MB
trunk free space = 0 MB
storage server count = 1
active server count = 1
storage server port = 23000
storage HTTP port = 8888
store path count = 1
subdir count per path = 256
current write server index = 0
current trunk file id = 0

  Storage 1:
    id = 192.168.10.203
    ip_addr = 192.168.10.203  ACTIVE
    http domain =
    version = 5.05
    join time = 2018-02-26 14:27:09
    up time = 2018-02-26 14:27:09
    total storage = 49086 MB
    free storage = 44696 MB
    upload priority = 10
    store_path_count = 1
    subdir_count_per_path = 256
    storage_port = 23000
    storage_http_port = 8888
    current_write_path = 0
    source storage id =
    if_trunk_server = 0
    connection.alloc_count = 256
    connection.current_count = 0
    connection.max_count = 0
    total_upload_count = 0
    success_upload_count = 0
    total_append_count = 0
    success_append_count = 0
    total_modify_count = 0
    success_modify_count = 0
    total_truncate_count = 0
    success_truncate_count = 0
    total_set_meta_count = 0
    success_set_meta_count = 0
    total_delete_count = 0
    success_delete_count = 0
    total_download_count = 0
    success_download_count = 0
    total_get_meta_count = 0
    success_get_meta_count = 0
    total_create_link_count = 0
    success_create_link_count = 0
    total_delete_link_count = 0
    success_delete_link_count = 0
    total_upload_bytes = 0
    success_upload_bytes = 0
    total_append_bytes = 0
    success_append_bytes = 0
    total_modify_bytes = 0
    success_modify_bytes = 0
    stotal_download_bytes = 0
    success_download_bytes = 0
    total_sync_in_bytes = 0
    success_sync_in_bytes = 0
    total_sync_out_bytes = 0
    success_sync_out_bytes = 0
    total_file_open_count = 0
    success_file_open_count = 0
    total_file_read_count = 0
    success_file_read_count = 0
    total_file_write_count = 0
    success_file_write_count = 0
    last_heart_beat_time = 2018-02-26 14:30:15
    last_source_update = 1970-01-01 08:00:00
    last_sync_update = 1970-01-01 08:00:00
    last_synced_timestamp = 1970-01-01 08:00:00

Group 3:
group name = group3
disk total space = 49086 MB
disk free space = 44962 MB
trunk free space = 0 MB
storage server count = 1
active server count = 1
storage server port = 23000
storage HTTP port = 8888
store path count = 1
subdir count per path = 256
current write server index = 0
current trunk file id = 0

  Storage 1:
    id = 192.168.10.204
    ip_addr = 192.168.10.204  ACTIVE
    http domain =
    version = 5.05
    join time = 2018-02-26 14:27:10
    up time = 2018-02-26 14:27:10
    total storage = 49086 MB
    free storage = 44962 MB
    upload priority = 10
    store_path_count = 1
    subdir_count_per_path = 256
    storage_port = 23000
    storage_http_port = 8888
    current_write_path = 0
    source storage id =
    if_trunk_server = 0
    connection.alloc_count = 256
    connection.current_count = 0
    connection.max_count = 0
    total_upload_count = 0
    success_upload_count = 0
    total_append_count = 0
    success_append_count = 0
    total_modify_count = 0
    success_modify_count = 0
    total_truncate_count = 0
    success_truncate_count = 0
    total_set_meta_count = 0
    success_set_meta_count = 0
    total_delete_count = 0
    success_delete_count = 0
    total_download_count = 0
    success_download_count = 0
    total_get_meta_count = 0
    success_get_meta_count = 0
    total_create_link_count = 0
    success_create_link_count = 0
    total_delete_link_count = 0
    success_delete_link_count = 0
    total_upload_bytes = 0
    success_upload_bytes = 0
    total_append_bytes = 0
    success_append_bytes = 0
    total_modify_bytes = 0
    success_modify_bytes = 0
    stotal_download_bytes = 0
    success_download_bytes = 0
    total_sync_in_bytes = 0
    success_sync_in_bytes = 0
    total_sync_out_bytes = 0
    success_sync_out_bytes = 0
    total_file_open_count = 0
    success_file_open_count = 0
    total_file_read_count = 0
    success_file_read_count = 0
    total_file_write_count = 0
    success_file_write_count = 0
    last_heart_beat_time = 2018-02-26 14:29:55
    last_source_update = 1970-01-01 08:00:00
    last_sync_update = 1970-01-01 08:00:00
    last_synced_timestamp = 1970-01-01 08:00:00
```

**5）文件上传测试（在客户机192.168.10.205上测试即可，也可以直接在Fastdfs\_tracker服务器如192.168.10.200上测试，只要在安装fastdfs\_client机器上测试就行）**

```text
1）修改客户端配置文件
[root@Fastdfs_client ~]# cp /etc/fdfs/client.conf.sample /etc/fdfs/client.conf
[root@Fastdfs_client ~]# vim /etc/fdfs/client.conf         //修改的内容如下（其它参数保留默认配置）:
......
base_path=/fastdfs/tracker
tracker_server=192.168.10.200:22122
tracker_server=192.168.10.201:22122

2）创建基础数据目录（参考基础目录 base_path 配置）
[root@Fastdfs_client ~]# mkdir -p /fastdfs/tracker

3）上传（可以通过Xftp5上传）一张图片（比如test.png）到客户机192.168.10.205目录/usr/local/src/，用作上传测试
[root@Fastdfs_client src]# pwd
/usr/local/src
[root@Fastdfs_client src]# ll test.png
-rw-r--r--. 1 root root 165712 Feb 26 14:53 test.png

然后使用FastDFS上传命令，将测试图片上传到FastDFS存储里。
[root@Fastdfs_client src]# /usr/bin/fdfs_upload_file /etc/fdfs/client.conf /usr/local/src/test.png
group1/M00/00/00/wKgKylqTsFCAdeEuAAKHUIZ3K9s287.png

如上能返回文件ID号，说明上传成功。由返回的信息可知，test.png图片被上传到group1下的数据目录下了。
到group1组内机器（即Fastdfs_storage_s1）的数据目录下可找到刚才上传的文件：
[root@Fastdfs_storage_s1 ~]# ll /fastdfs/storage/data/00/00/
total 164
-rw-r--r--. 1 root root 165712 Feb 26 14:59 wKgKylqTsFCAdeEuAAKHUIZ3K9s287.png

同理，再上传一张图片进行测试
[root@Fastdfs_client ~]# cd /usr/local/src/
[root@Fastdfs_client src]# ll haha.png
-rw-r--r--. 1 root root 12331 Feb 26 16:40 haha.png
[root@Fastdfs_client src]# /usr/bin/fdfs_upload_file /etc/fdfs/client.conf /usr/local/src/haha.png
group2/M00/00/00/wKgKy1qTyCKAFGzSAAAwK2jUNaY428.png

即haha.png图片被上传到group2组内机器的数据目录下了
[root@Fastdfs_storage_s2 ~]# ll /fastdfs/storage/data/00/00/
total 16
-rw-r--r--. 1 root root 12331 Feb 26 16:41 wKgKy1qTyCKAFGzSAAAwK2jUNaY428.png

再上传一次
[root@Fastdfs_client src]# /usr/bin/fdfs_upload_file /etc/fdfs/client.conf /usr/local/src/haha.png
group3/M00/00/00/wKgKzFqTyRaAeDLJAAAwK2jUNaY992.png
[root@Fastdfs_storage_s3 ~]# ll /fastdfs/storage/data/00/00/
total 16
-rw-r--r--. 1 root root 12331 Feb 26 16:45 wKgKzFqTyRaAeDLJAAAwK2jUNaY992.png

这里，我测试的时候，一个group组内只有一台存储服务器，测试图片上传到group内，只上传到一台存储服务器上了。
正常来说，应该要在同一个group组内添加2台以上的存储服务器，这样图片就是被上传到多台存储服务器上，
那么当一台存储服务器挂掉后，图片还能从其他的存储服务器上读出来。
```

**6）在各个存储节点上安装Nginx（Fastdfs\_storage\_s1、Fastdfs\_storage\_s2、Fastdfs\_storage\_s3）**

```text
1）fastdfs-nginx-module 作用说明
 FastDFS 通过Tracker服务器,将文件放在Storage服务器存储，但是同组存储服务器之间需要进入文件复制，有同步延迟的问题。
 假设Tracker服务器将文件上传到了A机器上，上传成功后文件ID已经返回给客户端。此时FastDFS存储集群机制会将这个文件同步到同组存储B机器上，
 在文件还没有复制完成的情况下，客户端如果用这个文件ID在B机器上取文件,就会出现文件无法访问的错误。
 而fastdfs-nginx-module可以重定向文件连接到源服务器取文件,避免客户端由于复制延迟导致的文件无法访问错误。
 （解压后的fastdfs-nginx-module在nginx安装时使用）

2）安装nginx前，需要配置以下依赖
[root@Fastdfs_storage_s1 ~]# yum install gcc gcc-c++ make automake autoconf libtool pcre pcre-devel zlib zlib-devel openssl openssl-devel

3）安装nginx和fastdfs-nginx-module
[root@Fastdfs_storage_s1 ~]# cd /usr/local/src/
[root@Fastdfs_storage_s1 src]# wget http://nginx.org/download/nginx-1.13.9.tar.gz

下载fastdfs-nginx-module地址：https://pan.baidu.com/s/1i6Jqsi5  
提取密码：g1ps

[root@Fastdfs_storage_s1 src]# ll fastdfs-nginx-module_v1.16.tar.gz
-rw-r--r--. 1 root root 17510 Feb 26 16:58 fastdfs-nginx-module_v1.16.tar.gz
[root@Fastdfs_storage_s1 src]# ls nginx-1.13.9.tar.gz
nginx-1.13.9.tar.gz

[root@Fastdfs_storage_s1 src]# tar -zvxf nginx-1.13.9.tar.gz
[root@Fastdfs_storage_s1 src]# tar -zvxf fastdfs-nginx-module_v1.16.tar.gz

[root@Fastdfs_storage_s1 src]# vim /usr/local/src/fastdfs-nginx-module/src/config
将
CORE_INCS="$CORE_INCS /usr/local/include/fastdfs /usr/local/include/fastcommon/"
修改为：
CORE_INCS="$CORE_INCS /usr/include/fastdfs /usr/include/fastcommon/"

千万注意：这个路径修改是很重要的，不然在 nginx 编译的时候会报错的！

紧接着进行nginx的编译安装操作
[root@Fastdfs_storage_s1 src]# cd nginx-1.13.9
[root@Fastdfs_storage_s1 nginx-1.13.9]# ./configure --prefix=/usr/local/nginx --add-module=/usr/local/src/fastdfs-nginx-module/src
[root@Fastdfs_storage_s1 nginx-1.13.9]# make && make install

4）复制 fastdfs-nginx-module 源码中的配置文件到/etc/fdfs 目录，并修改
[root@Fastdfs_storage_s1 src]# cp /usr/local/src/fastdfs-nginx-module/src/mod_fastdfs.conf /etc/fdfs/
[root@Fastdfs_storage_s1 src]# vim /etc/fdfs/mod_fastdfs.conf
......
connect_timeout=10
base_path=/tmp
tracker_server=192.168.10.200:22122
tracker_server=192.168.10.201:22122
storage_server_port=23000
group_name=group1
url_have_group_name = true
store_path0=/fastdfs/storage
group_count = 3

[group1]
group_name=group1
storage_server_port=23000
store_path_count=1
store_path0=/fastdfs/storage

[group2]
group_name=group2
storage_server_port=23000
store_path_count=1
store_path0=/fastdfs/storage

[group3]
group_name=group3
storage_server_port=23000
store_path_count=1
store_path0=/fastdfs/storage

特别注意：
第二组内的Storage服务器（这里指Fastdfs_storage_s2）的mod_fastdfs.conf配置与第一组配置只有group_nam不同，即：
group_name=group2

第三组内的Storage服务器（这里指Fastdfs_storage_s3）的mod_fastdfs.conf 配置与第一组配置只有group_name不同，即：
group_name=group3

5）复制 FastDFS 的部分配置文件到/etc/fdfs 目录
[root@Fastdfs_storage_s1 src]# cd /usr/local/src/FastDFS/conf
[root@Fastdfs_storage_s1 conf]# cp http.conf mime.types /etc/fdfs/

6）在/fastdfs/storage 文件存储目录下创建软连接,将其链接到实际存放数据的目录
[root@Fastdfs_storage_s1 conf]# ln -s /fastdfs/storage/data/ /fastdfs/storage/data/M00

7）配置 Nginx，简洁版 nginx 配置样例
[root@Fastdfs_storage_s1 src]# cd /usr/local/nginx/conf/
[root@Fastdfs_storage_s1 conf]# cp nginx.conf nginx.conf.bak
[root@Fastdfs_storage_s1 conf]# cat nginx.conf
user  www;                                                   //如果url访问图片时出现404，可以将这个修改为"user root"
worker_processes  8;

#error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;

#pid        logs/nginx.pid;


events {
    worker_connections  65535;
}


http {
    include       mime.types;
    default_type  application/octet-stream;
    charset utf-8;

    ######
    ## set access log format
    ######
    log_format  main  '$http_x_forwarded_for $remote_addr $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_cookie" $host $request_time';

    #######
    ## http setting
    #######
    sendfile       on;
    tcp_nopush     on;
    tcp_nodelay    on;
    keepalive_timeout  65;
    proxy_cache_path /var/www/cache levels=1:2 keys_zone=mycache:20m max_size=2048m inactive=60m;
    proxy_temp_path /var/www/cache/tmp;

    fastcgi_connect_timeout 3000;
    fastcgi_send_timeout 3000;
    fastcgi_read_timeout 3000;
    fastcgi_buffer_size 256k;
    fastcgi_buffers 8 256k;
    fastcgi_busy_buffers_size 256k;
    fastcgi_temp_file_write_size 256k;
    fastcgi_intercept_errors on;

    #
    client_header_timeout 600s;
    client_body_timeout 600s;
   # client_max_body_size 50m;
    client_max_body_size 100m;               #允许客户端请求的最大单个文件字节数
    client_body_buffer_size 256k;            #缓冲区代理缓冲请求的最大字节数，可以理解为先保存到本地再传给用户

    gzip  on;
    gzip_min_length  1k;
    gzip_buffers     4 16k;
    gzip_http_version 1.1;
    gzip_comp_level 9;
    gzip_types       text/plain application/x-javascript text/css application/xml text/javascript application/x-httpd-php;
    gzip_vary on;

    ## includes vhosts
    include vhosts/*.conf;
}

[root@Fastdfs_storage_s1 conf]# mkdir vhosts
[root@Fastdfs_storage_s1 conf]# cd vhosts/
[root@Fastdfs_storage_s1 vhosts]# vim fastdfs.conf
[root@Fastdfs_storage_s1 vhosts]# cat fastdfs.conf
server {
   listen 8888;
   server_name localhost;
   location /group1/M00 {                         //注意，Fastdfs_storage_s2机器内此处修改为/group2/M00,Fastdfs_storage_s3机器内此处修改为/group3/M00
   #alias /fastdfs/storage/data;
   ngx_fastdfs_module;
}

   error_page 500 502 503 504 /50x.html;
   location = /50x.html {
   root html;
   }
}

[root@Fastdfs_storage_s1 vhosts]#useradd www
[root@Fastdfs_storage_s1 vhosts]# mkdir -p /var/www/cache
[root@Fastdfs_storage_s1 vhosts]# /usr/local/nginx/sbin/nginx -t
[root@Fastdfs_storage_s1 vhosts]# /usr/local/nginx/sbin/nginx
[root@Fastdfs_storage_s1 vhosts]# /usr/local/nginx/sbin/nginx -s reload
[root@Fastdfs_storage_s1 vhosts]# lsof -i:8888
COMMAND   PID USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
nginx   31543 root   10u  IPv4 218910      0t0  TCP *:ddi-tcp-1 (LISTEN)
nginx   31617  www   10u  IPv4 218910      0t0  TCP *:ddi-tcp-1 (LISTEN)
nginx   31618  www   10u  IPv4 218910      0t0  TCP *:ddi-tcp-1 (LISTEN)
nginx   31619  www   10u  IPv4 218910      0t0  TCP *:ddi-tcp-1 (LISTEN)
nginx   31620  www   10u  IPv4 218910      0t0  TCP *:ddi-tcp-1 (LISTEN)
nginx   31621  www   10u  IPv4 218910      0t0  TCP *:ddi-tcp-1 (LISTEN)
nginx   31622  www   10u  IPv4 218910      0t0  TCP *:ddi-tcp-1 (LISTEN)
nginx   31623  www   10u  IPv4 218910      0t0  TCP *:ddi-tcp-1 (LISTEN)
nginx   31624  www   10u  IPv4 218910      0t0  TCP *:ddi-tcp-1 (LISTEN)

+++++++++++++++++++++++++++++++++++++++++++特别注意++++++++++++++++++++++++++++++++++++++++++++++++++
A、8888 端口值是要与/etc/fdfs/storage.conf 中的 http.server_port=8888 相对应，
   因为 http.server_port 默认为 8888,如果想改成 80，则要对应修改过来。
B、Storage 对应有多个 group 的情况下，访问路径带 group 名，如/group1/M00/00/00/xxx，
   对应的 Nginx 配置为：
   location ~/group([0-9])/M00 {
   ngx_fastdfs_module;
   }
C、如查下载时如发现老报 404，将 nginx.conf 第一行 user www 修改为 user root 后重新启动！这个要特别注意！！！！！
++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++

8）设置Nginx开机启动
[root@Fastdfs_storage_s1 ~]# vim /etc/rc.d/rc.local
........
/usr/local/nginx/sbin/nginx

9）关闭iptables防火墙
[root@Fastdfs_storage_s1 conf]# /etc/init.d/iptables stop
++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
温馨提示：
如果打开了iptables防火墙功能，则需要开通8888端口访问
[root@Fastdfs_storage_s1 ~]# vim /etc/sysconfig/iptables
## FastDFS Tracker Port
-A INPUT -m state --state NEW -m tcp -p tcp --dport 8888 -j ACCEPT

[root@Fastdfs_storage_s1 ~]# service iptables restart
++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++

10）通过浏览器访问测试时上传的文件（对应上传到FastDFS存储里的路径）
在客户端机器上传图片
[root@Fastdfs_client src]# /usr/bin/fdfs_upload_file /etc/fdfs/client.conf /usr/local/src/haha.png
group1/M00/00/00/wKgKylqT3OCAUrqYAAAwK2jUNaY262.png
[root@Fastdfs_client src]# /usr/bin/fdfs_upload_file /etc/fdfs/client.conf /usr/local/src/test.png
group2/M00/00/00/wKgKy1qT3P-Ae-vQAAKHUIZ3K9s459.png
[root@Fastdfs_client src]# /usr/bin/fdfs_upload_file /etc/fdfs/client.conf /usr/local/src/test.png
group3/M00/00/00/wKgKzFqT3SWAaunZAAKHUIZ3K9s849.png

然后访问图片的url
http://192.168.10.202:8888/group1/M00/00/00/wKgKylqT3OCAUrqYAAAwK2jUNaY262.png
http://192.168.10.203:8888/group2/M00/00/00/wKgKy1qT3P-Ae-vQAAKHUIZ3K9s459.png
http://192.168.10.204:8888/group3/M00/00/00/wKgKzFqT3SWAaunZAAKHUIZ3K9s849.png
```

**7）在各个跟踪节点服务器上安装Nginx（Fastdfs\_tracker\_t1、Fastdfs\_tracker\_t2）**

在tracker上安装的nginx主要为了提供 http 访问的反向代理、负载均衡以及缓存服务

```text
1）安装编译 Nginx 所需的依赖包
[root@Fastdfs_tracker_t1 ~]# yum install gcc gcc-c++ make automake autoconf libtool pcre pcre-devel zlib zlib-devel openssl openssl-devel

2）上传 ngx_cache_purge-2.3.tar.gz和nginx-1.13.9.tar.gz到/usr/local/src，解压
[root@Fastdfs_tracker_t1 ~]# cd /usr/local/src/
[root@Fastdfs_tracker_t1 src]# wget http://labs.frickle.com/files/ngx_cache_purge-2.3.tar.gz
[root@Fastdfs_tracker_t1 src]# wget http://nginx.org/download/nginx-1.13.9.tar.gz
[root@Fastdfs_tracker_t1 src]# tar -zvxf ngx_cache_purge-2.3.tar.gz
[root@Fastdfs_tracker_t1 src]# tar -zvxf nginx-1.13.9.tar.gz
[root@Fastdfs_tracker_t1 src]# cd nginx-1.13.9
[root@Fastdfs_tracker_t1 nginx-1.13.9]# ./configure --prefix=/usr/local/nginx --add-module=/usr/local/src/ngx_cache_purge-2.3 && make && make install

3）配置 Nginx，设置负载均衡以及缓存
[root@Fastdfs_tracker_t1 conf]# cp nginx.conf nginx.conf.conf
[root@Fastdfs_tracker_t1 conf]# cat nginx.conf
user  www;
worker_processes  8;

#error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;

#pid        logs/nginx.pid;

events {
    worker_connections  65535;
}

http {
    include       mime.types;
    default_type  application/octet-stream;
    charset utf-8;

    ######
    ## set access log format
    ######
    log_format  main  '$http_x_forwarded_for $remote_addr $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_cookie" $host $request_time';

    #######
    ## http setting
    #######
    sendfile       on;
    tcp_nopush     on;
    tcp_nodelay    on;
    keepalive_timeout  65;
    proxy_cache_path /fastdfs/cache/nginx/proxy_cache levels=1:2 keys_zone=mycache:20m max_size=2048m inactive=60m;
    proxy_temp_path /fastdfs/cache/nginx/proxy_cache/tmp;

    fastcgi_connect_timeout 3000;
    fastcgi_send_timeout 3000;
    fastcgi_read_timeout 3000;
    fastcgi_buffer_size 256k;
    fastcgi_buffers 8 256k;
    fastcgi_busy_buffers_size 256k;
    fastcgi_temp_file_write_size 256k;
    fastcgi_intercept_errors on;

    #
    client_header_timeout 600s;
    client_body_timeout 600s;
   # client_max_body_size 50m;
    client_max_body_size 100m;
    client_body_buffer_size 256k;

    gzip  on;
    gzip_min_length  1k;
    gzip_buffers     4 16k;
    gzip_http_version 1.1;
    gzip_comp_level 9;
    gzip_types       text/plain application/x-javascript text/css application/xml text/javascript application/x-httpd-php;
    gzip_vary on;

    ## includes vhosts
    include vhosts/*.conf;
}

[root@Fastdfs_tracker_t1 conf]# mkdir vhosts
[root@Fastdfs_tracker_t1 vhosts]# cat lb.conf                    //如果group组内有多个存储服务器，则这里的负载就起作用了。
upstream fdfs_group1 {
    server 192.168.10.202:8888 weight=1 max_fails=2 fail_timeout=30s;
              }

upstream fdfs_group2 {
    server 192.168.10.203:8888 weight=1 max_fails=2 fail_timeout=30s;
              }

upstream fdfs_group3 {
    server 192.168.10.204:8888 weight=1 max_fails=2 fail_timeout=30s;
              }

server{
    listen 8000;
    server_name localhost;

     access_log  /usr/local/nginx/logs/lb_access.log main;
     error_log  /usr/local/nginx/logs/lb_error.log;

    location /group1/M00 {
       proxy_pass http://fdfs_group1;
       }

    location /group2/M00 {
       proxy_pass http://fdfs_group2;
       }

    location /group3/M00 {
       proxy_pass http://fdfs_group3;
       }

#设置清除缓存的访问权限
    location ~/purge(/.*) {
       allow 127.0.0.1;
       allow 114.242.193.201;
       allow 192.168.10.0/24;
       deny all;
       proxy_cache_purge mycache $host$1$is_args$args ;
       }
}

按以上 nginx 配置文件的要求，创建对应的缓存目录
[root@Fastdfs_tracker_t1 conf]# useradd www
[root@Fastdfs_tracker_t1 conf]# ulimit 65535
[root@Fastdfs_tracker_t1 conf]# mkdir -p /fastdfs/cache/nginx/proxy_cache
[root@Fastdfs_tracker_t1 conf]# mkdir -p /fastdfs/cache/nginx/proxy_cache/tmp
[root@Fastdfs_tracker_t1 conf]# /usr/local/nginx/sbin/nginx -t
[root@Fastdfs_tracker_t1 conf]# /usr/local/nginx/sbin/nginx
[root@Fastdfs_tracker_t1 conf]# /usr/local/nginx/sbin/nginx -s reload

4）关闭iptables防火墙
[root@Fastdfs_tracker_t1 conf]# /etc/init.d/iptables stop
++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
温馨提示：
如果iptables防火墙中开启的话，则需要打开8000端口
[root@Fastdfs_tracker_t1 fdfs]# vim /etc/sysconfig/iptables
## FastDFS Tracker Port
-A INPUT -m state --state NEW -m tcp -p tcp --dport 8000 -j ACCEPT

[root@Fastdfs_tracker_t1 conf]# service iptables restart
++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++

5）设置Nginx开机启动
[root@Fastdfs_storage_s1 ~]# vim /etc/rc.d/rc.local
........
/usr/local/nginx/sbin/nginx

6）文件访问测试
在客户端机器上传图片（或使用上面测试中已经上传过的图片路径进行访问测试）
[root@Fastdfs_client src]#  /usr/bin/fdfs_upload_file /etc/fdfs/client.conf /usr/local/src/test.jpg
group1/M00/00/00/wKgKylqUxBOAFo8hAAKHUIZ3K9s443.jpg
[root@Fastdfs_client src]#  /usr/bin/fdfs_upload_file /etc/fdfs/client.conf /usr/local/src/haha.png
group2/M00/00/00/wKgKy1qUxBeAT0PxAAAwK2jUNaY575.png
[root@Fastdfs_client src]#  /usr/bin/fdfs_upload_file /etc/fdfs/client.conf /usr/local/src/test.jpg
group3/M00/00/00/wKgKzFqUxBmATFurAAKHUIZ3K9s282.jpg

然后访问图片的url
http://192.168.10.200:8000/group1/M00/00/00/wKgKylqUxBOAFo8hAAKHUIZ3K9s443.jpg
http://192.168.10.201:8000/group1/M00/00/00/wKgKylqUxBOAFo8hAAKHUIZ3K9s443.jpg
http://192.168.10.200:8000/group2/M00/00/00/wKgKy1qUxBeAT0PxAAAwK2jUNaY575.png
http://192.168.10.201:8000/group2/M00/00/00/wKgKy1qUxBeAT0PxAAAwK2jUNaY575.png
http://192.168.10.200:8000/group3/M00/00/00/wKgKzFqUxBmATFurAAKHUIZ3K9s282.jpg
http://192.168.10.201:8000/group3/M00/00/00/wKgKzFqUxBmATFurAAKHUIZ3K9s282.jpg

以上是简单配置，多次测试可以发现，每一个Tracker中的 Nginx都单独对后端的Storage存储服务器组做了负载均衡，
但整套FastDFS 集群如果想对外提供统一的文件访问地址，还需要对两个Tracker中的Nginx进行HA集群。

即对Fastdfs_tracker_t1（192.168.10.200）和Fastdfs_tracker_t2（192.168.10.201）做keepalived+nginx的高可用。
192.168.10.200       Master
192.168.10.201       Slave
192.168.10.210       VIP

也就是说整套FastDFS 集群对外提供统一的文件访问(VIP)地址192.168.10.210.
Keepalived+Nginx 主从部署记录可以参考：http://www.cnblogs.com/kevingrace/p/6138185.html，部署记录这里就不介绍了
```

