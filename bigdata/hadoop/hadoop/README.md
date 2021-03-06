# Hadoop



```text
[root@hadoop ~]# tree -L 3 hadoop-3.2.1
hadoop-3.2.1
├── bin
│   ├── container-executor
│   ├── hadoop
│   ├── hadoop.cmd
│   ├── hdfs
│   ├── hdfs.cmd
│   ├── mapred
│   ├── mapred.cmd
│   ├── oom-listener
│   ├── test-container-executor
│   ├── yarn
│   └── yarn.cmd
├── etc
│   └── hadoop
│       ├── capacity-scheduler.xml
│       ├── configuration.xsl
│       ├── container-executor.cfg
│       ├── core-site.xml
│       ├── hadoop-env.cmd
│       ├── hadoop-env.sh
│       ├── hadoop-metrics2.properties
│       ├── hadoop-policy.xml
│       ├── hadoop-user-functions.sh.example
│       ├── hdfs-site.xml
│       ├── httpfs-env.sh
│       ├── httpfs-log4j.properties
│       ├── httpfs-signature.secret
│       ├── httpfs-site.xml
│       ├── kms-acls.xml
│       ├── kms-env.sh
│       ├── kms-log4j.properties
│       ├── kms-site.xml
│       ├── log4j.properties
│       ├── mapred-env.cmd
│       ├── mapred-env.sh
│       ├── mapred-queues.xml.template
│       ├── mapred-site.xml
│       ├── shellprofile.d
│       ├── ssl-client.xml.example
│       ├── ssl-server.xml.example
│       ├── user_ec_policies.xml.template
│       ├── workers
│       ├── yarn-env.cmd
│       ├── yarn-env.sh
│       ├── yarnservice-log4j.properties
│       └── yarn-site.xml
├── sbin
│   ├── distribute-exclude.sh
│   ├── FederationStateStore
│   │   ├── MySQL
│   │   └── SQLServer
│   ├── hadoop-daemon.sh
│   ├── hadoop-daemons.sh
│   ├── httpfs.sh
│   ├── kms.sh
│   ├── mr-jobhistory-daemon.sh
│   ├── refresh-namenodes.sh
│   ├── start-all.cmd
│   ├── start-all.sh
│   ├── start-balancer.sh
│   ├── start-dfs.cmd
│   ├── start-dfs.sh
│   ├── start-secure-dns.sh
│   ├── start-yarn.cmd
│   ├── start-yarn.sh
│   ├── stop-all.cmd
│   ├── stop-all.sh
│   ├── stop-balancer.sh
│   ├── stop-dfs.cmd
│   ├── stop-dfs.sh
│   ├── stop-secure-dns.sh
│   ├── stop-yarn.cmd
│   ├── stop-yarn.sh
│   ├── workers.sh
│   ├── yarn-daemon.sh
│   └── yarn-daemons.sh
```

Hadoop部署方式：

* 单机模式
* 伪分布模式
  * 即在一台服务器上运行Hadoop（如果是分布式模式，则首先要配置Master主节点，其次配置Slave从节点）
* 完全分布式模式

