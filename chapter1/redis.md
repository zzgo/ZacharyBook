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

> 速度快
>
> 键值对的数据结构服务器
>
> 丰富的功能
>
> 简单稳定
>
> 持久化
>
> 主从复制
>
> 高可用和分布式转移
>
> 客户端语言多

场景简述

> 缓存数据库
>
> 排行榜
>
> 计算器应用
>
> 社交网络
>
> 消息队列

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

 版本

> 版本号第二位，为奇数为不稳定版本，为偶数为稳定版本



