### AOP源码透析

AOP原理：看给容器中注册了什么组件，这个组件什么时候工作，这个组件的功能是什么？

### @EnableAspectJAutoProxy

核心从这个注解开始入手，AOP整个功能都要启作用，就是靠这个注解，加入它才有AOP

所以_**跟进@EnableAspectJAutoProxy源码**_

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Import(AspectJAutoProxyRegistrar.class) //导入了此类，跟进去看看
public @interface EnableAspectJAutoProxy {
	//proxytargetClass属性，默认false，采用JDK动态代理织入增强（实现接口的方式）
	//如果设置为true，则采用CGLIB动态代理织入增强
	boolean proxyTargetClass() default false;
	//通过aop框架暴露该代理对象，aopContext能够访问
	boolean exposeProxy() default false;
}
```

**AspectJAutoProxyRegistrar.java**

```java
class AspectJAutoProxyRegistrar implements ImportBeanDefinitionRegistrar {

	@Override
	public void registerBeanDefinitions(
			AnnotationMetadata importingClassMetadata, BeanDefinitionRegistry registry) {

		AopConfigUtils.registerAspectJAnnotationAutoProxyCreatorIfNecessary(registry);

		AnnotationAttributes enableAspectJAutoProxy =
				AnnotationConfigUtils.attributesFor(importingClassMetadata, EnableAspectJAutoProxy.class);
		if (enableAspectJAutoProxy != null) {
			if (enableAspectJAutoProxy.getBoolean("proxyTargetClass")) {
				AopConfigUtils.forceAutoProxyCreatorToUseClassProxying(registry);
			}
			if (enableAspectJAutoProxy.getBoolean("exposeProxy")) {
				AopConfigUtils.forceAutoProxyCreatorToExposeProxy(registry);
			}
		}
	}

}
```

**ImportBeanDefinitionRegistrar **接口作用：能给容器中自定义注册组件，比如我们是使用过的

```java
public class CustomImportBeanDefinitionRegistrar implements ImportBeanDefinitionRegistrar {

    //    AnnotationMetadata:当前类的注解信息
//	BeanDefinitionRegistry:BeanDefinition注册类
//    把所有需要的添加到容器中的bean
//    调用BeanDefinitionRegistry.registerBeanDefinition自定义手工注册进来
    @Override
    public void registerBeanDefinitions(AnnotationMetadata annotationMetadata, BeanDefinitionRegistry beanDefinitionRegistry) {
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

```java
@Import({Dog.class, Cat.class, CustomImportSelector.class, CustomImportBeanDefinitionRegistrar.class})
```

再注入我们自定的名称和类的时候

在 **AspectJAutoProxyRegistrar **里可以自定义注册一些bean

那么注册了什么bean呢？以debug模式进行测试一下







