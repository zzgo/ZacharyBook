## 入门

### 系统要求

默认情况下，本堂课使用SpringBoot 2.1.2最新版本，最好安装JDK8以及以上的版本，maven使用3.3或者以上的版本（本教程使用maven3.6版本）

### 第一个简单的SpringBoot项目

**pom.xml导入springboot依赖**

```java
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.1.2.RELEASE</version>
</parent>
```

App.java测试启动类

```java
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.EnableAutoConfiguration;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

/**
 * Hello world!
 */
@RestController
@EnableAutoConfiguration
public class App {

    @RequestMapping(value = "/")
    public String hello() {
        return "hello world!";
    }

    public static void main(String[] args) {
        SpringApplication.run(App.class, args);
    }
}
```

测试：

在游览器输入：[http://localhost:8080](http://localhost:8080) ，显示hello world!

### 详细说明

@**RestController**.这被称为一个构造型（stereotype）注解。它为阅读代码的人们提供建议。对于Spring，该类扮演了一个特殊角色。在本示例中，我们的类是一个web @Controller，所以当处理进来的web请求时，Spring会询问它。

@**RequestMapping**.注解提供路由信息。它告诉Spring任何来自"/"路径的HTTP请求都应该被映射到hello方法。@RestController注解告诉Spring以字符串/json串的形式渲染结果，并直接返回给调用者。

@**EnableAutoConfiguration**。这个注解告诉Spring Boot根据添加的jar依赖猜测你想如何配置Spring。由于spring-boot-starter-web添加了Tomcat和Spring MVC，所以auto-configuration将假定你正在开发一个web应用并相应地对Spring进行设置。

**main**方法。这只是一个标准的方法，它遵循Java对于一个应用程序入口点的约定。我们的main方法通过调用run，将业务委托给了Spring Boot的**SpringApplication**类。SpringApplication将引导我们的应用，启动Spring，相应地启动被自动配置的Tomcat web服务器。**我们需要将App.class作为参数传递给run方法来告诉SpringApplication谁是主要的Spring组件。**

### 注意事项

SpringBoot不需要使用任何特殊的代码结构，然而，这里有一些地方需要注意

**使用default包**

当类没有包含package声明时，它被认为处于default package下，通常不推荐使用default package，并应该避免使用它。因为对于使用@ComponentScan，@EntityScan或@SpringBootApplication注解的Spring Boot应用来说，来自每个jar的类都会被读取，这会造成一定的问题。

**定位main应用类**

通常建议你将main应用类放在位于其他类上面的根包（root package）中。通常使用@EnableAutoConfiguration注解你的main类，并且暗地里为某些项定义了一个基础“search package”。例如，如果你正在编写一个JPA应用，被@EnableAutoConfiguration注解的类所在包将被用来搜索@Entity项。

使用根包允许你使用@ComponentScan注解而不需要定义一个basePackage属性。如果main类位于根包中，你也可以使用@**SpringBootApplication**注解。

```java
cn
 +- enjoy
     +- myproject
         +- Application.java
         |
         +- domain
         |   +- Customer.java
         |   +- CustomerRepository.java
         |
         +- service
         |   +- CustomerService.java
         |
         +- web
             +- CustomerController.java
```



