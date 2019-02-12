查询语句指令

```
db.collection.find(query,projection)
参数说明：
query:可选，使用查询操作符指定查询条件
projection:可选，使用投影操作符指定返回的键。查询时返回文档中所有键值， 只需省略该参数即可（默认省略）
注意：
0表示字段排除，非0表示字段选择并排除其他字段，
排除字段时所有字段必须设置为0，保留字段，排除其他字段时，只要是不为0（整数，负数，字符串）都可以（当然设置为1是一种规范）；
需要以易读的方式来读取数据，可以使用 pretty() 方法
比如：
db.users.find({"username":"lison"},{"username":0,"age":0}) 查询username等于lison的，返回文档不需要username和age两个字段
```

截图：

![](/assets/1289jdkfjka.png)

![](/assets/98jkjk1.png)

再次注意：

> 使用pojection时，{“username”:0,"age":0} 要么同时为0，要么同时不为0（可以是整数，负数，字符串）（当然设置为1是一种规范）。一个为0一个不为0则会出错。



查询选择器

| 运算符类型 | 运算符 | 描述 |
| :--- | :--- | :--- |
| 范围 | $eq | 等于 |
|  | $lt | 小于 |
|  | $gt | 大于 |
|  | $lte | 小于等于 |
|  | $gte | 大于等于 |
|  | $in | 判断元素是否在指定的集合范围里 |
|  | $all | 判断数组中是否包含某几个元素,无关顺序 |
|  | $nin | 判断元素是否不在指定的集合范围里 |
| 布尔运算 | $ne | 不等于，不匹配参数条件 |
|  | $not | 不匹配结果 |
|  | $or | 有一个条件成立则匹配 |
|  | $nor | 所有条件都不匹配 |
|  | $and | 所有条件都必须匹配 |
|  | $exists | 判断元素是否存在 |
| 其他 | . | 子文档匹配 |
|  | $regex | 正则表达式匹配 |

### 在linux下实战操作

连接mongodb，指定ip和端口

```
./mongo 192.168.111.128:27022
```

in选择器使用

```
db.users.find({"username":{"$in":["lison","james","mark"]}}).pretty()
查询username属于lison，james，mark这个范围的人
```

exists选择器使用

```
db.users.find({"lenght":{"$exists":true}}).pretty()

表示查询文档中有lenght字段的列表，
其中$exists 为 true或者0时，表示查询不含有lenght的列表，为其他值时，表示含有lenght的列表。
一般使用 true-false，0-1表示，以便来规范。

```

not选择器使用

```
db.users.find({"lenght":{"$not":{"$gte":1.77}}}).pretty()
查询lenght的值小于1.77，
如果文档文档中没有lenght这个字段，也是满足条件的
即：not语句 会把不包含查询语句字段的文档 也检索出来
```

映射

```
字段选择并排除其它字段
db.users.find({},{"age":1})
字段排除
db.users.find({},{"age":0})
```

![](/assets/jkkaka3892.png)

![](/assets/21spjfsa.png)

排序

```
sort()
db.users.find({},{"age":1}).sort({"age":1}).pretty() 
注意：
sort里面的值只能是 1 和 -1  其他值都会报错
1表示升序，-1表示降序
```

![](/assets/78jklk.png)

跳过和限制（可以做分页）

```
skip(n):跳过n条数据
limit(n):限制n条数据
db.user.find({},{"age":1}).sort({"age":1}).limit(1).skip(2) 返回一条记录，跳过前两条
可以用来做分页
```

![](/assets/fasa3892.png)

![](/assets/8989jhskjdak.png)

查询唯一值

```
distinct():查询指定字段的唯一值

db.users.distinct("age") 查询返回字段只包含年纪
```

![](/assets/898ikjs.png)



