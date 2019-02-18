### 建立伪的Redis服务端

伪redis服务端

```
import java.io.IOException;
import java.net.ServerSocket;
import java.net.Socket;
//伪的redis  6379
public class SocketRedisServer {
    public static void main(String args[]){

        try {
            ServerSocket serverSocket = new ServerSocket(6379); //监听6379端口
            Socket rec = serverSocket.accept();//等待，接收信息
            byte[] result = new byte[2048];
            rec.getInputStream().read(result);
            System.out.println(new String(result));
        } catch (IOException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        }
    }
}
```

### 手写客户端

java redis 客户端

```
//自定义的jedis
public class JamesJedis {

    //set key value
    /* resp协议
     *  *3   # *3 -> *开头表示有3组

        $3   # $表示指令 3 表示长度
        SET

        $11  # $ 11 长度 11
        lisonLength

        $1   # $ 1 长度 1
        3
     * 
     */
    public static String set(Socket socket, String key, String value) throws IOException{
        StringBuffer str  = new StringBuffer();
        str.append("*3").append("\r\n");
        str.append("$3").append("\r\n");
        str.append("set").append("\r\n");
        //key
        str.append("$").append(key.getBytes().length).append("\r\n");
        str.append(key).append("\r\n");
        //value
        str.append("$").append(value.getBytes().length).append("\r\n");
        str.append(value).append("\r\n");

        //向redis发送resp
        socket.getOutputStream().write(str.toString().getBytes());
        byte[] response = new byte[2048];
        socket.getInputStream().read(response);

        return new String(response);
    }

    public static String get(Socket socket, String key) throws IOException{
        StringBuffer str  = new StringBuffer();
        str.append("*2").append("\r\n");
        str.append("$3").append("\r\n");
        str.append("get").append("\r\n");
        //key
        str.append("$").append(key.getBytes().length).append("\r\n");
        str.append(key).append("\r\n");


        //向redis发送resp
        socket.getOutputStream().write(str.toString().getBytes());
        byte[] response = new byte[2048];
        socket.getInputStream().read(response);

        return new String(response);
    }

    public static void main(String args[]) throws UnknownHostException, IOException{
        Socket socket = new Socket("192.168.111.128",6379);
        set(socket,"lisonHeight","183");
        System.out.println(get(socket, "lisonHeight"));
    }

}
```

启动服务端，然后启动客户端，服务端可以接收到信息

```
*3    # *3 代表3组
$3    # $3 代表长度为3
set   # set 长度3 
$11   # $11 下面这个字符长度为11
lisonHeight
$3    # $3 下面这个字符长度为3
183
```

**上面这段redis 客户端代码，是没有redis 密码认证的，如果redis是有密码，那么就不能操作成功**

**你可以在 windows 启动下一个没有密码 redis，然后这段代码就可以执行成功。**

**如果是连接一个有密码的redis在linux上启动的，那么这段代码就不能起到作用**

