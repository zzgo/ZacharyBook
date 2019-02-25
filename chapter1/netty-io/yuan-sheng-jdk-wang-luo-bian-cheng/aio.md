### 阐述

AIO是一种异步的I/O，异步I/O采用"订阅-通知"模式，即应用程序向操作系统注册IO监听，然后继续做自己的事情，当操作系统发生IO事件，并且准备好数据后，在主动通知应用程序，触发相应的函数

### Java jdk使用

java 从jdk1.7开始支持AIO核心类有**AsynchronousScoketChannel**（客户端）、**AsynchronousServerScoketChannel**（服务端）。

java AIO为TCP通信提供的异步Channel，AsynchronousServerSocketChannel创建成功后，类似于ServerSocket，也是调用了accept\(\)方法来接收来自客户端的连接，由于异步IO实际的IO操作是交给操作系统来做的，用户进程只负责通知操作系统进行IO和接受操作系统IO完成的通知，所以异步的ServerChannel调用accept\(\)方法后，当前线程不会阻塞，程序也不知道accept\(\)方法什么时候能够接收客户端请求并且操作系统完成网络IO，为了解决这个问题，AIO中accept方法时这样的：

```java
 public abstract <A> void accept(A attachment,
                                    CompletionHandler<AsynchronousSocketChannel,? super A> handler);
```

开始接受来自客户端请求，连接成功和或者失败都会触发CompletionHandler对象相应方法，其中AsynchronousSocketChannel实例，? super A 代表这个io操作上附加的数据类型。

而CompletionHandler接口定义了两个方法

```java
public interface CompletionHandler<V,A> {

    void completed(V result, A attachment);
   
    void failed(Throwable exc, A attachment);
}
```

completed方法：当前IO完成时触发该方法，该方法的第一个参数代表IO操作返回的对象，第二个参数代表发起IO操作时传入的附加参数

failed方法：当IO失败时触发该方法，第一个参数代表IO操作失败引发的异常或错误

AsynchronousSocketChannel的用法与Socket类似，有三个方法：

connect\(\)：用于连接到指定端口，指定IP地址的服务器

read\(\)、write\(\)：完成读写

![](/assets/89sajk262ko.png)

### 实战演练





