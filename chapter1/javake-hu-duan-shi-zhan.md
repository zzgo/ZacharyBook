## 基于Redis设计的投票网站实战

#### 需求

* 用户可以发表文章,发表时默认给自己的文章投了一票
* 用户在查看网站时可以按评分进行排列查看
* 用户也可以按照文章发布时间进行排序
* 为节约内存，一篇文章发表后，7天内可以投票,7天过后就不能再投票了
* 为防止同一用户多次投票，用户只能给一篇文章投一次票

#### 数据库设计

文章基本信息表 t\_article

| article\_id | votes | link | title | content | post\_time | user\_id |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |


文章票数与分值表 t\_vote\_data

| article\_id | votes | scores |
| :--- | :--- | :--- |


文章投票详表 t\_vote\_details

| article\_id | vote\_time | user\_id |
| :--- | :--- | :--- |


#### 投票网站应用场景会使用到的Redis相关指令如下

* HASH类型命令：hset  hincrBy  hgetAll   expire
* SET集合命令：sadd  smembers
* ZSET集合命令：zadd   zscore  zincrby  zrevrange

```
hset key feild value key的字段feild的值为value
hincrby key feild incr key的字段feild的值加incr,增加
hgetAll key 获取字段名和对应的值
expire key seconds 设置过期时间 
sadd key member[member...] 为key设置值 可以是多个
smembers key 获取key的所有值
zadd key score member[ score member ...] key添加值以及对应的分数，可以添加多个
zscore key member 获取key下面的member元素的分数
zincrby key incr member key下面的member元素的分数加incr
zrevrange key start stop 获取从start开始到stop的元素，是有序的，并且进行反转排序
```

#### Redis的key设计思路

Key-Value键值对：set key value

key的设计一般以业务，功能模块或者表名开头，后跟主键（或是能表示数据唯一性的值）

比如：用户模块，其中用户ID 001，用户名称admin，那么key的设计：

set user:001:name admin key为user:001:name

#### 记录已投票的用户，防重投

已对001文章投过票的用户，使用set存储（无序，不能重复）

![](/assets/jksadksadk89932.png)

#### 记录文章分值

使用ZSET记录文章投票分数，按文章发布的时间戳（有序列，不能重复）

![](/assets/489jfkaj.png)

#### Redis缓存设计

![](/assets/9808asjidjak.png)

![](/assets/1321209asdopqwe.png)

#### 代码环节



