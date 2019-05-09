
# 数据库

org

# org 表

组织架构表

## ODS

```
drop table org_ods_org;

create table if not exists org_ods_org
(
  corp_id string COMMENT '租户id',
  org_uuid string COMMENT '主键uuid',
  code string,
  parent_id string COMMENT '父级id,顶级为0',
  dn string COMMENT '架构树地址',
  name string COMMENT '组织架构名称',
  province string COMMENT '省',
  city string COMMENT '市',
  region string COMMENT '区',
  longitude decimal(6,2)  COMMENT '经度',
  latitude decimal(6,2)  COMMENT '纬度',
  org_type string COMMENT '架构类型,未知,集团,大区,事业部,行政区,部门,小区',
  org_type_id string COMMENT '类型id',
  dr bigint COMMENT '删除标示,0正常,1删除',
  status bigint COMMENT '状态,0正常,1禁用',
  memo string COMMENT '备注',
  update_ts string COMMENT '时间戳,每次更新数据自动更新',
  create_ts string COMMENT '数据创建时间',
  family_type string COMMENT '族谱类型',
  family_type_id string COMMENT '族谱类型ID',
  sorted bigint COMMENT '排序,原表中字段名为sort',
  org_level string COMMENT '组织级别',
  time_entry string comment "数据进入数仓日期"
) 
row format delimited
fields terminated by '\t'
lines terminated by '\n' 
stored as textfile;

--原表
PRIMARY KEY (`org_uuid`),
KEY `dn` (`dn`(255)),
KEY `parent` (`parent_id`),
KEY `crop_id` (`corp_id`),
KEY `family_id` (`family_type_id`)

```

## 长沙市分局维度表

### 建表

```

drop table dim_changsha_branch;

create table if not exists dim_changsha_branch
(
  org_uuid string COMMENT '主键uuid',
  name string COMMENT '组织架构名称',
  dr bigint COMMENT '删除标示,0正常,1删除',
  status bigint COMMENT '状态,0正常,1禁用',
  memo string COMMENT '备注'
) 
row format delimited
fields terminated by '\t'
lines terminated by '\n' 
stored as textfile;

```

### 定时装载脚本

```

mysql> select * from org where parent_id = '9a06a339-87ef-4e4d-8a88-77e86808a95c';

TRUNCATE dim_changsha_branch;

insert into dim_changsha_branch
select
  org.org_uuid,
  org.name,
  org.dr,
  org.status,
  org.memo
from org_ods_org org
where org.parent_id = '9a06a339-87ef-4e4d-8a88-77e86808a95c'

```

## 长沙市三级机构维度表

### 建表

```

drop table dim_changsha_threelevel;

create table if not exists dim_changsha_threelevel
(
  org_uuid string COMMENT '主键uuid',
  parent_id string COMMENT '关联长沙市分局维度表',
  name string COMMENT '组织架构名称',
  dr bigint COMMENT '删除标示,0正常,1删除',
  status bigint COMMENT '状态,0正常,1禁用',
  memo string COMMENT '备注'
) 
row format delimited
fields terminated by '\t'
lines terminated by '\n' 
stored as textfile;
```

### 定时装载脚本

```
TRUNCATE dim_changsha_threelevel;

insert into dim_changsha_threelevel
select
  org.org_uuid,
  org.parent_id,
  org.name,
  org.dr,
  org.status,
  org.memo
from org_ods_org org
where org.parent_id in (
    select org_uuid from dim_changsha_branch 
)

```