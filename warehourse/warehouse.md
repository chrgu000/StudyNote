
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

insert into table customer_ods(customer_number,customer_name,customer_street_address,customer_zip_code,customer_city,customer_state)
                                 values(1,'zhangsan','羊台三街',743000,'深圳','广东');
insert into table customer_ods(customer_number,customer_name,customer_street_address,customer_zip_code,customer_city,customer_state)
                                 values(2,'lisi','友谊街',443000,'甘肃','定西');
insert into table customer_ods(customer_number,customer_name,customer_street_address,customer_zip_code,customer_city,customer_state)
                                 values(3,'wangwu','张自忠街',643000,'山西','太原');							 
								 
```

### 建立产品过渡表

``` 
create table product_ods (
	product_code int comment '产品编号',
	product_name string comment '产品名称',
	product_category string comment '产品分类'
);

insert into table product_ods(product_code,product_name,product_category)
values(1,'方便面','食品');
insert into table product_ods(product_code,product_name,product_category)
values(2,'石油','油类');
insert into table product_ods(product_code,product_name,product_category)
values(3,'汽油','油类');
```

###  建立销售订单过渡表

``` 
drop table sales_order;
create table sales_order (
	order_number int comment '订单号',
	customer_number int comment '客户号',
	product_code int comment '产品号',
	order_date string comment '下单时间',
	entry_date string comment '登记时间',
	order_amount decimal(10 , 2 ) comment '订单金额'
);
insert into table sales_order(order_number,customer_number,product_code,order_date,entry_date,order_amount)
values(1,1,1,'2019-04-26','2019-04-26',12.34);
insert into table sales_order(order_number,customer_number,product_code,order_date,entry_date,order_amount)
values(2,2,1,'2019-04-26','2019-03-26',13.34);
insert into table sales_order(order_number,customer_number,product_code,order_date,entry_date,order_amount)
values(3,1,2,'2019-04-26','2019-04-25',14.34);
insert into table sales_order(order_number,customer_number,product_code,order_date,entry_date,order_amount)
values(4,3,1,'2019-04-26','2019-03-21',15.34);
insert into table sales_order(order_number,customer_number,product_code,order_date,entry_date,order_amount)
values(5,2,3,'2019-04-26','2019-03-24',16.34);
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
create table customer_dim (
	customer_sk bigint comment 'surrogate key',
	customer_number bigint comment 'number',
	customer_name string comment 'name',
	customer_street_address string comment 'address',
	customer_zip_code bigint comment 'zipcode',
	customer_city string comment 'city',
	customer_state string comment 'state',
	version bigint comment 'version',
	effective_date string comment 'effective date',
	expiry_date string comment 'expiry date'
)
```

### 建立产品维度表

``` 
create table product_dim (
	product_sk int comment 'surrogate key',
	product_code int comment 'code',
	product_name string comment 'name',
	product_category string comment 'category',
	version int comment 'version',
	effective_date string comment 'effective date',
	expiry_date string comment 'expiry date'
)
```

### 建立订单维度表

``` 
create table order_dim (
	order_sk int comment 'surrogate key',
	order_number int comment 'number',
	version int comment 'version',
	effective_date string comment 'effective date',
	expiry_date string comment 'expiry date'
)
```

### 销售订单事实表

``` 
create table sales_order_fact (
	order_sk int comment 'order surrogate key',
	customer_sk int comment 'customer surrogate key',
	product_sk int comment 'product surrogate key',
	order_date_sk int comment 'date surrogate key',
	order_amount decimal(10 , 2 ) comment 'order amount'
)
```

## date_dim_generate.sh 预装载日期维度

``` 
#!/bin/bash
date1="$1"
date2="$2"
tempdate=`date -d "$date1" +%F`
tempdateSec=`date -d "$date1" +%s`
enddateSec=`date -d "$date2" +%s`
min=1
max=`expr \( $enddateSec - $tempdateSec \) / \( 24 \* 60 \* 60 \) + 1`
cat /dev/null > ./date_dim.csv
while [ $min -le $max ]
do
	month=`date -d "$tempdate" +%m`
	month_name=`date -d "$tempdate" +%B`
	quarter=`echo $month | awk '{print int(($0-1)/3)+1}'`
	year=`date -d "$tempdate" +%Y`
	echo ${min}","${tempdate}","${month}","${month_name}","${quarter}","${year} >> ./date_dim.csv
	tempdate=`date -d "+$min day $date1" +%F`
	tempdateSec=`date -d "+$min day $date1" +%s`
	min=`expr $min + 1`
done
hdfs dfs -put -f date_dim.csv /user/hive/warehouse/mydb.db/date_dim/
```

//执行预加载命令
[root@cdh1 shell]# ./date_dim_generate.sh 2000-01-01 2020-12-31