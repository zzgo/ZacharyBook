分布式锁和事务实战

# 1、分布式锁

引言

为什么要使用锁？

单机里面，可以完美的解决锁和事务

锁的场景

> 多任务环境下，多对一的资源访问
>
> 有状态的资源，会出现不一样的结果（有状态类）

问题本质

> 状态的变迁，需要时间，存在中间态
>
> 在1+1+1=3还未得出结果前，状态还是1，但只是幻象
>
> 其他任务出幻象值1，是错误的结果

明确一个信息

> 并不是所有的线程资源，都无法同时服务多个线程，比如无状态的资源
>
> 无成员变量或者成员变量不存在变化的类，就是无状态类，这种类是线程安全的
>
> 有状态的类也不一定是不安全的，前提：如果状态变化是原子的（即使没有中间变迁过程，变化不需要时间，没有中间态，一样是线程安全的）
>
> 重要的一个概念：动作的原子性
>
> 锁要解决的问题是： 资源数据会不一致
>
> 锁要达成的目标是 ：让资源使用起来，像原子性一样
>
> 锁达成目标的手段：让使用者访问资源时，只能排队，一个一个地去访问资源

锁的思路

> 对资源的访问进行排它处理
>
> 1+1+1=3得出结果之前，不允许别人染指
>
> 所有任务必须有序过独木桥

步骤

> 通过竞争获取锁（①竞争锁-排队）
>
> 任务在对资源操作中（②占有锁-占坑）
>
> 其他任务等待（③任务阻塞）
>
> 直到该任务完成（④释放锁）

目的

> 多个外部线程同时来竞争使用统一资源时，会彼此影响，导致混乱
>
> 将资源的使用做排它性处理，使同一时间，仅一个线程能访问资源

在单机应用里，JVM可以通过以下工具，可协调资源像原子性一样操作

> sychronized： java语言天生支持
>
> lock：jdk有接口标准

分布式锁方案

分布环境下，锁是脱离jvm控制的

分布式环境下，如何协调资源达到原子性的操作？

> sychronized / lock 这些java天然的实现，无法跨JVM发挥作用
>
> 只得去寻求分布式环境里，大家都公认的服务来做见证人，以协调资源
>
> 常见的公证人 ------&gt;  mysql/zk/file/redis
>
> 目标 ----- 通过公证人发出信号，来协调分布式的访问者，排队访问资源
>
> 条件 ----- 任何一个能够提供【是/否】信号量的事物，都可以来做公证人
>
> 陷阱 ----- 发出锁信号量的动作，本身必须是原子性的

|  | 实现思路 | 优点 | 缺点 |
| :--- | :--- | :--- | :--- |
| MySQL | 利用数据库自身提供的锁机制实现，要求数据库支持行级锁 | 实现简单，稳定可靠 | 性能差，无法适应高并发场景；容易出现死锁的情况；无法优雅的实现阻塞式锁； |
| Redis | 基于Redis的setnx命令实现，并通过lua脚本保证解锁时对缓存操作序列的原子性 | 性能好 | 实现相对较复杂，无法优雅的实现阻塞式锁； |
| Zookeeper | 基于zk的节点特性以及watch机制实现基于zk的节点特性以及watch机制实现 | 性能好，稳定可靠性，能较好的实现阻塞式锁 | 实现相对复杂 |

实例基于MySQL的分布式锁的实现

pom.xml

```
<?xml version="1.0" encoding="UTF-8"?>

<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <parent>
        <artifactId>cache</artifactId>
        <groupId>com.zachary</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <modelVersion>4.0.0</modelVersion>

    <artifactId>redis-lock</artifactId>

    <name>redis-lock</name>
    <url>http://www.example.com</url>

    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
        <java.version>1.8</java.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
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
            <version>2.8.1</version>
        </dependency>

        <dependency>
            <groupId>com.mchange</groupId>
            <artifactId>c3p0</artifactId>
            <version>0.9.5-pre10</version>
        </dependency>
        <!-- mysql连接 -->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.34</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-jdbc</artifactId>
            <version>${springframework.version}</version>
        </dependency>
        <!--使用swagger2-->
        <dependency>
            <groupId>io.springfox</groupId>
            <artifactId>springfox-swagger2</artifactId>
            <version>2.6.1</version>
        </dependency>
        <dependency>
            <groupId>io.springfox</groupId>
            <artifactId>springfox-swagger-ui</artifactId>
            <version>2.6.1</version>
        </dependency>
    </dependencies>
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-dependencies</artifactId>
                <version>1.5.3.RELEASE</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>
</project>
```

构建项目结构

![](/assets/fg2.png)

resources文件

application.xml

```
server:
  port: 8090
  compression:
    enabled: true
  connection-timeout: 3000
swagger:
  host: local.dev.com
```

config小的java文件

DataSourceConfig.java

```
package com.zachary.config;

import com.mchange.v2.c3p0.ComboPooledDataSource;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.jdbc.core.JdbcTemplate;
import org.springframework.jdbc.datasource.DataSourceTransactionManager;
import org.springframework.transaction.PlatformTransactionManager;
import org.springframework.transaction.support.TransactionTemplate;

import javax.sql.DataSource;
import java.beans.PropertyVetoException;

@Configuration
public class DataSourceConfig {

    @Bean
    public DataSource dataSource() throws PropertyVetoException {
        ComboPooledDataSource dataSource = new ComboPooledDataSource();
        dataSource.setJdbcUrl("jdbc:mysql://localhost:3306/lock?useUnicode=true&characterEncoding=utf8&autoReconnect=true&allowMultiQueries=true&zeroDateTimeBehavior=convertToNull");
        dataSource.setDriverClass("com.mysql.jdbc.Driver");
        dataSource.setUser("root");
        dataSource.setPassword("zhangqi");

        return dataSource;
    }

    @Bean
    public JdbcTemplate jdbcTemplate(DataSource dataSource) {
        return new JdbcTemplate(dataSource);
    }

    //事务管理器
    @Bean
    DataSourceTransactionManager transactionManager(DataSource dataSource) {//事务管理
        return new DataSourceTransactionManager(dataSource);
    }

    //事务模板
    @Bean
    TransactionTemplate transactionTemplate(PlatformTransactionManager platformTransactionManager) {
        return new TransactionTemplate(platformTransactionManager);
    }

}
```

RedisConfig.java

```
package com.zachary.config;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.data.redis.connection.jedis.JedisConnectionFactory;
import redis.clients.jedis.JedisPoolConfig;

@Configuration
public class RedisConfig {

    @Bean
    public JedisPoolConfig jedisPoolConfig() {
        JedisPoolConfig jedisPoolConfig = new JedisPoolConfig();
        jedisPoolConfig.setMaxIdle(10);
        jedisPoolConfig.setMaxTotal(10000);
        return jedisPoolConfig;
    }

    @Bean
    public JedisConnectionFactory jedisConnectionFactory(JedisPoolConfig jedisPoolConfig)  {
        JedisConnectionFactory jedisConnectionFactory = new JedisConnectionFactory();
        jedisConnectionFactory.setHostName("127.0.0.1");
        jedisConnectionFactory.setPort(6379);
        jedisConnectionFactory.setUsePool(true);
        jedisConnectionFactory.setPoolConfig(jedisPoolConfig);

        return jedisConnectionFactory;
    }
}
```

controller

LockController.java

```
package com.zachary.controller;

import io.swagger.annotations.Api;
import io.swagger.annotations.ApiOperation;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

import javax.annotation.Resource;
import java.util.concurrent.CountDownLatch;
import java.util.concurrent.locks.Lock;

/**
 * @Title:
 * @Author:Zachary
 * @Desc:
 * @Date:2019/1/15
 **/
@RestController
@RequestMapping("lock")
@Api(value = "锁机制" , description = "锁机制说明")
public class LockController {
    private static long count = 20;
    CountDownLatch countDownLatch = new CountDownLatch(5);

    @Resource(name = "mysqlLock")
    private Lock lock;

    @RequestMapping("/sale")
    @ApiOperation(value = "售票")
    public Long query() {
        count = 20;
        countDownLatch = new CountDownLatch(5);
        new PlusThread().start();
        new PlusThread().start();
        new PlusThread().start();
        new PlusThread().start();
        new PlusThread().start();
        return count;
    }

    //    模拟火车窗口买车票
    class PlusThread extends Thread {
        private int amount = 0;

        @Override
        public void run() {
            System.out.println(Thread.currentThread().getName() + "开始售票");
            countDownLatch.countDown();
            if (countDownLatch.getCount() == 0) {
                System.out.println("售票结果");
            }
            try {
                countDownLatch.await();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            while (count > 0) {
                //加锁
                lock.lock();
                try {
                    if (count > 0) {
                        amount++;
                        count--;
                    }
                } finally {
                    //解锁
                    lock.unlock();
                }
                try {
                    Thread.sleep(10);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
            System.out.println(Thread.currentThread().getName() + "售出" + (amount) + "张票");
        }
    }
}
```

lock

MysqlLock.java

```
package com.zachary.lock;

import org.springframework.jdbc.core.JdbcTemplate;
import org.springframework.stereotype.Service;

import javax.annotation.Resource;
import java.util.concurrent.TimeUnit;
import java.util.concurrent.locks.Condition;
import java.util.concurrent.locks.Lock;

/**
 * @Title:
 * @Author:Zachary
 * @Desc:
 * @Date:2019/1/15
 **/
@Service
public class MysqlLock implements Lock {
    private static final int ID_NUM = 1;
    //引入数据库操作模板，就是一些常规的增删改查
    @Resource
    private JdbcTemplate jdbcTemplate;

    @Override
    //加锁
    public void lock() {
        //尝试获取锁,获取到锁，直接返回
        if (tryLock()) {
            return;
        }
        //获取锁失败，线程休眠一会
        try {
            Thread.sleep(10);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        //递归，尝试重新获取锁
        lock();

    }

    @Override
    //尝试获取锁
//    非阻塞式加锁,往数据库写入id为1的数据，能写成功的即加锁成功
    public boolean tryLock() {
        try {
            String sql = "INSERT INTO db_lock(id) VALUE(?)";
            jdbcTemplate.update(sql, ID_NUM);//执行一条sql是一个原子性的操作
        } catch (Exception e) {
            return false;
        }
        return true;
    }


    @Override
    //解锁
    public void unlock() {
        String sql = " DELETE FROM db_lock WHERE id = ?";
        jdbcTemplate.update(sql, ID_NUM);
    }

    @Override
    public boolean tryLock(long time, TimeUnit unit) throws InterruptedException {
        return false;
    }

    @Override
    public void lockInterruptibly() throws InterruptedException {

    }

    @Override
    public Condition newCondition() {
        return null;
    }

}
```

lockApp.java

```
package com.zachary;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class LockApp {
    public static void main(String[] args) {
        SpringApplication.run(LockApp.class, args);
    }
}
```

Swagger2.java

```
package com.zachary;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import springfox.documentation.builders.ApiInfoBuilder;
import springfox.documentation.builders.PathSelectors;
import springfox.documentation.builders.RequestHandlerSelectors;
import springfox.documentation.service.ApiInfo;
import springfox.documentation.spi.DocumentationType;
import springfox.documentation.spring.web.plugins.Docket;
import springfox.documentation.swagger2.annotations.EnableSwagger2;

@Configuration
@EnableSwagger2
public class Swagger2 {
    @Bean
    public Docket createRestApi() {
        return new Docket(DocumentationType.SWAGGER_2)
//                .host(swaggerHost)
                .apiInfo(apiInfo())
//                .pathMapping("/")
    //                .directModelSubstitute(Date.class,String.class)
                .select()
                .apis(RequestHandlerSelectors.basePackage("com.zachary"))
                .paths(PathSelectors.any())
                .build();
    }
    private ApiInfo apiInfo() {
        return new ApiInfoBuilder()
                .title("Zacahry的程序")
                .contact("Zacahry")
                .version("1.0")
                .build();
    }
}
```

MySql作为这个公证人，实现锁---可以作为锁的，他本身的操作必须是原子性的。利用插入一条sql是原子性的操作，且有两个信号量（主键唯一，插入成功，插入失败）

优点：操作简单，缺点：性能太低了，存在一定的不安全性。

实现redis的解决方案

利用redis对的setnx是一个原子性的操作

RedisLock.java

```
package com.zachary.lock;

import org.springframework.data.redis.connection.jedis.JedisConnectionFactory;
import org.springframework.stereotype.Service;
import redis.clients.jedis.Jedis;

import javax.annotation.Resource;
import java.util.Arrays;
import java.util.UUID;
import java.util.concurrent.TimeUnit;
import java.util.concurrent.locks.Condition;
import java.util.concurrent.locks.Lock;

/**
 * @Title:
 * @Author:Zachary
 * @Desc:
 * @Date:2019/1/15
 **/
@Service
public class RedisLock implements Lock {
    private final String KEY = "LOCK_KEY";
    @Resource
    private JedisConnectionFactory factory;

    private ThreadLocal<String> local = new ThreadLocal<>();


    @Override
    public void lock() {
//        尝试获取锁
        if (tryLock()) {
            return;
        }
//        获取锁失败，暂停10ms
        try {
            Thread.sleep(10);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
//        重新获取这把锁
        lock();
    }

    @Override
    public boolean tryLock() {
        String uuid = UUID.randomUUID().toString();
        Jedis jedis = (Jedis) factory.getConnection().getNativeConnection();
        /**
         * key:我们使用key来当锁
         * uuid:唯一标识，这个锁是我加的，属于我
         * NX：设入模式【SET_IF_NOT_EXIST】--仅当key不存在时，本语句的值才设入
         * PX：给key加有效期
         * 1000：有效时间为 1 秒
         */
        String ret = jedis.set(KEY, uuid, "NX" , "PX" , 1000);
        if ("OK".equals(ret)) {
            local.set(uuid);
            return true;
        }
        return false;
    }
    //错误的解锁姿势
    public void unlockWrong() {
        Jedis jedis = (Jedis) factory.getConnection().getNativeConnection();
        String uuid = jedis.get(KEY);
        // 由于设置了过期时间，--为什么要设置过期时间呢？---防止造成死锁。---使用了过期时间会出现了什么问题呢？
        // 删除键的时候，key过期了。这个时候你删除的键其实是先一个线程获取到的键。你的操作造成了误删除。
        //根本原因是因为你引入了过期时间这个操作。造成了删除键不是原子性的操作。
        //所以我们使用lua脚本来执行
        if (null != uuid && uuid.equals(local.get())) {
            //失效了
            //删除键
            jedis.del(KEY);
        }
    }

    @Override
    public void unlock() {
        Jedis jedis = (Jedis) factory.getConnection().getNativeConnection();
        String uuid = jedis.get(KEY);
        String script = "if redis.call(\"get\",KEYS[1]) == ARGV[1] then \n" +
                "    return redis.call(\"del\",KEYS[1]) \n" +
                "else \n" +
                "    return 0 \n" +
                "end";
        jedis.eval(script, Arrays.asList(KEY), Arrays.asList(local.get()));
    }

    @Override
    public boolean tryLock(long time, TimeUnit unit) throws InterruptedException {
        return false;
    }


    @Override
    public void lockInterruptibly() throws InterruptedException {

    }


    @Override
    public Condition newCondition() {
        return null;
    }
}
```

注意：

**lua 脚本为什么是原子性的          
**

redis是单线程执行指令的，因此内部不存在线程竞争

![](/assets/5tg.png)

（1）服务器A依次发送了ab指令到redis

（2）服务器B依次发送了cd指令到redis

（3）两台机器同向redis发送的四条指令，最终在指令队列里顺序是：acbd

（4）可以看到，服务器A发送的ab两条指令，中间穿插了c指令，破坏了其完整性，因此，ab两条指令不是原子的

（5）lua脚本，被放进队列时，ab指令是放在一起的，因为ab会顺序一起被执行，成为了原子性动作

# 二、事务

概念

锁的问题

> 多对一问题，对个线程访问同一个资源，造成资源状态不一致

事务问题

> 一对多，一个线程里面执行多条sql语句，要么commit，要么rollback，即某条sql失败，整个业务回滚，失去意义。

思考

数据库中的事务的实现方式

> 第一步、service执行一个操作，需要执行N条sql（一条sql是一个原子性的操作）
>
> 第二步、数据库内部怎们实现事务的呢？
>
> 第三步、所有的sql执行完成之前，都是以副本的形式存在的。
>
> ![](/assets/hd5.png)
>
> 副本：数据库隔离数据，有隔离级别限制。
>
> 第四步、commit提交，业务线程把指令传给数据库，数据库把副本数据转正
>
> 第五步、rollback，发出指令，将数据库的副本数据丢掉

事务管理器

在jdbc规范里，定义这样的一个标准

定义了三个标准的接口：启事务（启副本）、副本转正，副本丢弃

（百度事务的一些概念，隔离级别，脏读，幻读...）

**分布式事务**

多台数据库额执行sql，也想要到达一致性的标准，多台一起要么commit，要么rollback

参照单机事务的模型，在分布式事务中进行思路沿袭，也想通过三个标准接口的模式来完成（启事务/commit/rollback）

按照这个思路，有人就做了这个事情，X/Open组织提出了分布式事务规范---XA

XA的核心，便是全局事务，再利用xa的二阶段提交协议，与各分布式数据库进交互，分准备和提交两个阶段

![](/assets/h66.png)

实战操练



