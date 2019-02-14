## Redis+Lua抢红包实战

#### 业务需求

* 将所有红包全部存储到Redis \( 红包池子的概念 \)
* 用户抢了多少红包, 记录红包被抢的详情信息;
* 用户只能抢一次红包, 不能重复抢红包
* 其它........

#### 抢红包实战预热

##### List队列：将N个红包放到List队列中，用来初始化红包池子

##### ![](/assets/loijhg3333.png)List类型：记录用户抢了多少钱

![](/assets/489891jdaskpiop.png)Hash类型：记录那些用户已抢过红包，防止重复抢

#### ![](/assets/sssggg666.png)业务流程

##### 初始化红包池

lpush hongBaoPool {id:rid\_1,money:9}... //将N个红包信息放入hongBaoPool中

##### Lua实现抢红包流程

#### ![](/assets/jjakao12389.png)Lua实现核心业务

##### Lua是什么？

Lua是一个小巧的脚本语言，有标准C编写而成，代码简洁优美，几乎在所有操作系统和平台上都可以编译，运行。

一个完整的Lua解释器不过200k，在目前所有脚本引擎中，Lua的速度是最快的

_Lua语言里的操作提供原子性_

##### Lua的使用

```
set name admin
eval "return redis.call('get',KEYS[1])" 1 name // 1个键，键名为name，返回admin
```

![](/assets/klsakai9012.png)

#### 代码实战





