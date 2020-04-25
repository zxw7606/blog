---
thumbnail: /images/.jpeg
title: java中的日志配置与解释
tags: 'java,log'
date: 2020-03-22 12:19:39
categories:
---



### log4j 配置

`log4j.properties`
```properties

 # rootLogger选择启用的日志appenderName，appenderName就是日志记录的方式的那个名称，比如记录到文件可以叫做A，记录控制台可以叫做B，数据库等等， 配置appenderName的实现在下边
log4j.rootLogger=  debug,console,A,B,C,D 

# 这是一个appenderName名为console的实现方式的配置
log4j.appender.console = org.apache.log4j.ConsoleAppender
log4j.appender.console.Target = System.out


#org.apache.log4j.HTMLLayout（以HTML表格形式布局），
#org.apache.log4j.PatternLayout（可以灵活地指定布局模式），
#org.apache.log4j.SimpleLayout（包含日志信息的级别和信息字符串），
#org.apache.log4j.TTCCLayout（包含日志产生的时间、线程、类别等等信息）
log4j.appender.console.layout = org.apache.log4j.PatternLayout

# %m   输出代码中指定的消息
# %p   输出优先级，即DEBUG，INFO，WARN，ERROR，FATAL 
# %r   输出自应用启动到输出该log信息耗费的毫秒数 
# %c   输出所属的类目，通常就是所在类的全名 
# %t   输出产生该日志事件的线程名 
# %n   输出一个回车换行符，Windows平台为“\r\n”，Unix平台为“\n” 
# %d   输出日志时间点的日期或时间，默认格式为ISO8601，也可以在其后指定格式，比如：%d{yyy MMM dd HH:mm:ss , SSS}，输出类似：2002年10月18日  22 ： 10 ： 28 ， 921  
# %l   输出日志事件的发生位置，包括类目名、发生的线程，以及在代码中的行数。举例：Testlog4.main(TestLog4.java: 10 ) 
log4j.appender.console.layout.ConversionPattern =  %d{ABSOLUTE} %5p %c{ 1 }:%L - %m%n
# 该appender只输出DEBUG级别以上的日志
log4j.appender.console.Threshold = DEBUG 

```

`java代码如下`，注：这是一个spring非web项目，日志框架为log4j，slf4j，slf4j-log4j2
```java


import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

public class AppTest {

  private static final Logger logger =  LoggerFactory.getLogger(AppTest.class);

    @Test
    public void logTest() {
        logger.info("hello");
    }
}


```
`文件结构如下：`
```
├── src
│   ├── test
│   │   └── java
│   │       └── cn
│   └── main
│       ├── java
│       │   └── cn
│       └── resources
│           ├── spring-dao.xml
│           ├── mapper
│           ├── jdbc.properties
│           └── log4j.properties #注意该文件一定得resource下根目录，即项目的根目录


```

### log4j2配置

1. `log4j2.xml`

```xml
<?xml version='1.0' encoding='UTF-8'?>
<configuration>

    <Appenders>
        <Console name="Console" target="SYSTEM_OUT">
            <PatternLayout pattern="%d{HH:mm:ss.SSS} [%t] %highlight{%-5level} %logger{36} - %msg%n"/>
        </Console>
    </Appenders>

    <Loggers>
        <Root level="all">
            <AppenderRef ref="Console"/>
        </Root>
    </Loggers>
</configuration>

```

2. `pom.xml`
```xml
<!-- 该pom集成了log4j2，slf4j-api，slf4j转log4j2的实现 -->
<dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-slf4j-impl</artifactId>
    <version>2.12.1</version>
</dependency>
<!-- 注意不要导入slf4j-log4j12，该适配器只能转换log4j  -->
```


### 实战，再进一步
如果我把这个包添加到一个spirngboot项目，作为依赖会怎么样呢？已知springboot默认为logback，而同一个java无法容纳两种日志。



1. 首先spring-boot项目的`pom.xml`
```xml

<!-- 刚刚那个spring的非web项目 -->
<dependency>
    <groupId>com.zxw</groupId>
    <artifactId>proj-service</artifactId>
    <version>1.0-SNAPSHOT</version>
</dependency>


<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
    <exclusions>
    <!-- 肯定要排除spirng-boot的默认日志实现类 -->
        <exclusion>
            <artifactId>logback-classic</artifactId>
            <groupId>ch.qos.logback</groupId>
        </exclusion>
        <!-- 排除掉低版本的log4j-to-slf4j，因为项目中就是要用log4j2啊，为何要转回去？ -->
        <exclusion>
            <artifactId>log4j-to-slf4j</artifactId>
            <groupId>org.apache.logging.log4j</groupId>
        </exclusion>
    </exclusions>
</dependency>
```

2. `application.properties`添加`logging.config=classpath:log4j2.xml`
