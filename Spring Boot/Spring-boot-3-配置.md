
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
