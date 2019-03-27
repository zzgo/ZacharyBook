## 全局异常

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





