### 生命周期的思考

* 所有这些方法调用依赖于spring 容器

**实现方式**

**1、@Bean或者xml配置**

```java
@Bean(initMethod="init",destroyMethod = "destroy")
也可以在xml上配置
<bean id="person" class="com.zachary.springanno.cap1.Person" init-method="init" destroy-method="destroy">
    <property name="name" value="admin"/>;
    <property name="age" value="100"/>;
</bean>
```

这里的initMedthod里面的值 对应于bean对象里面的方法，destroyMethod 同理

```java
public class Person {
    public void init() {
        System.out.println("init....");
    }

    public void destroy() {
        System.out.println("destroy...");
    }
}
```

**2、实现implements InitializatingBean,DisposableBean**

```java
import org.springframework.beans.factory.DisposableBean;
import org.springframework.beans.factory.InitializingBean;
import org.springframework.stereotype.Component;

/**
 * @Title:
 * @Author:Zachary
 * @Desc:
 * @Date:2019/1/23
 **/
@Component
public class Train implements InitializingBean, DisposableBean {
    public Train() {
        System.out.println("...Construct");
    }

    //容器创建时调用
    @Override
    public void afterPropertiesSet() throws Exception {
        System.out.println("...init");
    }

    //容器销毁时调用
    @Override
    public void destroy() throws Exception {
        System.out.println("...destroy");
    }
}
```

**3、JDK自带的，可以使用JSR250规则定义的\(java规范\)两个注解来实现**

```java
import org.springframework.stereotype.Component;

import javax.annotation.PostConstruct;
import javax.annotation.PreDestroy;

/**
 * @Title:
 * @Author:Zachary
 * @Desc:
 * @Date:2019/1/23
 **/
@Component
public class Jeep {

    private int value;

    public int getValue() {
        return value;
    }

    public void setValue(int value) {
        this.value = value;
    }

    /**
     * 可以使用JSR250规则定义的(java规范)两个注解来实现
     *
     * @PostConstruct: 在Bean创建完成, 且属于赋值完成后进行初始化, 属于JDK规范的注解
     * @PreDestroy: 在bean将被移除之前进行通知, 在容器销毁之前进行清理工作
     */

    public Jeep() {
        System.out.println("jeep construct ...");
    }

    //Bean 创建完成，赋值完成，进行初始化，后执行
    @PostConstruct
    public void init() {
        System.out.println("jeep init ...");
    }

    //销毁时执行
    @PreDestroy
    public void destroy() {
        System.out.println("jeep destroy ...");
    }
}
```

**4、实现 implements BeanProcessor**

```java
import org.springframework.beans.BeansException;
import org.springframework.beans.factory.config.BeanPostProcessor;
import org.springframework.lang.Nullable;
import org.springframework.stereotype.Component;

/**
 * @Title:
 * @Author:Zachary
 * @Desc:
 * @Date:2019/1/23 <p>
 * BeanPostProcessor类[interface]: bean的后 置处理器,在bean初始化之前调用进行拦截,
 *                                 作用:在bean初始化前后进行一些处理工作, 打开此类
 * </p>
 * <p>
 * a> postProcessBeforeInitialization():在初始化之前进行后置处理工作(在init-method之前),
 * </p>
 * 什么时候调用:它任何初始化方法调用之前(比如在InitializingBean的afterPropertiesSet初始化之前,或自定义init-method调用之前使用)
 * <p>
 * b> postProcessAfterInitialization():在初始化之后进行后置处理工作, 比如在InitializingBean的afterPropertiesSet()
 * </p>
 * 执行顺序 Construct -- postProcessBeforeInitialization -- init --- postProcessAfterInitialization -- destroy
 **/
@Component
public class CustomBeanPostProcessor implements BeanPostProcessor {

    //    这个方法在初始化之前调用
    @Nullable
    @Override
    public Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
        System.out.println(beanName + "CustomBeanPostProcessor 1");
        return bean;
    }

    //这个方法在初始化之后调用
    @Nullable
    @Override
    public Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {
        System.out.println(beanName + "CustomBeanPostProcessor 2");
        return bean;
    }
}
```

**实现了implements ApplicationContextAware 可以获取到上下文 ApplicationContext对象**

```java
import javax.annotation.PostConstruct;
import javax.annotation.PreDestroy;

import org.springframework.beans.BeansException;
import org.springframework.context.ApplicationContext;
import org.springframework.context.ApplicationContextAware;
import org.springframework.stereotype.Component;
@Component
public class Plane implements ApplicationContextAware{
    private ApplicationContext applicationContext;
    public Plane(){
        System.out.println("Plane.....constructor........");
    }
    @PostConstruct
    public void init(){
        System.out.println("Plane.....@PostConstruct........");
    }

    @PreDestroy
    public void destory(){
        System.out.println("Plane.....@PreDestroy......");
    }
    @Override
    public void setApplicationContext(ApplicationContext applicationContext) throws BeansException {
        //将applicationContext传进来,可以拿到
        this.applicationContext = applicationContext;
    }
}
```



