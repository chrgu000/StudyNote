
# 市面上的日志框架

关于日志框架统一的做法是，编写统一的接口层(抽象层)，然后根据抽象层编写各自的实现类。

常用的抽象层、实现层框架有：

```

抽象层                                       实现层
JCL（Jakarta Commons Logging）               Log4j 
SLF4j（Simple Logging Facade for Java）      JUL（java.util.logging） 
jboss-logging                                Log4j2
                                             Logback
```

**SpringBoot选用的日志框架如下：**
日志抽象层: SLF4J;
日志实现层: Logback;

# SLF4J日志框架使用原理

在开发的时候，日志记录方法的调用，不应该直接调用日志的实现类，而是调用日志抽象层里面的方法。

![SLF4J日志框架原理图](./images/springboot_log001.PNG)

1）SLF4J使用logback，可以直接使用；因为logback就是基于SLF4J编写的；

2）SLF4J使用log4j,因为log4j不是基于SLF4J编写的，所以需要一个适配层；适配层的作用就是实现SLF4J的抽象类，而在抽象类内部调用log4j的方法；

# 所有框架统一使用SLF4J的办法

在一个系统中我们想统一使用SLF4J进行日志的输出，但是其中第三方框架使用其它的日志输出框架（Spring（commons-logging）、Hibernate（jboss-logging）），可以采用如下的办法：

![统一使用SLF4J原理图](./images/springboot_log002.PNG)

1）jcl-over-slf4j里面的类和commons-logging 里面的类一模一样，但是内部却调用了SLF4J的方法；

# SpringBoot日志关系




