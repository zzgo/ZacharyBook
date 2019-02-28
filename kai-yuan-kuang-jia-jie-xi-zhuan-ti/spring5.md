#### @Import注解

```java
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

* 在Class上面使用注解@Import

**Import.java**

```java
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

* @Import\({A.class,B.class,CustomImportSelector.class,CustomImportBeanDefinitionRegistrar}\)
* A.class,B.class 手动导入的bean ，与使用@Bean 一样
* CustomImportSelector.class
* 自定义逻辑返回需要的组件 需要 implements ImportSelector
* CustomImportBeanDefinitionRegistrar.class
* 自定义注册类，通过自己的逻辑进行注册bean

**CustomImportSelector.java**

```java
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

**CustomImportBeanDefinitionRegistrar.java**

```java
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

* 直接注册bean，id为全类名，如A.class，B.class
* implements ImportSelector  String\[\] selectImports\(AnnotationMetadata annotationMetadata\)
* 返回数组形式的注册bean，每个字符串是全类名。返回批量，该方法可以包含第一种。id问全类名
* implements ImportBeanDefinitionRegistrar 自定义注册bean，还可以带有逻辑判断，自定义id

除了上面方式外，还有一种是通过**implements FactoryBean&lt;T&gt;** 实现的

* 首先我们自定义一个类CsutomFactoryBean implements FactoryBean&lt;T&gt; 这里的T就是 我们要注册的bean对象

**CustomFactoryBean.java**

```java
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

* 将我们自定义的CustomFactoryBean注册到spring ioc 中

```java
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

* 通过id获取这个对象，他最终会调用

```java
@Nullable
@Override
public Monkey getObject() throws Exception {
    return new Monkey();
}
getObject()方法 ，那么说明 实例出来的是我们注册，需要的bean，
getBean("factoryBean") >> Monkey
getBean("&factoryBean") >> CsutomFactroyBean
//注意下区别
CustomFactoryBean factoryBean = (CustomFactoryBean) app.getBean("&factoryBean");
System.out.println(factoryBean.getName());
Monkey monkey = (Monkey) app.getBean("factoryBean");
System.out.println(monkey.getName());
```



