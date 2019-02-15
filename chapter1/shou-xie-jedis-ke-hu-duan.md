### 建立伪的Redis服务端

redis 服务端

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

java redis 客户端

```
//自定义的jedis
public class JamesJedis {

    //set key value
    /*
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



