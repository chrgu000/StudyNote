
# Sqoop

Sqoop名字来源 : SQL - TO - Hadoop 将关系型数据库中的数据转储到Hadoop中；

Sqoop的本质是MapReduce程序；

RDBMS -> Hadoop(HDFS, Hive, HBase)     数据导入过程 import
 Hadoop(HDFS, Hive)  -> RDBMS  数据导出过程 import , 暂时不支持从HBase导出到关系型数据库中 
 
 # Sqoop 的安装
 
 1、下载解压
 
 [root@master1 sqoop]# tar -zxvf sqoop-1.4.6.bin__hadoop-2.0.4-alpha.tar.gz
 
 2、配置sqoop
 
 [root@master1 conf]# cp -a sqoop-site-template.xml sqoop-site.xml
 [root@master1 conf]# cp -a sqoop-env-template.sh sqoop-env.sh
 
sqoop-env.sh

HBase和Zookeeper暂时不配置，Zookeeper是和HBase一起搭配使用的。

``` 
#export HADOOP_COMMON_HOME=
HADOOP_COMMON_HOME=/root/Hadoop/hadoop-2.6.2

#Set path to where hadoop-*-core.jar is available
#export HADOOP_MAPRED_HOME=
HADOOP_MAPRED_HOME=/root/Hadoop/hadoop-2.6.2

#set the path to where bin/hbase is available
#export HBASE_HOME=

#Set the path to where bin/hive is available
#export HIVE_HOME=
HIVE_HOME=/root/Hive/apache-hive-2.3.4-bin

#Set the path for where zookeper config dir is
#export ZOOCFGDIR=
```
 
3、拷贝mysql驱动到 Sqoop lib 目录下面
 
 
4、验证Sqoop是否可以正常启动

出现下面的内容说明是可以正常启动的

[root@master1 bin]# ./sqoop help
Warning: /root/sqoop/sqoop-1.4.6.bin__hadoop-2.0.4-alpha/bin/../../hcatalog does not exist! HCatalog jobs will fail.
Please set $HCAT_HOME to the root of your HCatalog installation.
Warning: /root/sqoop/sqoop-1.4.6.bin__hadoop-2.0.4-alpha/bin/../../accumulo does not exist! Accumulo imports will fail.
Please set $ACCUMULO_HOME to the root of your Accumulo installation.
Warning: /root/sqoop/sqoop-1.4.6.bin__hadoop-2.0.4-alpha/bin/../../zookeeper does not exist! Accumulo imports will fail.
Please set $ZOOKEEPER_HOME to the root of your Zookeeper installation.
19/03/25 14:01:04 INFO sqoop.Sqoop: Running Sqoop version: 1.4.6
usage: sqoop COMMAND [ARGS]

Available commands:
  codegen            Generate code to interact with database records
  create-hive-table  Import a table definition into Hive
  eval               Evaluate a SQL statement and display the results
  export             Export an HDFS directory to a database table
  help               List available commands
  import             Import a table from a database to HDFS
  import-all-tables  Import tables from a database to HDFS
  import-mainframe   Import datasets from a mainframe server to HDFS
  job                Work with saved jobs
  list-databases     List available databases on a server
  list-tables        List available tables in a database
  merge              Merge results of incremental imports
  metastore          Run a standalone Sqoop metastore
  version            Display version information

See 'sqoop help COMMAND' for information on a specific command.


# Sqoop脚本打包

1、新建一个opt文件

[root@master1 task]# touch job_allimport.opt

2、编写sqoop脚本

``` 

import
--connect 
jdbc:mysql://192.168.1.192:3306/eshop
--username
root
--password
123456
--table
users
--target-dir
/file/eshop
--num-mappers
1
--fields-terminated-by
"\t"

```

3、执行该脚本

[root@master1 bin]# ./sqoop --options-file ../../task/job_allimport.opt


# Sqoop 全部导入

``` 
import
--connect 
jdbc:mysql://192.168.1.192:3306/eshop
--username
root
--password
123456
--table
users
--target-dir
/file/eshop
--num-mappers
1
--fields-terminated-by
"\t"

```

# 数据采集技巧

## 尽量避免使用 *

--query "select * from user;"

如果我们写成上面的这种形式，那么源端发生变化的时候，我们是无法及时发现的，会造成数据错误；