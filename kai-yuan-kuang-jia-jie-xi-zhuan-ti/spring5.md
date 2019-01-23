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

> 不同的实现方式，spring 会使用不同的类进行初始化以及获取
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

@ComponentScan 扫描规则认识

> @interface ComponentScan 类

```
package org.springframework.context.annotation;

import java.lang.annotation.Documented;
import java.lang.annotation.ElementType;
import java.lang.annotation.Repeatable;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;
import org.springframework.beans.factory.support.BeanNameGenerator;
import org.springframework.core.annotation.AliasFor;

@Retention(RetentionPolicy.RUNTIME)
@Target({ElementType.TYPE})
@Documented
@Repeatable(ComponentScans.class)
public @interface ComponentScan {
    @AliasFor("basePackages")
    String[] value() default {}; //扫描的包路径

    @AliasFor("value")
    String[] basePackages() default {};

    Class<?>[] basePackageClasses() default {};

    Class<? extends BeanNameGenerator> nameGenerator() default BeanNameGenerator.class;

    Class<? extends ScopeMetadataResolver> scopeResolver() default AnnotationScopeMetadataResolver.class;

    ScopedProxyMode scopedProxy() default ScopedProxyMode.DEFAULT;

    String resourcePattern() default "**/*.class";

    boolean useDefaultFilters() default true;//扫描范围，默认扫描value()包下面的全部

    ComponentScan.Filter[] includeFilters() default {};//包含的类

    ComponentScan.Filter[] excludeFilters() default {};//不包含的类

    boolean lazyInit() default false;

    @Retention(RetentionPolicy.RUNTIME)
    @Target({})
    public @interface Filter {
        FilterType type() default FilterType.ANNOTATION;//扫描规则

        @AliasFor("classes")
        Class<?>[] value() default {};

        @AliasFor("value")
        Class<?>[] classes() default {};

        String[] pattern() default {};
    }
}
```

> @ComponentScan\(value="com.enjoy.cap2扫描的包文件",
>
> includeFilters={
>
> @Filter\(type=FilterType.ANNOTATION,classes={Controller.class}\),        @Filter\(type=FilterType.ASSIGNABLE\_TYPE,classes={BookService.class}\)
>
> },
>
> useDefaultFilters=false\) //默认是true,扫描所有组件，要改成false,使用自定义扫描范围
>
> \*/
>
> //@ComponentScan value:指定要扫描的包
>
> //excludeFilters = Filter\[\] 指定扫描的时候按照什么规则排除那些组件
>
> //includeFilters = Filter\[\] 指定扫描的时候只需要包含哪些组件
>
> //useDefaultFilters = false 默认是true,扫描所有组件，要改成false
>
> //－－－－扫描规则如下
>
> //FilterType.ANNOTATION：按照注解
>
> //FilterType.ASSIGNABLE\_TYPE：按照给定的类型；比如按BookService类型
>
> //FilterType.ASPECTJ：使用ASPECTJ表达式
>
> //FilterType.REGEX：使用正则指定
>
> //FilterType.CUSTOM：使用自定义规则，自已写类，实现TypeFilter接口

配置类，自定义扫描规则

```
@Configurable
//自定义类来过滤规则
@ComponentScan(value = "com.zachary.springanno.cap2",
        includeFilters = {
                @Filter(type = FilterType.CUSTOM, classes = {CustomFilter.class})
        },
        useDefaultFilters= false)
public class Cap2MainConfig {
    @Bean
    public Person person01() {
        return new Person("admin", 18);
    }
}
```

FilterType.CUSTOM

```
import java.io.IOException;

import org.springframework.core.io.Resource;
import org.springframework.core.type.AnnotationMetadata;
import org.springframework.core.type.ClassMetadata;
import org.springframework.core.type.classreading.MetadataReader;
import org.springframework.core.type.classreading.MetadataReaderFactory;
import org.springframework.core.type.filter.TypeFilter;

public class CustomFilter implements TypeFilter {
    private ClassMetadata classMetadata;

    /*
     * MetadataReader:读取到当前正在扫描类的信息
     * MetadataReaderFactory:可以获取到其他任何类信息
     */

    @Override
    public boolean match(MetadataReader metadataReader, MetadataReaderFactory metadataReaderFactory)
            throws IOException {
        //获取当前类注解的信息
        AnnotationMetadata annotationMetadata = metadataReader.getAnnotationMetadata();
        //获取当前正在扫描的类信息
        classMetadata = metadataReader.getClassMetadata();
        //获取当前类资源(类的路径)
        Resource resource = metadataReader.getResource();

        String className = classMetadata.getClassName();
        System.out.println("----->" + className);
        if (className.contains("cap2")) {//当类包含cap2字符, 则匹配成功,返回true
            return true;//返回true 表示加入spring ioc 容器中
        }
        return false;//不加入
    }

}
```

@Scope注解

> //给容器中注册一个bean, 类型为返回值的类型, 默认是单实例
>
> ```
> /\*
>
>  \* prototype:多实例: IOC容器启动的时候,IOC容器启动并不会去调用方法创建对象, 而是每次获取的时候才会调用方法创建对象
>
>  \* singleton:单实例\(默认\):IOC容器启动的时候会调用方法创建对象并放到IOC容器中,
>  以后每次获取的就是直接从容器中拿\(大Map.get\)的同一个bean
>
>  \* request: 主要针对web应用, 递交一次请求创建一个实例
>
>  \* session:同一个session创建一个实例
>
>  \*/
> ```

```
    @Scope("prototype") //默认是单例模式，在Scope指明 prototype 则表示多例模式 
    @Bean
    public Person person() {
        return new Person("admin", 18);
    }
```

@Lazy注解

```
package com.zachary.springanno.cap4.config;

import com.zachary.springanno.cap1.Person;
import org.springframework.beans.factory.annotation.Configurable;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Lazy;

/**
 * @Title:
 * @Author:Zachary
 * @Desc:
 * @Date:2019/1/22
 **/

@Configurable
public class Cap4MainConfig {

    //jemes 老师的理解
    // 给容器中注册一个bean, 类型为返回值的类型, 默认是单实例
    /*
     * 懒加载: 主要针对单实例bean:默认在容器启动的时候创建对象
     * 懒加载:容器启动时候不创建对象, 仅当第一次使用(获取)bean的时候才创建被初始化
     */

    /**
     * 我的理解
     * 不加@Lazy 注解  对象实例在初始化spring IOC容器的时候就会被加载进去
     * 加上@Lazy注解  对象在被调用的时候才会被初始化 app.getBean(beanName)的时候才会被加载到spring IOC 容器中去
     */
//    @Lazy
    @Bean
    public Person person() {
        System.out.println("初始化调用...");
        return new Person("admin", 18);
    }
}
```

@Conditional注解

> 使用@Conditional 能够根据条件进行注册

Conditional.java

```
package org.springframework.context.annotation;

import java.lang.annotation.Documented;
import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

@Target({ElementType.TYPE, ElementType.METHOD})
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface Conditional {
    Class<? extends Condition>[] value();//放入一个CustomClass extend Condition
}
```

Cap5MainConfig .java

```
import com.zachary.springanno.cap1.Person;
import org.springframework.beans.factory.annotation.Configurable;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Conditional;

/**
 * @Title:
 * @Author:Zachary
 * @Desc: 使用@Conditional 条件注册
 * @Date:2019/1/22
 **/
@Configurable
public class Cap5MainConfig {
    @Bean
    public Person person() {
        System.out.println("容器...");
        return new Person("admin", 100);
    }

    @Conditional(WindowCondition.class)
    @Bean
    public Person person1() {
        return new Person("admin", 10);
    }

    @Conditional(LinuxCondition.class)
    @Bean
    public Person person2() {
        return new Person("admin", 18);
    }
}
```

> 使用@Conditional 根据我们自己类，去判断时候要加载该bean在spring ioc 容器中

WindowCondition.java

```
import org.springframework.beans.factory.config.ConfigurableListableBeanFactory;
import org.springframework.beans.factory.support.BeanDefinitionRegistry;
import org.springframework.context.annotation.Condition;
import org.springframework.context.annotation.ConditionContext;
import org.springframework.core.env.Environment;
import org.springframework.core.type.AnnotatedTypeMetadata;

/**
 * @Title:
 * @Author:Zachary
 * @Desc:
 * @Date:2019/1/22
 **/
public class WindowCondition implements Condition {
    /**
     * @param conditionContext      判断条件能使用的上下文信息（环境）
     * @param annotatedTypeMetadata 注释信息
     * @return
     */
    @Override
    public boolean matches(ConditionContext conditionContext, AnnotatedTypeMetadata annotatedTypeMetadata) {
        // 获取到spring ioc 使用的 beanFactory
        ConfigurableListableBeanFactory factory = conditionContext.getBeanFactory();
        //获得类加载
        ClassLoader loader = conditionContext.getClassLoader();
        //获取当前环境信息
        Environment environment = conditionContext.getEnvironment();
        //获得bean的注册类
        BeanDefinitionRegistry registry = conditionContext.getRegistry();
        //
        String property = environment.getProperty("os.name");
        System.out.println(property);
        if (property.contains("Windows")) { //判断操作系统是Windows 我们加入到spring ioc 容器中
            return true;
        }
        return false;
    }
}
```

LinuxCondition.java

```
import org.springframework.beans.factory.config.ConfigurableListableBeanFactory;
import org.springframework.beans.factory.support.BeanDefinitionRegistry;
import org.springframework.context.annotation.Condition;
import org.springframework.context.annotation.ConditionContext;
import org.springframework.core.env.Environment;
import org.springframework.core.type.AnnotatedTypeMetadata;

/**
 * @Title:
 * @Author:Zachary
 * @Desc:
 * @Date:2019/1/22
 **/
public class LinuxCondition implements Condition {
    /**
     * @param conditionContext      判断条件能使用的上下文信息（环境）
     * @param annotatedTypeMetadata 注释信息
     * @return
     */
    @Override
    public boolean matches(ConditionContext conditionContext, AnnotatedTypeMetadata annotatedTypeMetadata) {
        //获取spring ioc 使用的beanFactory
        ConfigurableListableBeanFactory factory = conditionContext.getBeanFactory();
        //        获取类加载
        ClassLoader classLoader = conditionContext.getClassLoader();
        //        获取当前环境信息
        Environment environment = conditionContext.getEnvironment();
        //        获取bean定义的注册类
        BeanDefinitionRegistry registry = conditionContext.getRegistry();

        String property = environment.getProperty("os.name");
        if (property.contains("linux")) {//判断操作系统为linux 加入到spring  ioc 容器中
            return true;
        }
        return false;
    }
}
```

总结

> 我们部署到不同的操作系统上时，就会加载不同的bean对象，进入spring ioc 容器

知道 给容器中注册组件的方式

> 1,@Bean: \[导入第三方的类或包的组件\],比如Person为第三方的类, 需要在我们的IOC容器中使用
>
> 2,包扫描+组件的标注注解\(@ComponentScan:  @Controller, @Service  @Reponsitory  @ Componet\),一般是针对 我们自己写的类,使用这个
>
> 3,@Import:\[快速给容器导入一个组件\] 注意:@Bean有点简单
>
> ```
> a,@Import\(要导入到容器中的组件\):容器会自动注册这个组件,bean 的 id为全类名
>
> b,ImportSelector:是一个接口,返回需要导入到容器的组件的全类名数组
>
> c,ImportBeanDefinitionRegistrar:可以手动添加组件到IOC容器, 所有Bean的注册可以使用BeanDifinitionRegistry
>
> 写CustomImportBeanDefinitionRegistrar实现ImportBeanDefinitionRegistrar接口即可
> ```
>
> 4,使用Spring提供的FactoryBean\(工厂bean\)进行注册

@Import注解

```
import com.zachary.springanno.cap1.Person;
import com.zachary.springanno.cap6.entity.Cat;
import com.zachary.springanno.cap6.entity.Dog;
import org.springframework.beans.factory.annotation.Configurable;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Import;

/**
 * @Title:
 * @Author:Zachary
 * @Desc:
 * @Date:2019/1/22
 **/
@Configurable
@Import({Dog.class, Cat.class, CustomImportSelector.class, CustomImportBeanDefinitionRegistrar.class})
public class Cap6MainConfig {


    /*
     * 给容器中注册组件的方式
     * 1,@Bean: [导入第三方的类或包的组件],比如Person为第三方的类, 需要在我们的IOC容器中使用
     * 2,包扫描+组件的标注注解(@ComponentScan:  @Controller, @Service  @Reponsitory  @ Componet),
     * 一般是针对 我们自己写的类,使用这个
     * 3,@Import:[快速给容器导入一个组件] 注意:@Bean有点简单
     *      a,@Import(要导入到容器中的组件):容器会自动注册这个组件,bean 的 id为全类名
     *      b,ImportSelector:是一个接口,返回需要导入到容器的组件的全类名数组
     *      c,ImportBeanDefinitionRegistrar:可以手动添加组件到IOC容器, 所有Bean的注册可以使用BeanDifinitionRegistry
     *          写JamesImportBeanDefinitionRegistrar实现ImportBeanDefinitionRegistrar接口即可
     *  4,使用Spring提供的FactoryBean(工厂bean)进行注册
     *
     *
     */


    @Bean
    public Person person() {
        return new Person("admin", 18);
    }


    //    这里注入我们的自定义的工厂bean
    @Bean
    public CustomFactoryBean factoryBean() {
        return new CustomFactoryBean();
    }
}
```

使用方法

> 在Class上面使用注解@Import

Import.java

```
package org.springframework.context.annotation;

import java.lang.annotation.Documented;
import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface Import {
    Class<?>[] value(); //接收一个Class的数组
}
```

> @Import\({A.class,B.class,CustomImportSelector.class,CustomImportBeanDefinitionRegistrar}\)
>
> A.class,B.class 手动导入的bean ，与使用@Bean 一样
>
> CustomImportSelector.class
>
> 自定义逻辑返回需要的组件 需要 implements ImportSelector
>
> CustomImportBeanDefinitionRegistrar.class
>
> 自定义注册类，通过自己的逻辑进行注册bean

CustomImportSelector.java

```
import org.springframework.context.annotation.ImportSelector;
import org.springframework.core.type.AnnotationMetadata;

/**
 * @Title:
 * @Author:Zachary
 * @Desc: 自定义逻辑返回需要导入的组件
 * @Date:2019/1/22
 **/

public class CustomImportSelector implements ImportSelector {

    //    返回值，就是导入到容器中的组件全类名
    //    AnnotationMatadata :当前标注@Import注解类的所有注解信息，不止能获取到import注解，还能获取其他注解
    //    方法不要返回null
    //    return null; 打开断点方法，如果返回空，F6跟进源码看看，数组。length包空指针异常，需要返回空字串数组
    //    return new String[]{}是OK的
    @Override
    public String[] selectImports(AnnotationMetadata annotationMetadata) {
        return new String[]{"com.zachary.springanno.cap6.entity.Fish"};
    }
}
```

CustomImportBeanDefinitionRegistrar.java

```
import com.zachary.springanno.cap6.entity.Tiger;
import org.springframework.beans.factory.support.BeanDefinitionRegistry;
import org.springframework.beans.factory.support.RootBeanDefinition;
import org.springframework.context.annotation.ImportBeanDefinitionRegistrar;
import org.springframework.core.type.AnnotationMetadata;

/**
 * @Title:
 * @Author:Zachary
 * @Desc:自定义注册类，实现bean的注册
 * @Date:2019/1/22
 **/
public class CustomImportBeanDefinitionRegistrar implements ImportBeanDefinitionRegistrar {

    //    AnnotationMetadata:当前类的注解信息
    //    BeanDefinitionRegistry:BeanDefinition注册类
    //    把所有需要的添加到容器中的bean
    //    调用BeanDefinitionRegistry.registerBeanDefinition自定义手工注册进来
    @Override
    public void registerBeanDefinitions(AnnotationMetadata annotationMetadata, 
                                        BeanDefinitionRegistry beanDefinitionRegistry) {
        boolean definition1 = beanDefinitionRegistry.containsBeanDefinition("com.zachary.springanno.cap6.entity.Cat");
        boolean definition2 = beanDefinitionRegistry.containsBeanDefinition("com.zachary.springanno.cap6.entity.Dog");
        //       判断容器中是否有dog和cat组件，有的话，添加Tiger组件
        if (definition1 && definition2) {
            //            以前的bean都是全类名，现在自定义bean名
            //            跟进registerBefinition() ,第二个参数beanDefinition
            //            锁定Bean的定义信息（Bean 的类型，bean的scope）
            RootBeanDefinition beanDefinition = new RootBeanDefinition(Tiger.class);
            //            注册一个bean,bean名为custom_tiger
            beanDefinitionRegistry.registerBeanDefinition("custom_tiger", beanDefinition);
        }
    }
}
```

大致@Import里面就可以放这些Class，知道区别

> 1、直接注册bean，id为全类名，如A.class，B.class
>
> 2、implements ImportSelector  String\[\] selectImports\(AnnotationMetadata annotationMetadata\)
>
> 返回数组形式的注册bean，每个字符串是全类名。返回批量，该方法可以包含第一种。id问全类名
>
> 3、implements ImportBeanDefinitionRegistrar 自定义注册bean，还可以带有逻辑判断，自定义id

除了上面方式外，还有一种是通过implements FactoryBean&lt;T&gt; 实现的

> 首先我们自定义一个类CsutomFactoryBean implements FactoryBean&lt;T&gt; 这里的T就是 我们要注册的bean对象

CustomFactoryBean.java

```
import com.zachary.springanno.cap6.entity.Monkey;
import org.springframework.beans.factory.FactoryBean;
import org.springframework.lang.Nullable;

/**
 * @Title:
 * @Author:Zachary
 * @Desc: 创建一个Spring定义的工厂bean
 * @Date:2019/1/22
 **/

/* 使用spring提供的FactoryBean(工厂bean)
        *   beans.factory.FatoryBean源码跟进去
        *     容器调用getObject()返回对象，把对象放到容器中；
        *     getObjectType()返回对象类型
        *     isSingleton()是否单例进行控制
        *     新建CsutomFactoryBean实现FactoryBean
        *     在config里新建factoryBean()方法
        *     写完测试用例后:
        *       a,默认获取到的是工厂bean调用getObject创建的对象
        *       b,要获取工厂Bean本身,需要在id前加个  &factoryBean 
*/


public class CustomFactoryBean implements FactoryBean<Monkey> {
    //    返回一个Monkey对象，这个对象会添加到容器中
    @Nullable
    @Override
    public Monkey getObject() throws Exception {
        return new Monkey();
    }

    @Nullable
    @Override
    public Class<?> getObjectType() {
        return Monkey.class;
    }

    //是否是单例
    //    true：这个bean是单实例，在容器中只会保存一份
    //    false：每次获取都会创建一个新的bean
    //    怎么创建，就是调用getObject()方法创建
    @Override
    public boolean isSingleton() {
        return true;
    }


    public String getName() {
        return "factoryBean";
    }
}
```

> 将我们自定义的CustomFactoryBean注册到spring ioc 中

```
import com.zachary.springanno.cap1.Person;
import com.zachary.springanno.cap6.entity.Cat;
import com.zachary.springanno.cap6.entity.Dog;
import org.springframework.beans.factory.annotation.Configurable;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Import;

/**
 * @Title:
 * @Author:Zachary
 * @Desc:
 * @Date:2019/1/22
 **/
@Configurable
@Import({Dog.class, Cat.class, CustomImportSelector.class, CustomImportBeanDefinitionRegistrar.class})
public class Cap6MainConfig {


    /*
     * 给容器中注册组件的方式
     * 1,@Bean: [导入第三方的类或包的组件],比如Person为第三方的类, 需要在我们的IOC容器中使用
     * 2,包扫描+组件的标注注解(@ComponentScan:  @Controller, @Service  @Reponsitory  @ Componet),
     *  一般是针对 我们自己写的类,使用这个
     * 3,@Import:[快速给容器导入一个组件] 注意:@Bean有点简单
     *      a,@Import(要导入到容器中的组件):容器会自动注册这个组件,bean 的 id为全类名
     *      b,ImportSelector:是一个接口,返回需要导入到容器的组件的全类名数组
     *      c,ImportBeanDefinitionRegistrar:可以手动添加组件到IOC容器, 所有Bean的注册可以使用BeanDifinitionRegistry
     *          写JamesImportBeanDefinitionRegistrar实现ImportBeanDefinitionRegistrar接口即可
     *  4,使用Spring提供的FactoryBean(工厂bean)进行注册
     *
     *
     */


    @Bean
    public Person person() {
        return new Person("admin", 18);
    }


    //    这里注入我们的自定义的工厂bean
    @Bean
    public CustomFactoryBean factoryBean() {
        return new CustomFactoryBean();
    }
}
```

思考，我们通过spring ioc 容器去获取这个bean的时候，获取的我们注册的bean 还是CustomFactoryBean本身？

如果获取到的是我们注册的bean，那么请问怎么获取CsutomFactoryBean 本身呢？

> 通过id获取这个对象，他最终会调用
>
> @Nullable
>
> ```
> @Override
>
> public Monkey getObject\(\) throws Exception {
>
>     return new Monkey\(\);
>
> }
> ```
>
> getObject\(\)方法 ，那么说明 实例出来的是我们注册，需要的bean，
>
> getBean\("factoryBean"\) &gt;&gt; Monkey
>
> getBean\("&factoryBean"\) &gt;&gt; CsutomFactroyBean
>
> 注意下区别
>
> CustomFactoryBean factoryBean = \(CustomFactoryBean\) app.getBean\("&factoryBean"\);
>
> System.out.println\(factoryBean.getName\(\)\);
>
> Monkey monkey = \(Monkey\) app.getBean\("factoryBean"\);
>
> System.out.println\(monkey.getName\(\)\);



