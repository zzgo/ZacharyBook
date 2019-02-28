# 数据结构 - 字符串（String）

* 字符串类型：实际上可以是字符串（包括xml，JOSN），还有数字（整形浮点数），二级制（图片，音频，视频），最大不能超过512MB

### set

* set age 23 ex 10  ---&gt; 10s后过期 px 10000毫秒过期
* setnx name test --&gt; 不存在键name时，返回1设值成功，存在的话失败0
* set age 10 xx --&gt; 如果存在这个age键 ，那么进行设值为10 ，返回1，如果不存在这个age键的话，不会有什么操作
* 【注意】**如果有多客户同时执行setnx,只有一个能设置成功，可做分布式锁**

### get

* get age --&gt;存在就返回value，不存在返回nil

### mset

* 批量设值，mset name test age 18 --&gt; 设值了两个key-value  name-test , age-18

### mget

* 批量获取，mget name age --&gt; 返回 test，18
* 好处：减少了要去链接的网络开销，没有mget 则会使用多次get命令。批量下mset，mget都会提高响应时间，主要是节省了网络开销这一块的时间

# 常用命令 - 字符串（计数）

### incr

* incr age --&gt; 必须为整数，自加1，非整数返回错误，无age键从0自增返回1

### decr

* decr age 整数age 减1

### incrby

* incrby age 2   整数age+2

### decrby

* decrby age 2 整数age-2

### incrbyfloat

* incrbyfloat score 1.1 浮点型score+1.1

# 常用命令 - 字符串（追加）

### append

* 追加：set name hello ; append name world ; get name 为 helloworld

### strlen

* 字符串长度：set hello "世界" ； strlen hello 结果为 6 每个中文沾3个字节

### getrange

* 截取字符串 ：set name helloworld ；getrange name 2 4 返回llo

# 数据结构 - 哈希（Hash）

* 哈希hash是一个string 类型的field和value的映射表，hash特别适合用于存储对象

### hset

* 设值：hset key field value
* hset user:1 name test 成功返回1失败返回0

### hget

* 取值：hget user:1 name 返回 test

### hdel

* 删除值：hdel user:1 name

### hlen

* 计算个数：hlen
* hset user:1 name test ; hset user:1 age 12
* hlen user:1 返回2 user:1 有两个属性name gae

### hmset

> 批量设值：hmset user:2 name test age 18 sex boy 返回ok

### hmget

> 批量取值：hmget user:2 name age sex 返回三行，test 18 boy

### hexists

> 判断field是否存在：hexists user:2 name 若存在返回1，不存在返回0

### hkeys

> 获取所有的field：hkeys user:2 返回 name age sex 三个field

### hvals

> 获取某个key所有的value：hvals user:2 返回 test 18 boy

### hgetall

> 获取某个key的所有field和value：hgetall user:2 返回 name test age 18 sex boy

### hincrby

> 增加1 hinctby user:2 age 1 age+1

### hincrbyfloat

> hincrbyfloat user:2 age 2 浮点型加2

# 常用命令 - Hash（应用场景）

![](/assets/213fsadas.png)

![](/assets/gfggwe32.png)

> 将表结构转化为hash存储的实现
>
> Hash 类型是稀疏，每个键都可有不同的field，若用redis模拟作关系复杂查询开发困难，维护成本高

## 比对三种方案实现存储用户信息的优缺点

1.原生

> 使用set：set user:1:name test; set user:1:age 18;set user:1:sex boy
>
> 优点：简单直观，每个键对应一个值
>
> 缺点：键数过多，占用内存多，用户信息过于分散，不利于生成环境

2.将对象序列化存入redis

> set user:1 serialize\(user\)
>
> 优点：编程简单，若使用序列化合理内存使用率高
>
> 缺点：序列化与反序列有一定开销，更新属性时需要把user全部取出来进行反序列化，更新后再序列化到redis

3.使用hash类型

> hmset user:1 name test age 18 sex boy
>
> 优点：简单直观，使用合理可减少内存空间消耗
>
> 缺点：要控制ziplist与hashtable两种编码转换，且hashtable会消耗更多的内存serialize\(user\);

总结

> 对于更新不多的情况下，可以使用序列化，对于value不大于64字节，可以使用hash类型

# 数据结构 - 列表 （List）

> 用来存储多个有序的字符串，一个列表最多可存2的32次方减1个元素

![](/assets/kasklsa901.png)

> 因为有序，可以通过索引下标获取元素或某个范围内的元素列表，列表元素可以重复

### rpush

> 插入值
>
> rpush strs c b a 从右向左有插入c b a 返回值3

### lrange

> 获取值 ， 索引下标特点：从左到右为0到N-1
>
> lrange strs 0 -1 从左到右获取列表所有元素 返回 c b a

### lpush

> 插入值
>
> lpush strs c b a 从左向右插入 c b a

### linsert

> 插入值
>
> 之前是：lrange strs 0 -1   c b a
>
> linsert strs before b d 在b之前插入d ，after 为之后
>
> 使用lrange strs 0 -1 查看 c d b a

### lindex

> lindex strs -1 返回最右末尾a  -2 返回 b

### llen

> llen strs 返回当前列表长度

### lpop

> 把最左边的第一个元素c删除
>
> lpop strs

### rpop

> 把最右边的元素a删除
>
> rpop strs

### lset

> lset key index value
>
> 修改：lset user:1 0 name 修改key为user:1的第一个元素的值为name

# 数据结构 - 集合（Set）

## 作用

> 保存多个元素，与列表不一样的是不允许有重复的元素，且集合是无序的，一个集合最多有可存2的32次方减1个元素，除了支持增删改查，还支持集合交集，并集，差集

## 场景

> 用户标签，社交，查询有共同兴趣爱好的人，智能推荐

## 命令

### exists

> exists user 坚持user键是否存在

### sadd

> sadd user a b c 向user插入3个元素 返回3
>
> sadd user a b 若插入相同的元素，则重复无效返回0

### smembers

> smembers user 获取user的所有元素，返回结果无序

### srem

> srem user a  返回1，删除a元素

### scard

> scard user 返回2，计算元素个数

### sinter

> 计算两个键的交集
>
> sinter user:1:fav user:2:fav

### 使用场景

> 标签，社交，查询有共同爱好兴趣的人，智能推荐
>
> 给用户添加标签
>
> sadd user:1:fav basball fball pq ; sadd user:2:fav basball fball
>
> 或者标签添加用户
>
> sadd basball:users user:1 user:2
>
> sadd fball.users user:1 user:2 user:3
>
> 计算出共同感兴趣的人
>
> sinter user:1:fav user:2:fav

# 数据结构-有序集合（ZSet）

场景

> 常用于排行榜，如视频网站需要对用户视频做排行榜，或点赞数与集合有联系，不能重复的成员

![](/assets/jjjf5.png)

### 有序集合与集合及队列区别

| 数据结构 | 是否允许元素重复 | 是否有序 | 有序实现方式 | 应用场景 |
| :--- | :--- | :--- | :--- | :--- |
| 列表 | 是 | 是 | 索引下标 | 时间轴，消息队列 |
| 集合 | 否 | 否 | 无 | 标签，社交 |
| 有序集合 | 否 | 是 | 分值 | 排行榜，点赞数 |

### 命令

### zadd

> zadd key score member \[score member...\]
>
> zadd user:zan 200 james james的点赞数，返回操作成功的条数1
>
> zadd user:zan 200 james 120 mike 100 lee 返回3
>
> zadd test:1 nx 100 james 键test:1必须不存在，主要用于添加
>
> zadd test:1 xx incr 200 james 键test:1必须存在，主要用于修改，此时为300
>
> zadd test:1 xx ch incr -299 james 返回操作结果1 300-299=1

### zrange

> zrange test:1 0 -1 withscores 查看点赞数与成员名 返回 james 100

### zcard

> 计算成员个数，返回1 zcard test:1 返回1

### zrank

> 返回名次 从0开始算
>
> zadd user:3 200 james 120 mike 100 lee 插入数据
>
> zrange user:3 0 -1 withscores 查看分数与成员
>
> zrange user:3 james 返回名次 第3名返回2 ，从0开始到2 ，共3名

### zrevrank

> 反排序
>
> zrevrank user:3 james 返回0，反排序，点赞数越高，排名越前

### zincrby

> zadd user:1:20180106 3 mike mike获取3个赞
>
> zincrby user:1:20180106 1 mike 在3的基础上加1

### zrem

> zrem user:120180106 mike 删除mike

### zrevrangebyrank

> 展示赞数最多的5个用户
>
> zrevrangebyrank user:1:20180106 0 4

### zscore

> 查看用户赞数与排名
>
> zscore user:120180106 mike 查看分数
>
> zrank user:1:20180106 mike 查看排名，在这个有序集合的位置

# 全局命令

### keys

> 查看所有键
>
> keys \* 返回所有键

### dbsize

> 键总数 **如果存在大量键，线上禁止使用此命令**

### exists

> 查看键是否存在
>
> exists key 存在返回1，不存在返回0

### del

> 删除键
>
> del key 返回删除的键个数，删除不存在的键返回0

### expire

> 键过期
>
> expire key seconds 设置键过期的时间

### ttl

> 查看剩余的过期时间
>
> ttl key

### type

> type key 键的数据结构类型
>
> type hello 返回string ，键不存在返回none

# 数据库管理

| redis数据库管理方式 | 作用 |
| :--- | :--- |
| select 0 | redis一般有16个数据库，select index index=\[0 16\),切换数据库 |
| flushdb | 清空当前数据库index的所有数据，线上禁止使用 |
| flushall | 清空所有的数据库（0-15）的所有数据，线上禁止使用 |
| dbsize | 统计当前数据库index下键的个数，键个数太多，线上禁止使用 |



