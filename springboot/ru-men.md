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

在游览器输入：http://localhost:8080 ，显示hello world!



