
# 功能

浏览器发送hello请求，

# 步骤

## 1、在IDEA中创建一个Model-SpringBoot,并添加Maven依赖；

## 2、导入Spring Boot依赖；

pom.xml

```
<!-- Inherit defaults from Spring Boot -->
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.1.4.RELEASE</version>
</parent>

<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
</dependencies>
```

## 3、编写主程序，用来启动Spring Boot应用；

```
package com.dongk.springboot.helloworld;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

/**
 *
 * 标明一个主程序类，说明这是一个Spring Boot应用
 * */
@SpringBootApplication
public class HelloWorldApp {

    public static void main(String[] args) {
        SpringApplication.run(HelloWorldApp.class);
    }
}

```

## 4、编写相关的Controller

```
package com.dongk.springboot.helloworld;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.ResponseBody;

@Controller
public class HelloWorldController {

    @ResponseBody
    @RequestMapping("/hello")
    public String hello(){
        return "Hello World";
    }
}

```

## 5、运行主程序测试

http://localhost:8080/hello


## 6、部署

Spring Boot程序的部署，只需要将程序打包成一个可执行的jar包；

pom.xml

```
<!-- Package as an executable jar -->
<!-- 创建一个可执行的jar包 -->
<build>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
        </plugin>
    </plugins>
</build>
```

加入上面的插件后，只需要执行Maven打包程序即可。

# Hello World 探究

## 父项目

```
<!-- Inherit defaults from Spring Boot -->
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.1.4.RELEASE</version>
</parent>
```

来真正管理Spring Boot应用里面的所有版本依赖（Spring Boot的版本仲裁中心）。

## 导入的依赖

```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```

**spring-boot-starter-web**
场景启动器：帮我们导入了Web模块正常运行所依赖的组件（版本由版本仲裁中心控制）；

Spring Boot将所有的功能场景都抽取出来，做成一个个的starters（启动器），只需要在项目里面引入这个starter; 我们需要什么功能就导入什么场景启动器。

## @SpringBootApplication 注解

```
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan(
    excludeFilters = {@Filter(
    type = FilterType.CUSTOM,
    classes = {TypeExcludeFilter.class}
), @Filter(
    type = FilterType.CUSTOM,
    classes = {AutoConfigurationExcludeFilter.class}
)}
)
public @interface SpringBootApplication 
```

### @SpringBootConfiguration

标注在某个类上，表示这是一个Spring Boot的配置类；

### @EnableAutoConfiguration

开启自动配置功能：以前需要我们配置的东西，Spring Boot帮我们自动配置。

```
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@AutoConfigurationPackage
@Import({AutoConfigurationImportSelector.class})
public @interface EnableAutoConfiguration {
```

**Import({AutoConfigurationImportSelector.class})** ： 就是给容器中导入这个场景需要的所有组件，并配置好这些组件；

**@AutoConfigurationPackage** : 将主配置类（@SpringBootApplication标注的类）的所在包及子包里面的所有类扫描到Spring容器；

