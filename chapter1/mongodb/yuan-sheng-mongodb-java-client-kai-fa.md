原生的java客户端开发

pom.xml

```
<dependency>
    <groupId>org.mongodb</groupId>
    <artifactId>mongo-java-driver</artifactId>
    <version>3.5.0</version>
</dependency>
```

获取

```
    // 数据库
    private MongoDatabase db;

    // 文档集合
    private MongoCollection<Document> doc;

    //连接客户端 （内置连接池）
    private MongoClient client;


    @Before
    public void init() {
        //拿到客户端实例
        client = new MongoClient("192.168.111.128", 27022);//host，port
        //拿到数据库
        db = client.getDatabase("lison");//lison为创建时的数据库名称
        //拿到文档集合表
        doc = db.getCollection("users");//哪一个表名==文档，创建时的名称一致
    }
```

插入一条数据

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

```
    //插入一条数据
    @Test
    public void insertDemo() {
        Document doc1 = new Document();
        doc1.put("username", "zachary");
        doc1.put("country", "HK");
        doc1.put("age", 18);
        doc1.put("lenght", 1.77f);
        doc1.put("salary", new BigDecimal("9999.63"));// 存金额，使用bigdecimal这个数据类型 BigDecimal(String类型)

        //地址map
        Map<String, Object> mapAddres = new HashMap<>();
        mapAddres.put("aCode", "xxxx000");
        mapAddres.put("add", "d11pff");
        doc1.put("address", mapAddres);

        //爱好map
        Map<String, Object> mapFavorites = new HashMap<>();
        mapFavorites.put("movies", Arrays.asList("杀破狼2", "1dushe", "雷神1"));
        mapFavorites.put("cites", Arrays.asList("1sh", "1cs", "1zz"));
        doc1.put("favorites", mapFavorites);

        doc.insertMany(Arrays.asList(doc1));
    }
    //查询
    @Test
    public void testFind() {
        final List<Document> ret = new ArrayList<>();
        //block接口专门用来处理查询出来的数据
        Block<Document> printBlock = new Block<Document>() {
            @Override
            public void apply(Document document) {
                System.out.println(document.toJson());
                ret.add(document);
            }
        };
        //select * from users  where favorites.cites has "东莞"、"东京"
        //db.users.find({ "favorites.cites" : { "$all" : [ "东莞" , "东京"]}})
        Bson all = all("favorites.cites", Arrays.asList("东莞", "东京"));//定义过滤器，喜欢的城市中要包含“东莞”、"东京"
        FindIterable<Document> find = doc.find(all);
        find.forEach(printBlock);
        System.out.println(String.valueOf(ret.size()));
        ret.removeAll(ret);

        //select * from users  where username like '%s%' and (contry= English or contry = USA)
        // db.users.find({ "$and" : [ { "username" : { "$regex" : ".*s.*"}} , { "$or" : [ { "country" : "English"} , { "country" : "USA"}]}]})
        String regexStr = ".*s.*";
        Bson regex = regex("username", regexStr);//定义过滤器,username like "%s%"
        Bson or = or(eq("country", "English"), eq("country", "USA"));//定义过滤器，(country= English or country = USA)
        Bson and = and(regex, or);
        FindIterable<Document> find2 = doc.find(and);
        find2.forEach(printBlock);
        System.out.println(String.valueOf(ret.size()));
        ret.removeAll(ret);
        // 喜欢杀破狼2的用户，
        // db.users.find({"favorites.movies":"杀破狼2"})
        Bson eq = eq("favorites.movies", "杀破狼2");
        FindIterable<Document> find3 = doc.find(eq);
        find3.forEach(printBlock);
        System.out.println(String.valueOf(ret.size()));
    }
    
    //更新
    @Test
    public void testUpdate() {
        //update  users  set age=6 where username = 'lison'
        //db.users.updateMany({ "username" : "lison"},{ "$set" : { "age" : 6}},true)
        Bson eq = eq("username", "lison");
        Bson set = set("age", 6);
        UpdateResult updateMany = doc.updateMany(eq, set);
        System.out.println("------------------>" + String.valueOf(updateMany.getModifiedCount()));

        //update users  set favorites.movies add "小电影2 ", "小电影3" where favorites.cites  has "东莞"
        //db.users.updateMany({ "favorites.cites" : "东莞"}, { "$addToSet" : { "favorites.movies" : { "$each" : [ "小电影2 " , "小电影3"]}}},true)
        Bson eq2 = eq("favorites.cites", "东莞");
        Bson addEachToSet = addEachToSet("favorites.movies", Arrays.asList("小电影2 ", "小电影3"));
        UpdateResult updateMany2 = doc.updateMany(eq2, addEachToSet);
        System.out.println("------------------>" + String.valueOf(updateMany2.getModifiedCount()));


        //年龄等于18岁的，添加一部电影"成人电影"
        //db.users.updateMany({"age":18},{"$addToSet":{"favorites.movies":{"$each":["成人电影"]}}})
        Bson eq3 = eq("age", 18);
        Bson addEachToSet2 = addEachToSet("favorites.movies", Arrays.asList("成人电影4"));
        UpdateResult updateMany3 = doc.updateMany(eq3, addEachToSet2);
        System.out.println("------------------>" + String.valueOf(updateMany3.getModifiedCount()));

    }
    //删除
    @Test
    public void testDelete() {
        //删除名字为lison的user
        //delete from users where username = ‘lison’
        //db.users.deleteMany({ "username" : "lison"} )

        Bson eq = eq("username", "lison");
        DeleteResult deleteMany = doc.deleteMany(eq);
        System.out.println("------------------>" + String.valueOf(deleteMany.getDeletedCount()));//打印受影响的行数

        //删除年龄大于8小于25的user
        //delete from users where age >8 and age <25
        //db.users.deleteMany({"$and" : [ {"age" : {"$gt": 8}} , {"age" : {"$lt" : 25}}]})

        Bson gt = gt("age", 8);
        Bson lt = lt("age", 25);
        Bson and = and(gt, lt);
        DeleteResult deleteMany2 = doc.deleteMany(and);
        System.out.println("------------------>" + String.valueOf(deleteMany2.getDeletedCount()));//打印受影响的行数

    }
```

总结

```
主要使用这两个包里面的方法进行操作

import static com.mongodb.client.model.Filters.*;
import static com.mongodb.client.model.Updates.*;

插入一条记录
    主要是构建document
查询一条记录，更新，删除
    Bson类，采用的建造者模式，构建查询语句

涉及到 and or eq lt gt addEachToSet ...等方法

```



