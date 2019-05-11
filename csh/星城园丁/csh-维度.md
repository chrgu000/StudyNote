# dim_date(日期维度表)

## 建表

``` 
--创建表之后，预装载200年日期

drop table dim_date;
create table dim_date
(
   sequence_id  bigint COMMENT '代理建',
   dateStr	string  COMMENT '日期',
   year	string  COMMENT '年',
   month string  COMMENT '月',
   month_name string  COMMENT '月名称',
   quarter string  COMMENT '周',
   day string  COMMENT '日'
)
row format delimited
fields terminated by '\t'
lines terminated by '\n' 
stored as textfile;

```