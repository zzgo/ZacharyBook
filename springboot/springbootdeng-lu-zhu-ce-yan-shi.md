## SpringBoot登录注册演示

### 新建tab\_user表

```java
CREATE TABLE `tab_user` (
    `id`  int NOT NULL AUTO_INCREMENT ,
    `passwd`  varchar(255) NULL ,
    `username`  varchar(255) NULL ,
    PRIMARY KEY (`id`)
);
```

### 搭建SpringBoot环境

pom依赖

```java
<dependencies>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.11</version>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
    </dependencies>
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-dependencies</artifactId>
                <version>2.1.2.RELEASE</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```

App.java启动类

```java
@SpringBootApplication
public class App {
    public static void main(String[] args) {
        SpringApplication.run(App.class, args);
    }
}
```

#### 继承mybatis

pom文件加入：

```java
<dependency>
    <groupId>org.mybatis.spring.boot</groupId>
    <artifactId>mybatis-spring-boot-starter</artifactId>
    <version>1.2.0</version>
</dependency>
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
</dependency>
```

在application.properties加入

```java
spring.datasource.driverClassName=com.mysql.jdbc.Driver
spring.datasource.url=jdbc:mysql://127.0.0.1:3306/quickstart?serverTimezone=GMT%2B8
spring.datasource.username=root
spring.datasource.password=zhangqi

mybatis.mapperLocations=classpath:mapping/*.xml
```

这里采用自动生成代码

准备mybatis的生成文件generatorConfig.xml，并在相应目录创建好model,dao,mapping文件夹

