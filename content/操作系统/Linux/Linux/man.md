# 获取帮助

00零.whatis为例，查找帮助信息思路  
type Command  
whatis Command  
man Command  
Command --help/-h  
info Command  
/usr/share/doc/  
本地帮助文档/usr/share/doc/  
官网在线文档  
Red Hat documentation  
其它网站和搜索  
whatis  
显示命令的简短描述  
每晚使用一个数据库更新  
刚安装后不可立即使用  
makewhatis 版本6 \| mandb 版本7 制作数据库

```text
    在学习man命令时候，估计很多朋友都发现有man –f ,man –k 参数，可以查出很多数据。这些有的与man手册页相同，有些不是属于手册页的。它们数据怎么收集来的，并且whatis是怎么样工作的。

    # man -h
    ...
     f:same as whatis(1)
    ...
    #觉得比较奇怪，whatis是什么呢？
    [chengmo@centos5 ~]$ man whatis
    #查询得到是：
    #whatis - search the whatis database for complete words. 
    #它是查询whatis数据库的工具
    #The whatis database is created using the command /usr/sbin/makewhatis.
    #里面还说，whatis数据库 是通过/usr/bin/makewhatis建立的
    收获：whatis数据库，并且有makewhatis创建，whatis脚本是用作查询的
    makewhatis是怎么样工作的呢？
    [chengmo@centos5 ~]$ man makewhatis
    #得到：
    makewhatis reads all the manual pages contained in the given sections of manpath or the preformatted pages con-tained in the given sections of catpath. For each page, it writes a line in the whatis database; each line consists of the name of the page and a short description, separated by a dash. The description is extracted using the content of the NAME section of the manual page.
    #大概意思是：makewhatis 从手册页配置的路径以及领域范围，搜集所有手册页索引信息，每个手册页在数据库加入一行，这行会包括手册页里面name,以及简单描述。
    收获：知道这个数据库是建立是索引，并且每个数据库写入一行(a line) ,会不会这个数据库就是文本文件呢？现在到这里，我们不知道数据库保存地方，也不知道它结构，只有看看whatis命令，看它是不是有源码信息
    怎么得到whatis程序源码?

    # type whatis
    whatis is /usr/bin/whatis
    #告诉路径我们看看内容

    # vi /usr/bin/whatis
    1 #!/bin/sh
     2 #
     3 # apropos -- search the whatis database for keywords.
     4 # whatis -- idem, but match only commands (as whole words).
    ……
    #它是个sh脚本，
    #得到：
    #grep “关键词“ /var/cache/man/whatis
    #它实际在查找这个文件，/var/cache/man/whatis就是上面说的whatis 数据库
    [chengmo@centos5 ~]$ head /var/cache/man/whatis 
    $notes_name [Module::Build::Notes] (3pm) - Configuration for $module_name
    *_unlocked [unlocked_stdio] (3) - non-locking stdio functions
    #whatis数据库就是一个文本文件记录手册页的索引信息
    whatis数据库是什么时候创建的呢？
    # ls -al /var/cache/man/whatis 
    -rw-r--r-- 1 root root 1057156 10-27 04:06 /var/cache/man/whatis
    #发现创建时间是凌晨4点左右，看了这个是系统创建的，那么少不了cron怀疑了

    # cat /etc/crontab 
    SHELL=/bin/bash
    PATH=/sbin:/bin:/usr/sbin:/usr/bin
    MAILTO=root
    HOME=/
    # run-parts
    01 * * * * root run-parts /etc/cron.hourly
    02 4 * * * root run-parts /etc/cron.daily
    22 4 * * 0 root run-parts /etc/cron.weekly
    42 4 1 * * root run-parts /etc/cron.monthly
    有run-parts脚本运行/etc/cron.daily目录下面的文件

    # cd /etc/cron.daily/
    [chengmo@centos5 cron.daily]$ cat makewhatis.cron 
    #!/bin/bash
    LOCKFILE=/var/lock/makewhatis.lock
    makewhatis -u -w
    ……
    找到了这个是由这个脚本运行的
    以上是通过man命令查找帮助信息的一个过程，有问题，首先分析帮助是个不错的选择。
```

一.help Command 内部命令

```text
     # type cd
       cd is a shell builtin
   - # help cd
   - # man bash   搜索关键字查看
```

二.Command --help/-h 外部命令  
\[\] 表示可选项  
CAPS或 或 &lt;&gt; 表示变化的数据  
... 表示一个列表  
x \|y\| z “ 的意思是“ x 或 或 y 或 或 z “  
-abc的 的 意思是-a -b –c  
{ } 表示分组

```text
     # date --help
     Usage: date [OPTION]... [+FORMAT]                           # 这里有基本语法
       or:  date [-u|--utc|--universal] [MMDDhhmm[[CC]YY][.ss]]  # 这是设定时间的语法
     Display the current time in the given FORMAT, or set the system date.
     # 底下是主要的选项说明
     Mandatory arguments to long options are mandatory for short options too.
      -d, --date=STRING         display time described by STRING, not 'now'
      -f, --file=DATEFILE       like --date once for each line of DATEFILE
     ....(中间省略)....
       -u, --utc, --universal    print or set Coordinated Universal Time (UTC)
          --help     显示此求助说明并离开
          --version  显示版本信息并离开
     # 底下则是重要的格式 (FORMAT) 的主要项目
     FORMAT controls the output.  Interpreted sequences are:

      %%   a literal %
      %a   locale's abbreviated weekday name (e.g., Sun)
      %A   locale's full weekday name (e.g., Sunday)
     ....(中间省略)....
     # 底下是几个重要的范例 (Example)
     Examples:
     Convert seconds since the epoch (1970-01-01 UTC) to a date
       $ date --date='@2147483647'
     ....(底下省略)....
     看一下上面的显示，首先一开始是下达语法的方式 (Usage)，这个 date 有两种基本语法，一种是直接下达并且取得日期回传值，且可以 +FORAMAT 的方式来显示。 至于另一种方式，则是加上 MMDDhhmmCCYY 的方式来设定日期时间。他的格式是『月月日日时时分分公元年』的格式！再往下看， 会说明主要的选项，例如 -d 的意义等等，后续又会出现 +FORMAT 的用法！从里面你可以查到我们之前曾经用过得『 date +%Y%m%d 』这个指令与选项的说明。
```

三.man 手册页存放：/usr/share/man/ man 命令的配置文件：/etc/man.config \| man\_db.conf  
man是manual\(操作说明\)的简写

```text
      # man date
      DATE(1)                          User Commands                         DATE(1)
      # 请注意上面这个括号内的数字
      NAME  <==这个指令的完整全名，如下所示为date且说明简单用途为设定与显示日期/时间
             date - print or set the system date and time

      SYNOPSIS  <==这个指令的基本语法如下所示
             date [OPTION]... [+FORMAT]                            <==第一种单纯显示的用法
             date [-u|--utc|--universal] [MMDDhhmm[[CC]YY][.ss]]   <==这种可以设定系统时间的用法

      DESCRIPTION  <==详细说明刚刚语法谈到的选项与参数的用法
             Display the current time in the given FORMAT, or set the system date.

             Mandatory arguments to long options are mandatory for short options too.

             -d, --date=STRING  <==左边-d为短选项名称，右边--date为完整选项名称
                    display time described by STRING, not 'now'

             -f, --file=DATEFILE
                    like --date once for each line of DATEFILE

             -I[TIMESPEC], --iso-8601[=TIMESPEC]
                    output  date/time  in  ISO  8601  format.   TIMESPEC='date' for date only (the
                    default), 'hours', 'minutes', 'seconds', or 'ns' for  date  and  time  to  the
                    indicated precision.
      ....(中间省略)....# 找到了！底下就是格式化输出的详细数据！
             FORMAT controls the output.  Interpreted sequences are:

             %%     a literal %

             %a     locale's abbreviated weekday name (e.g., Sun)

             %A     locale's full weekday name (e.g., Sunday)
      ....(中间省略)....
      ENVIRONMENT  <==与这个指令相关的环境参数有如下的说明
             TZ     Specifies the timezone, unless overridden by command line parameters. 
                    If neither is specified, the setting from /etc/localtime is used.

      EXAMPLES     <==一堆可用的范本
             Convert seconds since the epoch (1970-01-01 UTC) to a date

                    $ date --date='@2147483647'
      ....(中间省略)....

      DATE STRING  <==上面曾提到的 --date 的格式说明！
             The --date=STRING is a mostly free format human readable date string such as "Sun, 29
             Feb 2004 16:21:42 -0800" or "2004-02-29 16:21:42" or even "next  Thursday".   A  date
             string  may  contain  items  indicating calendar date, time of day, time zone, day of

      AUTHOR  <==这个指令的作者啦！
             Written by David MacKenzie.

      COPYRIGHT  <==受到著作权法的保护！用的就是 GPL 了！
             Copyright © 2013 Free Software Foundation, Inc.  License GPLv3+: GNU GPL version 3 or
             later <http://gnu.org/licenses/gpl.html>.
             This  is free software: you are free to change and redistribute it.  There is NO WAR‐
             RANTY, to the extent permitted by law.

      SEE ALSO  <==这个重要，你还可以从哪里查到与date相关的说明文件之意
             The full documentation for date is maintained as a Texinfo manual.  If the  info  and
             date programs are properly installed at your site, the command

                    info coreutils 'date invocation'

             should give you access to the complete manual.

      GNU coreutils 8.22                        June 2014                                  DATE(1)
      Tips进入man指令的功能后，你可以按下『空格键』往下翻页，可以按下『 q 』按键来离开man的环境。 更多在man指令下的功能，本小节后面会谈到的！

      数字的意义：
      1    普通用户可执行的命令
      2    系统核心调用的函数与工具等
      3    C常用的函数(function)与函式库(library)，大部分为C的函式库(libc)
      4    设备文件及特殊文件的说明，通常在/dev下的文件
      5    配置文件格式
      6    游戏(games)
      7    惯例与协议等，例如Linux文件系统、网络协议、ASCII code等等的说明
      8    系统管理员可用的命令
      9    kernel有关的文件

      NAME                名称及简要说明
      SYNOPSIS            用法格式说明
                         []   可选内容
                         <>   必选内容
                         a|b  二选一
                         { }  分组
                         ...  同一内容可出现多次
      DESCRIPTION          完整的说明
      OPTIONS              有列举的所有可用的选项说明
      COMMANDS            当这个程序(软件)在执行的时候，可以在此程序(软件)中下达的指令
      FILES                这个程序或数据所使用或参考或连结到的某些文件
      SEE ALSO            其他参考说明
      EXAMPLE              一些可以参考的范例
      AUTHOR            作者
      COPYRIGHT         版本信息
      REPORTING BUGS    Bug 信息





      首先， 如果要向下翻页的话，可以按下键盘的空格键，也可以使用[Page Up]与[Page Down]来翻页呢！同时，如果你知道某些关键词的话， 那么可以在任何时候输入『/word』，来主动搜寻关键词！例如在上面的搜寻当中，我输入了『/date』会变成怎样？
      DATE(1)                          User Commands                         DATE(1)

      NAME
             date - print or set the system date and time

      SYNOPSIS
             date [OPTION]... [+FORMAT]
             date [-u|--utc|--universal] [MMDDhhmm[[CC]YY][.ss]]

      DESCRIPTION
             Display  the  current  time  in  the given FORMAT, or set the system date.

      ....(中间省略)..../date<==只要按下/，光标就会跑到这个地方来，你就可以开始输入搜寻字符串咯
      看到了吗，当你按下『/』之后，光标就会移动到屏幕的最下面一行， 并等待你输入搜寻的字符串了。此时，输入date后，man page就会开始搜寻跟date有关的字符串， 并且移动到该区域呢！很方便吧！最后，如果要离开man page时，直接按下『 q 』就能够离开了。 我们将一些在man page常用的按键给他整理整理：

      按键                进行工作
      空格键              向下翻一页
      [Page Down]        向下翻一页
      [Page Up]          向上翻一页
      [Home]            去到第一页
      [End]              去到最后一页
      /string            向下搜寻 string 这个字符串，如果要搜寻 vbird 的话，就输入 /vbird
      ?string            向『上』搜寻 string 这个字符串
      n, N    利用 / 或 ? 来搜寻字符串时，可以用 n 来继续下一个搜寻 (不论是 / 或 ?) ，可以利用 N 来进行『反向』搜寻。
      q    结束这次的 man page
      翻屏：
```

向后翻一屏：SPACE  
向前翻一屏：b  
向后翻一行：ENTER  
向前翻一行：k

```text
      搜寻特定指令/档案的man page说明文件

      man只会找数据中的左边那个指令(或档案)的完整名称
      # man -f man
      man (1)              - an interface to the on-line reference manuals
      man (1p)             - display system documentation
      man (7)              - macros to format man pages

      # man 1 man<==这里是用 man(1) 的文件数据
      # man 7 man<==这里是用 man(7) 的文件数据
      搜寻的顺序是记录在/etc/man_db.conf ， 先搜寻到的那个说明档，就会先被显示出来！



      •    左边部分：指令(或档案)以及该指令所代表的意义(就是那个数字)；
      •    右边部分：这个指令的简易说明，例如上述的『-macros to format man pages』
      我想要同时找上面说的两个地方的内容，只要该内容有关键词存在， 不需要完全相同的指令(或档案)就能够找到时
      找出系统的说明文件中，只要有man这个关键词就将该说明列出来。

      # man -k man
      fallocate (2)        - manipulate file space
      zshall (1)           - the Z shell meta-man page
      ....(中间省略)....
      yum-config-manager (1) - manage yum configuration options and yum repositories
      yum-groups-manager (1) - create and edit yum's group metadata
      yum-utils (1)        - tools for manipulating repositories and extended package management
      因为这个是利用关键词将说明文件里面只要含有man那个字眼的(不见得是完整字符串) 就将他取出来！

      # whatis  [指令或者是数据]<==相当于 man -f [指令或者是数据]
      # apropos [指令或者是数据]<==相当于 man -k [指令或者是数据]

      而要注意的是，这两个特殊指令要能使用，必须要有建立 whatis 数据库才行！这个数据库的建立需要以 root 的身份下达如下的指令：
      # mandb# 旧版的 Linux 这个指令是使用 makewhatis 喔！这一版开使用 mandb 了！
```

四.info /usr/share/info/  
info 是将文件数据拆成段落，每个段落用自己的页面来撰写， 并且各个页面以超链接来跳到各不同的页面中，每个独立的页面也被称为一个节点\(node\)。

```text
    # info info
    File: info.info,  Node: Top,  Next: Getting Started,  Up: (dir)

    Info: An Introduction
    *********************

    The GNU Project distributes most of its on-line manuals in the "Info
    format", which you read using an "Info reader".  You are probably using
    an Info reader to read this now.
    ....(中间省略)....

       If you are new to the Info reader and want to learn how to use it,
    type the command 'h' now.  It brings you to a programmed instruction
    sequence. # 这一段在说明，按下 h 可以有简易的指令说明！很好用！....(中间省略)....

    * Menu:

    * Getting Started::             Getting started using an Info reader.
    * Advanced::                    Advanced Info commands.
    * Expert Info::                 Info commands for experts.
    * Index::                       An index of topics, commands, and variables.

    --zz-Info: (info.info.gz)Top, 52 lines --Bot------------------------------------------

    •    File：代表这个info page的资料是来自info.info档案所提供的；
    •    Node：代表目前的这个页面是属于Top节点。 意思是info.info内含有很多信息，而Top仅是info.info档案内的一个节点内容而已；
    •    Next：下一个节点的名称为Getting Started，你也可以按『N』到下个节点去；
    •    Up：回到上一层的节点总揽画面，你也可以按下『U』回到上一层；
    •    Prev：前一个节点。但由于Top是info.info的第一个节点，所以上面没有前一个节点的信息。

    按下N, P, U来去到下一个、上一个与上一层的节点(node)！ 第一行之后就是针对这个节点的说明。 

    --zz-Info: (info.info.gz)Top, 52 lines --Bot-------------------------------------------
    Basic Info command keys  # 这里是按下 h 之后才会出现的一堆简易按钮列说明！

    x           Close this help window.      # 按下 x 就可以关闭这个 help 的窗口
    q           Quit Info altogether.        # 完全离开 info page 喔！
    H           Invoke the Info tutorial.

    Up          Move up one line.
    Down        Move down one line.
    DEL         Scroll backward one screenful.
    SPC         Scroll forward one screenful.
    -----Info: *Info Help*, 405 lines --Top-------------------------------------------------


    info 按键

    按键    进行工作
    空格键    向下翻一页
    [Page Down]    向下翻一页
    [Page Up]    向上翻一页
    [tab]    在 node 之间移动，有 node 的地方，通常会以 * 显示。
    [Enter]    当光标在 node 上面时，按下 Enter 可以进入该 node 。
    b    移动光标到该 info 画面当中的第一个 node 处
    e    移动光标到该 info 画面当中的最后一个 node 处
    n    前往下一个 node 处
    p    前往上一个 node 处
    u    向上移动一层
    s(/)    在 info page 当中进行搜寻
    h, ?    显示求助选单
    q    结束这次的 info page
    info page是只有Linux上面才有的产物，而且易读性增强很多～不过查询的指令说明要具有info page功能的话，得用info page的格式来写成在线求助文件才行！我们CentOS 7将info page的文件放置到/usr/share/info/目录中！至于非以info page格式写成的说明文件(就是man page)，虽然也能够使用info来显示，不过其结果就会跟man相同。 举例来说，你可以下达『info man』就知道结果了！ ^_^
```

五.自带帮助文档\(documents\)  
/usr/share/doc  
---/usr/share/doc/xxx-tools-2.02  
---README Install ChangeLog \#程序自身的帮助文档  
原版软件释出的时候，都会有一些安装须知、预计工作事项、未来工作规划等等的东西，还有包括可安装的程序等。

六.优先参考项目官方文档  
发行版官方文档  
程序官方文档

七.百度谷歌  
遇到问题，二话不说百度谷歌

