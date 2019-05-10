
# qrcode_statistical表

czysp_user_service.qrcode_statistical表

记录派出所对应二维码的下载和注册数

## ODS

```
drop table czysp_user_ods_qrcode_statistical;

create table if not exists czysp_user_ods_qrcode_statistical
(
    id bigint,
    source_uuid string COMMENT '二维码uuid',
    register_num bigint,
    target_num bigint COMMENT '目标任务书，默认为100',
    download_num bigint COMMENT '下载次数',
    descr string COMMENT '备注，此字段在原表中是desc',
    create_at bigint COMMENT '创建时间',
    update_at bigint,
    time_entry string comment "数据进入数仓日期"
) 
row format delimited
fields terminated by '\t'
lines terminated by '\n' 
stored as textfile;

--原表
PRIMARY KEY (`id`),
UNIQUE KEY `uuid` (`source_uuid`) USING BTREE
```

# user

## ODS

```
drop table czysp_user_ods_qrcode_statistical;

create table if not exists czysp_user_ods_user
 (
  id bigint COMMENT '用户id',
  mobile string COMMENT '用户手机号码',
  dd_uuid string COMMENT '钉钉uuid',
  identity_id string COMMENT '身份证号码',
  union_uuid string COMMENT '统一平台用户编号',
  wx_uuid string COMMENT '微信uuid',
  password string COMMENT '普通密码',
  gesture_code string COMMENT '手势密码',
  salt string COMMENT '密码加密盐',
  uuid string COMMENT '用户uuid',
  state bigint COMMENT '状态，1：正常，2：禁用',
  is_deleted bigint COMMENT '删除标记,1:正常，2：删除',
  time_create bigint,
  time_entry string comment "数据进入数仓日期"
) 
row format delimited
fields terminated by '\t'
lines terminated by '\n' 
stored as textfile;

--原表
PRIMARY KEY (`id`),
UNIQUE KEY `uuid` (`uuid`) USING HASH,
UNIQUE KEY `mobile` (`mobile`) USING HASH

```

# user_agent_log(用户注册设备信息表)

1）、这张表每个用户对应对应一条信息；

## ODS

```
drop table czysp_user_ods_user_agent_log;

create table if not exists czysp_user_ods_user_agent_log
 (
  user_id bigint COMMENT '用户id',
  time_create bigint COMMENT '创建时间',
  ip_create bigint COMMENT '创建ip',
  agent_create string COMMENT '创建设备信息',
  time_update bigint COMMENT '更新时间',
  ip_update bigint COMMENT '最后更新ip',
  agent_update string COMMENT '最后更新身板信息',
  device_create string COMMENT '创建设备uuid',
  device_update string COMMENT '更新设备uuid',
  time_entry string comment "数据进入数仓日期"
)
row format delimited
fields terminated by '\t'
lines terminated by '\n' 
stored as textfile;

--原表
UNIQUE KEY `user_id` (`user_id`)

```

## DWD

```
drop table czysp_user_dwd_user_agent_log;

create table if not exists czysp_user_dwd_user_agent_log(
   transaction_sk bigint comment "代理键",
   user_id bigint COMMENT '用户id',
   time_create string COMMENT '创建时间',
   ip_create bigint COMMENT '创建ip',
   agent_create string COMMENT '创建设备信息',
   time_update string COMMENT '更新时间',
   ip_update bigint COMMENT '最后更新ip',
   agent_update string COMMENT '最后更新身板信息',
   device_create string COMMENT '创建设备uuid',
   device_update string COMMENT '更新设备uuid',
   time_entry string comment "数据进入数仓日期",
   version bigint comment "版本"
)
row format delimited
fields terminated by '\t'
lines terminated by '\n' 
stored as textfile;
```

## 第一次装载脚本

```
TRUNCATE user_manager_platform_test.czysp_user_dwd_user_agent_log;

insert into czysp_user_dwd_user_agent_log
select 
   row_number() over (order by t1.user_id) + t2.sk_max,
   user_id,
   from_unixtime(time_create),
   ip_create,
   agent_create,
   from_unixtime(time_update),
   ip_update,
   agent_update,
   device_create,
   device_update,
   t1.time_entry,
   1
from czysp_user_ods_user_agent_log t1
	cross join (select coalesce(max(transaction_sk),0) sk_max from 
               czysp_user_dwd_user_agent_log) t2;
```

