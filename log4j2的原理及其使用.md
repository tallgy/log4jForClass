# **1、log4j2的原理及其使用**



## **1.1、log4j2的介绍**



​		**Apache Log4j 2是Log4j的升级版，对Log4j的前身Log4j 1.x进行了重大改进，并提供了Logback中可用的许多改进，同时解决了Logback体系结构中的一些固有问题。采用了一些新技术（无锁异步、等等），使得日志的吞吐量、性能比log4j 1.x提高10倍，并解决了一些死锁的bug，而且配置更加简单灵活。**

###### **`**

### **1.1.1、log4j2的特征**

#### 	**1.性能提升**

​			**Log4j 2包含基于LMAX Disruptor库的下一代异步记录器。在多线程方案中，与Log4j 1.x和Logback相比，异步Logger的吞吐量高18倍，延迟降低了几个数量级。**

#### 	**2.避免锁定**

​			**编码为Log4j 2 API的应用程序始终可以选择使用任何SLF4J兼容库作为其Log4j-to-slf4j适配器的记录器实现。**

#### 	**3.自定义日志级别**

​			**在Log4j 2中，可以通过代码或配置轻松定义[自定义日志级别](http://logging.apache.org/log4j/2.x/manual/customloglevels.html)。不需要子类。**

#### 	**4.与Log4j 1.x兼容**

​			**Log4j 2的Log4j-1.2-api模块为使用Log4j 1日志记录方法的应用程序提供了兼容性。从Log4j 2.13.0开始，Log4j 2还提供了对Log4j 1.x配置文件的实验支持。**





## **1.2、log4j2的架构**

**![image-20201130215308544](https://img-blog.csdn.net/20170715114435821?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveWFuZ3NoYW5nd2Vp/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)**



**应用程序要使用Log4j 2的API，需要从LogManager中获取一个有明确名称的Logger。**

**LogManager将会定位到一个合适的LoggerContext并且从中获取Logger。**

**如果Logger必须被创建，那么它会和包含这些信息的LogConfig相关联：**
**a）与Logger相同的名称；**
**b）父包的名称；**
**c）根LoggerConfig。LoggerConfig对象根据配置中的Logger声明而创建。**

**LoggerConfig与实际处理LogEvent事件的Appender关联。**



#### **1.2.3、log4j2的几个概念**

**LoggerContext**

**LoggerContext在Logging System中扮演了锚点的角色。根据情况的不同，一个应用可能同时存在于多个有效的LoggerContext中。在同一LoggerContext下，log system是互通的。**

**Configuration**

**每一个LoggerContext都有一个有效的Configuration。Configuration包含了所有的Appenders、上下文范围内的过滤器、LoggerConfigs以及StrSubstitutor.的引用。在重配置期间，新与旧的Configuration将同时存在。当所有的Logger对象都被重定向到新的Configuration对象后，旧的Configuration对象将被停用和丢弃。**

 **Logger**

**Loggers 是通过调用LogManager.getLogger方法获得的。Logger对象本身并不实行任何实际的动作。它只是拥有一个name 以及与一个LoggerConfig相关联。它继承了AbstractLogger类并实现了所需的方法。当Configuration改变时，Logger将会与另外的LoggerConfig相关联，从而改变这个Logger的行为。**

**LoggerConfig**

**每个LoggerConfig和logger是对应的，获取到一个logger，写日志时其实是通过LoggerConfig来记日志的**



**区别**

**1.配置文件类型** 
**log4j是通过一个.properties的文件作为bai主配置文件的，而现在的log4j 2则已经弃用了这种方式，采用的是.xml，.json或者.jsn这种方式来做，可能这也是技术发展的一个必然性，毕竟properties文件的可阅读性真的是有点差。**
**2.核心JAR包 大家发现没，log4j和log4j 2的包路径是不同的，Apache为了区分，包路径都更新了，这样大家甚至可以在一个项目中使用2个版本的日志输出哦！(不过我想你们没有那么调皮吧，嘿嘿)**
**3.文件渲染** 
**log4j想要生效，我们需要在web.xml中进行配置，这段配置目的在于告诉工程去哪加载log4j的配置文件和定义一个扫描器,这样可以随心所欲的放置log4j配置文件。** 
**log4j2就比较简单，以maven工程为例，我们只需要把log4j2.xml放到工程resource目录下就行了。大家记住一个细节点，是log4j2.xml，而不是log4j.xml，xml名字少个2都不行！！**
**喜欢做学问的小伙伴，有兴趣可以去研究下，照我估计应该是在log4j2的包里面配置死了，而且大家可以尝试下怎么自定义log4j2.xml的位置。**



**4.Log调用** 
**log4j和log4j2调用都是很简单的。** 
**log4j：**

```java
import org.apache.log4j.Logger;
private final Logger LOGGER = Logger.getLogger(Test.class.getName());
```

**log4j2：**

```java
import org.apache.logging.log4j.Level;
import org.apache.logging.log4j.LogManager;
import org.apache.logging.log4j.Logger;
private static Logger logger = LogManager.getLogger(Test.class.getName());
```





## **1.3、log4j2 的使用**

#### 	**1.3.1、删除log4j的依赖和排除springboot的自带框架**

​		**这里就写了排除spring boot自带框架的代码**

```xml
		<!--排除springboot自带的日志框架-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
            <exclusions>
                <exclusion>    
                    <groupId>org.springframework.boot</groupId>
                    <artifactId>spring-boot-starter-logging</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
```



#### 	**1.3.2、导入log4j2**

****

```xml
    <!--导入log4j2-->
<dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-core</artifactId>
    <version>2.5</version>
</dependency>
<dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-api</artifactId>
    <version>2.5</version>
</dependency>

	<!--使用Lombok的@slf4j注解-->
    <dependency>
      <groupId>org.projectlombok</groupId>
      <artifactId>lombok</artifactId>
      <optional>true</optional>
    </dependency>
```



#### **测试代码**

```java
package cn.edu.sicnu;

import lombok.extern.slf4j.Slf4j;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;

@Slf4j
@RunWith(SpringJUnit4ClassRunner.class)
@SpringBootTest(classes = Application.class)
public class test {
    private Logger logger= LoggerFactory.getLogger(test.class);
    @Test
    public void findAll(){
        logger.info("test info");
        logger.warn("test warn");
        logger.error("test error");
    }
}

```



**![image-20201201195655269](.\log4j2-1.png)**



**这样只是在控制台进行了输出，并没有其他操作。**



#### 	**1.3.3、在resources文件夹中创建log4j2-spring.xml**

​		**log4j2已经全面废弃了.properties 配置，因为可读性太差。**



```xml
<?xml version="1.0" encoding="UTF-8"?>
<!--日志级别以及优先级排序: OFF > FATAL > ERROR > WARN > INFO > DEBUG > TRACE > ALL -->
<!--Configuration后面的status，这个用于设置log4j2自身内部的信息输出，可以不设置，当设置成trace时，你会看到log4j2内部各种详细输出-->
<!--monitorInterval：Log4j能够自动检测修改配置 文件和重新配置本身，设置间隔秒数-->
<configuration status="WARN" monitorInterval="30">
    <!--先定义所有的appender-->
    <appenders>
        <!--这个输出控制台的配置-->
        <console name="Console" target="SYSTEM_OUT">
            <!--输出日志的格式-->
            <PatternLayout pattern="[%d{HH:mm:ss:SSS}] [%p] - %l - %m%n"/>
            <!--控制台只输出level及其以上级别的信息（onMatch），其他的直接拒绝（onMismatch）-->
            <ThresholdFilter level="info" onMatch="ACCEPT" onMismatch="DENY"/>
        </console>
        <!--文件会打印出所有信息，这个log每次运行程序会自动清空，由append属性决定，这个也挺有用的，适合临时测试用-->
        <File name="Filelog" fileName="log/test.log" append="false">
            <!--%date表示日期，%thread表示线程名，%-5level：级别从左显示5个字符宽度 %msg：日志消息，%n是换行符  -->
            <PatternLayout pattern="%d{HH:mm:ss.SSS} %-5level %class{36} %L %M - %msg%xEx%n"/>
        </File>
        <!-- 这个会打印出所有的info及以下级别的信息，每次大小超过size，则这size大小的日志会自动存入按年份-月份建立的文件夹下面并进行压缩，作为存档-->
        <RollingFile name="RollingFileInfo" fileName="${sys:user.home}/logs/info.log"
                     filePattern="${sys:user.home}/logs/$${date:yyyy-MM}/info-%d{yyyy-MM-dd}-%i.log">
            <!--控制台只输出level及以上级别的信息（onMatch），其他的直接拒绝（onMismatch）-->
            <ThresholdFilter level="info" onMatch="ACCEPT" onMismatch="DENY"/>
            <PatternLayout pattern="[%d{HH:mm:ss:SSS}] [%p] - %l - %m%n"/>
            <Policies>
                <TimeBasedTriggeringPolicy/>
                <SizeBasedTriggeringPolicy size="100 MB"/>
            </Policies>
        </RollingFile>
        <RollingFile name="RollingFileWarn" fileName="${sys:user.home}/logs/warn.log"
                     filePattern="${sys:user.home}/logs/$${date:yyyy-MM}/warn-%d{yyyy-MM-dd}-%i.log">
            <ThresholdFilter level="warn" onMatch="ACCEPT" onMismatch="DENY"/>
            <PatternLayout pattern="[%d{HH:mm:ss:SSS}] [%p] - %l - %m%n"/>
            <Policies>
                <TimeBasedTriggeringPolicy/>
                <SizeBasedTriggeringPolicy size="100 MB"/>
            </Policies>
            <!-- DefaultRolloverStrategy属性如不设置，则默认为最多同一文件夹下7个文件，这里设置了20 -->
            <DefaultRolloverStrategy max="20"/>
        </RollingFile>
        <RollingFile name="RollingFileError" fileName="${sys:user.home}/logs/error.log"
                     filePattern="${sys:user.home}/logs/$${date:yyyy-MM}/error-%d{yyyy-MM-dd}-%i.log">
            <ThresholdFilter level="error" onMatch="ACCEPT" onMismatch="DENY"/>
            <PatternLayout pattern="[%d{HH:mm:ss:SSS}] [%p] - %l - %m%n"/>
            <Policies>
                <TimeBasedTriggeringPolicy/>
                <SizeBasedTriggeringPolicy size="100 MB"/>
            </Policies>
        </RollingFile>
    </appenders>
    <!--然后定义logger，只有定义了logger并引入的appender，appender才会生效-->
    <loggers>
        <!--过滤掉spring和mybatis的一些无用的DEBUG信息-->
        <logger name="org.springframework" level="INFO"></logger>
        <logger name="org.mybatis" level="INFO"></logger>
        <root level="info">
            <appender-ref ref="Console"/>
            <appender-ref ref="Filelog"/>
            <appender-ref ref="RollingFileInfo"/>
            <appender-ref ref="RollingFileWarn"/>
            <appender-ref ref="RollingFileError"/>
        </root>
    </loggers>
</configuration>
```



```xml
  简单说Appender就是一个管道，定义了日志内容的去向(保存位置)。
  配置一个或者多个Filter进行过滤
  配置Layout来控制日志信息的输出格式。
  配置Policies以控制日志何时(When)进行滚动。
  配置Strategy以控制日志如何(How)进行滚动。

  %d{HH:mm:ss.SSS} 表示输出到毫秒的时间
  %t 输出当前线程名称
  %-5level 输出日志级别，-5表示左对齐并且固定输出5个字符，如果不足在右边补0
  %logger 输出logger名称
  %c{参数} 或 %logger{参数}  ##输出日志名称
  %C{参数} 或 %class{参数    ##输出类型
  %d{参数}{时区te{参数}{时区} ##输出时间
  %F|%file                  ##输出文件名
  highlight{pattern}{style} ##高亮显示
  %l  ##输出错误的完整位置
  %L  ##输出错误行号
  %m 或 %msg 或 %message ##输出错误信息
  %M 或 %method ##输出方法名
  %n            ##输出换行符
  %level{参数1}{参数2}{参数3} ##输出日志的级别
  %t 或 %thread              ##创建logging事件的线程名


  其他常用的占位符有：
  %F 输出所在的类文件名，如Log4j2Test.java
  %L 输出行号
  %M 输出所在方法名
  %l 输出语句所在的行数, 包括类名、方法名、文件名、行数

  特殊符号：
  & —— &amp; 或者 &#38;
  < —— &lt;  或者 &#60;
  > —— &gt;  或者 &#62;
  “ —— &quot; 或者 &#34;
  ‘ —— &apos; 或者 &#39;


```



#### 	**1.3.4、指定配置的log4j2的文件路径**

```yml
#application.yml
logging:
	    config: classpath:log4j2-spring.xml
```



#### **1.3.5、再次运行**



```java
package cn.edu.sicnu;

import lombok.extern.slf4j.Slf4j;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;

@Slf4j
@RunWith(SpringJUnit4ClassRunner.class)
@SpringBootTest(classes = Application.class)
public class test {
    private Logger logger= LoggerFactory.getLogger(test.class);
    @Test
    public void findAll(){
        logger.info("test info");
        logger.warn("test warn");
        logger.error("test error");
    }
}

```



****

##### **日志将会输出到控制台，并且在本地文件也有保留**



**![image-20201201195744884](.\log4j2-2.png)**

******



##### **本地存储在了用户的logs里面**



**![image-20201130215800771](.\log4j2-3.png)**



## **1.4、log4j2 邮箱发送**



#### **1.4.1、准备**

​	**需要给邮箱开通SMTP服务**

​	**SMTP是一种提供可靠且有效的[电子邮件传输](https://baike.baidu.com/item/电子邮件传输/22035911)的协议。SMTP是建立在FTP[文件传输服务](https://baike.baidu.com/item/文件传输服务/5389842)上的一种邮件服务，主要用于系统之间的邮件信息传递，并提供有关来信的通知。**



#### **1.4.2、导入依赖**

```xml
<dependency>
  <groupId>javax.activation</groupId>
  <artifactId>activation</artifactId>
  <version>1.1.1</version>
</dependency>
<dependency>
    <groupId>com.sun.mail</groupId>
    <artifactId>javax.mail</artifactId>
    <version>1.5.4</version>
</dependency>
```



#### **1.4.3、写入log4j2-spring.xml**

```xml

<Configuration status="warn">
  <Appenders>
    <SMTP name="Mail" subject="Error Log" to="XXXXX@qq.com" from="XXXXX@163.com" replyTo="XXXXX@163.com"
          smtpProtocol="smtp" smtpHost="smtp.163.com" smtpPort="25" bufferSize="50" smtpDebug="false"
          smtpPassword="password" smtpUsername="XXXXX@163.com">
    </SMTP>
  </Appenders>
 
  <Loggers>
    <Root level="error">
          <AppenderRef ref="Mail"/>
    </Root>
  </Loggers>
</Configuration>
```



#### **1.4.4、SMTP的配置说明**

| **属性**         | **说明**                                                     |
| :--------------- | :----------------------------------------------------------- |
| **name**         | **appender 的名称**                                          |
| **subject**      | **邮件标题**                                                 |
| **to**           | **收件人列表，以逗号隔开 [7xxx@qq.com](http://qq.com/)[,1234@163.com](http://163.com/)** |
| **from**         | **发件人**                                                   |
| **smtpProtocol** | **协议，默认为 smtp**                                        |
| **smtpHost**     | **服务器 例：[smtp.163.com](http://smtp.163.com/)**          |
| **smtpPort**     | **端口**                                                     |
| **smtpPassword** | **密码**                                                     |
| **smtpUsername** | **用户名**                                                   |
| **bufferSize**   | **要缓冲以包含在消息中的最大日志事件数（默认为512）**        |
| **smtpDebug**    | **当设置为true时，可以在控制台上输出调试信息，默认为false**  |
| **replyTo**      | **以逗号分隔的回复电子邮件地址列表**                         |
| **cc**           | **明文抄送用户列表，多个以逗号隔开**                         |
| **bcc**          | **密文抄送用户列表，多个以逗号隔开**                         |



#### **1.4.5、日志打印**



```java
org.apache.logging.log4j.Logger logger = org.apache.logging.log4j.LogManager.getLogger("mail");
 logger.error("error log");
```

