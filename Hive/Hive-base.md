# 什么是Hive?

传统的MR程序适合处理一些复杂的业务，对于一些简单的业务我们可以使用Hive来实现；Hive是一个用来处理结构化数据的数据仓库。

# Hive的特点

* 提供了HiveQL语句；
* 不适合实时查询；
* 不适合行级更新（无法更新数据）；
* 用于处理结构化的数据；

# Hive的架构

下面的组件图描绘了Hive的结构：

![](image\hive_framework.jpg)

该组件图包含不同的单元。下表描述每个单元：

* **用户接口/界面:**  Hive是一个数据仓库基础工具软件，可以创建用户和HDFS之间互动。用户界面，Hive支持是Hive的Web UI，Hive命令行，HiveHD洞察（在Windows服务器）。
* **元数据：** Hive选择各自的数据库服务器，用以储存表，数据库，列模式或元数据表，它们的数据类型和HDFS映射。
* **HiveQL处理引擎：** HiveQL类似于SQL的查询上Metastore模式信息。这是传统的方式进行MapReduce程序的替代品之一。相反，使用Java编写的MapReduce程序，可以编写为MapReduce工作，并处理它的查询。
* **执行引擎：** HiveQL处理引擎和MapReduce的结合部分是由Hive执行引擎。执行引擎处理查询并产生结果和MapReduce的结果一样。它采用MapReduce方法。
* **HDFS 或 HBASE：** Hadoop的分布式文件系统或者HBASE数据存储技术是用于将数据存储到文件系统。



# Hive安装

**Hive只需要在master节点安装即可；**

## 1、解压Hive安装包

```
[root@master1 ~]# tar -zxvf apache-hive-2.3.4-bin.tar.gz
```



## 2、配置环境变量

```
<!-- /etc/profile 文件中添加如下内容 -->
#HIVE
export HIVE_HOME=/root/Hive/apache-hive-2.3.4-bin
export PATH=$HIVE_HOME/bin:$PATH
```



## 3、修改Hive配置文件

```
<!-- 数据库连接地址 -->
  <property>
    <name>javax.jdo.option.ConnectionURL</name>
    <value>jdbc:mysql://192.168.1.190:3306/hive</value>
    <description>
      JDBC connect string for a JDBC metastore.
      To use SSL to encrypt/authenticate the connection, provide database-specific SSL flag in the connection URL.
      For example, jdbc:postgresql://myhost/db?ssl=true for postgres database.
    </description>
  </property>
   <!-- 驱动程序 -->
  <property>
    <name>javax.jdo.option.ConnectionDriverName</name>
    <value>com.mysql.jdbc.Driver</value>
    <description>Driver class name for a JDBC metastore</description>
  </property>
  <!-- 用户名 -->
  <property>
    <name>javax.jdo.option.ConnectionUserName</name>
    <value>root</value>
    <description>Username to use against metastore database</description>
  </property>
  <!-- 密码 -->
  <property>
    <name>javax.jdo.option.ConnectionPassword</name>
    <value>123456</value>
    <description>password to use against metastore database</description>
  </property>
```

***注意：在配置文件中需要将${system:...字样替换成具体路径。***



## 4、将mysql驱动放置到Hive lib目录下面

## 5、MySql服务器设置

```
<!-- 关闭系统防火墙 -->
[root@master2 ~]# systemctl stop firewalld.service

<!-- 设置当前MySql数据库能够被远程访问 -->
[root@master2 ~]# mysql -uroot -p123456
mysql> GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY '123456' WITH GRANT OPTION;
```



## 6、初始化hive的元数据(表结构)到mysql中

```
[root@master1 bin]# cd /root/Hive/apache-hive-2.3.4-bin/bin
[root@master1 bin]# schematool -dbType mysql -initSchema
```



## 7、验证Hive是否安装成功

执行下面的命令，如果能正确的显示版本号，说明Hive已经安装成功。

```
[root@master1 Hive]# hive --version
```



# Hive的Shell环境

使用下面的命令即可启动Hive Shell环境。

```
<!-- 进入Hive Shell环境 -->
[root@master1 bin]# cd /root/Hive/apache-hive-2.3.4-bin/bin
[root@master1 bin]# ./hive

<!-- 退出Hive Shell环境 -->
hive> exit;

```


# 数据准备

为了方便测试，我们需要准备一张客户表（customer）,一张订单表（order）;并导入相应的数据。

//创建客户表，并导入相应的数据
hive> CREATE TABLE customers(id int,name string,age int) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' ;
//加载数据到客户表中
hive> load data local inpath '/root/file/customers.txt' into table customers ;

//客户数据如下 customers.txt
1,zhangsan,12
2,lisi,23
3,wanwu,45


//创建订单表，并导入相应的数据
hive> CREATE TABLE orders(id int,orderno string,price float,cid int) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' ;
hive> load data local inpath '/root/file/orders.txt' into table orders ;

//订单数据如下：
1,1001,11.34,1
2,1002,12.34,2
3,1003,13.34,3
4,1004,14.34,1
5,1005,15.34,2
6,1006,16.34,3


# Hive的服务

## 1、cli

命令行服务；

## 2、hiveserver2

针对远程服务访问的一种机制，默认端口是1000；相当于服务端，如果需要远程访问hive,就必须启动hiveserver2；

我们把它称作 thriftServer；

[root@master1 bin]# hive --service hiveserver2 &   //启动hiveserver2

[root@master1 bin]# netstat -anop | grep 10000    //查看hiveserver2是否启动成功

## 3、beeline

相当于hiveserver2的客户端，可以通过beeline连接hiveserver2访问hive;

[root@master1 bin]# hive --service beeline                             //使用beeline
beeline> !connect jdbc:hive2://localhost:10000/mydb2           //连接hive数据仓库

# Hive数据类型

## Hive 中数据精度的问题

使用Sqoop向Hive中导入数据的时候，会出现下面问题；

mySql( score [float])    ->   Hive(score[float])
12.45                                  12.449999809265137

mySql中float、double类型的字段，如果在Hive中也为相应float、double的类型；则会出现上面的精度问题。

解决办法：

TINYINT、SMALLINT、INT、BIGINT 统一使用 BIGINT类型；
float、double、decimal统一使用decimal类型；
string统一使用string类型；


## ARRAY、Map、struct

ARRAY：
ARRAY类型是由一系列相同数据类型的元素组成，这些元素可以通过下标来访问。
比如有一个ARRAY类型的变量fruits，它是由 ['apple','orange','mango']组成，那么我们可以通过fruits[1]来访问元素orange，因为ARRAY类型的下标是从 0开的； 

MAP：
MAP包含key->value键值对，可以通过key来访问元素。
比如”userlist”是一个map类型，其中username是 key，password是value；那么我们可以通过userlist['username']来得到这个用户对应的password； 

STRUCT：
STRUCT可以包含不同数据类型的元素。这些元素可以通过”点语法”的方式来得到所需要的元素。
比如user是一个STRUCT类型，那么可以通过user.address得到这个用户的地址。

例子

``` 
CREATE external TABLE mydb2.hive_user(
name string,
age int,
info struct<
            score:int,
			love:array<string>
			addr:map<string,string>
		    >
)
ROW FORMAT SERDE 'org.apache.hive.hcatalog.data.JsonSerDe'
STORED AS TEXTFILE;
```

上表对应的数据如下所示：

{"name":"zhang san", "age":13}
{"name":"li si", "age":23, "info":{"score":89}}
{"name":"Bob", "age":23, "info":{"score":89, "love":["pingpang","basketball"],"addr":{"country":"CH", "city":"shanghai"}}}

# Hive数据库

## 创建数据库

**在hdfs中数据库其实就是目录**

hive> create database mydb2;

hive> show databases;    //查询所有数据库

mysql> select * from DBS;  //在mysql中查看原数据信息

# Hive表

* hive表不支持删除；

## 托管表和外部表的创建

**托管表：** LOAD数据到Hive表中是一个移动操作；DROP是一个删除操作，所以数据会彻底消失。

**外部表：** 外部数据的位置需要在表创建的时候指定；丢弃外部表时，Hive只会删除Metadata数据，而不会删除HDFS上的数据。

hive> CREATE TABLE customers(id int,name string,age int);   //创建表，默认创建托管表

hive> insert into customers(id,name,age) values(1,'zhangsan',12);   //向表中插入数据，（转换成MR程序执行插入操作）

hive> show tables;   //查询所有的表信息

hive> desc formatted customers;  //显示customers表的详细信息

hive> drop table customers; //删除表，托管表删除的时候数据也会被删除


## 加载数据到hive表

//创建一张外部表t2;
//属性之间使用逗号分隔；
//存储形式为文本格式；
hive> CREATE external TABLE IF NOT EXISTS t2(id int, name string,age int) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' STORED AS TEXTFILE ; 

//加载本地数据到hive表（复制一份数据到HDFS）
hive> load data local inpath '/root/file/customers.txt' into table t2 ;

//加载HDFS中的数据到hive表（移动HDFS文件）
//overwrite表示是否覆盖原数据 ，可以加可以不加
hive> load data inpath '/file/customers.txt' [overwrite] into table t2;

## 复制表

//从t2复制一张新表t3, 复制表结构和数据
hive> create table t3 as select * from t2;

//从t2复制一张新表t4, 只复制表结构，不复制数据
hive> create table t4 like t2;

## 分区表

### 静态分区

分区表的好处是可以在指定的目录下查找，分区也是HIVE的重要优化手段之一；
所谓分区就是目录；

//按照年/月创建分区表
hive> CREATE TABLE t3(id int,name string,age int) PARTITIONED BY (Year INT, Month INT) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' ;

//添加分区
hive> alter table t3 add partition (year=2014, month=12);

//显示表的分区信息
hive> show partitions t3;

//删除分区
hive> ALTER TABLE t3 DROP IF EXISTS PARTITION (year=2014, month=12);

//加载数据到分区中
hive>  load data local inpath '/root/file/customers.txt' into table t3 partition (year=2014, month=12) ;

### 动态分区

Hive本身是不支持动态分区的；
但动态分区是真的方便啊..不然手动维护要累死..按日期甚至小时来分区时动辄就好几千上万的分区..手动到哪一年去..?

想要用动态分区要先做一些设置来修改默认的配置：
hive> set hive.exec.dynamic.partition=true;  //允许动态分区
hive> set hive.exec.dynamic.partition.mode=nonstrict;   //非严格模式（可以不指定静态分区）

//使用动态分区的方式向分区表导入数据
//最后两个字段year,month默认为分区字段
hive> insert into table t3 partition(year,month) select id,name,age,year,month from t2 where id=4;

## 桶表

桶表的好处是指定id查询的时候，可以从指定的文件中搜索；桶表的实质是将存储的文件划分成块（注意分区是目录，桶是文件）；
桶表不能使用LOAD关键字加载数据；

//创建桶表,按照id分作3个桶
hive>  CREATE TABLE t4(id int,name string,age int) CLUSTERED BY (id) INTO 3 BUCKETS ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' ;

//查看桶表的信息
hive> desc formatted t4;

//加载数据到桶表， 按照桶id进行hash存储到不同的文件中；
hive> insert into t4 select id,name,age from t2 ;

### 桶的数量如何划分

桶的数量太大或者太小都是不合适的，每个桶的数据量是block的两倍大小就可以；如总的数据量为1G左右，这样我们就可以分作4个桶（1G/(128M*2)；

# HiveQL

## 连接查询

//内连接
hive> select a.*, b.* from customers a, orders b where a.id=b.cid;

//左外连接查询
hive> select a.*, b.* from customers a left outer join orders b on a.id=b.cid;

//右外连接查询
hive> select a.*, b.* from customers a right outer join orders b on a.id=b.cid;

//全外连接查询
hive> select a.*, b.* from customers a full outer join orders b on a.id=b.cid;

## union查询

hive> select id,name from customers union select id,orderno from orders ;

## 导出表

//导出表数据 + 结构，导出到HDFS
//customers是一个目录
hive> export table customers to '/file/customers';

## 排序

//order by 全排序(参考MR全排序)
hive> select * from orders order by id desc;

//sort by map端排序，不在reduce端排序，只能保证本地有序
hive> select * from orders sort by id desc;

## DISTRIBUTE BY

DISTRIBUTE BY的作用是分区
相同的列被分配给同一个reduce处理，类似于ORACLE中的GROUP BY  操作；

//先按照cid分区、然后按照id排序
hive> select * from orders distribute by cid sort by id asc;

## CLUSTER BY

hive> select * from orders cluster by cid;
等价于
hive> select * from orders distribute by cid sort by cid;

## GROUP BY

//根据cid进行聚合
hive> select cid,count(*) c ,max(price) from orders group by cid having c > 1;

## map端连接

map端连接不需要reduce，因此性能会比较高；

//map端连接方式一：设置自动转换连接,默认开启了。
hive> set hive.auto.convert.join=true			

//map端连接方式二：使用hint， /*+ mapjoin(customers) */
hive>  select /*+ mapjoin(customers) */ a.*,b.* from customers a left outer join orders b on a.id = b.cid ;

## Hive Join On 不等条件实现办法

hql的join on操作只支持相等条件，比如：
select * from a join b on a.id=b.id;

但是不支持相等条件以外的情况，比如：
select * from a join b on a.id <> b.id;
select * from a join b on a.name like '%'+b.name+'%';

这是因为Hive很难把不等条件翻译成mapreduce job； 
但是工作中我们需要实现不等条件，比如微博需要向用户推送私信，但白名单的用户除外，现在全部用户的uid在表weibouid表的alluid分区，白名单在baimingdan分区，现在用join on实现去除alluid中的白名单uid,两个分区的uid是去重的。

select a.uid from
(select a.uid as auid,b.uid as buid from
(select uid from weibouid where part='alluid')a
left join
(select uid from weibouid where part='baimingdan')b
on a.uid = b.uid)c where c.buid is null;


# Hive自带函数

可以在 Hive 命令行中按 	Tab 键查看所有的函数；

## concat()

//将“100”和“tomas”两个字符串连接起来
hive> select concat('100','tomas');

## current_user()和 current_date()

//current_user() 表示当前用户
//current_date() 表示当前时间
hive> select current_user(),current_date();

## row_number() over()排序功能

 row_number() over()分组排序功能：
 
在使用 row_number() over()函数时候，over()里头的分组以及排序的执行晚于 where group by  order by 的执行；
partition by 用于给结果集分组，如果没有指定那么它把整个结果集作为一个分组；
它和聚合函数不同的地方在于它能够返回一个分组中的多条记录，而聚合函数一般只有一个反映统计值的记录；

``` 
-- 对每门课程进行排名
--首先根据c_id进行结果集分组，结果集内部按照s_score排序
-- partition by 如果不写，则所有的数据为一个分组
select
    score.s_id,
    s_name,
    s_score score,
    row_number() over(partition by score.c_id order by score.s_score desc) Ranking
 from score ,student
 where score.s_id=student.s_id;

```


# Hive事务

Hive事物在0.13.0之后才支持的；

Hive事务有以下的特点：
1、所有事务自动提交；
2、只支持orc格式的文件；
3、必须使用bucket表；
4、配置hive参数，使其支持事务；

//查看打开的事务
hive> show transactions;

//设置hive参数，使其支持事务

hive> SET hive.support.concurrency = true;
hive> SET hive.enforce.bucketing = true;
hive> SET hive.exec.dynamic.partition.mode = nonstrict;
hive> SET hive.txn.manager = org.apache.hadoop.hive.ql.lockmgr.DbTxnManager;
hive> SET hive.compactor.initiator.on = true;
hive> SET hive.compactor.worker.threads = 1;

//创建一个支持事务的桶(bucket)表
hive> CREATE TABLE tx(id int,name string,age int) CLUSTERED BY (id) INTO 3 BUCKETS ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' stored as orc TBLPROPERTIES ('transactional'='true');

//向事务表插入一条数据
hive> insert into tx(id,name,age) values(1,'zhangsan',23);
//事务表支持更新操作（非事务表不支持更新操作）;
hive> update tx set name='lisi' where id=1；

# Hive实现单词统计

//创建一张表
hive> CREATE TABLE doc(line string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' ;

//加载文档到新建的表中
hive> load data local inpath '/root/file/doc.txt' into table doc;

文档如下 doc.txt（空格分隔）：
hello world tom
hello world bob
hello world lisi
hello world zhangsan

//使用hive自带的函数 做单词统计
//split() 切分一行成为数组
//explode() 把数据炸开成为单独的行
hive> select t.word,count(*) c from ((select explode(split(line, ' ')) as word from doc) as t) group by t.word order by c desc limit 4 ;

# Hive 视图

视图就是为了简化查询；

//创建视图
hive> create view v1 as select a.id aid,a.name ,b.id bid , b.orderno from customers a left outer join default.orders b on a.id = b.cid ;

//查询视图v1
hive> select * from v1;


# Hive中对json形式文本的处理

1、数据准备

从本地 put json 格式的文本文件到HDFS下

/file/json/user.txt

{"name":"zhang san", "age":13}
{"name":"li si", "age":23, "info":{"score":89}}
{"name":"Bob", "age":23, "info":{"score":89, "love":["pingpang","basketball"],"addr":{"country":"CH", "city":"shanghai"}}}

2、JsonSerDe序列化形式创建表

``` 
CREATE external TABLE mydb2.hive_user(
name string,
age int,
info struct<
            score:int,
			love:array<string>
			addr:map<string,string>
		    >
)
ROW FORMAT SERDE 'org.apache.hive.hcatalog.data.JsonSerDe'
STORED AS TEXTFILE;

load data inpath '/file/json/user.txt' into table mydb2.hive_user;

```

3、查看表中的数据

select name,info.love[0],info.addr from hive_user;



# HIVE UDF

用户自定义函数

//显示所有的函数
hive> show functions;

//查看current_database函数的帮助
hive> desc function current_database;

## 创建UDF

1、创建java类；我们实现一个加法函数；

代码1：Maven依赖

```

 <dependency>
            <groupId>org.apache.hive</groupId>
            <artifactId>hive-service</artifactId>
            <version>2.3.4</version>
 </dependency>
		
```

代码2：自定义函数

``` 

package com.dongk.hive.udf;

import org.apache.hadoop.hive.ql.exec.Description;
import org.apache.hadoop.hive.ql.exec.UDF;

@Description(
        name = "addUDF",
        //describe information
        value = " addUDF(int a, int b) ==> return a + b",
        //describe extended information
        extended = "Example:\n"
                   + " addUDF(1,1) ==> 2 \n"
                   + " addUDF(1,2,3) ==> 6;"
)
public class AddUDF extends UDF {

    //方法名必须为evaluate
    //tow param
    public int evaluate(int a,int b){
        return a + b;
    }

    //three param
    public int evaluate(int a,int b,int c){
        return a + b + c;
    }

}


```

2、将程序打包成jar包；

3、添加jar包到hive的类路径(lib);

4、添加jar包到类路径；

hive> ADD JAR /root/Hive/apache-hive-2.3.4-bin/lib/HIVE-1.0-SNAPSHOT.jar;

5、创建临时函数(临时函数只在当前会话中有效)；

hive> CREATE TEMPORARY FUNCTION addUDF AS 'com.dongk.hive.udf.AddUDF';

6、在查询中使用自定义函数；

hive> select addudf(34,12);

## GenericUDF使用

GenericUDF可以实现比UDF更加复杂的功能；同时性能也较UDF高。

下面我们使用Hive自定义函数，将Hive分析结果导入MySql数据库中。

1、编写函数。

``` 
package com.dongk.hive.udf;

import org.apache.hadoop.hive.ql.exec.Description;
import org.apache.hadoop.hive.ql.exec.UDFArgumentException;
import org.apache.hadoop.hive.ql.exec.UDFArgumentTypeException;
import org.apache.hadoop.hive.ql.metadata.HiveException;
import org.apache.hadoop.hive.ql.udf.generic.GenericUDF;
import org.apache.hadoop.hive.serde.Constants;
import org.apache.hadoop.hive.serde2.objectinspector.ObjectInspector;
import org.apache.hadoop.hive.serde2.objectinspector.PrimitiveObjectInspector;
import org.apache.hadoop.hive.serde2.objectinspector.primitive.PrimitiveObjectInspectorFactory;
import org.apache.hadoop.hive.serde2.objectinspector.primitive.StringObjectInspector;
import org.apache.hadoop.io.IntWritable;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.SQLException;

@Description(
        name = "" +
                "",
        value = "_FUNC_(jdbctring,username,password,preparedstatement,[arguments])"
                + " - sends data to a jdbc driver",
        extended = "argument 0 is the JDBC connection string\n"
                + "argument 1 is the database user name\n"
                + "argument 2 is the database user's password\n"
                + "argument 3 is an SQL query to be used in the PreparedStatement\n"
                + "argument (4-n) The remaining arguments must be primitive and are "
                + "passed to the PreparedStatement object\n"
)
public class AnalyzeOutputUDF extends GenericUDF {

    private transient ObjectInspector[] argumentOI;
    private transient Connection connection = null;
    private String url;
    private String user;
    private String pass;
    private final IntWritable result = new IntWritable(-1);

    //这个方法只调用一次，并且在evaluate()方法之前调用;
    //该方法接收的参数是一个ObjectInspectors数组，该方法检查接收正确的参数类型和参数个数
    public ObjectInspector initialize(ObjectInspector[] arguments) throws UDFArgumentException {
        argumentOI = arguments;

        // this should be connection
        // url,username,password,query,column1[,columnn]*
        for (int i = 0; i < 4; i++) {
            if (arguments[i].getCategory() == ObjectInspector.Category.PRIMITIVE) {
                PrimitiveObjectInspector poi = ((PrimitiveObjectInspector) arguments[i]);

                if (!(poi.getPrimitiveCategory() == PrimitiveObjectInspector.PrimitiveCategory.STRING)) {
                    throw new UDFArgumentTypeException(i,
                            "The argument of function should be \""
                                    + Constants.STRING_TYPE_NAME + "\", but \""
                                    + arguments[i].getTypeName()
                                    + "\" is found");
                }
            }
        }
        for (int i = 4; i < arguments.length; i++) {
            if (arguments[i].getCategory() != ObjectInspector.Category.PRIMITIVE) {
                throw new UDFArgumentTypeException(i,
                        "The argument of function should be primative"
                                + ", but \"" + arguments[i].getTypeName()
                                + "\" is found");
            }
        }

        return PrimitiveObjectInspectorFactory.writableIntObjectInspector;
    }

    //这个方法类似evaluate方法，处理真实的参数，返回最终结果
    public Object evaluate(DeferredObject[] arguments) throws HiveException {

        url = ((StringObjectInspector) argumentOI[0]).getPrimitiveJavaObject(arguments[0].get());
        user = ((StringObjectInspector) argumentOI[1]).getPrimitiveJavaObject(arguments[1].get());
        pass = ((StringObjectInspector) argumentOI[2]).getPrimitiveJavaObject(arguments[2].get());

        try {
            connection = DriverManager.getConnection(url, user, pass);
        } catch (SQLException ex) {
            ex.printStackTrace();
            result.set(2);
        }

        if (connection != null) {
            try {

                PreparedStatement ps = connection
                                      .prepareStatement(((StringObjectInspector) argumentOI[3])
                                      .getPrimitiveJavaObject(arguments[3].get()));
                for (int i = 4; i < arguments.length; ++i) {
                    PrimitiveObjectInspector poi = ((PrimitiveObjectInspector) argumentOI[i]);
                    ps.setObject(i - 3, poi.getPrimitiveJavaObject(arguments[i].get()));
                }
                ps.execute();
                ps.close();
                result.set(0);
            } catch (SQLException ex) {
                ex.printStackTrace();
                result.set(1);
            } finally {
                try {
                    connection.close();
                } catch (Exception ex) {
                    ex.printStackTrace();
                }
            }
        }

        return result;
    }

    //此方法用于当实现的GenericUDF出错的时候，打印提示信息，提示信息就是该方法的返回的字符串
    public String getDisplayString(String[] strings) {
        StringBuilder sb = new StringBuilder();
        sb.append("dboutput(");
        if (strings.length > 0) {
            sb.append(strings[0]);
            for (int i = 1; i < strings.length; i++) {
                sb.append(",");
                sb.append(strings[i]);
            }
        }
        sb.append(")");
        return sb.toString();
    }
}

```

2、注册临时函数 analyzedboutput()
hive> CREATE TEMPORARY FUNCTION analyzedboutput AS 'com.dongk.hive.udf.AnalyzeOutputUDF';

3.使用自定义函数 analyzedboutput()

select analyzedboutput('jdbc:mysql://192.168.1.192:3306/test','root','123456','insert into hive values(?,?)',id,cid) from orders;

# Java客户端访问Hive

## Maven依赖 pom.xml

``` 
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>groupId</groupId>
    <artifactId>HIVE</artifactId>
    <version>1.0-SNAPSHOT</version>

    <dependencies>
        <dependency>
            <groupId>org.apache.hive</groupId>
            <artifactId>hive-jdbc</artifactId>
            <version>2.3.4</version>
        </dependency>
    </dependencies>

    
</project>

```

## 执行查询语句

``` 
package com.dongk.hive.demo;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.ResultSet;
import java.sql.Statement;

public class App {
    public static void main(String args[]){
        try {
            Class.forName("org.apache.hive.jdbc.HiveDriver");
            Connection conn = DriverManager.getConnection("jdbc:hive2://192.168.1.191:10000/mydb2");

            Statement st = conn.createStatement();
            ResultSet rs = st.executeQuery("select id, name, age from customers ");
            while(rs.next()){
                System.out.println(rs.getInt(1) + "," + rs.getString(2) + "," + rs.getInt(3));
            }
            rs.close();
            st.close();
        }catch (Exception ex){
            ex.printStackTrace();
        }
    }
}

```

# Hive的优化技巧

## explain

查看执行计划

``` 
hive> explain select count(*) from orders;
OK
STAGE DEPENDENCIES:
  Stage-1 is a root stage
  Stage-0 depends on stages: Stage-1   //阶段0依赖于阶段1

STAGE PLANS:
  Stage: Stage-1     //阶段1
    Map Reduce 
      Map Operator Tree:  //Map操作
          TableScan      //表扫描
            alias: orders
            Statistics: Num rows: 1 Data size: 94 Basic stats: COMPLETE Column stats: COMPLETE
            Select Operator
              Statistics: Num rows: 1 Data size: 94 Basic stats: COMPLETE Column stats: COMPLETE
              Group By Operator 
                aggregations: count()
                mode: hash
                outputColumnNames: _col0
                Statistics: Num rows: 1 Data size: 8 Basic stats: COMPLETE Column stats: COMPLETE
                Reduce Output Operator
                  sort order: 
                  Statistics: Num rows: 1 Data size: 8 Basic stats: COMPLETE Column stats: COMPLETE
                  value expressions: _col0 (type: bigint)
      Reduce Operator Tree:  //reduce操作
        Group By Operator    //聚合操作
          aggregations: count(VALUE._col0)
          mode: mergepartial
          outputColumnNames: _col0
          Statistics: Num rows: 1 Data size: 8 Basic stats: COMPLETE Column stats: COMPLETE
          File Output Operator
            compressed: false
            Statistics: Num rows: 1 Data size: 8 Basic stats: COMPLETE Column stats: COMPLETE
            table:
                input format: org.apache.hadoop.mapred.SequenceFileInputFormat
                output format: org.apache.hadoop.hive.ql.io.HiveSequenceFileOutputFormat
                serde: org.apache.hadoop.hive.serde2.lazy.LazySimpleSerDe

  Stage: Stage-0   //阶段0
    Fetch Operator  //提取操作
      limit: -1     //无limit
      Processor Tree:
        ListSink

Time taken: 0.151 seconds, Fetched: 42 row(s)
```

## 本地模式

本地运行map-reduce作业。这对于在小型数据集上运行查询非常有用 - 在这种情况下，本地模式的执行通常比向大型集群提交作业要快得多。从HDFS透明地访问数据。相反，本地模式只能运行一个reducer，处理较大的数据集可能非常慢。

### 完全本地模式
对于所有mapreduce任务都以本地模式运行，要启用此功能，用户可以启用以下选项：

//开启本地模式
hive> SET mapreduce.framework.name = local;
//设置本地模式运行的临时目录(可以不用设置，有默认值)
SET mapred.local.dir=/tmp/username/mapred/local；

### 自动本地模式

Hive通过条件判断是否通过本地模式运行mapreduce任务。

条件为： 
作业的总输入大小低于：hive.exec.mode.local.auto.inputbytes.max，默认为128MB ；
map任务的总数小于：hive.exec.mode.local.auto.tasks.max，默认为4 ；
所需的reduce任务总数为1或0；

//开启自动本地模式
hive> set hive.exec.mode.local.auto = true;

## 并行执行

用过oracle rac的应该都知道parallel的用途。
并行执行的确可以大的加快任务的执行速率，但不会减少其占用的资源。

在hive中也有并行执行的选项。
//打开任务并行执行
hive> set hive.exec.parallel=true;   
//同一个sql允许最大并行度，默认为8。我们可以通过配置来修改这个值；
hive> set hive.exec.parallel.thread.number=16; 

对于同一个SQL产生的JOB,如果不存在依赖的情况下，可以使用并行执行；比如插入语句。

## 严格模式

Hive配置中有个参数hive.mapred.mode，分为nonstrict，strict，默认是nonstrict
如果设置为strict，会对三种情况的语句将不被允许：

//设置为严格模式
hive> set hive.mapred.mode=strict; 

1、分区表必须指定分区进行查询，否则查询语句将会报错；
2、使用order by时必须使用limit子句；
3、不允许笛卡尔积；

## JVM重用

JVM重用是hadoop调优参数的内容，对hive的性能具有非常大的影响，特别是对于很难避免小文件的场景或者task特别多的场景，这类场景大多数执行时间都很短。hadoop默认配置是使用派生JVM来执行map和reduce任务的，这是jvm的启动过程可能会造成相当大的开销，尤其是执行的job包含有成千上万个task任务的情况。

JVM重用可以使得JVM实例在同一个JOB中重新使用N次，N的值可以在Hadoop的mapre-site.xml文件中进行设置
mapred.job.reuse.jvm.num.tasks

也可在hive的执行设置：
hive> set  mapred.job.reuse.jvm.num.tasks=10;

## 控制 reduce 的数量


**set hive.exec.reducers.bytes.per.reducer=number**

这是一条Hive命令，用于设置在执行SQL的过程中每个reducer处理的最大字节数量。可以在配置文件中设置，也可以由我们在命令行中直接设置。
如果处理的数据量大于number，就会多生成一个reudcer。例如，number = 1024K，处理的数据是1M，就会生成10个reducer。
因此如果我们知道数据的大小，只要通过set hive.exec.reducers.bytes.per.reducer命令设置每个reducer处理数据的大小就可以控制reducer的数量。

**set hive.exec.reducers.max=number**

这是一条Hive命令，用于设置Hive的最大reducer数量，如果我们设置number为50，表示reducer的最大数量是50。

**set mapreduce.job.reduces=number**

这条Hive命令是设置reducer的数量，在执行sql会生成多少个reducer处理数据。

## 解决数据倾斜问题

### Group By 中的计算均衡优化

先看看下面这条SQL，由于用户的性别只有男和女两个值 （未知）。如果没有map端的部分聚合优化，map直接把groupby_key 当作reduce_key发送给reduce做聚合，就会导致计算不均衡的现象。虽然map有100万个，但是reduce只有两个在做聚合，每个reduce处理100亿条记录。

**select user.gender, count(1) from user group by user.gende**

![enter description here](./images/hive_skewing001_1.jpg)

没开map端聚合产生的计算不均衡现象

**hive.map.aggr=true**
参数控制在group by的时候是否map局部聚合，这个参数默认是打开的。参数打开后的计算过程如下图。由于map端已经做了局部聚合，虽然还是只有两个reduce做最后的聚合，但是每个reduce只用处理100万行记录，相对优化前的100亿小了1万;

![enter description here](./images/hive_skewing002.jpg)

map端聚合开关缺省是打开的，但是不是所有的聚合都需要这个优化。考虑先面的sql,如果groupby_key是用户ID，因为用户ID没有重复的，因此map聚合没有太大意义，并且浪费资源。     

**select user..id,count(1) from user group by user.id**

**hive.groupby.mapaggr.checkinterval = 100000**
**Hive.map.aggr.hash.min.reduction=0.5**

上面这两个参数控制关掉map聚合的策略。Map开始的时候先尝试给前100000 条记录做hash聚合，如果聚合后的记录数/100000>0.5说明这个groupby_key没有什么重复的，再继续做局部聚合没有意义，100000 以后就自动把聚合开关关掉。


**数据倾斜**

通常这种情况都是在有distinct出现的时候，比如下面的sql,由于map需要保存所有的user.id,map聚合开关会自动关掉，导致出现计算不均衡的现象，只有2个redcue做聚合，每个reduce处理100亿条记录。

**select user.gender,count(distinct user.id ) from user group by user.gender **

![enter description here](./images/hive_skewing003.jpg)

**hive.groupby.skewindata =true**
参数会把上面的sql翻译成两个MR，第一个MR的reduce_key是gender+id。因为id是一个随机散列的值，因此这个MR的reduce计算是很均匀的，reduce完成局部聚合的工作。

![enter description here](./images/hive_skewing004.jpg)

MR1第二个MR完成最终的聚合，统计男女的distinct id值，数据流如下图所示，每个Map只输出两条记录，因此虽然只有两个redcue计算也没有关系，绝大部分计算量已经在第一个MR完成。

![enter description here](./images/hive_skewing005.jpg)

hive.groupby.skewindata默认是关闭的，因此如果确定有不均衡的情况，需要手动打开这个开关。当然，并不是所有的有distinct的group by都需要打开这个开关，比如下面的sql。因为user.id是一个散列的值，因此已经是计算均衡的了，所有的reduce都会均匀计算。只有在groupby_key不散列，而distinct_key散列的情况下才需要打开这个开关，其他的情况map聚合优化就足矣。

**select id,count (distinct gender) from user group by user.id**

## hive distinct优化

需求，计算总共有多少学生有成绩

select count(distinct s_id ) from score;

hive针对count(distinct xxx)只产生一个reduce的优化。

造成原因：

由于使用了distinct，导致在map端的combine无法合并重复数据；对于这种count()全聚合操作时，即使设定了reduce task个数，set mapred.reduce.tasks=100；hive也只会启动一个reducer。这就造成了所有map端传来的数据都在一个tasks中执行，成为了性能瓶颈。。

解决方法：随机分组法

``` 
select 
    sum(tc)
from (
    select
       count(*) tc,
       tag
    from (
        select
            cast(rand()*100 as bigint) tag,
            s_id 
        from 
            score
        group by s_id
    ) t1
    group by tag
) t2;
```


# Hive常见问题

## 匿名用户登录权限问题

root is not allowed to impersonate anonymous

解决办法如下：

1、Hadoop 配置文件 core-site.xml 添加如下内容，并重启Hadoop

``` 
<!-- 配置超级代理用户 -->
 <property>
        <name>hadoop.proxyuser.root.hosts</name>
        <value>*</value>
	</property>
	<property>
        <name>hadoop.proxyuser.root.groups</name>
        <value>*</value>
	</property>

```
	
上面的配置hadoop.proxyuser.xxx.hosts和hadoop.proxyuser.xxx.groups中的xxx设置为root(即你的错误日志中显示的User：xxx为什么就设置为什么)。
表示可通过超级代理“xxx”操作hadoop的用户、用户组和主机。

主要原因是hadoop引入了一个安全伪装机制，使得hadoop 不允许上层系统直接将实际用户传递到hadoop层，而是将实际用户传递给一个超级代理，由此代理在hadoop上执行操作，避免任意客户端随意操作hadoop，如下图：

![enter description here](./images/hive_question1.jpg)

2、更改HDFS中对应的/tmp文件更改权限

[root@master1 bin]# hdfs dfs -chmod 777 /tmp

# Hive sql语句必练50题-入门到精通(1)

https://blog.csdn.net/Thomson617/article/details/83212338

## 数据准备

/ file/ student/ student.csv

``` 
01	赵雷	1990-01-01	男
02	钱电	1990-12-21	男
03	孙风	1990-05-20	男
04	李云	1990-08-06	男
05	周梅	1991-12-01	女
06	吴兰	1992-03-01	女
07	郑竹	1989-07-01	女
08	王菊	1990-01-20	女
```

/ file/ student/ course.csv

``` 
01	语文	02
02	数学	01
03	英语	03
```


/ file/ student/ teacher.csv

``` 
01	张三
02	李四
03	王五
```

/ file/ student/ score.csv

``` 
01	01	80
01	02	90
01	03	99
02	01	70
02	02	60
02	03	80
03	01	80
03	02	80
03	03	80
04	01	50
04	02	30
04	03	20
05	01	76
05	02	87
06	01	31
06	03	34
07	02	89
07	03	98
```

创建表

``` 
create table student(s_id string,s_name string,s_birth string,s_sex string) row format delimited fields terminated by '\t';

create table course(c_id string,c_name string,t_id string) row format delimited fields terminated by '\t';

create table teacher(t_id string,t_name string) row format delimited fields terminated by '\t';

create table score(s_id string,c_id string,s_score int) row format delimited fields terminated by '\t';

```

加载数据到表中

``` 
load data  inpath '/file/student/student.csv' into table student;

load data  inpath '/file/student/course.csv' into table course;

load data  inpath '/file/student/teacher.csv' into table teacher;

load data  inpath '/file/student/score.csv' into table score;
```


## 查询平均成绩大于等于60分的同学的学生编号和学生姓名和平均成绩:

``` 
select 
  student.s_id,
  student.s_name,
  round(avg (score.s_score),1) as avgscore
from student
join score on student.s_id = score.s_id
group by student.s_id, student.s_name
having avg(score.s_score) >= 60;
```

## 查询平均成绩小于60分的同学的学生编号和学生姓名和平均成绩:

(包括有成绩的和无成绩的)

需要主要的事HIVE中的not in是不支持子查询的，可以使用 left join 来改写

``` 

select  score.s_id,student.s_name,round(avg (score.s_score),1) as avgScore from student
inner join score on student.s_id=score.s_id
group by score.s_id,student.s_name
having avg (score.s_score) < 60
union all
select  s2.s_id,s2.s_name,0 as avgScore from student s2
where s2.s_id not in
    (select distinct sc2.s_id from score sc2);
	
## 上面这种方式是不可以的，因为HIVE中的not in是不支持子查询的

select
  score.s_id,
  student.s_name,
  round(avg (score.s_score),1) as avgScore 
from student
inner join score on student.s_id=score.s_id
group by score.s_id,student.s_name
having avg (score.s_score) < 60
union all
select
  s2.s_id,s2.s_name,
  0 as avgScore 
from student s2
left join score c2 on s2.s_id=c2.s_id
where c2.s_score is null;

```

## 查询"李"姓老师的数量

``` 
select t_name,count(1) from teacher  where t_name like '李%' group by t_name;
```

## 查询没学过"张三"老师授课的同学的信息:

``` 
with temp as(
    select 
       s_id 
    from score
    join  course on course.c_id=score.c_id
    join  teacher on course.t_id=teacher.t_id and t_name='zhangsan'
)
select s.* from student s
left join temp t on s.s_id=t.s_id
where t.s_id is null
```

## 查询各科成绩最高分、最低分和平均分

–及格为>=60，中等为：70-80，优良为：80-90，优秀为：>=90

以如下形式显示：课程ID，课程name，最高分，最低分，平均分，及格率，中等率，优良率，优秀率:

``` 
select 
    course.c_id,
    course.c_name,
    tmp.maxScore,
    tmp.minScore,
    tmp.avgScore,
    tmp.passRate,
    tmp.moderate,
    tmp.goodRate,
    tmp.excellentRates 
from course
join(
    select 
        c_id,
        max(s_score) as maxScore,
        min(s_score)as minScore,
        round(avg(s_score),2) avgScore,
        round(sum(case when s_score>=60 then 1 else 0 end)/count(c_id),2) passRate,
        round(sum(case when s_score>=60 and s_score<70 then 1 else 0 end)/count(c_id),2) moderate,
        round(sum(case when s_score>=70 and s_score<80 then 1 else 0 end)/count(c_id),2) goodRate,
        round(sum(case when s_score>=80 and s_score<90 then 1 else 0 end)/count(c_id),2) excellentRates
    from score group by c_id
    ) tmp on tmp.c_id=course.c_id;

```

## 查询学生的总成绩并进行排名

``` 
select
    score.s_id,
    s_name,
    sum(s_score) sumscore,
    row_number() over(order by sum(s_score) desc) Ranking
 from score ,student
 where score.s_id=student.s_id
 group by score.s_id,s_name order by sumscore desc;

```



# Hive Sql语法总结

## Hive不支持join的非等值连接,不支持or

分别举例如下及实现解决办法。
不支持不等值连接
错误:select * from a inner join b on a.id<>b.id
替代方法:select * from a inner join b on a.id=b.id and a.id is null;

不支持or
错误:select * from a inner join b on a.id=b.id or a.name=b.name
 替代方法:select * from a inner join b on a.id=b.id
 union all
 select * from a inner join b on a.name=b.name
 两个sql union all的字段名必须一样或者列别名要一样。

## HIVE不识别分号

分号字符:不能智能识别concat(‘;’,key)，只会将‘；’当做SQL结束符号。

解决的办法是，使用分号的八进制的ASCII码进行转义，那么上述语句应写成：
select concat(s_name,concat('\073',s_sex)) from student;

# Hive 正则表达式


