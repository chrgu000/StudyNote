
mysql -h120.77.63.54 -ubigdata -pV6f1_s2N&70%Lv^>

use czy_single_service

# xcws_login_log(彩之云登录日志表)

1）、update_at应该是一个没有任何作用的字段；
2）、在mysql库中按月分表；
3）、记录用户的登录日志信息；
4）、每登录一次就会增加一条记录；

```
drop table czy_single_ods_xcws_login_log;

create table if not exists czy_single_ods_xcws_login_log
(
  id bigint COMMENT '自增长id',
  account string COMMENT '账号',
  type bigint COMMENT '类型，1：登录，2：退出',
  ip string COMMENT 'ip',
  device_code string COMMENT '设备唯一码',
  device_type bigint COMMENT '设备类型，1：安卓，2：苹果，3：其他',
  device_name string COMMENT '设备名称（苹果11）',
  device_info string COMMENT '设备详情（json）',
  version string COMMENT '版本号',
  login_type bigint COMMENT '登录方式，1：静默，2：密码，3：手势， 4：微信，5：QQ',
  update_at bigint,
  community_uuid string COMMENT '小区uuid',
  community_name string COMMENT '小区名称',
  param string COMMENT '预留',
  time_entry string comment "数据进入数仓日期"
)
row format delimited
fields terminated by '\t'
lines terminated by '\n' 
stored as textfile;

--原表
PRIMARY KEY (`id`),
KEY `account` (`account`) USING BTREE,
KEY `device_code` (`device_code`) USING BTREE,
KEY `update_at` (`update_at`) USING BTREE,
KEY `community` (`community_uuid`) USING BTREE,
KEY `param` (`param`)

```
