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

使用config set 完成后，若想将配置持久化保存到redis.conf，要执行config rewrite

