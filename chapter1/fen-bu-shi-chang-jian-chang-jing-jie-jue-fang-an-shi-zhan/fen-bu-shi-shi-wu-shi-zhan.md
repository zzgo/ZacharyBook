单机下的分布式事务

TransController.java

```
package com.zachary.controller;

import io.swagger.annotations.Api;
import io.swagger.annotations.ApiOperation;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.dao.DataAccessException;
import org.springframework.jdbc.core.JdbcTemplate;
import org.springframework.jdbc.datasource.DataSourceTransactionManager;
import org.springframework.transaction.TransactionStatus;
import org.springframework.transaction.support.DefaultTransactionDefinition;
import org.springframework.transaction.support.TransactionCallbackWithoutResult;
import org.springframework.transaction.support.TransactionTemplate;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;

/**
 * @Title:
 * @Author:Zachary
 * @Desc:
 * @Date:2019/1/18
 **/
@Api("事务机制")
@RestController
public class TransController {
    //jdbc模板
    @Autowired
    private JdbcTemplate jdbcTemplate;
    // 事务管理器，事务管理器模板
    @Autowired
    private TransactionTemplate transactionTemplate;

    @Autowired
    private DataSourceTransactionManager transactionManager;

    @GetMapping("spring/trans/{id}")
    @ApiOperation(value = "spring编程式事务")
    public Long springTrans(@RequestParam("id") int id) {
        String sql = "insert into db_lock(id) value(?)";
        //        执行我们的execute方法
        //       构造内部类TransactionCallbackWithoutResult
        transactionTemplate.execute(new TransactionCallbackWithoutResult() {
            @Override
            protected void doInTransactionWithoutResult(TransactionStatus transactionStatus) {
                int i = id;
                jdbcTemplate.update(sql, i++);
                jdbcTemplate.update(sql, i++);
                jdbcTemplate.update(sql, i++);
            }
        });
        return 1L;
    }

    @GetMapping("trans/{id}")
    @ApiOperation(value = "JAVA事务")
    public Long javaTrans(@RequestParam("id") int id) {
        String sql = "insert into db_lock(id) value(?)";
        int i = id;
        //拿到transactionStatus
        TransactionStatus status = transactionManager.getTransaction(new DefaultTransactionDefinition());
        try {
            jdbcTemplate.update(sql, i++);
            jdbcTemplate.update(sql, i++);
            jdbcTemplate.update(sql, i++);
        } catch (DataAccessException ex) {
            //副本丢弃
            transactionManager.rollback(status);
            throw ex;
        }
        //副本转正
        transactionManager.commit(status);
        return 1L;
    }


}
```

补充

事务执行的中间态说明

sql执行后，尚未提交，此时对应的数据状态：

正本数据（原来的数据）处于锁定状态，允许其他线程读不允许改（此时易出现幻读）

副本数据（sql执行的结果），处于隔离状态，其他线程是无法接触到的（可设置事务隔离级别，使其可读）未生效的数据（脏读）

场景

> 预置一个事务问题场景：去哪网上订购机票
>
> 国航：北京 ---- 香港
>
> 南航：香港 ----- 泰国
>
> 只有同时能在国航/南航买到机票，才算有意义，否则做退票处理！

## XA 2PC 例子

> 遵守X/Open 的组织结构（全局事务管理器，二阶段提交）
>
> 遵守XA协议的数据源

pom.xml

```
<?xml version="1.0" encoding="UTF-8"?>

<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <parent>
        <artifactId>cache</artifactId>
        <groupId>com.enjoy</groupId>
        <version>1.0</version>
    </parent>
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.enjoy</groupId>
    <artifactId>trans-2pc</artifactId>
    <version>1.0</version>

    <name>trans-2pc</name>
    <!-- FIXME change it to the project's website -->
    <url>http://www.example.com</url>

    <properties>
        <spring.boot.version>1.5.4.RELEASE</spring.boot.version>
        <druid.version>1.0.31</druid.version>
    </properties>


    <dependencies>
        <!-- https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-web -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
            <version>${spring.boot.version}</version>
            <exclusions>
                <!-- 排除spring boot默认使用的tomcat，使用jetty -->
                <exclusion>
                    <groupId>org.springframework.boot</groupId>
                    <artifactId>spring-boot-starter-tomcat</artifactId>
                </exclusion>
            </exclusions>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-jetty</artifactId>
            <version>${spring.boot.version}</version>
        </dependency>

        <!-- https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-jdbc -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-jdbc</artifactId>
            <version>${spring.boot.version}</version>
        </dependency>

        <!-- https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-jta-atomikos -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-jta-atomikos</artifactId>
            <version>${spring.boot.version}</version>
        </dependency>

        <!-- https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-test -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <version>${spring.boot.version}</version>
        </dependency>

        <!-- alibaba database pool begin -->
        <!-- https://mvnrepository.com/artifact/com.alibaba/druid -->
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>druid</artifactId>
            <version>${druid.version}</version>
        </dependency>

        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>druid-spring-boot-starter</artifactId>
            <version>1.1.1</version>
        </dependency>
        <!-- alibaba database pool end -->

        <!-- https://mvnrepository.com/artifact/mysql/mysql-connector-java -->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.42</version>
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
</project>
```

application.yml

```
server:
  port: 8090
spring:
#  profiles:
#    active:
#      - test

  datasource:
    type: com.alibaba.druid.pool.xa.DruidXADataSource
    druid:
      ghDB:
        name: ghDB
        url: jdbc:mysql://localhost:3306/enjoy?useUnicode=true&characterEncoding=utf8&useSSL=false
        username: root
        password: zhangqi
        # 下面为连接池的补充设置，应用到上面所有数据源中
        # 初始化大小，最小，最大
        initialSize: 5
        minIdle: 5
        maxActive: 20
        # 配置获取连接等待超时的时间
        maxWait: 60000
        # 配置间隔多久才进行一次检测，检测需要关闭的空闲连接，单位是毫秒 
        timeBetweenEvictionRunsMillis: 60000
        # 配置一个连接在池中最小生存的时间，单位是毫秒 
        minEvictableIdleTimeMillis: 30
        validationQuery: SELECT 1 
        validationQueryTimeout: 10000
        testWhileIdle: true
        testOnBorrow: false
        testOnReturn: false
        # 打开PSCache，并且指定每个连接上PSCache的大小 
        poolPreparedStatements: true
        maxPoolPreparedStatementPerConnectionSize: 20
        filters: stat,wall
        # 通过connectProperties属性来打开mergeSql功能；慢SQL记录
        connectionProperties: druid.stat.mergeSql=true;druid.stat.slowSqlMillis=5000
        # 合并多个DruidDataSource的监控数据
        useGlobalDataSourceStat: true 

      nhDB:
        name: nhDB

        url: jdbc:mysql://localhost:3306/enjoy2?useUnicode=true&characterEncoding=utf8&useSSL=false
        username: root
        password: zhangqi
        # 下面为连接池的补充设置，应用到上面所有数据源中
        # 初始化大小，最小，最大
        initialSize: 5
        minIdle: 5
        maxActive: 20
        # 配置获取连接等待超时的时间
        maxWait: 60000
        # 配置间隔多久才进行一次检测，检测需要关闭的空闲连接，单位是毫秒 
        timeBetweenEvictionRunsMillis: 60000
        # 配置一个连接在池中最小生存的时间，单位是毫秒 
        minEvictableIdleTimeMillis: 30
        validationQuery: SELECT 1 
        validationQueryTimeout: 10000
        testWhileIdle: true
        testOnBorrow: false
        testOnReturn: false
        # 打开PSCache，并且指定每个连接上PSCache的大小 
        poolPreparedStatements: true
        maxPoolPreparedStatementPerConnectionSize: 20
        filters: stat,wall
        # 通过connectProperties属性来打开mergeSql功能；慢SQL记录
        connectionProperties: druid.stat.mergeSql=true;druid.stat.slowSqlMillis=5000
        # 合并多个DruidDataSource的监控数据
        useGlobalDataSourceStat: true 

  #jta相关参数配置   
  jta:
    log-dir: classpath:tx-logs
    transaction-manager-id: txManager
```

项目结构

![](/assets/3214hfgh.png)

DataSourceConfig.java

```
package com.zachary.config;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Configurable;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.boot.jta.atomikos.AtomikosDataSourceBean;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Primary;
import org.springframework.core.env.Environment;
import org.springframework.jdbc.core.JdbcTemplate;

import javax.sql.DataSource;
import java.util.Properties;

/**
 * @Title:
 * @Author:Zachary
 * @Desc:
 * @Date:2019/1/17
 **/
@Configurable
public class DataSourceConfig {
    // 数据库配置的遵守XA协议

    @Bean(name = "ghDataSource")
    @Primary
    @Autowired
    public DataSource ghDataSource(Environment env) {
        AtomikosDataSourceBean ds = new AtomikosDataSourceBean();
        Properties prop = build(env, "spring.datasource.druid.ghDB.");
        ds.setXaDataSourceClassName("com.alibaba.druid.pool.xa.DruidXADataSource");
        ds.setUniqueResourceName("ghDB");
        ds.setPoolSize(5);
        ds.setXaProperties(prop);
        return ds;

    }

    @Autowired
    @Bean(name = "nhDataSource")
    public AtomikosDataSourceBean businessDataSource(Environment env) {

        AtomikosDataSourceBean ds = new AtomikosDataSourceBean();
        Properties prop = build(env, "spring.datasource.druid.nhDB.");
        ds.setXaDataSourceClassName("com.alibaba.druid.pool.xa.DruidXADataSource");
        ds.setUniqueResourceName("nhDB");
        ds.setPoolSize(5);
        ds.setXaProperties(prop);

        return ds;
    }

    @Bean("ghJdbcTemplate")
    public JdbcTemplate sysJdbcTemplate(@Qualifier("ghDataSource") DataSource ds) {
        return new JdbcTemplate(ds);
    }

    @Bean("nhJdbcTemplate")
    public JdbcTemplate busJdbcTemplate(@Qualifier("nhDataSource") DataSource ds) {
        return new JdbcTemplate(ds);
    }

    private Properties build(Environment env, String prefix) {

        Properties prop = new Properties();
        prop.put("url", env.getProperty(prefix + "url"));
        prop.put("username", env.getProperty(prefix + "username"));
        prop.put("password", env.getProperty(prefix + "password"));
        prop.put("driverClassName", env.getProperty(prefix + "driverClassName", ""));
        prop.put("initialSize", env.getProperty(prefix + "initialSize", Integer.class));
        prop.put("maxActive", env.getProperty(prefix + "maxActive", Integer.class));
        prop.put("minIdle", env.getProperty(prefix + "minIdle", Integer.class));
        prop.put("maxWait", env.getProperty(prefix + "maxWait", Integer.class));
        prop.put("poolPreparedStatements", env.getProperty(prefix + "poolPreparedStatements", Boolean.class));

        prop.put("maxPoolPreparedStatementPerConnectionSize",
                env.getProperty(prefix + "maxPoolPreparedStatementPerConnectionSize", Integer.class));

        prop.put("maxPoolPreparedStatementPerConnectionSize",
                env.getProperty(prefix + "maxPoolPreparedStatementPerConnectionSize", Integer.class));
        prop.put("validationQuery", env.getProperty(prefix + "validationQuery"));
        prop.put("validationQueryTimeout", env.getProperty(prefix + "validationQueryTimeout", Integer.class));
        prop.put("testOnBorrow", env.getProperty(prefix + "testOnBorrow", Boolean.class));
        prop.put("testOnReturn", env.getProperty(prefix + "testOnReturn", Boolean.class));
        prop.put("testWhileIdle", env.getProperty(prefix + "testWhileIdle", Boolean.class));
        prop.put("timeBetweenEvictionRunsMillis",
                env.getProperty(prefix + "timeBetweenEvictionRunsMillis", Integer.class));
        prop.put("minEvictableIdleTimeMillis", env.getProperty(prefix + "minEvictableIdleTimeMillis", Integer.class));
        prop.put("filters", env.getProperty(prefix + "filters"));

        return prop;
    }

}
```

OrderController.java

```
package com.zachary.web.controller;

import com.zachary.web.service.OrderService;
import io.swagger.annotations.Api;
import io.swagger.annotations.ApiOperation;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

import java.util.UUID;

/**
 * @Title:
 * @Author:Zachary
 * @Desc:
 * @Date:2019/1/17
 **/
@Api("2pc")
@RestController
public class OrderController {

    @Autowired
    private OrderService orderService;

    @ApiOperation("保存订单")
    @GetMapping("save")
    public String saveOrder(String idCard) {
        String busId = UUID.randomUUID().toString();
        return orderService.doOrder(busId, idCard);
    }
}
```

OrderService.java

```
package com.zachary.web.service;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.jdbc.core.JdbcTemplate;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;

/**
 * @Title:
 * @Author:Zachary
 * @Desc:
 * @Date:2019/1/17
 **/
@Service
public class OrderService {
    @Autowired
    private JdbcTemplate ghJdbcTemplate;
    @Autowired
    private JdbcTemplate nhJdbcTemplate;


    @Transactional
    public String doOrder(String busId, String idcard){
        System.out.println("begin.....");

        String sql = "UPDATE tcc_fly_order SET bus_id=?,STATUS = 1,idcard=?,remark=? WHERE STATUS = 0 LIMIT 1";

        //锁定订单
        int ret = ghJdbcTemplate.update(sql, busId, idcard, "国航");
        if (ret != 1) {
            throw new RuntimeException("国航下单失败，无票可订");
//            使用Exception 抛异常是不会回滚的 ，默认是RuntimeException
//            throw new Exception();
        }

        //锁定订单
        ret = nhJdbcTemplate.update(sql, busId, idcard, "南航");
        if (ret != 1) {
            throw new RuntimeException("南航下单失败，无票可订");
            //            使用Exception 抛异常是不会回滚的,默认是RuntimeException
//            throw new Exception();
        }
        System.out.println("end.....");
        return "success";
    }
}
```

App.java

```
package com.zachary;

import com.zachary.config.DataSourceConfig;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.EnableAutoConfiguration;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Import;
import org.springframework.transaction.annotation.EnableTransactionManagement;

/**
 * Hello world!
 */
@EnableAutoConfiguration
@ComponentScan("com.zachary")
@Configuration
@EnableTransactionManagement(proxyTargetClass = true)
@Import({DataSourceConfig.class})
public class App {
    public static void main(String[] args) {
        SpringApplication.run(App.class, args);
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

总结

> 默认只有runtime（包括其子类）异常回滚 --------普通异常Exception是不回滚的
>
> 2pc架构的限制/问题
>
> 1.必须使用支持XA协议的datasource数据源
>
> 2.必须同时锁定两个数据库的数据，导致事务锁定时间大大延长--数据库性能可能成为瓶颈

## Base理论/柔性事务

> 为了可用性，牺牲一致性

了解

> BA:Basic Availability 基本事务可用性
>
> S:Soft state 柔性状态（允许出现中间态不一致）
>
> E:Eventual consistency 最终一致性

补充

> base理论的本质，是不在遵守ACID，不做强一致
>
> base理论的S，即指事务执行的中间状态（commit之前的状态），可以不设置隔离锁定，允许对外界表现出不一致
>
> 比如：一个事物执行需要时间--&gt;a步骤--&gt;b步骤----20毫秒---中间状态
>
> base理论的E，即指保证最终所有的步骤都能得到执行，达到最终数据一致性

过程

> 事务开始前，数据是一致的
>
> 事务进行中，数据是不一致的中间态不一致，允许出现这样的情况（放宽标准）
>
> 事务对比结束后，数据保持一致

对比理解

> 对比ACID事务，base事务就是砍掉了其中的数据锁定状态
>
> 为了可用性（移除数据锁定的状态，不可用的状态）

分类

> ACID标准，强一致要求的事务标准，刚性事务
>
> 牺牲一致性，放弃ACID里的强一致要求，柔性事务

tcc\(try-confirm-cancel\)

tcc两阶段、补偿性事务

![](/assets/32ff.png)

说明，接口改造

> 一个普通的业务接口，需要切成三个接口：try-confirm-cancel
>
> try:原业务接口，接口执行完毕后，数据库就提交 如：insert 一条数据1
>
> confirm：确认数据，将try阶段的数据查询出来确认，如select 1 确认数据是都已经OK
>
> cancel：抵消数据，将try阶段的数据做逆向取消，如：delete from 删除数据/insert -1 再加一条数据进行抵消，这是逆向的业务方法，又称之为补偿动作

tcc联动步骤

> 如果try不出异常正常结束，则框架会自动调用confirm方法
>
> 如果try出了异常，框架去调用cancel方法
>
> 备注：对于思考cancel出了异常的理解
>
> 1.在业务上我们要去确保cancel方法的顺利执行
>
> 2.对于网络抖动/服务宕机之类的原因，它是有日志，会自己重试的

变种使用tcc

> tcc方案，对原业务的侵入性太强，为了更好的控制通常需要做变种使用
>
> try：将原业务接口，涉及到的资源锁定/或者做预备，通过一个冻结的状态，做排它处理
>
> confirm：正式调用原业务接口方法，如insert一条数据1--因为try阶段已经为此语句准备好了执行条件，不会再出现业务冲突的情况（业务上一定可行）
>
> cancel：恢复资源状态，取消资源的冻结状态

**TCC框架是有事务的日志表的**

tcc框架对业务方法的要求

> try-confirm-cancel 三个方法，都必须要满足幂等性/可查询性

![](/assets/hdsfkasdlfu91221212.png)

对比

> tcc事务与其他事务框架的对比
>
> 异步确保性事务
>
> 典型的使用MQ中间件来传递事务调用，其重点是确保事务消息一定会到达目标系统，目标系统只许成功不许失败（不成功我也不管），因为是异步的，因此事务发起后，就不在关注了，名曰事务，已不像事务范畴
>
> 最大努力通知型
>
> 通过多线程异步重试，来传递事务调用，即通过重试机制，努力将事务调用传递到目标系统，目前系统只许成功不许失败（不成功我也不管）

 实战演练





