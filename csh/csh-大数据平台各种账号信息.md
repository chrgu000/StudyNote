# 测试环境

## IP主机名映射

192.168.1.100  master
192.168.1.99 slave1
192.168.1.98 slave2
192.168.1.97 slave3
192.168.1.96 slave4

## CDH

地址：http://192.168.1.100:7180/cmf/home

用户名：admin
密码： admin

## HUE

地址：http://slave2:8888/accounts/login/

用户名：colourlife         
密码：colourlife123456
数据魔方
地址：http://192.168.1.96:9002/index.html

用户名：sktest
密码：123456

## MySQL地址

地址 ：192.168.1.100 
端口 ：3306
用户名：root
密码：123456
数据库：etl_test_src、etl_test_tar

# 正式环境

## IP主机映射

192.168.56.12 slave1
192.168.56.13 slave2
192.168.56.14 slave3
192.168.56.15 slave4
192.168.56.16 slave5
192.168.56.17 slave6
192.168.56.18 slave7
192.168.56.19 slave8
192.168.56.11 hamaster
192.168.56.10 master

## CDH

地址：http://192.168.56.11:7180
用户名：colourlife
密码：colourlife123456

## HUE

数据魔方
内网地址：http://192.168.0.137:9000/login/login.html
外网地址：http://etl.cnfantasia.com:9000/login/login.html
用户名：colourlife     
密码：123456

# 正式环境Jar包提交服务器
 

主机地址：cdhmaster.cnfantasia.com
主机外网IP：cdhmaster.cnfantasia.com [210.75.13.8]
用户名：colourlife
端口：8081
密码：colourlife123456

运行jar包的命令如下
nohup java -jar  -Xms500m -Xmn500m -Xmx2g /root/caihuibi_kafka/caihuibi-0.0.1-SNAPSHOT.jar  >/dev/null  2>&1 &

start "CaiHuiBiJar" nohup java -jar  -Xms500m -Xmn500m -Xmx2g caihuibi-0.0.1-SNAPSHOT.jar 

注意：程序中的日志文件、认证文件需要放到colourlife用户根目录下面。

# VPN

VPN地址：https://vpn.cnfantasia.com
用户名：dashuju
密码：hyn12345

用户名：dashuju2
密码：hyn12345

链接工具：EasyConnect
