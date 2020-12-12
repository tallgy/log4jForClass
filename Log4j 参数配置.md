# Log4j 参数配置

## 1.日志优先级

log4j由三个重要组件构成：日志信息的优先级，日志信息的输出目的地，日志信息的输出格式。

日志信息的优先级(用来指定这条日志信息的**重要程度**),从高到低有：

OFF:最高级别日志

FATAL: 将会导致应用程序退出的错误 

ERROR : 发生错误事件，但仍不影响系统的继续运行 

WARN : 警告，即潜在的错误情形 

INFO : 一般和在粗粒度级别上，强调应用程序的运行全程 

DEBUG: 一般用于细粒度级别上，对调试应用程序非常有帮助 

ALL: 最低等级，打开所有日志记录 

**官方推荐只使用ERROR,WARN,INFO,DEBUG四个级别**，当设定了日志优先级后，只会输出**优先级等于或高于**这个优先级的日志信息。

## 2.Appender(输出端)

日志信息的输出目的地指定了日志输出到哪个地方，可以同时指定日志的输出目的地。Log4j允许将信息输出到许多不同的输出设备中，一个log信息输出目的地就叫做一个Appender。

| 输出端 类型              | 作用                                                         |
| ------------------------ | ------------------------------------------------------------ |
| ConsoleAppender          | 将日志输出到控制台                                           |
| FileAppender             | 将日志输出到文件中                                           |
| DailyRollingFileAppender | 将日志输出到一个日志文件，并且每天输出到一个新的文件         |
| RollingFileAppender      | 将日志信息输出到一个日志文件，并且指定文件的尺寸，当文件大 小达到指定尺寸时，会自动把文件改名，同时产生一个新的文件 |
| JDBCAppender             | 把日志信息保存到数据库中                                     |

## 3.Layout( 控制日志信息的显示格式 )

主要作用是控制日志信息的输出的形式。

| 格式化器类型  | 作用                                                         |
| ------------- | ------------------------------------------------------------ |
| HTMLLayout    | 格式化日志输出为HTML表格形式                                 |
| SimpleLayout  | 简单的日志输出格式化，打印的日志格式为（info - message）     |
| PatternLayout | 最强大的格式化，可以根据自定义格式输出日志，如果没有指定转换格式，就是用默认的转换格式 |

使用PatternLayout：

log4j 采用类似 C 语言的 printf 函数的打印格式格式化日志信息，具体的占位符及其含义如下：

%m  输出代码中指定的日志信息

%p  输出优先级，及 DEBUG、INFO 等

%n  换行符（Windows平台的换行符为 "\n"，Unix 平台为 "\n"）

%r   输出自应用启动到输出该 log 信息耗费的毫秒数

%c  输出打印语句所属的类的全名

%t   输出产生该日志的线程全名

%d  输出服务器当前时间，默认格式为 ISO8601，也可以在后面指定格式。如：%d{yyyy年MM月dd日 HH:mm:ss:sss}

%l   输出日志时间发生的位置，包括类名、发生的线程，以及在代码中的行数，如：Test.main(Test.java:10)

%F  输出日志消息产生时所在的文件名称

%L  输出代码中的行号

%x  输出和当前线程相关的 NDC（嵌套诊断环境）

%%  输出一个 "%" 字符

可以在 % 与字符之间加上修饰符来控制最小宽度、最大宽度和文本的对其方式。如：

%5c  输出category名称，最小宽度是5，category<5，默认的情况下右对齐
%-5c  输出category名称，最小宽度是5，category<5，"-"号指定左对齐,会有空格
%.5c  输出category名称，最大宽度是5，category>5，就会将左边多出的字符截掉，<5不会有空格
%20.30c  category名称<20补空格，并且右对齐，>30字符，就从左边交远销出的字符截掉

## 4.log4j properties配置文件

#### 4.1rootLogger配置：

语法：log4jLogger = [level],appenderName1,appenderName2,.......

level为日志优先级，appenderName为日志输出目的地，可以同时指定多个。

#### 4.2配置日志信息输出目的地：

  其语法为：
   log4j.appender.appenderName = fully.qualified.name.of.appender.class //
   "fully.qualified.name.of.appender.class" 可以指定下面五个目的地中的一个：

​     1.org.apache.log4j.ConsoleAppender（控制台）
​     2.org.apache.log4j.FileAppender（文件）
​     3.org.apache.log4j.DailyRollingFileAppender（每天产生一个日志文件）
​     4.org.apache.log4j.RollingFileAppender（文件大小到达指定尺寸的时候产生一个新的文件）
​     5.org.apache.log4j.WriterAppender（将日志信息以流格式发送到任意指定的地方）

​       1.ConsoleAppender选项
​          Threshold=WARN:指定日志消息的输出最低层次。
​          ImmediateFlush=true:默认值是true,意谓着所有的消息都会被立即输出。
​          Target=System.err：默认情况下是：System.out,指定输出控制台
​       2.FileAppender 选项
​          Threshold=WARN:指定日志消息的输出最低层次。
​          ImmediateFlush=true:默认值是true,意谓着所有的消息都会被立即输出。
​          File=mylog.txt:指定消息输出到mylog.txt文件。
​          Append=false:默认值是true,即将消息增加到指定文件中，false指将消息覆盖指定的文件内容。
​      3.DailyRollingFileAppender 选项
​          Threshold=WARN:指定日志消息的输出最低层次。
​          ImmediateFlush=true:默认值是true,意谓着所有的消息都会被立即输出。
​          File=mylog.txt:指定消息输出到mylog.txt文件。
​          Append=false:默认值是true,即以追加的方式将消息增加到指定文件中，false指将消息覆盖指定的文件内容。
​          DatePattern=''.''yyyy-ww:每周滚动一次文件，即每周产生一个新的文件。当然也可以指定按月、周、天、时和分。即对应的格式如下：
​          1)''.''yyyy-MM: 每月
​          2)''.''yyyy-ww: 每周 
​          3)''.''yyyy-MM-dd: 每天
​          4)''.''yyyy-MM-dd-a: 每天两次
​          5)''.''yyyy-MM-dd-HH: 每小时
​          6)''.''yyyy-MM-dd-HH-mm: 每分钟
​      4.RollingFileAppender 选项
​          Threshold=WARN:指定日志消息的输出最低层次。
​          ImmediateFlush=true:默认值是true,意谓着所有的消息都会被立即输出。
​          File=mylog.txt:指定消息输出到mylog.txt文件。
​          Append=false:默认值是true,即将消息增加到指定文件中，false指将消息覆盖指定的文件内容。
​          MaxFileSize=100KB: 后缀可以是KB, MB 或者是 GB. 在日志文件到达该大小时，将会自动滚动，即将原来的内容移到mylog.log.1文件。
​          MaxBackupIndex=2:指定可以产生的滚动文件的最大数。 

#### 4.3配置日志信息的格式

  其语法为：
　　1). log4j.appender.appenderName.layout = fully.qualified.name.of.layout.class
       "fully.qualified.name.of.layout.class" 可以指定下面4个格式中的一个：
            1.org.apache.log4j.HTMLLayout（以HTML表格形式布局），
　　    2.org.apache.log4j.PatternLayout（可以灵活地指定布局模式），
　　    3.org.apache.log4j.SimpleLayout（包含日志信息的级别和信息字符串），
　　    4.org.apache.log4j.TTCCLayout（包含日志产生的时间、线程、类别等等信息）
          1.HTMLLayout 选项
           LocationInfo=true:默认值是false,输出java文件名称和行号
           Title=my app file: 默认值是 Log4J Log Messages.
          2.PatternLayout 选项
           ConversionPattern=%m%n :指定怎样格式化指定的消息。
          3.XMLLayout 选项
           LocationInfo=true:默认值是false,输出java文件和行号
  