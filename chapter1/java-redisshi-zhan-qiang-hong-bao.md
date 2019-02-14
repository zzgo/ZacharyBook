## Redis+Lua抢红包实战

#### 业务需求

* 将所有红包全部存储到Redis \( 红包池子的概念 \)
* 用户抢了多少红包, 记录红包被抢的详情信息;
* 用户只能抢一次红包, 不能重复抢红包
* 其它........

#### 抢红包实战预热

##### List队列：将N个红包放到List队列中，用来初始化红包池子

##### ![](/assets/loijhg3333.png)List类型：记录用户抢了多少钱

![](/assets/489891jdaskpiop.png)Hash类型：记录那些用户已抢过红包，防止重复抢

#### ![](/assets/sssggg666.png)业务流程

##### 初始化红包池

lpush hongBaoPool {id:rid\_1,money:9}... //将N个红包信息放入hongBaoPool中

##### Lua实现抢红包流程

#### ![](/assets/jjakao12389.png)Lua实现核心业务

##### Lua是什么？

Lua是一个小巧的脚本语言，有标准C编写而成，代码简洁优美，几乎在所有操作系统和平台上都可以编译，运行。

一个完整的Lua解释器不过200k，在目前所有脚本引擎中，Lua的速度是最快的

_Lua语言里的操作提供原子性_

##### Lua的使用

```
set name admin
eval "return redis.call('get',KEYS[1])" 1 name // 1个键，键名为name，返回admin
```

![](/assets/klsakai9012.png)

#### 代码实战

##### pom.xml

```
<dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>3.8.1</version>
            <scope>test</scope>
        </dependency>
        <!-- 单元测试相关依赖 -->
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-test</artifactId>
            <version>4.3.2.RELEASE</version>
            <scope>test</scope>
        </dependency>
        <!-- config jedis data and client jar -->
        <dependency>
            <groupId>org.springframework.data</groupId>
            <artifactId>spring-data-redis</artifactId>
            <version>1.7.1.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>redis.clients</groupId>
            <artifactId>jedis</artifactId>
            <version>2.7.2</version>
        </dependency>
        <dependency>
            <groupId>commons-lang</groupId>
            <artifactId>commons-lang</artifactId>
            <version>2.6</version>
        </dependency>
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>fastJson</artifactId>
            <version>1.2.17</version>
        </dependency>
```

##### Basic.java

```
public class Basic {
    //基本的redis配置信息
    public static String IP = "192.168.111.128";
    public static int PORT = 6379;
    public static String AUTH = "zhangqi";

    public static int hongBaoCount = 1000;//红包数量

    public static int threadCount = 20;//线程数量

    public static String hongBaoPoolKey = "hongBaoPollKey";//List类型来模拟红包池子

    public static String hongBaoDetailListKey = "hongBaoDetailListKey";//List来记录用户抢红包的详情

    public static String userIdRecordKey = "userIdRecordKey";//Hash类型，记录了那些用户抢了红包，防止重复抢红包


    /*
     * KEYS[1]:hongBaoPoolKey：                   //键hongBaoPool为List类型，模拟红包池子，用来从红包池抢红包
     * KEYS[2]:hongBaoDetailListKey：//键hongBaoDetailList为List类型，记录所有用户抢红包的详情
     * KEYS[3]:userIdRecordKey：           //键userIdRecord为Hash类型，记录所有已经抢过红包的用户ID
     * KEYS[4]:userid ：                              //模拟抢红包的用户ID
     *
     *
     * jedis.eval(  Basic.getHongBaoScript,   4,    Basic.hongBaoPoolKey,  Basic.hongBaoDetailListKey,    Basic.userIdRecordKey,  userid);
     *                      Lua脚本                                参数个数                  key[1]                     key[2]                       key[3]      key[4]
    */

    public static String hongBaoLuaScript =
            //使用 redis.call()来执行redis 命令
            //判断userIdRecordKey 里面的用户userIId 是否存在了，存在了的话，就不需要抢，直接返回nil（空）
            "if redis.call('hexists',KEYS[3],KEYS[4]) ~= 0 then \n" +
                    "return nil;\n" +
            "else\n" +
                    //从红包池中取出一个红包
                    "local hongBao = redis.call('rpop',KEYS[1])\n" +
                    "if hongBao then\n" +
                        //local关键字，声明局部变量，x理解成table（表）类型, "cjson是c的一个类，decode解码成{"rid_1":1,"money":9}形式
                        "local x = cjson.decode(hongBao)\n" +
                        "x['userId'] = KEYS[4]\n" +
                        //encode 加密成redis能认识的格式 ， re = {"rid_1":1,"money":9,"userId":001}
                        "local re = cjson.encode(x)\n" +
                        //hset userIdRecordKey userId 为 1 记录用户已经抢过红包了
                        "redis.call('hset',KEYS[3],KEYS[4],'1')\n" +
                        //lpush key value list 从左边添加一条记录记录用户抢红包的详情
                        "redis.call('lpush',KEYS[2],re)\n" +
                        "return re\n" +
                    "end\n" +
            "end\n" +
            "return nil;";

}
```

##### GetRedPack.java

```
public class GetRedPack {
    public void getHongBao() {
        final JedisUtil jedis = new JedisUtil(Basic.IP, Basic.PORT, Basic.AUTH);
        final CountDownLatch count = new CountDownLatch(Basic.threadCount);
        for (int i = 0; i < Basic.threadCount; i++) {
            new Thread() {
                @Override
                public void run() {
                    while (true) {
                        //模拟用户ID
                        String userId = UUID.randomUUID().toString();
                        Object object = jedis.eval(Basic.hongBaoLuaScript, 4, Basic.hongBaoPoolKey, Basic.hongBaoDetailListKey, Basic.userIdRecordKey, userId);
                        if (object != null) {
                            System.out.println("用户" + userId + "抢到红包，数量是：" + object);
                        } else {
                            if (jedis.llen(Basic.hongBaoPoolKey) == 0) {
                                break;
                            }
                        }
                    }
                    count.countDown();
                }
            }.start();
        }
        try {
            count.await();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}
```

##### InitRedPack.java

```
public class InitRedPack {

    /**
     * 初始化红包池子
     */
    public void init() {
        final JedisUtil jedis = new JedisUtil(Basic.IP, Basic.PORT, Basic.AUTH);
        jedis.flushall();  //清空,线上不要用.....
        final CountDownLatch count = new CountDownLatch(Basic.threadCount);
        for (int i = 0; i < Basic.threadCount; i++) {
            final int page = i;
            Thread thread = new Thread() {
                @Override
                public void run() {
                    int mCount = Basic.hongBaoCount / Basic.threadCount;
                    for (int j = page * mCount; j < (page + 1) * mCount; j++) {
                        JSONObject json = new JSONObject();
                        json.put("id", "rid_" + j);
                        json.put("money", j);
                        jedis.lpush(Basic.hongBaoPoolKey, json.toJSONString());
                    }

                    count.countDown();
                }
            };
            thread.start();
        }
        try {
            count.await();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}
```

##### JedisUtil.java

该类需要自行百度下载

##### App.java

```
public class App {
    public static void main(String[] args) {
        new InitRedPack().init();
        new GetRedPack().getHongBao();
    }
}
```



