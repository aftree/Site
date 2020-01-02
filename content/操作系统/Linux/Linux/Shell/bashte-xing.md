
    学习 shell 的原因主要有：文字接口的 shell 在各大 distribution 都一样；远程管理时文字接口速度较快； shell 是管理 Linux 系统非常重要的一环，因为 Linux 内很多控制都是以 shell 撰写的。

    Shell 是指一种应用程序，这个应用程序提供了一个界面，用户通过这个界面访问操作系统内核的服务。

    Linux的Shell种类众多，常见的有：
    Bourne Shell（/bin/sh）
    Bourne Again Shell（/bin/bash）Linux 预设的 shell
    C Shell（/usr/bin/csh）
    K Shell（/usr/bin/ksh）

    Bash的基础特性
  
    1 命令历史
    2 命令别名
    3 快捷键
    4 命令补全和路径补全
    5 命令行展开
    6 命令执行状态码
    7 glob通配符
    8 I/O重定向和管道
    9 变量
    10 进程控制
    
    11 bash 环境中的特殊符号
    
1 命令历史

    shell进程会保存其会话中用户曾经执行过的命令，默认可以记录1000条历史命令；
    命令保存在用户的家目录中的.bash_history文件中。
    只有当用户正常退出当前shell时，在当前shell中运行的命令才会保存至.bash_history文件中。
    登录进shell 后新执行的命令只会记录在缓存中；这些命令会用户退出时“追加”至命令历史文件中
    同一账号同时登录 bash，因为等到退出时才会保存文件， 最后退出的 bash 才会最后写入文件(前面退出保存的只是被最后退出的 bash 覆盖更新了) 
    无法记录时间：其实可以透过 ~/.bash_logout 来进行 history 的记录，并加上 date来增加时间参数

    history

    -c:  清空命令历史
    -d:  删除历史中指定的命令
     #:  显示最近的 # 条历史
    -a:  追加本次会话新执行的命令历史列表至历史文件
    -n:  读历史文件中未读过的行到历史列表
    -r:  读历史文件附加到历史列表
    -w:  保存历史列表到指定的历史文件
    -p:  展开历史参数成多行 ，但不存在历史列表中
    -s:  展开历史参数成一行，附加在历史列表后

    命令历史相关环境变量

    HISTSIZE ：命令历史记录的条数
    HISTFILE ：指定历史文件，默认为~/.bash_history
    HISTFILESIZE ：命令历史文件记录历史的条数
    HISTTIMEFORMAT=“%F %T“ 显示时间
    HISTIGNORE=“str1:str2:… “  忽略string1,string2 历史
    
    控制命令历史的记录方式

    环境变量：HISTCONTROL
    ignoredups  默认，忽略 重复 的命令，连续且相同为 “重复”
    ignorespace  忽略 所有以空白开头的命令
    ignoreboth ignoredups, ignorespace
    erasedups 删除 重复命令
    export  变量名=" 值“
    存放在 /etc/profile 或 或 ~/.bash_profile

    命令行历史

    重复前一个命令，有4 种方法：
    • 重复前一个命令 使用上方向键，并回车执行
    • 按 按 !!  并回车执行
    • 入 输入 !-1  并回车执行
    • 按 按 Ctrl+p  并回车执行

    Ctrl + n ：历史中的下一条命令
    !num: 按照history 命令输出中的序号重复对应命令
    !-n : 重复n 个命令之前的那个命令

    !string  重复前一个以“string ”开头的命令
    !?string  重复前一个包含string 的命令
    !string:p  仅打印命令历史，而不执行
    !$:p 出 打印输出 !$  的内容
    !*:p 出 打印输出 !*） （所有参数）  的内容
    ^string  删除上一条命令中的string
    ^string1^string2  将上一条命令中的string1 替换为string2
    !!:gs/string1/string2  将上一条命令中所有的string1都替换为 string2

    使用up （向上）和down （向下）键来上下浏览从前输入的命令
    ctrl-r 来在命令历史中搜索命令
    • （ （reverse-i-search ）`’： ：
    Ctrl+g ：从历史搜索模式退出
    要重新调用前一个命令中最后一个参数：
    !$ 表示
    Esc, . （点击Esc击 键后松开，然后点击 .  键）
    Alt+ . （按住Alt击 键的同时点击 .  键）

    调用历史参数

    command !^ :  利用上一个命令的第一个参数做cmd 的参数
    command !$ :  利用上一个命令的最后一个参数做cmd 的参数
    command !* :  利用上一个命令的全部参数做cmd 的参数
    command !:n :  利用上一个命令的第n 个参数做cmd 的参数
    command !n:^  调用第n 条命令的第一个 参数
    command !n:$  调用第n 条命令的最后一个 参数
    command !n:m  调用第n 条命令的第 第m个 个 参数
    command !n:*  调用第n 条命令的所有参数
    command !string:^ 以 从命令历史中搜索以 string  开头的命令，并获取它的第 一个 参数
    command !string:$ 以 从命令历史中搜索以 string  开头的命令, 并获取它的 最后一个 参数
    command !string:n 以 从命令历史中搜索以 string  开头的命令，并获取它的第n个 个 参数
    command !string:* 以 从命令历史中搜索以 string  开头的命令，并获取它的 所有 参数
      
2 命令别名

    设置默认的选项在一些惯用的指令上面，可以预防一些不小心误杀档案的情况发生的时候 -- rm

    获取当前用户可用的别名的定义：
    # alias
    定义别名：
    # alias  NAME='COMMAND'
    生命周期：当前shell进程；在命令行中定义的别名，仅对当前shell
    撤消别名：
    # unalias NAME

    如果想永久有效，要定义在配置文件中
    仅对当前用户：~/.bashrc
    对所有用户有效：/etc/bashrc

    编辑配置给出的新配置不会立即生效

    bash 进程重新读取配置文件
    source /path/to/config_file
    . /path/to/config_file

    撤消别名：unalias
    unalias [-a] name [name ...]
    -a  取消所有别名
   
    如果别名同原命令同名，如果要执行原命令，可使用
    “\COMMAND”
    ’COMMAND’
    /PATH/COMMAND

3 快捷键 

    Ctrl + l 清屏，相当于clear 命令
    Ctrl + o 执行当前命令，并重新显示本命令
    Ctrl + s 阻止屏幕输出，锁定
    Ctrl + q 允许屏幕输出
    Ctrl + c 终止命令，另起一行
    Ctrl + z 挂起命令
    Ctrl + d 正常exit
    Ctrl + u 提示符下，将整列删除
    Ctrl + m Enter

    Ctrl + a 移到命令行首
    Ctrl + e 移到命令行尾
    Ctrl + f 按字符前移（右向）
    Ctrl + b 按字符后移（左向）
    Alt + f 按单词前移（右向）
    Alt + b 按单词后移（左向）
    Ctrl + xx  在命令行首和光标之间移动
    Ctrl + u 从光标处删除至命令行首
    Ctrl + k 从光标处删除至命令行尾

    Ctrl + w 从光标处向左删除至单词首
    Ctrl + d 删除光标处的字符
    Ctrl + h 删除光标前的字符
    Ctrl + y 将删除的字符粘贴至光标后
    Ctrl + t 交换光标处和之前的字符

    Alt + d 从光标处向右删除至字尾
    Alt + c 从光标处更改为首字母大写的单词
    Alt + u 从光标处更改为全部大写的单词
    Alt + l 从光标处更改为全部小写的单词
    Alt + t 交换光标处和之前的单词
    Alt + N 提示输入指定字符后，重复显示该字符N次 次

    注意：Alt 快捷键较少使用，因为常常和编辑器冲突

4 命令补全和路径补全

    命令补全：
    shell程序在接收到用户执行命令的请求且分析完成之后，最左侧字符串将被当作命令去查找；
    查找机制：
    (1) 查找内部命令；
    (2) 查找外部命令：
    1、去$PATH变量所指定的各路径下，自左而右逐个搜索各目录下的文件名；
    2、给定的打头的字符串如果能惟一标识某命令程序文件的文件名，则直接补全；
    3、不能惟一标识，再击tab可给列表；
    4、错误：没有任何命令可被此打头字符串标识；

    路径补全：
    在给定的起始路径的上级目录下，以对应路径下的打头字符串来逐一匹配上级目标下的每个文件：
    惟一标识：tab补全；
    不能惟一标识：tab, tab给出列表；
    错误路径：没有响应；
 
5 命令行展开
                   
    命令行扩展：$( ) 或 或 ``
    把一个命令的输出打印给另一个命令的参数
    $ echo "This system's name is $(hostname ) "
    This system's name is server1.example.com
    $echo "i am `whoami ` "
    i am root

    ~：自动展开为用户的家目录，或指定的用户的家目录；
    把命令行的给定的特殊符号自动替换为相应字符串的机制；
    ~: 自动替换为用户家目录；
    ~USERNAME：自动替换为指定用户的家目录；

    {}：可承载一个以逗号分隔的路径列表，能够将其展开为多个独立路径；
    打印重复字符串的简化形式
    例如： 
    /tmp/{a,b,c} /tmp/a /tmp/b /tmp/c 
    /tmp/{a,b}/z /tmp/a/z /tmp/b/z 

6 命令执行状态码

    命令的执行状态结果：
    成功：0
    失败：1-255

    bash用特殊变量$?来保存最一次执行的命令的状态结果:echo $?

7 glob通配符  man 7 glob

    glob通配符：文件名通配；快速引用多个文件；文件名整体匹配度检测；    

    *：匹配任意长度的任意字符；
    ?：匹配任意单个字符；
    [ ]：匹配指定集合内的任意单个字符；
    [a-z0-9]:任意单个字母或数字
    [0-9]:任意单个数字
    [a-zA-Z]:所有的字母大小写中的一个 
    [a-z], [A-Z]：不区分字符大小写；，但不包括所有字母
    [^]：匹配指定范围之外的任意单个字母，取反
    字符集
    [[:upper:]]：所有大写字母；
    [[:lower:]]：所有小写字母；
    [[:digit:]]：所有的数字；
    [[:alpha:]]：所有字母；
    [[:alnum:]]：所有字母和数字；
    [[:space:]]：空白字符；
    [[:punct:]]：标点符号；
 
    测试：
    1、显示/etc目录下，以非字母开头，后面跟了一个字母及其它任意长度任意字符的文件或目录； 
        ls  -d  /etc/[^[:alpha:]][a-z]*     加上 -d 是为了仅显示目录而已
    2、复制/etc目录下，所以n开头，以非数字结尾的文件或目录至/tmp/etc目录下；
        mkdir /tmp/etc 
        cp  -r  /etc/n*[^0-9]  /tmp/etc/
    3、显示/usr/share/man目录下，所有以man开头，后跟一个数字结尾的文件或目录；
        ls  -d  /ur/share/man/man[0-9]
    4、复制/etc目录下，所以p,m,r开头的，且以.conf结尾的文件或目录至/tmp/conf.d目录下；
        mkdir  /tmp/conf.d/
        cp  -r  /etc/[pmr]*.conf   /tmp/conf.d/

8 I/O重定向和管道

    重定向：
    屏幕输出的信息很重要，而且我们需要将他存下来的时候；
    背景执行中的程序，不希望他干扰屏幕正常的输出结果时；
    一些系统的例行命令 (例如写在 /etc/crontab 中的档案) 的执行结果，希望他可以存下来时；
    一些执行命令的可能已知错误讯息时，想以 2> /dev/null 将他丢掉时；
    错误讯息与正确讯息需要分别输出时。

    标准输入　　(stdin) ： 0 ，使用 < 或 <<
    标准输出　　(stdout)： 1 ，使用 > 或 >>
    标准错误输出(stderr)： 2 ，使用 2> 或 2>> 

    fd：file descriptor，对用户而言，访问文件通过文件名，对内核而言是一个数字（文件描述符）

    shell的一个功能开关：
    # set -C
    禁止覆盖输出重定向至已存在的文件；
    注意：此时仍然可以使用“>|”至目标文件； 
    # set +C
    关闭上述特性；
   
    合并标准输出与错误输出流：
    (1) &>, &>> 2>&1  2>>&1 
    特殊输出目标：/dev/null
    特殊的输入文件：/dev/zero

    () ：合并多个程序的STDOUT
    ( cal 2007 ; cal 2008 ) > all.txt

    Here Document
    COMMAND << EOF
    COMMAND > /PATH/TO/SOMEFILE << EOF

    例：用 stdin 取代键盘的输入以建立新档案的简单流程
    $ cat > catfile < ~/.bashrc

    例：标准输出 以 standard error output 的格式来输出
    $ echo "error message" 1>&2
    $ echo "error message" 2> /dev/null 1>&2
  
    例：cat 直接输入数据输出到 catfile 中， 且当由键盘输入 eof 时，该次输入就结束
    $ cat > catfile << "eof"
    > This is a test.
    > OK now stop
    > eof <==输入这关键词，立刻就结束而不需要输入 [ctrl]+d

    管道  |

    COMMAND1 | COMMAND2 | COMMAND3 | …
    管线命令仅会处理 standard output，对于 standard error output 会予以忽略
    管线命令必须要能够接受来自前一个指令的数据成为 standard input 继续处理才行

    STDERR 默认不能通过管道转发，可利用2>&1 或 或 |& 
    最后一个命令会在当前shell 进程的子shell 进程中执行用来

    关于减号 - 的用途
    管道命令，常常会使用到前一个指令的 stdout 作为 stdin ， 某些指令需要用到文件名 (例如 tar) 来进行处理时，该 stdin 与 stdout 可以利用减号 "-" 来替代， 

    # tar -cvf - /home | tar -xvf - -C /tmp/homeback  #-C 指定-C 后面的目录为此次操作的工作目录
    将 /home 里面的档案给他打包，但打包的数据不是纪录到档案，而是传送到 stdout； 经过管线后，将 tar -cvf - /home 传送给后面的 tar -xvf - 后面的这个 - 则是取用前一个指令的 stdout， 因此，我们就不需要使用 filename 

9 变量

    变量移到shell变量板块 

10 bash 环境中的特殊符号

    #       批注符号：这个最常被使用在 script 当中，视为说明！在后的数据均不执行
    \       跳脱符号：将『特殊字符或通配符』还原成一般字符
    |       管线 (pipe)：分隔两个管线命令的界定(后两节介绍)；
    ;       连续指令下达分隔符：连续性命令的界定 (注意！与管线命令并不相同)
    ~       用户的家目录
    $       取用变数前导符：亦即是变量之前需要加的变量取代值
    &       工作控制 (job control)：将指令变成背景下工作
    !       逻辑运算意义上的『非』 not 的意思！
    /       目录符号：路径分隔的符号
    >, >>   数据流重导向：输出导向，分别是『取代』与『累加』
    <, <<   数据流重导向：输入导向 (这两个留待下节介绍)
    ' '     单引号，不具有变量置换的功能 ($ 变为纯文本)
    " "     具有变量置换的功能！ ($ 可保留相关功能)
    ` `     两个『 ` 』中间为可以先执行的指令，亦可使用 $( )
    ( )     在中间为子 shell 的起始与结束
    { }     在中间为命令区块的组合！