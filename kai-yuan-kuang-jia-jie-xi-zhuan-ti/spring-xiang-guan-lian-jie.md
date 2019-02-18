Spring 下几种常用的连接池

目前常用的连接池有：C3P0、DBCP、Proxool

* C3P0比较耗费资源，效率方面可能要低一点。
* DBCP在实践中存在BUG，在某些种情会产生很多空连接不能释放，Hibernate3.0已经放弃了对其的支持。
* Proxool的负面评价较少，现在比较推荐它，而且它还提供即时监控连接池状态的功能，便于发现连接泄漏的情况。

参看链接：[https://www.cnblogs.com/hehaiyang/p/4275228.html](https://www.cnblogs.com/hehaiyang/p/4275228.html)





