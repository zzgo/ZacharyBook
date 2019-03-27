## SpringBoot登录注册演示

### 新建tab\_user表

```java
CREATE TABLE `tab_user` (
    `id`  int NOT NULL AUTO_INCREMENT ,
    `passwd`  varchar(255) NULL ,
    `username`  varchar(255) NULL ,
    PRIMARY KEY (`id`)
);
```

### 搭建SpringBoot环境

pom依赖

```java
<dependencies>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.11</version>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
    </dependencies>
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-dependencies</artifactId>
                <version>2.1.2.RELEASE</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```

App.java启动类

```java
@SpringBootApplication
public class App {
    public static void main(String[] args) {
        SpringApplication.run(App.class, args);
    }
}
```

#### 继承mybatis

pom文件加入：

```java
<dependency>
    <groupId>org.mybatis.spring.boot</groupId>
    <artifactId>mybatis-spring-boot-starter</artifactId>
    <version>1.2.0</version>
</dependency>
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
</dependency>
```

在application.properties加入

```java
spring.datasource.driverClassName=com.mysql.jdbc.Driver
spring.datasource.url=jdbc:mysql://127.0.0.1:3306/quickstart?serverTimezone=GMT%2B8
spring.datasource.username=root
spring.datasource.password=zhangqi

mybatis.mapperLocations=classpath:mapping/*.xml
```

这里采用自动生成代码

准备mybatis的生成文件generatorConfig.xml，并在相应目录创建好model,dao,mapping文件夹

**使用这个自动化生成代码以及mapping.xml文件需要得工作如下**

第一步：

你首先使用的maven的工程，加入插件

```java
<build>
        <plugins>
            <plugin>
                <groupId>org.mybatis.generator</groupId>
                <artifactId>mybatis-generator-maven-plugin</artifactId>
                <version>1.3.2</version>
                <configuration>
                    <verbose>true</verbose>
                    <overwrite>true</overwrite>
                </configuration>
            </plugin>
        </plugins>
    </build>
```

第二步：

generatorconfig.xml文件，存放在resources目录下，注释已经很详细了。

```java
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE generatorConfiguration
        PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
        "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">
<generatorConfiguration>
    <!-- 数据库驱动:选择你的本地硬盘上面的数据库驱动包-->
    <classPathEntry  location="D:\software\maven\apache-maven-3.3.9-bin\repos\mysql\mysql-connector-java\5.1.38\mysql-connector-java-5.1.38.jar"/>
    <context id="DB2Tables"  targetRuntime="MyBatis3">
        <commentGenerator>
            <property name="suppressDate" value="true"/>
            <!-- 是否去除自动生成的注释 true：是 ： false:否 -->
            <property name="suppressAllComments" value="true"/>
        </commentGenerator>
        <!--数据库链接URL，用户名、密码 -->
        <jdbcConnection driverClass="com.mysql.jdbc.Driver" connectionURL="jdbc:mysql://127.0.0.1:3306/quickstart" userId="root" password="zhangqi">
        </jdbcConnection>
        <javaTypeResolver>
            <property name="forceBigDecimals" value="false"/>
        </javaTypeResolver>
        <!-- 生成模型的包名和位置-->
        <javaModelGenerator targetPackage="com.zachary.springboot.helloworld.model" targetProject="src/main/java">
            <property name="enableSubPackages" value="true"/>
            <property name="trimStrings" value="true"/>
        </javaModelGenerator>
        <!-- 生成映射文件的包名和位置-->
        <sqlMapGenerator targetPackage="mapping" targetProject="src/main/resources">
            <property name="enableSubPackages" value="true"/>
        </sqlMapGenerator>
        <!-- 生成DAO的包名和位置-->
        <javaClientGenerator type="XMLMAPPER" targetPackage="com.zachary.springboot.helloworld.dao" targetProject="src/main/java">
            <property name="enableSubPackages" value="true"/>
        </javaClientGenerator>
        <!-- 要生成的表 tableName是数据库中的表名或视图名 domainObjectName是实体类名-->
        <table tableName="tab_user" domainObjectName="Users" enableCountByExample="false" enableUpdateByExample="false" enableDeleteByExample="false" enableSelectByExample="false" selectByExampleQueryId="false"></table>
    </context>
</generatorConfiguration>
```

第三步：并在相应目录创建好model,dao,mapping文件夹，mapping文件创建在resources下。

第四步：点击右边的Idea Maven projects ，找到如图：

![](/assets/2178fhjah.png)

完成！！！

#### 在App.java启动类加上@MapperScan\("包名"\)

```java
@SpringBootApplication
@MapperScan("com.zachary.springboot.helloworld.dao")
public class App {
    public static void main(String[] args) {
        SpringApplication.run(App.class, args);
    }
}
```

#### UsersMapper.java新增登录方法

```java
 Users findByUsernameAndPasswd(@Param("username") String username, @Param("passwd") String passwd);
```

对应的UsersMapper.xml

```java
<select id="findByUsernameAndPasswd" resultMap="BaseResultMap">
    SELECT
    <include refid="Base_Column_List"/>
    FROM tab_user
    WHERE
    username=#{username,jdbcType=VARCHAR} and passwd=#{passwd,jdbcType=VARCHAR}
    LIMIT 1
</select>
```

#### 单元测试

pom加入测试包

```java
<!--单元测试-->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-test</artifactId>
</dependency>
```

编写测试类

```java
@SpringBootTest(classes = {App.class})
@RunWith(SpringRunner.class)
public class UsersMapperTest {
    @Autowired
    private UsersMapper usersMapper;

    @Test
    public void findByUsernameAndPasswd() throws Exception {
        Users users = usersMapper.findByUsernameAndPasswd("admin", "admin");
        System.out.println(users.getId());
        System.out.println(users.getUsername());
        System.out.println(users.getPasswd());
    }

}
```

**注意测试时，junit 4.12 及 更高版本**

#### 新建Service层

UsersService.java

```java
public interface UsersService {
    boolean login(String username, String passwd);

    boolean register(String username, String passwd);
}
```

UsersServiceImpl.java

```java
@Service
public class UsersServiceImpl implements UsersService {
    @Autowired
    private UsersMapper usersMapper;

    @Override
    public boolean login(String username, String passwd) {
        Users users = usersMapper.findByUsernameAndPasswd(username, passwd);
        return username != null;
    }

    @Override
    public boolean register(String username, String passwd) {
        Users users = new Users();
        users.setUsername(username);
        users.setPasswd(passwd);
        int count = usersMapper.insertSelective(users);
        return count > 0
    }
}
```

UsersController.java

```java
@RestController
@RequestMapping("/users")
public class UsersController {
    @Autowired
    private UsersService usersService;

    @RequestMapping("login")
    public boolean login(String username, String passwd) {
        return usersService.login(username, passwd);
    }

    @RequestMapping("register")
    public boolean register(String username, String passwd) {
        return usersService.register(username, passwd);
    }
}
```

application.properties，部分配置

```java
#端口
server.port=80 
#访问带上，如localhost/root/
#server.servlet.context-path= /root
#SpringMvc 配置访问路径
#spring.mvc.view.prefix=/WEB-INF/jsp/
#spring.mvc.view.suffix=.jsp
```



