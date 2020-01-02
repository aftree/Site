# keepalived

HA Cluster：

```text
集群类型：LB（lvs/nginx（http/upstream, stream/upstream））、HA、HP



    SPoF: Single Point of Failure



系统可用性的公式：A=MTBF/（MTBF+MTTR）

    \(0,1\), 95%

    几个9（指标）: 99%, ..., 99.999%，99.9999%；

        99%： %1, 99.9%， 0.1%



    系统故障：

        硬件故障：设计缺陷、wear out、自然灾害、……

        软件故障：设计缺陷、



    提升系统高用性的解决方案之降低MTTR：



        手段：冗余（redundant）



        active/passive（主备），active/active（双主） 

            active --&gt; HEARTBEAT --&gt; passive 

            active &lt;--&gt; HEARTBEAT &lt;--&gt; active



    高可用的是“服务”：

        HA nginx service：

            vip/nginx process\[/shared storage\]



            资源：组成一个高可用服务的“组件”；



            \(1\) passive node的数量？

            \(2\) 资源切换？



        shared storage：

            NAS：文件共享服务器；

            SAN：存储区域网络，块级别的共享；



        Network partition：网络分区

            隔离设备：

                node：STONITH = Shooting The Other Node In The Head

                资源：fence 



            quorum：            

                with quorum： &gt; total/2

                without quorum: &lt;= total/2



            TWO nodes Cluster？

                辅助设备：ping node, quorum disk; 



    Failover：故障切换，即某资源的主节点故障时，将资源转移至其它节点的操作；

    Failback：故障移回，即某资源的主节点故障后重新修改上线后，将转移至其它节点的资源重新切回的过程； 



HA Cluster实现方案:

    vrrp协议的实现

        keepalived

    ais：完备HA集群

        RHCS（cman）

        heartbeat

        corosync



keepalived：



    vrrp协议：Virtual Redundant Routing Protocol

        术语：

            虚拟路由器：Virtual Router 

            虚拟路由器标识：VRID\(0-255\)

            物理路由器：

                master：主设备

                backup：备用设备

                priority：优先级

            VIP：Virtual IP 

            VMAC：Virutal MAC \(00-00-5e-00-01-VRID\)

                GraciousARP



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

        \(1\) 各节点时间必须同步；

            ntp, chrony

        \(2\) 确保iptables及selinux不会成为阻碍；

        \(3\) 各节点之间可通过主机名互相通信（对KA并非必须）；

            建议使用/etc/hosts文件实现； 

        \(4\) 各节点之间的root用户可以基于密钥认证的ssh服务完成互相通信；（并非必须）



    keepalived安装配置：

        CentOS 6.4+随base仓库提供；    



        程序环境：

            主配置文件：/etc/keepalived/keepalived.conf

            主程序文件：/usr/sbin/keepalived

            Unit File：keepalived.service

            Unit File的环境配置文件：/etc/sysconfig/keepalived



        配置文件组件部分：

            TOP HIERACHY

                GLOBAL CONFIGURATION

                    Global definitions

                    Static routes/addresses

                VRRPD CONFIGURATION

                    VRRP synchronization group\(s\)：vrrp同步组；

                    VRRP instance\(s\)：每个vrrp instance即一个vrrp路由器；

                LVS CONFIGURATION

                    Virtual server group\(s\)

                    Virtual server\(s\)：ipvs集群的vs和rs；



        单主配置示例：

            ! Configuration File for keepalived



            global\_defs {

                notification\_email {

                    root@localhost

                }

                notification\_email\_from keepalived@localhost

                smtp\_server 127.0.0.1

                smtp\_connect\_timeout 30

                router\_id node1

                vrrp\_mcast\_group4 224.0.100.19

            }



            vrrp\_instance VI\_1 {

                state BACKUP

                interface eno16777736

                virtual\_router\_id 14

                priority 98

                advert\_int 1

                authentication {

                    auth\_type PASS

                    auth\_pass 571f97b2

                }

                virtual\_ipaddress {

                    10.1.0.91/16 dev eno16777736

                }

            }            





        配置语法：

            配置虚拟路由器：

                vrrp\_instance &lt;STRING&gt; {

                    ....

                }



                专用参数：

                     state MASTER\|BACKUP：当前节点在此虚拟路由器上的初始状态；只能有一个是MASTER，余下的都应该为BACKUP；

                     interface IFACE\_NAME：绑定为当前虚拟路由器使用的物理接口；

                     virtual\_router\_id VRID：当前虚拟路由器的惟一标识，范围是0-255；

                     priority 100：当前主机在此虚拟路径器中的优先级；范围1-254；

                    advert\_int 1：vrrp通告的时间间隔；

                    authentication {

                        auth\_type AH\|PASS

                        auth\_pass &lt;PASSWORD&gt;

                    }

                    virtual\_ipaddress {

                        &lt;IPADDR&gt;/&lt;MASK&gt; brd &lt;IPADDR&gt; dev &lt;STRING&gt; scope &lt;SCOPE&gt; label &lt;LABEL&gt;

                        192.168.200.17/24 dev eth1

                        192.168.200.18/24 dev eth2 label eth2:1

                    }

                     track\_interface {

                        eth0

                        eth1

                        ...

                    }

                    配置要监控的网络接口，一旦接口出现故障，则转为FAULT状态；

                    nopreempt：定义工作模式为非抢占模式；

                    preempt\_delay 300：抢占式模式下，节点上线后触发新选举操作的延迟时长；



                    定义通知脚本：

                        notify\_master &lt;STRING&gt;\|&lt;QUOTED-STRING&gt;：当前节点成为主节点时触发的脚本；

                        notify\_backup &lt;STRING&gt;\|&lt;QUOTED-STRING&gt;：当前节点转为备节点时触发的脚本；

                        notify\_fault &lt;STRING&gt;\|&lt;QUOTED-STRING&gt;：当前节点转为“失败”状态时触发的脚本；



                        notify &lt;STRING&gt;\|&lt;QUOTED-STRING&gt;：通用格式的通知触发机制，一个脚本可完成以上三种状态的转换时的通知；
```

回顾：

```text
HA Cluster：

    A=MTBF/\(MTBF+MTTR\)

        99.5%, ..., 99.9999%



实现：

    vrrp协议：keepalived

    ais：OpenAIS



vrrp：

    virtual router：

        VRID，VIP，VMAC，priority, ...



keepalived：

    vrrp/ipvs\_wrapper/checkers
```

keepalived（2）

```text
        双主模型示例：            

            ! Configuration File for keepalived



            global\_defs {

                notification\_email {

                    root@localhost

                }

                notification\_email\_from keepalived@localhost

                smtp\_server 127.0.0.1

                smtp\_connect\_timeout 30

                router\_id node1

                vrrp\_mcast\_group4 224.0.100.19

            }



            vrrp\_instance VI\_1 {

                state MASTER

                interface eno16777736

                virtual\_router\_id 14

                priority 100

                advert\_int 1

                authentication {

                    auth\_type PASS

                    auth\_pass 571f97b2

                }

                virtual\_ipaddress {

                    10.1.0.91/16 dev eno16777736

                }

            }



            vrrp\_instance VI\_2 {

                state BACKUP

                interface eno16777736

                virtual\_router\_id 15

                priority 98

                advert\_int 1

                authentication {

                    auth\_type PASS

                    auth\_pass 578f07b2

                }

                virtual\_ipaddress {

                    10.1.0.92/16 dev eno16777736

                }

            }                        





通知脚本的使用方式：

        示例通知脚本：

            \#!/bin/bash

            \#

            contact='root@localhost'



            notify\(\) {

                mailsubject="$\(hostname\) to be $1, vip floating"

                mailbody="$\(date +'%F %T'\): vrrp transition, $\(hostname\) changed to be $1"

                echo "$mailbody" \| mail -s "$mailsubject" $contact

            }



            case $1 in

            master\)

                notify master

                ;;

            backup\)

                notify backup

                ;;

            fault\)

                notify fault

                ;;

            \*\)

                echo "Usage: $\(basename $0\) {master\|backup\|fault}"

                exit 1

                ;;

            esac            



        脚本的调用方法：

            notify\_master "/etc/keepalived/notify.sh master"

            notify\_backup "/etc/keepalived/notify.sh backup"

            notify\_fault "/etc/keepalived/notify.sh fault"            



虚拟服务器：

    配置参数：

        virtual\_server IP port \|

        virtual\_server fwmark int 

        {

            ...

            real\_server {

                ...

            }

            ...

        }



        常用参数：

             delay\_loop &lt;INT&gt;：服务轮询的时间间隔；

             lb\_algo rr\|wrr\|lc\|wlc\|lblc\|sh\|dh：定义调度方法；

             lb\_kind NAT\|DR\|TUN：集群的类型；

             persistence\_timeout &lt;INT&gt;：持久连接时长；

             protocol TCP：服务协议，仅支持TCP；

            sorry\_server &lt;IPADDR&gt; &lt;PORT&gt;：备用服务器地址；

            real\_server &lt;IPADDR&gt; &lt;PORT&gt;

            {

                 weight &lt;INT&gt;

                 notify\_up &lt;STRING&gt;\|&lt;QUOTED-STRING&gt;

                 notify\_down &lt;STRING&gt;\|&lt;QUOTED-STRING&gt;

                 HTTP\_GET\|SSL\_GET\|TCP\_CHECK\|SMTP\_CHECK\|MISC\_CHECK { ... }：定义当前主机的健康状态检测方法；

            }



            HTTP\_GET\|SSL\_GET：应用层检测



            HTTP\_GET\|SSL\_GET {

                url {

                    path &lt;URL\_PATH&gt;：定义要监控的URL；

                    status\_code &lt;INT&gt;：判断上述检测机制为健康状态的响应码；

                    digest &lt;STRING&gt;：判断上述检测机制为健康状态的响应的内容的校验码；

                }

                nb\_get\_retry &lt;INT&gt;：重试次数；

                delay\_before\_retry &lt;INT&gt;：重试之前的延迟时长；

                connect\_ip &lt;IP ADDRESS&gt;：向当前RS的哪个IP地址发起健康状态检测请求

                connect\_port &lt;PORT&gt;：向当前RS的哪个PORT发起健康状态检测请求

                bindto &lt;IP ADDRESS&gt;：发出健康状态检测请求时使用的源地址；

                bind\_port &lt;PORT&gt;：发出健康状态检测请求时使用的源端口；

                connect\_timeout &lt;INTEGER&gt;：连接请求的超时时长；

            }



             TCP\_CHECK {

                connect\_ip &lt;IP ADDRESS&gt;：向当前RS的哪个IP地址发起健康状态检测请求

                connect\_port &lt;PORT&gt;：向当前RS的哪个PORT发起健康状态检测请求

                bindto &lt;IP ADDRESS&gt;：发出健康状态检测请求时使用的源地址；

                bind\_port &lt;PORT&gt;：发出健康状态检测请求时使用的源端口；

                connect\_timeout &lt;INTEGER&gt;：连接请求的超时时长；

            }



    高可用的ipvs集群示例：

        ! Configuration File for keepalived



        global\_defs {

            notification\_email {

                root@localhost

            }

            notification\_email\_from keepalived@localhost

            smtp\_server 127.0.0.1

            smtp\_connect\_timeout 30

            router\_id node1

            vrrp\_mcast\_group4 224.0.100.19

        }



        vrrp\_instance VI\_1 {

            state MASTER

            interface eno16777736

            virtual\_router\_id 14

            priority 100

            advert\_int 1

            authentication {

                auth\_type PASS

                auth\_pass 571f97b2

            }

            virtual\_ipaddress {

                10.1.0.93/16 dev eno16777736

            }

            notify\_master "/etc/keepalived/notify.sh master"

            notify\_backup "/etc/keepalived/notify.sh backup"

            notify\_fault "/etc/keepalived/notify.sh fault"

        }



        virtual\_server 10.1.0.93 80 {

            delay\_loop 3

            lb\_algo rr

            lb\_kind DR

            protocol TCP



            sorry\_server 127.0.0.1 80



            real\_server 10.1.0.69 80 {

                weight 1

                HTTP\_GET {

                url {

                    path /

                    status\_code 200

                }

                connect\_timeout 1

                nb\_get\_retry 3

                delay\_before\_retry 1

                }

            }

            real\_server 10.1.0.71 80 {

                weight 1

                HTTP\_GET {

                url {

                    path /

                    status\_code 200

                }

                connect\_timeout 1

                nb\_get\_retry 3

                delay\_before\_retry 1

                }

            }

        }





博客作业：第一部分

    双主模式的lvs集群，拓扑、实现过程；



    配置示例（一个节点）：    



        ! Configuration File for keepalived



        global\_defs {

        notification\_email {

        root@localhost

        }

        notification\_email\_from kaadmin@localhost

        smtp\_server 127.0.0.1

        smtp\_connect\_timeout 30

        router\_id node1

        vrrp\_mcast\_group4 224.0.100.67

        }



        vrrp\_instance VI\_1 {

        state MASTER

        interface eno16777736

        virtual\_router\_id 44

        priority 100

        advert\_int 1

        authentication {

            auth\_type PASS

            auth\_pass f1bf7fde

        }

        virtual\_ipaddress {

            172.16.0.80/16 dev eno16777736 label eno16777736:0

        }

        track\_interface {

            eno16777736

        }

        notify\_master "/etc/keepalived/notify.sh master"

        notify\_backup "/etc/keepalived/notify.sh backup"

        notify\_fault "/etc/keepalived/notify.sh fault"

        }



        vrrp\_instance VI\_2 {

        state BACKUP

        interface eno16777736

        virtual\_router\_id 45

        priority 98

        advert\_int 1

        authentication {

            auth\_type PASS

            auth\_pass f2bf7ade

        }

        virtual\_ipaddress {

            172.16.0.90/16 dev eno16777736 label eno16777736:1

        }

        track\_interface {

            eno16777736

        }

        notify\_master "/etc/keepalived/notify.sh master"

        notify\_backup "/etc/keepalived/notify.sh backup"

        notify\_fault "/etc/keepalived/notify.sh fault"

        }



        virtual\_server fwmark 3 {

        delay\_loop 2

        lb\_algo rr

        lb\_kind DR

        nat\_mask 255.255.0.0

        protocol TCP

        sorry\_server 127.0.0.1 80



        real\_server 172.16.0.69 80 {

            weight 1

            HTTP\_GET {

            url {

                path /

                status\_code 200

            }

            connect\_timeout 2

            nb\_get\_retry 3

            delay\_before\_retry 3

            }

        }

        real\_server 172.16.0.6 80 {

            weight 1

            HTTP\_GET {

                url {

                    path /

                    status\_code 200

                }

                connect\_timeout 2

                nb\_get\_retry 3

                delay\_before\_retry 3

                }

            }

        }            







keepalived调用外部的辅助脚本进行资源监控，并根据监控的结果状态能实现优先动态调整；

    分两步：\(1\) 先定义一个脚本；\(2\) 调用此脚本；

        vrrp\_script &lt;SCRIPT\_NAME&gt; {

            script ""

            interval INT 

            weight -INT 

        }



        track\_script {

            SCRIPT\_NAME\_1

            SCRIPT\_NAME\_2

            ...

        }



    示例：高可用nginx服务



        ! Configuration File for keepalived



        global\_defs {

            notification\_email {

                root@localhost

            }

            notification\_email\_from keepalived@localhost

            smtp\_server 127.0.0.1

            smtp\_connect\_timeout 30

            router\_id node1

            vrrp\_mcast\_group4 224.0.100.19

        }



        vrrp\_script chk\_down {

            script "\[\[ -f /etc/keepalived/down \]\] && exit 1 \|\| exit 0"

            interval 1

            weight -5

        }



        vrrp\_script chk\_nginx {

            script "killall -0 nginx && exit 0 \|\| exit 1"

            interval 1

            weight -5

            fall 2

            rise 1

        }



        vrrp\_instance VI\_1 {

            state MASTER

            interface eno16777736

            virtual\_router\_id 14

            priority 100

            advert\_int 1

            authentication {

                auth\_type PASS

                auth\_pass 571f97b2

            }

            virtual\_ipaddress {

                10.1.0.93/16 dev eno16777736

            }

            track\_script {

                chk\_down

                chk\_nginx

            }

            notify\_master "/etc/keepalived/notify.sh master"

            notify\_backup "/etc/keepalived/notify.sh backup"

            notify\_fault "/etc/keepalived/notify.sh fault"

        }



博客作业：

    （1）双主模型的ipvs高可用集群；

    （2）双主模型的nginx proxy高可用集群； 



    测试：ipvs使用sh算法或持久连接时，故障切换后，同一个客户端是否依然能关联至此前绑定的RS；

           nginx使用ip\_hash或hash $request\_uri算法时，故障切换后，同一个客户端是否依然能关联至此前绑定的upstream server；
```

