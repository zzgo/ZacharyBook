## 基于Redis设计的投票网站实战

#### 需求

* 用户可以发表文章,发表时默认给自己的文章投了一票
* 用户在查看网站时可以按评分进行排列查看
* 用户也可以按照文章发布时间进行排序
* 为节约内存，一篇文章发表后，7天内可以投票,7天过后就不能再投票了
* 为防止同一用户多次投票，用户只能给一篇文章投一次票

#### 数据库设计

文章基本信息表 t\_article

| article\_id | votes | link | title | content | post\_time | user\_id |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |


文章票数与分值表 t\_vote\_data

| article\_id | votes | scores |
| :--- | :--- | :--- |


文章投票详表 t\_vote\_details

| article\_id | vote\_time | user\_id |
| :--- | :--- | :--- |


#### 投票网站应用场景会使用到的Redis相关指令如下

* HASH类型命令：hset  hincrBy  hgetAll   expire
* SET集合命令：sadd  smembers
* ZSET集合命令：zadd   zscore  zincrby  zrevrange

```
hset key feild value key的字段feild的值为value
hincrby key feild incr key的字段feild的值加incr,增加
hgetAll key 获取字段名和对应的值
expire key seconds 设置过期时间 
sadd key member[member...] 为key设置值 可以是多个
smembers key 获取key的所有值
zadd key score member[ score member ...] key添加值以及对应的分数，可以添加多个
zscore key member 获取key下面的member元素的分数
zincrby key incr member key下面的member元素的分数加incr
zrevrange key start stop 获取从start开始到stop的元素，是有序的，并且进行反转排序
```

#### Redis的key设计思路

Key-Value键值对：set key value

key的设计一般以业务，功能模块或者表名开头，后跟主键（或是能表示数据唯一性的值）

比如：用户模块，其中用户ID 001，用户名称admin，那么key的设计：

set user:001:name admin key为user:001:name

#### 记录已投票的用户，防重投

已对001文章投过票的用户，使用set存储（无序，不能重复）

![](/assets/jksadksadk89932.png)

#### 记录文章分值

使用ZSET记录文章投票分数，按文章发布的时间戳（有序列，不能重复）

![](/assets/489jfkaj.png)

#### Redis缓存设计

![](/assets/9808asjidjak.png)

![](/assets/1321209asdopqwe.png)

#### 代码环节

##### pom.xml

```
<!-- 单元测试相关依赖 -->
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-test</artifactId>
            <version>4.3.2.RELEASE</version>
            <scope>test</scope>
        </dependency>
        <!-- config jedis data and client jar -->
        <dependency>
            <groupId>org.springframework.data</groupId>
            <artifactId>spring-data-redis</artifactId>
            <version>1.7.1.RELEASE</version>
        </dependency>

        <dependency>
            <groupId>redis.clients</groupId>
            <artifactId>jedis</artifactId>
            <version>2.7.2</version>
        </dependency>

        <dependency>
            <groupId>commons-lang</groupId>
            <artifactId>commons-lang</artifactId>
            <version>2.6</version>
        </dependency>
        <!--阿里巴巴fastjson-->
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>fastJson</artifactId>
            <version>1.2.17</version>
        </dependency>
```

##### applicationContext.xml

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:tx="http://www.springframework.org/schema/tx"
       xsi:schemaLocation="
      http://www.springframework.org/schema/beans
      http://www.springframework.org/schema/beans/spring-beans-4.2.xsd
      http://www.springframework.org/schema/context
      http://www.springframework.org/schema/context/spring-context-4.2.xsd
      http://www.springframework.org/schema/tx 
      http://www.springframework.org/schema/tx/spring-tx-3.2.xsd">

    <!--<context:property-placeholder location="classpath:config.properties"/>-->

    <context:component-scan base-package="com.*">
    </context:component-scan>

</beans>
```

##### Constants.java

```
public class Constants {
    public static final int ONE_WEEK_IN_SECONDS = 7 * 86400;  //文章发布7天后失效，不能投票
    public static final int VOTE_SCORE = 400;//获取一票后文章分值加400
    public static final int ARTICLES_PER_PAGE = 25; //分页查询每页显示25条
}
```

##### RedisArticleService.java

```
public interface RedisArticleService {
    //发布文章
    String postArticle(String title, String content, String link, String userId);

    //查询文章
    Map<String, String> hgetAll(String key);

    //文章点赞
    void articleVote(String userId, String articleId);

    //查询文章中某一个属性值
    String hget(String key, String votes);

    //获取文章列表，带分页
    List<Map<String, String>> getArticles(int page, String key);
}
```

##### RedisArticleServiceImpl.java

```
@Service
public class RedisArticleServiceImpl implements RedisArticleService {
    @Resource
    private JedisUtil jedisService;

    /**
     * 发布文章
     *
     * @param title   标题
     * @param content 内容
     * @param link    链接
     * @param userId  用户id
     * @return
     */
    @Override
    public String postArticle(String title, String content, String link, String userId) {
        //模拟生成文章主键
        String articleId = String.valueOf(jedisService.incr("article:"));
        //时间戳
        long now = System.currentTimeMillis() / 1000;
        //生成文章属性
        Map<String, String> articleMap = new HashMap<>();
        articleMap.put("articleId", articleId);
        articleMap.put("title", title);
        articleMap.put("content", content);
        articleMap.put("postTime", String.valueOf(now));
        articleMap.put("link", link);
        articleMap.put("userId", userId);
        articleMap.put("votes", "1");
        //文章的redis的key 格式 为 article:id  -> article:1
        String articleKey = "article:" + articleId;
        //保存文章信息
        jedisService.hmset(articleKey, articleMap);

        //点赞记录表，记录点赞用户有哪些，并且不能重复，无序
        String voted = "voted:" + articleId;
        jedisService.sadd(voted, userId);
        jedisService.expire(voted, Constants.ONE_WEEK_IN_SECONDS);

        //记录文章点赞数，可以排序，评分是时间戳+400（每一个赞）
        jedisService.zadd("score:info", now + Constants.VOTE_SCORE, articleKey);
        //保存时间戳，可以按时间排序的文章列表
        jedisService.zadd("time:", now, articleKey);
        return articleId;
    }

    /**
     * @param key
     * @return
     */
    @Override
    public Map<String, String> hgetAll(String key) {
        return jedisService.hgetAll(key);
    }

    /**
     * 文章点赞
     *
     * @param userId
     * @param articleId
     */
    @Override
    public void articleVote(String userId, String articleId) {
        String articleKey = "article:" + articleId;
        long endtime = System.currentTimeMillis() / 1000 - Constants.ONE_WEEK_IN_SECONDS;

        if (endtime > jedisService.zscore("time:", articleKey)) {
            return;
        }
        //加入点赞表
        if (jedisService.sadd("voted:" + articleId, userId) == 1) {
            jedisService.zincrby("score.info", Constants.VOTE_SCORE, articleKey);
            jedisService.hincrby(articleKey, "votes", 1L);
        }
    }

    /**
     * @param key
     * @param votes
     * @return
     */
    @Override
    public String hget(String key, String votes) {
        return jedisService.hget(key, votes);
    }

    /**
     * 查询文章
     *
     * @param page
     * @param key
     * @return
     */
    @Override
    public List<Map<String, String>> getArticles(int page, String key) {
        int start = (page - 1) * Constants.ARTICLES_PER_PAGE;
        int end = start + Constants.ARTICLES_PER_PAGE - 1;
        Set<String> set = jedisService.zrevrange(key, start, end);
        List<Map<String, String>> maps = new ArrayList<>();
        for (String id : set) {
            Map<String, String> map = hgetAll(id);
            map.put("id", id);
            maps.add(map);
        }

        return maps;
    }
}
```

##### JedisUtil.java

这个类是操作redis的工具类，由于文件过大，在网上也可以找到，故自行解决

基于连接池，粘贴一部分代码

```
    private JedisPool pool = null;//连接池
    private String ip = "192.168.111.128";//主机ip
    private int port = 6379;//端口
    private String auth = "zhangqi";//密码

    public JedisUtil() {
        //传入ip和端口号构建redis 连接
        if (pool == null) {
            JedisPoolConfig config = new JedisPoolConfig();
            config.setMaxTotal(500);
            config.setMaxIdle(5);
            config.setMaxWaitMillis(100);
            config.setTestOnBorrow(true);
            pool = new JedisPool(config, this.ip, this.port, 100000, this.auth);
        }
    }
    /**
     * 通过key 对value进行加?+1操作,当value不是int类型时会返回错误,当key不存在是则value=1
     *
     * @param key
     * @return 加1后的结果
     */
    public Long incr(String key) {
        Jedis jedis = null;
        Long res = null;
        try {
            jedis = pool.getResource();
            res = jedis.incr(key);
        } catch (Exception e) {
            pool.returnBrokenResource(jedis);
            e.printStackTrace();
        } finally {
            returnResource(pool, jedis);
        }
        return res;
    }
    /**
     * 通过key给field设置指定的?,如果key不存,则先创建
     *
     * @param key
     * @param field 字段
     * @param value
     * @return 如果存在返回0 异常返回null
     */
    public Long hset(String key, String field, String value) {
        Jedis jedis = null;
        Long res = null;
        try {
            jedis = pool.getResource();
            res = jedis.hset(key, field, value);
        } catch (Exception e) {
            pool.returnBrokenResource(jedis);
            e.printStackTrace();
        } finally {
            returnResource(pool, jedis);
        }
        return res;
    }
    /**
     * 通过key同时设置 hash的多个field
     *
     * @param key
     * @param hash
     * @return 返回OK 异常返回null
     */
    public String hmset(String key, Map<String, String> hash) {
        Jedis jedis = null;
        String res = null;
        try {
            jedis = pool.getResource();
            res = jedis.hmset(key, hash);
        } catch (Exception e) {
            pool.returnBrokenResource(jedis);
            e.printStackTrace();
        } finally {
            returnResource(pool, jedis);
        }
        return res;
    }
    /**
     * 通过key  field 获取指定 value
     *
     * @param key
     * @param field
     * @return 没有返回null
     */
    public String hget(String key, String field) {
        Jedis jedis = null;
        String res = null;
        try {
            jedis = pool.getResource();
            res = jedis.hget(key, field);
        } catch (Exception e) {
            pool.returnBrokenResource(jedis);
            e.printStackTrace();
        } finally {
            returnResource(pool, jedis);
        }
        return res;
    }
    /**
     * 通过key给指定的field的value加上给定的?
     *
     * @param key
     * @param field
     * @param value
     * @return
     */
    public Long hincrby(String key, String field, Long value) {
        Jedis jedis = null;
        Long res = null;
        try {
            jedis = pool.getResource();
            res = jedis.hincrBy(key, field, value);
        } catch (Exception e) {
            pool.returnBrokenResource(jedis);
            e.printStackTrace();
        } finally {
            returnResource(pool, jedis);
        }
        return res;
    }
    ....
```

#### 测试用例

```
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration("classpath:applicationContext.xml")
@FixMethodOrder(MethodSorters.NAME_ASCENDING)
public class RedisArticleImplTest {
    @Resource(name = "redisArticleServiceImpl")
    private RedisArticleService redisArticleService;

    @Test
    public void postArticle() throws Exception {
        String userId = "010"; //用户ID 001
        String title = "The road to west";
        String content = "About body and mental health";
        String link = "www.miguo.com";
        //发布文章，返回文章ID
        String articleId = redisArticleService.postArticle(title, content, link, userId);
        System.out.println("刚发布了一篇文章，文章ID为: " + articleId);
        System.out.println("文章所有属性值内容如下:");
        Map<String, String> articleData = redisArticleService.hgetAll("article:" + articleId);
        for (Map.Entry<String, String> entry : articleData.entrySet()) {
            System.out.println("  " + entry.getKey() + ": " + entry.getValue());
        }
        System.out.println();
    }


    @Test
    public void articleVote() throws Exception {
        String userId = "006";
        String articleId = "2";

        System.out.println("开始对文章" + "article:" + articleId + "进行投票啦~~~~~");
        //cang用户给James的文章投票
        redisArticleService.articleVote(userId, articleId);//article:1
        //投票完后，查询当前文章的投票数votes
        String votes = redisArticleService.hget("article:" + articleId, "votes");

        System.out.println("article:" + articleId + "这篇文章的投票数从redis查出来结果为: " + votes);
    }


    @Test
    public void getArticles() throws Exception {
        int page = 1;
        String key = "score:info";
        System.out.println("查询当前的文章列表集合为：");
        List<Map<String, String>> articles = redisArticleService.getArticles(page, key);
        for (Map<String, String> article : articles) {
            System.out.println("  id: " + article.get("id"));
            for (Map.Entry<String, String> entry : article.entrySet()) {
                if (entry.getKey().equals("id")) {
                    continue;
                }
                System.out.println("    " + entry.getKey() + ": " + entry.getValue());
            }
        }
    }

}
```



