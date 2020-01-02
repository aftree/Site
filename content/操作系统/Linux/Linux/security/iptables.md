# iptables

iptables/netfilter:

```text
Packets Filter Firewall：
    包过滤型防火墙：

Firewall：隔离工具，工作于主机或网络的边缘处，对经由的报文根据预先定义的规则（识别标准）进行检测，对于能够被规则匹配到的报文实行某种预定义的处理机制的一套组件；
    硬件防火墙：在硬件级别实现部分功能的；
    软件防火墙：应用软件逻辑在通用硬件基础上实现；

    主机防火墙：
    网络防火墙：

ipfw --> ipchians --> iptables(ip6tables)

iptables/netfilter：
    iptables：规则管理工具；
    netfilter：防火墙框架，承载并生效规则；

    hook functions(netfilter)：
        prerouting
        input
        forward
        output
        postrouting

    iptables：
        PREROUTING
        INPUT
        FORWARD
        OUTPUT
        POSTROUTING

        允许用户自定义规则链；它们需要手动关联至指定的”钩子“；

    netfilter：功能
        filter(“防火”)：包过滤
        NAT：Network Address Translation
        mangle：拆解报文，做出修改，而后重新封装
        raw：关闭nat表上启用的连接追踪机制                        

    功能（表）<-->钩子
        filter：input, forward, output
        nat：prerouting, input, output, postrouting
        mangle：prerouting, input, forward, output, postrouting
        raw：prerouting, output

        优先级（由高而低）：raw --> mangle --> nat --> filter

    iptables规则的组成部分：
        匹配条件：
            网络层首部：SourceIP, DestinationIP, ...
            传输层首部：SourtPort, DestinationPort, TCP Flags(SYN,ACK,FIN,URG,RST,PSH), ...
            扩展模块引入的辅助检查机制：
        跳转目标：-j target 
            内建的处理机制：ACCEPT, DROP, REJECT, SNAT, DNAT, MASQUERADE, MARK, LOG, ...
            用户自定义链：

        添加规则时需要考量的因素：
            (1) 实现的功能：用于判定将规则添加至哪个表；
            (2) 报文的流经位置：用于判断将规则添加至哪个链；
            (3) 报文的流向：判定规则中何为”源“，何为”目标“；
            (4) 匹配条件：用于编写正确的匹配规则；
                (a) 专用于某种应用的同类规则，匹配范围小的放前面；
                (b) 专用于某些应用的不同类规则，匹配到的可能性较多的放前面；同一类别的规则可使用自定义链单独存放；
                (c) 用于通用目的的规则放前面；                

    filter表：过滤，”防火墙“意义的核心所在；
        INPUT，FORWARD，OUTPUT

安装：
    netfilter：位于内核中的tcp/ip协议栈报文处理框架；
    iptables：
        CentOS 5/6：iptables命令生成规则，可保存于文件中以反复装载生效；
            # iptables -t filter -F
            # service iptables save

            man iptables
        CentOS 7：firewalld, firewall-cmd, firewall-config
            # systemctl disable firewalld.service

            man iptables
            man iptables-extensions

iptables命令：

    iptables [-t table] {-A|-C|-D} chain rule-specification

    iptables [-t table] -I chain [rulenum] rule-specification
    iptables [-t table] -R chain rulenum rule-specification
```

、 iptables \[-t table\] -D chain rulenum iptables \[-t table\] -S \[chain \[rulenum\]\]

```text
    iptables [-t table] {-F|-L|-Z} [chain [rulenum]] [options...]

    iptables [-t table] -N chain

    iptables [-t table] -X [chain]

    iptables [-t table] -P chain target

    iptables [-t table] -E old-chain-name new-chain-name

        rule-specification = [matches...] [target]

        match = -m matchname [per-match-options]

        target = -j targetname [per-target-options]            

    COMMANDs：
        链管理：
            -N, --new-chain chain：新建一个自定义的规则链；
            -X, --delete-chain [chain]：删除用户自定义的引用计数为0的空链；
            -F, --flush [chain]：清空指定的规则链上的规则；
            -E, --rename-chain old-chain new-chain：重命名链；
            -Z, --zero [chain [rulenum]]：置零计数器；
                注意：每个规则都有两个计数器
                    packets：被本规则所匹配到的所有报文的个数；
                    bytes：被本规则所匹配到的所有报文的大小之和；
            -P, --policy chain target
        规则管理：
            -A, --append chain rule-specification：追加新规则于指定链的尾部； 
            -I, --insert chain [rulenum] rule-specification：插入新规则于指定链的指定位置，默认为首部；
            -R, --replace chain rulenum rule-specification：替换指定的规则为新的规则；
            -D, --delete chain rulenum：根据规则编号删除规则；
            -D, --delete chain rule-specification：根据规则本身删除规则；                
        规则显示：
            -L, --list [chain]：列出规则；
                 -v, --verbose：详细信息； 
                    -vv
                -n, --numeric：数字格式显示主机地址和端口号；
                -x, --exact：显示计数器的精确值，而非圆整后的数据；
                --line-numbers：列出规则时，显示其在链上的相应的编号；
            -S, --list-rules [chain]：显示指定链的所有规则；    

    rule-specification = [matches...] [target]
        matches：匹配条件 
        target：跳转目标

        匹配条件：
            通用匹配（PARAMETERS）：
                [!] -s, --source address[/mask][,...]：检查报文的源IP地址是否符合此处指定的范围，或是否等于此处给定的地址；
                [!] -d, --destination address[/mask][,...]：检查报文的目标IP地址是否符合此处指定的范围，或是否等于此处给定的地址；
                [!] -p, --protocol protocol：匹配报文中的协议，可用值tcp, udp,  udplite, icmp,  icmpv6,esp,  ah, sctp, mh 或者  "all", 亦可以数字格式指明协议；
                 -m, --match match：调用指定的扩展匹配模块来扩展匹配条件检查机制；
                 [!] -i, --in-interface name：限定报文仅能够从指定的接口流入；only for packets entering the INPUT, FORWARD  and  PREROUTING  chains.
                 [!] -o, --out-interface name：限定报文仅能够从指定的接口流出；for packets entering the FORWARD, OUTPUT and POSTROUTING chains.
            扩展匹配（MATCH EXTENSIONS）
                -m tcp 
                    --sport, --dport

        跳转目标：
            -j targetname [per-target-options]
                ACCEPT：接受
                DROP：丢弃
                REJECT：拒绝

    练习：172.18.0.67
        1、开放本机的所有tcp服务给所有主机；
            # iptables -I INPUT  -d 172.18.0.67 -p tcp -j ACCEPT
            # iptables -I OUTPUT  -s 172.18.0.67 -p tcp -j ACCEPT 
        2、开放本机的所有udp服务给172.16.0.0/16网络中的主机，但不包含172.16.0.200；
            # iptables -I INPUT 2 -d 172.18.0.67 -s 172.18.0.200 -p udp -j REJECT
            # iptables -I INPUT 3 -d 172.18.0.67 -s 172.18.0.0/16 -p udp -j ACCEPT
            # iptables -I OUTPUT 2 -s 172.18.0.67 -d 172.18.0.0/16 -p udp -j ACCEPT
        3、默认策略为REJECT；
        扩展：
        1、仅开放本机的ssh服务给172.16.0.0/16中的主机，而且不包含172.16.0.200; 
```

回顾： iptables/netfilter： netfilter：框架 iptables：管理规则的工具

```text
    四表：filter, nat, mangle, raw
    五链：PREROUTING，INPUT，FORWARD，OUTPUT，POSTROUTING
        流经位置：
            PREROUTING --> INPUT
            PREROUTING --> FORWARD --> POSTROUTING
            OUTPUT --> POSTROUTING

iptables命令：
    iptables [-t table] COMMAND [chain] [PARAMETERS] [-m matchname [per-match-options]] [-j targetname [per-target-options]]

    PARAMETERS：
        -s, -d, -p {tcp|udp|icmp|sctp|udplite|...}, -i, -o, -m

    COMMAND：
        链管理：-P, -X, -N, -E, -F, -Z
        规则管理：-A，-I，-R，-D
        查看：
            -L，-n, -v, -x, --line-numbers
            -S

    -j targetname
        ACCEPT, DROP, REJECT, RETURN
```

iptables（2） iptables \[-t table\] COMMAND \[chain\] \[PARAMETERS\] \[-m matchname \[per-match-options\]\] \[-j targetname \[per-target-options\]\]

```text
    匹配条件：
        基本匹配条件：PARAMETERS
        扩展匹配条件：
            隐式扩展：在使用-p选项指明了特定的协议时，无需再同时使用-m选项指明扩展模块的扩展机制；
            显式扩展：必须使用-m选项指明要调用的扩展模块的扩展机制；

        隐式扩展：
            -p tcp：可直接使用tcp扩展模块的专用选项；
                [!] --source-port,--sport port[:port]  匹配报文源端口；可以给出多个端口，但只能是连续的端口范围 ；
                [!] --destination-port,--dport port[:port]   匹配报文目标端口；可以给出多个端口，但只能是连续的端口范围 ；
                [!] --tcp-flags mask comp  匹配报文中的tcp协议的标志位；Flags are: SYN ACK FIN RST URG PSH ALL NONE；
                    mask：要检查的FLAGS list，以逗号分隔；
                    comp：在mask给定的诸多的FLAGS中，其值必须为1的FLAGS列表，余下的其值必须为0；
                        --tcp-flags SYN,ACK,FIN,RST  SYN
                        --tcp-flags ALL ALL
                        --tcp-flags ALL NONE
                [!] --syn：    --tcp-flags SYN,ACK,FIN,RST  SYN    

            -p udp：可直接使用udp协议扩展模块的专用选项：
                [!] --source-port,--sport port[:port]
                [!] --destination-port,--dport port[:port]

            [!] --icmp-type {type[/code]|typename}
                0/0： echo reply
                8/0：echo request

        显式扩展：必须使用-m选项指明要调用的扩展模块的扩展机制；
            1、multiport
                以离散或连续的 方式定义多端口匹配条件，最多15个；

                [!] --source-ports,--sports port[,port|,port:port]...：指定多个源端口；
                [!] --destination-ports,--dports port[,port|,port:port]...：指定多个目标端口；

                # iptables -I INPUT  -d 172.16.0.7 -p tcp -m multiport --dports 22,80,139,445,3306 -j ACCEPT

            2、iprange
                以连续地址块的方式来指明多IP地址匹配条件；
                [!] --src-range from[-to]
                [!] --dst-range from[-to]

                # iptables -I INPUT -d 172.16.0.7 -p tcp -m multiport --dports 22,80,139,445,3306 -m iprange --src-range 172.16.0.61-172.16.0.70 -j REJECT

            3、time
                This  matches  if the packet arrival time/date is within a given range.

                 --timestart hh:mm[:ss]
                 --timestop hh:mm[:ss]

                 [!] --weekdays day[,day...]

                 [!] --monthdays day[,day...]

                --datestart YYYY[-MM[-DD[Thh[:mm[:ss]]]]]
                --datestop YYYY[-MM[-DD[Thh[:mm[:ss]]]]]

                 --kerneltz：使用内核配置的时区而非默认的UTC；

            4、string
                This modules matches a given string by using some pattern matching strategy. 

                --algo {bm|kmp}
                [!] --string pattern
                [!] --hex-string pattern

                --from offset
                --to offset

                ~]# iptables -I OUTPUT -m string --algo bm --string "gay" -j REJECT

            5、connlimit 
                Allows  you  to  restrict  the  number  of parallel connections to a server per client IP address (or client address block).

                --connlimit-upto n
                --connlimit-above n

                ~]# iptables -I INPUT -d 172.16.0.7 -p tcp --syn --dport 22 -m connlimit --connlimit-above 2 -j REJECT

            6、limit 
                This  module  matches  at  a limited rate using a token bucket filter. 

                --limit rate[/second|/minute|/hour|/day]
                --limit-burst number

                ~]# iptables -I OUTPUT -s 172.16.0.7 -p icmp --icmp-type 0 -j ACCEPT

            7、state
                The "state" extension is a subset of the "conntrack" module.  "state" allows access to the connection tracking state for this packet.

                [!] --state state
                    INVALID, ESTABLISHED, NEW, RELATED or UNTRACKED.

                    NEW: 新连接请求；
                    ESTABLISHED：已建立的连接；
                    INVALID：无法识别的连接；
                    RELATED：相关联的连接，当前连接是一个新请求，但附属于某个已存在的连接；
                    UNTRACKED：未追踪的连接；

                    state扩展：
                        内核模块装载：
                            nf_conntrack
                            nf_conntrack_ipv4

                            手动装载：
                                nf_conntrack_ftp 

                追踪到的连接：
                    /proc/net/nf_conntrack

                调整可记录的连接数量最大值：
                    /proc/sys/net/nf_conntrack_max

                超时时长：
                    /proc/sys/net/netfilter/*timeout*

处理动作（跳转目标）：
    -j targetname [per-target-options]
        简单target：
            ACCEPT， DROP

        扩展target：
            REJECT
                This is used to send back an error packet in response to the matched packet: otherwise it is equivalent to  DROP  so it  is  a  terminating  TARGET,  ending  rule traversal.

                --reject-with type
                    The type given can be icmp-net-unreachable, icmp-host-unreachable, icmp-port-unreachable, icmp-proto-unreach‐ able, icmp-net-prohibited, icmp-host-prohibited, or icmp-admin-prohibited (*), which return  the  appropriate ICMP  error  message (icmp-port-unreachable is the default).

            LOG
                Turn  on  kernel  logging of matching packets.

                --log-level
                --log-prefix

        自定义链做为target：


保存和载入规则：
    保存：iptables-save > /PATH/TO/SOME_RULE_FILE
    重载：iptabls-restore < /PATH/FROM/SOME_RULE_FILE
        -n, --noflush：不清除原有规则
        -t, --test：仅分析生成规则集，但不提交

    CentOS 6：
        保存规则：
            service iptables save
            保存规则于/etc/sysconfig/iptables文件，覆盖保存；
        重载规则：
            service iptables restart
            默认重载/etc/sysconfig/iptables文件中的规则 

        配置文件：/etc/sysconfig/iptables-config

    CentOS 7：
        (1) 自定义Unit File，进行iptables-restore；
        (2) firewalld服务；
        (3) 自定义脚本；

规则优化的思路：
    使用自定义链管理特定应用的相关规则，模块化管理规则；

    (1) 优先放行双方向状态为ESTABLISHED的报文；
    (2) 服务于不同类别的功能的规则，匹配到报文可能性更大的放前面；
    (3) 服务于同一类别的功能的规则，匹配条件较严格的放在前面；
    (4) 设置默认策略：白名单机制
        (a) iptables -P，不建议；
        (b) 建议在规则的最后定义规则做为默认策略；
```

回顾： iptables/netfilter： netfilter：raw，mangle, nat, filter PREROUTING --&gt; INPUT PREROUTING --&gt; FORWARD --&gt; POSTROUTING OUTPUT --&gt; POSTROUTING filter：INPUT，FORWARD，OUTPUT nat：PREROUTING，INPUT，OUTPUT，POSTROUTING

```text
iptables：
    [-t table] COMMAND [chain] rule-specification
        -m matchname [per-match-options]
        -t targetname [per-target-options]
        [options]

    匹配 条件：
        基本匹配条件：-s, -d, -p, -m, -i, -o
        扩展匹配条件：
            隐式扩展：
                -p tcp: --dport, --sport, --tcp-flags, --syn 
                -p udp：--dport, --sport
                -p imcp: --icmp-type
            显式扩展：
                multiport：--sports, --dports
                iprange：--src-range, --dst-range
                time：--timestart, --timestop, --weekdays, --monthdays, --datestart, --datestop
                string：--algo {bm|kmp}, --string
                connlimit：--connlimit-upto, --connlimit-above
                limit：--limit, --limit-burst
                state：--state
                    NEW, ESTABLISHED, RELATED, INVALID, UNTRACKED

    target：
        -j：
            ACCEPT/DROP
            REJECT：--reject-with
            LOG：--log-level, --log-prefix

iptables-save/iptables-restore



iptables/netfilter网络防火墙：
    (1) 网关；
    (2) filter表的FORWARD链；

    要注意的问题：
        (1) 请求-响应报文均会经由FORWARD链，要注意规则的方向性；
        (2) 如果要启用conntrack机制，建议将双方向的状态为ESTABLISHED的报文直接报文；

    NAT: Network Address Translation
        请求报文：由管理员定义；
        响应报文：由NAT的conntrack机制自动实现； 

        请求报文：
            改源地址：SNAT，MASQUERADE
            改目标地址：DNAT

    iptables/netfilter：
        NAT定义在nat表；
            PREROUTING，INPUT，OUTPUT，POSTROUTING

            SNAT：POSTROUTING
            DNAT：PREROUTING
            PAT：

    target：
        SNAT：
            This  target  is only valid in the nat table, in the POSTROUTING and INPUT chains, and user-defined chains which are only called from those chains.

            --to-source [ipaddr[-ipaddr]]

        DNAT：
            This target is only valid in the nat table, in the PREROUTING and OUTPUT chains, and user-defined chains  which  are only  called from those chains.

            --to-destination [ipaddr[-ipaddr]][:port[-port]]

         MASQUERADE
            This target is only valid in the nat table, in the POSTROUTING chain.  It  should  only  be  used  with  dynamically assigned  IP (dialup) connections: if you have a static IP address, you should use the SNAT target.

            SNAT场景中应用于POSTROUTING链上的规则实现源地址转换，但外网地址不固定时，使用此target；

        REDIRECT
            This  target  is only valid in the nat table, in the PREROUTING and OUTPUT chains, and user-defined chains which are only called from those chains.

            --to-ports port[-port]

    layer7



博客作业：iptables/netfilter入门到进阶
```

tcp\_wrapper：

```text
库文件：libwrap.so，tcp包装器；

判断一个服务程序是否能够由tcp_wrapper进行访问控制的方法：
    (1) 动态链接至libwrap.so库；
        ldd  /PATH/TO/PROGRAM
            libwrap.so
    (2) 静态编译libwrap.so库文件至程序中：
        strings /PATH/TO/PGRGRAM 
            hosts_access

配置文件：/etc/hosts.allow, /etc/hosts.deny

     See 'man 5 hosts_options' and 'man 5 hosts_access' for information on rule syntax.    

    配置文件语法：
        daemon_list : client_list[ : option : option ...]

        daemon_list：程序文件名称列表
            (1) 单个应用程序文件名；
            (2) 程序文件名列表，以逗号分隔；
            (3) ALL：所有受tcp_wrapper控制的应用程序文件；

        client_list：
            (1) 单个IP地址或主机名；
            (2) 网络地址：n.n.n.n/m.m.m.m，n.n.n.；
            (3) 内建的ACL:
                ALL：所有客户端主机；
                LOCAL：Matches any host whose name does not contain a dot character.
                UNKNOWN
                KNOWN
                PARANOID

            OPERATORS：
                EXCEPT
                    list1 EXCEPT list2 EXCEPT list3

                    sshd: 172.16. EXCEPT 172.16.100. EXCEPT 172.16.100.68

        [ : option : option ...]

            deny：拒绝，主要用于hosts.allow文件中定义“拒绝”规则；
            allow：允许，主要用于hosts.deny文件中定义”允许“规则；

            spawn：生成，发起，触发执行用户指定的任意命令，此处通常用于记录日志；

                vsftpd: 172.16. : spawn /bin/echo $(date) login attempt from %c to %s >> /var/log/tcp_wrapper.log 

练习：仅开放本机的sshd服务给172.16.0.0/16网络中除了172.16.0.0/24网络中的主机之外的所有主机，但允许172.16.0.200访问； 每次的用户访问都要记录于日志文件中；
```

