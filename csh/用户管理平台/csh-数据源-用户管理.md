

用户管理数据库(colourlife_user_service)

# User(用户主表)

## ODS

``` 
drop table colourlife_user_ods_user;
create table if not exists colourlife_user_ods_user
(
   id bigint comment "用户id",
   mobile string comment "用户手机号码",
   email string comment "邮箱",
   password string comment "普通密码",
   gesture_code string comment "手势密码",
   salt string comment "密码加密盐",
   uuid string comment "用户uuid",
   state bigint comment "状态，0：正常，1：禁用",
   is_deleted bigint comment "是否删除，1：正常，2：删除",
   pay_password bigint comment "支付密码",
   time_create bigint,
   wx_uuid string comment "微信uuid",
   qq_uuid string  comment "QQ对应的uuid",
   dd_uuid string  comment "钉钉uuid",
   time_entry string comment "数据进入数仓日期"
)
row format delimited
fields terminated by '\t'
lines terminated by '\n' 
stored as textfile;

```

## DWD

``` 

drop table colourlife_user_dwd_user;

create table if not exists colourlife_user_dwd_user
(
   user_sk bigint comment "代理键",
   id bigint comment "用户id",
   mobile string comment "用户手机号码",
   email string comment "邮箱",
   password string comment "普通密码",
   gesture_code string comment "手势密码",
   salt string comment "密码加密盐",
   uuid string comment "用户uuid",
   state bigint comment "状态，0：正常，1：禁用",
   is_deleted bigint comment "是否删除，1：正常，2：删除",
   pay_password bigint comment "支付密码",
   time_create bigint,
   wx_uuid string comment "微信uuid",
   qq_uuid string  comment "QQ对应的uuid",
   dd_uuid string  comment "钉钉uuid",
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

TRUNCATE colourlife_user_dwd_user;

insert into colourlife_user_dwd_user
select row_number() over (order by t1.id) + t2.sk_max,
   id,
   mobile,
   email,
   password,
   gesture_code,
   salt,
   uuid,
   state,
   is_deleted,
   pay_password ,
   time_create,
   wx_uuid,
   qq_uuid,
   dd_uuid,
   time_entry,
   1 
from colourlife_user_ods_user t1
	cross join (select coalesce(max(user_sk),0) sk_max from colourlife_user_dwd_user) t2;
	
```

# user_address(用户收货地址表)

## ODS

``` 
drop table colourlife_user_ods_user_address;

create table if not exists colourlife_user_ods_user_address(
	id bigint comment "id",
	user_id bigint comment "用户id",
	provice string comment "省名称",
	provice_code string comment "省份uuid",
	region string comment "市区名称",
	region_code string comment "市uuid",
	area string comment "地区名称",
	area_code string comment "地区uuid",
	detail string comment "详细街道地址",
	address string comment "备用地址字段",
	is_deleted string comment "1:正常，2：已删除"
)  
row format delimited
fields terminated by '\t'
lines terminated by '\n' 
stored as textfile;
```

# user_info(用户主表)

## ODS

``` 
drop table colourlife_user_ods_user_info;

create table if not exists colourlife_user_ods_user_info(
       user_id bigint comment "用户id",
	   nick_name string comment "用户昵称",
	   name string comment "用户姓名",
	   portrait string comment "用户头像",
	   register_type bigint comment "注册类型，0：网站注册，1：安卓注册,2:IOS注册",
	   chanel bigint comment "注册渠道，具体值待定",
	   gender bigint comment "性别，0：不确定，1：男，2：女",
	   gesture_state bigint comment "0:未设置，1：开启，2：未开启",
	   default_community_id bigint comment "默认选择小区地址id",
	   default_address_id bigint comment "默认选择收货地址id",
	   longitude decimal(10,6) comment "经度",
	   latitude decimal(10,6) comment "纬度",
	   permit_position bigint comment "是否允许显示到附近人位置，1允许，2不允许",
	   geohash string comment "geohash值"
)
row format delimited
fields terminated by '\t'
lines terminated by '\n' 
stored as textfile;

```
		
# user_identity(用户认证信息表)

## ODS

``` 
drop table colourlife_user_ods_user_identity;

create table if not exists colourlife_user_ods_user_identity(
	user_id bigint comment "用户id",
	identity_val string comment "证件号码加密值",
	identity_name string comment "身份证姓名",
	mobile string comment "手机号码",
	bank_id string comment "银行id",
	state bigint comment "待定",
	time_update bigint,
	note string comment "备注"
) 
row format delimited
fields terminated by '\t'
lines terminated by '\n' 
stored as textfile;
```

# user_community(用户地址表)

## ODS

``` 
drop table colourlife_user_ods_user_community;

create table if not exists colourlife_user_ods_user_community(
	id bigint comment "id",
	user_id bigint comment "用户id",
	community_uuid string comment "小区uuid",
	community_name string comment "小区名称",
	build_uuid string comment "楼栋uuid",
	build_name string comment "楼栋名称",
	room_uuid string comment "房间uuid",
	room_name string comment "房间名称",
	is_deleted bigint comment "1:正常，2：已删除",
	time_create bigint,
	time_update bigint,
	czy_id bigint,
	unit_uuid string,
	unit_name string,
	resident_uuid string comment "住户关系uuid",
	is_default bigint comment "1：默认"
)
row format delimited
fields terminated by '\t'
lines terminated by '\n' 
stored as textfile;

```
			  
# user_finance_relation(用户金融平台关联表)

## ODS

``` 
drop table colourlife_user_ods_user_finance_relation;

create table if not exists colourlife_user_ods_user_finance_relation(
	id bigint comment "自增量",
	user_id bigint comment "用户id",
	mobile string comment "用户手机号码",
	user_uuid string comment "用户uuid",
	pano string comment "平台饭票账号",
	atid bigint comment "平台饭票类型",
	cno string comment "用户号",
	cano string comment "用户饭票账号",
	time_create bigint comment "创建时间",
	time_update bigint,
	version bigint comment "1：旧，2：新uuid开通"
) 
row format delimited
fields terminated by '\t'
lines terminated by '\n' 
stored as textfile;

```

# invite_log(邀请记录表)

## ODS

```
drop table  colourlife_user_ods_invite_log;

create table if not exists colourlife_user_ods_invite_log(
	id bigint comment "自增量",
	user_id bigint comment "用户id",
	mobile string comment "邀请手机号码",
	time_create bigint comment "邀请时间",
	time_valid bigint comment "过期时间",
	invite_state bigint comment "邀请状态",
	time_register bigint comment "注册成功时间"
) 
row format delimited
fields terminated by '\t'
lines terminated by '\n' 
stored as textfile;

```

# user_third_auth(第三方注册用户表（微信、QQ，原表）)

## ODS

``` 
drop table colourlife_user_ods_user_third_auth;

create table if not exists colourlife_user_ods_user_third_auth(
	user_id bigint comment "用户id",
	source string comment "第三方来源",
	open_code string comment "第三方唯一标识",
	time_create bigint comment "创建时间",
	union_code string comment "唯一code",
	username string comment "第三方账号昵称"
)
row format delimited
fields terminated by '\t'
lines terminated by '\n' 
stored as textfile;
```

# user_agent_log(用户注册设备信息表)

## ODS

用户注册设备信息表

一个用户一条信息，time_update表示最近一次登录时间。

``` 
drop table colourlife_user_ods_user_agent_log;

create table if not exists colourlife_user_ods_user_agent_log(
  user_id bigint COMMENT '用户id',
  time_create bigint COMMENT '创建时间',
  ip_create bigint COMMENT '创建ip',
  agent_create string COMMENT '创建设备信息',
  time_update bigint COMMENT '更新时间',
  ip_update bigint COMMENT '最后更新ip',
  agent_update string  COMMENT '最后更新身板信息',
  device_create string COMMENT '创建设备uuid',
  device_update string COMMENT '更新设备uuid',
  time_entry bigint comment "数据进入数仓日期"
) 
row format delimited
fields terminated by '\t'
lines terminated by '\n' 
stored as textfile;

```

## DWD

``` 
//dwd
drop table colourlife_user_dwd_user_agent_log;

create table if not exists colourlife_user_dwd_user_agent_log
(
   user_agent_log_sk bigint comment "代理键",
   user_id bigint COMMENT '用户id',
  time_create string COMMENT '创建时间',  
  ip_create bigint COMMENT '创建ip',
  agent_create string COMMENT '创建设备信息',
  time_update string COMMENT '更新时间',
  ip_update bigint COMMENT '最后更新ip',
  agent_update string  COMMENT '最后更新身板信息',
  device_create string COMMENT '创建设备uuid',
  device_update string COMMENT '更新设备uuid',
  time_entry string comment "数据进入数仓日期",
  version bigint comment "版本",
  expiry_date string comment "有效日期"
)
row format delimited
fields terminated by '\t'
lines terminated by '\n' 
stored as textfile;
```

## 第一次装载

``` 
use user_manager_platform_test;

TRUNCATE colourlife_user_dwd_user_agent_log;

insert into colourlife_user_dwd_user_agent_log
select 
   row_number() over (order by t1.user_id) + t2.sk_max,
   t1.user_id,
   from_unixtime(t1.time_create),
   t1.ip_create,
   t1.agent_create,
    from_unixtime(t1.time_update),
   t1.ip_update,
   t1.agent_update,
   t1.device_create,
   t1.device_update,
   from_unixtime(t1.time_entry,'yyyy-MM-dd'),
   1,
   '2200-01-01'
from colourlife_user_ods_user_agent_log t1
	cross join (select coalesce(max(user_agent_log_sk),0) sk_max from colourlife_user_dwd_user_agent_log) t2;
	
```

## 定期每天装载

``` 

```
