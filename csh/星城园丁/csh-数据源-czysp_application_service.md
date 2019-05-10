mysql -h120.77.63.54 -ubigdata -pV6f1_s2N&70%Lv^>

use czysp_application_service

# application

1）、在这里每个应用对应一条记录，如巡逻防控、积分商场分别对应一个应用；
2）、table_pre表示字表的前缀；

## ODS

```
drop table czysp_application_ods_application;

create table if not exists czysp_application_ods_application
(
  id string COMMENT 'application_id',
  name string COMMENT '应用名称',
  secret string COMMENT 'secret',
  redirect_uri string COMMENT '回调地址',
  state bigint COMMENT '0：审核中 ，1：正常，2：禁用',
  logo string COMMENT '应用logo',
  mobile string COMMENT '应用联系人',
  email string COMMENT '邮箱',
  oauth_type bigint COMMENT '授权级别，1：code，2：access_token，3：codeaccess_toke都可以',
  create_at bigint COMMENT '创建时间',
  update_at bigint COMMENT '更新时间',
  salt string COMMENT '预留',
  password string COMMENT '密码，预留',
  czy_id string COMMENT '预留',
  andriod_package string COMMENT '安卓包名',
  ios_package string COMMENT '苹果包名',
  table_pre string COMMENT '表前缀',
  public_user_id bigint COMMENT '是否返回用户ID。默认0不返回，1返回',
  company string,
  public_mobile bigint COMMENT '是否展示手机号码',
  time_entry string comment "数据进入数仓日期"
) 
row format delimited
fields terminated by '\t'
lines terminated by '\n' 
stored as textfile;

--原表
PRIMARY KEY (`id`)

```

# application_access_token

show create table application_access_token;

## ODS

```
| application_access_token | CREATE TABLE `application_access_token` (
  `access_token` char(32) NOT NULL COMMENT '授权token',
  `user_uuid` char(36) CHARACTER SET utf8mb4 NOT NULL COMMENT '用户uuid',
  `user_id` int(11) NOT NULL COMMENT '用户编号',
  `application_id` varchar(100) NOT NULL COMMENT '应用id',
  `name` varchar(100) DEFAULT NULL COMMENT '名称，预留',
  `scopes` text COMMENT '授权作用域',
  `revoked` tinyint(2) NOT NULL DEFAULT '0' COMMENT '是否取消',
  `code` varchar(150) DEFAULT NULL COMMENT '授权code码',
  `create_at` int(11) NOT NULL COMMENT '创建时间',
  `update_at` int(11) NOT NULL DEFAULT '0' COMMENT '更新时间',
  `expires_at` int(11) NOT NULL COMMENT '失效时间',
  `version` varchar(50) CHARACTER SET utf8mb4 NOT NULL DEFAULT '0' COMMENT '客户端版本号',
  `native_type` tinyint(2) NOT NULL DEFAULT '0' COMMENT '客户端标识，1：安卓，2：苹果	',
  `device_uuid` varchar(100) CHARACTER SET utf8mb4 NOT NULL DEFAULT '0' COMMENT '设备uuid',
  PRIMARY KEY (`access_token`),
  KEY `user` (`user_id`) USING BTREE,
  KEY `code-application` (`code`,`application_id`) USING BTREE
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COMMENT='应用授权access_token表' |

```

# application_open

show create table application_open;

每个访问过的用户都会在这里有个记录; 每个用户一条记录。

## ODS

```
drop table czysp_application_ods_application_open;

create table if not exists czysp_application_ods_application_open
(
  user_uuid string COMMENT '用户uuid',
  user_id bigint,
  salt string COMMENT '盐',
  openid string COMMENT 'openid',
  update_at bigint COMMENT '最后更新时间',
  mobile string COMMENT '手机号码',
  table_pre string COMMENT '表前缀',
  time_entry string comment "数据进入数仓日期"
)
row format delimited
fields terminated by '\t'
lines terminated by '\n' 
stored as textfile;

--原表
PRIMARY KEY (`user_id`),
UNIQUE KEY `openid` (`openid`) USING HASH

```

