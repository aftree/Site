# grep

> grep，Global search REgular expression and Print out the line. 是Linux中常用的文本搜索工具，基于正则表达式（Regular Expression）和文本字符来编写模式（Pattern），然后根据指定的模式对目标文本逐行进行匹配，打印匹配到的行。

## 工具分类

grep工具共有三个：

* grep – 默认支持基本正则表达式BRE；
* egrep – 默认支持扩展正则表达式ERE；
* fgrep – 默认不支持正则表达式。

虽然工具由三种，但三者都可以通过选项来达到彼此的使用效果：

* grep \(1\) grep -E：相当于egrep \(2\) grep -F：相当于fgrep
* egrep \(1\) egrep -G：相当于grep \(2\) egrep -F：相当于fgrep
* fgrep \(1\) fgrep -G：相当于grep \(2\) fgrep -E：相当于egrep

所以一般只使用grep配合选项来达到不同的效果即可。  
另外，如果需要进行文本搜索的是很大的文件，尽量使用fgrep或-F选项来直接匹配文本，不通过正则表达式处理，性能会有较大提高，f就是fast之意。

## 命令

* grep \[OPTIONS\] PATTERN \[FILE…\]

  grep \[OPRIONS\] \[-e PATTERN \| -f FILE\] \[FILE…\]

* OPTIONS

  –color=auto：高亮显示匹配到的文本

  -i：–ignore-case：忽略字符大小写

  -o：仅显示匹配到的字符串本身，而非整行（默认显示被模式匹配到的行）

  -v：–invert-match：反向匹配，仅显示不能被模式匹配到的行

  -E：支持使用扩展的正则表达式

  -q：–quiet，–silent：静默模式，即不输出任何信息

> grep还可以显示匹配到的行的上下文：  
> -A \#：after：后\#行  
> -B \#：before：前\#行  
> -C \#：context：前后各\#行

要用好grep等具有正则表达式引擎的工具，熟练应用正则表达式才是根本。

## 正则表达式

正则表达式：Regular Expression，REGEXP。

> 正则表达式是由一类特殊字符及本本字符本身所编写的模式，其中有些字符不表示其字面意义，而是用于表示控制或通配的功能。

正则表达式分为两类：

> 基本正则表达式：BRE  
> 扩展正则表达式：ERE

### \(1\) 基本正则表达式的元字符

* 字符匹配

  .：匹配任意单个字符

  \[\]：匹配指定范围内的任意单个字符

  \[^\]：匹配指定范围外的任意单个字符

> 字符集：  
> \[:digit:\]，\[:lower:\]，\[:upper:\]，\[:alpha:\]，\[:alnum:\]，\[:punct:\]，\[:space:\]，等等

* 次数匹配

  用在要指定其出现次数的字符的后面，用于限制其前面字符出现的次数。。

  \*：匹配其前面的字符任意次

  `比如：.*：匹配任意长度的任意字符`

  ?：匹配其前面的字符0次或1次，即其前面的字符是可有可无的

  +：匹配其前面的字符1次或多次，即其前面的要出现至少1次

  {m}：匹配其前面的字符m次

  {m,n}：匹配其前面的字符至少m到n次

* 位置锚定

  ^：行首锚定：用于模式的最左侧

  $：行尾锚定：用于模式的最右侧

  `如： ^PATTERN$：用PATTERN来匹配整行`

  `^$：匹配空白行，这行不包含任意字符（连空格也没有）`

  `^[[:space:]]*$：空行或包含space的行`

  &lt;  
  或 \b：词首锚定：用于单词模式的左侧

  &gt;  
  或 \b：词尾锚定：用于单词模式的右侧

  `如：\`  
  `<`  
  `PATTERN\`  
  `>`  
  `用于匹配完整单词`

> 这里强调一下LInux中单词的含义：非特殊字符组成的连续字符（字符串）都称为单词。  
> 即：如果有特殊字符或空格出现，则单词中断。

* 分组及引用

  \\(\\)：将一个或多个字符捆绑在一起，当做一个整体进行处理

  `如\(xy\)*z：表示匹配“xy”任意次`

\\#：后向引用，\#表示序号，引用前面的分组括号中的模式所匹配到的字符

> 分组括号中的模式匹配到的内容会被正则表达式引擎自动记录于内部的变量中，这些变量为：  
> \1：模式从左侧起，第一个左括号以及与之匹配的右括号之间的模式所匹配的字符  
> \2：模式从左侧起，第二个左括号以及与之匹配的右括号之间的模式所匹配的字符  
> \3  
> …

### \(2\) 扩展正则表达式的元字符

* 字符匹配

> 扩展正则表达式的字符匹配元字符和基本正则表达式相同。

* 次数匹配

  扩展正则表达式的次数匹配元字符省去了反斜线\，更易读：

  \*：任意次（即0，1，或多次）

  ?：0次或1次，其前的字符是可有可无的

  +：其前字符至少1次

  {m}：其前的字符m次

  {m,n}：至少m次，至多n次

* 位置锚定

> 扩展正则表达式的位置锚定元字符和基本正则表达式相同。

* 分组及引用

  扩展正则表达式的分组元字符省去了反斜线\，更易读：

  \(\)：分组

> 后向引用元字符和基本正则表达式相同

* “或”关系

  扩展正则表达式比基本正则表达式多了一个“或”的元字符：

  \|：“或”

  `如：a|b：a或者b`

  `C|cat：C或cat`

  `(c|C)at：cat或Cat`

## 实例

#### 1.显示/proc/meminfo文件中以大写或小写S开头的行；用两种方式

第一种方式，直接使用grep工具：

```text
[root@localhost ~]# grep '^[s,S]' /proc/meminfo
SwapCached: 0 kB
SwapTotal: 975868 kB
SwapFree: 975868 kB
Shmem: 4684 kB
Slab: 55728 kB
SReclaimable: 20580 kB
SUnreclaim: 35148 kB
```

第二种方式，先用cat，然后通过管道将命令结果交给grep处理：

```text
[root@localhost ~]# cat /proc/meminfo | grep '^[s,S]'
SwapCached: 0 kB
SwapTotal: 975868 kB
SwapFree: 975868 kB
Shmem: 4684 kB
Slab: 55880 kB
SReclaimable: 20588 kB
SUnreclaim: 35292 kB
```

#### 2.显示/etc/passwd文件中其默认shell为非/sbin/nologin的用户

```bash
[root@localhost ~]# grep -v '/sbin/nologin$' /etc/passwd
root:x:0:0:root:/root:/bin/bash
sync:x:5:0:sync:/sbin:/bin/sync
shutdown:x:6:0:shutdown:/sbin:/sbin/shutdown
halt:x:7:0:halt:/sbin:/sbin/halt
amandabackup:x:33:6:Amanda user:/var/lib/amanda:/bin/bash
tomcat:x:91:91:Apache Tomcat:/usr/share/tomcat:/bin/nologin
hadoop:x:1500:1500::/home/hadoop:/bin/bash
```

#### 3.显示/etc/passwd文件中其默认shell为/bin/bash的用户；

```text
[root@localhost ~]# grep '/bin/bash$' /etc/passwd
root:x:0:0:root:/root:/bin/bash
amandabackup:x:33:6:Amanda user:/var/lib/amanda:/bin/bash
hadoop:x:1500:1500::/home/hadoop:/bin/bash
```

#### 4.找出/etc/passwd文件中的一位数或两位数；

```text
[root@localhost ~]# grep -o "\<[[:digit:]]{1,2}>" /etc/passwd
0
0
1
1
2
2
```

#### 5.显示/boot/grub2/grub.conf中以至少一个空白字符开头的行

```text
[root@localhost ~]# grep '^[[:space:]]+' /boot/grub2/grub.cfg
```

#### 6.显示/etc/rc.d/rc.local文件中以\#开头，后面跟至少一个空白字符，而后又有至少一个非空白字符的行

```text
[root@localhost ~]# grep '^#[[:space:]]+' /etc/rc.d/rc.local
# THIS FILE IS ADDED FOR COMPATIBILITY PURPOSES
# It is highly advisable to create own systemd services or udev rules
# to run scripts during boot instead of using this file.
```

#### 7.打出netstat -tan命令执行结果中以‘LISTEN’，后跟空白字符结尾的行

```text
[root@localhost ~]# netstat -tan | grep 'LISTEN[[:space:]]+$'
tcp 0 0 0.0.0.0:22 0.0.0.0: LISTEN
tcp 0 0 127.0.0.1:25 0.0.0.0: LISTEN
tcp6 0 0 :::22 ::: LISTEN
tcp6 0 0 ::1:25 ::: LISTEN
```

#### 8.添加用户bash, testbash, basher, nologin \(此一个用户的shell为/sbin/nologin\)，而后找出当前系统上其用户名和默认shell相同的用户的信息；

```text
[root@localhost ~]# grep -E "(\<[[:alnum:]]+>).*\1$" /etc/passwd
sync:x:5:0:sync:/sbin:/bin/sync
shutdown:x:6:0:shutdown:/sbin:/sbin/shutdown
halt:x:7:0:halt:/sbin:/sbin/halt
bash:x:1501:1501::/home/bash:/bin/bash
nologin:x:1504:1504::/home/nologin:/sbin/nologin
```

```text
grep, egrep, fgrep    

grep: 根据模式搜索文本，并将符合模式的文本行显示出来。
Pattern: 文本字符和正则表达式的元字符组合而成匹配条件

grep [options] PATTERN [FILE...]
    -i
    --color
    -v: 显示没有被模式匹配到的行
    -o：只显示被模式匹配到的字符串


*: 任意长度的任意字符
?: 任意单个字符
[]:
[^]:    

正则表达式：REGular EXPression, REGEXP
元字符：
.: 匹配任意单个字符
[]: 匹配指定范围内的任意单个字符
[^]：匹配指定范围外的任意单个字符
    字符集合：[:digit:], [:lower:], [:upper:], [:punct:], [:space:], [:alpha:], [:alnum:]

匹配次数（贪婪模式）：
*: 匹配其前面的字符任意次    
    a, b, ab, aab, acb, adb, amnb
    a*b， a?b
    a.*b

    .*: 任意长度的任意字符
\?: 匹配其前面的字符1次或0次
\{m,n\}:匹配其前面的字符至少m次，至多n次
    \{1,\}
    \{0,3\}

位置锚定：
^: 锚定行首，此字符后面的任意内容必须出现在行首
$: 锚定行尾，此字符前面的任意内容必须出现在行尾
^$: 空白行

\<或\b: 锚定词首，其后面的任意字符必须作为单词首部出现
\>或\b: 锚定词尾，其前面的任意字符必须作为单词的尾部出现

分组：
\(\)
    \(ab\)*
    后向引用
    \1: 引用第一个左括号以及与之对应的右括号所包括的所有内容
    \2:
    \3:

He love his lover.
She like her liker.
He like his lover.

l..e

练习：
1、显示/proc/meminfo文件中以不区分大小的s开头的行；
grep -i '^s' /proc/meminfo
grep '^[sS]' /proc/meminfo
2、显示/etc/passwd中以nologin结尾的行; 
grep 'nologin$' /etc/passwd

取出默认shell为/sbin/nologin的用户列表
grep "nologin$' /etc/passwd | cut -d: -f1

取出默认shell为bash，且其用户ID号最小的用户的用户名
grep 'bash$' /etc/passwd | sort -n -t: -k3 | head -1 | cut -d: -f1

3、显示/etc/inittab中以#开头，且后面跟一个或多个空白字符，而后又跟了任意非空白字符的行；
grep "^#[[:space:]]\{1,\}[^[:space:]]" /etc/inittab

4、显示/etc/inittab中包含了:一个数字:(即两个冒号中间一个数字)的行；
grep ':[0-9]:' /etc/inittab

5、显示/boot/grub/grub.conf文件中以一个或多个空白字符开头的行；
grep '^[[:space:]]\{1,\}' /boot/grub/grub.conf

6、显示/etc/inittab文件中以一个数字开头并以一个与开头数字相同的数字结尾的行；
grep '^\([0-9]\).*\1$' /etc/inittab

练习：
1、找出某文件中的，1位数，或2位数；
grep '[0-9]\{1,2\}' /proc/cpuinfo
grep --color '\<[0-9]\{1,2\}\>' /proc/cpuinfo

2、找出ifconfig命令结果中的1-255之间的整数；


3、查找当前系统上名字为student(必须出现在行首)的用户的帐号的相关信息, 文件为/etc/passwd
grep '^student\>' /etc/passwd | cut -d: -f3
id -u student

student1
student2

练习：分析/etc/inittab文件中如下文本中前两行的特征(每一行中出现在数字必须相同)，请写出可以精确找到类似两行的模式：
l1:1:wait:/etc/rc.d/rc 1
l3:3:wait:/etc/rc.d/rc 3

grep '^l\([0-9]\):\1.*\1$' /etc/inittab


REGEXP：REGular EXPression
Pattern: 

正则表达式：
    Basic REGEXP：基本
    Extended REGEXP：扩展

基本正则表达式：
.: 
[]: 
[^]:

次数匹配：
*: 
\?: 0或1次
\{m,n\}：至少m次，至多n次；

.*: 

锚定：
^:
$:
\<, \b:
\>, \b:

\(\)
\1, \2, \3, ...

grep：使用基本正则表达式定义的模式来过滤文本的命令；
    -i
    -v
    -o
    --color
    -E: 使用扩展正则表达式
    -A #: 
    -B #:
    -C #:

扩展正则表达式：

字符匹配：
.
[]
[^]

次数匹配：
*: 
?:
+: 匹配其前面的字符至少1次
{m,n}

位置锚定：
^
$
\<
\>

分组：
()：分组
\1, \2, \3, ...

或者
|: or
C|cat: Cat或cat, C或cat

grep -E = egrep 



4、显示所有以数字结尾且文件名中不包含空白的文件；
ls *[^[:space:]]*[0-9]   ?????????


找出/boot/grub/grub.conf文件中1-255之间的数字；
\<([1-9]|[1-9][0-9]|1[0-9][0-9]|2[0-4][0-9]|25[0-5])\>

\.

ifconfig | egrep '\<([0-9]|[1-9][0-9]|1[0-9][0-9]|2[0-4][0-9]|25[0-5])\>\.\<([0-9]|[1-9][0-9]|1[0-9][0-9]|2[0-4][0-9]|25[0-5])\>\.\<([0-9]|[1-9][0-9]|1[0-9][0-9]|2[0-4][0-9]|25[0-5])\>\.\<([0-9]|[1-9][0-9]|1[0-9][0-9]|2[0-4][0-9]|25[0-5])\>' 

ifconfig | egrep --color '(\<([0-9]|[1-9][0-9]|1[0-9][0-9]|2[0-4][0-9]|25[0-5])\>\.){3}\<([0-9]|[1-9][0-9]|1[0-9][0-9]|2[0-4][0-9]|25[0-5])\>' 

IPv4: 
5类：A B C D E
A：1-127
B：128-191
C：192-223

\<([1-9]|[1-9][0-9]|1[0-9]{2}|2[01][0-9]|22[0-3])\>(\.\<([0-9]|[1-9][0-9]|1[0-9][0-9]|2[0-4][0-9]|25[0-4])\>){2}\.\<([1-9]|[1-9][0-9]|1[0-9][0-9]|2[0-4][0-9]|25[0-4])\>


http://mageedu.blog.51cto.com/


grep, egrep
fgrep: 不支持正则表达式
```

