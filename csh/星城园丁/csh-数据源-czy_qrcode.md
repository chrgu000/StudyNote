
# czy_code(记录二维码编码与派出所的映射关系)

1)、application_id=21 表示派出所

## ODS

```
drop table czy_qrcode_ods_czy_code;

create table if not exists czy_qrcode_ods_czy_code
(
  id bigint COMMENT 'id',
  name string COMMENT '二维码名称',
  code string COMMENT '二维码编码',
  application_id bigint COMMENT '类别',
  community_uuid string,
  build string COMMENT '楼栋',
  unit string COMMENT '单元号',
  room string COMMENT '房间号',
  type bigint COMMENT '0:普通码，1：定制唯一码',
  path string COMMENT '二维码路径',
  status bigint COMMENT '0：未激活，1：已激活，3：已绑定',
  activation string COMMENT '激活者',
  activation_oa string COMMENT '激活者oa',
  community_name string COMMENT '小区名称',
  build_name string COMMENT '楼栋名称',
  unit_name string COMMENT '单元名称',
  update_at bigint COMMENT '更新时间',
  create_at bigint COMMENT '创建时间',
  address string COMMENT '房屋详细地址',
  room_uuid string COMMENT '房屋uuid',
  code_type bigint COMMENT '二维码类型,0:未知,1:商户二维码,2:业主二维码,3:小区公共区域',
  scan_times bigint COMMENT '扫码次数',
  time_entry string comment "数据进入数仓日期"
) 
row format delimited
fields terminated by '\t'
lines terminated by '\n' 
stored as textfile;

--原表
 PRIMARY KEY (`id`)

```