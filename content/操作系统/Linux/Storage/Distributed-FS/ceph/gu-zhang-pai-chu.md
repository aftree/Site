# 故障排除

**1）修改 OSD CRUSH weight**  
1.1）问题描述  
部署完成后，集群处于 PG Degraded 状态，经查 ceph health detail，发现 PG 的 acting OSD 只有 \[0\]，而不是两个。查 osd tree，osd 日志等，看不出明显问题。

1.2）原因分析  
我的 Ceph 集群的 OSD 的 weight 都是 0！！

```text
[root@ceph1]# /etc/ceph# ceph osd tree
# id    weight  type name       up/down reweight
-1      0       root default
-2      0               host ceph1
0       0                       osd.0   up      1
2       0                       osd.2   up      1
-3      0               host ceph2
1       0                       osd.1   up      1
3       0                       osd.3   up      1
```

从上面 ceph osd tree 的结果里面可以看到这里有两个weight：weight 和 reweight。这篇文章 有详细的分析。简单来说：

* weight

  ：即 osd crush weight，表示设备\(device\) 容量的相对值，比如如果1TB对应1.00，那么 500MB 对应 0.50。bucket weight 是所有 item weight 之和，item weight 的变化会影响 bucket weight 的变化，也就是 osd.X 会影响host。 对于 straw bucket，如果 item weight 为0，则 item straw 也为0，当CRUSH 算法在 bucket 选择 item 时，也就不太可能选中该 item。

* reweight

  ：取值为0~1。osd reweight 并不会影响 host。当 osd 被踢出集群（out）时，osd weight 被设置0，加入集群时，设置为1。它会参与 CRUSH 创建 PG 的过程。CRUSH在选择 OSD 时，如果发现 weight 为0，就跳过该 OSD。

因此，问题的症结就在于 osd crush weight 为0。至于为什么会这样，以及该值对 PG 分配的影响，有待进一步查明。

1.3）解决办法：修改 osd crush weight

```text
ceph osd crush reweight osd.0 1
ceph osd crush reweight osd.1 1
ceph osd crush reweight osd.2 1
ceph osd crush reweight osd.3 1
```

修改后，集群就回到了 HEALTH\_OK 状态。

**注意：**修改 OSD 的 crush weight 会带来部分 PG 之间的数据移动，这可能会影响集群的性能，因此在生产环境中使用要小心。你可以参考[这篇文章](https://ceph.com/planet/ceph-osd-reweight/)来看数据移动的情况。

**2）修改 CRUSH tunables（可调参数）**  
2.1 ）问题描述  
将 osd.1 设置为 out 后，集群并没有开始做 recovery，部分 PG 保持在 remapped 状态：

```text
[root@ceph1]# ceph -s
    cluster 5ccdcb2d-961d-4dcb-a9ed-e8034c56cf71
     health HEALTH_WARN 88 pgs stuck unclean
     monmap e2: 1 mons at {ceph1=192.168.56.102:6789/0}, election epoch 1, quorum 0 ceph1
     osdmap e71: 4 osds: 4 up, 3 in
      pgmap v442: 256 pgs, 4 pools, 285 MB data, 8 objects
            690 MB used, 14636 MB / 15326 MB avail
                  88 active+remapped
                 168 active+clean
```

2.2）原因分析

-&gt; 查看 ceph health detail

```text
[root@ceph1]# ceph health detail
HEALTH_WARN 88 pgs stuck unclean
pg 1.23 is stuck unclean for 337.342290, current state active+remapped, last acting [0,1]
pg 0.1f is stuck unclean for 336.838743, current state active+remapped, last acting [0,1]
pg 1.1f is stuck unclean for 337.355851, current state active+remapped, last acting [0,1]
```

Remapped（重映射）：当 PG 的 acting set 变化后，数据将会从旧 acting set 迁移到新 action set。新主 OSD 需要过一段时间后才能提供服务。因此，它会让老的主 OSD 继续提供服务，直到 PG 迁移完成。数据迁移完成后，PG map 将使用新 acting set 中的主OSD。

以 PG 为例，比较在 osd.1 out 前后的 PG map：

```text
state           state_stamp                     v       reported        up      up_primary      acting      acting_primary
active+clean    2016-06-03 00:31:44.220896      0'0     57:74           [0,1]    0              [0,1]       0               #osd.1 out 之前
active+remapped 2016-06-03 00:47:12.703537      0'0     71:109          [0]      0              [0,1]       0               #osd.1 out 之后
```

2.3）解决办法  
办法一：将 cursh tunables 设置为 optimal

-&gt; 从[这篇文章](http://bbs.ceph.org.cn/question/125)中获得线索，这可能和 crush tunables 有关系。它的默认值应该是 legacy，运行下面的命令将其修改为 optimal 后，集群状态回到正常。

```text
ceph osd crush tunables optimal
```

-&gt; 继续找原因，Red Hat[这篇文章](https://access.redhat.com/documentation/en/red-hat-ceph-storage/1.3/storage-strategies/chapter-9-crush-tunables)给出了一些线索。

在新版本的Ceph 集群中使用 legacy 值可能会有一些问题，包括：

* 当叶子bucket（往往是 host）所拥有的设备数目很小时，一些 PG 被映射到的 OSD 数目少于存储池的size。这在 host 节点的 OSD 数目为 1-3 时较为常见。
* 大型集群中，小部分的 PG 被映射到的 OSD 数目小于规定的数目。这在 CRUSH 层级结构中好几层（比如 row，rack，host，osd 等）时比较常见。
* 当一些 OSD 被标记为 out 时，重新分布的数据会更多地在附近的 OSD 上而不是整个层级结构中。

而第一种情况正是我的测试集群所遇到的情况，每个 host 拥有的 OSD 数目在3个以内，然后部分 PG 所在的 OSD 数目较 replica 少一些。

办法二：将 OSD 的 reweight 修改为 0 而不是使用 out 命令  
Ceph 官方的[这篇文章](http://docs.ceph.com/docs/jewel/rados/operations/add-or-rm-osds/)给出了另一个思路。它认为在主机数目很小的集群中，当一个 OSD 被 out 后，部分 PG 限于 active+remapped 状态是经常出现的。解决办法是先运行 ceph osd in {osd-num} 将集群状态恢复到初始状态，然后运行 ceph osd crush reweight osd.{osd-num} 0 来将这个 osd 的 crush weight 修改为 0，然后集群会开始数据迁移。对小集群来说，reweight 命令甚至更好些。

当集群中 PG 限于 active + remapped 状态时，可以通过 reweight 命令来使得集群恢复正常。当往集群中新加入 OSD 时，为了减少数据移动对集群性能的影响，Ceph 官方建议逐渐地增加 OSD 的 crush weight，比如起始值为0，先设置为 0.2，等数据迁移结束，再设置为 0.4，依此类推，逐渐增加为 0.6,0.8 和 1 甚至更高。在要停用一个 OSD 时，建议采用相反操作，逐渐减少 OSD 的 crush weight 直至 0.

**3）修改 CRUSH ruleset**  
3.1）问题描述  
继续将跟 osd.1 在同意个host 上的 osd.3 out，看看 Ceph 集群能不能继续恢复。Ceph 集群中部分 PG 再次进入 remapped 状态：

```text
[root@ceph1:~]# ceph -s
    cluster 5ccdcb2d-961d-4dcb-a9ed-e8034c56cf71
     health HEALTH_WARN 256 pgs stuck unclean
     monmap e2: 1 mons at {ceph1=192.168.56.102:6789/0}, election epoch 1, quorum 0 ceph1
     osdmap e77: 4 osds: 4 up, 2 in
      pgmap v480: 256 pgs, 4 pools, 285 MB data, 8 objects
            625 MB used, 9592 MB / 10217 MB avail
                 256 active+remapped
```

运行 ceph pg 1.0 query 查看 PG 1.0 的状态：

```text
"recovery_state": [
        { "name": "Started\/Primary\/Active",
          "enter_time": "2016-06-03 01:31:22.045434",
          "might_have_unfound": [],
          "recovery_progress": { "backfill_targets": [],
              "waiting_on_backfill": [],
              "last_backfill_started": "0\/\/0\/\/-1",
              "backfill_info": { "begin": "0\/\/0\/\/-1",
                  "end": "0\/\/0\/\/-1",
                  "objects": []},
              "peer_backfill_info": [],
              "backfills_in_flight": [],
              "recovering": [],
              "pg_backend": { "pull_from_peer": [],
                  "pushing": []}},
          "scrub": { "scrubber.epoch_start": "0",
              "scrubber.active": 0,
              "scrubber.block_writes": 0,
              "scrubber.finalizing": 0,
              "scrubber.waiting_on": 0,
              "scrubber.waiting_on_whom": []}},
        { "name": "Started",
          "enter_time": "2016-06-03 01:31:20.976290"}],
```

可见它已经开始 recovery 了，但是没完成。

3.2）原因分析  
PG 的分布和 CRUSH ruleset 有关。我的集群当前只有一个默认的 ruleset：

```text
[root@ceph1:~]# ceph osd crush rule dump
[
    { "rule_id": 0,
      "rule_name": "replicated_ruleset",
      "ruleset": 0,
      "type": 1,
      "min_size": 1,
      "max_size": 10,
      "steps": [
            { "op": "take",
              "item": -1,
              "item_name": "default"},
            { "op": "chooseleaf_firstn",
              "num": 0,
              "type": "host"},
            { "op": "emit"}]}]
```

注意其 type 为 “host”，也就是说 CRUSH 不会为一个 PG 选择在同一个 host 上的两个 OSD。而我的环境中，目前只有 ceph1 上的两个 OSD 是in 的，因此，CRUSH 无法为所有的 PG 重新选择一个新的 OSD 来替代 osd.3.

3.3）解决办法  
按照以下步骤，将 CRUSH ruleset 的 type 由 “host” 修改为 “osd”，使得 CRUSH 为 PG 选择 OSD 时不再局限于不同的 host。

```text
[root@ceph1:~]# ceph osd getcrushmap -o crushmap_compiled_file
got crush map from osdmap epoch 77
[root@ceph1:~]# crushtool -d crushmap_compiled_file -o crushmap_decompiled_file
[root@ceph1:~]# vi crushmap_decompiled_file
rule replicated_ruleset {
        ruleset 0
        type replicated
        min_size 1
        max_size 10
        step take default
        step chooseleaf firstn 0 type osd #将 type 由 “host” 修改为 “osd”
        step emit
}

[root@ceph1:~]# crushtool -c crushmap_decompiled_file -o newcrushmap
[root@ceph1:~]# ceph osd setcrushmap -i newcrushmap
set crush map
```

以上命令执行完毕后，可以看到 recovery 过程继续进行，一段时间后，集群恢复 OK 状态。

```text
[root@ceph1:~]# ceph -s
    cluster 5ccdcb2d-961d-4dcb-a9ed-e8034c56cf71
     health HEALTH_WARN 256 pgs stuck unclean
     monmap e2: 1 mons at {ceph1=192.168.56.102:6789/0}, election epoch 1, quorum 0 ceph1
     osdmap e80: 4 osds: 4 up, 2 in
      pgmap v493: 256 pgs, 4 pools, 285 MB data, 8 objects
            552 MB used, 9665 MB / 10217 MB avail
                 256 active+remapped
[root@ceph1:~]# ceph -s
    cluster 5ccdcb2d-961d-4dcb-a9ed-e8034c56cf71
     health HEALTH_WARN 137 pgs stuck unclean
     monmap e2: 1 mons at {ceph1=192.168.56.102:6789/0}, election epoch 1, quorum 0 ceph1
     osdmap e80: 4 osds: 4 up, 2 in
      pgmap v494: 256 pgs, 4 pools, 285 MB data, 8 objects
            677 MB used, 9540 MB / 10217 MB avail
                 137 active+remapped
                 119 active+clean
recovery io 34977 B/s, 0 objects/s
[root@ceph1:~]# ceph -s
    cluster 5ccdcb2d-961d-4dcb-a9ed-e8034c56cf71
     health HEALTH_OK
     monmap e2: 1 mons at {ceph1=192.168.56.102:6789/0}, election epoch 1, quorum 0 ceph1
     osdmap e80: 4 osds: 4 up, 2 in
      pgmap v495: 256 pgs, 4 pools, 285 MB data, 8 objects
            679 MB used, 9538 MB / 10217 MB avail
                 256 active+clean
recovery io 18499 kB/s, 0 objects/s
```

**4）将一个 OSD 移出集群**

4.11）将该 osd 设置为 out

```text
[root@ceph1:/home/s1]# ceph osd out osd.1
marked out osd.1.
```

4.2）集群做 recovery

```text
2017-06-03 01:54:21.596632 mon.0 [INF] osdmap e90: 4 osds: 4 up, 3 in
2017-06-03 01:54:21.608675 mon.0 [INF] pgmap v565: 256 pgs: 256 active+clean; 1422 MB data, 2833 MB used, 12493 MB / 15326 MB avail
2017-06-03 01:54:26.352909 mon.0 [INF] pgmap v566: 256 pgs: 1 active, 255 active+clean; 1422 MB data, 2979 MB used, 12347 MB / 15326 MB avail; 2/40 objects degraded (5.000%); 51033 B/s, 0 objects/s recovering
2017-06-03 01:54:28.624334 mon.0 [INF] pgmap v567: 256 pgs: 4 active, 252 active+clean; 1422 MB data, 3427 MB used, 11899 MB / 15326 MB avail; 8/40 objects degraded (20.000%); 51053 B/s, 0 objects/s recovering
2017-06-03 01:54:31.320973 mon.0 [INF] pgmap v568: 256 pgs: 3 active, 253 active+clean; 1422 MB data, 3539 MB used, 11787 MB / 15326 MB avail; 6/40 objects degraded (15.000%); 19414 kB/s, 0 objects/s recovering
2017-06-03 01:54:32.323443 mon.0 [INF] pgmap v569: 256 pgs: 256 active+clean; 1422 MB data, 3730 MB used, 11595 MB / 15326 MB avail; 77801 kB/s, 0 objects/s recovering
2017-06-03 01:56:10.949077 mon.0 [INF] pgmap v570: 256 pgs: 256 active+clean; 1422 MB data, 3730 MB used, 11595 MB / 15326 MB avail
```

4.3）完成后，该 osd 的状态还是 up，表示它的服务还在运行。现在将其服务停掉。

```text
[root@ceph1:/home/s1]# ssh ceph2 service ceph stop osd.1
/etc/init.d/ceph: osd.1 not found (/etc/ceph/ceph.conf defines , /var/lib/ceph defines )
```

该命令出错，需要将 osd.1 加入 ceph.conf 中。在 ceph1 上的 ceph.conf 中添加：

```text
[osd]

[osd.1]
host = ceph2

[osd.2]
host = ceph1

[osd.3]
host = ceph2

[osd.0]
host = ceph1
```

然后运行 ceph-deploy –overwrite-conf config push ceph2 将它拷贝到 ceph2 上。重启所有的 osd 服务。诡异的事情出现了：

```text
[root@ceph1:/etc/ceph]# ceph osd tree
# id    weight  type name       up/down reweight
-1      4       root default
-2      4               host ceph1
0       1                       osd.0   up      1
2       1                       osd.2   up      1
1       1                       osd.1   up      0
3       1                       osd.3   up      1
-3      0               host ceph2
```

osd.1 和 osd.3 跑到了 ceph1 节点上！查看 start 命令，它将 curshmap 中的 osd.1 的 host 修改为了 ceph2：

```text
[root@ceph1:/etc/ceph]# /etc/init.d/ceph -a start osd
=== osd.1 ===
df: â€˜/var/lib/ceph/osd/ceph-1/.â€™: No such file or directory
create-or-move updating item name 'osd.1' weight 1 at location {host=ceph1,root=default} to crush map
Starting Ceph osd.1 on ceph2...
starting osd.1 at :/0 osd_data /var/lib/ceph/osd/ceph-1 /var/lib/ceph/osd/ceph-1/journal
```

从 这篇文章 可以看出，这其实是Ceph的一个 bug：make osd crush placement on startup handle multiple trees \(e.g., ssd + sas\)。该bug 在 OSD location reset after restart 中也有讨论。目前 Ceph 没有机制可以确保 CRUSH map 结构不变，最简单的办法是在 ceph.conf 中 \[OSD\] 部分设置 osd crush update on start = false。

尝试手工挪动 osd.1 和 osd.3：

```text
[root@ceph1:/etc/ceph]# ceph osd crush remove osd.1
removed item id 1 name 'osd.1' from crush map
[root@ceph1:/etc/ceph]# ceph osd crush remove osd.3
removed item id 3 name 'osd.3' from crush map

[root@ceph1:/etc/ceph]# ceph osd tree
# id    weight  type name       up/down reweight
-1      2       root default
-2      2               host ceph1
0       1                       osd.0   up      1
2       1                       osd.2   up      1
-3      0               host ceph2
1       0       osd.1   up      0
3       0       osd.3   up      1

[root@ceph1:/etc/ceph]# ceph osd crush set 1 1 root=default host=ceph2
Error ENOENT: unable to set item id 1 name 'osd.1' weight 1 at location {host=ceph2,root=default}: does not exist
```

该错误的原因待查。索性直接修改 crush map，然后正确的结果就回来了：

```text
[root@ceph1:/etc/ceph]# ceph osd tree
# id    weight  type name       up/down reweight
-1      2       root default
-2      2               host ceph1
0       1                       osd.0   up      1
2       1                       osd.2   up      1
-3      0               host ceph2
1       1                       osd.1   up      0
3       1                       osd.3   up      1
```

继续运行命令 ssh ceph2 /etc/init.d/ceph stop osd.1 去停止 osd.1 的服务，但是无法停止。据说是因为用 ceph-deploy 部署的 OSD 的服务都没法停止。只能想办法把进程杀掉了。

然后继续执行：

```text
[root@ceph1:/etc/ceph]# ceph osd crush remove osd.1
removed item id 1 name 'osd.1' from crush map
[root@ceph1:/etc/ceph]# ceph auth del osd.1
updated
[root@ceph1:/etc/init]# ceph osd rm osd.1
removed osd.1
```

此时，osd tree 中再也没有 osd.1 了：

```text
[root@ceph1:/etc/ceph]# ceph osd tree
# id    weight  type name       up/down reweight
-1      3       root default
-2      2               host ceph1
0       1                       osd.0   up      1
2       1                       osd.2   up      1
-3      1               host ceph2
3       1                       osd.3   up      1
```

**5）将一个 OSD 加入集群**

* /dev/sdb1 分区删除
* 清理磁盘：ceph-deploy disk zap ceph2:/dev/sdb
* 创建 OSD：ceph-deploy osd create ceph2:sdb:/dev/sdd1

结果OSD就回来了：

```text
[root@ceph1:~]# ceph-deploy osd create ceph2:sdb:/dev/sdd1c^C
[root@ceph1:~]# ceph osd tree
# id    weight  type name       up/down reweight
-1      2       root default
-2      2               host ceph1
0       1                       osd.0   up      1
2       1                       osd.2   up      1
-3      0               host ceph2
4       0                       osd.4   up      1
1       0                       osd.1   up      1
```

其实将上面第四步和第五步合并在一起，就是替换一个故障磁盘的过程。

**6）在特定 OSD 上创建存储池**  
假设 osd.0 和 osd.2 的磁盘是 SSD 磁盘，osd.1 和 osd.4 的磁盘是 SATA 磁盘。我们将创建两个pool：pool-ssd 和 pool-sata，并确保 pool-ssd 中的对象都保存在 osd.0 和 osd.2 上，pool-sata 中的对象都保存在 osd.1 和 osd.4 上。

6.1）修改 CRUSH map

```text
[root@ceph1:~]# ceph osd getcrushmap -o crushmapdump
got crush map from osdmap epoch 124
[root@ceph1:~]# crushtool -d crushmapdump -o crushmapdump-decompiled
[root@ceph1:~]# vi crushmapdump-decompiled
[root@ceph1:~]# crushtool -c crushmapdump-decompiled -o crushmapdump-compiled
[root@ceph1:~]# ceph osd setcrushmap -i crushmapdump-compiled
```

在 crushmapdump-decompiled 文件中添加如下内容：

```text
root ssd {
        id -5
        alg straw
        hash 0
        item osd.0 weight 1
        item osd.2 weight 1
}

root sata {
        id -6
        alg straw
        hash 0
        item osd.1 weight 1
        item osd.4 weight 1
}

# rules
...

rule ssd-pool {
        ruleset 1
        type replicated
        min_size 1
        max_size 10
        step take ssd
        step chooseleaf firstn 0 type osd
        step emit
}

rule sata-pool {
        ruleset 2
        type replicated
        min_size 1
        max_size 10
        step take sata
        step chooseleaf firstn 0 type osd
        step emit
}
```

6.2） ceph osd tree 如下：

```text
[root@ceph1:~]# ceph osd tree
# id    weight  type name       up/down reweight
-6      2       root sata
1       1               osd.1   up      1
4       1               osd.4   up      1
-5      2       root ssd
0       1               osd.0   up      1
2       1               osd.2   up      1
-1      2       root default
-2      2               host ceph1
0       1                       osd.0   up      1
2       1                       osd.2   up      1
-3      0               host ceph2
4       0                       osd.4   up      1
1       0                       osd.1   up      1
```

6.3）创建 ssd-pool，其默认的 ruleset 为 0：

```text
[root@ceph1:~]# ceph osd pool create ssd-pool 8 8
pool 'ssd-pool' created
root@ceph1:~# ceph osd dump | grep -i ssd
pool 4 'ssd-pool' replicated size 2 min_size 1 crush_ruleset 0 object_hash rjenkins pg_num 8 pgp_num 8 last_change 126 flags hashpspool stripe_width 0
```

6.4）修改 ssd-pool 的 ruleset 为 ssd-pool 其id 为 1：

```text
[root@ceph1:~]# ceph osd pool set ssd-pool crush_ruleset 1
set pool 4 crush_ruleset to 1
[root@ceph1:~]# ceph osd dump | grep -i ssd
pool 4 'ssd-pool' replicated size 2 min_size 1 crush_ruleset 1 object_hash rjenkins pg_num 8 pgp_num 8 last_change 128 flags hashpspool stripe_width 0
```

6.5）类似地创建 sata-pool 并设置其 cursh ruleset 为 sata-pool 其id 为 2：

```text
[root@ceph1:~]# ceph osd pool create sata-pool 8 8
pool 'sata-pool' created
[root@ceph1:~]# ceph osd pool set sata-pool crush_ruleset 2
set pool 5 crush_ruleset to 2
[root@ceph1:~]# ceph osd dump | grep -i sata
pool 5 'sata-pool' replicated size 2 min_size 1 crush_ruleset 2 object_hash rjenkins pg_num 8 pgp_num 8 last_change 131 flags hashpspool stripe_width 0
```

6.6）分别放一个文件进这两个pool：

```text
[root@ceph1:/home/s1]# rados -p ssd-pool put root-id_rsa root-id_rsa
[root@ceph1:/home/s1]# rados -p sata-pool put root-id_rsa root-id_rsa
[root@ceph1:/home/s1]# rados -p ssd-pool ls
root-id_rsa
[root@ceph1:/home/s1]# rados -p sata-pool ls
root-id_rsa
```

6.7）查看对象所在的 OSD

```text
[root@ceph1:/home/s1]# ceph osd map ssd-pool root-id_rsa
osdmap e132 pool 'ssd-pool' (4) object 'root-id_rsa' -> pg 4.38e001ef (4.7) -> up ([2,0], p2) acting ([2,0], p2)
[root@ceph1:/home/s1]# ceph osd map sata-pool root-id_rsa
osdmap e132 pool 'sata-pool' (5) object 'root-id_rsa' -> pg 5.38e001ef (5.7) -> up ([4,1], p4) acting ([4,1], p4)
```

可见，两个pool各自在ssd 和 sata 磁盘上。

