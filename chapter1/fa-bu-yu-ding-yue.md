### 发布与订阅

redis提供了“发布、订阅”模式消息机制，其中消息订阅者与发布者不直接通信，发布者向指定的频道（channel）发布信息，订阅该频道的每个客户端都可以接收到消息

![](/assets/fjkf992122.png)

#### 发布与订阅命令

**redis主要提供发布消息、订阅频道、取消订阅以及按照模式订阅和取消订阅**

和很多专业的消息队列（kfaka rabbitmq），redis的发布订阅显得很lower，比如无法实现消息规程和回溯，但就是简单，如果能满足应用场景，用这个也可以

发布消息

```
127.0.0.1:6379> PUBLISH channel:test "hello world"
(integer) 0        #该频道没有订阅者，所以返回0接收到消息
```

订阅消息

```
127.0.0.1:6379> subscribe channel:test
Reading messages... (press Ctrl-C to quit)
1) "subscribe"
2) "channel:test"
3) (integer) 1
```

查看订阅数

```
127.0.0.1:6379> pubsub numsub channel:test
1) "channel:test"
2) (integer) 0        #订阅数
```

取消订阅

```
127.0.0.1:6379> unsubscribe channel:test
1) "unsubscribe"
2) "channel:test"
3) (integer) 0
```

按模式订阅和取消订阅

```
127.0.0.1:6379> psubscribe ch*         #正则，满足ch*的频道都将会接收消息
Reading messages... (press Ctrl-C to quit)
1) "psubscribe"
2) "ch*"
3) (integer) 1

127.0.0.1:6379> punsubscribe ch*        #取消订阅
1) "punsubscribe"
2) "ch*"
3) (integer) 0
```

#### 应用场景

* 今日头条订阅号、微信订阅公众号、新浪微博关注、邮件订阅系统
* 即使通信系统
* 群聊部落系统（微信群）

##### 测试实践：微信班级群class：20190101

学生C订阅一个主题叫：class:20190101

```
127.0.0.1:6379> SUBSCRIBE class:20190101
Reading messages... (press Ctrl-C to quit)
1) "subscribe"
2) "class:20190101"
3) (integer) 1
```

学生A针对class:20190101主体发送消息，那么所有订阅该主题的用户都能收到该数据

```
127.0.0.1:6379> publish class:20190101 "hello world! I am A"
(integer) 1

```

C学生的界面

```
127.0.0.1:6379> SUBSCRIBE class:20190101
Reading messages... (press Ctrl-C to quit)
1) "subscribe"
2) "class:20190101"
3) (integer) 1
1) "message"
2) "class:20190101"
3) "hello world! I am A"
```

学生B针对class:20190101主体发送消息，那么所有订阅该主题的用户都能收到该数据

```
127.0.0.1:6379> publish class:20190101 "hello world! I am B"
(integer) 1
```

C学生的界面

```
127.0.0.1:6379> SUBSCRIBE class:20190101
Reading messages... (press Ctrl-C to quit)
1) "subscribe"
2) "class:20190101"
3) (integer) 1
1) "message"
2) "class:20190101"
3) "hello world! I am A"
1) "message"
2) "class:20190101"
3) "hello world! I am B"

```



