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

#### server端

AioServerHandler.java

```java
public class AioServerHandler implements Runnable {
    public CountDownLatch count;
    public AsynchronousServerSocketChannel channel;

    public AioServerHandler(int port) {
        try {
            channel = AsynchronousServerSocketChannel.open();
            //注册监听事件
            channel.bind(new InetSocketAddress(port));
            System.out.println("server is start，port ：" + port);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    @Override
    public  void run() {
        count = new CountDownLatch(1);
        channel.accept(this, new AioAcceptHandler());
        try {
            //使线程阻塞在这里，不会结束
            count.await();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}
```

AioAcceptHandler.java

```go
public class AioAcceptHandler implements CompletionHandler<AsynchronousSocketChannel, AioServerHandler> {
    @Override
    public void completed(AsynchronousSocketChannel channel, AioServerHandler serverHandler) {
        serverHandler.channel.accept(serverHandler, this);
        ByteBuffer buffer = ByteBuffer.allocate(1024);
        channel.read(buffer,buffer,new AioReadHandler(channel));
    }

    @Override
    public void failed(Throwable exc, AioServerHandler serverHandler) {
        exc.printStackTrace();
        serverHandler.count.countDown();
    }
}
```

AioReadHandler.java

```java
public class AioReadHandler implements CompletionHandler<Integer, ByteBuffer> {
    AsynchronousSocketChannel channel;

    public AioReadHandler(AsynchronousSocketChannel channel) {
        this.channel = channel;
    }

    @Override
    public void completed(Integer result, ByteBuffer byteBuffer) {
        if (result == -1) {
            try {
                channel.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
            return;
        }
        byteBuffer.flip();
        byte[] b = new byte[byteBuffer.remaining()];
        byteBuffer.get(b);
        System.out.println(result);
        try {
            String msg = new String(b, "UTF-8");
            System.out.println(msg);
            String res = response(msg);
            doWrite(res);
        } catch (UnsupportedEncodingException e) {
            e.printStackTrace();
        }


    }

    private void doWrite(String res) {
        byte[] b = res.getBytes();
        final ByteBuffer buffer = ByteBuffer.allocate(b.length);
        buffer.put(b);
        buffer.flip();
        channel.write(buffer, buffer, new CompletionHandler<Integer, ByteBuffer>() {
            @Override
            public void completed(Integer result, ByteBuffer attachment) {
                if (attachment.hasRemaining()) {
                    channel.write(attachment, attachment, this);
                } else {
                    //读取客户端传回的数据
                    ByteBuffer readBuffer = ByteBuffer.allocate(1024);
                    //异步读数据
                    channel.read(readBuffer, readBuffer, new AioReadHandler(channel));
                }
            }

            @Override
            public void failed(Throwable exc, ByteBuffer attachment) {
                try {
                    channel.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        });

    }

    @Override
    public void failed(Throwable exc, ByteBuffer byteBuffer) {
        try {
            channel.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}

```

测试AioServer.java

```java
public class AioServer {
    public static void main(String[] args) {
        new Thread(new AioServerHandler(DEFAULT_PORT)).start();
    }
}
```

#### Client端

AioClientHandler.java

```java
public class AioClientHandler implements CompletionHandler<Void, AioClientHandler>, Runnable {
    public CountDownLatch count;
    public AsynchronousSocketChannel channel;
    private String ip;
    private int port;

    public AioClientHandler(String ip, int port) {
        this.ip = ip;
        this.port = port;
        try {
            channel = AsynchronousSocketChannel.open();
        } catch (IOException e) {
            e.printStackTrace();
        }

    }

    @Override
    public void run() {
        count = new CountDownLatch(1);
        channel.connect(new InetSocketAddress(ip, port), null, this);

        try {
            count.await();
            channel.close();
        } catch (IOException e) {
            e.printStackTrace();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }

    @Override
    public void completed(Void result, AioClientHandler attachment) {
        System.out.println("连接成功...");
    }

    @Override
    public void failed(Throwable exc, AioClientHandler attachment) {
        System.out.println("链接失败...");
        exc.printStackTrace();
        count.countDown();
        try {
            channel.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    public void sendMessage(String msg) {
        byte[] b = msg.getBytes();
        ByteBuffer buffer = ByteBuffer.allocate(b.length);
        buffer.put(b);
        buffer.flip();//整理一下开始start-end
        channel.write(buffer, buffer, new AioClientWriteHandler(channel, count));
    }

}
```

AioClientWriteHandler.java

```java
public class AioClientWriteHandler implements CompletionHandler<Integer, ByteBuffer> {
    AsynchronousSocketChannel channel;
    CountDownLatch count;

    public AioClientWriteHandler(AsynchronousSocketChannel channel, CountDownLatch count) {
        this.channel = channel;
        this.count = count;
    }

    @Override
    public void completed(Integer result, ByteBuffer attachment) {
        if (attachment.hasRemaining()) {
            channel.write(attachment, attachment, this);
        } else {
            ByteBuffer buffer = ByteBuffer.allocate(1024);
            channel.read(buffer, buffer, new AioClientReadHandler(channel, count));
        }

    }

    @Override
    public void failed(Throwable exc, ByteBuffer attachment) {
        exc.printStackTrace();
        count.countDown();
        try {
            channel.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

AioClientReadHandler.java

```java
public class AioClientReadHandler implements CompletionHandler<Integer, ByteBuffer> {
    AsynchronousSocketChannel channel;
    CountDownLatch count;

    public AioClientReadHandler(AsynchronousSocketChannel channel, CountDownLatch count) {
        this.channel = channel;
        this.count = count;
    }

    @Override
    public void completed(Integer result, ByteBuffer attachment) {
        attachment.flip();
        byte[] bytes = new byte[attachment.remaining()];
        attachment.get(bytes);
        String msg;
        try {
            msg = new String(bytes, "UTF-8");
            System.out.println(msg);
        } catch (UnsupportedEncodingException e) {
            e.printStackTrace();
        }
    }

    @Override
    public void failed(Throwable exc, ByteBuffer attachment) {
        exc.printStackTrace();
        try {
            channel.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
        count.countDown();
    }
}
```

测试AioClient.java

```java
public class AioClient {
    static AioClientHandler clientHandler;

    public static void start() {
        clientHandler = new AioClientHandler(DEFAULT_SERVER, DEFAULT_PORT);
        new Thread(clientHandler).start();
    }

    public static boolean sendMsg(String msg) {
        clientHandler.sendMessage(msg);
        return true;
    }

    public static void main(String[] args) {
        AioClient.start();
        System.out.println("请输入内容：");
        Scanner scanner = new Scanner(System.in);
        while (AioClient.sendMsg(scanner.nextLine())) ;
    }
}
```



