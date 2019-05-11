
数据库：xcws_xlfk_service(巡逻防控数据)

# mysql数据库信息

1)、带月份的表可以不用管（之前做了分表，后面业务改动去掉了）；
2)、order 任务表；
3)、order_child 子任务表；
4)、order_record 任务接单表；

```
+-------------------------------+
| Tables_in_xcws_xlfk_service   |
+-------------------------------+
| appeal                        |
| entity                        |
| order                         |
| order_201901                  |
| order_201902                  |
| order_201903                  |
| order_201904                  |
| order_201905                  |
| order_child                   |
| order_child_201901            |
| order_child_201902            |
| order_child_201903            |
| order_child_201904            |
| order_child_201905            |
| order_record                  |
| order_record_201901           |
| order_record_201902           |
| order_record_201903           |
| order_record_201904           |
| order_record_201905           |
| order_record_operation_201901 |
| order_record_operation_201902 |
| order_record_operation_201903 |
| order_record_operation_201904 |
| order_record_operation_201905 |
| order_record_operation_201906 |
| point                         |
| privilege_user                |
| route                         |
| scheduling                    |
| scheduling_employee           |
| track_201901                  |
| track_201902                  |
| track_201903                  |
| track_201904                  |
| track_201905                  |
| track_201906                  |
| user_mileage                  |
+-------------------------------+
38 rows in set (0.24 sec)
```

mysql -h120.77.63.54 -ubigdata -pV6f1_s2N&70%Lv^>
use xcws_xlfk_service

# order