Mongodb连接池配置

| 参数名 | 默认值 | 说明 |
| :--- | :--- | :--- |
| writeConcern | ACKNOWLEDGED | 写入安全机制，是一种客户端设置，用于控制写入安全的级别:  ACKNOWLEDGED默认选项，数据写入到Primary就向客户端发送确认 0 Unacknowledged对客户端的写入不需要发送任何确认，适用于性能要求高，但不关注正确性的场景;  1 W1数据写入后，会等待集群中1台发送确认 2 W2数据写入后，会等待集群中两台台发送确认 3 W3数据写入后，会等待集群中3台台发送确认 JOURNALED确保所有数据提交到journal file  MAJORITY等待集群中大多数服务器提交后确认； |
| codecRegistry | MongoClient.getDefaultCodecRegistry\(\) | 编解码类，实现Codec接口 |
| minConnectionsPerHost |  | 最小连接数，connections-per-host |
| connectionsPerHost | 100 | 最大连接数 |
| threadsAllowedToBlockForConnectionMultiplier | 5 | 此参数跟connectionsPerHost的乘机为一个线程变为可用的最大阻塞数，超过此乘机数之后的所有线程将及时获取一个异常 |
| maxWaitTime | 1000 \* 60 \* 2 | 一个线程等待链接可用的最大等待毫秒数，0表示不等待 |
| maxConnectionIdleTime | 0 | 设置池连接的最大空闲时间，0表示没有限制 |
| maxConnectionLifeTime | 0 | 设置池连接的最大使用时间，0表示没有限制 |
| connectTimeout | 1000\*10 | 连接超时时间 |
| alwaysUseMBeans | false | 是否打开JMX监控 |

| heartbeatFrequency | 10000 | 设置心跳频率。 这是驱动程序尝试确定群集中每个服务器的当前状态的频率。 |
| :--- | :--- | :--- |
| minHeartbeatFrequency | 500 | 设置最低心跳频率。 如果驱动程序必须经常重新检查服务器的可用性，那么至少要等上一次检查以避免浪费。 |
| heartbeatConnectTimeout | 20000 | 心跳检测连接超时时间 |
| heartbeatSocketTimeout | 20000 | 心跳检测Socket超时时间 |

数据库设计对比![](/assets/shsjah89.png)nosql在数据模式设计上的优势

```
读写效率高，在IO性能上有先天独厚的优势
可扩展能力强，不需要考虑关联，数据分区分库，水平扩展就比较简单
动态模式，不要求每个文档都具有完全相同的结构，对很多异构数据场景支持非常好
模型自然，文档模型最接近于我们熟悉的对象模型
```

### mongoDB关联查询

```
待完善
```

### 聚合的理解（重点）

```
聚合框架就是定义一个管道，管道里的每一步都为下一步输出数据
```

![](/assets/87893jjfa.png)常用的管道操作

```
$project:投影，指定输出文档中的字段
$match:用于过滤数据，只输出符合条件的文档，$match使用MongoDB的标准查询操作
$limit:用来限制MongoDb聚合管道返回的文档数
$skip:在聚合管道中跳过指定数量的文档，并返回余下的文档
$unwind:将文档中的某一个数组类型字段拆分成多条，每条包含数组中的一个值
$group:将集合中的文档分组，可用于统计结果
$sort:将输入文档排序后输出
```

$group操作符

```
$group:可以分组的数据执行如下的表达式计算：
$sum:计算总和
$avg:计算平均值
$min:根据分组，获取集合中所有文档对应值的最小值
$max:根据分组，获取集合中所有文档对应值的最大值
$push:将指定的表达式的值添加到一个数组中
$addToSet:将表达式的值添加到一个集合中（无重复值）
$first:返回每组第一个文档，如果有排序，按照排序，如果没有按照默认的存储的顺序的第一个文档
$last:返回每组最后一个文档，如果有排序，按照排序，如果没有按照默认的存储的顺序的最后一个文档
```

聚合训练

查询2015年4月3号之前，每个用户每个月消费了多少钱，并按照用户名进行排序

```
db.orders.aggregate([{"$match":{ "orderTime" : { "$lt" : new Date("2015-04-03T16:00:00.000Z")}}},
{"$group":{"_id":{"useCode":"$useCode","month":{"$month":"$orderTime"}},"total":{"$sum":"$price"}}},
{"$sort":{"_id":1}}]).pretty()
```

![](/assets/742381yfaaas.png)

