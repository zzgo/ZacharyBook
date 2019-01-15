分布式锁和事务实战

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



