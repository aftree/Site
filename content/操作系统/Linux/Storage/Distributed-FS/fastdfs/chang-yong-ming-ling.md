# 常用命令

```text
1）上传文件命令：fdfs_upload_file
不仅可以上传图片文件，也可以上传其他文件。
[root@Fastdfs_client src]# echo "hahaha" > test.txt
[root@Fastdfs_client src]#  /usr/bin/fdfs_upload_file /etc/fdfs/client.conf /usr/local/src/test.txt
group3/M00/00/00/wKgKzFqUyiqAabn8AAAAB-kMomQ229.txt

上传成功后，返回一串带有组和路径标识的字符串，这是文件的id，然后到group3组内的存储服务器上查看这个文件的id信息
[root@Fastdfs_storage_s3 ~]# ll /fastdfs/storage/data/00/00/
total 84
-rw-r--r--. 1 root root      7 Feb 27 11:02 wKgKzFqUyiqAabn8AAAAB-kMomQ229.txt

[root@Fastdfs_storage_s3 ~]# cat /fastdfs/storage/data/00/00/wKgKzFqUyiqAabn8AAAAB-kMomQ229.txt
hahaha

[root@Fastdfs_storage_s3 ~]# md5sum /fastdfs/storage/data/00/00/wKgKzFqUyiqAabn8AAAAB-kMomQ229.txt
5a6d311c0d8f6d1dd03c1c129061d3b1  /fastdfs/storage/data/00/00/wKgKzFqUyiqAabn8AAAAB-kMomQ229.txt

2）下载文件命令：fdfs_download_file
在客户端机器上将上面上传到Fastdfs存储里文文件下载下来
[root@Fastdfs_client ~]# cd /mnt/
[root@Fastdfs_client mnt]# /usr/bin/fdfs_download_file /etc/fdfs/client.conf group3/M00/00/00/wKgKzFqUyiqAabn8AAAAB-kMomQ229.txt
[root@Fastdfs_client mnt]# ls
wKgKzFqUyiqAabn8AAAAB-kMomQ229.txt
[root@Fastdfs_client mnt]# cat wKgKzFqUyiqAabn8AAAAB-kMomQ229.txt
hahaha

[root@Fastdfs_client mnt]# md5sum wKgKzFqUyiqAabn8AAAAB-kMomQ229.txt
5a6d311c0d8f6d1dd03c1c129061d3b1  wKgKzFqUyiqAabn8AAAAB-kMomQ229.txt

下载后可以对比文件的md5值。

3）查看文件信息命令：fdfs_file_info
[root@Fastdfs_client ~]# /usr/bin/fdfs_file_info /etc/fdfs/client.conf group3/M00/00/00/wKgKzFqUyiqAabn8AAAAB-kMomQ229.txt
source storage id: 0
source ip address: 192.168.10.204
file create timestamp: 2018-02-27 11:02:02
file size: 7
file crc32: 3909919332 (0xE90CA264)

4）删除文件命令：fdfs_delete_file
[root@Fastdfs_client ~]# /usr/bin/fdfs_delete_file /etc/fdfs/client.conf group3/M00/00/00/wKgKzFqUyiqAabn8AAAAB-kMomQ229.txt

删除之后没有信息提示，我们再进行下载和查找，结果如下：
[root@Fastdfs_client mnt]# /usr/bin/fdfs_download_file /etc/fdfs/client.conf group3/M00/00/00/wKgKzFqUyiqAabn8AAAAB-kMomQ229.txt
[2018-02-27 14:21:08] ERROR - file: tracker_proto.c, line: 48, server: 192.168.10.204:23000, response status 2 != 0
download file fail, error no: 2, error info: No such file or directory

到group3组内的存储服务器上查看，发现没有这个文件的id信息
[root@Fastdfs_storage_s3 ~]# ll /fastdfs/storage/data/00/00/
[root@Fastdfs_storage_s3 ~]#

5）追加文件内容命令：fdfs_upload_appender 和 fdfs_append_file
先使用命令上传一个appender文件
[root@Fastdfs_client src]# echo "123456" > haha.txt
[root@Fastdfs_client src]# /usr/bin/fdfs_upload_appender /etc/fdfs/client.conf /usr/local/src/haha.txt
group1/M00/00/00/wKgKylqU_myEa194AAAAACXX7BE787.txt

到group1组内的存储服务器上查看这个文件
[root@Fastdfs_storage_s1 ~]# ll /fastdfs/storage/data/00/00/
total 12
-rw-r--r--. 1 root root      7 Feb 27 14:45 wKgKylqU_myEa194AAAAACXX7BE787.txt
[root@Fastdfs_storage_s1 ~]# cat /fastdfs/storage/data/00/00/wKgKylqU_myEa194AAAAACXX7BE787.txt
123456

然后进行内容追加
[root@Fastdfs_client src]# echo "abcdefgh" > heihei.txt
[root@Fastdfs_client src]# /usr/bin/fdfs_append_file /etc/fdfs/client.conf group1/M00/00/00/wKgKylqU_myEa194AAAAACXX7BE787.txt /usr/local/src/heihei.txt

再次到group1组内的存储服务器上查看这个文件内容
[root@Fastdfs_storage_s1 ~]# cat /fastdfs/storage/data/00/00/wKgKylqU_myEa194AAAAACXX7BE787.txt
123456
abcdefgh

或者将文件下载到本地查看
[root@Fastdfs_client src]# cd /mnt/
[root@Fastdfs_client mnt]# /usr/bin/fdfs_download_file /etc/fdfs/client.conf group1/M00/00/00/wKgKylqU_myEa194AAAAACXX7BE787.txt
[root@Fastdfs_client mnt]# ls
wKgKylqU_myEa194AAAAACXX7BE787.txt 
[root@Fastdfs_client mnt]# cat wKgKylqU_myEa194AAAAACXX7BE787.txt
123456
abcdefgh

6）监控服务器状态命令：fdfs_monitor
[root@Fastdfs_client src]# /usr/bin/fdfs_monitor /etc/fdfs/client.conf
[2018-02-27 14:49:06] DEBUG - base_path=/fastdfs/tracker, connect_timeout=30, network_timeout=60, tracker_server_count=2, anti_steal_token=0, anti_steal_secret_key length=0, use_connection_pool=0, g_connection_pool_max_idle_time=3600s, use_storage_id=0, storage server id count: 0

server_count=2, server_index=0

tracker server is 192.168.10.200:22122

group count: 3

Group 1:
group name = group1
disk total space = 50268 MB
disk free space = 44987 MB
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
........
........

7）移除故障节点示例
如果一个group组内有多个节点，其中一个节点的上的fastdfs出了问题，需要将它从组中移除，可根据下面的命令操作：
a）停掉fastdfs上的storage服务
   # /etc/init.d/fdfs_storaged stop
   # lsof -i :23000        //确保storage服务停止
b）将问题节点（比如group1组内的节点storage-node3，ip为192.168.10.23）从组中移除
   # /usr/bin/fdfs_monitor /etc/fdfs/client.conf delete group1 192.168.10.23
c）移除成功，使用fdfs_monitor命令进行监控
   # /usr/bin/fdfs_monitor /etc/fdfs/client.conf

通过监控，可以发现移除之后，storage-node3节点的状态为DELETED。
d）当storage-node3上的fastdfs修复后，可以再把它加入到fastdfs组中，加入的操作只需要把storage-node3上的fastdfs服务正常启动就好，
因为配置文件已经做了配置，如果服务正常的话，修复后的节点可以自动加入到fastdfs组中

启动fastdfs_storaged服务
   # /etc/init.d/fdfs_storaged stop
   # lsof -i :23000 

使用fdfs_monitor命令进行监控
   # /usr/bin/fdfs_monitor /etc/fdfs/client.conf

修复后，再次监控，可以看到，此时storage-node3已经变为ACTIVE状态了。
```

