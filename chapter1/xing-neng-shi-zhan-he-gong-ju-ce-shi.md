### Redis慢查询分析

与mysql一样：当执行时间超过阀值，会将发生时间 耗时 命令**记录**

![](/assets/2189qiajhdkaja.png)

描述了客户端发送命令，到redis服务端排队，然后执行，再返回执行后的结果

慢查询只统计**第三个**执行步骤的时间，即**set key value ，set 命令执行的时间**，不去计算发送命令和命令排队消耗的时间

### 慢查询阀值

有两种方式，redis.conf默认设置为10ms

1、动态设置

```
> config set slowlog-log-slower-than 10000 // 10毫秒
```

使用config set 完成后，重启redis后，配置变为默认设置，,若想将配置持久化保存到redis.conf，要执行**config rewrite**

![](/assets/2189a89dau.png)

![](/assets/kkkoiaosid012.png)

2、redis.conf修改：找到slowlog-log-slower-than 10000，修改保存即可

```
注意：slowlog-log-slower-than  =0记录所有命令 =-1 命令都不记录
```

### 慢查询原理

慢查询记录也是存在队列里的，slow-max-len 存放的记录最大条数，比如设置slow-man-len=10，当有第11条慢查询命令插入时，队列的第一条命令就会出列，第11条入列到慢查询队列中，可以config set动态设置，也可以修改redis.conf完成配置

### 慢查询命令

获取队列里慢查询的命令

```
> slowlog get
1) 1) (integer) 1            # 唯一性(unique)的日志标识符
   2) (integer) 1550196043   # 被记录命令的执行时间点，以 UNIX 时间戳格式表示
   3) (integer) 11           # 查询执行时间，以微秒为单位
   4) 1) "set"               # 执行的命令，以数组的形式排列
      2) "k"                 # 这里完整的命令是 SET k v
      3) "v"
   5) "192.168.111.128:47434"
   6) ""
```

获取慢查询列表当前的长度

```
> slowlog len
(integer) 3
```

对慢查询列表清理（重置）

```
> slowlog reset
OK
> slowlog len
(integer) 0
> slowlog get
(empty list or set)
```

建议

* 对于线上slow-max-len配置建议：线上可加大slow-max-len的值，记录慢查询存长命令时redis会做截断，不会占用大量内存，线上可设置1000以上
* 对于线上slowlog-log-slower-than配置建议：默认为10毫秒，根据redis并发量来调整，对于高并发建议为1毫秒
* 慢查询是先进先出的队列，访问日志记录出列丢失，需定期执行slow get，将结果存储到其他设备中（如mysql）

### redis-cli详解

可以参看一下

[https://blog.csdn.net/u014034934/article/details/72955663](https://blog.csdn.net/u014034934/article/details/72955663)

返回pong表示127.0.0.1:6379能通，正常，-r 3 重复执行3次，-a zhangqi 指定密码zhangqi，ping 看能不能ping通

```
> ./redis-cli -r 3 -a zhangqi ping
PONG
PONG
PONG
```



