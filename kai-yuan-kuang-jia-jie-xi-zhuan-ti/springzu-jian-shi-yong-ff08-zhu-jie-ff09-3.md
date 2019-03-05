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

**CapMainConfig.java 扫描指定包文件**

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



