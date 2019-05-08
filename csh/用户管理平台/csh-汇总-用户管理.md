
# 管理/用户管理/用户详情

## 用户信息

### 需要提供的信息 

{
手机号 : "18000000000",
用户类型 : ["员工","业主"],
昵称 : "Windir",
名字 : "Windir",
小区 : "广东省-深圳市-龙华区-彩悦大厦 2单元B栋302",
注册时间 : "2018-04-27 10:00:00",
小区uuid : "13454",
注册类型 : "APP注册-ios",
绑定客户经理 : "张三 15200000001",
标签 : ["活跃","鲸鱼用户","预流失"]
}

### 数据来源

{
    手机号 : colourlife_user_dwd_user.mobile,
    用户类型 : ??,
    昵称 : colourlife_user_dwd_user_info.nick_name,
    名字 : colourlife_user_dwd_user_info.name,
    小区 : colourlife_user_dwd_user_community.community_name,
    注册时间 : colourlife_user_dwd_user.time_create,
    小区uuid : colourlife_user_dwd_user_community.community_uuid,
    注册类型 : colourlife_user_dwd_user_info.register_type,
    绑定客户经理 : colourlife_operation_dwd_user_manager_build.user_id,
    标签 : ??,
}

**绑定客户经理说明**
```
colourlife_user_ods_user
colourlife_user_ods_user_community              //user_id(用户id)
colourlife_operation_ods_user_manager_build     //community_uuid(小区uuid)

```

### SQL

```

```


### 返回样例

```
{
    mobilePhone 手机号 : colourlife_user_dwd_user.mobile,
    userType 用户类型 : ??,
    nickName 昵称 : colourlife_user_dwd_user_info.nick_name,
    name 名字 : colourlife_user_dwd_user_info.name,
    community 小区 : colourlife_user_dwd_user_community.community_name,
    registeredTime 注册时间 : colourlife_user_dwd_user.time_create,
    communityUuid 小区uuid : colourlife_user_dwd_user_community.community_uuid,
    registeredType 注册类型 : colourlife_user_dwd_user_info.register_type,
    bindManager 绑定客户经理 : colourlife_operation_dwd_user_manager_build.user_id,
    label 标签 : ??,
}
```

## 统计信息


