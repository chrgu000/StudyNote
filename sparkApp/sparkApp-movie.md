
**Spark 商业案例之大数据电影点评系统应用案例**

https://github.com/steveloughran/winutils

# 需求

* 统计某特定电影观看者中男性和女性不同年龄的人数；
* 计算所有电影中平均得分最高（口碑最好）的电影TopN；
* 计算最流行电影（即所有电影中粉丝或者观看人数最多）的电影TopN；
* 实现所有电影中最受男性、女性喜爱的电影TopN；
* 实现所有电影中QQ 或者微信核心目标用户最喜爱电影TopN 分析；
* 实现所有电影中淘宝核心目标用户最喜爱电影TopN 分析；

# 大数据电影点评系统中电影数据说明

**ratings.dat（评级文件） 的格式描述如下。

``` 
UserID::MovieID::Rating::Timestamp
用户ID、电影ID、评分数据、时间戳
－用户ID 范围在1 ～ 6040 之间
－电影ID 范围在1～ 3952 之间
－评级：使用五星评分方式
－时间戳表示系统记录的时间
－每个用户至少有20 个评级
```

**评级文件ratings.dat 中摘取部分记录如下**

``` 
1::1193::5::978300760
1::661::3::978302109
1::914::3::978301968
1::3408::4::978300275
1::2355::5::978824291
1::1197::3::978302268
1::1287::5::978302039
1::2804::5::978300719
1::594::4::978302268
1::919::4::978301368
```

**用户文件users.dat 的格式描述如下**

``` 
UserID:: Gender::Age::Occupation::Zip-code
用户ID 、性别、年龄、职业、邮编代码
－所有的用户资料由用户自愿提供， GroupLens 项目组不会去检查用户数据的准确性。这个数据集中包含用户提供的用户数据
－性别: “ M”是男性、“ F ”是女性
－年龄由以下范围选择：
		1: ”少于18 岁”
		18: ” 18 年龄段：从18 岁到24 岁”
		25: ” 25 年龄段：从25 岁到34 岁”
		35: ” 35 岁年龄段：从35 岁到44 岁”
		45: ” 45 岁年龄段：从45 岁到49 岁”
		50: ” 50 岁年龄段：从50 岁到55 岁”
        56: ” 56 岁年龄段：大于56 岁”
```

**从用户文件users.dat 中摘取部分记录如下**

``` 
1::F::1::10::48067
2::M::56::16::70072
3::M::25::15::55117
4::M::45::7::02460
5::M::25::20::55455
6::F::50::9::55117
7::M::35::1::06810
8::M::25::12::11413
9::M::25::17::61614
10::F::35::1::95320
```

**电影文件movies.dat 的格式描述如下**

``` 
MovieID::Title::Genres
电影ID、电影名、电影类型
－标题是由亚马逊公司的互联网电影资料库（ IMDB ）提供的，包括电影发布年份
－电影类型包括以下类型：
Action ：行动
Adventure ：冒险
Animation ：动画
Children's ：儿童
Comedy ：喜剧
Crime ：犯罪
Documetary ：纪录片
Drama ：喜剧
Thriller ：惊悚
Romance ：浪漫
Fantasy ：幻想
```

**电影文件movies.dat 中摘取的部分记录如下**

``` 
1::Toy Story ( 1995)::Animation|Children's|Comedy 
2::Juma 口 ji (1995)::Adventure|Children's|Fantasy
3::Grumpier Old Men (1995)::Comedy|Romance 
4::Waiting to Exhale (1995)::Comedy|Drama 
5::Father of the Bride Part II (1995)::Comedy 
6::Heat (1995)::Action|Crime|Thriller 
7::Sabrina (1995)::Comedy|Romance 
8::Tom and Huck (1995)::Adventure|Children's
9::Sudden Death (1995)::Action 
lO::GoldenEye (1995)::Action|Adventure|Thriller
```

**职业文件 occupations.dat 的格式描述如下**

``` 
OccupationID::Occupatio
职业 ID 、职业名
－职业包含如下选择：
		0: “其他”或未指定
		1: “学术／教育者”
		2 : “艺术家”
		3: “文书／行政”
		4: “高校毕业生”
		5: “客户服务”
		6: “医生／保健”
		7 : “行政／管理”
		8: “农民”
		9: “家庭主妇”
		10: “中小学生”
		11 : “律师”
		12: “程序员”
		13: “退休”
		14 : “销售／市场营销”
		15: “科学家”
		16: “个体户”
		17: “技术员／工程师”
		18: “商人和工匠”
		19: “失业”
		20 : “作家”
```

**从职业文件 occupations.dat  中摘取部分记录如下**

``` 
O::other or not spec i fied 
l::academic/educator 
2::artist 
3::clerical/admin 
4::college/grad student 
5::customer service 
6::doctor/health care 
7::executive/managerial 
8::farmer 
9::homemaker 
10::K- 12 student 
11::lawyer 
12::programmer 
13::retired 
14::sales/marketing 
15::scientist 
16::self- employed 
17::technician/engineer 
18::tradesman/craftsman 
19::unemployed 
20::writer
```

# 观看电影1193的用户信息

分析如下（java 是根据这个导航图开发的）

![enter description here](./images/sparkApp_movie_1193UserInformation_3.png)

## scala


``` 
package com.dongk.spark.movie

import org.apache.log4j.{Level, Logger}
import org.apache.spark.{SparkConf, SparkContext}

/**
  *
  *   电影点评系统用户行为分析之一：统计具体某部电影观看的用户信息。
  *   如电影1193的用户信息（用户的 ID、Age、Gender、Occupation ）
  * */
object Movie_Users_Analyzer_RDD {
  def main(args: Array[String]): Unit = {
    //设置打印日志的输出级别
    Logger.getLogger("org").setLevel(Level.ERROR);

    //默认程序运行在本地 Local 模式中，主要用于学习和测试
    var masterUrl = "local[4]"
    //数据存放的目录
    var dataPath = "C:\\FILE\\movie\\"

    /**
      * 当我们把程序打包运行在集群上的时候，一般都会传入集群的 URL 信息;
      * 这里我们假设如果传入参数，第一个参数只传入 Spark 集群的 URL；
      * 第二个参数传入的是数据的地址信息；
      */
    if(args.length > 0){
      masterUrl = args(0)
    }else if(args.length > 1){
      dataPath = args(1)
    }

    /**
      * 创建 Spark 集群上下文 SC ,在 SC 中可以进行各种依赖和参数的设置等；
      * 大家可以通过 SparkSubmit 脚本的 help 去看设置信息
      **/
    val sc = new SparkContext(new SparkConf().setMaster(masterUrl).setAppName("Movie_Users_Analyzer"))

    //读取数据
    val usersRDD = sc.textFile(dataPath + "users.dat")
    val moviesRDD = sc.textFile(dataPath + "movies.dat")
    val occupationsRDD = sc.textFile(dataPath + "occupations.dat")
    val ratingsRDD = sc.textFile(dataPath + "ratings.dat")

    val userBasic = usersRDD
      .map(_.split("::"))
      .map(user => {
        (user(3),(user(0),user(1),user(2)))
      })

    for(elem <- userBasic.collect().take(2)){
      println("userBasic(职业ID,（用户ID,性别,年龄）)：" + elem)
    }

    val occupations = occupationsRDD
      .map(_.split("::"))
      .map(job => (job(0),job(1)))

    for(elem <- occupations.collect().take(2)){
      println("occupationsRDD(职业ID,职业名)：" + elem)
    }

    val userInformation = userBasic.join(occupations)

    userInformation.cache()

    for(elem <- userInformation.collect().take(2)){
      println("userInformationRDD(职业ID,((用户ID,性别，年龄)，职业名))：" + elem)
    }

    val targetMovie = ratingsRDD
      .map(_.split("::"))
      .map(x => (x(0),x(1)))
      .filter(_._2.equals("1193"))

    for(elem <- targetMovie.collect().take(2)){
      println("targetMovie(用户ID，电影ID) : " + elem)
    }

    val targetUsers = userInformation
      .map(x => (x._2._1._1,x._2))
    for(elem <- targetUsers.collect().take(2)){
      println("targetUsers(用户ID，（（用户ID，性别，年龄）,职业名）)：" + elem)
    }

    println("电影1193的用户信息（用户的 ID、Age、Gender、Occupation ）")
    val userInformationForSpecificMovie = targetMovie
      .join(targetUsers)
    for(ele <- userInformationForSpecificMovie.collect()){
      println("userInformationForSpecificMovie(用户ID，（（用户ID，性别，年龄）,职业名）)：" + ele)
    }
  }
}

```

## java

``` 
```


# 实现二次排序（java、scala）

二次排序的含义是先按照第一列数字进行排序，如果第一列数字中有相同的数字存在，然后再按照第二列的数字进行排序。

**dataforsecondarysorting.txt**

``` 
2 4 
2 10 
3 6 
1 5
```

**类说明**

``` 
SecondarySortingKey_java.java    //自定义key值
SecondarySortingMain_java.java   //实现二次排序功能
```

## java

### SecondarySortingKey_java.java

``` 
package com.dongk.spark.movie;

import scala.Serializable;
import scala.math.Ordered;

/**
 * 自定义key
 * 然后重写 $greater、$greater$eq、$less、less$eq、compare、compareTo方法，
 * 以及 hashCode、equals 方法
 * */
public class SecondarySortingKey_java implements Ordered<SecondarySortingKey_java>
, Serializable {

    //first、second为需要二次排序的组合key值
    private int first;
    private int second;

    public int getFirst() {
        return first;
    }
    public void setFirst(int first) {
        this.first = first;
    }
    public int getSecond() {
        return second;
    }
    public void setSecond(int second) {
        this.second = second;
    }

    //构造方法
    public SecondarySortingKey_java(int first, int second) {
        this.first = first;
        this.second = second;
    }

    @Override
    public String toString() {
        return first + " : " + second;
    }

    @Override
    public int hashCode() {
        int result = first;
        result = 31 * result + second;
        return result;
    }

    @Override
    public boolean equals(Object o) {
        if(this == o) return true;
        if(o == null || getClass() != o.getClass()) return false;
        SecondarySortingKey_java that = (SecondarySortingKey_java)o;
        if(first != that.first) return false;
        return second == that.second;
    }

    public int compare(SecondarySortingKey_java that) {
        if(this.first - that.getFirst() != 0 ) {
            return this.first - that.getFirst();
        }else{
            return this.second - that.second;
        }
    }

    public boolean $less(SecondarySortingKey_java that) {
        if(this.first < that.getFirst()){
            return true;
        }else if(this.first == that.getFirst() && this.second < that.second){
            return true;
        }
        return false;
    }

    public boolean $less$eq(SecondarySortingKey_java that) {
        if(SecondarySortingKey_java.this.$less(that)){
            return true;
        }else if( this.first == that.first && this.second == that.second){
            return true;
        }
        return false;
    }
    public boolean $greater(SecondarySortingKey_java that) {
        if(this.first > that.getFirst()){
            return true;
        }else if(this.first == that.getFirst() && this.second > that.second){
            return true;
        }
        return false;
    }

    public boolean $greater$eq(SecondarySortingKey_java that) {
        if(SecondarySortingKey_java.this.$greater(that)){
            return true;
        }else if( this.first == that.first && this.second == that.second){
            return true;
        }
        return false;
    }

    public int compareTo(SecondarySortingKey_java that) {
        if(this.first - that.getFirst() != 0 ) {
            return this.first - that.getFirst();
        }else{
            return this.second - that.second;
        }
    }
}

```

### SecondarySortingMain_java.java

``` 
package com.dongk.spark.movie;

import org.apache.spark.SparkConf;
import org.apache.spark.api.java.JavaPairRDD;
import org.apache.spark.api.java.JavaRDD;
import org.apache.spark.api.java.JavaSparkContext;
import org.apache.spark.api.java.function.Function;
import org.apache.spark.api.java.function.PairFunction;
import scala.Tuple2;

import java.util.List;

/**
 *
 * 二次排序功能实现
 *
 * 1、读入dataforsecondarysorting.txt中的每行数据
 * 2、对读入的数据进行 mapToPair 转换，格式为 key-value
 *    key ：自定义的 SecondarySortingKey_java
 *    value : 每行元数据
 * 3、使用sortByKey算子进行排序
 * 4、使用map函数进行转换，直接返回key-value键值对的value
 * */
public class SecondarySortingMain_java {
    public static void main(String args[]){

        //创建Spark集群上下文
        JavaSparkContext sc = new JavaSparkContext(new SparkConf()
                .setMaster("local[4]")
                .setAppName("SecondarySortingMain_java"));

        JavaRDD<String> lines = sc.textFile("C:\\FILE\\movie\\dataforsecondarysorting.txt");

        JavaPairRDD<SecondarySortingKey_java,String> keyvalues = lines.mapToPair(
                new PairFunction<String, SecondarySortingKey_java, String>() {
                    public Tuple2<SecondarySortingKey_java, String> call(String line) throws Exception {
                        String[] splited = line.split(" ");
                        SecondarySortingKey_java key = new SecondarySortingKey_java(
                                Integer.valueOf(splited[0]),
                                Integer.valueOf( splited[1]));
                        return new Tuple2<SecondarySortingKey_java, String>(key,line);
                    }
                }
        );

       //使用key 进行二次排序
        JavaPairRDD<SecondarySortingKey_java, String> sorted = keyvalues.sortByKey(true);

        JavaRDD<String> result = sorted.map(
                new Function<Tuple2<SecondarySortingKey_java, String>, String>() {
                    public String call(Tuple2<SecondarySortingKey_java, String> tuple) throws Exception {
                        return tuple._2;
                    }
                }
        );

        List<String> collected = result.take(10);

        /**
         * 下面输出的内容为：
         *  1 5
         *  2 4
         *  2 10
         *  3 6
         *
         */
        for(String item : collected){
            System.out.println(item);
        }
    }
}

```

## scala

### SecondarySortingKey.scala

``` 

```

### SecondarySortingMain.scala

``` 

```

