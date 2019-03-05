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



