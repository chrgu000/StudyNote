
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


# 彩生活对账平台





