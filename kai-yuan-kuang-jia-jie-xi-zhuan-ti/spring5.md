## Spring

思考Spring是什么？

> Spring是一款开源的轻量级的框架，是为了解决企业应用程序开发复杂行而创建的，Spring致力于解决JavaEE各层的解决方案
>
> 不仅仅于某一层的解决方案

Spring的目标

> 1、让现有的技术更容易使用
>
> 2、促进良好的编程习惯
>
> Spring是一个全面的解决方案，它坚持一个原则：不从新造轮子。已经有较好解决方案的领域，Spring绝不重复性实现，比如：对象持久化和OR映射，Spring只对现有的JDBC，Hibernate等技术提供支持，使之更容易使用，而不做重复的实现。Spring框架有很多特性，这些特性由7个定义良好的模块构成。

Spring的体系结构

> Spring Core：Spring核心，它是框架最基础的部分，提供IOC（inversion of control 控制反转）和依赖注入\(DI Dependency Injection\)特性
>
> Spring Context：Spring上下文容器，它是BeanFactory功能加强的一个子接口
>
> Spring Web：它提供Web应用开发的支持
>
> Spring MVC：它针对Web应用中MVC思想的实现
>
> Spring DAO：提供对JDBC抽象层，简化了JDBC编码，同时，编码更具有健壮性。
>
> Spring ORM：它支持用于流行的ORM框架的整合，比如：Spring + Hibernate、Spring + iBatis、Spring + JDO的整合等等。
>
> Spring AOP：AOP（Aspect Oriented Programming）即，面向切面编程，它提供了与AOP联盟兼容的编程实现

Spring 常用组件

![](/assets/88jakjsa.png)

这个注解或类依赖于spring-context

pom.xml导入spring-context

```
<dependencies>

        <!--spring  content 上下文-->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
            <version>5.0.6.RELEASE</version>
        </dependency>

    </dependencies>
```

补充

> 谈谈你对spring ioc 的理解  
> [https://www.cnblogs.com/xdp-gacl/p/4249939.html](https://www.cnblogs.com/xdp-gacl/p/4249939.html)

创建bean对象两种方式

> 第一种配置application.xml，在xml中注入bean对象和信息

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans 
                           http://www.springframework.org/schema/beans/spring-beans.xsd">
      <!--手动的注入bean对象，初始化bean信息 通过id 获取到bean-->
     <bean id="person" class="com.zachary.springanno.cap1.Person">
        <property name="name" value="admin"/>
        <property name="age" value="100"/>
    </bean>
    
</beans>
```

> 第二种通过，@Configurable注解，配置bean信息，通过@Bean注入

```
package com.zachary.springanno.cap1.config;

import com.zachary.springanno.cap1.Person;
import org.springframework.beans.factory.annotation.Configurable;
import org.springframework.context.annotation.Bean;

/**
 * @Title:
 * @Author:Zachary
 * @Desc:
 * @Date:2019/1/22
 **/
@Configurable
public class MainConfig {

    @Bean
    public Person person01() {
        return new Person("admin", 10);
    }
}

```

二者区别

> 不同的实现方向，spring 会使用不同的类进行初始化以及获取
>
> 采用xml形式的话，使用 ClassXmlPathApplication application = new ClassXmlPathApplication\("application.xml"\)
>
> 采用配置类加注解@Configurable的形式，使用 AnnocationConfigApplication application = new  AnnotationConfigApplication\(YourConfig.class\);

```
ClassPathXmlApplicationContext applicationContext = new ClassPathXmlApplicationContext("beans.xml");
Person person = (Person) applicationContext.getBean("person");
System.out.println(person);
```

```
AnnotationConfigApplicationContext annotationConfigApplicationContext =
                new AnnotationConfigApplicationContext(MainConfig.class);
//        修改bean的名称
//        直接修改方法名称
//        @Bean("你要修改的名称")

String[] beanNames = annotationConfigApplicationContext.getBeanNamesForType(Person.class);
for (String beanName : beanNames) {
    System.out.println(beanName);
    Person person = (Person) annotationConfigApplicationContext.getBean(beanName);
    System.out.println(person);
}
```

@Configurable解释

> 被@Configurable注解修饰的类，会告诉spring这是一个配置类



