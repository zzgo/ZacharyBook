### ApplicationConextAwareProcessor 实现分析

spring执行后，会执行到后置处理器

```java
exposedObject = initializeBean(beanName, exposedObject, mbd);
->
wrappedBean = applyBeanPostProcessorsBeforeInitialization(wrappedBean, beanName);
->
一个一个去遍历 所有的 BeanPostProcessor 
```

ApllicationContextAware  类

```java
ApplicationContextAwareProcessor implements BeanPostProcessor
```

这个类做了什么事呢？

```java
	@Override
	@Nullable
	public Object postProcessBeforeInitialization(final Object bean, String beanName) throws BeansException {
		AccessControlContext acc = null;

		if (System.getSecurityManager() != null &&
				(bean instanceof EnvironmentAware || bean instanceof EmbeddedValueResolverAware ||
						bean instanceof ResourceLoaderAware || bean instanceof ApplicationEventPublisherAware ||
						bean instanceof MessageSourceAware || bean instanceof ApplicationContextAware)) {
			acc = this.applicationContext.getBeanFactory().getAccessControlContext();
		}

		if (acc != null) {
			AccessController.doPrivileged((PrivilegedAction<Object>) () -> {
				invokeAwareInterfaces(bean);
				return null;
			}, acc);
		}
		else {
			invokeAwareInterfaces(bean);
		}

		return bean;
	}

	private void invokeAwareInterfaces(Object bean) {
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
			if (bean instanceof ApplicationContextAware) {
				((ApplicationContextAware) bean).setApplicationContext(this.applicationContext);
			}
		}
	}
```

在类进行初始化前，做一些注入工作，获取对象，比如implements ApplicationContextAware  就可以获取到 applicationContext 上下文



### BeanValidationPostProcess 类



### InitDestroyAnnotationBeanPostProcessor类

此处理器用来处理@PostConstruct, @PreDestroy, 怎么知道这两注解是前后开始调用的呢

就是使用InitDestroyAnnotationBeanPostProcessor



