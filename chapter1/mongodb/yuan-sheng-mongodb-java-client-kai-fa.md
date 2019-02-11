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
```



