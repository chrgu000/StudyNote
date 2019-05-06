
# 配置文件

Spring boot支持的配置文件的格式有.properties、.yml等

## 全局配置文件

配置文件的作用：修改SpringBoot自动配置的默认值；

SpringBoot使用一个全局的配置文件，配置文件名是固定的；

* application.properties
* application.yml

## YAML配置的特点

YAML: 以数据为中心;
XML: 注重格式，可阅读性强；

**配置实例：配置Server端口号**

YAML
```
server:
  port: 8081
```

XML
```
<server>
    <port>8081</port>
</server>
```

# YAML语法

## 基础语法

**k:[空格]v** ：表示一对键值对(空格必须有)；

以空格的缩进来控制层级关系，只要是左对齐的一列数据，都是同一个层级的；

```
server:
    port: 8081
    path: /hello
```

属性和值也是大小写敏感的；

## 值的写法

### 字面量：普通的值（数字，字符串，布尔）
​	
1、k: v字面直接来写；
2、字符串默认不用加上单引号或者双引号；
3、""（双引号）不会转义字符串里面的特殊字符；如{name: "zhangsan \n lisi"}，输出{zhangsan 换行 lisi}；
4、''（单引号）会转义特殊字符；如{name: ‘zhangsan \n lisi’}，输出{zhangsan \n lisi}；

### 对象、Map（属性和值）

k: v 在下一行来写对象的属性和值的关系，注意缩进。

```
friends:
    lastName: zhangsan
    age: 20
```
或
```
friends: {lastName: zhangsan,age: 18}
```

### 数组（List、Set）

用 {- 值} 来表示数组；

```
pets:
 - cat
 - dog
 - pig
```
或
```
pets: [cat,dog,pig]
```

# YAML 配置文件的获取

## 1、文件配置处理器依赖导入

```
<!--导入配置文件处理器，配置文件进行绑定就会有提示-->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-configuration-processor</artifactId>
    <optional>true</optional>
</dependency>
```

## 2、JavaBean类

### Person.java

```
package com.dongk.springboot.bean;



import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.stereotype.Component;

import java.util.Date;
import java.util.List;
import java.util.Map;

/**
 * 将配置文件中配置的值映射到这个类中
 *
 * 只有这个组件是容器中的组件，才能使用这个功能
 * */
@Component
@ConfigurationProperties(prefix = "person")
public class Person {

    private String lastName;
    private Integer age;
    private Boolean boss;
    private Date birth;

    private Map<String,Object> maps;
    private List<Object> lists;
    private Dog dog;

    public String getLastName() {
        return lastName;
    }

    public void setLastName(String lastName) {
        this.lastName = lastName;
    }

    public Integer getAge() {
        return age;
    }

    public void setAge(Integer age) {
        this.age = age;
    }

    public Boolean getBoss() {
        return boss;
    }

    public void setBoss(Boolean boss) {
        this.boss = boss;
    }

    public Date getBirth() {
        return birth;
    }

    public void setBirth(Date birth) {
        this.birth = birth;
    }

    public Map<String, Object> getMaps() {
        return maps;
    }

    public void setMaps(Map<String, Object> maps) {
        this.maps = maps;
    }

    public List<Object> getLists() {
        return lists;
    }

    public void setLists(List<Object> lists) {
        this.lists = lists;
    }

    public Dog getDog() {
        return dog;
    }

    public void setDog(Dog dog) {
        this.dog = dog;
    }

    @Override
    public String toString() {
        return "Person{" +
                "lastName='" + lastName + '\'' +
                ", age=" + age +
                ", boss=" + boss +
                ", birth=" + birth +
                ", maps=" + maps +
                ", lists=" + lists +
                ", dog=" + dog +
                '}';
    }
}

```

### Dog.java

```
package com.dongk.springboot.bean;

import org.springframework.stereotype.Component;

@Component
public class Dog {

    private String name;
    private  Integer age;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public Integer getAge() {
        return age;
    }

    public void setAge(Integer age) {
        this.age = age;
    }

    @Override
    public String toString() {
        return "Dog{" +
                "name='" + name + '\'' +
                ", age=" + age +
                '}';
    }
}

```

## 3、 配置文件

```
person:
  lastName: hello
  age: 18
  boss: false
  birth: 2017/12/12
  maps: {k1: v1,k2: 12}
  lists:
    - zhangsan
    - lisi
  dog:
    name: 金毛
    age: 2
```

## 4、主测试类

```
package com.dongk.springboot.bean;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.ConfigurableApplicationContext;

@SpringBootApplication
public class YamlApp {


    public static void main(String[] args) {
        ConfigurableApplicationContext ctx = SpringApplication.run(YamlApp.class, args);
        Person person = (Person)ctx.getBean("person");
        System.out.println(person);
    }
}

```

## 5、运行结果

```
Person{lastName='hello', age=18, boss=false, birth=Tue Dec 12 00:00:00 CST 2017, maps={k1=v1, k2=12}, lists=[zhangsan, lisi], dog=Dog{name='金毛', age=2}}
```

# Properties配置文件中文乱码问题

上面的例子也可以在.properties配置文件中配置，如下：

```
person.lastName=张三
person.age=${random.int}
person.birth=2017/12/15
person.boss=false
person.maps.k1=v1
person.maps.k2=14
person.lists=a,b,c
person.dog.name=hello_dog
person.dog.age=15
```

IDEA中.properties文件默认为UTF-8编码，中文会在运行时发生乱码，只需要设置在运行时转换为ASCII编码即可，如下：

![](./images/springboot_conf001.PNG)

# 使用@Value获取配置文件中的值

* @Value可以从配置文件中获取值(${});
* @Value可以使用SpEL表达式获取值(#{});
* @Value不支持复杂类型(如Map类型)，而@ConfigurationProperties支持复杂类型;

```
@Value("${person.lastName}")
private String lastName;
@Value("#{3 * 8}")
private Integer age;
```

# @PropertySource

@PropertySource表示加载指定的配置文件；

我们编写一个配置文件 person.properties

```
person.lastName=张三
person.age=${random.int}
person.birth=2017/12/15
person.boss=false
person.maps.k1=v1
person.maps.k2=14
person.lists=a,b,c
person.dog.name=hello_dog
person.dog.age=15
```

在配置类上使用注解@PropertySource，获取person.properties配置文件中的属性

```
@Component
@ConfigurationProperties(prefix = "person")
@PropertySource("classpath:person.properties")
public class Person {
```

# 配置类

定义一个组件;

```
package com.dongk.springboot.service;

public class HelloWorldService {
}

```

我们可以使用配置类，来代替之前Spring中的配置文件；

```
package com.dongk.springboot.conf;

import com.dongk.springboot.service.HelloWorldService;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

/**
 *  @Configuration 指明该类是一个配置类，
 *  用来代替之前的Spring配置文件。
 * */
@Configuration
public class MyAppConfig {

    /**
     * 将方法的返回值添加到容器中；
     * 容器中这个组件的id就是方法名；
     * */
    @Bean
    public HelloWorldService helloWorldService(){
        return new HelloWorldService();
    }
}

```

测试配置类中的组件是否在IOC容器中存在；

```
package com.dongk.springboot;

import com.dongk.springboot.service.HelloWorldService;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.ConfigurableApplicationContext;

/**
 *
 * 标明一个主程序类，说明这是一个Spring Boot应用
 * */
@SpringBootApplication
public class MyAppTest {
    public static void main(String[] args) {
        ConfigurableApplicationContext ctx = SpringApplication.run(MyAppTest.class, args);
        HelloWorldService helloWorldService = (HelloWorldService)ctx.getBean("helloWorldService");
        System.out.println("获取HelloWorldService：" + helloWorldService);
    }
}

```

运行结果；

```
获取HelloWorldService：com.dongk.springboot.service.HelloWorldService@3c1e23ff
```

# 配置文件占位符

```
# lastName的值使用uuid
person.lastName=${random.uuid}

# ${person.hello:hello}
# 表示使用配置文件中的属性person.hello
# 如果属性person.hello不存在，默认为hello
person.dog.name=${person.hello:hello}_dog
```

# profile

用于快速切换环境；

## 多profile文件

开发环境端口号
```
server.port=8081
```

生产环境端口号
```
server.port=8082
```

默认端口号
激活开发环境profile
```
server.port=8080
spring.profiles.active=dev
```


## YAML文档块

--- 用来切分文档块，第一块为默认文档块

```
#默认端口号
server:
  port: 8080
spring:
  profiles:
    active: dev

# 开发环境
---
server:
  port: 8081
spring:
  profiles: dev

# 生产环境
---
server:
  port: 8082
spring:
  profiles: pro
```

## 通过命令行激活profile

java -jar SpringBoot-1.0-SNAPSHOT.jar com.dongk.springboot.MyAppTest.java --spring.profiles.active=dev

# 配置文件加载位置

Springboot启动的时候会扫描以下位置的application.properties、application.yml；

```
## Project(IDEA)下的config文件夹
–file:./config/
## Project(IDEA)的根目录
–file:./
## Module(IDEA)下的resources目录中的config文件夹
–classpath:/config/
## Module(IDEA)下的resources目录
–classpath:/
```

优先级由高到底，高优先级的配置会覆盖低优先级的配置；

项目打包好以后，我们可以使用命令行参数的形式，指定配置文件；命令行中指定的配置文件和默认配置文件共同起作用形成互补配置(命令行中指定配置文件的优先级高于jar包中的配置文件)；

java -jar SpringBoot-1.0-SNAPSHOT.jar --spring.config.location=D:/application.properties

# 自动配置原理

## 1、启动的时候加载主配置类

SpringBoot启动的时候加载主配置类，开启了自动配置功能(@EnableAutoConfiguration)；

## 2、@EnableAutoConfiguration加载各种自动配置类

@EnableAutoConfiguration利用EnableAutoConfigurationImportSelector给容器中导入各种自动配置类。

扫描所有jar包类路径下 META-INF/spring.factories，将里面配置的所有EnableAutoConfiguration的值加入到了容器中。

META-INF/spring.factories部分内容如下：

```
org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
org.springframework.boot.autoconfigure.admin.SpringApplicationAdminJmxAutoConfiguration,\
org.springframework.boot.autoconfigure.aop.AopAutoConfiguration,\
org.springframework.boot.autoconfigure.amqp.RabbitAutoConfiguration,\
org.springframework.boot.autoconfigure.batch.BatchAutoConfiguration,\
org.springframework.boot.autoconfigure.cache.CacheAutoConfiguration,\
org.springframework.boot.autoconfigure.cassandra.CassandraAutoConfiguration,\
org.springframework.boot.autoconfigure.cloud.CloudAutoConfiguration,\
org.springframework.boot.autoconfigure.context.ConfigurationPropertiesAutoConfiguration,\
```

## 3、自动配置类进行自动配置功能

下面以HttpEncodingAutoConfiguration（Http编码自动配置）为例。

```
//表示这是一个配置类；
@Configuration

//启动指定类的ConfigurationProperties功能；
//将配置文件中对应的值和HttpEncodingProperties绑定起来；
//并把HttpEncodingProperties加入到ioc容器中；
@EnableConfigurationProperties(HttpProperties.class)

//@Conditional注解(Spring底层注解)；如果满足指定的条件，整个配置类里面的配置就会生效；   
//判断当前应用是否是web应用；如果是，当前配置类生效；
@ConditionalOnWebApplication(type = ConditionalOnWebApplication.Type.SERVLET)

//判断当前项目有没有这个类CharacterEncodingFilter；
//SpringMVC中进行乱码解决的过滤器；
@ConditionalOnClass(CharacterEncodingFilter.class)

//判断配置文件中是否存在某个配置  spring.http.encoding.enabled；
//matchIfMissing = true : 如果不存在，判断也是成立的
@ConditionalOnProperty(prefix = "spring.http.encoding", value = "enabled", matchIfMissing = true)
public class HttpEncodingAutoConfiguration {
```

1）、根据当前不同的条件判断，决定这个配置类是否生效；
2）、这个组件的某些值需要从properties中获取；

## 4、关联配置文件中的属性

1）、所有在配置文件中能配置的属性都是在xxxxProperties类中封装着；
2）、配置文件能配置什么可以参照某个功能对应的属性类；

```
//从配置文件中获取指定的值和bean的属性进行绑定
@ConfigurationProperties(prefix = "spring.http.encoding")  
public class HttpEncodingProperties {

   public static final Charset DEFAULT_CHARSET = Charset.forName("UTF-8");

```

# @Conditional派生注解

@Conditional是Spring的原生注解，SpringBoot派生了很多注解，如下：

| @Conditional扩展注解                | 作用（判断是否满足当前指定条件）               |
| ------------------------------- | ------------------------------ |
| @ConditionalOnJava              | 系统的java版本是否符合要求                |
| @ConditionalOnBean              | 容器中存在指定Bean；                   |
| @ConditionalOnMissingBean       | 容器中不存在指定Bean；                  |
| @ConditionalOnExpression        | 满足SpEL表达式指定                    |
| @ConditionalOnClass             | 系统中有指定的类                       |
| @ConditionalOnMissingClass      | 系统中没有指定的类                      |
| @ConditionalOnSingleCandidate   | 容器中只有一个指定的Bean，或者这个Bean是首选Bean |
| @ConditionalOnProperty          | 系统中指定的属性是否有指定的值                |
| @ConditionalOnResource          | 类路径下是否存在指定资源文件                 |
| @ConditionalOnWebApplication    | 当前是web环境                       |
| @ConditionalOnNotWebApplication | 当前不是web环境                      |
| @ConditionalOnJndi              | JNDI存在指定项                      |

**自动配置类必须在一定的条件下才能生效；**

# 自动配置报告打印

在配置文件中配置如下内容：

application.yml
```
debug: true
```

就会在控制台打印启用的自动配置类、没启用的自动配置类；

启用的自动配置类
```
Positive matches:
-----------------

   CodecsAutoConfiguration matched:
      - @ConditionalOnClass found required class 'org.springframework.http.codec.CodecConfigurer' (OnClassCondition)

   CodecsAutoConfiguration.JacksonCodecConfiguration matched:
      - @ConditionalOnClass found required class 'com.fasterxml.jackson.databind.ObjectMapper' (OnClassCondition)
```

没有启用的自动配置类
```
Negative matches:
-----------------

   ActiveMQAutoConfiguration:
      Did not match:
         - @ConditionalOnClass did not find required class 'javax.jms.ConnectionFactory' (OnClassCondition)

   AopAutoConfiguration:
      Did not match:
         - @ConditionalOnClass did not find required class 'org.aspectj.lang.annotation.Aspect' (OnClassCondition)
```







