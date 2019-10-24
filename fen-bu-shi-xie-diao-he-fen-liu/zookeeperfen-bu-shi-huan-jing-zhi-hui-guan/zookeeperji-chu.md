## Zookeeper基础

### 1.1.单基部署

先把ZK安装起来，后面的很多操作，都是的前提都是由ZK的操作环境，先来把ZK安装好，

#### 1.1.1.Zookeeper windows环境安装

环境要求:必须要有jdk环境,本次讲课使用jdk1.8

#### 1.安装jdk

2.安装Zookeeper. 在官网[http://zookeeper.apache.org/下载zookeeper.我下载的是zookeeper-3.4.12版本。](http://zookeeper.apache.org/下载zookeeper.我下载的是zookeeper-3.4.12版本。)

解压zookeeper-3.4.6至D:\machine\zookeeper-3.4.12.

在D:\machine 新建data及log目录。

3.ZooKeeper的安装模式分为三种，分别为：单机模式（stand-alone）、集群模式和集群伪分布模式。ZooKeeper 单机模式的安装相对比较简单，如果第一次接触ZooKeeper的话，建议安装ZooKeeper单机模式或者集群伪分布模式。

安装单击模式。 至D:\machine\zookeeper-3.4.12\conf 复制 zoo\_sample.cfg 并粘贴到当前目录下，命名zoo.cfg.

#### 1.1.2.目录结构

* **bin             存放系统脚本**
* **conf            存放配置文件**
* contrib          zk附加功能支持
* dist-maven       maven仓库文件
* docs            zk文档
* lib              依赖的第三方库
* recipes          经典场景样例代码
* src             zk源码

其中bin和conf是非常重要的两个目录，平时也是经常使用的。

#### 1.1.2.1.bin目录

先看下bin目录

![](/assets/21781h2dsgfhg8.png)

其中

zkServer为服务器，启动后默认端口为2181

zkCli为命令行客户端

#### 1.1.2.2.conf目录

Conf目录为配置文件存放的目录，zoo.cfg为核心的配置文件

这里面的配置很多，这配置是运维的工作，目前没必要，也没办法全部掌握。

序号    参数名    说明

1    clientPort    客户端连接server的端口，即对外服务端口，一般设置为2181吧。

2    dataDir    存储快照文件snapshot的目录。默认情况下，事务日志也会存储在这里。建议同时配置参数dataLogDir, 事务日志的写性能直接影响zk性能。

3    tickTime    ZK中的一个时间单元。ZK中所有时间都是以这个时间单元为基础，进行整数倍配置的。例如，session的最小超时时间是2\*tickTime。

4    dataLogDir    事务日志输出目录。尽量给事务日志的输出配置单独的磁盘或是挂载点，这将极大的提升ZK性能。（No Java system property）

5    globalOutstandingLimit    最大请求堆积数。默认是1000。ZK运行的时候， 尽管server已经没有空闲来处理更多的客户端请求了，但是还是允许客户端将请求提交到服务器上来，以提高吞吐性能。当然，为了防止Server内存溢出，这个请求堆积数还是需要限制下的。\(Java system property: zookeeper.globalOutstandingLimit.\)

6    preAllocSize    预先开辟磁盘空间，用于后续写入事务日志。默认是64M，每个事务日志大小就是64M。如果ZK的快照频率较大的话，建议适当减小这个参数。\(Java system property: zookeeper.preAllocSize\)

7    snapCount    每进行snapCount次事务日志输出后，触发一次快照\(snapshot\), 此时，ZK会生成一个snapshot.\*文件，同时创建一个新的事务日志文件log.\*。默认是100000.（真正的代码实现中，会进行一定的随机数处理，以避免所有服务器在同一时间进行快照而影响性能）\(Java system property: zookeeper.snapCount\)

8    traceFile    用于记录所有请求的log，一般调试过程中可以使用，但是生产环境不建议使用，会严重影响性能。\(Java system property:? requestTraceFile\)

9    maxClientCnxns    单个客户端与单台服务器之间的连接数的限制，是ip级别的，默认是60，如果设置为0，那么表明不作任何限制。请注意这个限制的使用范围，仅仅是单台客户端机器与单台ZK服务器之间的连接数限制，不是针对指定客户端IP，也不是ZK集群的连接数限制，也不是单台ZK对所有客户端的连接数限制。

10    clientPortAddress    对于多网卡的机器，可以为每个IP指定不同的监听端口。默认情况是所有IP都监听 clientPort指定的端口。 New in 3.3.0

11    minSessionTimeoutmaxSessionTimeout    Session超时时间限制，如果客户端设置的超时时间不在这个范围，那么会被强制设置为最大或最小时间。默认的Session超时时间是在2 \* tickTime ~ 20 \* tickTime 这个范围 New in 3.3.0

12    fsync.warningthresholdms    事务日志输出时，如果调用fsync方法超过指定的超时时间，那么会在日志中输出警告信息。默认是1000ms。\(Java system property: fsync.warningthresholdms\) New in 3.3.4

13    autopurge.purgeInterval    在上文中已经提到，3.4.0及之后版本，ZK提供了自动清理事务日志和快照文件的功能，这个参数指定了清理频率，单位是小时，需要配置一个1或更大的整数，默认是0，表示不开启自动清理功能。\(No Java system property\) New in 3.4.0

14    autopurge.snapRetainCount    这个参数和上面的参数搭配使用，这个参数指定了需要保留的文件数目。默认是保留3个。\(No Java system property\) New in 3.4.0

15    electionAlg    在之前的版本中， 这个参数配置是允许我们选择leader选举算法，但是由于在以后的版本中，只会留下一种“TCP-based version of fast leader election”算法，所以这个参数目前看来没有用了，这里也不详细展开说了。\(No Java system property\)

16    initLimit    Follower在启动过程中，会从Leader同步所有最新数据，然后确定自己能够对外服务的起始状态。Leader允许F在 initLimit时间内完成这个工作。通常情况下，我们不用太在意这个参数的设置。如果ZK集群的数据量确实很大了，F在启动的时候，从Leader上同步数据的时间也会相应变长，因此在这种情况下，有必要适当调大这个参数了。\(No Java system property\)

17    syncLimit    在运行过程中，Leader负责与ZK集群中所有机器进行通信，例如通过一些心跳检测机制，来检测机器的存活状态。如果L发出心跳包在syncLimit之后，还没有从F那里收到响应，那么就认为这个F已经不在线了。注意：不要把这个参数设置得过大，否则可能会掩盖一些问题。

18    leaderServes    默认情况下，Leader是会接受客户端连接，并提供正常的读写服务。但是，如果你想让Leader专注于集群中机器的协调，那么可以将这个参数设置为no，这样一来，会大大提高写操作的性能。\(Java system property: zookeeper. leaderServes\)。

19    server.x=\[hostname\]:nnnnn\[:nnnnn\]    这里的x是一个数字，与myid文件中的id是一致的。右边可以配置两个端口，第一个端口用于F和L之间的数据同步和其它通信，第二个端口用于Leader选举过程中投票通信。

20    group.x=nnnnn\[:nnnnn\]weight.x=nnnnn    对机器分组和权重设置

21    cnxTimeout    Leader选举过程中，打开一次连接的超时时间，默认是5s。\(Java system property: zookeeper. cnxTimeout\)

22	zookeeper.DigestAuthenticationProvider.superDigest	ZK权限设置相关，具体参见 《 使用super 身份对有权限的节点进行操作》 和 《 ZooKeeper 权限控制》

23	skipACL	对所有客户端请求都不作ACL检查。如果之前节点上设置有权限限制，一旦服务器上打开这个开头，那么也将失效。\(Java system property: zookeeper.skipACL\)

24	forceSync	这个参数确定了是否需要在事务日志提交的时候调用 FileChannel.force来保证数据完全同步到磁盘。\(Java system property: zookeeper.forceSync\)

25	jute.maxbuffer	每个节点最大数据量，是默认是1M。这个限制必须在server和client端都进行设置才会生效。\(Java system property: jute.maxbuffer\)

