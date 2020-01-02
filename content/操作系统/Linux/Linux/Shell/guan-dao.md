
	管道命令
	1 按列选取：cut
	2 排序统计：sort, uniq, wc
	3 重定向到多个目标：tee
	4 字符转换命令：tr, col, join, paste, expand
	5 分割命令： split
	6 参数代换： xargs
	7 关于减号 - 的用途

1 cut -- 按列选取

	$ cut -d'分隔字符' -f 字段	==用于有特定分隔字符
	$ cut -c 字符区间            ==用于排列整齐的讯息
	选项与参数：
	-d  ：后面接分隔字符。与 -f 一起使用；
	-f  ：依据 -d 的分隔字符将一段讯息分割成为数段，用 -f 取出第几段的意思；
		  #:  第# 个字段
		  #,#[,#] ：离散的多个字段，例如1,3,6
		  #-# ：连续的多个字段,  例如1-6
		  混合使用：1-3,7
	-c  ：以字符 (characters) 的单位取出固定字符区间；
		  cut -c 2-5
		  cut -c 12-
	--output-delimiter=STRING  指定输出分隔符

	例：取得第 12 字符以后的所有字符串
	$ export
	declare -x HISTCONTROL="ignoredups"
	
	$ export | cut -c 12-
	HISTCONTROL="ignoredups"

	cut 主要使用在分析一些数据或文字数据的时候 这是因为有时候我们会以某些字符当作分割的参数，然后来将数据加以切割，以取得我们所需要的数据。 

2 sort -- 依据不同的数据类型排序，显示在标准输出，不改变原始文件

	sort [选项] [file or stdin]
	选项与参数：
	-f  ：忽略大小写的差异，例如 A 与 a 视为编码相同；
	-b  ：忽略最前面的空格符部分；
	-M  ：以月份的名字来排序，例如 JAN, DEC 等等的排序方法；
	-n  ：使用『纯数字』进行排序(默认是以文字型态来排序的)；
	-r  ：反向排序；
	-u  ：就是 uniq ，相同的数据中，仅出现一行代表；
	-t  ：+分隔符，预设是用 [tab] 键来分隔；
	-k  ：+数字 以那个区间 (field) 来进行排序的意思

	# sort是默认以第一个字段来排序，默认是以『文字』型态来排序的喔！所以由 a 开始排到最后啰！

	范例二：/etc/passwd 内容是以 : 来分隔的，以第三栏含后面数据来排序
	$ cat /etc/passwd | sort -t ':' -k 3
	root:x:0:0:root:/root:/bin/bash
	dmtsai:x:1000:1000:dmtsai:/home/dmtsai:/bin/bash
	alex:x:1001:1002::/home/alex:/bin/bash
	arod:x:1002:1003::/home/arod:/bin/bash# 

	单纯以第三字段来处理则是：
	$ cat /etc/passwd | sort -t ':' -k 3,3
	# 如果是以文字型态来排序的话，原本就会是这样，想要使用数字排序：
	# cat /etc/passwd | sort -t ':' -k 3,3 -n
	# 这样才行啊！用那个 -n 来告知 sort 以数字来排序

3 uniq -- 从输入中删除(连续且完全相同)重复的行

	uniq [OPTION]... [FILE]...
	选项与参数：
	-i  ：忽略大小写字符的不同；
	-c  ：显示每行重复出现的次数
	-d:  仅显示重复过 的行
	-u:  仅显示不曾重复 的行

	常和sort  命令一起配合使用：
	sort userlist.txt | uniq -c

	范例一：使用 last 将账号列出，仅取出账号栏，进行排序后仅取出一位；
	[dmtsai@study ~]$ last | cut -d ' ' -f1 | sort | uniq

	范例二：承上题，如果我还想要知道每个人的登入总次数呢？
	[dmtsai@study ~]$ last | cut -d ' ' -f1 | sort | uniq -c

	举个例子来说， 你要知道这个月份登入你主机的用户有谁，而不在乎他的登入次数，
	那么就使用上面的范例， 
	(1)先将所有的数据列出；
	(2)再将人名独立出来；
	(3)经过排序；
	(4)只显示一个！
	由于这个指令是在将重复的东西减少，所以当然需要『配合排序过的档案』来处理啰！

4 wc [-lwm]

	选项与参数：
	-l  ：仅列出行；
	-w  ：仅列出多少字(英文单字)；
	-m  ：多少字符；
	-c  ：统计字节总数

	例：那个 /etc/man_db.conf 里面到底有多少相关字、行、字符数？
	[dmtsai@study ~]$ cat /etc/man_db.conf | wc
	    131     723    5171
	# 输出的三个数字中，分别代表：『行、字数、字符数』

5 tee -- 重定向到多个目标

	tee 会同时将数据流分送到档案去与屏幕 (screen)；而输出到屏幕的，其实就是 stdout 

	$ 命令1 | tee 名 文件名 |  命令2
	把命令1 的STDOUT 保存在文件名中，然后管道输入给命令2

	$ tee [-a] file
	选项与参数：
	-a ：以累加 (append) 的方式，将数据加入 file 当中
	$ last | tee last.list | cut -d " " -f1  #将 last 的输出存一份到 last.list 
	$ ls -l /home | tee ~/homefile | more    #将 ls 的数据存一份到 ~/homefile，同时屏幕也输出
	$ ls -l / | tee -a ~/homefile | more     # tee 后的文件会被覆盖，-a 追加内容

字符转换命令： tr, col, join, paste, expand

6 tr 删除、转换字符

	tr [选项] SET1 ...
	选项与参数：
	-c –C --complement ：取字符集的补集
	-d --delete ：删除所有属于第一字符集的字符
	-s --squeeze-repeats ：把连续重复的字符以单独一个字符表示
	-t --truncate-set1：将第一个字符集对应字符转化为第二字符集

	[:alnum:]：字母和数字 
	[:alpha:]：字母 
	[:cntrl:]：控制（非打印）字符 
	[:digit:]：数字 
	[:graph:]：图形字符 
	[:lower:]：小写字母 
	[:print:]：可打印字符 
	[:punct:]：标点符号 
	[:space:]：空白字符 
	[:upper:]：大写字母 
	[:xdigit:]：十六进制字符

	例一：将 last 输出的讯息中，所有的小写转换大写字符：
	$ last | tr '[a-z]' '[A-Z]'

	范例二：将 /etc/passwd 输出的讯息中，将冒号 (:) 删除
	[dmtsai@study ~]$ cat /etc/passwd | tr -d ':'

	范例三：将 /etc/passwd 转存成 dos 断行到 /root/passwd 中，再将 ^M 符号删除
	$ cat ~/passwd | tr -d '\r' > ~/passwd.linux# 那个 \r 指的是 DOS 的断行字符
	例如上面第三个例子当中，可以去除 DOS 档案留下来的 ^M 这个断行的符号！这东西相当的有用！即是 DOS 底下会自动的在每行行尾加入 ^M 这个断行符号！可以使用这个 tr 来将 ^M 去除！ ^M 可以使用 \r 来代替

7 col

	col [-xb]
	选项与参数：
	-x  ：将 tab 键转换成对等的空格键

	范例一：利用 cat -A 显示出所有特殊按键，最后以 col 将 [tab] 转成空白
	$ cat -A /etc/man_db.conf   #此时会看到很多 ^I 的符号，那就是 tab
	$ cat /etc/man_db.conf | col -x | cat -A | more   #tab按键会被取代成为空格键

8 join -- 处理『两个档案当中，有"相同数据"的那一行，才将他加在一起

	join [-ti12] file1 file2
	选项与参数：
	-t  ：join 默认以空格符分隔数据，并且比对『第一个字段』的数据，如果两个档案相同，则将两笔数据联成一行，且第一个字段放在第一个！
	-i  ：忽略大小写的差异；
	-1  ：这个是数字的 1 ，代表『第一个档案要用那个字段来分析』的意思；
	-2  ：代表『第二个档案要用那个字段来分析』的意思。

	例一：用 root 的身份，将 /etc/passwd 与 /etc/shadow 相关数据整合成一栏
	# head -n 3 /etc/passwd /etc/shadow
	# 由输出的资料可以发现这两个档案的最左边字段都是相同账号！且以 : 分隔
	 
	# join -t ':' /etc/passwd /etc/shadow | head -n 3
	root:x:0:0:root:/root:/bin/bash:$6$wtbCCce/PxMeE5wm$KE2IfSJr...:16559:0:99999:7:::
	bin:x:1:1:bin:/bin:/sbin/nologin:*:16372:0:99999:7:::
	daemon:x:2:2:daemon:/sbin:/sbin/nologin:*:16372:0:99999:7:::# 透过上面这个动作，我们可以将两个档案第一字段相同者整合成一列！
	# 第二个档案的相同字段并不会显示(因为已经在最左边的字段出现了啊！)

	例二：/etc/passwd 第四个字段 GID ，那个GID记 /etc/group 当中的第三个字段，请问如何将两个档案整合？
	[root@study ~]# head -n 3 /etc/passwd /etc/group
	# 从上面可以看到，确实有相同的部分喔！赶紧来整合一下！
	 
	# join -t ':' -1 4 /etc/passwd -2 3 /etc/group | head -n 3
	0:root:x:0:root:/root:/bin/bash:root:x:
	1:bin:x:1:bin:/bin:/sbin/nologin:bin:x:
	2:daemon:x:2:daemon:/sbin:/sbin/nologin:daemon:x:# 
	# 请读者们配合上述显示两个档案的实际内容来比对！

	特别注意:在使用 join 之前，你所需要处理的档案应该要事先经过排序 (sort) 否则有些比对的项目会被略过

9 paste -- 合并两个文件同行号的列到一行，中间以 [tab] 键隔开

	$ paste [-d] file1 file2
	选项与参数：
	-d  ：后面可以接分隔字符。预设是以 [tab] 来分隔的！
	-   ：如果 file 部分写成 - ，表示来自 standard input 的资料的意思。
	-s  ：  所有行合成一行显示

	例：先将 /etc/group 读出(用 cat)，然后与/etc/passwd /etc/shadow贴上一起！且仅取出前三行
	# cat /etc/group|paste /etc/passwd /etc/shadow -|head -n 3
	# 这个例子的重点在那个 - 的使用！那玩意儿常常代表 stdin 

10 expand -- 将 [tab] 按键转成空格键

	$ expand [-t] file
	选项与参数：
	-t  ：后面可以接数字。一般来说，一个 tab 按键可以用 8 个空格键取代。
	      我们也可以自行定义一个 [tab] 按键代表多少个字符呢！

	想要将所有的符号都列出来？(用 cat)
	$ grep '^MANPATH' /etc/man_db.conf | head -n 3 |cat -A
	MANPATH_MAP^I/bin^I^I^I/usr/share/man$
	# 发现差别了吗？没错～ [tab] 按键可以被 cat -A 显示成为 ^I 

	承上，我将 [tab] 按键设定成 6 个字符的话？
	[dmtsai@study ~]$ grep '^MANPATH' /etc/man_db.conf | head -n 3 | expand -t 6 - | cat -A
	MANPATH_MAP /bin              /usr/share/man$
	MANPATH_MAP /usr/bin          /usr/share/man$
	MANPATH_MAP /sbin             /usr/share/man$
	123456123456123456123456123456123456123456123456...
	# 仔细看一下上面的数字说明，因为我是以 6 个字符来代表一个 [tab] 的长度，所以，
	# MAN... 到 /usr 之间会隔 12 (两个 [tab]) 个字符喔！如果 tab 改成 9 的话，

	unexpand 这个将空白转成 [tab] 的指令功能
 
11 分割命令： split -- 可以将一个大档案，依据档案大小或行数来分割，就可以将大档案分割成为小档案了

	[split [-bl] file PREFIX
	选项与参数：
	-b  ：后面可接欲分割成的档案大小，可加单位，例如 b, k, m 等；
	-l  ：以行数来进行分割。
	PREFIX ：代表前导符的意思，可作为分割档案的前导文字。

	例一：我的 /etc/services 有六百多K，若想要分成 300K 一个档案时？
	$ split -b 300k /etc/services services
	$ ll -k services*
	-rw-rw-r--. 1 dmtsai dmtsai 307200 Jul  9 22:52 servicesaa
	-rw-rw-r--. 1 dmtsai dmtsai 307200 Jul  9 22:52 servicesab
	-rw-rw-r--. 1 dmtsai dmtsai  55893 Jul  9 22:52 servicesac
	# 只要写上前导文字，小档案就会以xxxaa, xxxab, xxxac 等方式来建立小档案的！

	例二：如何将上面的三个小档案合成一个档案，档名为 servicesback
	$ cat services* >> servicesback# 很简单吧？就用数据流重导向就好

	例三：使用 ls -al / 输出的信息中，每十行记录成一个档案
	$ ls -al / | split -l 10 - lsroot
	$ wc -l lsroot*
	  10 lsrootaa
	  10 lsrootab
	   4 lsrootac
	  24 total
	# 一般来说，如果需要 stdout/stdin 时，但偏偏又没有档案，有的只是 - 时，那么那个 - 就会被当成 stdin 或 stdout 

12 参数替换： xargs -- x 是加减乘除的乘号，args 则是 arguments (参数) 
	
	使用 xargs 的原因， 很多指令不支持管线命令，因此我们可以透过 xargs 来提供该指令引用 standard input 之用
	xargs 可以读入 stdin 的数据，并且以空格符或断行字符作为分辨，将 stdin 的资料分隔成为 arguments 。 因为是以空格符作为分隔，所以，如果有一些档名或者是其他意义的名词内含有空格符的时候， xargs 可能就会误判

	xargs [选项] command
	选项与参数：
	-0  ：如果输入的 stdin 含有特殊字符，例如 `, \, 空格键等等字符时，这个 -0 参数可以将他还原成一般字符。这个参数可以用于特殊状态喔！
	-e  ：这个是 EOF (end of file) 的意思。后面可以接一个字符串，当 xargs 分析到这个字符串时，就会停止继续工作！
	-p  ：在执行每个指令的 argument 时，都会询问使用者的意思；
	-n  ：后面接次数，每次 command 指令执行时，要使用几个参数的意思。

	当 xargs 后面没有接任何的指令时，默认是以 echo 来进行输出喔！

	例一：将 /etc/passwd 内的第一列取出，仅取三行，使用 id 这个指令将每个账号内容秀出来
	 
	$ cut -d ':' -f 1 /etc/passwd | head -n 3 | xargs -n 1 id
	uid=0(root) gid=0(root) groups=0(root)
	uid=1(bin) gid=1(bin) groups=1(bin)
	uid=2(daemon) gid=2(daemon) groups=2(daemon)
	# 透过 -n 来处理，一次给予一个参数，因此上述的结果就 OK 正常的显示啰！

	例二：同上，但是每次执行 id 时，都要询问使用者是否动作？
    $ cut -d ':' -f 1 /etc/passwd | head -n 3 | xargs -p -n 1 id

	例三：将所有的 /etc/passwd 内的账号都以 id 查阅，但查到 sync 就结束指令串
	$ cut -d ':' -f 1 /etc/passwd | xargs -e'sync' -n 1 id
	# -e'sync' 是连在一起的，中间没有空格键。
	# 上个例子当中，第六个参数是 sync 啊，那么我们下达 -e'sync' 后，则分析到 sync 这个字符串时，后面的其他 stdin 的内容就会被 xargs 舍弃掉了！

	例四：找出 /usr/sbin 底下具有特殊权限的档名，并使用 ls -l 列出详细属性
	$ find /usr/sbin -perm /7000 | xargs ls -l
	$ ls -l $(find /usr/sbin -perm /7000) 俩个都可以完成
	-rwx--s--x. 1 root lock      11208 Jun 10  2014 /usr/sbin/lockdev
	-rwsr-xr-x. 1 root root     113400 Mar  6 12:17 /usr/sbin/mount.nfs
	-rwxr-sr-x. 1 root root      11208 Mar  6 11:05 /usr/sbin/netreport
	
13 关于减号 - 的用途

    管道命令，常常会使用到前一个指令的 stdout 作为 stdin ， 某些指令需要用到文件名 (例如 tar) 来进行处理时，该 stdin 与 stdout 可以利用减号 "-" 来替代， 

    # tar -cvf - /home | tar -xvf - -C /tmp/homeback  #-C 指定-C 后面的目录为此次操作的工作目录
    将 /home 里面的档案给他打包，但打包的数据不是纪录到档案，而是传送到 stdout； 经过管线后，将 tar -cvf - /home 传送给后面的 tar -xvf - 后面的这个 - 则是取用前一个指令的 stdout， 因此，我们就不需要使用 filename 