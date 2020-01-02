# Cluster

Linux Cluster：

```text
Cluster：计算机集合，为解决某个特定问题组合起来形成的单个系统；

Linux Cluster类型：
    LB：Load Balancing，负载均衡；
    HA：High Availiablity，高可用；
        A=MTBF/（MTBF+MTTR）
            (0,1)：90%, 95%, 99%, 99.5%,  99.9%, 99.99%, 99.999%
    HP：High Performance，高性能；

        www.top500.org

系统扩展方式：
    Scale UP：向上扩展
    Scale Out：向外扩展
        Cluster 
```

LB Cluster：

```text
LB Cluster的实现：
    硬件：
        F5 Big-IP
        Citrix Netscaler
        A10 A10
    软件：
        lvs：Linux Virtual Server
        nginx
        haproxy
        ats：apache traffic server 
        perlbal
        pound

    基于工作的协议层次划分：
        传输层（通用）：（DPORT）
            lvs：
            nginx：（stream）
            haproxy：（mode tcp）
        应用层（专用）：（自定义的请求模型分类）
            proxy sferver：
                http：nginx, httpd, haproxy(mode http), ...
                fastcgi：nginx, httpd, ...
                mysql：mysql-proxy, ...
                ...

        站点指标：
            PV：Page View
            UV：Unique Vistor
            IP：

    会话保持：
        (1) session sticky
            Source IP
            Cookie
        (2) session replication; 
            session cluster
        (3) session server

lvs：Linux Virtual Server 
    VS: Virtual Server
    RS: Real Server

    作者：章文嵩；alibaba --> didi

    l4：四层路由器，四层交换机；
        VS：根据请求报文的目标IP和目标协议及端口将其调度转发至某RealServer，根据调度算法来挑选RS；

    iptables/netfilter：
        iptables：用户空间的管理工具；
        netfilter：内核空间上的框架；
            流入：PREROUTING --> INPUT 
            流出：OUTPUT --> POSTROUTING
            转发：PREROUTING --> FORWARD --> POSTROUTING

        DNAT：目标地址转换； PREROUTING；

    lvs: ipvsadm/ipvs
        ipvsadm：用户空间的命令行工具，规则管理器，用于管理集群服务及RealServer；
        ipvs：工作于内核空间的netfilter的INPUT钩子之上的框架；

    lvs集群类型中的术语：
        vs：Virtual Server, Director, Dispatcher, Balancer
        rs：Real Server, upstream server, backend server

        CIP：Client IP, VIP: Virtual serve IP, RIP: Real server IP, DIP: Director IP

        CIP <--> VIP == DIP <--> RIP 

    lvs集群的类型：
        lvs-nat：修改请求报文的目标IP
        lvs-dr：操纵封装新的MAC地址；
        lvs-tun：在原请求IP报文之外新加一个IP首部；
        lvs-fullnat：修改请求报文的源和目标IP；

        lvs-nat：
            多目标IP的DNAT，通过将请求报文中的目标地址和目标端口修改为某挑出的RS的RIP和PORT实现转发；

            （1）RIP和DIP必须在同一个IP网络，且应该使用私网地址；RS的网关要指向DIP；
            （2）请求报文和响应报文都必须经由Director转发；Director易于成为系统瓶颈；
            （3）支持端口映射，可修改请求报文的目标PORT；
            （4）vs必须是Linux系统，rs可以是任意系统；

        lvs-dr：
            Direct Routing，直接路由；

            通过为请求报文重新封装一个MAC首部进行转发，源MAC是DIP所在的接口的MAC，目标MAC是某挑选出的RS的RIP所在接口的MAC地址；源IP/PORT，以及目标IP/PORT均保持不变；

            Director和各RS都得配置使用VIP；

            (1) 确保前端路由器将目标IP为VIP的请求报文发往Director：
                (a) 在前端网关做静态绑定；
                (b) 在RS上使用arptables；
                (c) 在RS上修改内核参数以限制arp通告及应答级别；
                    arp_announce
                    arp_ignore
            (2) RS的RIP可以使用私网地址，也可以是公网地址；RIP与DIP在同一IP网络；RIP的网关不能指向DIP，以确保响应报文不会经由Director；
            (3) RS跟Director要在同一个物理网络；
            (4) 请求报文要经由Director，但响应不能经由Director，而是由RS直接发往Client；
            (5) 不支持端口映射；

        lvs-tun：
            转发方式：不修改请求报文的IP首部（源IP为CIP，目标IP为VIP），而在原IP报文之外再封装一个IP首部（源IP是DIP，目标IP是RIP），将报文发往挑选出的目标RS；

            (1) DIP, VIP, RIP都应该是公网地址；
            (2) RS的网关不能，也不可能指向DIP；
            (3) 请求报文要经由Director，但响应不能经由Director；
            (4) 不支持端口映射；
            (5) RS的OS得支持隧道功能；

        lvs-fullnat：
            通过同时修改请求报文的源IP地址和目标IP地址进行转发；
                CIP --> DIP 
                VIP --> RIP 

            (1) VIP是公网地址，RIP和DIP是私网地址，且通常不在同一IP网络；因此，RIP的网关一般不会指向DIP；
            (2) RS收到的请求报文源地址是DIP，因此，只需响应给DIP；但Director还要将其发往Client；
            (3) 请求和响应报文都经由Director；
            (4) 支持端口映射；

            注意：此类型默认不支持；
```

回顾： lvs类型： lvs-nat：通过修改请求报文的目标IP地址完成调度并转发；请求和响应报文都经由Director； lvs-dr：通过直接封装新的MAC首部完成调度并转发；仅请求报文经由Director； lvs-tun：通过在原IP报文首部之外新封装一个IP首部完成调度并转发；仅请求报文经由Director； lvs-fullnat：通过修改请求报文的目标IP地址和源IP地址完成调度并转发；请求和响应报文都经由Director；

```text
会话保持：
    session sticky, session replication, session server；

lvs:
    ipvsadm/ipvs 
```

ipvs（2）

```text
ipvs scheduler：
    根据其调度时是否考虑各RS当前的负载状态，可分为静态方法和动态方法两种：

        静态方法：仅根据算法本身进行调度；
            RR：roundrobin，轮询；
            WRR：Weighted RR，加权轮询；
            SH：Source Hashing，实现session sticy，源IP地址hash；将来自于同一个IP地址的请求始终发往第一次挑中的RS，从而实现会话绑定；
            DH：Destination Hashing；目标地址哈希，将发往同一个目标地址的请求始终转发至第一次挑中的RS；

        动态方法：主要根据每RS当前的负载状态及调度算法进行调度；
            Overhead=

            LC：least connections
                Overhead=activeconns*256+inactiveconns
            WLC：Weighted LC
                Overhead=(activeconns*256+inactiveconns)/weight
            SED：Shortest Expection Delay
                Overhead=(activeconns+1)*256/weight
            NQ：Never Queue

            LBLC：Locality-Based LC，动态的DH算法；
            LBLCR：LBLC with Replication，带复制功能的LBLC；

ipvsadm/ipvs：
    ipvs：
        ~]# grep -i -C 10 "ipvs" /boot/config-3.10.0-327.el7.x86_64

            支持的协议：TCP， UDP， AH， ESP， AH_ESP, SCTP；

        ipvs集群：
            集群服务
            服务上的RS

    ipvsadm命令：
        ipvsadm -A|E -t|u|f service-address [-s scheduler] [-p [timeout]] [-M netmask] [--pe persistence_engine] [-b sched-flags]
        ipvsadm -D -t|u|f service-address
        ipvsadm -C
        ipvsadm -R
        ipvsadm -S [-n]
        ipvsadm -a|e -t|u|f service-address -r server-address [options]
        ipvsadm -d -t|u|f service-address -r server-address
        ipvsadm -L|l [options]
        ipvsadm -Z [-t|u|f service-address]

        管理集群服务：增、改、删；
            增、改：
                ipvsadm -A|E -t|u|f service-address [-s scheduler] [-p [timeout]]

            删：
                ipvsadm -D -t|u|f service-address

            service-address：
                -t|u|f：
                    -t: TCP协议的端口，VIP:TCP_PORT
                    -u: TCP协议的端口，VIP:UDP_PORT
                    -f：firewall MARK，是一个数字；

            [-s scheduler]：指定集群的调度算法，默认为wlc；

        管理集群上的RS：增、改、删；
            增、改：
                ipvsadm -a|e -t|u|f service-address -r server-address [-g|i|m] [-w weight]

            删：
                ipvsadm -d -t|u|f service-address -r server-address

            server-address：
                rip[:port]

            选项：
                lvs类型：
                    -g: gateway, dr类型
                    -i: ipip, tun类型
                    -m: masquerade, nat类型

                -w weight：权重；

        清空定义的所有内容：
            ipvsadm -C

        查看：
            ipvsadm -L|l [options]
                 --numeric, -n：numeric output of addresses and ports 
                 --exact：expand numbers (display exact values)

                 --connection， -c：output of current IPVS connections
                 --stats：output of statistics information
                 --rate ：output of rate information

        保存和重载：
            ipvsadm -S = ipvsadm-save
            ipvsadm -R = ipvsadm-restore 

负载均衡集群的设计要点：
    (1) 是否需要会话保持；
    (2) 是否需要共享存储；
        共享存储：NAS， SAN， DS（分布式存储）
        数据同步：
            课外作业：rsync+inotify实现数据同步 

    lvs-nat：

        设计要点：
            (1) RIP与DIP在同一IP网络, RIP的网关要指向DIP；
            (2) 支持端口映射；

        实践作业（博客）：负载均衡一个php应用；
            测试：(1) 是否需要会话保持；(2) 是否需要共享存储；

    lvs-dr：

        dr模型中，各主机上均需要配置VIP，解决地址冲突的方式有三种：
            (1) 在前端网关做静态绑定；
            (2) 在各RS使用arptables；
            (3) 在各RS修改内核参数，来限制arp响应和通告的级别；
                限制响应级别：arp_ignore
                    0：默认值，表示可使用本地任意接口上配置的任意地址进行响应；
                    1: 仅在请求的目标IP配置在本地主机的接收到请求报文接口上时，才给予响应；
                限制通告级别：arp_announce
                    0：默认值，把本机上的所有接口的所有信息向每个接口上的网络进行通告；
                    1：尽量避免向非直接连接网络进行通告；
                    2：必须避免向非本网络通告；


        RS的预配置脚本：
            #!/bin/bash
            #
            vip=10.1.0.5
            mask='255.255.255.255'

            case $1 in
            start)
                echo 1 > /proc/sys/net/ipv4/conf/all/arp_ignore
                echo 1 > /proc/sys/net/ipv4/conf/lo/arp_ignore
                echo 2 > /proc/sys/net/ipv4/conf/all/arp_announce
                echo 2 > /proc/sys/net/ipv4/conf/lo/arp_announce

                ifconfig lo:0 $vip netmask $mask broadcast $vip up
                route add -host $vip dev lo:0
                ;;
            stop)
                ifconfig lo:0 down

                echo 0 > /proc/sys/net/ipv4/conf/all/arp_ignore
                echo 0 > /proc/sys/net/ipv4/conf/lo/arp_ignore
                echo 0 > /proc/sys/net/ipv4/conf/all/arp_announce
                echo 0 > /proc/sys/net/ipv4/conf/lo/arp_announce

                ;;
            *) 
                echo "Usage $(basename $0) start|stop"
                exit 1
                ;;
            esac                    

        VS的配置脚本：
            #!/bin/bash
            #
            vip='10.1.0.5'
            iface='eno16777736:0'
            mask='255.255.255.255'
            port='80'
            rs1='10.1.0.7'
            rs2='10.1.0.8'
            scheduler='wrr'
            type='-g'

            case $1 in
            start)
                ifconfig $iface $vip netmask $mask broadcast $vip up
                iptables -F

                ipvsadm -A -t ${vip}:${port} -s $scheduler
                ipvsadm -a -t ${vip}:${port} -r ${rs1} $type -w 1
                ipvsadm -a -t ${vip}:${port} -r ${rs2} $type -w 1
                ;;
            stop)
                ipvsadm -C
                ifconfig $iface down
                ;;
            *)
                echo "Usage $(basename $0) start|stop"
                exit 1
                ;;
            esac                

        课外扩展作业：vip与dip/rip不在同一网段的实验环境设计及配置实现； 

        博客作业：lvs的详细应用
            讲清楚类型、调度方法；并且给出nat和dr类型的设计拓扑及具体实现；

FWM：FireWall Mark 
    借助于防火墙标记来分类报文，而后基于标记定义集群服务；可将多个不同的应用使用同一个集群服务进行调度；

    打标记方法（在Director主机）：
        # iptables -t mangle -A PREROUTING -d $vip -p $proto --dport $port -j MARK --set-mark NUMBER 

    基于标记定义集群服务：
        # ipvsadm -A -f NUMBER [options]

lvs persistence：持久连接

    持久连接模板：实现无论使用任何算法，在一段时间内，实现将来自同一个地址的请求始终发往同一个RS；

        ipvsadm -A|E -t|u|f service-address [-s scheduler] [-p [timeout]]

    port Affinity：
        每端口持久：每集群服务单独定义，并定义其持久性；
        每防火墙标记持久：基于防火墙标记定义持久的集群服务；可实现将多个端口上的应用统一调度，即所谓的port Affinity；
        每客户端持久：基于0端口定义集群服务，即将客户端对所有应用的请求统统调度至后端主机，而且可使用持久连接进行绑定；

保存及重载规则：
    保存：建议保存至/etc/sysconfig/ipvsadm
        ipvsadm-save > /PATH/TO/IPVSADM_FILE
        ipvsadm -S > /PATH/TO/IPVSADM_FILE
        systemctl stop ipvsadm.service 

    重载：    
        ipvsadm-restore < /PATH/FROM/IPVSADM_FILE
        ipvsadm -R < /PATH/FROM/IPVSADM_FILE
        systemctl restart ipvsadm.service 

考虑：
    (1) Director不可用，整个系统将不可用；SPoF
        解决方案：高可用 
            keepalived 
            heartbeat/corosync
    (2) 某RS不可用时，Director依然会调度请求至此RS；
        解决方案：对各RS的健康状态做检查，失败时禁用，成功时启用；
            keepalived
            heartbeat/corosync, ldirectord
        检测方式：
            (a) 网络层检测；
            (b) 传输层检测，端口探测；
            (c) 应用层检测，请求某关键资源；

            ok --> failure
            failure --> ok
```

HA Cluster：

```text
集群类型：LB、HA、HP

    SPoF: Single Point of Failure

系统可用性的公式：A=MTBF/（MTBF+MTTR）
    (0,1), 95%
    几个9: 99%, ..., 99.999%
        99%： %1, 99.9%， 0.1%

    降低MTTR：冗余（redundant）
        active/passive 
            active --> HEARTBEAT --> passive 

            (1) passive node的数量？
            (2) 资源切换？

        shared storage：
            NAS：文件共享服务器；
            SAN：存储区域网络，块级别的共享；

        Network partition：
            隔离设备：
                node：STONITH
                资源：fence 

        quorum：            
            with quorum： > total/2
            without quorum: <= total/2

        HA Service：
            nginx service：ip/nginx

        TWO nodes Cluster？
            辅助设备：ping node, quorum disk; 

HA Cluster实现方案:
    vrrp协议的实现
        keepalived
    ais：完备HA集群；
        heartbeat
        corosync

keepalived：

    vrrp协议：Virtual Redundant Routing Protocol
        术语：
            虚拟路由器：Virtual Router 
            虚拟路由器标识：VRID(0-255)
            物理路由器：
                master：主设备
                backup：备用设备
                priority：优先级
            VIP：Virtual IP 
            VMAC：Virutal MAC (00-00-5e-00-01-VRID)

        通告：心跳，优先级等；周期性；

        抢占式，非抢占式；

        安全工作：
            认证：
                无认证
                简单字符认证
                MD5
        工作模式：
            主/备：单虚拟路径器；
            主/主：主/备（虚拟路径器1），备/主（虚拟路径器2）

    keepalived:
        vrrp协议的软件实现，原生设计的目的为了高可用ipvs服务：
            vrrp协议完成地址流动；
            为vip地址所在的节点生成ipvs规则（在配置文件中预先定义）；
            为ipvs集群的各RS做健康状态检测；
            基于脚本调用接口通过执行脚本完成脚本中定义的功能，进而影响集群事务；

        组件：
            核心组件：
                vrrp stack
                ipvs wrapper
                checkers
            控制组件：配置文件分析器
            IO复用器
            内存管理组件

    HA Cluster的配置前提：
        (1) 各节点时间必须同步；
            ntp, chrony
        (2) 确保iptables及selinux不会成为阻碍；
        (3) 各节点之间可通过主机名互相通信（对KA并非必须）；
            建议使用/etc/hosts文件实现； 
        (4) 各节点之间的root用户可以基于密钥认证的ssh服务完成互相通信；（并非必须）

    keepalived安装配置：
        CentOS 6.4+    

        程序环境：
            配置文件：/etc/keepalived/keepalived.conf
            主程序：/usr/sbin/keepalived
            Unit File：keepalived.service

        配置文件组件部分：
            TOP HIERACHY
                GLOBAL CONFIGURATION
                    Global definitions
                    Static routes/addresses
                VRRPD CONFIGURATION
                    VRRP synchronization group(s)
                    VRRP instance(s)
                LVS CONFIGURATION
                    Virtual server group(s)
                    Virtual server(s)

        单主配置示例：
            ! Configuration File for keepalived

            global_defs {
                notification_email {
                    root@localhost
                }
                notification_email_from keepalived@localhost
                smtp_server 127.0.0.1
                smtp_connect_timeout 30
                router_id node1
                vrrp_mcast_group4 224.0.100.19
            }

            vrrp_instance VI_1 {
                state BACKUP
                interface eno16777736
                virtual_router_id 14
                priority 98
                advert_int 1
                authentication {
                    auth_type PASS
                    auth_pass 571f97b2
                }
                virtual_ipaddress {
                    10.1.0.91/16 dev eno16777736
                }
            }            


        配置语法：
            配置虚拟路由器：
                vrrp_instance <STRING> {
                    ....
                }

                专用参数：
                     state MASTER|BACKUP：当前节点在此虚拟路由器上的初始状态；只能有一个是MASTER，余下的都应该为BACKUP；
                     interface IFACE_NAME：绑定为当前虚拟路由器使用的物理接口；
                     virtual_router_id VRID：当前虚拟路由器的惟一标识，范围是0-255；
                     priority 100：当前主机在此虚拟路径器中的优先级；范围1-254；
                    advert_int 1：vrrp通告的时间间隔；
                    authentication {
                        auth_type AH|PASS
                        auth_pass <PASSWORD>
                    }
                    virtual_ipaddress {
                        <IPADDR>/<MASK> brd <IPADDR> dev <STRING> scope <SCOPE> label <LABEL>
                        192.168.200.17/24 dev eth1
                        192.168.200.18/24 dev eth2 label eth2:1
                    }
                     track_interface {
                        eth0
                        eth1
                        ...
                    }
                    配置要监控的网络接口，一旦接口出现故障，则转为FAULT状态；
                    nopreempt：定义工作模式为非抢占模式；
                    preempt_delay 300：抢占式模式下，节点上线后触发新选举操作的延迟时长；

                    定义通知脚本：
                        notify_master <STRING>|<QUOTED-STRING>：当前节点成为主节点时触发的脚本；
                        notify_backup <STRING>|<QUOTED-STRING>：当前节点转为备节点时触发的脚本；
                        notify_fault <STRING>|<QUOTED-STRING>：当前节点转为“失败”状态时触发的脚本；

                        notify <STRING>|<QUOTED-STRING>：通用格式的通知触发机制，一个脚本可完成以上三种状态的转换时的通知；

        双主模型示例：            
            ! Configuration File for keepalived

            global_defs {
                notification_email {
                    root@localhost
                }
                notification_email_from keepalived@localhost
                smtp_server 127.0.0.1
                smtp_connect_timeout 30
                router_id node1
                vrrp_mcast_group4 224.0.100.19
            }

            vrrp_instance VI_1 {
                state MASTER
                interface eno16777736
                virtual_router_id 14
                priority 100
                advert_int 1
                authentication {
                    auth_type PASS
                    auth_pass 571f97b2
                }
                virtual_ipaddress {
                    10.1.0.91/16 dev eno16777736
                }
            }

            vrrp_instance VI_2 {
                state BACKUP
                interface eno16777736
                virtual_router_id 15
                priority 98
                advert_int 1
                authentication {
                    auth_type PASS
                    auth_pass 578f07b2
                }
                virtual_ipaddress {
                    10.1.0.92/16 dev eno16777736
                }
            }                        



        示例通知脚本：
            #!/bin/bash
            #
            contact='root@localhost'

            notify() {
                mailsubject="$(hostname) to be $1, vip floating"
                mailbody="$(date +'%F %T'): vrrp transition, $(hostname) changed to be $1"
                echo "$mailbody" | mail -s "$mailsubject" $contact
            }

            case $1 in
            master)
                notify master
                ;;
            backup)
                notify backup
                ;;
            fault)
                notify fault
                ;;
            *)
                echo "Usage: $(basename $0) {master|backup|fault}"
                exit 1
                ;;
            esac            

        脚本的调用方法：
            notify_master "/etc/keepalived/notify.sh master"
            notify_backup "/etc/keepalived/notify.sh backup"
            notify_fault "/etc/keepalived/notify.sh fault"            
```

回顾：HA cluster, keepalived HA Cluster：vrrp协议的实现、ais类型的集群； vrrp：keepalived ais：heartbeat/corosync+pacemaker

```text
keepalived：
    vrrp instance
        虚拟路由器（接口）、VRID（0-255）、priority（1-254）、master/backup、VMAC/VIP, ...
    Virtual Server
```

keepalived（2）

```text
虚拟服务器：
    配置参数：
        virtual_server IP port |
        virtual_server fwmark int 
        {
            ...
            real_server {
                ...
            }
            ...
        }

        常用参数：
             delay_loop <INT>：服务轮询的时间间隔；
             lb_algo rr|wrr|lc|wlc|lblc|sh|dh：定义调度方法；
             lb_kind NAT|DR|TUN：集群的类型；
             persistence_timeout <INT>：持久连接时长；
             protocol TCP：服务协议，仅支持TCP；
            sorry_server <IPADDR> <PORT>：备用服务器地址；
            real_server <IPADDR> <PORT>
            {
                 weight <INT>
                 notify_up <STRING>|<QUOTED-STRING>
                 notify_down <STRING>|<QUOTED-STRING>
                 HTTP_GET|SSL_GET|TCP_CHECK|SMTP_CHECK|MISC_CHECK { ... }：定义当前主机的健康状态检测方法；
            }

            HTTP_GET|SSL_GET {
                url {
                    path <URL_PATH>：定义要监控的URL；
                    status_code <INT>：判断上述检测机制为健康状态的响应码；
                    digest <STRING>：判断上述检测机制为健康状态的响应的内容的校验码；
                }
                nb_get_retry <INT>：重试次数；
                delay_before_retry <INT>：重试之前的延迟时长；
                connect_ip <IP ADDRESS>：向当前RS的哪个IP地址发起健康状态检测请求
                connect_port <PORT>：向当前RS的哪个PORT发起健康状态检测请求
                bindto <IP ADDRESS>：发出健康状态检测请求时使用的源地址；
                bind_port <PORT>：发出健康状态检测请求时使用的源端口；
                connect_timeout <INTEGER>：连接请求的超时时长；
            }

             TCP_CHECK {
                connect_ip <IP ADDRESS>：向当前RS的哪个IP地址发起健康状态检测请求
                connect_port <PORT>：向当前RS的哪个PORT发起健康状态检测请求
                bindto <IP ADDRESS>：发出健康状态检测请求时使用的源地址；
                bind_port <PORT>：发出健康状态检测请求时使用的源端口；
                connect_timeout <INTEGER>：连接请求的超时时长；
            }

    高可用的ipvs集群示例：
        ! Configuration File for keepalived

        global_defs {
            notification_email {
                root@localhost
            }
            notification_email_from keepalived@localhost
            smtp_server 127.0.0.1
            smtp_connect_timeout 30
            router_id node1
            vrrp_mcast_group4 224.0.100.19
        }

        vrrp_instance VI_1 {
            state MASTER
            interface eno16777736
            virtual_router_id 14
            priority 100
            advert_int 1
            authentication {
                auth_type PASS
                auth_pass 571f97b2
            }
            virtual_ipaddress {
                10.1.0.93/16 dev eno16777736
            }
            notify_master "/etc/keepalived/notify.sh master"
            notify_backup "/etc/keepalived/notify.sh backup"
            notify_fault "/etc/keepalived/notify.sh fault"
        }

        virtual_server 10.1.0.93 80 {
            delay_loop 3
            lb_algo rr
            lb_kind DR
            protocol TCP

            sorry_server 127.0.0.1 80

            real_server 10.1.0.69 80 {
                weight 1
                HTTP_GET {
                url {
                    path /
                    status_code 200
                }
                connect_timeout 1
                nb_get_retry 3
                delay_before_retry 1
                }
            }
            real_server 10.1.0.71 80 {
                weight 1
                HTTP_GET {
                url {
                    path /
                    status_code 200
                }
                connect_timeout 1
                nb_get_retry 3
                delay_before_retry 1
                }
            }
        }

keepalived调用外部的辅助脚本进行资源监控，并根据监控的结果状态能实现优先动态调整；
    分两步：(1) 先定义一个脚本；(2) 调用此脚本；
        vrrp_script <SCRIPT_NAME> {
            script ""
            interval INT 
            weight -INT 
        }

        track_script {
            SCRIPT_NAME_1
            SCRIPT_NAME_2
            ...
        }

    示例：高可用nginx服务

        ! Configuration File for keepalived

        global_defs {
            notification_email {
                root@localhost
            }
            notification_email_from keepalived@localhost
            smtp_server 127.0.0.1
            smtp_connect_timeout 30
            router_id node1
            vrrp_mcast_group4 224.0.100.19
        }

        vrrp_script chk_down {
            script "[[ -f /etc/keepalived/down ]] && exit 1 || exit 0"
            interval 1
            weight -5
        }

        vrrp_script chk_nginx {
            script "killall -0 nginx && exit 0 || exit 1"
            interval 1
            weight -5
        }

        vrrp_instance VI_1 {
            state MASTER
            interface eno16777736
            virtual_router_id 14
            priority 100
            advert_int 1
            authentication {
                auth_type PASS
                auth_pass 571f97b2
            }
            virtual_ipaddress {
                10.1.0.93/16 dev eno16777736
            }
            track_script {
                chk_down
                chk_nginx
            }
            notify_master "/etc/keepalived/notify.sh master"
            notify_backup "/etc/keepalived/notify.sh backup"
            notify_fault "/etc/keepalived/notify.sh fault"
        }

博客作业：以上所有内容；
```

HA Cluster（keepalived）：

```text
系统可用性：
    A = MTBF/(MTBF+MTTR)
        (0,1), 95%,
        几个9: 99%, 99.9%, 99.99%, 99.999%
            1%, 0.1%, 0.01%

            降低MTTR：冗余（redundant）

高可用服务：
    高可用资源：
        web service:
            ip/httpd/filesystem

    network partition：
        隔离设备：
            node：STONITH
            资源：fence

HA Cluster实现方案：
    vrrp协议的实现
        keepalived
    ais：完备HA集群
        heartbeat
        corosync

keepalived：

    vrrp协议 ：Virtual Redundant Routing Protocol
        虚拟路由器、VRID、master、backup、VIP、VMAC（00-00-5e-00-01-VRID）、优先级；

        抢占式、非抢占式

        工作模式：
            主/备
            主/主：配置多个virtual router；
        认证试工：
            无认证 
            简单字符串
            MD5

    keepalived：
        是vrrp协议的软件实现，原生设计的目的为了高可用ipvs服务；
        ka：
            vrrp协议完成地址流动；
            为vip地址所在的节点生成ipvs规则（定义在配置文件中）；
            为各rs做健康状态检测；
            基于脚本调用接口通过执行脚本完成脚本中定义的功能；

        组件：
            控制组件：配置文件分析器
            内在管理：
            IO复用器：
            核心组件：
                vrrp stack
                checkers
                ipvs wrapper
                watch dog

        HA Cluster的配置前提：
            (1) 各节点时间必须同步；
                ntp, chrony

            (2) 确保iptables及selinux不会成为阻碍；

            (3) 各节点之间可通过主机名互相通信（对KA并非必须）；

            (4) 各节点之间的root用户 可以基于密钥认证的ssh互相通信；

    keepalived：
        CentOS 6.4+,  CentOS 7.2

        程序环境：
            配置文件：/etc/keepalived/keepalived.conf
            程序文件：/usr/sbin/keepalived
            Unit File：keepalived.service 

            配置文件组成：

                GLOBAL CONFIGURATION
                    Global definations
                    static routes
                VRRPD CONFIGURATION
                    VRRP synchronization group(s)
                    VRRP instance(s)
                LVS CONFIGURATION
                    Virtual server group(s)
                    Virtual server(s)

        配置示例：
            ! Configuration File for keepalived

            global_defs {
                notification_email {
                    root@localhost
                }
                notification_email_from keepalived@localhost
                smtp_server 127.0.0.1
                smtp_connect_timeout 30
                router_id node1
                vrrp_mcast_group4 224.0.100.18
            }

            vrrp_instance VI_1 {
                state MASTER
                interface eno16777736
                virtual_router_id 15
                priority 100
                advert_int 1
                authentication {
                    auth_type PASS
                    auth_pass RrpIoZU7
                }
                virtual_ipaddress {
                    172.16.100.99
                }
            }

        示例2（双主模型）：
            ! Configuration File for keepalived

            global_defs {
                notification_email {
                    root@localhost
                }
                notification_email_from keepalived@localhost
                smtp_server 127.0.0.1
                smtp_connect_timeout 30
                router_id node1
                vrrp_mcast_group4 224.0.100.18
            }

            vrrp_instance VI_1 {
                state MASTER
                interface eno16777736
                virtual_router_id 15
                priority 100
                advert_int 1
                authentication {
                    auth_type PASS
                    auth_pass RrpIoZU7
                }
                virtual_ipaddress {
                    172.16.100.99
                }
            }

            vrrp_instance VI_2 {
                state BACKUP
                interface eno16777736
                virtual_router_id 16
                priority 98
                advert_int 1
                authentication {
                    auth_type PASS
                    auth_pass 7r0IoZU7
                }
                virtual_ipaddress {
                    172.16.100.88
                }
            }    

        配置语法：
            vrrp_instance <STRING> {
                state MASTER|BACKUP
                    当前节点在此虚拟路由器上的初始状态； 只能有一个MASTER主机，余下的都应该为BACKUP；
                interface IFACE_NAME
                    vrrp实例绑定的接口； 
                virtual_router_id #
                    虚拟路径器的VRID，范围是0-255；
                priority 100
                    当前主机在此虚拟路径器中的优先级；范围是1-254；
                advert_int #
                    vrrp通告时间的间隔；

                authentication {     # Authentication block
                    # PASS||AH
                    # PASS - Simple Passwd (suggested)
                    # AH - IPSEC (not recommended))
                    auth_type PASS
                    # Password for accessing vrrpd.
                    # should be the same for all machines.
                    # Only the first eight (8) characters are used.
                    auth_pass 1234
                }

                virtual_ipaddress {
                    <IPADDR>/<MASK> brd <IPADDR> dev <STRING> scope <SCOPE> label <LABEL>
                    192.168.200.17/24 dev eth1
                    192.168.200.18/24 dev eth2 label eth2:1
                }    

                track_interface {
                    eth0
                    eth1
                    ...
                }                    
                配置要监控的网络接口，一旦接口出现故障，则转为FAULT状态； 


                notify_master <STRING>|<QUOTED-STRING>
                notify_backup <STRING>|<QUOTED-STRING>
                notify_fault <STRING>|<QUOTED-STRING>
                notify <STRING>|<QUOTED-STRING>                    

            通知脚本示例：
                #!/bin/bash
                #
                contact='root@localhost'

                notify() {
                    mailsubject="$(hostname) to be $1, vip floating."
                    mailbody="$(date +'%F %T'): vrrp transition, $(hostname) changed to be $1"
                    echo "$mailbody" | mail -s "$mailsubject" $contact
                }

                case $1 in
                master)
                    notify master
                    ;;
                backup)
                    notify backup
                    ;;
                fault)
                    notify fault
                    ;;
                *)
                    echo "Usage: $(basename $0) {master|backup|fault}"
                    exit 1
                    ;;
                esac        

            脚本调用方法示例：
                notify_master "/etc/keepalived/notify.sh master"
                notify_backup "/etc/keepalived/notify.sh backup"
                notify_fault "/etc/keepalived/notify.sh fault"                

        虚拟服务器：
             virtual_server IP PORT |
            virtual_server fwmark FWM {
                 lb_algo rr|wrr|lc|wlc|lblc|sh|dh
                    定义调度方法
                delay_loop <INT>
                    服务轮询的时间间隔；
                lb_kind NAT|DR|TUN
                    lvs的类型
                persistence_timeout <INT>
                    持久连接时长
                 protocol TCP
                    服务协议，仅支持TCP
                sorry_server <IPADDR> <PORT>
                    所有RS均故障时用于向用户 say sorry的主机；
                 real_server <IPADDR> <PORT> {
                    weight <INT>
                        权重
                    notify_up <STRING>|<QUOTED-STRING>
                    notify_down <STRING>|<QUOTED-STRING>
                        RS状态改变通知脚本
                     HTTP_GET|SSL_GET {

                     }
                     TCP_CHECK {

                     }
                 }
            }

            HTTP_GET|SSL_GET {
                url {
                    #eg path / , or path /mrtg2/
                    path <STRING>
                    # healthcheck needs status_code
                    # or status_code and digest
                    # Digest computed with genhash
                    # eg digest 9b3a0c85a887a256d6939da88aabd8cd
                    digest <STRING>
                    # status code returned in the HTTP header
                    # eg status_code 200
                    status_code <INT>
                }
                 nb_get_retry <INT>
                    重试的次数；
                delay_before_retry <INT>
                    重试之前延迟时长；
                connect_ip <IP ADDRESS>
                    向当前RS的哪个IP地址发起健康状态检测请求；
                connect_port <PORT>
                    向当前RS的哪个PORT发起健康状态检测请求；
                bindto <IP ADDRESS>
                    发出健康状态检测请求时使用的源地址；
                 bind_port <PORT>
                    源端口
                connect_timeout <INTEGER>
                    连接超时时长
            }

             TCP_CHECK {
                 nb_get_retry <INT>
                    重试的次数；
                delay_before_retry <INT>
                    重试之前延迟时长；
                connect_ip <IP ADDRESS>
                    向当前RS的哪个IP地址发起健康状态检测请求；
                connect_port <PORT>
                    向当前RS的哪个PORT发起健康状态检测请求；
                bindto <IP ADDRESS>
                    发出健康状态检测请求时使用的源地址；
                 bind_port <PORT>
                    源端口
                connect_timeout <INTEGER>
                    连接超时时长
            }

        示例：

            ! Configuration File for keepalived

            global_defs {
                notification_email {
                    root@localhost
                }
                notification_email_from keepalived@localhost
                smtp_server 127.0.0.1
                smtp_connect_timeout 30
                router_id node1
                vrrp_mcast_group4 224.0.100.18
            }

            vrrp_instance VI_1 {
                state MASTER
                interface eno16777736
                virtual_router_id 57
                priority 100
                advert_int 1
                authentication {
                    auth_type PASS
                    auth_pass 98181111
                }
                virtual_ipaddress {
                    172.16.100.71/32 dev eno16777736 brd 172.16.100.71 label eno16777736:0
                }
            }


            virtual_server 172.16.100.71 80 {
                delay_loop 3
                lb_algo rr 
                lb_kind DR
                nat_mask 255.255.0.0
                protocol TCP

                sorry_server 127.0.0.1 80

                real_server 172.16.100.8 80 {
                    weight 1
                    HTTP_GET {
                    url { 
                        path /index.html
                        #digest 640205b7b0fc66c1ea91c463fac6334d
                        status_code 200
                    }
                    connect_timeout 3
                    nb_get_retry 3
                    delay_before_retry 3
                    }
                }
                real_server 172.16.100.9 80 {
                    weight 1
                    HTTP_GET {
                    url { 
                        path /index.html
                        #digest 640205b7b0fc66c1ea91c463fac6334d
                        status_code 200
                    }
                    connect_timeout 3
                    nb_get_retry 3
                    delay_before_retry 3
                    }
                }
            }

    keepalived调用外部分辅助脚本，完成资源监控，并根据监控的结果状态来实现优先动态调整；
        vrrp_script：定义一个资源监控脚本；
            vrrp_script  <STRING> {
                script ""
                interval INT 
                weight -INT 
            }
        track_script：调用定义的资源监控脚本；
            track_script {
                SCRIPT_NAME
            }

        示例：
            ! Configuration File for keepalived

            global_defs {
                notification_email {
                    root@localhost
                }
                notification_email_from keepalived@localhost
                smtp_server 127.0.0.1
                smtp_connect_timeout 30
                router_id node1
                vrrp_mcast_group4 224.0.100.18
            }

            vrrp_script chk_down {
                script "[[ -f /etc/keepalived/down ]] && exit 1 || exit 0"
                interval 1
                weight -5
            }

            vrrp_script chk_httpd {
                script "killall -0 httpd && exit 0 || exit 1"
                interval 1
                weight -5
            }

            vrrp_instance VI_1 {
                state MASTER
                interface eno16777736
                virtual_router_id 57
                priority 100
                advert_int 1
                authentication {
                    auth_type PASS
                    auth_pass 98181111
                }
                virtual_ipaddress {
                    172.16.100.71/32 dev eno16777736
                }

                track_script {
                    chk_down
                    chk_httpd
                }
                notify_master "/etc/keepalived/notify.sh master"
                notify_backup "/etc/keepalived/notify.sh backup"
                notify_fault "/etc/keepalived/notify.sh fault"
            }

    博客作业：以上所有内容；
```

HA Cluster:

```text
Linux Cluster: LB/HA/HP

HA：
    A=MTBF/(MTBF+MTTR)
        MTBF: Mean Time Between Failure
        MTTR：Mean Time To Repair

            冗余：Failover，Failback

    1>A>0，(0,1)，百分比
        95%, 97%, 99%, 99.9%, ...

    故障场景：
        硬件：人为故障、wear out、设计缺陷、……
        软件：人为故障、bug、设计缺陷、……

HA解决方案：
    vrrp协议：keepalived
    SA Forum：AIS，OpenAIS（开放式应用接口标准）

    OpenAIS：
        Messaging Layer（Infrastructure Layer）
            HA-Aware
        CRM（Cluster Resource Manager）
            非HA-Aware 
            管理接口：
                CLI：命令行接口
                GUI：图形用户界面 
            LRM（Local Resoure Manager）
        RA（Resource Agent）
            start|stop|restart|status
                status：
                    running/stopped

        各自的实现：
            Messaging Layer：
                heartbeat：v1 --> v2 --> v3
                openais --> cman (cluster manager)
                openais --> corosync engine 
            CRM：
                heartbeat v1 --> haresources
                    配置文件：haresources配置文件
                heartbeat v2 --> crm 
                    运行方式：在集群中的每个节点上运行一个crmd守护进程（5560/tcp），提供API；
                    配置接口：crmsh, hb_gui
                heartbeat v3 --> pacemaker
                    配置接口：crmsh, pcs
                cman --> rgmanager
                    配置接口：
                        配置文件：cluster.conf(xml)
                        GUI: system-config-cluster
                        WebGUI：conga(ricci/luci)
                        CLI：cman_tool, ccs_tool, clustat, ...
                corosync --> pacemaker

        组合方式：
            heartbeat v1 + haresources
            heartbeat v2 + crm (crmsh, hb_gui)
            heartbeat v3 + cluster-glue + pacemaker
            corosync + pacemaker
                corosync v1 + pacemaker (plugin)
                corosync v2 (quorumsystem) + pacemaker (standalone daemon)
            cman + rgmanager
            cman + corosync v1 + pacemaker

        RHCS：RedHat Cluster Suite
            RHEL5: cman + rgmanager + conga(ricci/luci)
            RHEL6：
                cman + rgmanager + conga(ricci/luci)
                corosync v1 + pacemaker + crmsh/pcs
                cman + corosync v1 + pacemaker + crmsh/pcs
            RHEL7：
                corosync v2 + pacemaker + pcs 

    quorum：

        network partition：brain-split

        quorum system：    投票系统
            node: vote

            with quorum：votes > total/2
            without quorum: votes <= total/2
                no_quorum_policy
                    stop
                    ignore 
                    suicide
                    freeze

            fecing：
                node level：STONITH （Shooting The Other Node In The Node）
                    stonith device：
                        hardware：power switch, ...
                        software：virtulization kvm/xen/...，ssh/telnet, ...
                        meatware：                            
                resource level：
                    fc switch

            特殊场景：two nodes cluster
                (1) no_quorum_policy
                    ignore
                (2) quorum device 
                    (a) ping node
                    (b) quorum disk
                    ...

            集群事务信息及心跳信息的传递方式：
                (1) unicast
                (2) multicast
                (3) broadcast 

    Resource Allocation Layer：资源管理
        DC：Designated Coordinator

        Component：
            DC：
                CRM，CIB（Cluster Information Base），PE（Policy Engine），LRM
            非DC：
                CRM，CIB，LRM

        资源类型：
            primitive：基本资源，主资源；
                仅能运行一份，仅能运行于单个节点；
            group：组
                将一个或多个资源组织成为一个可统一管理的单一单位；
            clone：克隆
                一个资源可以在集群中运行多个副本；可以运行于多个节点；
            multi-state(master/slave)：
                是clone类型的资源的特殊表现，存在多个副本，副本间存在主从关系；drbd是常见的此类应用；

        集群的架构类型：
            A/P， A/A
            N个节点：
                N-1：N个节点运行一个服务；
                N-M：N个节点运行M个服务，M<N，备用节点数量（N-M）；
                N-N：N个节点运行N个服务；

        资源倾向性：资源的约束关系；
            score：(-oo, +oo)
                正数：正倾向性；
                负数：负倾向性；

            location：位置约束，资源对节点的倾向性；
            colocation：排列约束，资源与资源运行于同一节点的倾向性；
            order：顺序约束，定义资源间的依赖关系；

    RA：资源代理
        类别：
            LSB：/etc/rc.d/init.d/*
                脚本，支持start|stop|status|restart|reload|force-reload；
                注意：一定不能设置为开机自动启动；
            OCF：Open Cluster Framework
                /usr/lib/ocf/resource.d/provider/目录下；
                类似于LSB的脚本，但支持start|stop|status|monitor|meta-data等；
            systemd unti file：/usr/lib/systemd/system
                注意：一定要设置为enable；
            STONITH：专用于配置stonith设备的RA；
            service：
```

回顾： HA Cluster： vrrp协议的实现 ais\(OpenAIS\) Messaging Layer： heartbeat, cman, openais\(corosync\) Resource Allocation Layer（CRM）： haresources, crm, pacemaker, rgmanager RA： classes：LSB，OCF，systemd，STONITH，service

```text
        Quorum System(network partition)：
            with quorum：votes > total/2
            without quorum
                no_quorum_policy：stop, ignore, suicide, freeze 

            fencing：
                node level：
                    hardware, software, meatware, ...
                resource level
                    fc switch

        N-1, N-M, N-N
            failover, failback

        Resource Type：
            primitive, group, clone, multi-state(master/slave)

        Resource Constraint：
            location
            colocation
            order
```

HA Cluster（2）

```text
CentOS 7：corosync v2 (quorum system) + pacemaker (standalone daemon) + crmsh/pcs

安装配置：
    前提：时间同步、主机名解析、多播地址；

    安装：各节点安装相关的程序包，corosync/pacemaker

    corosync的程序环境：
        配置文件：/etc/corosync/corosync.conf
        密钥文件：/etc/corosync/authkey
        Unit File：corosync.service

    配置文件格式：
        totem { }：
            interface { }：

            totem协议：节点间的通信协议，主要定义通信方式、通信协议版本、加密算法等 ；
            interface{}:定义集群心跳信息传递的接口，可以有多组；
                 Within the interface sub-directive of totem there are four parameters which are required.  There  is  one  parameter which is optional.

                 ringnumber： When using the redundant ring protocol, each interface should specify separate ring numbers to uniquely identify to the membership protocol which interface  to  use for which redundant ring. The ringnumber must start at 0.

                 bindnetaddr：should be an IP address configured on the system, or a network address.

                 mcastaddr： This is the multicast address used by corosync executive.

                 mcastport：This specifies the UDP port number.

                 ttl：This  specifies  the  Time To Live (TTL).

         version：This specifies the version of the configuration file. 目前取值仅有2一项可用；
         crypto_hash：This specifies which HMAC authentication should be used to authenticate all messages. Valid values  are  none (no authentication), md5, sha1, sha256, sha384 and sha512.
        crypto_cipher：This  specifies  which cipher should be used to encrypt all messages.  Valid values are none (no encryption), aes256, aes192, aes128 and 3des.  Enabling crypto_cipher, requires also enabling of crypto_hash.

        配置示例：
            totem {
                version: 2
                crypto_cipher: aes256
                crypto_hash: sha1
                interface {
                    ringnumber: 0
                    bindnetaddr: 10.1.0.0
                    mcastaddr: 239.255.100.1
                    mcastport: 5405
                    ttl: 1
                }
            }
            logging {
                fileline: off
                to_stderr: no
                to_logfile: yes
                logfile: /var/log/cluster/corosync.log
                to_syslog: no
                debug: off
                timestamp: on
                logger_subsys {
                    subsys: QUORUM
                    debug: off
                }
            }
            quorum {
                provider: corosync_votequorum
            }
            nodelist {
                node {
                    ring0_addr: node1.magedu.com
                    nodeid: 1
                }
                node {
                    ring0_addr: node2.magedu.com
                    nodeid: 2
                }
            }

        生成authkey：
            corosync-keygen

        启动服务：systemctl start corosync.service

        验正服务启动：
            (1) 查看日志；
            (2) corosync-cmapctl  | grep members 
            (3) corosync-cfgtool：管理工具；
                -s：显示当前节点各ring相关的信息；
                -R：控制所有节点重载配置；

    pacemaker：
        程序环境：
            配置文件：/etc/sysconfig/pacemaker
            主程序：/usr/sbin/pacemakerd
            Unit File：pacemaker.service

        启动服务：
            systemctl start pacemaker.service 

        监控服务启动是否正常：
            # crm_mon 

    配置接口（crmsh/pcs）
        crmsh：
            运行方式：    
                交互式方式：
                    crm(live)#
                命令方式：
                    crm COMMAND

            获取帮助：ls, help
                help [KEYWORD]

        COMMAND：
            cd             Navigate the level structure
            help           Show help (help topics for list of topics)
            ls             List levels and commands
            quit           Exit the interactive shell
            report         Create cluster status report
            status         Cluster status
            up             Go back to previous level    
            cib/           CIB shadow management
            cibstatus/     CIB status management and editing
             cluster/       Cluster setup and management
             configure/     CIB configuration
             assist/        Configuration assistant
             history/       Cluster history
             ra/            Resource Agents (RA) lists and documentation
             template/      Edit and import a configuration from a template
             corosync/      Corosync management
             node/          Nodes management
             options/       User preferences
             resource/      Resource management
             script/        Cluster script management
             site/          Site support

        configure命令：
            Finally, there are the cluster properties, resource meta attributes defaults, and operations defaults. All are just a set of attributes. These attributes are managed by the following commands:

                - property
                - rsc_defaults
                - op_defaults

            Commands for resources are:

                - primitive
                - monitor
                - group
                - clone
                - ms/master (master-slave)

            There are three types of constraints:

                - location
                - colocation
                - order                

            设置集群的全局属性：property 
                stonith-enabled=true|false

            定义一个primitive资源的方法：
                primitive <rsc> {[<class>:[<provider>:]]<type>}     [params attr_list] [op op_type [<attribute>=<value>...] ...]

                op_type :: start | stop | monitor    

            定义一个组资源的方法：
                group <name> <rsc> [<rsc>...]

                <rsc>：资源的ID，字符串；
                [<class>:[<provider>:]]<type>

            定义资源监控：
                (1) monitor <rsc>[:<role>] <interval>[:<timeout>]
                (2) primitive <rsc> {[<class>:[<provider>:]]<type>}     [params attr_list] [op monitor interval=# timeout=#]

        ra:
            Commands:
                cd             Navigate the level structure
                classes        List classes and providers
                help           Show help (help topics for list of topics)
                info           Show meta data for a RA
                list           List RA for a class (and provider)
                ls             List levels and commands
                providers      Show providers for a RA and a class
                quit           Exit the interactive shell
                up             Go back to previous level

            常用命令：
                classes：类别列表
                list CLASSES [PROVIDER]：列出指定类型(及提供者)之下的所有可用RA；
                info [<class>:[<provider>]:]]<type>：显示指定的RA的详细文档；

        资源约束关系的定义：

            位置约束：
                location <id> rsc  <score>:  <node>
                    <score>：
                        #, -#
                        inf, -inf 

                    property default-resource-stickiness

            排列约束：
                colocation <id> <score>:  <rsc>  <with-rsc>

            顺序约束：
                order <id> [{kind|<score>}:] first then [symmetrical=<bool>]

    高可用ipvs，可借助于ldirectord实现； 

    博客作业：corosync, pacemaker，nfs高可用mariadb；

pcs：node1, node2, node3
```

