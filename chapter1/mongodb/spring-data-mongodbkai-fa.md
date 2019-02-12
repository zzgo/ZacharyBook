引入pom文件

```
<dependency>
	<groupId>org.springframework.data</groupId>
	<artifactId>spring-data-mongodb</artifactId>
	<version>1.10.18.RELEASE</version>
</dependency>

tips:
spring-data-mongodb的最新版本是2.x.x，如果是spring为5.0版本以上的才推荐使用；
spring-data-mongodb的1.10.18版本基于spring4.3.x开发，但是默认依赖的mongodb驱动为2.14.3，可以将mongodb的驱动设置为3.5.0的版本；
spring-data-mongodb一般使用pojo的方式开发；
```



