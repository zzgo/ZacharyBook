## Zookeeper基础

1.1.单基部署

先把ZK安装起来，后面的很多操作，都是的前提都是由ZK的操作环境，先来把ZK安装好，

1.1.1.Zookeeper windows环境安装

环境要求:必须要有jdk环境,本次讲课使用jdk1.8



1.安装jdk

2.安装Zookeeper. 在官网http://zookeeper.apache.org/下载zookeeper.我下载的是zookeeper-3.4.12版本。

解压zookeeper-3.4.6至D:\machine\zookeeper-3.4.12.

在D:\machine 新建data及log目录。

3.ZooKeeper的安装模式分为三种，分别为：单机模式（stand-alone）、集群模式和集群伪分布模式。ZooKeeper 单机模式的安装相对比较简单，如果第一次接触ZooKeeper的话，建议安装ZooKeeper单机模式或者集群伪分布模式。

安装单击模式。 至D:\machine\zookeeper-3.4.12\conf 复制 zoo\_sample.cfg 并粘贴到当前目录下，命名zoo.cfg.



1.1.2.目录结构

bin             存放系统脚本

conf            存放配置文件

contrib          zk附加功能支持

dist-maven       maven仓库文件

docs            zk文档

lib              依赖的第三方库

recipes          经典场景样例代码

src             zk源码



其中bin和conf是非常重要的两个目录，平时也是经常使用的。



1.1.2.1.bin目录

先看下bin目录

