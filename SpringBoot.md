# SpringBoot入门

## SpringBoot概述

### Spring缺点

* 配置繁琐 XML
* 依赖繁琐 Maven

### SpringBoot功能

* 自动配置
* 起步依赖：依赖传递
* 辅助功能

SpringBoot提供了一种快速开发Spring项目的方式，而不是对Spring功能上的增强

## 构建项目步骤

### 创建Controller

给类添加注解@RestController

给方法添加注解 @RequestMapping("path") 并指定路径 作为入口



### 创建引导类

springboot项目的入口

给类添加注解@SpringBootApplication

定义主函数main，调用静态方法SpringApplication.run(当前class, args)

运行引导类，可以在浏览器中访问localhost:8080/path



## 起步依赖原理

parent父工程的pom文件中定义了各种技术的版本信息（版本锁定）

starter定义了完成该功能需要的坐标合集

继承parent，引入starter就能很好的使用规定好的各种技术，不会造成版本冲突或不兼容



## 配置

### 两种配置文件类型：

properties和yaml(yml)

properties:

```properties
server.port=8081
```

yaml/yml:

```yml
server:
    port: 8082
```

优先级 properties > yml > yaml

同一个属性在高优先定义后不会被低优先级的配置文件修改


### YAML

全称YAML Ain't Markup Language 不是标记语言，是一种直观的能够被计算机识别的语言

简洁：不用写xml的标签尖括号，使用缩进的方式代替properties每次指定元素

#### 语法

* 数据值前必须有空格，作为分隔符
* 使用缩进表示层级，且必须使用空格（idea可以用tab）
* #表示注释

#### 数据格式

对象、数组、纯量

![image-20240422173822412](C:\Users\WindLY\AppData\Roaming\Typora\typora-user-images\image-20240422173822412.png)

参数引用：

```yaml
name: zhangsan
person:
    name: ${name} # 引用上方变量
```

### 读取配置内容

三种方式

* @Value
* Environment
* @ConfigurationProperties

#### @Value

在变量上使用注解@Value，可以将配置文件中的值赋值给单个变量

在变量少的时候适用

```java
@Value("${name}")
private String name; // zhangsan

@Value("${address[0]}")
private String address; // beijing
```

#### Environment

大量使用变量时适用

```java
@Autowired
private Environment env;
public void hello() {
    sout(env.getProperty("person.name"));
}
```

#### @ConfigurationProperties

将配置文件的变量加载到类变量时适用

```java
@Component
@ConfigurationProperties(prefix = "person") 
// 前缀表示访问yml文件中的对象，将变量加载到Java类的成员变量中
public class Person {
    private String name;
    private int age;
    private String[] address;
    // ...
}
```

### profile

配置方式
* 多profile文件方式
* yml多文档方式

profile激活方式
* 配置文件
* 虚拟机参数
* 命令行参数

#### 多profile文件方式

配置文件命名为：

```java
application.properties // 主配置文件
application-dev.properties
application-pro.properties
application-test.properties
```

在配置文件中命名参数，例如启用dev配置文件

```properties
spring.profiles.active=dev
```

#### yml多文档方式

使用---进行文档的分隔，并给每个文档命名，再激活其中一个文档

```yaml
---
server:
  port: 8081

spring:
  profiles: pro
---
server:
  port: 8082

spring:
  profiles: test
---
spring:
  profiles:
    active: pro
```

#### 虚拟机参数

更改idea设置

在VM options指定：-Dspring.profiles.active=dev

#### 命令行参数

打包成jar包后运行时设置参数

```shell
java -jar ./xx.jar --spring.profiles.active=pro
```

### 内部配置加载顺序

优先级如下：
* file:../config/
* file:../
* classpath:/config/
* classpath/

### 外部配置加载顺序

详见官网spring.io

## SpringBoot整合其他框架

