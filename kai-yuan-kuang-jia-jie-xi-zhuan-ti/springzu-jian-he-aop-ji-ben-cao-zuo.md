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



