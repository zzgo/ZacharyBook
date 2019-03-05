### 组件使用注解3

### @Autowired 自动装配

自动装配：Spring利用依赖注入（DI），完成对IOC容器中的各个组件的依赖关系赋值

新建三个类，分别建立在指定的包内，可看步骤2，这些所有Java类的对象扫描后都是保存在IOC容器管理的

**TestController.java**

```java
@Controller
public class TestController {
    @Autowired
    private TestService testService;
    ...
}
```

**TestDao.java**

```java
@Repository
public class TestDao {

}
```

**TestService.java**

```java
@Service
public class TestService {
    @Autowired
    private TestDao testDao;

    public void println() {
        System.out.println("service testDao is " + testDao);
    }
}
```

**Cap10MainConfig.java 扫描指定包文件**

```java
@Configurable
@ComponentScan({"com.zachary.springanno.cap10.dao", 
                "com.zachary.springanno.cap10.service", 
                "com.zachary.springanno.cap10.controller"})
public class Cap10MainConfig {

}
```

测试是否获取到同一个TestDao对象

```
 public static void main(String[] args) {
        //加载配置类
        AnnotationConfigApplicationContext anno = new AnnotationConfigApplicationContext(Cap10MainConfig.class);
        TestService testService = anno.getBean(TestService.class);
        testService.println();
        TestDao testDao = anno.getBean(TestDao.class);
        System.out.println("spring testDao bean  is " + testDao);
        anno.close();
 }
```

运行结果，表示为同一对象

```java
service testDao is com.zachary.springanno.cap10.dao.TestDao@16f7c8c1
spring testDao bean  is com.zachary.springanno.cap10.dao.TestDao@16f7c8c1
```

总结

@Autowired 表示默认优先按类型去容器中找对应的组件，相当于anno.getBean\(TestDao.class\)去容器获取id为testDao的bean，并注入到TestService的bean中

即

```java
@Service
public class TestService {
    @Autowired
    private TestDao testDao; //默认去容器中找id为“testDao”的bean
}
```

注意事项

如果容器中找到**多个testDao**，会加载哪个testDao呢?

在**Cap10MainConfig.java**加入以下代码，声明一个testDao2的bean

```java
@Configurable
@ComponentScan({"com.zachary.springanno.cap10.dao",
             "com.zachary.springanno.cap10.service",
              "com.zachary.springanno.cap10.controller"})
public class Cap10MainConfig {
    @Bean("testDao2")
    public TestDao testDao() {
        TestDao testDao = new TestDao();
        testDao.setFlag("2");
        return testDao;
    }
}
```

在TestDao中加入属性flag来区分

```java
@Repository
public class TestDao {
    private String flag = "1";

    public String getFlag() {
        return flag;
    }

    public void setFlag(String flag) {
        this.flag = flag;
    }

    @Override
    public String toString() {
        return "TestDao{" +
                "flag='" + flag + '\'' +
                '}';
    }
}
```

如何区分TestService是使用了（**@Reponstry 的testDao的flag=1**）的bean还是（**testDao2 的flag=2**）的bean？

测试如下

直接使用@Autowired，将testDao注入到TestService

```java
@Service
public class TestService {
    @Autowired
    private TestDao testDao; //bean id 为testDao,根据id默认去找@Respositry注解的testDao

    public void println() {
        System.out.println("service testDao is " + testDao);
    }
}
```

测试类：

```java
public static void main(String[] args) {
    //加载配置类
    AnnotationConfigApplicationContext anno = new AnnotationConfigApplicationContext(Cap10MainConfig.class);
    TestService testService = anno.getBean(TestService.class);
    testService.println();
    anno.close();
}
```

运行结果：

```
service testDao is TestDao{flag='1'}
```

如果一定要使用容器中的testDao2呢，怎么操作？

```java
    @Autowired
    private TestDao testDao2; //将bean id 指向testDao2即可
```

测试一下

```java
service testDao is TestDao{flag='2'}
```

虽然以上定义了private TestDao testDao2 ，但是还是想加载bean，id为testDao\(flag=1\)的bean，怎么办？此时可以使用@Autowired和@Qualifier结合来指定注入哪一个bean

```java
@Qualifier("testDao") //用Qualifier指定注解
@Autowired
private TestDao testDao2; //将bean id 指向testDao2即可
```

运行结果：

```java
service testDao is TestDao{flag='1'}
```

如果容器中没有任何一个testDao，会出现什么情况？

**去掉@Repositry和@Bean\("testDao2"\)**

再次运行，没有找到Qualifier所修饰的类

```java
No qualifying bean of type 'com.zachary.springanno.cap10.dao.TestDao' available:
```

因为@Autowired注解里的属性默认required=true.必须找到bean

解决方法：可以直接在@Autowired\(required=false\) 指定为非必须，当容器中无此bean，也不报错

```java
service testDao is null
```

### @Primary 注解

@primary注解指定bean如何加载呢？（将以上原注释的@Repository 和@Bean\("testDao2"\)恢复）

**Cap10MainConfig.java**

```java
@Configurable
@ComponentScan({"com.zachary.springanno.cap10.dao", 
        "com.zachary.springanno.cap10.service", 
        "com.zachary.springanno.cap10.controller"})
public class Cap10MainConfig {
    @Primary
    @Bean("testDao2")
    public TestDao testDao() {
        TestDao testDao = new TestDao();
        testDao.setFlag("2");
        return testDao;
    }
}
```

### 重要

为了验证@Qualifer与@Primary两注解的加载顺序，测试如下

当对于testDao在容器中同时存在多个时，且@Qualifier与@Primary注解同时存在，会发生什么呢？

测试：

```
public class Cap10MainTest {
    public static void main(String[] args) {
        AnnotationConfigApplicationContext anno = new AnnotationConfigApplicationContext(Cap10MainConfig.class);
        TestService testService = anno.getBean(TestService.class);
        testService.println();
        TestDao testDao = anno.getBean(TestDao.class);
        System.out.println("spring testDao bean  is " + testDao);
        anno.close();
    }
}
```

结果

```java
service testDao is TestDao{flag='1'}
spring testDao bean  is TestDao{flag='2'}
```

TestService打印的结果：使用了@Qualifier，表示直接到容器中找寻testDao的bean，flag=1

直接使用app.getBean\("TestDao"\),获取到的是@Primary注解声明的bean，flag=2

**说明一点：@Qualifier是根据bean。id执定获取的1testDao,不受@Primary影响**

那么@Primary的功能在那呢？

**改变TestService.java类**

```java
@Service
public class TestService {
//    @Qualifier("testDao") //去掉注解@Qualifier
    @Autowired(required = false)
    private TestDao testDao;//将testDao2改成testDao,用来测试是否会加载bean，id为testDao

    public void println() {
        System.out.println("service testDao is " + testDao);
    }
}
```

结果

```java
service testDao is TestDao{flag='2'}
spring testDao bean  is TestDao{flag='2'}
```

很明显都是注入@Primary指定的testDao

通过@Primary标记的bean，它的bean默认首选使用

### @Resource\(JSR250\)  @Inject\(JSR330\)

将Qualifier和Autowired注释掉（此时@primary还没有注释）

```java
@Service
public class TestService {
//    @Qualifier("testDao")
//    @Autowired(required = false)
    @Resource
    private TestDao testDao;

    public void println() {
        System.out.println("service testDao is " + testDao);
    }
}
```

运行结果：

```java
service testDao is TestDao{flag='1'}
spring testDao bean  is TestDao{flag='2'}
```

效果也是一样的，但它不会优先装配@Primary的bean



### 总结

@Resource 和Autowired一样可以装配bean

@Resource 缺点：不能支持@Primary功能，不支持@Autowired（required=false）的功能

当然也可以在TestService里按以下指定要注入的Bean

```java
@Service
public class TestService {
//    @Qualifier("testDao")
//    @Autowired(required = false)
    @Resource(name = "testDao2")//指定注入testDao2的bean，与@Qualifier类似，但不支持@primary和required=false功能
    private TestDao testDao;

    public void println() {
        System.out.println("service testDao is " + testDao);
    }
}
```

测试结果

```java
service testDao is TestDao{flag='2'}
spring testDao bean  is TestDao{flag='2'}
```

### @Inject 自动装配的使用

@Inject与@Autowired的区别如下

@Inject和@Autowired一样可以装配bean，并支持@Primary功能，可用于非spring框架

@Inject缺点：但不能支持@Autowired（required=false）的功能，需要引入第三方包javax.inject

操作步骤

pom.xml加入javax.inject

```java
<!--支持@Inject注解-->
        <dependency>
            <groupId>javax.inject</groupId>
            <artifactId>javax.inject</artifactId>
            <version>1</version>
        </dependency>
```

使用@Inject注解

```java
@Service
public class TestService {
    //    @Qualifier("testDao")
//    @Autowired(required = false)
//    @Resource(name = "testDao2")
    @Inject
    private TestDao testDao;

    public void println() {
        System.out.println("service testDao is " + testDao);
    }
}
```

结果：

```java
信息: JSR-330 'javax.inject.Inject' annotation found and supported for autowiring
service testDao is TestDao{flag='2'}
spring testDao bean  is TestDao{flag='2'}
```

autowiring--》支持Autowired

@Inject不支持required=false，但支持@Primary注解

```java
@Target({ElementType.METHOD, ElementType.CONSTRUCTOR, ElementType.FIELD})
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface Inject {
}
```

无任何属性。

### 最后建议

**Autowired属于spring的，不能脱离spring，@Resource和@Inject都是java规范，推荐使用spring的@Autowired**







