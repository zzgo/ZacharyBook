### Servlet与SpringMvc那些事

#### 前面的话

以前写web的需要三大组件：servlet、filter、listener，都需要在web.xml中进行注册，包括springmvc的前端控制器DispactherServlet也需要在web.xml注册，现在可以通过**注解**的方式快速搭建我们的web应用

servlet3.0需要tomcat7以上版本进行支持

#### 项目搭建（不创建web.xml）

使用@WebServlet\("/url"\)来实现访问





