全局异常

界面上出现这500，404等错误，这对用户来说还是不友好。一般在企业里面对这些异常一般都会统一捕获，由一个专门的异常处理类来统一处理。

### 异常捕获

#### @ControllerAdvice 注解，@ExceptionHandler注解

创建一个GlobalExceptionHandler.java

```java
@ControllerAdvice
public class GlobalExceptionHandler {
    //RuntimeException异常捕获
    @ExceptionHandler(value = RuntimeException.class)
    @ResponseBody
    public Object defaultErrorHandler(HttpServletRequest req, Exception e) throws Exception {
        e.printStackTrace();
        return "我是个异常处理类";
    }
}
```

### 404页面处理

在浏览器上故意输错地址：localhost/batchAddx?username=enjoy&passwd=123，后端并没有这服务，虽然已经做了相关的异常捕获，但浏览器还是显示了；这个时候就要做404（其他异常代码一样）；在配置这样错误页面的时候，以前是在WEB.XML中进行配置，而在这里，需要有个WebServerFactoryCustomizer的实例进行配置。

在 GlobalExceptionHandler.java 新增方法

```java
@Bean
public WebServerFactoryCustomizer<ConfigurableWebServerFactory> webServerFactoryCustomizer() {
    //lambdas表达式
    return (factory -> {
        //path = 404.do 对应BaseController一个请求
        ErrorPage error404Page = new ErrorPage(HttpStatus.NOT_FOUND, "/404.do");
        factory.addErrorPages(error404Page);
    });
}
```

新增BaseController.java

```java
@RestController
public class BaseController {
    //这个路径与全局遗产配置的404.do一致
    @RequestMapping("/404.do")
    public Object error_404() {
        return "你要找的页面，被偷吃了！";
    }

}
```



