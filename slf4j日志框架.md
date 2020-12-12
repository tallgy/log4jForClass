# slf4j日志框架

## 一. 门面(外观)模式以及日志门面

### 1.  门面模式的简要介绍



![image.png](https://img.hacpai.com/file/2019/07/image-0c2d0d74.png?imageView2/2/w/768/format/jpg/interlace/1/q/100)

​	外部客户端与一个子系统的通信必须通过一个统一的外观对象进行，为子系统中的一组接口提供一个一致的界面，外观模式定义了一个高层接口，这个接口使得这一子系统更加容易使用。

​	假设我们现在有一个使用者的实体类，他需要同时启动或者关闭以下三种电器，在没有门面的情况下，我们会这么做：

![image-20201130170540902](C:\Users\流离\AppData\Roaming\Typora\typora-user-images\image-20201130170540902.png)

​	可以看到我们的使用者，会频繁的和各个电器类打交道，显得比较麻烦。

​	假如这个时候加入了其他电器会这么样呢？



​	而加入了门面(电闸)后，我们的使用者就不会和各类电器直接打交道了，直接与门面交互就可以实现我们想要的结果

![image-20201130171530670](C:\Users\流离\AppData\Roaming\Typora\typora-user-images\image-20201130171530670.png)

### 2. 日志门面以及日志实现

​	slf4j是Simple Logging Facade for Java的简写，即Java简单日志门面，用来服务于各种各样的日志框架，充当日志门面

​	目前java生态用的比较多的日志框架：

JUL、JCL、Jboss-logging、logback、log4j、log4j2、slf4j....

| 日志门面  （日志的抽象层）                                   | 日志实现                                                     |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| JCL（Jakarta  Commons Logging）                                                                                            slf4j（Simple  Logging Facade for Java）                                                                                                                        jboss-logging | Log4j  JUL（java.util.logging）  Log4j2                   Logback |

以后我们要使用日志框架，就应该选择一个日志门面+日志实现

## 二. slf4j的使用



### 1. 在项目中使用slf4j

使用slf4j：**https://www.slf4j.org**

``` java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

public class HelloWorld {
  public static void main(String[] args) {
    Logger logger = LoggerFactory.getLogger(HelloWorld.class);
    logger.info("Hello World");
  }
} 
```

项目中要使用slf4j日志记录的方式，在系统中导入slf4j的jar包和一个日志实现的jar包

### 2. 日志开发应该遵守的规约

阿里巴巴Java开发手册对于日志框架开发使用的约束：

![image-20201129112321087](C:\Users\流离\AppData\Roaming\Typora\typora-user-images\image-20201129112321087.png)

我们在开发的时候，日志记录方法的调用，不应该来直接调用日志的实现类，而是调用日志抽象层里面的方法；



## 三. 绑定日志实现

来看slf4j官网上的用户手册的一张图：

![concrete-bindings.png (1152×636)](http://www.slf4j.org/images/concrete-bindings.png)

slf4j绑定到logback，需要导入slf4j.api.jar(slf4j的jar包)以及logback-classic.jar，logback-core.jar(logback的jar包)

同理，slf4j绑定到log4j，也是同样导入slf4j的jar包和log4j的jar包，唯一不同的是，slf4j绑定到log4j，需要一个适配层，因为后面考虑到面向slf4j编程的日志模式，才导入这层来适配log4j和slf4j，适配层已经帮我们实现了这种适配的关系，我们要做的就是导入对应适配层的jar包

每一个日志框架都有自己的配置文件，那么使用了slf4j后，我们项目中的日志框架应该使用谁的配置文件呢？

**slf4j只是一个日志门面，并不是日志系统的具体实现**

所以项目中的日志配置文件是使用日志实现框架本身的配置文件

## 四. 通过slf4j统一日志

### 1. 遗留问题

但是，在实际项目中，我们会遇到以下的问题 ：

**我们自己的系统中使用了logback这个日志系统**
**我们的系统使用了A.jar，A.jar中使用的日志系统为log4j**
**我们的系统又使用了B.jar，B.jar中使用的日志系统为slf4j-simple**

**这样，我们的系统就不得不同时支持并维护logback、log4j、slf4j-simple三种日志框架，非常不便。**

方案：通过日志门面来统一日志记录

那么怎么将所有日志框架统一到slf4j，进行统一的管理和输出呢？

![legacy.png (1587×1123)](http://www.slf4j.org/images/legacy.png)

我们只需要导入对应日志框架转成slf4j的中间转换包，就可以实现将所有日志框架统一到slf4j(把其他日志转换为slf4j)

### 2.  springboot的底层日志关系

作为参考，我们可以来了解一下sprngboot的底层日志关系

打开右侧的maven选项可以看到：

![image-20201130182315743](C:\Users\流离\AppData\Roaming\Typora\typora-user-images\image-20201130182315743.png)

​	

假如使用的不是IDEA的社区版，那么我们可以直接在pom.xml文件右键打开依赖树

![image-20201130202951657](C:\Users\流离\AppData\Roaming\Typora\typora-user-images\image-20201130202951657.png)

 	说明springboot在做日志统一也是通过中间转换包将其他日志框架转换成slf4j的，如果我们要引入其他框架，要日志统一，就必须要将这个日志的默认框架通过中间转换包换成slf4j

​	但是，还有一个问题，我们点进log4j到slf4j的中间转换包，可以看见这个jar包下面竟然有个org.apache.log4j的jar包。而log4j的默认jar包就是这个包名，所以我们在导入其他框架前，必须要把该框架对应的默认日志赖移除掉

![image-20201130211927472](C:\Users\流离\AppData\Roaming\Typora\typora-user-images\image-20201130211927472.png)

### 3. 将所有日志框架统一到slf4j的步骤

**如何让系统中所有的日志都统一到slf4j；**

1、将系统中其他日志框架先排除出去；

2、用中间包来替换原有的日志框架；

3、我们导入slf4j其他的实现

## 五. 切换日志框架

### 1. slf4j + log4j的方式

```xml
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-web</artifactId>
  <exclusions>
    <exclusion>
      <artifactId>logback-classic</artifactId>
      <groupId>ch.qos.logback</groupId>
    </exclusion>
    <exclusion>
      <artifactId>log4j-over-slf4j</artifactId>
      <groupId>org.slf4j</groupId>
    </exclusion>
  </exclusions>
</dependency>

<dependency>
  <groupId>org.slf4j</groupId>
  <artifactId>slf4j-log4j12</artifactId>
</dependency>
```



### 2. slf4j + log4j2的方式

``` xml
 <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
            <exclusions>
                <exclusion>
                    <artifactId>spring-boot-starter-logging</artifactId>
                    <groupId>org.springframework.boot</groupId>
                </exclusion>
            </exclusions>
        </dependency>

<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-log4j2</artifactId>
</dependency>
```



## 各日志框架之间的转换jar包附图：



![image-20201130184520329](C:\Users\流离\AppData\Roaming\Typora\typora-user-images\image-20201130184520329.png)







