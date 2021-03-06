### 阐述

服务端提供IP和监听端口，客户端通过连接操作向服务器监听的地址发起连接请求，通过三次握手，如果连接成功建立，双方就可以通过套接字进行通信。

传统的同步阻塞模型开发中，ServerSocket负责绑定IP地址，启动监听端口，Socket负责发起连接操作，连接成功后，双方通过输入和输出流进行同步阻塞式通信。

### 模型

采用BIO通信模型的服务端，通常由一个独立的Acceptor线程负责监听客户端的链接，它接收到客户端连接请求后为每个客户端创建一个新的线程进行处理，通过输出流返回应答给客户端，线程销毁，即典型的请求-应答模式。

### 缺点

该模型最大的问题就是缺乏弹性伸缩能力，当客户端并发访问量增加后，服务端的线程个数和客户端并发访问数呈1:1的正比关系，Java中的线程也是比较宝贵的系统资源，线程数量快速膨胀后，系统的性能将急剧下降，随着访问量的继续增大，系统最终就死掉了

### 改进

为了改进这种连接-线程的模型，我们可以使用线程池来管理这些线程，实现1个或多个线程处理N个客户端的模型，（但是其底层还是使用的同步阻塞I/O），通常被称为“伪异步I/O模型‘，我们知道，如果使用**CachedThreadPool**线程池，其实除了能自动帮我们管理线程（复用），看起来也就像是1:1客户端：线程数模型，而是用**FixedThreadPool**我们就有效的控制了线程的最大数量，保证了系统有限的资源的控制，实现了N:M的伪异步I/O模型，但是正因为限制了线程数量，如果发生读取数据较慢时（比如数据量大，网络传输慢等），大并发量的情况下，其他接入的信息，只能一直等待，这就是最大的弊端。

### 流程图

![](/assets/2190asjdkas.png)![](/assets/123478ashdjah.png)

![](/assets/21390udkfajd.png)

### 实战演练

BioServer.java

```java
public class BioServer {
    //开启一个线程池设置5个可用线程去处理这个socket连接，进行通信
    private static ExecutorService executorService = Executors.newFixedThreadPool(5);

    public static void main(String[] args) {
        start();
    }

    public static void start() {
        //声明一个ServerSocket
        ServerSocket server = null;
        try {
            server = new ServerSocket(DEFAULT_PORT);
            System.out.println("服务端已启动，默认端口：" + DEFAULT_PORT);
            while (true) {
                //等待客户端连接
                Socket socket = server.accept();
                System.out.println("有客户端连接了");
                //有客户端连接后，用线程池的一个线程去处理socket通信
                executorService.execute(new BioServerHandler(socket));
            }
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            if (server != null) {
                try {
                    server.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
    }
}
```

BioServerHandler.java

```java
public class BioServerHandler implements Runnable {
    private Socket socket;

    public BioServerHandler(Socket socket) {
        this.socket = socket;
    }


    @Override
    public void run() {
        //输入输出流处理
        try (
                //读取从客户端传来的信息
                BufferedReader in = new BufferedReader(new InputStreamReader(socket.getInputStream()));
                //向客户端推送响应的消息
                PrintWriter out = new PrintWriter(socket.getOutputStream(), true)
        ) {
            String message, result;
            while ((message = in.readLine()) != null) {
                System.out.println("Server accept message ：" + message);
                result = response(message);
                //推送信息
                out.println(result);
            }
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            close();
        }
    }

    private void close() {
        if (socket != null) {
            try {
                socket.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
            socket = null;
        }
    }
}
```

BioClient.java

```java
public class BioClient {
    public static void main(String[] args) {
        try {
            Socket socket = new Socket(DEFAULT_SERVER, DEFAULT_PORT);
            System.out.println("请输入内容：");
            new ReadMsg(socket).start();
            PrintWriter pw = null;
            while (true) {
                //向服务发送消息OutputStream
                pw = new PrintWriter(socket.getOutputStream());
                pw.println(new Scanner(System.in).next());
                pw.flush();
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    static class ReadMsg extends Thread {
        Socket socket;

        public ReadMsg(Socket socket) {
            this.socket = socket;
        }

        @Override
        public void run() {
            try (
                    //读取从服务端发来的信息
                    BufferedReader buffer = new BufferedReader(new InputStreamReader(socket.getInputStream()));
            ) {
                String line = null;
                while ((line = buffer.readLine()) != null) {
                    System.out.println(line);
                }
            } catch (IOException e) {
                System.out.println("断开连接");
                e.printStackTrace();
            } finally {
                if (socket != null) {
                    try {
                        socket.close();
                    } catch (IOException e) {
                        e.printStackTrace();
                    }
                }
            }

        }
    }
}
```



