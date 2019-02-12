引入pom文件

```
<dependency>
    <groupId>org.springframework.data</groupId>
    <artifactId>spring-data-mongodb</artifactId>
    <version>1.10.18.RELEASE</version>
</dependency>

<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-test</artifactId>
    <version>4.3.2.RELEASE</version>
    <scope>test</scope>
</dependency>

<!-- 日志相关依赖 -->
<dependency>
    <groupId>org.slf4j</groupId>
    <artifactId>slf4j-api</artifactId>
    <version>1.7.10</version>
</dependency>
<dependency>
    <groupId>ch.qos.logback</groupId>
    <artifactId>logback-classic</artifactId>
    <version>1.1.2</version>
</dependency>
<dependency>
    <groupId>ch.qos.logback</groupId>
    <artifactId>logback-core</artifactId>
    <version>1.1.2</version>
</dependency>


tips:
spring-data-mongodb的最新版本是2.x.x，如果是spring为5.0版本以上的才推荐使用；
spring-data-mongodb的1.10.18版本基于spring4.3.x开发，但是默认依赖的mongodb驱动为2.14.3，可以将mongodb的驱动设置为3.5.0的版本；
spring-data-mongodb一般使用pojo的方式开发；
```

配置applicationContext.xml

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:context="http://www.springframework.org/schema/context"
    xmlns:mvc="http://www.springframework.org/schema/mvc" xmlns:mongo="http://www.springframework.org/schema/data/mongo"
    xsi:schemaLocation="http://www.springframework.org/schema/mvc http://www.springframework.org/schema/mvc/spring-mvc-4.0.xsd
        http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.0.xsd
        http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.0.xsd
        http://www.springframework.org/schema/data/mongo http://www.springframework.org/schema/data/mongo/spring-mongo.xsd">

    <!-- <context:property-placeholder location="classpath:/com/myapp/mongodb/config/mongo.properties" 
        /> -->
    <!-- mongodb连接池配置 -->
    <mongo:mongo-client host="192.168.111.128" port="27022">
        <mongo:client-options 
             write-concern="ACKNOWLEDGED"
              connections-per-host="100"
              threads-allowed-to-block-for-connection-multiplier="5"
              max-wait-time="120000"
              connect-timeout="10000"/> 
    </mongo:mongo-client>

    <!-- mongodb数据库工厂配置 -->
    <mongo:db-factory dbname="lison" mongo-ref="mongo" />

      <mongo:mapping-converter base-package="com.zachary.entity">
      <mongo:custom-converters>
          <mongo:converter>
            <bean class="com.zachary.convert.BigDecimalToDecimal128Converter"/>
          </mongo:converter>
          <mongo:converter>
            <bean class="com.zachary.convert.Decimal128ToBigDecimalConverter"/>
          </mongo:converter>
    </mongo:custom-converters>

    </mongo:mapping-converter>

    <!-- mongodb模板配置 -->
    <bean id="anotherMongoTemplate" class="org.springframework.data.mongodb.core.MongoTemplate">
        <constructor-arg name="mongoDbFactory" ref="mongoDbFactory" />
         <constructor-arg name="mongoConverter" ref="mappingConverter"/>
        <property name="writeResultChecking" value="EXCEPTION"></property>
    </bean>



</beans>
```

logback.xml

```
<?xml version="1.0" encoding="UTF-8"?>
<!--
scan：当此属性设置为true时，配置文件如果发生改变，将会被重新加载，默认值为true。
scanPeriod：设置监测配置文件是否有修改的时间间隔，如果没有给出时间单位，默认单位是毫秒当scan为true时，此属性生效。默认的时间间隔为1分钟。
debug：当此属性设置为true时，将打印出logback内部日志信息，实时查看logback运行状态。默认值为false。
-->
<configuration scan="false" scanPeriod="60 seconds" debug="false">
    <!-- 定义日志的根目录 -->
<!--     <property name="LOG_HOME" value="/app/log" /> -->
    <!-- 定义日志文件名称 -->
    <property name="appName" value="netty"></property>
    <!-- ch.qos.logback.core.ConsoleAppender 表示控制台输出 -->
    <appender name="stdout" class="ch.qos.logback.core.ConsoleAppender">
        <Encoding>UTF-8</Encoding>
        <!--
        日志输出格式：%d表示日期时间，%thread表示线程名，%-5level：级别从左显示5个字符宽度
        %logger{50} 表示logger名字最长50个字符，否则按照句点分割。 %msg：日志消息，%n是换行符
        -->
        <encoder>
            <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %logger{50} - %msg%n</pattern>
        </encoder>
    </appender>

    <!-- 滚动记录文件，先将日志记录到指定文件，当符合某个条件时，将日志记录到其他文件 -->  
    <appender name="appLogAppender" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <Encoding>UTF-8</Encoding>
        <!-- 指定日志文件的名称 -->  
        <file>cache-demo2.log</file>
        <!--
        当发生滚动时，决定 RollingFileAppender 的行为，涉及文件移动和重命名
        TimeBasedRollingPolicy： 最常用的滚动策略，它根据时间来制定滚动策略，既负责滚动也负责出发滚动。
        -->
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <!--
            滚动时产生的文件的存放位置及文件名称 %d{yyyy-MM-dd}：按天进行日志滚动 
            %i：当文件大小超过maxFileSize时，按照i进行文件滚动
            -->
            <fileNamePattern>${appName}-%d{yyyy-MM-dd}-%i.log</fileNamePattern>
            <!-- 
            可选节点，控制保留的归档文件的最大数量，超出数量就删除旧文件。假设设置每天滚动，
            且maxHistory是365，则只保存最近365天的文件，删除之前的旧文件。注意，删除旧文件是，
            那些为了归档而创建的目录也会被删除。
            -->
            <MaxHistory>365</MaxHistory>
            <!-- 
            当日志文件超过maxFileSize指定的大小是，根据上面提到的%i进行日志文件滚动 注意此处配置SizeBasedTriggeringPolicy是无法实现按文件大小进行滚动的，必须配置timeBasedFileNamingAndTriggeringPolicy
            -->
            <timeBasedFileNamingAndTriggeringPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedFNATP">
                <maxFileSize>100MB</maxFileSize>
            </timeBasedFileNamingAndTriggeringPolicy>
        </rollingPolicy>
        <!--
        日志输出格式：%d表示日期时间，%thread表示线程名，%-5level：级别从左显示5个字符宽度 %logger{50} 表示logger名字最长50个字符，否则按照句点分割。 %msg：日志消息，%n是换行符
        -->     
        <encoder>
            <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} [ %thread ] - [ %-5level ] [ %logger{50} : %line ] - %msg%n</pattern>
        </encoder>
    </appender>

    <!-- 
    logger主要用于存放日志对象，也可以定义日志类型、级别
    name：表示匹配的logger类型前缀，也就是包的前半部分
    level：要记录的日志级别，包括 TRACE < DEBUG < INFO < WARN < ERROR
    additivity：作用在于children-logger是否使用 rootLogger配置的appender进行输出，false：表示只用当前logger的appender-ref，true：表示当前logger的appender-ref和rootLogger的appender-ref都有效
    -->

<!--     <logger name="edu.hyh" level="info" additivity="true">
        <appender-ref ref="appLogAppender" />
    </logger> -->

    <!-- 
    root与logger是父子关系，没有特别定义则默认为root，任何一个类只会和一个logger对应，
    要么是定义的logger，要么是root，判断的关键在于找到这个logger，然后判断这个logger的appender和level。 
    -->

    <logger name="org.springframework.beans.factory.support" level="info" additivity="true">

    </logger>

    <root level="debug">
        <appender-ref ref="stdout" />
        <appender-ref ref="appLogAppender" />
    </root>
</configuration>
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

说明

```
WriteResult updateMulti(Query query, Update update, Class<?> class);
query:筛选条件
update：更新键值对
class：实体类
WriteResult remove(Query query, Class<?> class);
query:筛选条件
class：实体类
<T> List<T> find(Query query, Class<T> class);
query:查询条件
class：实体类

重要的包：
import static org.springframework.data.mongodb.core.query.Criteria.*;//查询
import static org.springframework.data.mongodb.core.query.Query.*;//查询
import static org.springframework.data.mongodb.core.query.Update.*;//更新
```



