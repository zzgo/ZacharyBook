### Pipeline出现的背景

redis客户端执行一条命令分4个过程

```
  发送命令-》命令排队-》命令执行-》返回结果
```

这个过程称为Round trip time（RTT，往返时间），mget mset 有效节约了RTT，但大部分命令（如hgetall，并没有mhgetall），不支持批量操作，需要消耗N次RTT，这个时候就需要pipeline来解决这个问题

### Pipeline作用

未使用pipeline执行N条命令

![](/assets/721384781jikfasa.png)

使用了pipeline执行N条命令

![](/assets/uifuias898943.png)

### 两者性能对比

| 网络 | 延迟 | 非Pipeline | Pipeline |
| :--- | :--- | :--- | :--- |
| 本机 | 0.17ms | 573ms | 134ms |
| 内网服务器 | 0.41ms | 1610ms | 240ms |
| 异地机房 | 7ms | 80 000ms | 1104ms |

这是一组统计数据出来的数据

使用Pipeline执行速度与逐条执行要快，特别是客户端与服务端的

网络延迟越大，性能体能越明显

### 原生批命令（mset，mget）与pipeline对比

* 原生批命令时原子性，pipeline是非原子性（原子性概念:一个事务是一个不可分割的最小工作单位,要么都成功要么都失败。
  原子操作是指你的一个业务逻辑必须是不可拆分的. 处理一件事情要么都成功，
  要么都失败，其实也引用了生物里概念，分子－〉原子，原子不可拆分）
* 原生批命令，一个命令对个key ，但pipeline支持多命令（存在事务），非原子性
* 原生批命令时服务端实现，而pipeline需要服务器与客户端共同完成

Pipeline正确使用方式

* 使用pipeline组装的命令个数不能太多，不然数据量过大，增加客户端的等待时间，还可能造成网络阻塞
* 可以考虑将大量命令进行拆分多个小的pipeline命令完成

### pipeline实战

批量删除key实现

```
    /**
     * 批量删除key
     *
     * @param keys
     */
    public void pipelineDel(String... keys) {
        Jedis jedis = null;
        try {
            jedis = pool.getResource();
            //拿到管道
            Pipeline pipeline = jedis.pipelined();
            for (int i = 0; i < keys.length; i++) {
                pipeline.del(keys[i]);
            }
            //真正提交命名
            pipeline.sync();
        } catch (Exception e) {
            pool.returnBrokenResource(jedis);
            e.printStackTrace();
        } finally {
            returnResource(pool, jedis);
        }
    }
```

原生的批量添加key value 使用

```
    /**
     * 批量的设置key:value,可以一个 example: 
     * jedis.mset(new String[]{"key2","value1","key2","value2"})
     * 偶数索引为key，奇数索引为value。
     * @param keysvalues
     * @return 成功返回OK 失败 异常 返回 null
     */
    public String mset(String... keysvalues) {
        Jedis jedis = null;
        String res = null;
        try {
            jedis = pool.getResource();
            res = jedis.mset(keysvalues);
        } catch (Exception e) {
            pool.returnBrokenResource(jedis);
            e.printStackTrace();
        } finally {
            returnResource(pool, jedis);
        }
        return res;
    }
```

返回操作结果，并且有多个不同命名的pipeline操作

```
    public List<Object> pipeline() {
        Jedis jedis = null;
        try {
            jedis = pool.getResource();
            //拿到管道
            Pipeline pipeline = jedis.pipelined();
            //向管道中添加命令
            pipeline.set("name", "admin");
            pipeline.zadd("votes", 1.0, "article:001");
            pipeline.hset("user:001", "name", "test");
            //真正提交命令，并返回结果List<Object> 命令执行的结果[OK,1,1,OK]
            return pipeline.syncAndReturnAll();
        } catch (Exception e) {
            pool.returnBrokenResource(jedis);
            e.printStackTrace();
        } finally {
            returnResource(pool, jedis);
        }
        return null;
    }
```

### 总结：

记住两个真正提交的命令

* pipeline.sync\(\) 提交，没有返回值
* pipeline.syncAndReturnAll\(\) 返回List&lt;Object&gt; 操作后的结果信息\[OK,1,1,OK\]



