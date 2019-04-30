豆沙绿数值：#C7EDCC

vscode 设置背景色
C:\software\vscode\Microsoft VS Code Insiders\resources\app\extensions\theme-quietlight\themes\quietlight-color-theme.json

设置此项的值为豆沙绿："editor.background": "#C7EDCC",


# 命名标准

源数据库标识_仓库层级_原数据库表名

# 数据类型选择标准

## 时间
ODS 层: 时间使用biglog(因为原数据库都是这样的)；                                             其它层: 时间都使用string;

## 整型数字
统一使用bigint

## 浮点型数字
统一使用decimal

# Hive 库名称

user_manager_platform_test 测试
user_manager_platform_pro 正式

# mysql 数据源

## 用户管理数据库

数据库名：colourlife_user_service

mysql -h120.77.63.54 -ubigdata -pV6f1_s2N&70%Lv^>


## 家访管理服务数据库

源IP：210.75.13.8（210.75.8.34）
目的IP：120.77.63.54
库名：colourlife_visit_service（家访管理服务）
用户名：bigdata
密码：V6f1_s2N&70%Lv^>

mysql -h120.77.63.54 -ubigdata -pV6f1_s2N&70%Lv^>

## 彩生活商户对帐平台数据库

源IP：210.75.13.8（210.75.8.34）
目的IP：120.77.63.54
库名：czy_business_platform（彩生活商户对帐平台）
用户名：bigdata
密码：V6f1_s2N&70%Lv^>

mysql -h120.77.63.54 -ubigdata -pV6f1_s2N&70%Lv^>


## 档案系统数据库

源IP：210.75.13.8（210.75.8.34）
目的IP：119.29.226.227
库名：cl_hr（档案系统）
用户名：bigdata
密码：V6f1_s2N&70%Lv^>

mysql -h119.29.226.227 -ubigdata -pV6f1_s2N&70%Lv^>