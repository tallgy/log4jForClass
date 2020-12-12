# 一、log4j-文件拆分

## 1、FileAppender向文件输出

```
#文件
log4j.logger.cn.edu.sicnu.test=ERROR
log4j.appender.File=org.apache.log4j.FileAppender
log4j.appender.File.file=F://software-contents//intellijIDEA//log//a.log
log4j.appender.File.layout=org.apache.log4j.PatternLayout
log4j.appender.File.layout.ConversionPattern=%5p %d{yyyy MM dd HH:mm:ss,SSS} (%C:%M) [%t]%n
```

运行结果：

![1606744396784](C:\Users\封心辰溪\AppData\Roaming\Typora\typora-user-images\1606744396784.png)

## 2、DailyRollingFileAppender每日产生一个日志文件

```
#文件DailyRollingFileAppender
log4j.logger.cn.edu.sicnu.test=ERROR
log4j.appender.File=org.apache.log4j.DailyRollingFileAppender
log4j.appender.File.file=F://software-contents//intellijIDEA//log//a.log
log4j.appender.File.layout=org.apache.log4j.PatternLayout
log4j.appender.File.layout.ConversionPattern=%5p %d{yyyy MM dd HH:mm:ss,SSS} (%C:%M) [%t]%n
```

每日产生一个日志文件，当天日志文件的名字是a.log，第二天的时候就会把第一天的日志文件的名字加上一个时间 戳，当天的日志文件还是a.log。

运行结果：

![1606744666672](C:\Users\封心辰溪\AppData\Roaming\Typora\typora-user-images\1606744666672.png)

## 3、RollingFileAppender设置日志文件大小

```
#文件RollingFileAppenderlog4j.logger.cn.edu.sicnu.test=ERRORlog4j.appender.File=org.apache.log4j.RollingFileAppenderlog4j.appender.File.file=F://software-contents//intellijIDEA//log//a.loglog4j.appender.File.MaxFileSize=10KBlog4j.appender.File.layout=org.apache.log4j.PatternLayoutlog4j.appender.File.layout.ConversionPattern=%5p %d{yyyy MM dd HH:mm:ss,SSS} (%C:%M) [%t]%n
```

当日志文件达到指定大小后就会产生一个新的日志文件，在原来日志文件名字的基础上加上点1表示一个新的日志文件。

![1606744735746](C:\Users\封心辰溪\AppData\Roaming\Typora\typora-user-images\1606744735746.png)

![1606744745568](C:\Users\封心辰溪\AppData\Roaming\Typora\typora-user-images\1606744745568.png)

## 4、按照日志等级拆分文件

```
<?xml version="1.0" encoding="GB2312" ?>
<!DOCTYPE log4j:configuration SYSTEM "http://logging.apache.org/log4j/1.2/apidocs/org/apache/log4j/xml/doc-files/log4j.dtd">

<log4j:configuration debug="true">

    <appender name="log.console" class="org.apache.log4j.ConsoleAppender">
        <layout class="org.apache.log4j.PatternLayout">
            <param name="ConversionPattern" value="%5p %d{yyyy MM dd HH:mm:ss,SSS} (%C:%M) [%m]%n"/>
        </layout>
    </appender>
    <!--debug级别-->
    <appender name="log.file.debug" class="org.apache.log4j.DailyRollingFileAppender">
        <param name="File" value="F://software-contents//intellijIDEA//log//debug.log"/>
        <layout class="org.apache.log4j.PatternLayout">
            <param name="ConversionPattern" value="%5p %d{yyyy MM dd HH:mm:ss,SSS} (%C:%M) [%m]%n"/>
        </layout>
        <filter class="org.apache.log4j.varia.LevelRangeFilter">
            <param name="levelMin" value="debug"/>
            <param name="levelMax" value="debug"/>
            <param name="AcceptOnMatch" value="true"/>
        </filter>
    </appender>

    <!--info级别-->
    <appender name="log.file.info" class="org.apache.log4j.DailyRollingFileAppender">
        <param name="File" value="F://software-contents//intellijIDEA//log//info.log"/>
        <layout class="org.apache.log4j.PatternLayout">
            <param name="ConversionPattern" value="%5p %d{yyyy MM dd HH:mm:ss,SSS} (%C:%M) [%m]%n"/>
        </layout>
        <filter class="org.apache.log4j.varia.LevelRangeFilter">
            <param name="levelMin" value="info"/>
            <param name="levelMax" value="info"/>
            <param name="AcceptOnMatch" value="true"/>
        </filter>
    </appender>

    <!--warn级别-->
    <appender name="log.file.warn" class="org.apache.log4j.DailyRollingFileAppender">
        <param name="File" value="F://software-contents//intellijIDEA//log//warn.log"/>
        <layout class="org.apache.log4j.PatternLayout">
            <param name="ConversionPattern" value="%5p %d{yyyy MM dd HH:mm:ss,SSS} (%C:%M) [%m]%n"/>
        </layout>
        <filter class="org.apache.log4j.varia.LevelRangeFilter">
            <param name="levelMin" value="warn"/>
            <param name="levelMax" value="warn"/>
            <param name="AcceptOnMatch" value="true"/>
        </filter>
    </appender>

    <!--error级别-->
    <appender name="log.file.error" class="org.apache.log4j.DailyRollingFileAppender">
        <param name="File" value="F://software-contents//intellijIDEA//log//error.log"/>
        <layout class="org.apache.log4j.PatternLayout">
            <param name="ConversionPattern" value="%5p %d{yyyy MM dd HH:mm:ss,SSS} (%C:%M) [%m]%n"/>
        </layout>
        <filter class="org.apache.log4j.varia.LevelRangeFilter">
            <param name="levelMin" value="error"/>
            <param name="levelMax" value="error"/>
            <param name="AcceptOnMatch" value="true"/>
        </filter>
    </appender>

    <!--fatal级别-->
    <appender name="log.file.fatal" class="org.apache.log4j.DailyRollingFileAppender">
        <param name="File" value="F://software-contents//intellijIDEA//log//fatal.log"/>
        <layout class="org.apache.log4j.PatternLayout">
            <param name="ConversionPattern" value="%5p %d{yyyy MM dd HH:mm:ss,SSS} (%C:%M) [%m]%n"/>
        </layout>
        <filter class="org.apache.log4j.varia.LevelRangeFilter">
            <param name="levelMin" value="fatal"/>
            <param name="levelMax" value="fatal"/>
            <param name="AcceptOnMatch" value="true"/>
        </filter>
    </appender>

    <logger name="cn.edu.sicnu.debug" additivity="false">
        <level value="debug"/>
        <appender-ref ref="log.file.debug"/>
    </logger>
    <logger name="cn.edu.sicnu.info" additivity="false">
        <level value="info"/>
        <appender-ref ref="log.file.info"/>
    </logger>
    <logger name="cn.edu.sicnu.warn" additivity="false">
        <level value="warn"/>
        <appender-ref ref="log.file.warn"/>
    </logger>
    <logger name="cn.edu.sicnu.error" additivity="false">
        <level value="error"/>
        <appender-ref ref="log.file.error"/>
    </logger>
    <logger name="cn.edu.sicnu.fatal" additivity="false">
        <level value="fatal"/>
        <appender-ref ref="log.file.fatal"/>
    </logger>

    <root>
        <level value="error"/>
        <appender-ref ref="log.console"/>
    </root>
</log4j:configuration>

```

把不同的包或者类设置为不同级别的，调用日志时就会把不同的信息写入不同的日志文件中去。

debug级别

![1606744948849](C:\Users\封心辰溪\AppData\Roaming\Typora\typora-user-images\1606744948849.png)

info级别

![1606744970578](C:\Users\封心辰溪\AppData\Roaming\Typora\typora-user-images\1606744970578.png)

warn级别

![1606744990610](C:\Users\封心辰溪\AppData\Roaming\Typora\typora-user-images\1606744990610.png)

error级别

![1606745003941](C:\Users\封心辰溪\AppData\Roaming\Typora\typora-user-images\1606745003941.png)

fatal级别

![1606745019322](C:\Users\封心辰溪\AppData\Roaming\Typora\typora-user-images\1606745019322.png)