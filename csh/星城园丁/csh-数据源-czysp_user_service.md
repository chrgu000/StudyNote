
# qrcode_statistical表

czysp_user_service.qrcode_statistical表

记录派出所对应二维码的下载和注册数

## ODS

```
drop table czysp_user_ods_qrcode_statistical;

create table if not exists czysp_user_ods_qrcode_statistical
(
    id bigint,
    source_uuid string COMMENT '二维码uuid',
    register_num bigint,
    target_num bigint COMMENT '目标任务书，默认为100',
    download_num bigint COMMENT '下载次数',
    descr string COMMENT '备注，此字段在原表中是desc',
    create_at bigint COMMENT '创建时间',
    update_at bigint,
    time_entry string comment "数据进入数仓日期"
) 
row format delimited
fields terminated by '\t'
lines terminated by '\n' 
stored as textfile;

--原表
PRIMARY KEY (`id`),
UNIQUE KEY `uuid` (`source_uuid`) USING BTREE
```

# 

## ODS

```

```