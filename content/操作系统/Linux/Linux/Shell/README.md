# Shell

什么是shell编程呢？简单来说，shell编程就是对多个Linux命令进行逻辑处理。

## 编程基础

#### 1.什么是程序？

程序是指：指令+数据

程序编程风格可分为：

过程式：以指令为中心，数据服务于指令（关注过程，小软件开发）

对象式：以数据为中心，指令服务于数据（关注结果，适合开发大型软件）

#### 2.程序的执行方式

计算机：运行二进制命令

编程语言：

低级：汇编

高级：

编译：高级语言–&gt;编译器–&gt;目标代码 \(由程序员完成编译器翻译过程，安全性相对好\)

java，C\#

解释：高级语言–&gt;解释器–&gt;机器代码 （由电脑完成解释器翻译过程，安全性不如编译型）

shell，perl，python

shell程序：提供了编程能力，解释执行

#### 3.编程基本概念

编程逻辑处理方式：

顺序执行

循环执行

选择执行

shell编程：过程式、解释执行

编程语言的基本结构：

各种系统命令的组合

数据存储：变量、数组

表达式：a+b

语句：if

### **脚本基本格式**

#### 格式要求：

首行shebang机制（即声明脚本使用哪种编程语言）

如： \#！/bin/bash

\#！/bin/python

#### shell脚本的用途有：

自动化常用命令

执行系统管理和故障排除

创建简单的应用程序

处理文本或文件

#### 运行脚本：

1.给予执行权限，执行

2.直接运行解释器，将脚本作为解释器程序的参数运行

#### 脚本规范：

脚本代码开头约定：

1.第一行一般为调用使用的语言

2.程序名，避免更改文件名为无法找到正确的文件

3.版本号

4.更改后的时间

5.作者相关信息

6.该程序的作用，及注意事项

7.最后是各版本的更新简要说明

#### 脚本示例：

```text
#!/bin/bash
# ——————————————
# Filename:    hello.sh # Revision:    1.1
# Date:        2018/04/01
# Author:      wangx
# Email:       wangx@gmail.com
# Website:     www.donlihuoguo.cn
# Description: This is the first script
# ——————————————
# Copyright:   2018 wang
# License:     GPL  echo “hello world”
```

#### 脚本调试：

bash -n script 检查脚本的语法错误

bash -x script 调试脚本执行

### **三、变量**

定义变量时，变量名不加$符号，如：

my\_name=”wangx”

#### \*变量命名规则：

1.数字、字母、下划线，不能以数字开头

2.不能使用程序中的保留字：如if，for等

3.做到见名知意

注意：

引用变量一般需加$

赋值变量：

name=” ” 要加引号

存放多行字符时”$name”变量外加双引号可保留换行格式

#### \*bash变量的种类

局部变量、全局变量、本地变量、位置变量、特殊变量

局部变量：只在当前shell进程生效，对当前shell之外其他shell进程，包括父shell和子shell都无效

全局变量：生效范围在当前shell进程及其子进程；父进程变量可以传给子进程，子进程变量不可向上传给父进程

数字变量：$10以上的变量需要加花括号${10}

特殊变量：$1,$@,$\*,$\#,$0等、

$1,$2,…：对应第1、第2参数，shift\[n\]换位置

$0：命令本身

$\*：传递给脚本的所有参数，每个参数合为一个字符串

$@：传递给脚本的所有参数，每个参数为独立字符串

$\#：传递给脚本的参数的个数

$@$\*只在被双引号包起来的时候才会有差异

set– 清空所有位置变量

注：怎么判断变量前加不加$符号？

如果命令能识别变量就不加$,如果命令不能识别变量加$；

养成好习惯：编完脚本将变量用unset删掉；

（）中使用的命令为一次性的，小括号中相当于在子进程中执行命令

注：小括号中所说的子进程与常规的子进程不是一回事

例如：x=1;echo $$;\(echo$$\)中显示进程号一致

{；}中使用命令时在当前shell环境中执行，不开启子进程影响当前shell环境

#### \*变量运算

bash中的算术运算：help let

+，-，\*，/，%取余，\*\*乘方

实现算术运算：

1.let var=算术表达式

2.var=$\[算术表达式\]

3.var=$\(\(算术表达式\)\)

注：算术运算过程中，空格尽量删除

条件测试过程中，能加空格地方尽量加空格

正则条件测试时，正则表达式不加引号

### **四、条件测试**

#### 测试命令：

test EXPRESSION

\[ EXPRESSION \]

\[\[ EXPRESSION \]\] 双括号一般用于左侧字符匹配右侧扩展正则表达式

注：EXPRESSION前后必须有空白字符

评估布尔声明，以便用在条件性执行中：

若真，则返回0

若假，则返回1

例如：\#a=1;b=2

\#\[ $a -eq $b \]

\#echo $?

\#1

\#a=2;b=2

\#\[ $a -eq $b \]

\#echo $?

\#0

#### 条件性的执行操作符

&&代表条件性的AND THEN，前面执行结果$?=0时，继续执行&&后命令

\|\|代表条件性的OR ELSE，前面执行结果$?不为0时，执行\|\|后命令

test 与\[ \]效果相同，注意括号里内容两边需加空格

-v VAR 变量VAR是否设置

#### 数值测试：

-gt 是否大于（greater-than）

-ge 是否大于等于（greater-than-or-equal）

-eq 是否等于（equal）

-ne 是否不等于（not-equal）

-lt 是否小于（less-than）

-le 是否小于等于（less-than-or-equal）

#### 字符串测试：

== 是否等于

&gt; ascii码是否大于ascii码

&lt; 是否小于

!= 是否不等于

=~ 左侧字符串是否能够被右侧的PATTERN所匹配

注：使用PATTERN匹配时，需要加双括号\[\[ \]\]；使用扩展的正则表达式，且正则表达式不能加引号

-z 字符串是否为空，空为真，不空为假

-n 字符串是否为不空，不空为真，空为假

注：用于字符串比较时的用到的操作应该都使用引号

字符串是否为空还可用\[ “$name” == “” \]来测试。

#### 文件相关测试：

存在性测试：

-a FILE 同-e

-e FILE 文件存在性测试，存在为真，否则为假

存在性及类别测试：

-b FILE 是否存在且为块设备文件

-c FILE 是否存在且为字符设备文件

-d FILE 是否存在且为目录文件

-f FILE 是否存在且为普通文件

-h FILE 同-L

-L FILE 是否存在且为符号链接文件

-p FILE 是否存在且为管道文件

-S FILE 是否存在且为套接字文件

文件权限测试：

-r FILE 是否存在且有可读权限

-w FILE 是否存在且有可写权限

-x FILE 是否存在且有可执行权限

文件特殊权限测试：

-u FILE 是否存在且拥有suid权限

-g FILE 是否存在且拥有sgid权限

-k FILE 是否存在且拥有sticky权限

文件大小测试：

-s FILE 是否存在且非空

文件是否打开：

-t fd：fd 文件描述符是否在终端已经打开

-N FILE 文件自从上一次被读取之后是否被修改过

-O FILE 当前有效用户是否为文件属主

-G FILE 当前有效用户是否文文件属组

注意：文件测试后跟空字符时，也反馈为True，如果要保证一个文件存在且满足测试条件时，

应表示为：例：\[ -n “file” -a -d “file” \]

file文件是否存在且为目录文件

#### 双目测试：

FILE1 -ef FILE2 FILE1是否是FILE2的硬链接

FILE1 -nt FILE2 FILE1是否新于FILE2（mtime）

FILE1 -ot FILE2 FILE1是否旧于FILE2

#### Bash的组合测试条件：

第一种方式：

COMMAND1 && COMMAND2 并且

COMMAND1 \|\| COMMAND2 或者

！COMMAND 非

第二种方式：

\[ EXPRESSION -a EXPRESSION \] 并且

\[ EXPRESSION -o EXPRESSION \] 或者

\[ ！EXPRESSION \] 非

注：优先级为：”!”最高，”-a”次之，”-o”最低

短路与&&

0&&0=0

0&&1=0

1&&0=0

1&&1=1

cmd1 && cmd2

如果cmd1为假，cmd2不需要执行，反之cmd1为真，需要cmd2执行

短路或\|\|

0\|\|0=0

0\|\|1=1

1\|\|0=1

1\|\|1=1

cmd1 \|\| cmd2

如果cmd1为真，cmd2不需要执行，反之cmd1为假，需要cmd2执行

异或 XOR ^

如果相同为假，反则为真

0 ^ 1 = 1

1 ^ 0 = 1

1 ^ 1 = 0

0 ^ 0 = 0

注意：\[ \]和test用变量时，最好加双引号””避免出问题

false \|\| { echo cmd1 ; echo cmd2 ; }

false \|\| \(echo cmd1 ; echo cmd2\)

#### 使用read命令来接受输入

read 使用read来把输入值分配给一个或多个shell变量

-p 指定要显示的提示

-s 静默输出，一般用于密码

-n N指定输入的字符长度N

-d ‘字符’输入结束符，类型与输入重定向的&lt;&lt;EOF

-t N TIMEOUT为N秒，N秒内无输入将自动退出

read命令从标准输入中读取单词，并给每个单词分配一个变量

例如：

read -p “Enter a filename:” FILE 标准输入将赋予变量$FILE

read命令从标准输入中读取单词，并给每个单词分配一个变量

read 给多个变量赋值的方法：

（1）将多个变量输入一个文件中，用空格分开，输出重定向

read a b c &lt; f

（2）用“&gt;&gt;&gt;”分开：

read a b c &lt;&lt;&lt; “x y z”

### **五、配置用户环境**

#### bash的配置文件

按生效范围划分，存在两类：

全局配置：

/etc/profile

/etc/profile.d/\*.sh

/etc/bashrc

个人配置：

~/.profile

~/.bashrc

shell登录的两种方式：

交互式登录：

1.直接输入账号密码登录

2.su – username 切换的用户

执行顺序：/etc/profile –&gt; /etc/profile.d/\*.sh –&gt; ~/.bash\_profile –&gt; ~/.bashrc–&gt; /etc/bashrc

非交互式的登录：

1.su usernme

2.图形界面打开的终端

3.执行脚本

4.任何其他的bash实例

执行顺序：~/.bashrc –&gt; /etc/bashrc –&gt; /etc/profile.d/\*.sh

注：只在交互式登录生效：/etc/profile，~/.bash\_profile

配置执行文件按功能划分，存在两类：

profile类和bashrc类

Profile类：为交互式登录的shell提供配置

全局生效：/etc/profile ，/etc/profile.d/\*.sh

个人生效：~/.bash\_profile

功用：

（1）用于定义环境变量

（2）运行命令或脚本

Bashrc类：为非交互式和交互式登录的shell提供配置

全局生效：/etc/bashrc

个人生效：~/.bashrc

功用：

（1）定义命令别名和函数

（2）定义本地变量

修改profile和bashrc文件后使生效的两种方法：

（1）重新启动shell进程

（2）.或sourc；例：.~/.bashrc

注意： .或source执行脚本表示将在当前进程运行，而不是开启一个子进程运行（配置文件一般用此方式执行）

bash执行脚本则是开启一个进程执行（运行脚本一般用此方式执行）

#### Bash退出任务

保存在~./.bash\_logout文件中（用户）

在退出登录shell时运行

可用于：

创建自动备份

清除临时文件

清除操作历史等

#### 特殊的变量$-

\#echo $-

\#hibBH

h：hash缓存功能；set -h，关闭hash功能；set +h，开启hash功能

i：代表交互式shell

m：打开监控模式

B：大括号扩展

H：history，命令操作历史

#### bash展开命令的优先级：

把命令行分成单个命令词

展开别名

展开大括号{}

展开波浪符~

命令替换$\(\)和

再次把命令行分成命令词

展开文件通配（\*、？等）

I/O重定向

运行命令

## 循环

```text
一、if、case条件判断
二、for、while及until循环
三、循环控制语句continue、break、shift及select菜单
四、信号捕捉trap
```

### **一、if、case条件判断**

#### **条件判断if语句：**

if语句是指编程语言中用来判定所给定的条件是否满足，根据判定的结果（真或假）决定执行给出的两种操作之一。

```text
格式：

if   判断条件1；then
条件1为真的分支代码
    elif 判断条件2；then
    条件2为真的分支代码
    elif 判断条件3；then
    条件3为真的分支代码
else
以上条件都不满足的分支代码
fi
```

if语句会逐条件的进行判断，当第一次遇到为“真”的条件时，就会执行其分支代码，而后结束整个if语句。

#### 条件判断case语句：

```text
case语句是实现选择结构程序设计的一种语句，比较适合处理离散型变量。

格式：

case 变量引用 in
1|2|3)

cmd1

;;

4|5|6)

   cmd2

;;

7|8|9)

cmd3

;;

*)

    cmd4

esac
```

**注意：**case支持glob风格的通配符：

如：\* 表示任意长度任意字符

？ 表示任意单个字符

\[\] 指定范围内的任意单个字符

a\|b a或者b

### **二、for、while及until循环**

循环执行

将某代码段重复运行多次

重复运行多少次

次数已知

次数未知

有进入条件和退出条件

循环主要语句有：**for、whlie、until**

#### **for循环**

for语句将循环一个列表中的每一个元素执行一次循环体代码，直到列表中元素全部执行过一次。

一般用于次数预先知道的循环。

```text
格式1：

for 变量名 in 列表；do

循环体

done
```

**其中列表的生成方式有多种：**

**1.直接给出列表**

**2.整数列表：**

（a）{start..end\[..step\]}

（b）$（seq\[start\[step\]end\]）

**3.返回列表的命令：**

$（COMMAND）

**4.使用glob，如：\*.sh**

**5.变量引用：**

$@,$\*等

**执行机制：**

依次将列表中的元素赋值给“变量名”，每次赋值后即执行一次循环体；直到列表中的

元素全部被执行，循环结束

**格式2：C语言风格**

**for**\(\(: for \(\( exp1; exp2; exp3 \)\);**do**COMMANDS;**done**

Arithmetic for loop.

Equivalent to

\(\( EXP1 \)\)

while \(\( EXP2 \)\);**do**

COMMANDS

\(\( EXP3 \)\)

**done**

**例：利用for循环依次读取列表{1..100}中的内容，并相加，得出1到100的数字之和**

```bash
#! /bin/bash
# 第一种
declare -i sum=0
for i in {1..100};do
    let sum+=i
done
echo sum=$sum
unset sum
----------------------------------
# 第二种
for ((sum=0,i=1; i<=100;i++ ));do
    let sum+=i
done
echo sum=$sum

# bash for.sh
sum=5050
sum=5050
```

#### **while循环**

while结构循环为当型循环\(when type loop\)，一般用于不知道循环次数的情况。维持循环的是一个条件表达式，条件成立执行循环体，条件不成立退出循环。

```text
格式：

while 循环控制条件；do

         循环体

done
```

**循环控制条件：**进入循环之前，先做一次判断；每次循环之后会再次做判断；当条件为True时，则继续执行循环；直到条件测试状态为false时终止循环。

**例：利用while循环，定义变量i=0，当i小于100时，执行循环，并让变量i每执行一次循环加1，直到i=100时停止循环，得出数字1到100之和**

```text
#! /bin/bash
declare -i sum=0
i=0
while [ $i -le 100 ];do
    let sum+=i
    let i++
done
echo sum=$sum

---------------------
# bash while.sh
sum=5050
```

**while循环的特殊用法（遍历文件的每一行）：**

**while read line； do**

循环体

**done &lt; /PATH/FROM/SOMEFILE**

依次读取**/PATH/FROM/SOMEFILE**文件中的每一行，且将行赋值给变量line

#### **for循环与while循环的区别：**

for循环与while循环是可以**相互替代**的；比如：

for i in {1..10}；do

echo “OK”

done

**用while语句来写的话：**

i=1

while \[ $i -le 10 \];do

echo “OK”

let i++

done

两者意思都是打印10次“OK”，那什么时候用for循环，什么时候又用while循环呢？

for循环用于针对集合中的每个元素的一个代码块，而while循环不断的运行，直到指定的条件不满足为止。

一般来说：

**for循环比较适合循环次数确定的情况**

**while循环适合循环次数不确定的情况**

**until循环**

维持循环的也是一个条件表达式，但与while用法正好相反，条件不成立时执行循环体，条件成立时退出循环。

**until循环控制条件；do**

**循环体**

**done**

**until语句和while语句可相互转化：**

如：until \[ test \]；do 等价于 while \[ ! test \]；do

### **三、循环控制语句continue、break、shift**

#### **continue语句**

用于循环体中

**continue\[N\]：**提前结束第N层的本轮循环，而直接进入下一轮判断；最内层为第1层

**格式：**

**while循环控制条件；do**

 **CMD1**

 **…**

 **if判断条件；then**

 **continue**

 **fi**

 **CMDn**

 **…**

**done**

**例：依次打印数字1至10，当continue出现在5循环中时，直接跳过，继续执行6循环；**

**当continue出现在嵌套循环中时，continue 2，提前结束第二次本轮循环，并打印3次**

```text
#! /bin/bash
for i in {1..10};do
    if [ "$i" = "5" ];then continue;fi
    echo i=$i
done

for i in {1..3};do
    for j in {1..10};do
        if [ "$j" = "5"];then coutinue 2;fi
        echo j=$j
    done
    echo
done

    -------------
# bash continue.sh
```

#### **break语句**

用于循环体中

**break\[N\]：**提前结束第N层循环，最内层为第一层

**while循环控制条件；do**

 **CMD1**

 **…**

 **if判断条件；then**

 **break**

 **fi**

 **CMDn**

 **…**

**done**

**例：break语句将直接结束本轮循环**

```text
#! /bin/bash
for i in {1..10};do
    if [ "$i = "5"];then break;fi
    echo i=$i
done
---------------------------------
# bash break.sh
i=1
i=2
i=3
i-4
```

#### **shift命令**

**shift\[n\]：**将参量列表list左移指定次数，缺省为左移一次，适合用于处理位置参数

参量列表list一旦被移动，最左端的那个参数就从列表中删除。while循环遍历位置参量列表时，

常用到shift

**例1：利用shifit，每次位置变量向左移动1位，并打印本次全部位置变量，直到位置变量为0个**

```text
while [ $# -gt 0 ];do
    echo $*
    shift
done
---------------------
# bash shift.sh
12345
2345
345
45
5
```

**例2：利用shifit，每次变量向左移动一位，并打印本次第一个位置变量，直到位置变量小于1时停止执行**

```text
until [ -z "$1" ];do
    echo "$1"
    shift
done
echo
--------------
# bash shift.sh
1
2
3
4
5
```

#### **避免出现无限循环：**

**while true；do**

 **循环体**

**done**

**until false；do**

**循环体**

**done**

每个程序员都会偶尔不小心而编写出无限循环，在循环退出条件比较微妙时尤其如此，如果程序陷入无限循环，可按**ctrl+c**退出

要避免编写无限循环，务必对每个while、until循环进行测试，确保它按预期那样结束。

#### **select循环与菜单**

**select变量in list；do**

**循环体命令**

**done**

select循环主要用于创建菜单，常与case语句搭配使用，用户输入菜单列表中的某个数字，执行相应的命令

select是个无限循环，因此要记住用break命令退出循环，或用户exit命令终止脚本。可按ctrl+c退出循环

与for循环类似，可以省略in list，此时使用位置变量。

相关变量：

**$PS3** menu菜单提示符

**$REPLY**  保存用户的输入信息

### **四、信号捕捉trap**

**trap‘触发指令’信号**

自定义进程收到系统发出的指定信号好，将执行触发指令，而不会执行原操作

**trap” 信号**

忽略信号的操作

**trap‘-‘ 信号**

恢复原信号的操作

**trap -p**

列出自定义信号操作

**例：打印数字1到10的过程中，trap -p显示自定义信号操作，由于trap ”int捕获了int信号，ctrl+c无法中断显示，且显示自定义输入：press ctrl+c；**

**打印数字11到20的过程中，由于trap ”int捕获了int信号，ctrl+c无法中断显示**

**打印数字21到30的过程中，trap ‘-‘恢复了原信号操作，顾ctrl+c又可中断显示**

```text
trap 'echo press ctrl+c' int
trap -p
for ((i=0;i<=10;i++));do
    echo $i
    sleep 0.5
done

trap ' ' int
trap -p
for ((i=11;i<=20;i++));do
    echo $i
    sleep 0.5
done

trap '-' int
trap -p
for ((i=21;i<=30;i++));do
    echo $i
    sleep 0.5
done
```

```text
# bash trap.sh
trap -- 'echo press ctrl+c' SiGINT
0
1
2
3
^Cpress ctrl+c
9
10
trap -- '' SIGINT
11
12
^C13
14
^C15
16
^C17
18
19
20
21
22
^C
```

