### Servlet与SpringMvc那些事

#### 前面的话

以前写web的需要三大组件：servlet、filter、listener，都需要在web.xml中进行注册，包括springmvc的前端控制器DispactherServlet也需要在web.xml注册，现在可以通过**注解**的方式快速搭建我们的web应用

servlet3.0需要tomcat7以上版本进行支持

#### 项目搭建（不创建web.xml）

使用@WebServlet\("/url"\)来实现访问

```java
@WebServlet("/test")
public class TestServlet extends HttpServlet {

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        resp.getWriter().write("test ... ");
    }
}
```

Shared libraries（共享库） and runtimes pluggability（运行时插件）的原理，在后面的框架中整理中，用的比较多。来分析下它；

**ServletContainerInitializer初始化web容器：**

在web容器启动时为提供给第三方组件会做一些初始化的工作，例如注册servlet或者filters等，servlet规范（**JSR356**）中通过**ServletContainerInitializer**实现此功能。

每个框架要使用ServletContainerInitializer就必须对应的jar包的**META-INF/services** 目录创建一个名为**javax.servlet.ServletContainerInitializer**的文件，文件内容指定**具体的ServletContainerInitializer实现类**，那么，当web容器启动时就会运行这个初始化器去做一些组件内的初始化工作。

创建这个文件

![](/assets/219873hdahajaj.png) 内容：com.enjoy.sevlet.JamesServletContainerInitializer

接下来我们只需要去**实现ServletContainerInitializer接口**

```java
import java.util.EnumSet;
import java.util.Set;

import javax.servlet.DispatcherType;
import javax.servlet.ServletContainerInitializer;
import javax.servlet.ServletContext;
import javax.servlet.ServletException;
import javax.servlet.annotation.HandlesTypes;

import com.enjoy.service.JamesService;

//容器启动的时候会将@HandlesTypes指定的这个类型下面的子类（实现类，子接口等）传递过来；
//传入感兴趣的类型；
@HandlesTypes(value = {JamesService.class})
public class JamesServletContainerInitializer implements ServletContainerInitializer {
    /**
     * tomcat启动时加载应用的时候，会运行onStartup方法；
     * <p>
     * Set<Class<?>> arg0：感兴趣的类型的所有子类型(对实现了JamesService接口相关的)；
     * ServletContext arg1:代表当前Web应用的ServletContext；一个Web应用一个ServletContext；
     * <p>
     * 1）、使用ServletContext注册Web组件（Servlet、Filter、Listener）
     * 2）、使用编码的方式，在项目启动的时候给ServletContext里面添加组件；
     * 必须在项目启动的时候来添加；
     * 1）、ServletContainerInitializer得到的ServletContext；
     * 2）、ServletContextListener得到的ServletContext；
     */
    @Override
    public void onStartup(Set<Class<?>> arg0, ServletContext arg1) throws ServletException {
        System.out.println("感兴趣的类型：");
        for (Class<?> claz : arg0) {
            System.out.println(claz);//当传进来后，可以根据自己需要利用反射来创建对象等操作
        }
        //注册servlet组件
        javax.servlet.ServletRegistration.Dynamic servlet = arg1.addServlet("orderServlet", new OrderServlet());
        //配置servlet的映射信息（路径请求）
        servlet.addMapping("/orderTest");

        //注册监听器Listener
        arg1.addListener(OrderListener.class);

        //注册Filter
        javax.servlet.FilterRegistration.Dynamic filter = arg1.addFilter("orderFilter", OrderFilter.class);
        //添加Filter的映射信息，可以指定专门来拦截哪个servlet
        filter.addMappingForUrlPatterns(EnumSet.of(DispatcherType.REQUEST), true, "/*");

    }

}
```

加载一些感兴趣的类

@HandlesTypes\(value = {JamesService.class}\)

会传入\(Set&lt;Class&lt;?&gt;&gt; arg0\)到实现该类的类和继承该类的接口

![](/assets/349873kjdfhdsjhfs.png)

![](/assets/jkdjfkafa8399324932.png)

这其实就是基于运行时插件的机制，启动并运行这个ServletContainerInitializer，在整合springmvc的时候会用到。

对于我们自己写的JamesServlet，我们可以使用@WebServlet注解来加入JamesServlet组件

但若是我们要导入第三方阿里的连接池或filter，以前的web.xml方式就可以通过配置加载就可以了，但现在我们使用ServletContext注入进来；

使用ServletContext来加入filter（过滤器）和listener（监听器）

新建OrderFilter.java过滤器

![](/assets/31041jhfjhppkpk.png)

新建OrderListener.java监听类

![](/assets/98381kdjfkajhfdkaj.png)

新建OrderServlet.java类

![](/assets/jdkasdoi39487294.png)

使用ServletContext来注册以上三个组件![](/assets/32974jfnjfdf.png)**注意在运行的过程中，是不可以注册组件，和IOC道理一样，出于安全考虑**



