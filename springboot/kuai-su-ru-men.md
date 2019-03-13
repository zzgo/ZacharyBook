### SpringBoot快速入门

可以继承spring-boot-start-parent项目来获取合适的默认配置

项配置你的项目继承spring-boot-starter-parent只需要简单地设置为，只需要改变版本，就能适配出所有合适的版本

```
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.1.2.RELEASE</version>
</parent>
```

注：你应该只需要在依赖上指定SpringBoot版本。如果导入其他的starters，你可以放心得省略版本号

使用没有父POM的SpringBoot

不是每一个人都喜欢继承spring-boot-starter-parent-POM。你可能需要使用公司的标准parent，或你可能倾向于显式声明所有Maven配置。

如果你不适用spring-boot-starter-parent，通过使用一个scope=import的依赖，你仍能获取到依赖管理的好处

```java
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



