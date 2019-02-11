### 人员信息数据结构

```
{
        "_id" : ObjectId("59f938235d93fc4af8a37114"),
        "username" : "lison",
        "country" : "in11digo",
        "address" : {
                "aCode" : "邮编",
                "add" : "d11pff"
        },
        "favorites" : {
                "movies" : ["杀破狼2","1dushe","雷神1"],
                "cites" : ["1sh","1cs","1zz"]
        },
       "age" : 18，
       "salary"：NumberDecimal("2.099"),
       "lenght" ：1.79
}
```

新增

> db.users.insert\(json格式\);

查询喜欢的城市包含东莞和东京的user

```
类似sql语句

select * from users where favorites.cites has ‘东莞’、‘东京’ 

mongodb语句

db.users.find({"favorites.cites":{"$all":['东莞','东京']}})

类似文档的形式查询，key-value形式
$all -> 查询条件都满足的显示出来
$all -> [] 数组，每一项都包括
```

![](/assets/sajka322.png)

> **注意使用**：./mongo localhost:port 链接到数据库内，如：./mongo localhost:27022
>
> 使用 show dbs 查看所有数据库
>
> 使用 use 数据库  切换数据库，如：use lison
>
> 使用 db.数据库表.find\({}\) 查询所有，如：db.users.find\({}\)

查询国籍为英国或者美国，名字中包含s的user

```
sql语句

select * from users where username like '%s%' and ( country = english or country = usa )

mongodb语句

db.users.find({"$and":[{"username":{"$regex":".*s.*"}},{"$or":[{"country":"English"},{"country":"USA"}]}]})
```

![](/assets/fsajkf239.png)

> 1、$and 连接多个语句，同时满足
>
> 2、$or连接多个语句 ，或者满足
>
> 3、$regex正则表达式

把lison的年龄修改为6岁

```
sql语句

update users set age=6 where username=lison

mongodb语句

db.users.updateMany({"username":"lison"},{"$set":{"age":6}},true)
```

![](/assets/sahj2348.png)

> 使用db.users.updateMany 来修改数据

```
db.collection.update(
   <query>,
   <update>,
   {
     upsert: <boolean>,
     multi: <boolean>,
     writeConcern: <document>
   }
)

参数说明：

query : update的查询条件，类似sql update查询内where后面的。
update : update的对象和一些更新的操作符（如$,$inc...）等，也可以理解为sql update查询内set后面的
upsert : 可选，这个参数的意思是，如果不存在update的记录，是否插入objNew,true为插入，默认是false，不插入。
multi : 可选，mongodb 默认是false,只更新找到的第一条记录，如果这个参数为true,就把按条件查出来多条记录全部更新。
writeConcern :可选，抛出异常的级别。
```

> $set设置需要改变的值 :
>
> 格式：{ $set: { &lt;field1&gt;: &lt;value1&gt;, ... } }
>
> 比如：db.users.updateMany\({"username":"lison"},{"$set":{"age":6,"lenght":1.8}},true\)

喜欢的城市包含东莞的人，给他喜欢的电影加入“小电影2”“小电影3”

```
sql语句

update users set favorites.movies add “小电影2”,“小电影3” where favorites。cites has “东莞”

mongodb 语句

db.users.updateMany({"favorites.cites":"东莞"},{"$addToSet":{"favorites.movies":{"$each":["小电影2","小电影3"]}}})
```

![](/assets/saasas65665.png)

> $addToset和$each连用，满足条件将每一项添加到movies这个数组中
>
> 与$set有的区别，注意区分开来

删除名字为lison的user

```
sql语句
delete from users where username = "lison"

mongodb语句
db.users.deleteMany({"username":"lison"})
```

![](/assets/ashjas93239.png)

> 使用db.collection.deleteMany\(\)

```
db.collection.remove(
   <query>,
   {
     justOne: <boolean>,
     writeConcern: <document>
   }
)

参数说明：

query :（可选）删除的文档的条件。
justOne : （可选）如果设为 true 或 1，则只删除一个文档，如果不设置该参数，或使用默认值 false，则删除所有匹配条件的文档。
writeConcern :（可选）抛出异常的级别。
```

删除年龄大于8小于25的user

```
sql语句
delete users where age > 8 and age < 25

mongodb语句
db.users.deleteMany({"$and":["age":{"$gt":8},{"age":{"$lt":25}}]})
```

![](/assets/djfka8223.png)

> $gt 大于
>
> $lt 小于



