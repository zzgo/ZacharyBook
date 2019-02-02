redis是什么

> Redis是一个开源的使用ANSI C 语言编写，支持网络，可基于内存亦可持久化的日志型，Key-Value数据库，并提供多种语言的
>
> API
>
> Redis 安装在磁盘，数据存储在内存中

简述redis特性

> redis基于键值对（key-value）数据库，其中的value可以是string，hash，list，set，zset等多种数据库结构
>
> 提供键过期，发布订阅，事务，流水线等附加功能
>
> 速度快，数据放在内存中，官方给出的读写性能是10万/s，数据放在内存中是速度块的主要原因，C语言实现，与操作系统距离近，使用了单线程，预防多线程可能产生的竞争问题
>
> 键值对的数据结构服务器
>
> 丰富的功能
>
> 简单稳定：单线程
>
> 持久化：发生断电或机器故障，数据可能会丢失，持久化到硬盘
>
> 主从复制：可以实现多个相同数据的redis副本
>
> 高可用和分布式转移：哨兵机制实现高可用，保证redis节点故障发现和自动转移
>
> 客户端语言多

场景简述

> 缓存数据库：合理的使用缓存加快数据访问速度，降低后端数据源压力
>
> 排行榜：按照热度排名，按照发布时间排行，主要用到列表和有序集合
>
> 计算器应用：视频网站播放数，网站游览数，主要使用redis计数
>
> 社交网络：赞，踩，粉丝，下拉刷新
>
> 消息队列：发布和订阅

redis安装后需要知道的几个文件

| 可执行文件 | 作用 |
| :--- | :--- |
| redis-server | 启动redis |
| redis-cli | redis命令行客户端 |
| redis-benchmark | 基准测试工具 |
| redis-check-aof | AOF持久化文件检测和修复工具 |
| redis-check-dump | RDB持久化文件检测和修复工具 |
| redis-sentinel | 启动哨兵 |
| redis-trib | cluster集群构建工具 |

redis-server

> 启动redis命令，linux下：./redis-server redis.conf &
>
> 解释 ./redis-server 启动， redis.conf 启动加载的配置文件 & 后台运行

redis-cli

> 命令：./redis-cli -h {host} -p {port} -a {password}
>
> ./redis -h 192.168.1.1 -p 6379 -a 12345678
>
> -h 接ip地址  -p 接端口号 -a 设置的密码
>
> 停止redis 服务
>
> ./redis-cli shutdown
>
> 注意：关闭时，断开连接，持久化文件生成，相对安全，还可以关闭用kill，此方式不会持久化，还会造成缓冲区非法关闭，可能会造成AOF和丢失数据

版本

> 版本号第二位，为奇数为不稳定版本，为偶数为稳定版本

了解，redis基本通讯模型

> 执行过程，发送命令-&gt;执行命令-&gt;返回结果
>
> 执行命令，单线程执行，所有命令进入队列，按顺序执行
>
> 单线程块的原因，纯内存访问，单线程避免线程切换和竞争产生资源消耗，RESP协议简单
>
> 描述RESP协议（[https://redis.io/topics/protocol），发送报文格式](https://redis.io/topics/protocol），发送报文格式)



