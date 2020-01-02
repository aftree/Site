# tomcat

编程语言： 硬件级：微码编程，汇编语言 系统级：C，C++，... 应用级：Java, PHP, Python, Ruby, Perl, C\#, ... Python： PVM Standard Library Web Framework：Django, Flask, ... Java：JVM，JDK bash：bash explainer ...

```text
程序：指令+数据
    过程式编程：以指令为中心，数据服务于代码；
    对象式编程：以数据为中心，指令服务于数据；
        对象，方法（method）

Java：
    Sun, Green Project, Oak,  James Gosling;
    1995：Java 1.0, Write once, Run Anywhere;
    1996：JDK（Java Development Kit），包含类库、开发工具(javac)、JVM（SUN Classic VM）
        JDK 1.0,  Applet, AWT
    1997：JDK 1.1
    1998: JDK  1.2
        Sun分拆Java技术为三个方向：
            J2SE：Standard Edition
            J2EE：Enterprise Edition
            J2ME：Mobile Edition

        代表性技术：EJB，java plugin, Swing, JIT(Just In Time，即时编译)

    2000：JDK 1.3
        HotSpot VM
    2002：JDK 1.4

    2006：Sun开源了Java技术，GPL，建立一个称为OpenJDK组织；
        Java 2 SE, Java 2 EE, Java 2 ME

    2011：JDK 1.7
    2014：JDK 1.8
    2016：JDK 1.9

Java代码的运行：
    *.java(source code) --> javac --> *.class(bytecode)
        jvm：class loader，加载程序的类文件，及程序的类文件依赖到的其它的类文件而后运行； 整个运行表现为一个jvm进程；
            threads；

java技术体系：
    Java编程语言
    Java Class文件格式
    Java API 
    Java VM 
        class loader
        执行引擎

    JVM运行时区域：
        方法区：线程共享； 用于存储被JVM加载的class信息、常量、静态变量、方法等；
        堆：是jvm所管理的内存中占用空间最大的一部分；也是GC管理的主要区域；存储对象；
        Java栈：线程私有，存储 线程自己的局部变量；
        PC寄存器：线程私有的内存空间，程序的指令指针；
        本地方法栈：    

安装JDK
    了解当前的java环境：
         ~]# java  -version

    OpenJDK： 
        java-VERSION-openjdk：
            The OpenJDK runtime environment.
        java-VERSION-openjdk-headless：
             The OpenJDK runtime environment without audio and video support.
        java-VERSION-openjdk-devel：
            The OpenJDK development tools.

        CentOS 7：
            VERSION：1.6.0, 1.7.0, 1.8.0

        注意：多版本并存时，可使用 alternatives命令设定默认使用的版本；

    Oracle JDK：
        安装相应版本的rpm包；
            jdk-VERSION-OS-ARCH.rpm
            例如:jdk-1.8.0_25-linux-x64.rpm 

    注意：安装完成后，要配置JAVA_HOME环境变量，指向java的安装路径；
        OpenJDK：
            JAVA_HOME=/usr
        Oracle JDK:
            JAVA_HOME=/usr/java/jdk_VERSION



Java 2 EE：
    CGI: Common Gateway Interface

    Servlet：
        类库；web app；
        Servlet container, Servlet Engine

    JSP: Java Server Page
        <html>
            <title>TITLE</title>
            <body>
                <h1>...</h1>
                <%
                    ... java code...
                %>
            </body>
        </html>

        .jsp -->jasper--> .java --> javac --> .class --> jvm 

        注意：基于jasper将静态输出的数据转为java代码进行输出，结果为servlet规范的代码；

    JSP Container：
        JSP + Servlet Container

        Java Web Server：JWS
        ASF：JServ

            Tomcat 3.x
            Tomcat 4.x
                Catalina

            http://tomcat.apache.org/

        商业实现：
            WebSphere, WebLogic, Oc4j, Glassfish, Geronimo, JOnAS, JBoss, ...
        开源实现：
            Tomcat, Jetty, Resin, ...

        Tomcat： Java 2 EE技术体系的不完整实现； 
```

回顾： java技术体系： Java编程语言、Java Class文件格式、Java API、JVM

```text
java的技术方向：
    J2SE --> JAVA 2 SE, JDK
    j2EE --> JAVA 2 EE, 
        Servlet, JSP, JMX, ...
    J2ME --> JAVA 2 ME

Servlet Container：
    JDK + Servlet 

JSP Container：
    JSP + Servlet Container

JVM运行时区域：方法区、堆、Java栈、PC寄存器、本地方法栈；

Tomcat：事先部署JDK；
    Java 2 EE不完整实现；
```

Tomcat（2）

```text
Tomcat：
    使用java语言编写：

    Tomcat的核心组件：server.xml
        <Server>
            <Service>
                <connector/>
                <connector/>
                ...
                <Engine>
                    <Host>
                        <Context/>
                        <Context/>
                        ...
                    </Host>
                    <Host>
                        ...
                    </Host>
                    ...
                </Engine>
            </Service>
        </Server>

        每一个组件都由一个Java“类”实现，这些组件大体可分为以下几个类型：
            顶级组件：Server
            服务类组件：Service
            连接器组件：http, https, ajp
            容器类：Engine, Host, Context
            被嵌套类：valve, logger, realm, loader, manager, ...
            集群类组件：listener, cluster, ...

    安装Tomcat:
        Base Repo：
            tomcat, tomcat-lib, tomcat-admin-webapps, tomcat-webapps, tomcat-docs-webapp

        Tomcat binary release：
            # tar xf apache-tomcat-VERSION.tar.gz  -C /usr/local/
            # cd /usr/local
            # ln -sv apache-tomcat-VERSION  tomcat


            /etc/profile.d/tomcat.sh 
                export CATALINA_BASE=/usr/local/tomcat
                export PATH=$CATALINA_BASE/bin:$PATH    

tomcat程序环境：

    tomcat的目录结构
        bin：脚本，及启动时用到的类；
        conf：配置文件目录；
        lib：库文件，Java类库，jar；
        logs：日志文件目录；
        temp：临时文件目录；
        webapps：webapp的默认目录；
        work：工作目录；

    rpm包安装的程序环境：
        配置文件目录：/etc/tomcat
            主配置文件：server.xml 
        webapps存放位置：/var/lib/tomcat/webapps/
            examples
            manager
            host-manager
            docs
        Unit File：tomcat.service
        环境配置文件：/etc/sysconfig/tomcat

    tomcat的配置文件：
        server.xml：主配置文件；
        web.xml：每个webapp只有“部署”后才能被访问，它的部署方式通常由web.xml进行定义，其存放位置为WEB-INF/目录中；此文件为所有的webapps提供默认配置；
        context.xml：每个web都可以专用的配置文件，它通常由专用的配置文件context.xml来定义，其存放位置为WEB-INF/目录中；此文件为所有的webapps提供默认配置；
        tomcat-users.xml：用户认证的账号和密码文件；
        catalina.policy：当使用-security选项启动tomcat时，用于为tomcat设置安全策略； 
        catalina.properties：Java属性的定义文件，用于设定类加载器路径，以及一些与JVM调优相关参数；
        logging.properties：日志系统相关的配置；                

    # catalina.sh --help
        debug             Start Catalina in a debugger
        debug -security   Debug Catalina with a security manager
        jpda start        Start Catalina under JPDA debugger
        run               Start Catalina in the current window
        run -security     Start in the current window with security manager
        start             Start Catalina in a separate window
        start  -security   Start in a separate window with security manager
        stop              Stop Catalina, waiting up to 5 seconds for the process to end
        stop n            Stop Catalina, waiting up to n seconds for the process to end
        stop -force       Stop Catalina, wait up to 5 seconds and then use kill -KILL if still running
        stop n -force     Stop Catalina, wait up to n seconds and then use kill -KILL if still running
        configtest        Run a basic syntax check on server.xml - check exit code for result
        version           What version of tomcat are you running?    

    JSP WebAPP的组织结构：
        /: webapps的根目录
            index.jsp：主页；
            WEB-INF/：当前webapp的私有资源路径；通常用于存储当前webapp的web.xml和context.xml配置文件；
            META-INF/：类似于WEB-INF/；
            classes/：类文件，当前webapp所提供的类；
            lib/：类文件，当前webapp所提供的类，被打包为jar格式；

    webapp归档格式：
        .war：webapp
        .jar：EJB的类打包文件；
        .rar：资源适配器类打包文件；
        .ear：企业级webapp；

部署(deploy)webapp的相关操作：
    deploy：将webapp的源文件放置于目标目录(网页程序文件存放目录)，配置tomcat服务器能够基于web.xml和context.xml文件中定义的路径来访问此webapp；将其特有的类和依赖的类通过class loader装载至JVM；
        部署有两种方式：
            自动部署：auto deploy
            手动部署:
                冷部署：把webapp复制到指定的位置，而后才启动tomcat；
                热部署：在不停止tomcat的前提下进行部署；
                    部署工具：manager、ant脚本、tcd(tomcat client deployer)等；                    
    undeploy：反部署，停止webapp，并从tomcat实例上卸载webapp；
    start：启动处于停止状态的webapp；
    stop：停止webapp，不再向用户提供服务；其类依然在jvm上；
    redeploy：重新部署；

手动提供一测试类应用，并冷部署：
    # mkidr  -pv  /usr/local/tomcat/webapps/test/{classes,lib,WEB-INF}
    创建文件/usr/local/tomcat/webapps/test/index.jsp 
        <%@ page language="java" %>
        <%@ page import="java.util.*" %>
        <html>
            <head>
                <title>Test Page</title>
            </head>
            <body>
                <% out.println("hello world");
                %>
            </body>
        </html>        

tomcat的两个管理应用:
    manager
    host-manager

tomcat的常用组件配置：

    Server：代表tomcat instance，即表现出的一个java进程；监听在8005端口，只接收“SHUTDOWN”。各server监听的端口不能相同，因此，在同一物理主机启动多个实例时，需要修改其监听端口为不同的端口； 

    Service：用于实现将一个或多个connector组件关联至一个engine组件；

    Connector组件：
        负责接收请求，常见的有三类http/https/ajp；

        进入tomcat的请求可分为两类：
            (1) standalone : 请求来自于客户端浏览器；
            (2) 由其它的web server反代：来自前端的反代服务器；
                nginx --> http connector --> tomcat 
                httpd(proxy_http_module) --> http connector --> tomcat
                httpd(proxy_ajp_module) --> ajp connector --> tomcat 

        属性：
            port="8080" 
            protocol="HTTP/1.1"
            connectionTimeout="20000"

            address：监听的IP地址；默认为本机所有可用地址；
            maxThreads：最大并发连接数，默认为200；
            enableLookups：是否启用DNS查询功能；
            acceptCount：等待队列的最大长度；
            secure：
            sslProtocol：

    Engine组件：Servlet实例，即servlet引擎，其内部可以一个或多个host组件来定义站点； 通常需要通过defaultHost来定义默认的虚拟主机；

        属性：
            name=
            defaultHost="localhost"
            jvmRoute=

    Host组件：位于engine内部用于接收请求并进行相应处理的主机或虚拟主机，示例：
         <Host name="localhost"  appBase="webapps"
            unpackWARs="true" autoDeploy="true">
        </Host>

        常用属性说明：
            (1) appBase：此Host的webapps的默认存放目录，指存放非归档的web应用程序的目录或归档的WAR文件目录路径；可以使用基于$CATALINA_BASE变量所定义的路径的相对路径；
            (2) autoDeploy：在Tomcat处于运行状态时，将某webapp放置于appBase所定义的目录中时，是否自动将其部署至tomcat；

            示例：
              <Host name="tc1.magedu.com" appBase="/appdata/webapps" unpackWARs="true" autoDeploy="true">
            </Host>

            # mkdir -pv /appdata/webapps
            # mkdir -pv /appdata/webapps/ROOT/{lib,classes,WEB-INF}
            提供一个测试页即可；

    Context组件:
        示例：
            <Context path="/PATH" docBase="/PATH/TO/SOMEDIR" reloadable=""/>

    综合示例：
        <Host name="node1.magedu.com" appBase="/web/apps" unpackWARs="true" autoDeploy="true">
            <Valve className="org.apache.catalina.valves.AccessLogValve" directory="logs"
                prefix="node1_access" suffix=".log"
                pattern="%h %l %u %t &quot;%r&quot; %s %b" />
            <Context path="/test" docBase="test" reloadable="">
                <Valve className="org.apache.catalina.valves.AccessLogValve" directory="logs"
                prefix="node1_test_access_" suffix=".log"
                pattern="%h %l %u %t &quot;%r&quot; %s %b" />
            </Context>
        </Host>        

    Valve组件：
        <Valve className="org.apache.catalina.valves.AccessLogValve" directory="logs"
            prefix="localhost_access_log" suffix=".txt"
            pattern="%h %l %u %t &quot;%r&quot; %s %b" />

        Valve存在多种类型：
            定义访问日志：org.apache.catalina.valves.AccessLogValve
            定义访问控制：org.apache.catalina.valves.RemoteAddrValve 

             <Valve className="org.apache.catalina.valves.RemoteAddrValve" deny="172\.16\.100\.67"/>



LNMT：Linux Nginx MySQL Tomcat 
    Client (http) --> nginx (reverse proxy)(http) --> tomcat  (http connector)

    location / {
        proxy_pass http://tc1.magedu.com:8080;
    }

    location ~* \.(jsp|do)$ {
        proxy_pass http://tc1.magedu.com:8080;
    }

LAMT：Linux Apache(httpd) MySQL Tomcat 
    httpd的代理模块：
        proxy_module
        proxy_http_module：适配http协议客户端；
        proxy_ajp_module：适配ajp协议客户端；

    Client (http) --> httpd (proxy_http_module)(http) --> tomcat  (http connector)
    Client (http) --> httpd (proxy_ajp_module)(ajp) --> tomcat  (ajp connector)
    Client (http) --> httpd (mod_jk)(ajp) --> tomcat  (ajp connector)

    proxy_http_module代理配置示例：
        <VirtualHost *:80>
            ServerName      tc1.magedu.com
            ProxyRequests Off
            ProxyVia        On
            ProxyPreserveHost On
            <Proxy *>
                Require all granted
            </Proxy>
            ProxyPass / http://tc1.magedu.com:8080/ 
            ProxyPassReverse / http://tc1.magedu.com:8080/ 
            <Location />
                Require all granted
            </Location>
        </VirtualHost>

    proxy_ajp_module代理配置示例：
        <VirtualHost *:80>
            ServerName      tc1.magedu.com
            ProxyRequests Off
            ProxyVia        On
            ProxyPreserveHost On
            <Proxy *>
                Require all granted
            </Proxy>
            ProxyPass / ajp://tc1.magedu.com:8009/ 
            ProxyPassReverse / ajp://tc1.magedu.com:8009/ 
            <Location />
                Require all granted
            </Location>
        </VirtualHost>

课外实践：client --> nginx --> httpd --> tomcat
proxy_http_module)(http) --> tomcat  (http connector)
    Client (http) --> httpd (proxy_ajp_module)(ajp) --> tomcat  (ajp connector)
    Client (http) --> httpd (mod_jk)(ajp) --> tomcat  (ajp connector)

    proxy_http_module代理配置示例：
        <VirtualHost *:80>
            ServerName      tc1.magedu.com
            ProxyRequests Off
            ProxyVia        On
            ProxyPreserveHost On
            <Proxy *>
                Require all granted
            </Proxy>
            ProxyPass / http://tc1.magedu.com:8080/ 
            ProxyPassReverse / http://tc1.magedu.com:8080/ 
            <Location />
                Require all granted
            </Location>
        </VirtualHost>

    proxy_ajp_module代理配置示例：
        <VirtualHost *:80>
            ServerName      tc1.magedu.com
            ProxyRequests Off
            ProxyVia        On
            ProxyPreserveHost On
            <Proxy *>
                Require all granted
            </Proxy>
            ProxyPass / ajp://tc1.magedu.com:8009/ 
            ProxyPassReverse / ajp://tc1.magedu.com:8009/ 
            <Location />
                Require all granted
            </Location>
        </VirtualHost>

课外实践：client --> nginx --> httpd --> tomcat


会话保持：
    (1) session sticky
        source_ip
            nginx: ip_hash
            haproxy: source
            lvs: sh
        cookie：
            nginx：hash 
            haproxy: cookie
    (2) session cluster：delta session manager
    (3) session server：redis(store), memcached(cache)

Tomcat Cluster(session)
    (1) session sticky
    (2) session cluster
        tomcat delta manager
    (3) session server 
        memcached

Tomcat Cluster
    (1) httpd + tomcat cluster
        httpd: mod_proxy, mod_proxy_http, mod_proxy_balancer
        tomcat cluster：http connector
    (2) httpd + tomcat cluster
        httpd: mod_proxy, mod_proxy_ajp, mod_proxy_balancer
        tomcat cluster：ajp connector
    (3) httpd + tomcat cluster
        httpd: mod_jk
        tomcat cluster：ajp connector
    (4) nginx + tomcat cluster


    第一种方法的实现：
        <proxy balancer://tcsrvs>
            BalancerMember http://172.18.100.67:8080
            BalancerMember http://172.18.100.68:8080
            ProxySet lbmethod=byrequests
        </Proxy>

        <VirtualHost *:80>
            ServerName lb.magedu.com
            ProxyVia On
            ProxyRequests Off
            ProxyPreserveHost On
            <Proxy *>
                Require all granted
            </Proxy>
            ProxyPass / balancer://tcsrvs/
            ProxyPassReverse / balancer://tcsrvs/
            <Location />
                Require all granted
            </Location>
        </VirtualHost>        

    会话粘性的实现方法：
        Header add Set-Cookie "ROUTEID=.%{BALANCER_WORKER_ROUTE}e; path=/" env=BALANCER_ROUTE_CHANGED

        <proxy balancer://tcsrvs>
            BalancerMember http://172.18.100.67:8080 route=TomcatA loadfactor=1
            BalancerMember http://172.18.100.68:8080 route=TomcatB loadfactor=2
            ProxySet lbmethod=byrequests
            ProxySet stickysession=ROUTEID
        </Proxy>

        <VirtualHost *:80>
            ServerName lb.magedu.com
            ProxyVia On
            ProxyRequests Off
            ProxyPreserveHost On
            <Proxy *>
                Require all granted
            </Proxy>
            ProxyPass / balancer://tcsrvs/
            ProxyPassReverse / balancer://tcsrvs/
            <Location />
                Require all granted
            </Location>
        </VirtualHost>    

        启用管理接口：
            <Location /balancer-manager>
                SetHandler balancer-manager
                ProxyPass !
                Require all granted
            </Location>                

            示例程序：
                演示效果，在TomcatA上某context中（如/test），提供如下页面
                <%@ page language="java" %>
                <html>
                    <head><title>TomcatA</title></head>
                    <body>
                        <h1><font color="red">TomcatA.magedu.com</font></h1>
                        <table align="centre" border="1">
                            <tr>
                                <td>Session ID</td>
                            <% session.setAttribute("magedu.com","magedu.com"); %>
                                <td><%= session.getId() %></td>
                            </tr>
                            <tr>
                                <td>Created on</td>
                                <td><%= session.getCreationTime() %></td>
                            </tr>
                        </table>
                    </body>
                </html>


                演示效果，在TomcatB上某context中（如/test），提供如下页面
                <%@ page language="java" %>
                <html>
                    <head><title>TomcatB</title></head>
                        <body>
                        <h1><font color="blue">TomcatB.magedu.com</font></h1>
                        <table align="centre" border="1">
                            <tr>
                                <td>Session ID</td>
                            <% session.setAttribute("magedu.com","magedu.com"); %>
                                <td><%= session.getId() %></td>
                            </tr>
                            <tr>
                                <td>Created on</td>
                                <td><%= session.getCreationTime() %></td>
                            </tr>
                        </table>
                        </body>
                </html>            

    第二种方式：
            <proxy balancer://tcsrvs>
                BalancerMember ajp://172.18.100.67:8009
                BalancerMember ajp://172.18.100.68:8009
                ProxySet lbmethod=byrequests
            </Proxy>

            <VirtualHost *:80>
                ServerName lb.magedu.com
                ProxyVia On
                ProxyRequests Off
                ProxyPreserveHost On
                <Proxy *>
                    Require all granted
                </Proxy>
                ProxyPass / balancer://tcsrvs/
                ProxyPassReverse / balancer://tcsrvs/
                <Location />
                    Require all granted
                </Location>
                <Location /balancer-manager>
                    SetHandler balancer-manager
                    ProxyPass !
                    Require all granted
                </Location>
            </VirtualHost>    

        保持会话的方式参考前一种方式。    

Tomcat Session Replication Cluster：
        (1) 配置启用集群，将下列配置放置于<engine>或<host>中；
            <Cluster className="org.apache.catalina.ha.tcp.SimpleTcpCluster"
                    channelSendOptions="8">

                <Manager className="org.apache.catalina.ha.session.DeltaManager"
                    expireSessionsOnShutdown="false"
                    notifyListenersOnReplication="true"/>

                <Channel className="org.apache.catalina.tribes.group.GroupChannel">
                <Membership className="org.apache.catalina.tribes.membership.McastService"
                        address="228.0.0.4"
                        port="45564"
                        frequency="500"
                        dropTime="3000"/>
                <Receiver className="org.apache.catalina.tribes.transport.nio.NioReceiver"
                    address="auto"
                    port="4000"
                    autoBind="100"
                    selectorTimeout="5000"
                    maxThreads="6"/>

                <Sender className="org.apache.catalina.tribes.transport.ReplicationTransmitter">
                <Transport className="org.apache.catalina.tribes.transport.nio.PooledParallelSender"/>
                </Sender>
                <Interceptor className="org.apache.catalina.tribes.group.interceptors.TcpFailureDetector"/>
                <Interceptor className="org.apache.catalina.tribes.group.interceptors.MessageDispatch15Interceptor"/>
                </Channel>

                <Valve className="org.apache.catalina.ha.tcp.ReplicationValve"
                    filter=""/>
                <Valve className="org.apache.catalina.ha.session.JvmRouteBinderValve"/>

                <Deployer className="org.apache.catalina.ha.deploy.FarmWarDeployer"
                    tempDir="/tmp/war-temp/"
                    deployDir="/tmp/war-deploy/"
                    watchDir="/tmp/war-listen/"
                    watchEnabled="false"/>

                <ClusterListener className="org.apache.catalina.ha.session.JvmRouteSessionIDBinderListener"/>
                <ClusterListener className="org.apache.catalina.ha.session.ClusterSessionListener"/>
                </Cluster>    

            确保Engine的jvmRoute属性配置正确。

        (2) 配置webapps
            编辑WEB-INF/web.xml，添加<distributable/>元素；

Tomcat的常用优化配置：
    (1) 内存空间：
        /etc/sysconfig/tomcat
            JAVA_OPTS="-server -Xms -Xmx -XX:NewSize= -XX:MaxNewSize= -XX:PermSize= -XX:MaxPermSize="
                -server：服务器模型
                -Xms：堆内存初始化大小；
                -Xmx：堆内存空间上限；
                -XX:NewSize=：新生代空间初始化大小；                    
                -XX:MaxNewSize=：新生代空间最大值；
                -XX:PermSize=：持久代空间初始化大小；
                -XX:MaxPermSize=：持久代空间最大值； 

    (2) 线程池设置：
        <Connector port="8080" protocol="HTTP/1.1"  connectionTimeout="20000" redirectPort="8443" />

        常用属性：
            maxThreads：最大线程数；
            minSpareThreads：最小空闲线程数；
            maxSpareThreads：最大空闲线程数；
            acceptCount：等待队列的最大长度；
            URIEncoding：URI地址编码格式，建议使用UTF-8；
            enableLookups：是否启用dns解析，建议禁用； 
            compression：是否启用传输压缩机制，建议“on";
            compressionMinSize：启用压缩传输的数据流最小值，单位是字节；
            compressableMimeType：定义启用压缩功能的MIME类型；
                text/html, text/xml, text/css, text/javascript


    (3) 禁用8005端口；
        <Server port="-1" shutdown="SHUTDOWN">

    (4) 隐藏版本信息：
        <Connector port="8080" protocol="HTTP/1.1"  connectionTimeout="20000" redirectPort="8443" />

        Server="SOME STRING"

Tomcat Session Server (memcached)

    https://github.com/magro/memcached-session-manager

    支持的session server类型：
        memcached：
        couchbase：
        redis：

博客作业：tomcat
    (1) nginx + tomcat cluster, httpd(proxy_http_module)+tomcat cluster, httpd(proxy_ajp_module)+tomcat cluster；
        特别说明会话保持的实现方式；
    (2) tomcat cluster升级为session cluster, 使用deltaManager；
    (3) tomcat cluster将会话保存至memcached中；

JVM常用的分析工具： 
    jps：用来查看运行的所有jvm进程；
    jinfo：查看进程的运行环境参数，主要是jvm命令行参数；
    jstat：对jvm应用程序的资源和性能进行实时监控；
    jstack：查看所有线程的运行状态；
    jmap：查看jvm占用物理内存的状态；
    jconsole：
    jvisualvm：

    jps：Java virutal machine Process Status tool，
        jps [-q] [-mlvV] [<hostid>]
            -q：静默模式；
            -v：显示传递给jvm的命令行参数；
            -m：输出传入main方法的参数；
            -l：输出main类或jar完全限定名称；
            -V：显示通过flag文件传递给jvm的参数；
            [<hostid>]：主机id，默认为localhost；

    jinfo：输出给定的java进程的所有配置信息；
        jinfo [option] <pid>
            -flags：to print VM flags
            -sysprops：to print Java system properties
            -flag <name>：to print the value of the named VM flag

    jstack：查看指定的java进程的线程栈的相关信息；
        jstack [-l] <pid>
        jstack -F [-m] [-l] <pid>
            -l：long listings，会显示额外的锁信息，因此，发生死锁时常用此选项；
            -m：混合模式，既输出java堆栈信息，也输出C/C++堆栈信息；
            -F：当使用“jstack -l PID"无响应，可以使用-F强制输出信息；

    jstat：输出指定的java进程的统计信息
        jstat -help|-options
        jstat -<option> [-t] [-h<lines>] <vmid> [<interval> [<count>]]

        # jstat -options
            -class：class loader
            -compiler：JIT
            -gc：gc
            -gccapacity：统计堆中各代的容量
            -gccause：
            -gcmetacapacity
            -gcnew：新生代
            -gcnewcapacity
            -gcold：老年代
            -gcoldcapacity
            -gcutil
            -printcompilation

        [<interval> [<count>]]
            interval：时间间隔，单位是毫秒；
            count：显示的次数；

        -gc：
            YGC：新生代的垃圾回收次数；
            YGCT：新生代垃圾回收消耗的时长； 
            FGC：Full GC的次数；
            FGCT：Full GC消耗的时长；
            GCT：GC消耗的总时长；

    jmap：Memory Map, 用于查看堆内存的使用状态； 
    jhat：Java Heap Analysis Tool
        jmap [option] <pid>

        查看堆空间的详细信息：
            jmap -heap <pid>

        查看堆内存中的对象的数目：
            jmap -histo[:live] <pid>
                live：只统计活动对象；

        保存堆内存数据至文件中，而后使用jvisualvm或jhat进行查看：
            jmap -dump:<dump-options> <pid>
                dump-options:
                live         dump only live objects; if not specified, all objects in the heap are dumped.
                format=b     binary format
                file=<file>  dump heap to <file>    

课外作业：了解何为full gc，如何应对；
```

session会话保持之session服务器

前提： 两个tomcat节点：172.16.100.7\(tomcatA.magedu.com\)，172.16.100.8\(tomcatB.magedu.com\) 两个memcached节点：172.16.100.9, 172.16.100.10 一个负载均衡节点：172.16.100.6

Clients--&gt;172.16.100.6--&gt;\(tomcatA, tomcatB\)

memcached-session-manager项目地址，[http://code.google.com/p/memcached-session-manager/](http://code.google.com/p/memcached-session-manager/), [https://github.com/magro/memcached-session-manager](https://github.com/magro/memcached-session-manager)

下载如下jar文件至各tomcat节点的tomcat安装目录下的lib目录中，其中的${version}要换成你所需要的版本号，tc${6,7,8}要换成与tomcat版本相同的版本号。 memcached-session-manager-${version}.jar memcached-session-manager-tc${6,7,8}-${version}.jar spymemcached-${version}.jar msm-javolution-serializer-${version}.jar javolution-${version}.jar

分别在两个tomcat上的某host上定义一个用于测试的context容器，并在其中创建一个会话管理器，如下所示：

分别为两个context提供测试页面：

tomcatA:

## mkdir -pv /usr/local/tomcat/webapps/test/WEB-INF/{classes,lib}

## vim /usr/local/tomcat/webapps/test/index.jsp

添加如下内容： &lt;%@ page language="java" %&gt;

TomcatA

## TomcatA.magedu.com

| Session ID |  |
| :--- | :--- |
| Created on |  |

tomcatB:

## mkdir -pv /usr/local/tomcat/webapps/test/WEB-INF/{classes,lib}

## vim /usr/local/tomcat/webapps/test/index.jsp

添加如下内容： &lt;%@ page language="java" %&gt;

TomcatB

## TomcatB.magedu.com

| Session ID |  |
| :--- | :--- |
| Created on |  |

在172.16.100.6上配置反向代理的负载均衡内容，类似如下所示：

 BalancerMember http://172.16.100.7:8080 loadfactor=1 BalancerMember http://172.16.100.8:8080 loadfactor=1 ProxySet lbmethod=byrequests

ProxyVia Off ProxyRequests Off ProxyPass / balancer://tomcat/ ProxyPassReverse / balancer://tomcat/

 Order Allow,Deny Allow From all

 Order Allow,Deny Allow From all

测试结果，在浏览器中访问[http://172.16.100.6/test，结果如下所示，其session](http://172.16.100.6/test，结果如下所示，其session) ID在负载均衡环境中保持不变。

TomcatA.magedu.com

Session ID 4DD0340CE6294FF2BBE802CD4CD039EC-n2 Created on 1399890838103

TomcatB.magedu.com

Session ID 4DD0340CE6294FF2BBE802CD4CD039EC-n2 Created on 1399890838103

