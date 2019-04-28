
# 彩生活Git账户
http://code.ops.colourlife.com
账户名：dongkang
密码：dkdk1234

CaihuiBI仓库地址
http://xiening@code.ops.colourlife.com:10080/colourlifeCMDB/colourlife_caihui_bigdata_collection.git

# 法本通账号名密码
工号：13926
密码：身份证后六位


# 用户轨迹需求

## 推送消息格式

生产者开发人：徐良毅

``` 
消息格式
	{
		"message_id":"55016990-3d22-3c13-977a-a38fef3bdd54",    //唯一标识
		"czy_id":"4410797",                          //彩之云用户id
		"mobile":"13640035963",                       //用户手机号
		"terminal":"wap",                           //访问终端
		"os":"iPhone 12.1.4",                         //系统
		"channel":"彩之云",                          //来源渠道
		"create_time":1555418766,                      //访问时间
		"page":"订单列表"                           //访问页面
	}
	
```

## 建表语句

``` 
create table user_action_log (
  message_id string,
  czy_id string,
  mobile string,
  terminal string,
  os string,
  channel string,
  create_time string,
  page string
)
row format delimited
fields terminated by '\t'
lines terminated by '\n' 
stored as textfile;



```

## 查询语句

``` 
truncate trade_order;
truncate trade_refund_order;
truncate charge_order;
truncate charge_refund_order;
truncate user_action_log;


select * from trade_order;
select * from trade_refund_order;
select * from charge_order;
select * from charge_refund_order;
select * from user_action_log;
```

## 测试 Kafka地址

地址：193.112.86.246:9092

topic：caihui_bi_user_visit_log_test

## 正式 Kafka地址

地址：120.79.92.30:9092
订单的 topic：caihui_bi_order_pro
用户访问日志的 topic：caihui_bi_user_visit_log_pro

## 启动jar程序

nohup java -jar  -Xms500m -Xmn500m -Xmx2g /home/colourlife/jars/caihuibi-0.0.1-SNAPSHOT.jar

# 用户管理平台

## 采集办法

### user表

金融平台 集中管控平台 商户平台的区别？

金融平台 ：订单信息
集中管控平台：小区、住房这些
商户平台：饭票之类


## 首页

员工用户 业主用户 普通用户的区别？

业主：有缴费信息，有房产，
员工：
普通用户：

活跃用户数 ：什么样的算活跃用户？

GMV：的概念是什么？
消费数据

环比：周期是多少？



## 用户管理

有哪些标签？

源IP：210.75.13.8
目的IP：120.77.63.54
库名：colourlife_user_service
用户名：bigdata
密码：V6f1_s2N&70%Lv^>

user_manager_platform_test 测试
user_manager_platform_pro 正式

mysql -h120.77.63.54 -ubigdata -pV6f1_s2N&70%Lv^>

## 建表语句(ODS)

### User

``` 
drop table user_ods;
create table if not exists user_ods
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

**DWD层**

``` 
//dwd
create table if not exists user_dwd
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

**第一次装载**

``` 
TRUNCATE user_manager_platform_test.user_dwd;

insert into user_manager_platform_test.user_dwd
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
from user_manager_platform_test.user_ods t1
	cross join (select coalesce(max(user_sk),0) sk_max from user_manager_platform_test.user_dwd) t2;
	
```

### user_address

``` 
create table if not exists user_address_ods(
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

### user_info

``` 
create table if not exists user_info_ods(
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
		
### user_identity

用户认证信息表

``` 
create table if not exists user_identity_ods(
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

### user_community

用户地址表

``` 
create table if not exists user_community_ods(
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
			  
### user_finance_relation

用户金融平台关联表（沿用原表结构做优化）

``` 
create table if not exists user_finance_relation_ods(
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

### invite_log

邀请记录表（原表）

``` 
create table if not exists invite_log_ods(
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

### user_third_auth

第三方注册用户表（微信、QQ，原表）

``` 
create table if not exists user_third_auth_ods(
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

### user_agent_log用户注册设备信息表

用户注册设备信息表

一个用户一条信息，time_update表示最近一次登录时间。

``` 
drop table user_agent_log_ods;

create table if not exists user_agent_log_ods(
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

**DWD层**

``` 
//dwd
drop table user_agent_log_dwd;

create table if not exists user_agent_log_dwd
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

**第一次装载**

``` 

TRUNCATE user_manager_platform_test.user_agent_log_dwd;

insert into user_manager_platform_test.user_agent_log_dwd
select row_number() over (order by t1.user_id) + t2.sk_max,
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
from user_manager_platform_test.user_agent_log_ods t1
	cross join (select coalesce(max(user_agent_log_sk),0) sk_max from user_manager_platform_test.user_agent_log_dwd) t2;
	
```

**定期每天装载**

``` 

```

## 建表语句（DWS）

### date_dim

``` 
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

    insert into user_manager_platform_test.date_dim
   (sequence_id,dateStr,year,month,month_name,quarter,day)
   values
   (1,'2019-04-27','2019','04','四月','4','27')

```

### user_statistics_fact_dws

``` 
drop table user_statistics_fact_dws;
create table user_statistics_fact_dws
(
   register_acount bigint COMMENT '注册用户数',
   active_acount  bigint COMMENT '活跃用户数',
   sequence_id   bigint COMMENT '日期代理建'
)
row format delimited
fields terminated by '\t'
lines terminated by '\n' 
stored as textfile;
```

**初次装载**

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


数据源                         

1 2 3 
1 3 4

HIVE
1 1 2 3

1 1 3 3   //先处理SCD1
2 1  3 4




