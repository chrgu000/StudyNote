

# Ubuntu 17 使用特性

## Ubuntu 17 和 win 10复制粘贴

**1.点击菜单中虚拟机，选择安装VMware tools**

虚拟机>>安装VMware Tools

**2.复制文件到 /tmp 文件夹下面并解压**

``` 
sudo cp VMwareTools-10.2.5-8068393.tar.gz /tmp
tar -zxvf VMwareTools-10.2.5-8068393.tar.gz
```

**2.解压文件并执行如下命令**

``` 
  sudo ./vmware-install.pl
```

## 查询当前系统

``` 
root@ubuntu:~/java/jdk1.8.0_25/bin# uname -a
```

## 使用root用户登录

**1.在下列文件中添加如下内容**

sudo gedit /usr/share/lightdm/lightdm.conf.d/50-ubuntu.conf

``` 
greeter-show-manual-login=true
```

**2.修改root用户密码**

``` 
sudo passwd root
```

**3.重启，即可用root用户登录**



## 更换软件源

**1.修改软件源**

root@ubuntu:~# gedit /etc/apt/sources.list

``` 
deb http://mirrors.aliyun.com/ubuntu/ bionic main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ bionic-security main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ bionic-updates main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ bionic-proposed main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ bionic-backports main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-security main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-updates main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-proposed main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-backports main restricted universe multiverse

```

**2.执行更新命令**

``` 
root@ubuntu:~# apt-get update
```

## 安装ssh

``` 
<!--安装server端-->
root@ubuntu:~# sudo apt-get install openssh-server
<!--安装client端-->
root@ubuntu:~# sudo apt-get install openssh-client
```

## 允许root用户远程登录

**1.修改ssh配置文件允许远程登录**

root@ubuntu:~# gedit /etc/ssh/sshd_config

``` 
PermitRootLogin yes
```

**2.重启ssh服务**

``` 
root@ubuntu:~# /etc/init.d/ssh restart
```

# Centos 使用特性

 ## 查看本机IP地址
   
```
ip addr
```

## 修改当前机器IP地址

```
<!--
	通过图形化界面配置CenterOS mini 的网络信息。
-->
nmtui
```
 
## 软件安装

```
<!--
	查找软件源
-->
yum search vim

<!--
	.从查找到的软件源中挑选适合的软件进行安装
-->
yum install vim-enhanced
```

## hosts 文件目录

```
/etc/hosts
```
## hostname 文件目录

```
/etc/hostname
```

## ssh传输文件

```
scp /etc/hosts root@192.168.1.183:/etc/hosts
```

## CentOS 关闭防火墙命令

```
systemctl stop firewalld.service
systemctl status firewalld.service
//禁止开机启动防火墙
systemctl disable firewalld.service
```
   
 ## CentOS ntpd 时间服务器  

```
//安装ntp服务
yum install ntp

//启动ntpd服务
service ntpd start
service ntpd status

//设置ntpd服务开机启动
chkconfig ntpd on
//必须关掉这个服务，会影响ntpd开机启动
systemctl disable chronyd.service
systemctl enable ntpd.service

//设置时区，下面将时区设置为上海
[root@cdh1 ~]# cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
cp: overwrite ‘/etc/localtime’? yes

//时间同步
ntpdate -u ntp.api.bz

//将时间写入BIOS
hwclock -w

```

## CentOS搭建NTP服务器

192.168.1.171: 此机器负责与外部网络的NTPD服务同步标准时间，并作为局域网内的NTPD服务器。 
192.168.1.172.: 与NTPD服务同步时间。
192.168.1.173.与NTPD服务同步时间。
  
  ### 配置内网NTP-Server(192.168.1.171)
  

``` 
// 允许客户机的配置：允许任何IP的客户机都可以进行时间同步
# restrict default nomodify notrap nopeer noquery
restrict default nomodify

//通过网络同步时间
server 0.cn.pool.ntp.org iburst
server 1.cn.pool.ntp.org iburst
server 2.cn.pool.ntp.org iburst
server 3.cn.pool.ntp.org iburst

//使用本地时间
server 127.127.1.0     # local clock
fudge  127.127.1.0 stratum 10

```

### 配置内网NTP-Server(192.168.1.172、192.168.1.173)

``` 
# server 0.centos.pool.ntp.org iburst
# server 1.centos.pool.ntp.org iburst
# server 2.centos.pool.ntp.org iburst
# server 3.centos.pool.ntp.org iburst

server cdh1 iburst

```

### 在 172 、173两台机器上同步时间

[root@cdh2 init.d]# date cdh1



# Linux Netcat 命令——网络工具中的瑞士军刀 

etcat是网络工具中的瑞士军刀，它能通过TCP和UDP在网络中读写数据。通过与其他工具结合和重定向，你可以在脚本中以多种方式使用它。使用netcat命令所能完成的事情令人惊讶。

netcat所做的就是在两台电脑之间建立链接并返回两个数据流，在这之后所能做的事就看你的想像力了。你能建立一个服务器，传输文件，与朋友聊天，传输流媒体或者用它作为其它协议的独立客户端。

## Netcat安装

安装nc和nmap的包

``` 
[root@master1 bin]# yum install nmap -y
[root@master1 bin]# yum install nmap -y
```

## Chat Server
假如你想和你的朋友聊聊，有很多的软件和信息服务可以供你使用。但是，如果你没有这么奢侈的配置，比如你在计算机实验室，所有的对外的连接都是被限制的，你怎样和整天坐在隔壁房间的朋友沟通那？不要郁闷了，netcat提供了这样一种方法，你只需要创建一个Chat服务器，一个预先确定好的端口，这样子他就可以联系到你了。

**Server**

``` 
<!--
	netcat 命令在1567端口启动了一个tcp 服务器，所有的标准输出和输入会输出到该端口。输出和输入都在此shell中展示。
-->
[root@master1 bin]# nc -l 1567
```

**Client**

``` 
<!--
	不管你在机器B上键入什么都会出现在机器A上。
-->
[root@salve1 ~]# nc 192.168.1.191 1567
```



# Linux 文件权限

## 1、Linux用户

### 1.1 /etc/passwd文件

Linux系统使用了一个专门的文件将用户的登录名匹配到 UID， 这个文件就是/etc/passwd

```
[root@master1 bin]# cat /etc/passwd
root:x:0:0:root:/root:/bin/bash

1、root: 登陆用户名；
2、x: 用户密码(这里密码为x,并不代表密码真的是x; Linux为了安全，将密码保存在/etc/shadow)；
3、0: 用户账户的UID；
4、0: 用户账户的GID；
5、root: 用于账户的文本描述(备注)；
6、/root: 用户HOME目录的位置；
7、/bin/bash：用户默认的shell；
```



### 1.2 /etc/shadow文件

/etc/shadow文件用来管理密码，只有root用户才能访问此文件，它比/etc/passwd文件要安全很多。

```
[root@master1 bin]# cat /etc/shadow
root:$6$SqSKaQ7R13rhcQDa$50dzhwbsFRtQd9.FmgMj3ziAvkIBjaO.fJ2sTVmrJwMFufYTrC4ZJJs9g48/Ceg.eZD2DivlYLSmoYAnpRNdf0::0:99999:7:::

1、root: 与 /etc/passwd对应的登陆名；
2、$6$SqSKaQ7R13rhcQDa$50dzhwbsFRtQd9.FmgMj3ziAvkIBjaO.fJ2sTVmrJwMFufYTrC4ZJJs9g48/Ceg.eZD2DivlYLSmoYAnpRNdf0： 加密后的密码；


```



### 1.3 新增用户

```
[root@master1 bin]# useradd dongkang

1、新用户会默认被添加到GID为100的公共组；
```



### 1.4 删除用户

```
[root@master1 bin]# userdel -r dongkang

1、 -r 表示在删除用户的同时，删除其对应的文件目录。
```



### 1.5 修改用户

**修改用户密码**

```
[root@master1 bin]# passwd dongkang

1、如果只用passwd（后面不加用户名称），它会改变你自己的密码；
2、只有roor用户才有权限改变别人的密码；
```



## 2 Linux组

### 2.1 /etc/group文件

/etc/group文件用于保存组信息。

```
[root@master1 bin]# cat /etc/group
root:x:0:

1、root :组名
2、x：组密码
3、0：组GID
```



### 2.2 创建组

```
<!-- 
  创建组，默认没有用户属于该组成员
-->
[root@master1 bin]# groupadd shared

<!--
  添加用户到该组
-->
[root@master1 bin]# usermod -G shared dongkang

<!--
查看用户所属的组
-->
[root@master1 bin]# groups dongkang
```



### 2.3 修改组

```
<!--
	修改组名
	1、-n 表示修改组名；
	2、shared 原组名
	3、sharing 新组名
-->
[root@master1 bin]# groupmod -n sharing shared
```



## 3、Linux文件权限

### 3.1 理解文件权限

```
<!--
	使用ll命令查看文件的详细信息
-->
[root@master1 temp]# ll
总用量 8
-rw-r--r--. 1 root root  44 12月  4 20:55 customers.txt
-rw-r--r--. 1 root root 115 12月  4 20:55 orders.txt

```

**-rw-r--r-- 解释**

**第一个字符**
​	- 表示是一个文件
​	d 表示是一个目录
**第2-4个字符**
​	表示文件属主的权限
**第5-7个字符**
​	表示文件属组成员的权限
**第8-10个字符**
​	表示其它用户的权限



**第一个root解释**

​	表示对象的属主



**第二个root解释**

​	表示对象的属组

### 3.2 Linux文件权限码

| 权限 | 二进制值 | 八进制值 | 描述                         |
| ---- | -------- | -------- | ---------------------------- |
| ---  | 000      | 0        | 无任何权限                   |
| ---x | 001      | 1        | 执行权限                     |
| -w-  | 010      | 2        | 写入权限                     |
| -wx  | 011      | 3        | 写入权限、执行权限           |
| r--  | 100      | 4        | 只读权限                     |
| r-x  | 101      | 5        | 只读权限、执行权限           |
| rw-  | 110      | 6        | 执行权限、写入权限           |
| rwx  | 111      | 7        | 只读权限、写入权限、执行权限 |



### 3.2 修改文件所属用户

```
[root@master1 temp]# chown dongkang orders.txt 
[root@master1 temp]# ll
总用量 8
-rw-r--r--. 1 root     root  44 12月  4 20:55 customers.txt
-rwxrwxrwx. 1 dongkang root 115 12月  4 20:55 orders.txt
```



### 3.3 修改文件所属组

```
[root@master1 temp]# chgrp sharing orders.txt
[root@master1 temp]# ll
总用量 8
-rw-r--r--. 1 root     root     44 12月  4 20:55 customers.txt
-rwxrwxrwx. 1 dongkang sharing 115 12月  4 20:55 orders.txt
```



### 3.4 修改文件权限

```
[root@master1 temp]# chmod 777 orders.txt 
[root@master1 temp]# ll
总用量 8
-rw-r--r--. 1 root root  44 12月  4 20:55 customers.txt
-rwxrwxrwx. 1 root root 115 12月  4 20:55 orders.txt
```

