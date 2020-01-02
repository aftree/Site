# Introduction

1、Linux的命令可以分为内部命令和外部命令：

内置命令在系统启动时就调入内存，是常驻内存的，所以执行效率高。

而外部命令是系统的软件功能，用户需要时才从硬盘中读入内存。

2、type - Display information about command type  
\# 查看当前系统的命令是否为内置命令和外部命令。

3、BUILTIN COMMANDS  
alias, bg, bind, break, builtin, caller, cd, command, compgen, complete, compopt, continue, declare, dirs, disown, echo,enable, eval, exec, exit, export, false, fc, fg, getopts, hash, help,history, jobs, kill, let, local, logout, mapfile, popd, printf, pushd,pwd, read, readonly, return, set, shift, shopt, source, suspend, test,times, trap, true, type, typeset, ulimit, umask, unalias, unset, wait

\#Linux中所有的内置命令。

4、内置命令和外部命令的总结

在RHEL6中我们说which只能在PATH变量中搜索命令的绝对路径，内置命令是内置在bash中的，所以我们找不到命令的绝对路径，

But，在RHEL7中好像优化了这么一个特点，内置命令在PAHT环境中能找出命令的绝对路径

**1. 程序可执行文件（文件系统 中的命令）**

当你执行一条命令的时候，Linux 通过从左到右搜索存储在 $PATH 环境变量中的目录来找到这条命令的可执行文件。你可以像下面这样查看存储在 $PATH 中的目录：

```text
$ echo $PATH
/home/aaronkilik/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games
```

在上面的命令中，目录

```text
/home/aaronkilik/bin
```

将会被首先搜索，紧跟着是

```text
/usr/local/sbin
```

然后一直接着下去。在搜索过程中，搜索顺序是至关重要的。

**. Linux 别名**

这些是用户定义的命令，它们是通过 shell 内置命令 alias 创建的，其中包含其它一些带有选项和参数的 shell 命令。这个意图主要是使用新颖、简短的名字来替代冗长的命令。创建一个别名的语法像下面这样：

```text
$ alias newcommand='command -options'
```

通过下面的命令，可以列举系统中的所有别名：

```text
$ alias -p
alias alert='notify-send --urgency=low -i "$([ $? = 0 ] 
&
&
 echo terminal || echo error)" "$(history|tail -n1|sed -e '/''s/^/s*[0-9]/+/s*//;s/[;
&
|]/s*alert$//'/'')"'
alias egrep='egrep --color=auto'
alias fgrep='fgrep --color=auto'
alias grep='grep --color=auto'
alias l='ls -CF'
alias la='ls -A'
alias ll='ls -alF'
alias ls='ls --color=auto'
```

要在 Linux 中创建一个新的别名，仔细阅读下面的例子。

```text
$ alias update='sudo apt update'
$ alias upgrade='sudo apt dist-upgrade'
$ alias -p | grep 'up'
```

![](https://github.com/aftree/A/tree/46ba3f3482b57318efea7ebcb4b9fbdafb25a241/assets/065440z58ezkxu7xxvossv.png)  
然而，上面这些我们创建的别名只能暂时的工作，当经过下一次系统启动后它们不再工作。你可以像下面展示的这样在 '.bashrc' 文件中设置永久别名。

添加以后，运行下面的命令来激活：

```text
$ source ~/.bashrc
```

**. Linux Shell 保留字**

在 shell 程序设计中，

```text
if、then、fi、for、while、case、esac、else、until
```

以及其他更多的字都是 shell 保留字。正如描述所暗示的，它们在 shell 中有特殊的含义。你可以通过使用下面展示的 type 命令来列出所有的 shell 关键字：

```text
$ type if then fi for while case esac else until
if is a shell keyword
then is a shell keyword
fi is a shell keyword
for is a shell keyword
while is a shell keyword
case is a shell keyword
esac is a shell keyword
else is a shell keyword
until is a shell keyword
```

**4. Linux shell 函数**

一个 shell 函数是一组在当前 shell 内一起执行的命令。函数有利于在 shell 脚本中实现特殊任务。在 shell 脚本中写 shell 函数的传统形式是下面这样：

```text
function_name() {
command1
command2
......
}
```

或者像这样：

```text
function function_name {
command1
command2
......
}
```

让我们看一看如何在一个名为 shell\_functions.sh 的脚本中写 shell 函数。

```text
#!/bin/bash 
#write a shell function to update and upgrade installed packages 
upgrade_system(){
sudo apt update;
sudo apt dist-upgrade;
}
#execute function
upgrade_system
```

取代通过命令行执行两条命令：

```text
sudo apt update
```

和

```text
sudo apt dist-upgrade
```

我们在脚本内写了一个像执行一条单一命令一样来执行两条命令的 shell 函数 upgrade\_system。保存文件，然后使脚本可执行。最后像下面这样运行 shell 函数：

```text
$ chmod +x shell_functions.sh
$ ./shell_functions.sh
```

**5. Linux Shell 内置命令**

这些是在 shell 中内置的 Linux 命令，所以你无法在文件系统中找到它们。这些命令包括 pwd、cd、bg、alias、history、type、source、read、exit 等，你可以通过下面展示的 type 命令来列出或检查 Linux 内置命令：

```text
$ type pwd
pwd is a shell builtin
$ type cd
cd is a shell builtin
$ type bg
bg is a shell builtin
$ type alias
alias is a shell builtin
$ type history
history is a shell builtin
```

**结论**

作为一个 Linux 用户，知道你所运行的命令类型是很重要的。我相信，通过上面明确、简单并且易于理解的解释，包括一些相关的说明，你可能对 “Linux命令的不同种类”有了很好的理解。

type后跟一个指令，会展示当该指令作为一个命令时是如何解读的。

参数-a，type会打印指令的所有位置。  
参数-t，type会打印alias，keyword，function，builtin，file的之一。alias：别名。 keyword：shell保留字。 function： shell函数。 builtin： shell内建命令。 file：磁盘文件，外部命令。

当我们键入某个命令时, shell会按照`alias->keyword->function->builtin->$PATH`的顺序进行搜索， 本着“先到先得”的原则，就是说如果有如名为mycmd的命令同时存在于alias和function中的话，那么定会使用alias的mycmd命令。但是hash比它们的优先级都高。

内建命令：  
shell内建命令是指bash（或其它版本）工具集中的命令。一般都会有一个与之同名的系统命令，比如bash中的echo命令与/bin/echo是两个不同的命令，尽管他们行为大体相仿。内建命令比系统论命令有比较高的执行效率，外部命令执行时往往需要fork一个子进程，而内建命令一般不用。

hash：  
linux系统下会有一个hash表，当你刚开机时这个hash表为空，每当你执行过一条命令时，hash表会记录下这条命令的路径，就相当于缓存一样。第一次执行命令shell解释器默认的会从PATH路径下寻找该命令的路径，当你第二次使用该命令时，shell解释器首先会查看hash表，没有该命令才会去PATH路径下寻找。输入hash可以查看hash表的内容，`hash -p a b`添加一项a改名为b，执行b时实际会执行a命令。

bash所有的内建指令：

```text
bash, :, ., [, alias, bg, bind, break, builtin, caller, cd, command,
compgen, complete, compopt, continue, declare, dirs, disown, echo,
enable, eval, exec, exit, export, false, fc, fg, getopts, hash, help,
history, jobs, kill, let, local, logout, mapfile, popd, printf, pushd,
pwd, read, readonly, return, set, shift, shopt, source, suspend, test,
times, trap, ture, type, typeset, ulimit, umask, unalias, unset, wait
```

