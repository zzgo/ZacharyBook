# Summary

* [Introduction](README.md)
* [架构师](jia-gou-shi.md)
  * [架构师筑基专题](chapter1.md)
    * [并发编程进阶](chapter1/bing-fa-bian-cheng-jin-jie.md)
      * [一、线程基础、线程之间的共享和协作](chapter1/bing-fa-bian-cheng-jin-jie/xian-cheng-ji-chu-3001-xian-cheng-zhi-jian-de-gong-xiang-he-xie-zuo.md)
        * [1.1 基础概念，启动和终止线程，线程再认识](chapter1/bing-fa-bian-cheng-jin-jie/xian-cheng-ji-chu-3001-xian-cheng-zhi-jian-de-gong-xiang-he-xie-zuo/ji-chu-gai-nian.md)
        * [1.2线程间的共享](chapter1/bing-fa-bian-cheng-jin-jie/xian-cheng-ji-chu-3001-xian-cheng-zhi-jian-de-gong-xiang-he-xie-zuo/xian-cheng-jian-de-gong-xiang.md)
        * [1.5 线程间协作](chapter1/bing-fa-bian-cheng-jin-jie/xian-cheng-ji-chu-3001-xian-cheng-zhi-jian-de-gong-xiang-he-xie-zuo/xian-cheng-jian-xie-zuo.md)
      * [二、线程的并发工具类](chapter1/bing-fa-bian-cheng-jin-jie/er-3001-xian-cheng-de-bing-fa-gong-ju-lei.md)
        * [2.1 Fork/Join](chapter1/bing-fa-bian-cheng-jin-jie/er-3001-xian-cheng-de-bing-fa-gong-ju-lei/21-forkjoin.md)
        * [2.2 CountDownLatch作用、应用场景和实战](chapter1/bing-fa-bian-cheng-jin-jie/er-3001-xian-cheng-de-bing-fa-gong-ju-lei/22-countdownlatchzuo-yong-3001-ying-yong-chang-jing-he-shi-zhan.md)
        * [2.3 CyclicBarrier作用、应用场景和实战](chapter1/bing-fa-bian-cheng-jin-jie/er-3001-xian-cheng-de-bing-fa-gong-ju-lei/23-cyclicbarrierzuo-yong-3001-ying-yong-chang-jing-he-shi-zhan.md)
        * [2.4 CountDownlatch和CyclicBarrier](chapter1/bing-fa-bian-cheng-jin-jie/er-3001-xian-cheng-de-bing-fa-gong-ju-lei/24-countdownlatchhe-cyclicbarrier.md)
        * [2.5 Semaphore作用、应用场景和实战](chapter1/bing-fa-bian-cheng-jin-jie/er-3001-xian-cheng-de-bing-fa-gong-ju-lei/25-semaphorezuo-yong-3001-ying-yong-chang-jing-he-shi-zhan.md)
        * [2.6 Exchange作用、应用场景和实战](chapter1/bing-fa-bian-cheng-jin-jie/er-3001-xian-cheng-de-bing-fa-gong-ju-lei/26-exchangezuo-yong-3001-ying-yong-chang-jing-he-shi-zhan.md)
        * [2.7 Callable、Future和FutureTask](chapter1/bing-fa-bian-cheng-jin-jie/er-3001-xian-cheng-de-bing-fa-gong-ju-lei/27-callablefuturehe-futuretask.md)
      * 三、原子操作CAS
        * [3.1 CAS的原理](chapter1/bing-fa-bian-cheng-jin-jie/31-casde-yuan-li.md)
        * [3.2 CAS的问题](chapter1/bing-fa-bian-cheng-jin-jie/32-casde-wen-ti.md)
        * [3.3 原子操作类的使用](chapter1/bing-fa-bian-cheng-jin-jie/33-yuan-zi-cao-zuo-lei-de-shi-yong.md)
      * 四、显示锁和AQS
        * [4.1 显示锁](chapter1/bing-fa-bian-cheng-jin-jie/xian-shi-suo.md)
        * 4.2 LockSupport工具进阶
        * [4.3 AbstractQueuedSynchronizer实现和源码分析](chapter1/bing-fa-bian-cheng-jin-jie/43-abstractqueuedsynchronizershi-zhan.md)
      * 五、并发容器
        * 5.1 ConcurrentHashMap
        * 5.2 其他并发容器
        * 5.3 阻塞队列
      * [六、线程池](chapter1/bing-fa-bian-cheng-jin-jie/liu-3001-xian-cheng-chi.md)
        * 6.1 什么是线程池？为什么要用线程池？
        * 6.2 实现一个我们自己的线程池
        * 6.3 JDK中的线程池
        * 6.4 线程池工作机制
        * 6.5 合理的配置线程池
        * 6.6 系统为我们预定义的线程池
        * 6.7 Executor框架
        * 6.8 CompletionService
      * [七、并发安全](chapter1/bing-fa-bian-cheng-jin-jie/qi-3001-bing-fa-an-quan.md)
        * 7.1 类的线程安全
        * 7.2 如何做到类的线程安全
        * 7.3 线程不安全引发的问题
        * 7.4 线程安全的单例模式
      * [八、实战项目](chapter1/bing-fa-bian-cheng-jin-jie/ba-3001-shi-zhan-xiang-mu.md)
        * 8.1 并发任务执行框架
        * 8.2 应用性能优化实战
      * [九、JVM和底层实现原理](chapter1/bing-fa-bian-cheng-jin-jie/jiu-3001-jvm-he-di-ceng-shi-xian-yuan-li.md)
        * 9.1 现代计算机物理上的内存模型
        * 9.2 Java内存模型（JVM）
    * [MySQL深度优化](chapter1/mysqlshen-du-you-hua.md)
    * [Netty IO](chapter1/netty-io.md)
      * [网络编程基础](chapter1/netty-io/wang-luo-bian-cheng-ji-chu.md)
      * [Linux网络I/O模型](chapter1/netty-io/linuxwang-luo-i-o-mo-xing.md)
      * [原生JDK网络编程](chapter1/netty-io/yuan-sheng-jdk-wang-luo-bian-cheng.md)
        * [BIO](chapter1/netty-io/yuan-sheng-jdk-wang-luo-bian-cheng/bio.md)
        * AIO
        * NIO之Reactor模式
    * [常见50句SQL联系](chapter1/mysqlshen-du-you-hua/chang-jian-50-ju-sql-lian-xi.md)
  * [开源框架解析专题](kai-yuan-kuang-jia-jie-xi-zhuan-ti.md)
    * [Spring](kai-yuan-kuang-jia-jie-xi-zhuan-ti/spring.md)
      * [ioc容器](kai-yuan-kuang-jia-jie-xi-zhuan-ti/spring5.md)
    * [Mybstis](kai-yuan-kuang-jia-jie-xi-zhuan-ti/mybstis.md)
    * [SpringMVC](kai-yuan-kuang-jia-jie-xi-zhuan-ti/springmvc.md)
    * [spring 相关链接](kai-yuan-kuang-jia-jie-xi-zhuan-ti/spring-xiang-guan-lian-jie.md)
  * [高性能架构专题](chapter1/gao-xing-neng-jia-gou-zhuan-ti.md)
    * [分布式常见场景解决方案实战](chapter1/fen-bu-shi-chang-jian-chang-jing-jie-jue-fang-an-shi-zhan.md)
      * [分布式锁和事务](chapter1/fen-bu-shi-chang-jian-chang-jing-jie-jue-fang-an-shi-zhan/fen-bu-shi-shi-wu-he-suo.md)
      * [分布式事务实战](chapter1/fen-bu-shi-chang-jian-chang-jing-jie-jue-fang-an-shi-zhan/fen-bu-shi-shi-wu-shi-zhan.md)
      * [单点登录解决方案](chapter1/fen-bu-shi-chang-jian-chang-jing-jie-jue-fang-an-shi-zhan/dan-dian-deng-lu.md)
      * [分布式任务调度](chapter1/fen-bu-shi-chang-jian-chang-jing-jie-jue-fang-an-shi-zhan/fen-bu-shi-ren-wu-diao-du.md)
    * [缓存和NoSql](chapter1/huancun-he-nosql.md)
      * [Redis](chapter1/redisbasic.md)
        * [安装](chapter1/an-zhuang.md)
        * [基本认识](chapter1/redis.md)
        * [命令操作](chapter1/ming-ling-cao-zuo.md)
        * [java-redis实战文章点赞](chapter1/javake-hu-duan-shi-zhan.md)
        * [java-redis实战抢红包](chapter1/java-redisshi-zhan-qiang-hong-bao.md)
        * [Redis实现购物车核心功能](chapter1/redisshi-xian-gou-wu-che-he-xin-gong-neng.md)
        * [Lua学习](chapter1/luaxue-xi.md)
          * [Lua基础](chapter1/luayu-yan-xue-xi.md)
          * [Lua数据类型](chapter1/luashu-ju-lei-xing.md)
          * [Lua变量](chapter1/luabian-liang.md)
        * [性能实战和工具测试](chapter1/xing-neng-shi-zhan-he-gong-ju-ce-shi.md)
        * [手写Jedis客户端](chapter1/shou-xie-jedis-ke-hu-duan.md)
        * [Pipeline详解](chapter1/pipelinexiang-jie.md)
        * [redis事务](chapter1/redisshi-wu.md)
        * [发布与订阅](chapter1/fa-bu-yu-ding-yue.md)
        * [LUA语言和Redis](chapter1/luayu-yan-he-redis.md)
        * [持久化原理剖析](chapter1/chi-jiu-hua-yuan-li-pou-xi.md)
      * [MongoDB](chapter1/mongodb.md)
        * [测试数据](chapter1/mongodb/ce-shi-shu-ju.md)
        * [了解](chapter1/mongodb/ru-men.md)
        * [安装](chapter1/mongodb/an-zhuang.md)
        * [快速入门](chapter1/mongodb/kuai-su-ru-men.md)
        * [原生mongodb-java-client开发](chapter1/mongodb/yuan-sheng-mongodb-java-client-kai-fa.md)
        * [原生java驱动 Pojo的操作方式](chapter1/mongodb/yuan-sheng-java-qu-dong-pojo-de-cao-zuo-fang-shi.md)
        * [spring-data-mongodb开发](chapter1/mongodb/spring-data-mongodbkai-fa.md)
        * [版本选择与MongoDB数据类型](chapter1/mongodb/ban-ben-xuan-ze-yu-mongodb-shu-ju-lei-xing.md)
        * [控制台命令](chapter1/mongodb/kong-zhi-tai-ming-ling.md)
        * [回顾java客户端与mongodb](chapter1/mongodb/hui-gu-java-ke-hu-duan-yu-mongodb.md)
        * [mongodbg高级](chapter1/mongodb/mongodbggao-ji.md)
        * [聚合操作日期分组](chapter1/mongodb/ju-he-cao-zuo-ri-qi-fen-zu.md)
    * [高可靠数据存储](chapter1/gao-ke-kao-shu-ju-cun-chu.md)
      * [MySql高性能存储](chapter1/gao-ke-kao-shu-ju-cun-chu/mysqlgao-xing-neng-cun-chu.md)
        * [linux下mysql命名大全](chapter1/gao-ke-kao-shu-ju-cun-chu/mysqlgao-xing-neng-cun-chu/linuxxia-mysql-ming-ming-da-quan.md)
  * [Demo](demo.md)
    * [秒杀](miao-sha.md)
  * [面试题](chapter1/mian-shi-ti.md)
    * [算法题](chapter1/mian-shi-ti/suan-fa-ti.md)
    * [逻辑题](chapter1/mian-shi-ti/luo-ji-ti.md)
    * [一般面试题](chapter1/mian-shi-ti/yi-ban-mian-shi-ti-2.md)
    * [经验之谈](chapter1/mian-shi-ti/jing-yan-zhi-tan.md)
    * [线程问题](chapter1/mian-shi-ti/xian-cheng-wen-ti.md)
    * [问题记录](chapter1/mian-shi-ti/wen-ti-ji-lu.md)
  * [杂谈基础](chapter1/ji-chu.md)
* [门萨测试题](men-sa-ce-shi-ti.md)

