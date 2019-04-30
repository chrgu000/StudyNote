
彩生活档案系统数据(cl_hr)

# clhr_archives_jobapplication(创建人事档案职位申请表)

## ODS

-- 这张表每天采集一次
-- 采集方法是全量采集

```
drop table cl_hr_ods_clhr_archives_jobapplication;

create table if not exists cl_hr_ods_clhr_archives_jobapplication(
  JobsId  bigint  COMMENT '编号',
  Archid bigint  COMMENT '外键',
  CnName string  COMMENT '中文名字',
  EnName string COMMENT '英文名字',
  Birthday string  COMMENT '出生日期',
  BloodType string  COMMENT '血型',
  Constellation string  COMMENT '星座',
  DocumentsType string  COMMENT '持证件种类',
  Card string  COMMENT '身份证号码',
  NowContact string  COMMENT '本市联系方式',
  OldContact string  COMMENT '原籍联系方式',
  Skills string  COMMENT '个人专长',
  Tel string  COMMENT '本人电话号码',
  Sex string  COMMENT '性别,0代表男,1代表女',
  Native string  COMMENT '籍贯',
  CensusRegister string  COMMENT '户籍',
  Weight bigint  COMMENT '体重',
  Height bigint  COMMENT '身高',
  Languages string  COMMENT '通晓语言',
  Marriage string  COMMENT '婚姻状况,0代表未婚,1代表已婚,2代表离婚',
  Certificates string  COMMENT '持何职称证书',
  Certificatesname string  COMMENT '职称名称',
  Interest string  COMMENT '兴趣爱好',
  Email string  COMMENT '邮件地址',
  scNum string  COMMENT '社保号',
  scType string  COMMENT '社保种类',
  MiType string  COMMENT '医疗保险种类',
  BXGongS string ,
  HousingFund bigint COMMENT '住房公积金，0表示无，1表示有',
  HousingFundNum decimal(18,2)  COMMENT '住房公积金金额',
  HousingFundAccount string COMMENT '住房公积金个人帐号',
  Grade bigint  COMMENT '员工星级',
  PicUrl string  COMMENT '图像图片地址',
  LocalCall string  COMMENT '本市电话',
  OldTel string  COMMENT '原籍电话',
  BankCity string  COMMENT '银行所在城市',
  BankCityCode string  COMMENT '银行所在城市编号',
  BankCode string  COMMENT '银行编号',
  BankLinenum string  COMMENT '银行行别号',
  BankSuName string  COMMENT '支行名称',
  BankUserName string  COMMENT '开户名称',
  BankName string  COMMENT '银行名称',
  BankNo string  COMMENT '银行帐号',
  JOATel string ,
  Cornet string,
  CompaynCornet string,
  CreditCardNo string,
  contractType bigint  COMMENT '合同类别',
  startcontracttime string  COMMENT '合同开始时间',
  endcontracttime string  COMMENT '合同结果时间',
  isbanktransfer bigint  COMMENT '是否通过银行进行转帐,1员工工资将通过银行自动转帐',
  allowShengrialter string ,
  yyxType bigint COMMENT '是否购买意外险，1有0没有',
  YYXGongS string  COMMENT '意外险购买公司',
  YYXGouMaide string  COMMENT '购买地',
  YYXNote string  COMMENT '未购买意外险的原因说明',
  archcode string  COMMENT '档案保存编号',
  archsaveaddress string  COMMENT '档案保存地',
  address string  COMMENT '常住地址',
  lovefee decimal(11,2) ,
  baoxianfee decimal(11,2) ,
  gongjijinfee decimal(11,2) 
) 
row format delimited
fields terminated by '\t'
lines terminated by '\n' 
stored as textfile;
```

