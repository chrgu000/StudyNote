**用户行为分析平台**

# 数据库表

## user_info表 (hive)

user_info表，实际上，就是一张最普通的用户基础信息表；

``` 
user_id：其实就是每一个用户的唯一标识，通常是自增长的Long类型，BigInt类型
username：是每个用户的登录名
name：每个用户自己的昵称、或者是真实姓名
age：用户的年龄
professional：用户的职业
city：用户所在的城市
sex: 性别

```

## user_visit_action表 (hive)

user_visit_action表，其实就是放，比如说网站，或者是app，每天的点击流的数据。可以理解为，用户对网站/app每点击一下，就会代表在这个表里面的一条数据。

``` 
date：日期，代表这个用户点击行为是在哪一天发生的
user_id：代表这个点击行为是哪一个用户执行的
session_id ：唯一标识了某个用户的一个访问session
page_id ：点击了某些商品/品类，也可能是搜索了某个关键词，然后进入了某个页面，页面的id
action_time ：这个点击行为发生的时间点
search_keyword ：如果用户执行的是一个搜索行为，比如说在网站/app中，搜索了某个关键词，然后会跳转到商品列表页面；搜索的关键词
click_category_id ：可能是在网站首页，点击了某个品类（美食、电子设备、电脑）
click_product_id ：可能是在网站首页，或者是在商品列表页，点击了某个商品（比如呷哺呷哺火锅XX路店3人套餐、iphone 6s）
order_category_ids ：代表了可能将某些商品加入了购物车，然后一次性对购物车中的商品下了一个订单，这就代表了某次下单的行为中，有哪些
商品品类，可能有6个商品，但是就对应了2个品类，比如有3根火腿肠（食品品类），3个电池（日用品品类）
order_product_ids ：某次下单，具体对哪些商品下的订单
pay_category_ids ：代表的是，对某个订单，或者某几个订单，进行了一次支付的行为，对应了哪些品类
pay_product_ids：代表的，支付行为下，对应的哪些具体的商品

```

## task表（mysql)

task表，其实是用来保存平台的使用者，通过J2EE系统，提交的基于特定筛选参数的分析任务的信息；

``` 
task_id: 表的主键
task_name: 任务名称
create_time: 创建时间
start_time: 开始运行的时间
finish_time: 结束运行的时间
task_type: 任务类型，就是说，在一套大数据平台中，肯定会有各种不同类型的统计分析任务，比如说用户访问session分析任务，页面单跳转化率统计任务；所以这个字段就标识了每个任务的类型
task_status: 任务状态，任务对应的就是一次Spark作业的运行，这里就标识了，Spark作业是新建，还没运行，还是正在运行，还是已经运行完毕
task_param: 最最重要，用来使用JSON的格式，来封装用户提交的任务对应的特殊的筛选参数

CREATE TABLE `task` (
  `task_id` int(11) NOT NULL AUTO_INCREMENT,
  `task_name` varchar(255) DEFAULT NULL,
  `create_time` varchar(255) DEFAULT NULL,
  `start_time` varchar(255) DEFAULT NULL,
  `finish_time` varchar(255) DEFAULT NULL,
  `task_type` varchar(255) DEFAULT NULL,
  `task_status` varchar(255) DEFAULT NULL,
  `task_param` text,
  PRIMARY KEY (`task_id`)
) ENGINE=InnoDB AUTO_INCREMENT=0 DEFAULT CHARSET=utf8

```

# 按照session粒度进行数据聚合

思维导图
![enter description here](./images/sparkApp_user_sessionAnalysis.xls)

``` 
 private static JavaPairRDD<String, String> aggregateBySession(
            SQLContext sqlContext,
            JSONObject taskParam) {
        //第一步 查询表user_visit_action的信息
        //{"startDate":"2019-04-01","endDate":"2019-05-01"}
        String startDate = ParamUtils.getParam(taskParam, Constants.PARAM_START_DATE);
        String endDate = ParamUtils.getParam(taskParam, Constants.PARAM_END_DATE);
        String sql =
                "select * "
                        + "from user_visit_action "
                        + "where date>='" + startDate + "' "
                        + "and date<='" + endDate + "'";

        JavaRDD<Row> actionRDD = sqlContext.sql(sql).javaRDD();

        //第二步：映射成( session_id, row) sessionid2ActionRDD
        JavaPairRDD<String, Row> sessionid2ActionRDD = actionRDD.mapToPair(
                /**
                 * PairFunction
                 * 第一个参数，相当于是函数的输入
                 * 第二个参数和第三个参数，相当于是函数的输出（Tuple），分别是Tuple第一个和第二个值
                 */
                new PairFunction<Row, String, Row>() {
                    public Tuple2<String, Row> call(Row row) throws Exception {
                        return new Tuple2<String, Row>(row.getString(2), row);
                    }
                }
        );

        //第三步 groupByKey ( session_id, row)
        JavaPairRDD<String, Iterable<Row>> sessionid2ActionsRDD = sessionid2ActionRDD.groupByKey();

        //第四步 第四步 mapToPair ( user_id, “session_id='##'|search_keyword='##'|click_category_id='##'”)
        JavaPairRDD<Long, String> userid2PartAggrInfoRDD = sessionid2ActionsRDD.mapToPair(
                new PairFunction<Tuple2<String, Iterable<Row>>, Long, String>() {
                    public Tuple2<Long, String> call(Tuple2<String, Iterable<Row>> tuple) throws Exception {
                        String sessionId = tuple._1;
                        Iterator<Row> iterator = tuple._2.iterator();

                        StringBuffer searchKeywordsBuffer = new StringBuffer("");
                        StringBuffer clickCategoryIdsBuffer = new StringBuffer("");

                        Long userid = null;
                        // 遍历session所有的访问行为
                        while (iterator.hasNext()) {
                            // 提取每个访问行为的搜索词字段和点击品类字段
                            Row row = iterator.next();
                            if (userid == null) {
                                userid = row.getLong(1);
                            }
                            String searchKeyword = row.getString(5);
                            Long clickCategoryId = 0L;
                            if(row.get(6) != null){
                                clickCategoryId = row.getLong(6);
                            }

                            // 实际上这里要对数据说明一下
                            // 并不是每一行访问行为都有searchKeyword何clickCategoryId两个字段的
                            // 其实，只有搜索行为，是有searchKeyword字段的
                            // 只有点击品类的行为，是有clickCategoryId字段的
                            // 所以，任何一行行为数据，都不可能两个字段都有，所以数据是可能出现null值的

                            if (StringUtils.isNotEmpty(searchKeyword)) {
                                if (!searchKeywordsBuffer.toString().contains(searchKeyword)) {
                                    searchKeywordsBuffer.append(searchKeyword + ",");
                                }
                            }
                            if (clickCategoryId != null) {
                                if (!clickCategoryIdsBuffer.toString().contains(
                                        String.valueOf(clickCategoryId))) {
                                    clickCategoryIdsBuffer.append(clickCategoryId + ",");
                                }
                            }
                        }

                        String searchKeywords = StringUtils.trimComma(searchKeywordsBuffer.toString());
                        String clickCategoryIds = StringUtils.trimComma(clickCategoryIdsBuffer.toString());

                        String partAggrInfo = Constants.FIELD_SESSION_ID + "=" + sessionId + "|"
                                + Constants.FIELD_SEARCH_KEYWORDS + "=" + searchKeywords + "|"
                                + Constants.FIELD_CLICK_CATEGORY_IDS + "=" + clickCategoryIds;

                        return new Tuple2<Long, String>(userid, partAggrInfo);
                    }
                }
        );

        //第五步 mapToPair ( user_id, row)
        // 查询所有用户数据，并映射成<userid,Row>的格式
        String usersql = "select * from user_info";
        JavaRDD<Row> userInfoRDD = sqlContext.sql(usersql).javaRDD();

        JavaPairRDD<Long, Row> userid2InfoRDD = userInfoRDD.mapToPair(
                new PairFunction<Row, Long, Row>() {
                    public Tuple2<Long, Row> call(Row row) throws Exception {
                        return new Tuple2<Long, Row>(row.getLong(0), row);
                    }
                }
        );

        //第六步 join ( user_id, (“session_id='##'|search_keyword='##'|click_category_id='##'”
        //                  ,row
        //                   )
        //            )
        JavaPairRDD<Long, Tuple2<String, Row>> userid2FullInfoRDD = userid2PartAggrInfoRDD.join(userid2InfoRDD);

        //第七步 mapToPair ( user_id,  “
        //                    session_id='##'|search_keyword='##'|click_category_id='##'
        //                    |age='##'
        //                    |professional='##'
        //                    |city='##'
        //                    |sex='##'
        //                    ”
        //                )
        JavaPairRDD<String, String> sessionid2FullAggrInfoRDD = userid2FullInfoRDD.mapToPair(
                new PairFunction<Tuple2<Long, Tuple2<String, Row>>, String, String>() {
                    public Tuple2<String, String> call(Tuple2<Long, Tuple2<String, Row>> tuple) throws Exception {
                        String partAggrInfo = tuple._2._1;
                        Row userInfoRow = tuple._2._2;

                        String sessionid = StringUtils.getFieldFromConcatString(
                                partAggrInfo, "\\|", Constants.FIELD_SESSION_ID);

                        int age = userInfoRow.getInt(3);
                        String professional = userInfoRow.getString(4);
                        String city = userInfoRow.getString(5);
                        String sex = userInfoRow.getString(6);

                        String fullAggrInfo = partAggrInfo + "|"
                                + Constants.FIELD_AGE + "=" + age + "|"
                                + Constants.FIELD_PROFESSIONAL + "=" + professional + "|"
                                + Constants.FIELD_CITY + "=" + city + "|"
                                + Constants.FIELD_SEX + "=" + sex;

                        return new Tuple2<String, String>(sessionid, fullAggrInfo);
                    }
                }
        );
        return sessionid2FullAggrInfoRDD;
    }
```




