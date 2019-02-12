插入一条记录为例

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

第一步，分析数据结构，构建实体类

User.java

```
import java.math.BigDecimal;

/**
 * @Title:
 * @Author:Zachary
 * @Desc:
 * @Date:2019/2/12
 **/
public class User {
    private String username;
    private String country;
    private int age;
    private BigDecimal salary;
    private float lenght;
    //地址集合
    private Address address;
    //爱好集合
    private Favorites favorites;

    public String getUsername() {
        return username;
    }

    public void setUsername(String username) {
        this.username = username;
    }

    public String getCountry() {
        return country;
    }

    public void setCountry(String country) {
        this.country = country;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    public BigDecimal getSalary() {
        return salary;
    }

    public void setSalary(BigDecimal salary) {
        this.salary = salary;
    }

    public float getLenght() {
        return lenght;
    }

    public void setLenght(float lenght) {
        this.lenght = lenght;
    }

    public Address getAddress() {
        return address;
    }

    public void setAddress(Address address) {
        this.address = address;
    }

    public Favorites getFavorites() {
        return favorites;
    }

    public void setFavorites(Favorites favorites) {
        this.favorites = favorites;
    }

    @Override
    public String toString() {
        return "User{" +
                "username='" + username + '\'' +
                ", country='" + country + '\'' +
                ", age=" + age +
                ", salary=" + salary +
                ", lenght=" + lenght +
                ", address=" + address +
                ", favorites=" + favorites +
                '}';
    }
}
```

Address.java

```
/**
 * @Title:
 * @Author:Zachary
 * @Desc:
 * @Date:2019/2/12
 **/
public class Address {
    private String aCode;
    private String add;

    public String getaCode() {
        return aCode;
    }

    public void setaCode(String aCode) {
        this.aCode = aCode;
    }

    public String getAdd() {
        return add;
    }

    public void setAdd(String add) {
        this.add = add;
    }

    @Override
    public String toString() {
        return "Address{" +
                "aCode='" + aCode + '\'' +
                ", add='" + add + '\'' +
                '}';
    }
}
```

Favorites.java

```
/**
 * @Title:
 * @Author:Zachary
 * @Desc:
 * @Date:2019/2/12
 **/
public class Favorites {
    private List<String> movies;
    private List<String> cites;

    public List<String> getMovies() {
        return movies;
    }

    public void setMovies(List<String> movies) {
        this.movies = movies;
    }

    public List<String> getCites() {
        return cites;
    }

    public void setCites(List<String> cites) {
        this.cites = cites;
    }

    @Override
    public String toString() {
        return "Favorites{" +
                "movies=" + movies +
                ", cites=" + cites +
                '}';
    }
}
```

获取

```
    //客户端
    private MongoClient client;
    //数据库
    private MongoDatabase db;
    //文档集合
    private MongoCollection<User> doc;

    @Before
    public void init() {
        //编解码器的list
        List<CodecRegistry> codecRegistries = new ArrayList<>();
        //list 加入默认的编解码器集合
        codecRegistries.add(MongoClient.getDefaultCodecRegistry());
        //生成一个pojo的编解码器
        CodecRegistry pojoCodecRegistry = CodecRegistries.fromProviders(PojoCodecProvider.builder().automatic(true).build());
        //将list加入pojo的编解码器
        codecRegistries.add(pojoCodecRegistry);
        //通过编解码器的list生成编解码器注册中心
        CodecRegistry registry = CodecRegistries.fromRegistries(codecRegistries);
        //把编解码注册中心放入到MongoClientOptions
        //MongoClientOptions 相当于连接池的配置信息
        MongoClientOptions build = MongoClientOptions.builder().codecRegistry(registry).build();

        ServerAddress serverAddress = new ServerAddress("192.168.111.128", 27022);

        client = new MongoClient(serverAddress,build);
        db = client.getDatabase("lison");
        doc = db.getCollection("users",User.class);
    }
    /* 
    注意：
    MongoCollection<User> doc --> Document变为了User ，pojo模式
    手动的加入pojo的编解码器
    */
```

说明

```
pojo模式，除了插入采用实体类来插入外，以及泛型是实体对象
其他的查询，更新，删除与原生的mongodb开发一样
```

实例代码

```
@Test
    public void testInsert() {
        User user = new User();
        user.setAge(18);
        user.setCountry("OK");
        user.setLenght(1.80f);
        user.setSalary(new BigDecimal("6666.6"));
        user.setUsername("admin");

        Address address = new Address();
        address.setAdd("542189ajsaa");
        address.setCode("0000");
        System.out.println(address);
        user.setAddress(address);

        Favorites favorites = new Favorites();
        favorites.setMovies(Arrays.asList("杀破狼2", "1dushe", "雷神1"));
        favorites.setCites(Arrays.asList("1sh", "1cs", "1zz"));
        user.setFavorites(favorites);

        doc.insertMany(Arrays.asList(user));
    }

    @Test
    public void testFind() {
        //记录数据和打印
        final List<User> ret = new ArrayList<>();
        Block<User> block = new Block<User>() {
            @Override
            public void apply(User user) {
                System.out.println(user);
                ret.add(user);
            }
        };
        //查询一条数据
        Bson eq = eq("username", "admin");
        FindIterable<User> users = doc.find(eq);
        users.forEach(block);
        System.out.println("----->" + String.valueOf(ret.size()));

    }

    @Test
    public void testUpdate() {
        //db.users.updateMany({ "username" : "lison"},{ "$set" : { "age" : 6}},true)
        Bson eq = eq("username", "lison");
        Bson set = set("age", 6);
        UpdateResult result = doc.updateMany(eq, set);
        System.out.println(result.getModifiedCount());

        //db.users.updateMany({ "favorites.cites" : "东莞"}, { "$addToSet" : { "favorites.movies" : { "$each" : [ "小电影2 " , "小电影3"]}}},true)
        Bson eq2 = eq("favorites.cites", "东莞");
        Bson addEachToSet = addEachToSet("favorites.movies", Arrays.asList("小电影2", "小电影3"));
        UpdateResult result2 = doc.updateMany(eq2, addEachToSet);
        System.out.println(result2.getModifiedCount());
    }

    @Test
    public void testDelete() {
        //delete from users where username = ‘lison’
        Bson eq = eq("username", "lison");
        DeleteResult result = doc.deleteMany(eq);
        System.out.println(result.getDeletedCount());

        //delete from users where age >8 and age <25
        Bson gt = gt("age", 8);
        Bson lt = lt("age", 25);
        Bson and = and(gt, lt);
        DeleteResult result2 =  doc.deleteMany(and);
        System.out.println(result2.getDeletedCount());

    }
```

注意：

```
使用pojo模式，插入 属性当中有大写字母出现，比如 aCode 时，发现插入到mongodb中会出现自动丢失现象。

具体原因待解决：
    ...
```



