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





