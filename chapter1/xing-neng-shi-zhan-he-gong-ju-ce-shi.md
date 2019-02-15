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
$ ./redis-cli -r 3 -a zhangqi ping
PONG
PONG
PONG
```

每秒输出内存使用量，输出10次，-r 10 重复执行10次，-i 1 每秒执行 info \| grep used\_\_memory\_\_human 内存使用量

```
& ./redis-cli -r 10 -i 1 info |grep used_memory_human
```

关闭redis服务（安全关闭redis服务）

```
& ./redis-cli shutdown
```

./redis.cli --help 命令

```
用法:redis-cli [OPTIONS] [cmd [arg [arg…]]

    -h <主机名>服务器主机名(默认:127.0.0.1)

    -p <端口>服务器端口(默认:6379)

    -s Server socket(覆盖主机名和端口)

    -a 连接到服务器时使用的密码>。

    -u 服务器uri。

    -r Execute specified command N times.重复执行指定命令N次。

    -r使用时，每个命令等待<间隔>秒。

    可以指定亚秒时间，比如-i 0.1。

    -n 数据库号。

    -x读取STDIN的最后一个参数。

    -d <分隔符>多块分隔符用于原始格式(默认:\n)。

    -c 启用集群模式(遵循-ASK和-MOVED重定向)。

    ——raw 原始回复使用原始格式(默认为STDOUT)

    不是tty)。

    ——no-raw 即使STDOUT不是tty，也没有原始强制格式化输出。

    ——csv格式输出。

    --stat 输出关于服务器的滚动统计信息:mem, client，…

    --latency 延迟进入一种特殊模式，持续采样延迟。

    如果您在交互式会话中使用此模式，它将运行

    永远显示实时状态。否则，如果

    ——csv被指定，或者如果您将输出重定向到non

    TTY，它对延迟进行了1秒的采样(可以使用)

    -改变间隔)，然后产生一个输出

    并退出。

    --latency-history 类似延迟历史的延迟，但跟踪延迟随时间的变化。

    默认的时间间隔是15秒。

    --latency-dist 延迟区显示延迟作为一个频谱，需要xterm 256颜色。

    默认时间间隔是1秒。使用-i更改它。

    ——lru-test 使用80-20分布模拟缓存工作负载。

    -slave模拟一个显示从主机接收到的命令的slave。

    ——rdb 将rdb转储文件从远程服务器传输到本地文件。

    ——pipe 管道将原始Redis协议从stdin传输到服务器。

    ——pipe-timeout 在——管道模式下，如果发送完所有数据，则中止，并出现错误。

    在秒内没有收到回复。

    默认超时时间:30。用0永远等待。

    ——bigkeys示例Redis keys正在寻找大键。

    ——hotkeys示例Redis键寻找热键。

    只有在最大内存策略为*lfu时才有效。

    ——scan 使用scan命令列出所有键。

    ——pattern used with——scan指定扫描模式。

    ——intrinsic-latency 内部延迟运行测试来测量内部系统延迟。

    测试将运行指定的秒数。

    ——eval 使用上的Lua脚本发送一个eval命令。

    ——ldb with——eval启用Redis Lua调试器。

    -ldb-sync-mode Like -ldb，但使用的是同步Lua调试器

    此模式将阻塞服务器并更改脚本

    没有从服务器内存回滚。

    ——help输出此帮助并退出。

    ——version 输出版本并退出。


例子:

    cat /etc/passwd | redis-cli -x set mypasswd

    redis-cli get mypasswd

    redis-cli - r100 lpush mylist x

    redis-cli -r 100 - 1 info | grep used_memory_human:

    redis-cli - eval myscript.lua key1 key2, arg1 arg2 arg3

    redis-cli -scan -pattern '*:12345*'
```

### redis-server详解

指定配置文件启动 redis.conf 配置文件路径启动服务，& 后台运行

```
$ ./redis-server redis.conf &
```

检测操作系统能否1G内存给redis，常用于测试，想快速沾满机器内存做极端条件测试，可使用这个指令，--test-memory 1024 内存测试 1G

```
$ ./redis-server --test-memory 1024
```

redis-server --help

```
Usage: ./redis-server [/path/to/redis.conf] [options]
       ./redis-server - (read config from stdin)
       ./redis-server -v or --version
       ./redis-server -h or --help
       ./redis-server --test-memory <megabytes>

Examples:
       ./redis-server (run the server with default conf)
       ./redis-server /etc/redis/6379.conf
       ./redis-server --port 7777
       ./redis-server --port 7777 --slaveof 127.0.0.1 8888
       ./redis-server /etc/myredis.conf --loglevel verbose

Sentinel mode:
       ./redis-server /etc/sentinel.conf --sentinel
```

### redis-benchmark详解

100个并发连接，100000个请求，检测host为localhost端口为6379的redis服务性能，-c 100 100并发，-n 100000 10万请求

```
$ ./redis-benchmark -h 192.168.111.128 -p 6379 -c 100 -n 100000
====== PING_INLINE ======
  100000 requests completed in 1.94 seconds
  100 parallel clients
  3 bytes payload
  keep alive: 1

30.72% <= 1 milliseconds
91.65% <= 2 milliseconds
98.73% <= 3 milliseconds
99.94% <= 4 milliseconds
100.00% <= 4 milliseconds
51493.30 requests per second # 可以看到每秒请求
...
====== SET ======
  100000 requests completed in 2.06 seconds
  100 parallel clients
  3 bytes payload
  keep alive: 1

26.17% <= 1 milliseconds
90.48% <= 2 milliseconds
98.24% <= 3 milliseconds
99.94% <= 4 milliseconds
100.00% <= 4 milliseconds
48638.13 requests per second
```

测试存取大小为100字节的数据包的性能 -q 只显示查询秒值，-d 字节大小100

```
$ ./redis-benchmark -h 192.168.111.128 -p 6379 -q -d 100
PING_INLINE: 51679.59 requests per second #结果中只显示了秒值，其他信息不会显示
PING_BULK: 52631.58 requests per second
SET: 50301.81 requests per second
GET: 50125.31 requests per second
INCR: 50251.26 requests per second
LPUSH: 49529.47 requests per second
RPUSH: 49382.71 requests per second
LPOP: 50276.52 requests per second
RPOP: 50125.31 requests per second
SADD: 49875.31 requests per second
HSET: 49140.05 requests per second
SPOP: 50175.61 requests per second
LPUSH (needed to benchmark LRANGE): 49701.79 requests per second
```



