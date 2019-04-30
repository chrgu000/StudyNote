
# date_dim(日期维度表)

## 建表

``` 
--创建表之后，预装载200年日期

drop table date_dim;
create table date_dim
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

# statistics_dws_user_fact(用户行为统计表)

## 建表

``` 
drop table statistics_dws_user_fact;

create table statistics_dws_user_fact
(
   sequence_id   bigint COMMENT '日期代理建',
   register_acount bigint COMMENT '注册用户数',
   active_acount  bigint COMMENT '活跃用户数',
   gmv  bigint COMMENT '活跃用户数'
)
row format delimited
fields terminated by '\t'
lines terminated by '\n' 
stored as textfile;
```

## 初次装载

``` 
with registerinfo as (

    select 
        from_unixtime(time_create,'yyyy-MM-dd') as datestr,
        count(1) as register_acount --用户注册数
    from 
        user_dwd
    group by from_unixtime(time_create,'yyyy-MM-dd')
),
activeinfo as (
    select 
        from_unixtime(time_update,'yyyy-MM-dd') as datestr,
        count(1) as active_acount -- 用户活跃数
    from 
       user_manager_platform_test.user_agent_log_dwd
    group by from_unixtime(time_update,'yyyy-MM-dd')
)

select
    coalesce(r.datestr,a.datestr),
    coalesce(r.register_acount,0),
    coalesce(a.active_acount,0)
from
registerinfo r
full join 
activeinfo a
on r.datestr = a.datestr


```
