
# Redis ZSet类型及操作

Sorted set是set的一个升级版本，它在set的基础上增加了一个顺序属性，这一属性在添加修改元素时候可以指定，每次指定后，zset会自动重新按新的值调整顺序。可以理解为有两列字段的数据表，一列存value,一列存顺序编号。操作中key理解为zset的名字。

## zadd

zadd:向名称为key的zset中添加元素member,score用于排序。如果该元素存在，则更新其顺序。
用法：zadd 有序集合 顺序编号 元素值

``` 
//向zset中连续添加3个元素
redis(192.168.56.13:7002)>zadd zset1 1 two
redis(192.168.56.12:7000)>zadd zset1 2 one
redis(192.168.56.12:7000)>zadd zset1 3 seven

```

## zrange

zrange:显示集合中指定下标的元素值(按score从小到大排序)。如果需要显示元素的顺序编号，带上withscores。
用法：zrange 有序集合  下标索引1 下标索引2 withscores

``` 
redis(192.168.56.12:7000)>zrange zset1 0 -1 withscores
 1)  "two"
 2)  "1"
 3)  "one
"
 4)  "2"
 5)  "seven"
 6)  "3"
```
## zremrangebyrank

zremrangebyrank:删除集合中排名在给定区间的元素。
用法：zremrangebyrank 有序集合 索引编号1 索引编号2)

``` 
redis(192.168.56.12:7000)>zremrangebyrank zset1 0 0
"1"
redis(192.168.56.12:7000)>zrange zset1 0 -1 withscores
 1)  "one
"
 2)  "2"
 3)  "seven"
 4)  "3"
```
## Zremrangebyscore

 Zremrangebyscore 命令用于移除有序集中，指定分数（score）区间内的所有成员。

``` 
redis(192.168.56.12:7000)>Zremrangebyscore zset1 2 3
"2"
redis(192.168.56.12:7000)>zrange zset1 0 -1 withscores
 1)  "two"
 2)  "1"
redis(192.168.56.12:7000)>
```

#  


