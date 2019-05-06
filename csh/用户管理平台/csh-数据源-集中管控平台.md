
集中管控平台数据库(colourlife_operation_system)

# user_manager_build(客户经理与楼栋关系表)

这张表会经常更新。

## ODS

```
drop table colourlife_operation_ods_user_manager_build;

create table if not exists colourlife_operation_ods_user_manager_build
(
  id bigint,
  user_id bigint COMMENT '用户Id',
  username string COMMENT 'OA账号',
  build_uuid string COMMENT '楼栋uuid',
  build_name string COMMENT '楼栋名称',
  community_uuid string COMMENT '小区uuid',
  community_name string COMMENT '小区名称',
  created_at bigint COMMENT '创建时间',
  created_by bigint COMMENT '创建人',
  updated_at bigint COMMENT '修改时间',
  is_del bigint COMMENT '状态：0.正常；1.停用；2.删除；',
  time_entry bigint comment "数据进入数仓日期"
)
row format delimited
fields terminated by '\t'
lines terminated by '\n' 
stored as textfile;

--原表的信息
PRIMARY KEY (`id`),
KEY `user_id` (`user_id`) USING BTREE,
KEY `is_del` (`is_del`) USING BTREE
```

## DWD 

```
drop table colourlife_operation_dwd_user_manager_build;

create table if not exists colourlife_operation_dwd_user_manager_build
(
  user_manager_build_sk bigint comment "代理键",
  id bigint,
  user_id bigint COMMENT '用户Id',
  username string COMMENT 'OA账号',
  build_uuid string COMMENT '楼栋uuid',
  build_name string COMMENT '楼栋名称',
  community_uuid string COMMENT '小区uuid',
  community_name string COMMENT '小区名称',
  created_at string COMMENT '创建时间',
  created_by bigint COMMENT '创建人',
  updated_at string COMMENT '修改时间',
  is_del bigint COMMENT '状态：0.正常；1.停用；2.删除；',
  time_entry string comment "数据进入数仓日期",
  version bigint comment "版本"
)
row format delimited
fields terminated by '\t'
lines terminated by '\n' 
stored as textfile;
```

## 第一次装载

```
use user_manager_platform_test;

TRUNCATE colourlife_operation_dwd_user_manager_build;

insert into colourlife_operation_dwd_user_manager_build
select 
   row_number() over (order by t1.id) + t2.sk_max,
   id bigint,
   user_id bigint COMMENT '用户Id',
   username string COMMENT 'OA账号',
   build_uuid string COMMENT '楼栋uuid',
   build_name string COMMENT '楼栋名称',
   community_uuid string COMMENT '小区uuid',
   community_name string COMMENT '小区名称',
   created_at string COMMENT '创建时间',
   created_by bigint COMMENT '创建人',
   updated_at string COMMENT '修改时间',
   is_del bigint COMMENT '状态：0.正常；1.停用；2.删除；',
   from_unixtime(t1.time_entry,'yyyy-MM-dd'),
   1
from colourlife_operation_ods_user_manager_build t1
	cross join (select coalesce(max(user_manager_build_sk),0) sk_max from 
               colourlife_operation_dwd_user_manager_build) t2;
               
```

## 定期每天装载

```

```