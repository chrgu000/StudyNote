
# Impala 和 Hive的关系

![enter description here](./attachments/impala-base001(impala和hive的关系).xls)

# Impala的优点和缺点

优点：

1、速度快；
	 
缺点：

1、内存消耗比较大（因为是基于内存进行查询的），内存大小128G起步；
2、底层是通过c++实现的，所以维护难度比较大；
3、和hive相互依存；
4、没有hive稳定；

# Impala的架构

![enter description here](./attachments/impala-base002(impala的架构).xls)


# Impala的安装

安装规划
192.168.1.191 impala-catalog impala-state-store
192.168.1.181 impala-server
192.168.1.182 impala-server
192.168.1.183 impala-server

# Impala 命令

## 重新加载数据库中的所有表

INVALIDATE METADATA;