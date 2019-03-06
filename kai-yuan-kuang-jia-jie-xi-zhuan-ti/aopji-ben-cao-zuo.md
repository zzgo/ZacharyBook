### AOP操作

#### 概念

AOP：面向切面编程，底层就是动态代理

指程序在运行期间动态的将某段代码切入到指定方法位置运行的编程方式

### 测试

**在pom.xml导入spring-aspects依赖包**

```java
<!-- AOP 编程必须 -->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-aspects</artifactId>
    <version>5.0.6.RELEASE</version>
</dependency>
```

新建一个业务逻辑类

**Calculator.java**

```java
public class Calculator {
    //业务逻辑方法
    public int div(int i, int j){
        System.out.println("----div方法内开始执行----");
        return i/j;
    }
}
```

现在有这样一个需求：在div方法运行之前，记录一下日志，运行后也记录一下，运行出异常，也打印一下

你可能会这么做，在每个方法加入打印，就是上面代码写的那样。

但是这样就会出现问题，这种方式耦合了。

**解决方案1：采用AOP面向切面编程**

新建一个日志切面类

**LogAspects.java**

```java
//日志切面类
public class LogAspects {
    public void logStart(){
        System.out.println("除法运行......参数列表是:{}");
    }
    public void logEnd(){
        System.out.println("除法结束.....");
    }
    public void logReturn(){
        System.out.println("除法正常返回.....运行结果是：{}");
    }
    public void logException(){
        System.out.println("除法异常......异常信息是:{}");
    }
}
```

**说明：**

日志切面类的方法需要动态感知到div\(\)运行到哪里了，然后在执行，如果除法开始，日志开始方法，也叫通知方法，分为以下几种

前置通知：logStart\(\)，在目标方法div运行之前运行，@Before

后置通知：logEnd\(\)，在目标方法div运行结束之后运行，无论正常或异常结束，@After

返回通知：logReturn\(\)，在目标方法div正常放回之后运行，@AfterReturning

异常通知：logException\(\)，在目标方法div出现异常后运行@AfterThrowing

环绕通知：以上没写动态代理，手动执行目标方法运行joinPoint.procced\(\)，最底层通知

手动指定执行目标方法@Around，执行之前想当于前置通知，执行之后相当于返回后置通知

其实就是通过反射执行目标对象的连接点处的方法

**给日志切面类LogAspect的方法标注何时运行（即通知注解）**

加上对应的注解

```java
//日志切面类
public class LogAspects {
    @Before("execution(public int com.zachary.springanno.cap12.aop.Calculator.div(int,int))")
    public void logStart() {
        System.out.println("除法运行......参数列表是:{}");
    }

    @After("execution(public int com.zachary.springanno.cap12.aop.Calculator.div(int,int))")
    public void logEnd() {
        System.out.println("除法结束.....");
    }

    @AfterReturning("execution(public int com.zachary.springanno.cap12.aop.Calculator.div(int,int))")
    public void logReturn() {
        System.out.println("除法正常返回.....运行结果是：{}");
    }

    @AfterThrowing("execution(public int com.zachary.springanno.cap12.aop.Calculator.div(int,int))")
    public void logException() {
        System.out.println("除法异常......异常信息是:{}");
    }
}
```

如不想区分切入了那个方法以及参数类型和个数，可以有如下指定方式

```java
//日志切面类
public class LogAspects {
    // *(..) 不区分是哪一个方法*，任意对个参数及类型加 ".."
    @Before("execution(public int com.zachary.springanno.cap12.aop.Calculator.*(..))")
    public void logStart() {
        System.out.println("除法运行......参数列表是:{}");
    }
    ....
}
```

不难发现问题：注解里的内容是冗余重复的，公共的代码应该抽出来

并加上Around环绕通知

**即形成：**

```java
@Aspect //告诉spring这是一个切面类
public class LogAspects {
    //公共切点
    @Pointcut("execution(public int com.zachary.springanno.cap12.aop.Calculator.*(..))")
    public void pointCut() {

    }

    // *(..) 不区分是哪一个方法*，任意对个参数及类型加 ".."
    @Before("pointCut()") //公共部分抽离出来一个方法
    public void logStart() {
        System.out.println("除法运行......参数列表是:{}");
    }

    @After("pointCut()")
    public void logEnd() {
        System.out.println("除法结束.....");
    }

    @AfterReturning("pointCut()")
    public void logReturn() {
        System.out.println("除法正常返回.....运行结果是：{}");
    }

    @AfterThrowing("pointCut()")
    public void logException() {
        System.out.println("除法异常......异常信息是:{}");
    }

    @Around("pointCut()") //环绕通知
    public Object Around(ProceedingJoinPoint proceedingJoinPoint) throws Throwable {
        System.out.println("@Around：执行目标方法之前...");
        Object obj = proceedingJoinPoint.proceed();
        System.out.println("@Around：执行目标方法之后...");
        return obj;
    }
}
```

抽离出来公共的部分，如果是其他包下切面类或者其他类需要这个共同的切点切入的话。就在注解里面表明 ，**声明全路径**

```java
@After("com.zachary.springanno.cap12.aop.LogAspects.pointCut()") //声明全路径
public void logEnd() {
    System.out.println("除法结束.....");
}
```

将这业务类和切面类注入到Spring IOC容器中，@Bean加入进去，或者扫描进去

**最后一定要注意，加上@EnableAspectJAutoProxy 表示开启注解AOP模式，**

注意在Spring以后有很多@EnableXXX，表示开启某项功能，取代XML配置

```java
@Configurable
@EnableAspectJAutoProxy
public class Cap12MainConfig {
    @Bean
    public Calculator calculator() {
        return new Calculator();
    }

    @Bean
    public LogAspects logAspects() {
        return new LogAspects();
    }
}
```

注意：**AOP切面编程，是基于Spring 容器实现的，所以我们使用的Bean对象需要有Spring 容器提供，才能切入成功**

测试类：

```java
public class Cap12MainTest {
    public static void main(String[] args) {
        AnnotationConfigApplicationContext app = new AnnotationConfigApplicationContext(Cap12MainConfig.class);
        System.out.println("===========普通自己New方式===============");
        // 普通自己New方式
        Calculator c1 = new Calculator();
        int result1 = c1.div(4, 3);
        System.out.println(result1);
        System.out.println("===========Spring ioc 容器管理方式===============");
        //Spring ioc 容器管理方式
        Calculator c2 = app.getBean(Calculator.class);
        int result2 = c2.div(4, 3);
        System.out.println(result2);
        app.close();

    }
}
```

结果：

```java
===========普通自己New方式===============
----div方法内开始执行----
1
===========Spring ioc 容器管理方式===============
@Around：执行目标方法之前...
除法运行......参数列表是:{}
----div方法内开始执行----
@Around：执行目标方法之后...
除法结束.....
除法正常返回.....运行结果是：{}
1
```

AOP的操作，当然这里还没有完，**在AOP切面 编程的中，我们可以获取许多东西，比如ProceedingJoinPoint 参数**

使用JoinPoint可以拿到相关内容，比如方法名...参数等

```java
@Aspect
public class LogAspects {
    @Pointcut("execution(public int com.zachary.springanno.cap12.aop.Calculator.*(..))")
    public void pointCut() {

    }
    // *(..) 不区分是哪一个方法*，任意对个参数及类型加 ".."
    @Before("pointCut()")
    public void logStart(JoinPoint joinPoint) {
        System.out.println(joinPoint.getSignature().getName() + "，除法运行......参数列表是:{" + joinPoint.getArgs() + "}");
    }

    @After("com.zachary.springanno.cap12.aop.LogAspects.pointCut()")
    public void logEnd(JoinPoint joinPoint) {
        System.out.println(joinPoint.getSignature().getName() + "，除法结束.....");
    }

    @AfterReturning("pointCut()")
    public void logReturn() {
        System.out.println("除法正常返回.....运行结果是：{}");
    }

    @AfterThrowing("pointCut()")
    public void logException() {
        System.out.println("除法异常......异常信息是:{}");
    }

    @Around("pointCut()")
    public Object Around(ProceedingJoinPoint proceedingJoinPoint) throws Throwable {
        System.out.println("@Around：执行目标方法之前...");
        Object obj = proceedingJoinPoint.proceed();
        System.out.println("@Around：执行目标方法之后...");
        return obj;
    }
}
```



