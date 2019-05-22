## Spring里面的设计模式有哪些？

#### 简单工厂

又叫静态工厂方法

#### 工厂方法

反射

#### 单例模式

spring下默认的bean均为singleton，可通过singleton=true,false或者scope=?指定

#### 适配器

在spring Aop中，使用Advice（通知）来增强被代理的功能，面向切面编程

#### 包装器

需要连接多数据源，spring提供org.springframework.jndiObjectFactoryBean，然后sessionFactory根据客户请求，将dataSource属性设置成不同的数据源，以达到切换数据源的目的。

spring中的包装器模式在类名上有两种表现：一种是类名含有Wrapper，另一种是类名含有Decorator,基本上都是动态地给一个对象添加额外的职责

#### 代理（proxy）

spring的proxy模式在aop中有体现，比如jdkDynamicAopProxy和Cglib2AopProxy

#### 观察者（Observer）

定义对象键的一种一对多的依赖关系，当一个对象的状态发生改变时，所有依赖于它的对象都得到通知并被告知自动更新

spring里面的observer模式常用是 listener的实现，ApplicationListener

#### 策略模式（strategy）

定义一系列算法

#### 模板方法模式

jdbcTemplate execute

redisTemplate

....

