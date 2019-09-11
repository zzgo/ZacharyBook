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



