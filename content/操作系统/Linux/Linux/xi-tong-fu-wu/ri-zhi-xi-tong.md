# 日志系统

rsyslog：

```text
日志：历史事件；
    历史事件：时间、地点、事件；

syslog：
    klogd：kernel
    syslogd：system(application)

    事件记录格式：
        日期时间   主机   进程[pid]: 事件内容；

    C/S架构：通过TCP或UDP协议的服务完成日志记录传送；

rsyslog：
    rsyslog的特性：
        多线程；
        UDP/TCP/SSL/TLS/RELP；
        存储日志信息于MySQL, PGSQL, Oracle等RDBMS；
        强大的过滤器，实现过滤日志信息中的任何部分的内容；
        自定义的输出格式；
        ……

    ELK：
        E: elasticsearch, L: logstash, K: kibana

rsyslog日志收集器的基本术语：
    facility：设施，收束日志数据流为有限几个；
        auth, authpriv, cron, daemon, kern, lpr, mail, mark, news, security, user, uucp, syslog, local0-local7
    proirity：优先级
        debug, info, notice, warn(warning), err(error), crit(critical), alert, emerg(panic)

程序包：rsyslog
    程序环境：
        配置文件：/etc/rsyslog.conf，/etc/rsyslog.d/*.conf
        主程序：/usr/sbin/rsyslogd
        CentOS 6：service rsyslogs {start|stop|restart|status}
        CentOS 7：/usr/lib/systemd/system/rsyslog.service

    配置文件格式：
        由三部分组成：
            MODULES：模块配置
            GLOBAL DIRECTIVES：全局配置
            RULES：日志记录相关的配置

    RULES:
        配置格式：
            facility.priority     target

            facility：
                *: 所有的facility；
                f1,f2,f3,...：指定的facility列表； 

            priority：
                *: 所有级别；
                none：没有级别；
                PRIORITY：指定级别（含）以上的所有级别；
                =PRIORITY：仅记录指定级别的日志信息； 

            target：
                文件：将日志信息记录到指定的文件中；文件路径前的-表示异步写入；
                用户：将日志事件通知给指定的用户；
                日志服务器：@host，把日志通过网络送往指定的服务器记录，而非由本地记录； 
                管道： | COMMAND

    配置rsyslog成为日志服务器：
        #### MODULES ####
        # Provides UDP syslog reception
        $ModLoad imudp
        $UDPServerRun 514

        # Provides TCP syslog reception
        $ModLoad imtcp
        $InputTCPServerRun 514            

    其它的日志文件：
        /var/log/secure：系统安装日志，应该周期性分析；
        /var/log/btmp：当前系统上，用户的失败尝试登录相关的日志信息，lastb命令进行查看；
        /var/log/wtmp：当前系统上，用户正常 登录系统的相关日志信息，last命令可以查看；

            lastlog命令：用于查看当前系统上每一个用户最近一次的登录信息；

        /var/log/messages：系统日志信息；
        /var/log/dmesg：系统引导过程中的日志信息；
            文本查看工具查看；
            也可以使用专用命令dmesg查看；

    rsyslog将日志记录于MySQL中： 
        (1) 准备MySQL Server；
        (2) 在mysql server上授权rsyslog能连接至当前服务器；
            mysql> GRANT ALL ON Syslog.* TO 'USER'@'HOST' IDENTIFIED BY 'PASSWORD';
        (3) 在rsyslog主机上安装mysql模块相关的程序包；
            yum install rsyslog-mysql
        (4) 为rsyslog创建数据库及表；
            mysql -uUSERNAME -hHOST -pPASSWORD < /usr/share/doc/rsyslog-7.4.7/mysql-createDB.sql
        (5) 配置rsyslog将日志保存到mysql中；
            #### MODULES ####
            $ModLoad ommysql

            #### RULES ####
            facility.priority      :ommysql:DBHOST,DBNAME,DBUSER,DBUSERPASSWORD

        (6) 重启rsyslog服务；

    通过loganalyzer展示数据库中的日志：
        (1) 准备amp或nmp组合；
            # yum install httpd php php-mysql php-gd

        (2) 安装LogAnalyzer：
            # tar xf loganalyzer-3.6.5.tar.gz
            # cp -a loganalyzer-3.6.5/src  /var/www/html/loganalyzer
            #  cd  /var/www/html/loganalyzer
            # touch config.php
            # chmod 666 config.php

        (3) 配置loganalyzer：
            systemctl start httpd.service

            http://HOST/loganalyzer

            MySQL Native, Syslog Fields, Monitorware

        (4) 安全加强
            #  cd  /var/www/html/loganalyzer
            # chmod 644 config.php

博客作业：rsyslog+mysql+loganalyzer
```

