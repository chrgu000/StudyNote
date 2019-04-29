
豆沙绿数值：#C7EDCC

vscode 设置背景色
C:\software\vscode\Microsoft VS Code Insiders\resources\app\extensions\theme-quietlight\themes\quietlight-color-theme.json

设置此项的值为豆沙绿："editor.background": "#C7EDCC",


# 命名标准

源数据库标识_仓库层级_原数据库表名

# 数据类型选择标准

## 时间
ODS 层: 时间使用biglog(因为原数据库都是这样的)；                                                                       其它层: 时间都使用string;

## 整型数字
统一使用bigint

## 浮点型数字
统一使用decimal

# mysql 数据源

## 用户管理数据库

数据库名：colourlife_user_service

mysql -h120.77.63.54 -ubigdata -pV6f1_s2N&70%Lv^>


## 家访管理服务数据库

源IP：210.75.13.8（210.75.8.34）
目的IP：120.77.63.54
库名：colourlife_visit_service（家访管理服务）
用户名：bigdata
密码：V6f1_s2N&70%Lv^>

## 彩生活商户对帐平台数据库

源IP：210.75.13.8（210.75.8.34）
目的IP：120.77.63.54
库名：czy_business_platform（彩生活商户对帐平台）
用户名：bigdata
密码：V6f1_s2N&70%Lv^>

mysql -h120.77.63.54 -ubigdata -pV6f1_s2N&70%Lv^>


## 档案系统数据库

源IP：210.75.13.8（210.75.8.34）
目的IP：119.29.226.227
库名：cl_hr（档案系统）
用户名：bigdata
密码：V6f1_s2N&70%Lv^>

mysql -h119.29.226.227 -ubigdata -pV6f1_s2N&70%Lv^>

# 彩生活商户对帐平台数据(czy_business_platform)

## transaction(订单交易流水表)

### ODS 

```

-- 这张表不会有更新
-- 可以根据time_create去采集
drop table czy_business_ods_transaction;

-- colour_sn: 是我们这边的订单(我们根据这个字段做统计)
-- out_trade_no: 是业务系统订单号

create table if not exists czy_business_ods_transaction(
   id bigint,
   application_id string comment "彩之云应用id",
   colour_sn string comment "彩之云预支付订单号",
   out_trade_no string comment "商户订单号",
   meal_total_fee decimal(10,2) comment "饭票支付金额",
   total_fee decimal(10,2) comment "订单金额，单位分，无小数",
   open_id string comment "应用用户标识",
   czy_id bigint comment "彩之云用户id", --对应user_id
   fee_type string comment "标记币种",
   trade_type string comment "交易类型",
   limit_payment string comment "指定支付方式",
   time_start bigint comment "交易起始时间",
   time_expire bigint comment "交易结束时间",
   body string comment "商品描述",
   attach string,
   detail string comment "商品详情",
   device_info string comment "设备号",
   spbill_create_ip string comment "终端ip",
   notify_url string comment "通知地址",
   code_url string comment "支付二维码",
   time_create bigint comment "订单创建时间",
   address string comment "场景信息--门店详细地址",
   community_uuid string comment "场景信息--小区uuid",
   community_name string comment "场景信息--小区名称",
   shop_id string comment "场景信息--门店id",
   shop_name string comment "场景信息--门店名称",
   course_code string comment "场景信息--科目编码",
   time_entry bigint comment "数据进入数仓日期"
) 
row format delimited
fields terminated by '\t'
lines terminated by '\n' 
stored as textfile;
```

### DWD

```
drop table czy_business_dwd_transaction;

create table if not exists czy_business_dwd_transaction(
   transaction_sk bigint comment "代理键",
   id bigint,
   application_id string comment "彩之云应用id",
   colour_sn string comment "彩之云预支付订单号",
   out_trade_no string comment "商户订单号",
   meal_total_fee decimal(10,2) comment "饭票支付金额",
   total_fee decimal(10,2) comment "订单金额，单位分，无小数",
   open_id string comment "应用用户标识",
   czy_id bigint comment "彩之云用户id", --对应user_id
   fee_type string comment "标记币种",
   trade_type string comment "交易类型",
   limit_payment string comment "指定支付方式",
   time_start string comment "交易起始时间",
   time_expire string comment "交易结束时间",
   body string comment "商品描述",
   attach string,
   detail string comment "商品详情",
   device_info string comment "设备号",
   spbill_create_ip string comment "终端ip",
   notify_url string comment "通知地址",
   code_url string comment "支付二维码",
   time_create string comment "订单创建时间",
   address string comment "场景信息--门店详细地址",
   community_uuid string comment "场景信息--小区uuid",
   community_name string comment "场景信息--小区名称",
   shop_id string comment "场景信息--门店id",
   shop_name string comment "场景信息--门店名称",
   time_entry string comment "数据进入数仓日期",
   version bigint comment "版本"
)
row format delimited
fields terminated by '\t'
lines terminated by '\n' 
stored as textfile;
```

### 第一次装载SQL

```
TRUNCATE user_manager_platform_test.czy_business_dwd_transaction;

insert into user_manager_platform_test.czy_business_dwd_transaction
select 
   row_number() over (order by t1.id) + t2.sk_max,
   t1.id,
   t1.application_id,
   t1.colour_sn,
   t1.out_trade_no,
   t1.meal_total_fee,
   t1.total_fee,
   t1.open_id,
   t1.czy_id, --对应user_id
   t1.fee_type,
   t1.trade_type,
   t1.limit_payment,
   from_unixtime(t1.time_start),
   from_unixtime(t1.time_expire),
   t1.body,
   t1.attach,
   t1.detail,
   t1.device_info,
   t1.spbill_create_ip,
   t1.notify_url,
   t1.code_url,
   from_unixtime(t1.time_create),
   t1.address,
   t1.community_uuid,
   t1.community_name,
   t1.shop_id,
   t1.shop_name,
   from_unixtime(t1.time_entry,'yyyy-MM-dd'),
   1
from user_manager_platform_test.czy_business_ods_transaction t1
	cross join (select coalesce(max(transaction_sk),0) sk_max from 
               user_manager_platform_test.czy_business_dwd_transaction) t2;
```

### 定期每天装载SQL

```
--这张表不会有更新,所以我们直接装载就可以
insert into user_manager_platform_test.czy_business_dwd_transaction
select
   row_number() over (order by t1.id) + t2.sk_max,
   t1.id,
   t1.application_id,
   t1.colour_sn,
   t1.out_trade_no,
   t1.meal_total_fee,
   t1.total_fee,
   t1.open_id,
   t1.czy_id, --对应user_id
   t1.fee_type,
   t1.trade_type,
   t1.limit_payment,
   from_unixtime(t1.time_start),
   from_unixtime(t1.time_expire),
   t1.body,
   t1.attach,
   t1.detail,
   t1.device_info,
   t1.spbill_create_ip,
   t1.notify_url,
   t1.code_url,
   from_unixtime(t1.time_create),
   t1.address,
   t1.community_uuid,
   t1.community_name,
   t1.shop_id,
   t1.shop_name,
   from_unixtime(t1.time_entry,'yyyy-MM-dd'),
   1
from   
(select 
  *
from user_manager_platform_test.czy_business_ods_transaction
where 
--时间是当前时间的上一天
from_unixtime(time_create,'yyyy-MM-dd') = from_unixtime(unix_timestamp(days_add(now(),-1)),'yyyy-MM-dd')
) t1
cross join (select coalesce(max(transaction_sk),0) sk_max from 
            user_manager_platform_test.czy_business_dwd_transaction) t2;

```

## transaction_child(交易流水子表)

### ODS

```

-- 这张表这主表transaction是一一对应的关系
-- 这张表示存在变更情况的
-- 可以根据time_update去增量采集
drop table czy_business_ods_transaction_child;

-- 统计时间：time_pay
-- 统计金额：real_total_fee
create table if not exists czy_business_ods_transaction_child(
   colour_sn string COMMENT '彩之云预支付订单号',
   colour_trade_no string  COMMENT '彩之云交易订单号',
   trade_state bigint COMMENT '1--未支付；2--已付款；3--交易成功；4--已关闭；5--已撤销；6--用户支付中；7--转入退款；8--支付失败（其他原因，如彩之云返回失败）',
   payment_uuid string COMMENT '支付方式uuid',
   discount bigint COMMENT '折扣率',
   notify_msg string COMMENT '通知返回信息',
   notify_num bigint COMMENT '通知业务系统次数',
   time_pay bigint COMMENT '支付时间',
   callback_results string COMMENT '支付机构回调结果',
   actual_pay_amount decimal(10,2) COMMENT '最终支付支付金额，包含支付机构优惠',
   time_update bigint,
   real_total_fee decimal(10,2) COMMENT '订单支付金额',
   business_uuid string COMMENT '彩之云商户id',
   mobile string COMMENT '用户手机号码',
   split_state bigint COMMENT '分账状态，默认0,；0--无需分账，1--未分账通知或通知失败，2--通知分账成功',
   arrival_fee decimal(10,6) COMMENT '到账商户金额',
   service_fee decimal(10,6) COMMENT '服务费金额',
   is_deleted bigint COMMENT '1：正常，2：已删除',
   time_entry bigint comment "数据进入数仓日期"
) 
row format delimited
fields terminated by '\t'
lines terminated by '\n' 
stored as textfile;
```

### DWD

```
drop table czy_business_dwd_transaction_child;

create table if not exists czy_business_dwd_transaction_child(
   transaction_child_sk bigint comment "代理键",
   colour_sn string COMMENT '彩之云预支付订单号',
   colour_trade_no string  COMMENT '彩之云交易订单号',
   trade_state bigint COMMENT '1--未支付；2--已付款；3--交易成功；4--已关闭；5--已撤销；6--用户支付中；7--转入退款；8--支付失败（其他原因，如彩之云返回失败）',
   payment_uuid string COMMENT '支付方式uuid',
   discount bigint COMMENT '折扣率',
   notify_msg string COMMENT '通知返回信息',
   notify_num bigint COMMENT '通知业务系统次数',
   time_pay string COMMENT '支付时间',
   callback_results string COMMENT '支付机构回调结果',
   actual_pay_amount decimal(10,2) COMMENT '最终支付支付金额，包含支付机构优惠',
   time_update string,
   real_total_fee decimal(10,2) COMMENT '订单支付金额',
   business_uuid string COMMENT '彩之云商户id',
   mobile string COMMENT '用户手机号码',
   split_state bigint COMMENT '分账状态，默认0,；0--无需分账，1--未分账通知或通知失败，2--通知分账成功',
   arrival_fee decimal(10,6) COMMENT '到账商户金额',
   service_fee decimal(10,6) COMMENT '服务费金额',
   is_deleted bigint COMMENT '1：正常，2：已删除',
   time_entry string comment "数据进入数仓日期",
   version bigint comment "版本"
)
row format delimited
fields terminated by '\t'
lines terminated by '\n' 
stored as textfile;
```

### 第一次装载SQL

```
TRUNCATE user_manager_platform_test.czy_business_dwd_transaction_child;

insert into user_manager_platform_test.czy_business_dwd_transaction_child
select 
   row_number() over (order by t1.colour_sn) + t2.sk_max,
   t1.colour_sn,
   t1.colour_trade_no,
   t1.trade_state,
   t1.payment_uuid,
   t1.discount,
   t1.notify_msg,
   t1.notify_num,
   from_unixtime(t1.time_pay),
   t1.callback_results,
   t1.actual_pay_amount,
   from_unixtime(t1.time_update),
   t1.real_total_fee,
   t1.business_uuid,
   t1.mobile,
   t1.split_state,
   t1.arrival_fee,
   t1.service_fee,
   t1.is_deleted,
   from_unixtime(t1.time_entry,'yyyy-MM-dd'),
   1
from user_manager_platform_test.czy_business_ods_transaction_child t1
	cross join (select coalesce(max(transaction_child_sk),0) sk_max from 
               user_manager_platform_test.czy_business_dwd_transaction_child) t2;
```

### 定期每天装载SQL

```


```


