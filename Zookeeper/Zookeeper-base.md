# 什么是Zookeeper?

Zookeeper是一个分布式协调服务。

# Zookeeper组件

**Zookeeper 是复制的（replicated）**，就像它协调的分布式进程一样，Zookeeper 自身也在被称为“ensemble” 的一组主机之间进行复制。

![Zookeeper组件图](./images/zookeeper001.jpg)

 1）客户端（Client）：获取Server端的数据，且周期性发送数据给server，表示当前客服端存活。server返回ack（确认信息）信息。如果客户端没有收到ack信息，客户端会自动重定向到另一个server。
 
 2） 服务器（Server）：Zookeeper集群中的一员，负责向客户端（Client）提供数据。

 3） ensemble：一组Server，节点数不能小于3个。

 4）Leader：负责进行投票的发起和决议，更新系统状态。

 5）Follower：用于接受客户端请求并想客户端返回结果，在选举过程中参与投票。
 
# Sessions（会话）
会话对于ZooKeeper的操作非常重要。会话中的请求按FIFO顺序执行。一旦客户端连接到服务器，将建立会话并向客户端分配会话ID 。

客户端以特定的时间间隔发送心跳以保持会话有效。如果ZooKeeper集合在超过服务器开启时指定的期间（会话超时）都没有从客户端接收到心跳，则它会判定客户端死机。

会话超时通常以毫秒为单位。当会话由于任何原因结束时，在该会话期间创建的临时节点也会被删除。

# znode简介

Zookeeper维护着一个树形层次结构，树中的节点被称为znode；znode可以用于存储数据，并且有与之相关联的一些元数据。元数据如下所示：

* **版本号 -**  每个znode都有版本号，这意味着每当与znode相关联的数据发生变化时，其对应的版本号也会增加。当多个zookeeper客户端尝试在同一znode上执行操作时，版本号的使用就很重要。在高并发访问的时候，往往先读取版本号，在写入时如果版本号发生变化，则说明已有其它客户端修改此节点，本次写入操作失败（类似于乐观锁）。

* **操作控制列表(ACL) -**  ACL基本上是访问znode的认证机制。它管理所有znode读取和写入操作。

* **时间戳 -**  时间戳表示创建和修改znode所经过的时间。它通常以毫秒为单位。ZooKeeper从“事务ID"(zxid)标识znode的每个更改。Zxid 是唯一的，并且为每个事务保留时间，以便你可以轻松地确定从一个请求到另一个请求所经过的时间。

* **数据长度 -**  存储在znode中的数据总量是数据长度。你最多可以存储1MB的数据。

# znode类型

* **持久节点（persistent）：** 即使在创建该特定znode的客户端断开连接后，持久节点仍然存在。默认情况下，除非另有说明，否则所有znode都是持久的。
* **临时节点（sequential）：** 客户端活跃时，临时节点就是有效的。当客户端与ZooKeeper集合断开连接时，临时节点会自动删除。因此，只有临时节点不允许有子节点。如果临时节点被删除，则下一个合适的节点将填充其位置。临时节点在leader选举中起着重要作用。
* **顺序节点（ephemeral）：** 顺序节点可以是持久的或临时的。当一个新的znode被创建为一个顺序节点时，ZooKeeper通过将10位的序列号附加到原始名称来设置znode的路径。例如，如果将具有路径 /myapp 的znode创建为顺序节点，则ZooKeeper会将路径更改为 /myapp0000000001 ，并将下一个序列号设置为0000000002。如果两个顺序节点是同时创建的，那么ZooKeeper不会对每个znode使用相同的数字。顺序节点在锁定和同步中起重要作用。


# Zookeeper 工作流
一旦ZooKeeper集合启动，它将等待客户端连接。客户端将连接到ZooKeeper集合中的一个节点。它可以是leader或follower节点。一旦客户端被连接，节点将向特定客户端分配会话ID并向该客户端发送确认。如果客户端没有收到确认，它将尝试连接ZooKeeper集合中的另一个节点。 一旦连接到节点，客户端将以有规律的间隔向节点发送心跳，以确保连接不会丢失。
* **读：** 如果客户端想要读取特定的znode，它将会向具有znode路径的节点发送读取请求，并且节点通过从其自己的数据库获取来返回所请求的znode。为此，在ZooKeeper集合中读取速度很快。
* **写：** 如果客户端想要将数据存储在ZooKeeper集合中，则会将znode路径和数据发送到服务器。连接的服务器将该请求转发给leader，然后leader将向所有的follower重新发出写入请求。如果只有大部分节点成功响应，而写入请求成功，则成功返回代码将被发送到客户端。 否则，写入请求失败。绝大多数节点被称为 Quorum 。

# Zookeeper leader选举（最小号选举法）
让我们分析如何在ZooKeeper集合中选举leader节点。考虑一个集群中有N个节点。leader选举的过程如下：

* 所有节点创建具有相同路径 /app/leader_election/guid_ 的顺序、临时节点。
* ZooKeeper集合将附加10位序列号到路径，创建的znode将是 /app/leader_election/guid_0000000001，/app/leader_election/guid_0000000002等。
* 对于给定的实例，在znode中创建最小数字的节点成为leader，而所有其他节点是follower。
* 每个follower节点监视下一个具有最小数字的znode。例如，创建znode/app/leader_election/guid_0000000008的节点将监视znode/app/leader_election/guid_0000000007，创建znode/app/leader_election/guid_0000000007的节点将监视znode/app/leader_election/guid_0000000006。
* 如果leader关闭，则其相应的znode/app/leader_electionN会被删除。
* 下一个在线follower节点将通过监视器获得关于leader移除的通知。
* 下一个在线follower节点将检查是否存在其他具有最小数字的znode。如果没有，那么它将承担leader的角色。否则，它找到的创建具有最小数字的znode的节点将作为leader。

# Zookeeper的安装

下面我们使用 192.168.1.181（salve1）、 192.168.1.182（salve2）、 192.168.1.183（salve3）三台机器搭建一个Zookeeper集群。

## 1 解压安装包

``` 
[root@salve1 Zookeeper]# tar -zxvf zookeeper-3.4.9.tar.gz
```

## 2 配置 Zookeeper

**zoo.cfg**
``` 
# 心跳时间
tickTime=2000
# 初始化心跳次数
initLimit=10
# 数据同步心跳次数
syncLimit=5
# 数据存放文件夹
dataDir=/root/Zookeeper/zookeeper-3.4.9/data
# 客户端访问端口
clientPort=2181
# server.1 中的 1 ，必须和myid中配置的id相对应
# 2888 端口用于 follower 和 leader 之间的连接
# 3888 端口用于选举
server.1=salve1:2888:3888
server.2=salve2:2888:3888
server.3=salve3:2888:3888
```

## 3 将Zookeeper发送到其它节点，并配置myid

将上面在salve1节点配置的zookeeper发送到其它节点; 并在 /root/Zookeeper/zookeeper-3.4.9/data 目录下创建各自的myid文件。

**发送**

``` 
[root@salve1 Zookeeper]# scp -r zookeeper-3.4.9  root@192.168.1.182:/root/Zookeeper
[root@salve1 Zookeeper]# scp -r zookeeper-3.4.9  root@192.168.1.183:/root/Zookeeper
```

**salve1 myid**

``` 
[root@salve1 conf]# cat /root/Zookeeper/zookeeper-3.4.9/data/myid 
1
```

**salve2 myid**

``` 
[root@salve2 conf]# cat /root/Zookeeper/zookeeper-3.4.9/data/myid 
2
```

**salve3 myid**

``` 
[root@salve3 conf]# cat /root/Zookeeper/zookeeper-3.4.9/data/myid 
3
```

## 4 配置环境变量

salve1、salve2、salve3环境变量配置

**/etc/profile**
``` 
##Zookeeper
ZK_HOME=/root/Zookeeper/zookeeper-3.4.9
export  PATH=$PATH:ZK_HOME/bin
```

## 5.启动Zookeeper

``` 
[root@salve1 bin]# ./zkServer.sh start
```



## 6.验证Zookeeper是否启动并查看状态
使用Jps命令查看，如果存在进程QuorumPeerMain，则说明Zookeeper启动成功。

``` 
<!-- 查看进程是否启动 -->
[root@salve1 bin]# jps
2547 QuorumPeerMain

<!-- 查看zkServer属于Leader或者是Follower -->
[root@salve1 bin]#  ./zkServer.sh status
```

# Zookeeper命令

## 1.进入Zookeeper客户端

``` 
[root@salve1 bin]# ./zkCli.sh -server localhost:2181
```

## 2.查看帮助

```
[zk: localhost:2181(CONNECTED) 0] help
```

## 3.创建数据

Zookeeper 使用树形结构来存储数据，每个节点就是一个znode; 每个znode允许存储的最大数据为1M。

``` 
<!--
	在根节点下面新建一个节点 "ZooDemo", 节点的数据为 "Hello Zookeeper"
-->
[zk: localhost:2181(CONNECTED) 2] create /ZooDemo "Hello Zookeeper"
```

## 4.获取数据

``` 
[zk: localhost:2181(CONNECTED) 3] get /ZooDemo
```

## 5.查看数据结构

``` 
[zk: localhost:2181(CONNECTED) 4] ls / 
```

## 6.修改数据

每次修改数据，znode版本都会加1。

``` 
[zk: localhost:2181(CONNECTED) 5] set /ZooDemo "hello Zookeeper set1"    
cZxid = 0x2
ctime = Mon Dec 10 13:52:50 CST 2018
mZxid = 0x3
mtime = Mon Dec 10 14:02:44 CST 2018
pZxid = 0x2
cversion = 0
dataVersion = 1      //版本号加1
aclVersion = 0
ephemeralOwner = 0x0
dataLength = 20
numChildren = 0
```

## 7.删除数据

``` 
[zk: localhost:2181(CONNECTED) 18] delete /ZooDemo
```

## 8.创建临时znode

``` 
[zk: localhost:2181(CONNECTED) 22] create -e /ephemeral "this is ephemeral znode"
Created /ephemeral
```

## 9.创建序列znode

我们可以通过下面的例子看到，序列znode会依次递增；就像数据库中的自增ID一样。

``` 
[zk: localhost:2181(CONNECTED) 1] create -s /myapp "sequence1"
Created /myapp0000000004
[zk: localhost:2181(CONNECTED) 2] create -s /myapp "sequence2"
Created /myapp0000000005
[zk: localhost:2181(CONNECTED) 3] ls /
[zookeeper, myapp0000000004, school, myapp0000000005]
[zk: localhost:2181(CONNECTED) 4] 
```

# java API 访问Zookeeper

## 1.添加Maven依赖

**pom.xml**

``` 
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.dongk</groupId>
    <artifactId>zookeeper</artifactId>
    <version>1.0-SNAPSHOT</version>

    <dependencies>
        <dependency>
            <groupId>org.apache.zookeeper</groupId>
            <artifactId>zookeeper</artifactId>
            <version>3.4.9</version>
        </dependency>

        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.11</version>
        </dependency>
    </dependencies>
</project>
```

## 2.输出所有的znode

``` 
package com.dongk.zk;

import org.apache.zookeeper.ZooKeeper;
import org.junit.Test;

import java.util.List;

public class ZKDemo {

    @Test
    public void getAllZnode() throws Exception{
        /**
         *  第一个参数 ： 表示zookeeper服务器的地址
         *  第二个参数 ： 表示超时时间
         * */
        ZooKeeper zk = new ZooKeeper("192.168.1.181:2181",5000,null);
        printZnode(zk,"/");

    }

    public void printZnode(ZooKeeper zk,String path)  throws Exception{
        List<String> list = zk.getChildren(path,null);
        if(null == list || list.isEmpty()){
            System.out.println(path);
        }else{
            for(String s : list){
                if(path.equals("/"))
                    printZnode(zk,"/" + s);
                else
                    printZnode(zk,path + "/" + s);
            }
        }

    }
}

```

## 3.监视znode信息

监视是一种简单的机制，使客户端收到关于ZooKeeper集合中的更改的通知。客户端可以在读取特定znode时设置Watches。Watches会向注册的客户端发送任何znode（客户端注册表）更改的通知。

Znode更改是与znode相关的数据的修改或znode的子项中的更改。只触发一次watches。如果客户端想要再次通知，则必须通过另一个读取操作来完成。当连接会话过期时，客户端将与服务器断开连接，相关的watches也将被删除。

``` 
package com.dongk.zk;

import org.apache.zookeeper.WatchedEvent;
import org.apache.zookeeper.Watcher;
import org.apache.zookeeper.ZooKeeper;
import org.apache.zookeeper.data.Stat;
import org.junit.Test;

public class ZKWatchDemo {

    @Test
    public void watcher() throws  Exception{

        final ZooKeeper zk = new ZooKeeper("192.168.1.181:2181",5000,null);

        /**
         *  Stat 用于表示znode节点的元数据
         */
        final Stat st = new Stat();

        Watcher watcher = new Watcher() {
            public void process(WatchedEvent watchedEvent) {
                try {
                    System.out.println("znode data is changed");
                    zk.getData("/school/classA/001", this, st);
                } catch (Exception e) {
                    e.printStackTrace();
                }
            }
        };

        byte[] data = zk.getData("/school/classA/001", watcher , st);

        System.out.println(new String(data));

        Thread.sleep(1000000000);

    }
}

```

## 4.zookeeper写数据

``` 
package com.dongk.zk;

import com.sun.org.apache.xml.internal.resolver.readers.ExtendedXMLCatalogReader;
import org.apache.zookeeper.ZooKeeper;
import org.apache.zookeeper.data.Stat;
import org.junit.Test;

public class ZKSetDemo {

    @Test
    public void set() throws Exception {
        final ZooKeeper zk = new ZooKeeper("192.168.1.181:2181",5000,null);
        final String path = "/school/classA/001";
        /**
         *  Stat 用于表示znode节点的元数据
         */
        Stat st = new Stat();
        byte[] data = zk.getData(path, null , st);
        //获取节点 /school/classA/001 的版本
        int version = st.getVersion();

        /**
         * 如果在这个过程中，有其他的客户端修改了此节点的数据；则version号会出现不一致的情况
         * 导致保存失败。（用于处理高并发情况下数据写入的问题）
         */
        zk.setData(path,"zhang san of java".getBytes(),version);

    }
}

```

## 5.创建临时节点

``` 
package com.dongk.zk;

import org.apache.zookeeper.CreateMode;
import org.apache.zookeeper.ZooDefs;
import org.apache.zookeeper.ZooKeeper;
import org.junit.Test;

public class ZKEphemeralDemo {

    @Test
    public void createEphemeral() throws Exception{
        ZooKeeper zk = new ZooKeeper("192.168.1.181:2181", 5000, null);
        /**
         *  ZooDefs.Ids.OPEN_ACL_UNSAFE 表示访问权限（ACL）
         *  CreateMode.EPHEMERAL 表示此节点是一个临时目录
         * */
        zk.create( "/school/classA/002" ,"zhang qiao".getBytes(), ZooDefs.Ids.OPEN_ACL_UNSAFE, CreateMode.EPHEMERAL);
    }
}

```