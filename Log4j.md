</font>

@[TOC](Log4j及Log4j2安装配置基本教程)


<hr style=" border:solid; width:100px; height:1px;" color=#000000 size=1">

# 前言

<font color=#999AAA >本文介绍log4j以及log4j2基本安装配置过程，意在帮助了解并上手使用log4j日志框架</font>

<hr style=" border:solid; width:100px; height:1px;" color=#000000 size=1">



# 一、日志
## 1.1日志基本信息
在说明log4j是什么之前，我们先说明一个概念：日志。在最初学习java的时候我们知道，java中其实自带一个日志系统JDK Logging，这玩意就是我们常用的log。那么日志主要是干什么？类比生活中的日志，很简单可以理解到，日志是用来记录应用的运行状态以及一些关键业务信息的一个组件。

## 1.2为什么使用日志？（日志的优点）
在项目开发中，都不可避免的使用到日志。没有日志虽然不会影响项目的正确运行，但是没有日志的项目可以说是不完整的。日志在调试，错误或者异常定位，数据分析中的作用是不言而喻的。

### 1.2.1 调试
在Java项目调试时，查看栈信息可以方便地知道当前程序的运行状态，输出的日志便于记录程序在之前的运行结果。

### 1.2.2 错误定位
不要以为项目能正确跑起来就可以高枕无忧，项目在运行一段时候后，可能由于数据问题，网络问题，内存问题等出现异常。这时日志可以帮助开发或者运维人员快速定位错误位置，提出解决方案。

### 1.2.3 数据分析
大数据的兴起，使得大量的日志分析成为可能，ELK也让日志分析门槛降低了很多。日志中蕴含了大量的用户数据，包括点击行为，兴趣偏好等，用户画像对于公司下一步的战略方向有一定指引作用。

### 1.2.4 其它
除开上面几个优势，日志相比单纯用System.out.println()还有如下几个优点
1.可以设置输出样式，避免自己每次都写"ERROR: " + var；
2.可以设置输出级别，禁止某些级别输出。例如，只输出错误日志；
3.可以被重定向到文件，这样可以在程序运行结束后查看日志；
4.可以按包名控制日志级别，只输出某些包打的日志；

至此，我们可以了解到，所谓的日志，其实就是用更加系统的方式来打印记录程序运行信息。
## 1.3 日志级别
在进入Log4j实操之前，我们先搞清楚日志级别是个啥。
我们人体所有的动作都会消耗能量，同理，程序执行的所有操作都需要有计算机系统资源来配合，咱们之前说到，在一个正常的项目中，会有非常多的日志输出语句，无论是输出到文件还是控制台，这些语句的执行一定会消耗系统资源，为了解决这个问题，引入一个新的概念：日志级别。
简单的对比理解一下，在没有日志级别(即所有日志同等级)的情况下，我们写出的所有日志输出语句都会被执行，极大程度消耗系统资源。而有了日志级别之后，我们可以通过控制不同日志输出语句的级别，而选择性地输出某些语句。达成节约资源目的，同时还不用费心费力去挨着挨着删除输出语句。
设置的日志级别越高，打印出的日志信息就越少。

一般日志级别如下：
OFF 	为最高等级 关闭了日志信息  
FATAL  	为可能导致应用中止的严重事件错误  
ERROR 	为严重错误 主要是程序的错误  
WARN 	为一般警告，比如session丢失  
INFO 	为一般要显示的信息，比如登录登出  
DEBUG 	为程序的调试信息  
TRACE 	为比DEBUG更细粒度的事件信息  
ALL 	为最低等级，将打开所有级别的日志

Log4J推荐使用：DEBUG， INFO， WARN， ERROR

在log4j配置文件中，我们可以便捷的修改来达成控制日志输出的目的。
# 二、Log4j
在了解了日志之后，进入正题，log4j。
## 2.1 log4j是啥？
知道了日志是干啥的，现在回到我们今天的主题log4j上面来。
Log4j是Apache的一个开放源代码项目，通过使用Log4j，我们可以控制日志信息输送的目的地是控制台、文件、GUI组件、甚至是套接口服务 器、NT的事件记录器、UNIX Syslog守护进程等;我们也可以控制每一条日志的输出格式;通过定义每一条日志信息的级别，我们能够更加细致地控制日志的生成过程。最令人感兴趣的就 是，这些可以通过一个配置文件来灵活地进行配置，而不需要修改应用的代码。
说白了，你可以简单粗暴的理解成：log4j就是一个日志框架，使用它的目的就是为了便捷和系统地管理日志。

## 2.2 Log4j的下载及配置
我们上文讲到的log4j其实在2015年就停止了更新，其最后版本为1.2.17，取而代之的是Apache新推出log4j2，但log4j仍被许多项目使用，这些信息咱们只做了解，不用细品。

### 2.2.1 下载网址
[log4j-1.2.17下载地址](http://logging.apache.org/log4j/1.2/download.html)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201130190139615.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQzNjY3NjI1,size_16,color_FFFFFF,t_70)
下载完成后解压
然后在项目中添加jar包
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020113019023724.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/202011301938541.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQzNjY3NjI1,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201130194508344.png)
导入jar包后我们还需要编写配置文件，之前提到过，log4j一个很大的优点就是我们可以通过修改log4j的配置文件来实现管理日志的输出级别，输出位置等操作。
Log4j支持两种配置文件格式，一种是XML格式的文件，一种是Java特性文件（键=值）
配置文件一般在项目resources文件下创建，log4j的配置文件参数较多，具体操作由下一位成员系统地介绍。一般情况下我们可以直接到csdn等网站找到现成的配置文件复制粘贴进行使用。

咱们先默认使用了一个网上找的配置文件，很快啊，啪的一下就复制过来了。然后咱们开始学习简单使用。
### 2.2.2 简单使用
```java
public class Log4jTest {
    private static Logger logger = Logger.getLogger(Log4jTest.class);

    public static void main(String[] args) {
        logger.info("普通信息");
        logger.error("普通错误");
        logger.trace("堆栈信息");
        logger.fatal("致命错误");
        logger.warn("警告信息");
        logger.debug("调试信息");
    }
}
```
其实很容易理解，首先获取日志记录器，也就是

```java
private static Logger logger = Logger.getLogger(Log4jTest.class);
```
getLogger里的参数一般取自本类名，然后就是调用包中的方法打印相关日志。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201130210107807.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQzNjY3NjI1,size_16,color_FFFFFF,t_70)
getLogger方法中这个参数我们需要稍微研究一下，
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201130210233613.png)
这是log4j日志输出格式的打印参数之一，其目的如上图。节约时间，咱们直接上结论，%c打印的类名其实就是咱们getLogger传入的参数。为了验证结论，我们修改其传入内容在运行输出结果如下：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201130210500831.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQzNjY3NjI1,size_16,color_FFFFFF,t_70)
