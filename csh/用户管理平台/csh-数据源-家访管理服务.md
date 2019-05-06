
家访管理服务数据库(colourlife_visit_service)

# comment(家访评论表)

这张表不会有更新

## ODS

```
drop table colourlife_visit_ods_comment;

create table if not exists colourlife_visit_ods_comment(
  id bigint COMMENT '自增长id',
  time_create bigint  COMMENT '评论时间',
  level_one bigint  COMMENT '评分等级，不满意',
  level_two bigint  COMMENT '一般',
  level_three bigint  COMMENT '-满意',
  level_four bigint  COMMENT '非常满意',
  level_five bigint  COMMENT '预留',
  content string COMMENT '评论内容',
  oa_username string COMMENT '客户经理OA',
  user_id bigint COMMENT '彩之云账号id',
  community_uuid string COMMENT '小区uuid',
  evaluate_type string COMMENT '评价类型',
  address bigint  COMMENT '业主地址',
  mark bigint COMMENT '是否标记1是0否',
  community_id bigint  COMMENT '小区id',
  device_code string  COMMENT '设备唯一码',
  time_entry bigint comment "数据进入数仓日期"
)
row format delimited
fields terminated by '\t'
lines terminated by '\n' 
stored as textfile;

-- 原表主键信息
PRIMARY KEY (`id`),
KEY `manager` (`oa_username`) USING BTREE,
KEY `user` (`user_id`) USING BTREE,
KEY `community` (`community_uuid`) USING BTREE,
KEY `time_create` (`time_create`) USING BTREE,
KEY `device_code` (`device_code`) USING BTREE

```