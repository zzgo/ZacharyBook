## SpringBoot介绍

#### SpringBoot介绍

Spring Boot使开发独立的，产品级别的基于Spring的应用变得非常简单，你只需"just run"。 我们为Spring平台及第三方库提供开箱即用的设置，这样你就可以有条不紊地开始。多数Spring Boot应用需要很少的Spring配置。

你可以使用Spring Boot创建Java应用，并使用java -jar启动它或采用传统的war部署方式。

##### 解决的问题

* 依赖太多了, 且存在版本问题 
* 配置太多了且每次都一样, 大部分工程, 配置每次都是一样的, 从一个地方拷贝到另外一个地方. 且Spring发展10多年, 各种配置版本太多, 对于很多程序员来说, 分不清哪个是有效, 哪个无效. 
* 部署太麻烦. 需要tomcat部署, 项目结构也需要照着Java EE的目录结构来写.

##### SpringBoot特点

* 创建独立的Spring应用程序
* 嵌入的Tomcat，无需部署WAR文件
* 简化Maven配置
* 自动配置Spring
* 提供生产就绪型功能，如指标，健康检查和外部配置
* 绝对没有代码生成和对XML没有要求配置

##### SpringBoot功能

* 自动配置\(auto-configuration\)

一项简化配置的功能，比如在classpath中发现有spring security的jar包，则自动创建相关的bean等

* starters\(简化依赖\)

这个比较关键，方便spring去集成各类组件，比如redis、mongodb等等。

##### SpringBoot的发展

近年来发展迅速

