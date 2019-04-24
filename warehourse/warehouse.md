
**数据仓库**

# 销售订单数据仓库模型

## 在Hive中建立RDS库表

为了支持Hive建表时插入中文注释 需要在MySQL中做如下设置：

use hive;
//修改字段注释字符集
alter table COLUMNS_V2 modify column COMMENT varchar(256) character set utf8;
//修改表注释字符集
alter table TABLE_PARAMS modify column PARAM_VALUE varchar(4000) character set utf8;
//修改分区注释字符集
alter table PARTITION_KEYS modify column PKEY_COMMENT varchar(4000) character set utf8;

### 建立客户过渡表

``` 
create table customer_ods (
	customer_number int comment '客户编号',
	customer_name string comment '客户名称',
	customer_street_address string comment '街道',
	customer_zip_code int comment '邮编',
	customer_city string comment '市',
	customer_state string comment '省'
);
```

### 建立产品过渡表

``` 
create table product_ods (
	product_code int comment '产品编号',
	product_name string comment '产品名称',
	product_category string comment '产品分类'
);
```

###  建立销售订单过渡表

``` 
create table sales_order (
	order_number int comment '订单号',
	customer_number int comment '客户号',
	product_code int comment '产品号',
	order_date timestamp comment '下单时间',
	entry_date timestamp comment '登记时间',
	order_amount decimal(10 , 2 ) comment '订单金额'
);
```

## 在Hive中建立TDS库表

### 建立日期维度表

``` 
create table date_dim (
	date_sk int comment 'surrogate key',
	datestr string comment 'date,yyyy-mm-dd',
	month int comment '月',
	month_name string comment '月名称',
	quarter int comment '季',
	year int comment '年'
)
row format delimited fields terminated by ','
stored as textfile;

```

### 建立客户维度表

``` 
create table
customer_dim (
customer_sk int comment
'surrogate key',
customer_number int comment
'number',
customer_name varchar
(50) comment
'name',
customer_street_address varchar
(50) comment
'address',
customer_zip_code int comment
'zipcode',
customer_city varchar
(30) comment
'city',
customer_state varchar
(2) comment
'state',
version int comment
'version',
effective_date date comment
'effective date',
expiry_date date comment
'expiry date'
)
clustered by
(customer_sk) into
8 buckets
stored as
orc tblproperties ('transactional'='true');
```