测试数据

```
db.users.drop();
var user1 = {
        "username" : "lison",
        "country" : "china",
        "address" : {
                "aCode" : "411000",
                "add" : "长沙"
        },
        "favorites" : {
                "movies" : ["妇联4","杀破狼2","战狼","雷神1","神奇动物在哪里"],
                "cites" : ["长沙","深圳","上海"]
        },
        "age" : 18,
	   "salary":NumberDecimal("18889.09"),
       "lenght" :1.79,
	   "comments" :  [
				{
					"author"  :  "lison1",
					"content"  :  "lison评论1",
					"commentTime" : ISODate("2017-01-06T00:00:00")
				},
				{
					"author"  :  "lison2",
					"content"  :  "lison评论2",
					"commentTime" : ISODate("2017-02-06T00:00:00")
				},
				{
					"author"  :  "lison3",
					"content"  :  "lison评论3",
					"commentTime" : ISODate("2017-03-06T00:00:00")
				},
				{
					"author"  :  "lison4",
					"content"  :  "lison评论4",
					"commentTime" : ISODate("2017-04-06T00:00:00")
				},
				{
					"author"  :  "lison5",
					"content"  :  "lison是苍老师的小迷弟",
					"commentTime" : ISODate("2017-05-06T00:00:00")
				},
				{
					"author"  :  "lison6",
					"content"  :  "lison评论6",
					"commentTime" : ISODate("2017-06-06T00:00:00")
				},
				{
					"author"  :  "lison7",
					"content"  :  "lison评论7",
					"commentTime" : ISODate("2017-07-06T00:00:00")
				},
				{
					"author"  :  "lison8",
					"content"  :  "lison评论8",
					"commentTime" : ISODate("2017-08-06T00:00:00")
				},
				{
					"author"  :  "lison9",
					"content"  :  "lison评论9",
					"commentTime" : ISODate("2017-09-06T00:00:00")
				}
		]
	    
};
var user2 = {
        "username" : "james",
        "country" : "English",
        "address" : {
                "aCode" : "311000",
                "add" : "地址"
        },
        "favorites" : {
                "movies" : ["复仇者联盟","战狼","雷神1"],
                "cites" : ["西安","东京","上海"]
        },
        "age" : 24,
       "salary":NumberDecimal("7889.09"),
       "lenght" :1.35,
	   "comments" :  [
				{
					"author"  :  "lison1",
					"content"  :  "lison评论1",
					"commentTime" : ISODate("2017-10-06T00:00:00")
				},
				{
					"author"  :  "lison6",
					"content"  :  "lison评论6",
					"commentTime" : ISODate("2017-11-06T05:26:18")
				},
				{
					"author"  :  "lison12",
					"content"  :  "lison评论12",
					"commentTime" : ISODate("2017-11-06T00:00:00")
				}
		]
};
var user3 ={
        "username" : "deer",
        "country" : "japan",
        "address" : {
                "aCode" : "411000",
                "add" : "长沙"
        },
        "favorites" : {
                "movies" : ["肉蒲团","一路向西","倩女幽魂"],
                "cites" : ["东莞","深圳","东京"]
        },
        "age" : 22,
       "salary":NumberDecimal("6666.66"),
       "lenght" :1.85,
	   "comments" :  [
				{
					"author"  :  "lison1",
					"content"  :  "lison评论1",
					"commentTime" : ISODate("2017-10-06T00:00:00")
				},
				{
					"author"  :  "lison22",
					"content"  :  "lison评论6",
					"commentTime" : ISODate("2017-11-06T00:00:00")
				},
				{
					"author"  :  "lison16",
					"content"  :  "lison评论12",
					"commentTime" : ISODate("2017-11-06T00:00:00")
				}
		]
};
var user4 =
{
        "username" : "mark",
        "country" : "USA",
        "address" : {
                "aCode" : "411000",
                "add" : "长沙"
        },
        "favorites" : {
                "movies" : ["蜘蛛侠","钢铁侠","蝙蝠侠"],
                "cites" : ["青岛","东莞","上海"]
        },
        "age" : 20,
       "salary":NumberDecimal("6398.22"),
       "lenght" :1.77
};

var user5 =
{
        "username" : "peter",
        "country" : "UK",
        "address" : {
                "aCode" : "411000",
                "add" : "TEST"
        },
        "favorites" : {
                "movies" : ["蜘蛛侠","钢铁侠","蝙蝠侠"],
                "cites" : ["青岛","东莞","上海"]
        },
       "salary":NumberDecimal("1969.88")
};

db.users.insert(user1);
db.users.insert(user2);
db.users.insert(user3);
db.users.insert(user4);
db.users.insert(user5);

```



