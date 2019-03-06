### 解析BeanPostProcessor源码

![](/assets/78327hfhasjj.png)

@Autowired @Resource @Iject

**总结：**

1、@Autowired是spring自带的，@Inject是JSR330规范实现的，@Resource是JSR250规范实现的，需要导入不同的包

2、@Autowired、@Inject用法基本一样，不同的是@Autowired有一个request属性

3、@Autowired、@Inject是默认按照类型匹配的，@Resource是按照名称匹配的

4、@Autowired如果需要按照名称匹配需要和@Qualifier一起使用，@Inject和@Name一起使用

### @Autowired

#### 注解的位置

查看@Autowired源码

```java
@Target(
    {ElementType.CONSTRUCTOR, 
    ElementType.METHOD, 
    ElementType.PARAMETER, 
    ElementType.FIELD,
     ElementType.ANNOTATION_TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface Autowired {

    /**
     * Declares whether the annotated dependency is required.
     * <p>Defaults to {@code true}.
     */
    boolean required() default true;

}
```

解释：

```
@Target(
    {ElementType.CONSTRUCTOR,  //注解位置 构造方法上
    ElementType.METHOD,        //方法上
    ElementType.PARAMETER,     //参数上
    ElementType.FIELD,         //字段上
    ElementType.ANNOTATION_TYPE})
```

**Sun.java**

```java
@Component
public class Sun {
    @Autowired //参数上
    private Moon moon;

    public Sun() {
    }

    @Autowired //构造方法
    public Sun(Moon moon) {
        this.moon = moon;
    }

    public Moon getMoon() {
        System.out.println(moon);
        return moon;
    }

    @Autowired  //方法上
    public void setMoon(Moon moon) {
        this.moon = moon;
    }
}
```

**注意下这个，参数上，是构造参数上，并且只能只有这个构造方法，不能有无参构造，否则就会出现问题null**

```java
public Sun(@Autowired Moon moon) { //参数上，是构造参数上，并且只能只有这个构造方法，不能有无参构造，否则就会出现问题null
        this.moon = moon;
}
```

### 自动转配：Aware 注入Spring 组件原理

自定义组件想要使用Spring的容器底层的组件（ApplicationContext,BeanFactory,.....）

思路：

自定义组件实现xxxAware，在创建对象的时候，会调用接口规定的方法注入到相关组件：Aware

比如：

```java
@Component
public class Plane implements ApplicationContextAware {

    private ApplicationContext applicationContext
    public Plane() {}
    
    @PostConstruct
    public void init() {}
    
    @PreDestroy
    public void destroy() {}
    
    @Override
    public void setApplicationContext(ApplicationContext applicationContext) throws BeansException {
        this.applicationContext = applicationContext;
    }
}
```

**Plane.java实现ApplicationContextAware就可以拿到我们的ApplicationContext上下文**

### 接口Aware

查看一下那些实现或者继承了这个类，列举一些类

```java
ApplicationContextAware org.springframework.context 
ApplicationEventPublisherAware org.springframework.context 注入时间委派性
BeanClassLoaderAware org.springframework.beans.factory 类加载器
BeanNameAware org.springframework.beans.factory bean名字
EnvironmentAware  org.springframework.context  运行环境
MessageSourceAware org.springframework.context 资源国际化
ResourceLoaderAware org.springframework.context 资源加载器
```

**操作：**

**新建Light.java类**

```java
@Component
public class Light implements ApplicationContextAware, BeanNameAware, EmbeddedValueResolverAware {

    //设置bean name
    @Override
    public void setBeanName(String s) {
        System.out.println("bean name " + s);
    }

    // 拿到上下文环境
    @Override
    public void setApplicationContext(ApplicationContext applicationContext) throws BeansException {
        System.out.println(applicationContext);
    }

    //解析
    @Override
    public void setEmbeddedValueResolver(StringValueResolver stringValueResolver) {
        System.out.println(stringValueResolver.resolveStringValue("系统是 ${os.name},计算#{3*8}"));
    }
}
```

implements ApplicationContextAware, BeanNameAware, EmbeddedValueResolverAware 

**实现 ApplicationContextAware获取Application环境上下文**

**实现 BeanNameAware获取设置bean name**

**实现 EmbeddedValueResolverAware 实现解析**

运行：

```java
bean name light
系统是 Windows 10,计算24
org.springframework.context.annotation.AnnotationConfigApplicationContext@2d38eb89
```

### 总结

把Spring底层的组件可以注入到自定义的bean中，ApplicationContextAware是利用ApplicationContextAwareProcessor来处理，其他XXXAware也类似，都有相关的Processor来处理，其实就是后置处理来处理

**XXXAware功能使用了XXXProcessor来处理的，这就是后置处理器的作用**

ApplicationContextAware--&gt;ApplicationContextProcessor后置处理器来处理的

### 问题：

### Spring怎么把applicationContext容器注入进来的呢？

很简单，首先执行到initializeBean-&gt;applyBeanPostProcessorsBeforeInitialization\(wrappedBean, beanName\);-&gt;遍历spring里面的所有Processor，然后我们自己的自定义实现了ApplicationContextAware 执行到，是否属于Aware，是否属于ApplicationContextAware 是的话，就调用setApplication方法注入，然后交由我们的自定义的来执行，也就可以获取到ApplicationContext容器了

**ApplicationContextAwareProcessor后置处理器**

```java
if (bean instanceof Aware) {
	if (bean instanceof EnvironmentAware) {
		((EnvironmentAware) bean).setEnvironment(this.applicationContext.getEnvironment());
	}
	if (bean instanceof EmbeddedValueResolverAware) {
		((EmbeddedValueResolverAware) bean).setEmbeddedValueResolver(this.embeddedValueResolver);
	}
	if (bean instanceof ResourceLoaderAware) {
		((ResourceLoaderAware) bean).setResourceLoader(this.applicationContext);
	}
	if (bean instanceof ApplicationEventPublisherAware) {
		((ApplicationEventPublisherAware) bean).setApplicationEventPublisher(this.applicationContext);
	}
	if (bean instanceof MessageSourceAware) {
		((MessageSourceAware) bean).setMessageSource(this.applicationContext);
	}
	//在这里注入
	if (bean instanceof ApplicationContextAware) {
		((ApplicationContextAware) bean).setApplicationContext(this.applicationContext);
	}
}
```



