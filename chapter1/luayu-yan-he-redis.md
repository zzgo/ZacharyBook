### LUA语言与Redis

LUA脚本语言是C开发的，类似存储过程

使用脚本的好处：

* 减少网络开销：本来5次网络请求的操作，可以用一个请求完成，原先5次请求的逻辑放在redis服务器上完成，使用脚本，减少网络往返时延
* 原子操作：Redis会将整个脚本作为一个整体执行，中间不会被其他命令插入
* 复用性：客户端发送的脚本会永久存储在Redis中，意味着其他客户端可以复用这一脚本而不需要使用代码完成同样的逻辑

#### LUA基本语法

```
127.0.0.1:6379> eval script numkeys key [key ...] arg [arg ...]  
# script表示脚本，numkeys 后面有几个key，key[...] arg [arg ...] 跟的参数
```

比如：

```
127.0.0.1:6379> set name test
OK
127.0.0.1:6379> eval "return redis.call('get',KEYS[1])" 1 name test
"test"

#KEYS[1]=name 表示第一个key KEYS[2] 表示第二个key KEYS[index] index从1开始
实际上执行了 get name > test
```

##### 案例1：

local lua的关键字，声明一个变量，

while 条件 do

执行内容

end

结束

print\(\) 打印内容

```
新建一个1.lua，内容
local int sum = 0
local int i = 0
while i < 100 do 
    sum = sum + i
    i = i + 1
end
print(sum)
```

##### 案列2：

tables 声明一个表，类似于数组形式

for i = start,end do   索引start-end次

执行逻辑

end

\#myArray 计算长度

if 条件 then

执行逻辑

else

执行逻辑

end

这里的end是一种标准，标志着结束

```lua
local tables myArray = {"james","java",false,34} 
local int sum = 0
print(myArray[3])//返回false
for i = 1,100 do
    sum = sum +1
end
print(sum)//从1到100，执行100次，每次加1,打印100
for j = 1,#myArray do
    print(myArray[j])
    if(myArray[j]=="james" then
        print("true")
        break
    else
        print("false")
    end
end
```

##### 实现访问频率限制

描述：实现访问者$ip 127.0.0.1 在一定的时间 $time 20s内只能访问 $limit 10 次，使用JAVA语言实现

java客户端操作redis代码如下：

```java
public class IpLimit {
    private static JedisUtil jedis = new JedisUtil("192.168.111.128", 6379, "zhangqi");

    public static boolean limit(String ip, int limit, int time) {
        boolean result = true;
        //判断是否是第一次访问
        String key = "rate:limit:" + ip;
        if (jedis.exists(key)) {
            //看一看是否超出了限制
            long afterValue = jedis.incr(key);
            if (afterValue > limit) {
                result = false;
            }
        } else {
            jedis.incr(key);
            jedis.expire(key, time);
        }
        return result;
    }


    public static void main(String[] args) {
        for (int i = 0; i < 1000; i++) {
            new Thread() {
                @Override
                public void run() {
                    boolean bool = limit("192.168.111.128", 50, 10);
                    if (bool == true) {
                        System.out.println("==============================" + true);
                    } else {
                        System.out.println(false);
                    }
                }
            }.start();
            try {
                Thread.sleep(50);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }

    }
}
```

以上代码会出现竞态：解决方法是用WATCH监控 rate.limit.$IP的变动，但较为麻烦

以上代码在不使用pipeline的情况下，最多需要向Redis请求5条指令，传输过多

使用Lua脚本来处理，保证了原子性

```lua
local key = KEYS[1]
local limit = tonumber(ARGV[1])
local exppire_time = ARGV[2]
local is_exists = redis.call("EXISTS",key)
if is_exists == 1 then
    if redis.call("INCR",key) > limit then
        return 0
    else
        return 1
    end
else
    redis.call("SET",key,1)
    redis.call("EXPIRE",key,expire_time)
    return 1
end
```

##### LUA执行逻辑

![](/assets/3489jhfjask.png)

语法格式 EVAL script numkeys key \[key ...\] arg \[arg ...\]

```
              &lt;1&gt; script：     你的lua脚本

              &lt;2&gt; numkeys:  key的个数

              &lt;3&gt; key:         redis中各种数据结构的替代符号

              &lt;4&gt; arg:         你的自定义参数
```

比如：KEYS表示key 下标从1开始，ARGV为参数，下标也为1开始。

```
127.0.0.1:6379> eval " return {KEYS[1],KEYS[2],ARGV[1],ARGV[2]} " 2 k1 k2 v1 v2
1) "k1"
2) "k2"
3) "v1"
4) "v2"
```

eval "return {KEYS\[1\],KEYS\[2\],ARGV\[1\],ARGV\[2\]}" 2 key1 key2 james 20

##### Redis对Lua脚本的管理

将Lua脚本加入到redis中

脚本random.lua

```lua
for i=1,ARGV[1] do
    redis.call("lpush",KEYS[1],math.random())
end
return true
```

加入到redis中 $\(cat ../works/random.lua\) cat 打开脚本，load 加载脚本

```
[root@localhost src]# ./redis-cli -a zhangqi script load "$(cat ../works/random.lua)"
"a7260ba6baef0c2f78d64b2fa777647741feeb60"
```

测试 evalsha 测试

```
127.0.0.1:6379> evalsha a7260ba6baef0c2f78d64b2fa777647741feeb60 1 testlua 1
(integer) 1
127.0.0.1:6379> lindex testlua 0
"0.1708280361121651"
```

检查脚本是否加载成功 script exists 查看是否存在

```
127.0.0.1:6379> script exists a7260ba6baef0c2f78d64b2fa777647741feeb60
1) (integer) 1
```

清空lua脚本内容 script flush

```
127.0.0.1:6379> script flush
OK
```

杀掉正在执行的lua脚本 script kill

```
127.0.0.1:6379> script kill
(error) NOTBUSY No scripts in execution right now.
```

#### 实战演练

IP限流控制

编写ipcount.lua脚本

```lua
local is_exist = redis.call('exists',KEYS[1])
if is_exist == 1  then
  local afterCount = redis.call('incr',KEYS[1])
  if afterCount > tonumber(ARGV[1]) then
     return 0
  else
     return 1
  end
else
   redis.call('set',KEYS[1],1)
   redis.call('expire',KEYS[1],ARGV[2])
   return 1
end
```

生成sha值

```
[root@localhost src]# ./redis-cli -a zhangqi script load "$(cat ../works/ipcount.lua)"
"693de244e76774f79066a4598702f8227979523a"
```

执行结果

```lua
127.0.0.1:6379> evalsha 693de244e76774f79066a4598702f8227979523a 1 192.168.111.128 5 5
(integer) 1
127.0.0.1:6379> evalsha 693de244e76774f79066a4598702f8227979523a 1 192.168.111.128 5 5
(integer) 1
127.0.0.1:6379> evalsha 693de244e76774f79066a4598702f8227979523a 1 192.168.111.128 5 5
(integer) 1
127.0.0.1:6379> evalsha 693de244e76774f79066a4598702f8227979523a 1 192.168.111.128 5 5
(integer) 1
127.0.0.1:6379> evalsha 693de244e76774f79066a4598702f8227979523a 1 192.168.111.128 5 5
(integer) 1
127.0.0.1:6379> evalsha 693de244e76774f79066a4598702f8227979523a 1 192.168.111.128 5 5
(integer) 0
127.0.0.1:6379> evalsha 693de244e76774f79066a4598702f8227979523a 1 192.168.111.128 5 5
(integer) 0
127.0.0.1:6379> evalsha 693de244e76774f79066a4598702f8227979523a 1 192.168.111.128 5 5
(integer) 0
127.0.0.1:6379> evalsha 693de244e76774f79066a4598702f8227979523a 1 192.168.111.128 5 5
(integer) 0
127.0.0.1:6379> evalsha 693de244e76774f79066a4598702f8227979523a 1 192.168.111.128 5 5
(integer) 0
127.0.0.1:6379> evalsha 693de244e76774f79066a4598702f8227979523a 1 192.168.111.128 5 5
(integer) 0
127.0.0.1:6379> evalsha 693de244e76774f79066a4598702f8227979523a 1 192.168.111.128 5 5
(integer) 0

```

抢红包，实战改写用sha值实现

脚本加入到redis中去

```lua
if redis.call('hexists',KEYS[3],KEYS[4]) ~= 0 then
   return nil
else
   local hongbao = redis.call('rpop',KEYS[1])
   if hongbao then
      local x = cjson.decode(hongbao)
      x['userId'] = KEYS[4]
      local re = cjson.encode(x)
      redis.call('hset',KEYS[3],KEY[4],'1')
      redis.call('lpush',KEYS[2],re)
      return re
   end
end
return nil
```

生成sha值

```
[root@localhost src]# ./redis-cli -a zhangqi script load "$(cat ../works/hongbao.lua)"
"66ad62496cc639672db4ca2852addcdb18c8b495"
```

Java客户端来调用

```java
public static String hongBaoLuaSha = "c871de8ba0e647a99df19054276dba9cc1814248";

Object object = jedis.evalsha(Basic.hongBaoLuaSha, 4, Basic.hongBaoPoolKey, 
                                Basic.hongBaoDetailListKey, Basic.userIdRecordKey, userId);
                                                               
public Object evalsha(String sha, int count, String... args) {
        Jedis jedis = null;
        try {
            jedis = pool.getResource();
            return jedis.evalsha(sha, count, args);
        } catch (Exception e) {
            pool.returnBrokenResource(jedis);
            e.printStackTrace();
        } finally {
            returnResource(pool, jedis);
        }
        return null;
}
```



