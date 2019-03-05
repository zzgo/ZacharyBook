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

    public void println() {
        System.out.println(testService);
    }
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
    private TestDao testDao2;
    ....
}
```



