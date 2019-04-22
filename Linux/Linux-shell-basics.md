# Linux 基础知识

## 变量

shell脚本变量是无类型的，且base shell不支持浮点型，只支持整形和字符型。若只是包含数字，则shell认为该变量是数字类型；否则为字符串类型。

### 环境变量

环境变量就是Linux系统中的变量。如系统的名称名称、登录到系统中的用户的名称、用户的系统ID(也就是UID).

我们可以用set命令来显示一份完整的活动的环境变量列表。

```
[root@master1 ~]# set
BASH=/bin/bash
BASHOPTS=checkwinsize:cmdhist:expand_aliases:extquote:force_fignore:histappend:hostcomplete:interactive_comments:login_shell:progcomp:promptvars:sourcepath
BASH_ALIASES=()
BASH_ARGC=()
BASH_ARGV=()
BASH_CMDS=()
BASH_LINENO=()
BASH_SOURCE=()
BASH_VERSINFO=([0]="4" [1]="2" [2]="46" [3]="1" [4]="release" [5]="x86_64-redhat-linux-gnu")
BASH_VERSION='4.2.46(1)-release'
CLASSPATH=.:/root/java/jdk1.8.0_25/lib/dt.jar:/root/java/jdk1.8.0_25/lib/tools.jar
COLUMNS=156
DIRSTACK=()
```



在shell脚本中，我们可以通过美元符（$)来在脚本中使用这些环境变量。

```
[root@master1 test]# cat script1.sh 
#!/bin/bash
echo UID: $UID
echo HOME: $HOME
[root@master1 test]# ./script1.sh 
UID: 0
HOME: /root
```



### 用户变量

shell脚本允许在脚本中定义和使用自己的变量。

```
[root@master1 test]# cat script1.sh 
#!/bin/bash
days=10
guest="Katie"
echo "$guest checked in $days days ago"
[root@master1 test]# ./script1.sh
Katie checked in 10 days ago
```



## 执行数学运算

在base中，要将一个数学运算结果赋值给某个变量时，可以使用美元符和方括号将表达式圈起来。

```
[root@master1 test]# cat script1.sh 
#!/bin/bash
var1=$[ 1 + 5 ]
echo $var1
[root@master1 test]# ./script1.sh 
6
```

数值操作符

| ARG1 + ARG2 | ARG1和ARG2算数运算和   |
| :---------: | ---------------------- |
| ARG1 - ARG2 | ARG1和ARG2算数运算差   |
| ARG1 * ARG2 | ARG1和ARG2算数运算积   |
| ARG1 / ARG2 | ARG1和ARG2算数运算商   |
| ARG1 % ARG2 | ARG1和ARG2算数运算余数 |
|  n1 -eq n2  | 检查n1是否与n2相等     |
|  n1 -ge n2  | 检查n1是否大于或等于n2 |
|  n1 -gt n2  | 检查n1是否大于n2       |
|  n1 -le n2  | 检查n1是否小于或等于n2 |
|  n1 -lt n2  | 检查n1是否小于n2       |
|  n1 -ne n2  | 检查n1是否不等于n2     |



字符串操作符

| str1 = str2  | 检查str1是否和str2相同  |
| ------------ | ----------------------- |
| str1 != str2 | 检查str1是否和str2不同  |
| str1 < str2  | 检查str1是否比str2小    |
| str1 > str2  | 检查str1是否比str2大    |
| -n str1      | 检查str1的长度是否为非0 |
| -z str1      | 检查str1的长度是否为0   |



文件操作符

| -d file | 检查file是否存在，并且是一个目录 |
| ------- | -------------------------------- |
| -e file | 检查file是否存在                 |
| -f file | 检查file是否存在并是一个文件     |



## 退出状态

在Linux中，每当命令执行完成后。系统都会返回一个退出状态，该状态用一个整数值表示，被保存在内置变量 “$?” 中。Linux中内置变量很多，下面简单列举一些出来：

| 0    | 命令成功结束  |
| ---- | ------------- |
| 1    | 通用未知错误  |
| 2    | 误用shell命令 |
| 126  | 命令不可执行  |
| 127  | 没找到命令    |



## 位置参数

位置参数是一种特殊的shell变量，用于命令行向shell脚本传递参数。

| $0      | 脚本名称           |
| ------- | ------------------ |
| $1      | 第1个参数          |
| ${10}   | 第10个参数         |
| $* / $@ | 表示所有的参数     |
| $#      | 表示所有参数的个数 |

下面是从命令行读取位置参数的一个小例子

```
[root@master1 test]# cat script1.sh
#!/bin/bash
total=$[ $1 * $2]

echo the first parameter is $1
echo the first parameter is $2
echo the total value is $total
[root@master1 test]# ./script1.sh 2 3
the first parameter is 2
the first parameter is 3
the total value is 6
```


## 可变数组

## Linux中 {} () (()) [] [[]]  的区别？

重定向输入和输出
输出重定向
 将命令的输出发布到一个文件中，bash shell 采用大于号来完成这项功能。

将date命令的输出保存到文件test6中，需要注意的是此操作会覆盖原文件。

如果你只是想将输出追加到test6中，可以用双大于号（）；如下：




输入重定向
将文件中的内容重定向到命令

我们先来了解一下wc命令，wc命令提供了对数据中文本的计数。默认情况下，它会输出了3个值：
文本的行数
文本的词数
文本的字节数

输入重定向的符号是小于号，如下：

还有一种输入重定向的方法—内联输入重定向。它从命令行接收数据，使用双小于号和一个文本标记来表示；文本标记可以使用任可字符表示，用它来区分输入数据的开始和结束。

管道
将某个命令的输出作为另一个命令的输入，如下


不要以为管道链接会一个一个的运行。Linux系统实际上会同时运行这两个命令，在系统内部将它们连接起来。在第一个命令产生输出的同时，输出会被立即送给第二个命令。中间不会用到任何数据缓冲区域。


# 将命令输出结果保存到变量中

``` 
#!/bin/bash
path=$(pwd)
echo $path
```

# 遍历输入的参数

``` 
#!/bin/bash
for arg in $@
do
 echo "$arg"
done
```

# Linux 中单引号、双引号、反引号的区别

## 单引号

单引号会告诉shell忽略所有的特殊字符

``` 
#!/bin/bash

echo 'echo $USER'
```
输出结果 ：echo $USER

## 双引号

具有变量置换的功能，即双引号之要求忽略大多数特殊字符，除了$、反向引号。


``` 
#!/bin/bash

echo "echo $USER"
```

输出结果 ：echo root

## 反引号

命令替换
$( )与｀｀的区别
在操作上，这两者都是达到相应的效果，但是建议使用$( )：

``` 
#!/bin/bash

path=`pwd`

echo "$path"
```

输出结果 ：/root/sh/test

