引入pom文件

```
<dependency>
    <groupId>org.springframework.data</groupId>
    <artifactId>spring-data-mongodb</artifactId>
    <version>1.10.18.RELEASE</version>
</dependency>




tips:
spring-data-mongodb的最新版本是2.x.x，如果是spring为5.0版本以上的才推荐使用；
spring-data-mongodb的1.10.18版本基于spring4.3.x开发，但是默认依赖的mongodb驱动为2.14.3，可以将mongodb的驱动设置为3.5.0的版本；
spring-data-mongodb一般使用pojo的方式开发；
```







代码示例

```
import com.mongodb.WriteResult;
import com.zachary.entity.Address;
import com.zachary.entity.Favorites;
import com.zachary.entity.User;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.data.mongodb.core.MongoOperations;
import org.springframework.data.mongodb.core.query.Criteria;
import org.springframework.data.mongodb.core.query.Query;
import org.springframework.data.mongodb.core.query.Update;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;

import javax.annotation.Resource;
import java.math.BigDecimal;
import java.util.Arrays;
import java.util.List;

import static org.springframework.data.mongodb.core.query.Criteria.*;
import static org.springframework.data.mongodb.core.query.Query.*;
import static org.springframework.data.mongodb.core.query.Update.*;

/**
 * @Title:
 * @Author:Zachary
 * @Desc:
 * @Date:2019/2/12
 **/
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration("classpath:applicationContext.xml")
public class QuickStartSpringPojoTest {
    @Resource
    private MongoOperations template;

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
        address.setaCode("0000");
        user.setAddress(address);

        Favorites favorites = new Favorites();
        favorites.setMovies(Arrays.asList("杀破狼2", "1dushe", "雷神1"));
        favorites.setCites(Arrays.asList("1sh", "1cs", "1zz"));
        user.setFavorites(favorites);

        template.insertAll(Arrays.asList(user));
    }

    @Test
    public void testFind() {
        //db.users.find({ "favorites.cites" : { "$all" : [ "东莞" , "东京"]}})
        Criteria criteria = where("favorites.cites").all(Arrays.asList("东莞", "东京"));
        List<User> userList = template.find(query(criteria), User.class);
        System.out.println(userList.size());
        // db.users.find({ "$and" : [ { "username" : { "$regex" : ".*s.*"}} , { "$or" : [ { "country" : "English"} , { "country" : "USA"}]}]})
        Criteria regex = where("username").regex(".*s.*");
        Criteria ora = where("country").is("English");
        Criteria orb = where("country").is("USA");
        Criteria or = new Criteria().orOperator(ora, orb);
        Criteria and = new Criteria().andOperator(regex, or);
        List<User> userList2 = template.find(query(and), User.class);
        System.out.println(userList2.size());
    }

    @Test
    public void testUpdate() {
        //update  users  set age=6 where username = 'lison'
        Criteria eq = where("username").is("lison");
        Update update = update("age", 6);
        WriteResult result = template.updateMulti(query(eq), update, User.class);
        System.out.println(result.getN());
        //update users  set favorites.movies add "小电影2 ", "小电影3" where favorites.cites  has "东莞"
        Query query = query(where("favorites.cites").is("东莞"));
        Update update2 = new Update().addToSet("favorites.movies").each("小电影2", "小电影3");
        WriteResult result2 = template.updateMulti(query, update2, User.class);
        System.out.println(result2.getN());
    }

    @Test
    public void testDelete() {
        //delete from users where username = ‘lison’
        Query query = query(where("username").is("lison"));
        WriteResult result = template.remove(query, User.class);
        System.out.println(result.getN());

        //delete from users where age >8 and age <25
        Query query1 = query(new Criteria().andOperator(where("age").gt(8), where("age").lt(25)));
        WriteResult result1 = template.remove(query1, User.class);
        System.out.println(result1.getN());
    }

}
```



