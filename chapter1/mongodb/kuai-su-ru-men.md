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

查询喜欢的城市包含东莞和东京的user

```
类似sql语句

select * from users where favorites.cites has ‘东莞’、‘东京’ 

mongodb语句

db.users.find({
    "favorites.cites":{
        "$all":['东莞','东京']
    }
})

类似文档的形式查询，key-value形式
$all -> 查询条件满足值即可
```

![](/assets/sajka322.png)

> **注意使用**：./mongo localhost:port 链接到数据库内
>
> 使用 show dbs 查看所有数据库
>
> 使用 use 数据库  切换数据库
>
> 使用 db.数据库表.find\({}\) 查询所有



