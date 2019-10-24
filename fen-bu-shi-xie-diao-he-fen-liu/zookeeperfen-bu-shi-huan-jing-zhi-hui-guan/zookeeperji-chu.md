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

22    zookeeper.DigestAuthenticationProvider.superDigest    ZK权限设置相关，具体参见 《 使用super 身份对有权限的节点进行操作》 和 《 ZooKeeper 权限控制》

23    skipACL    对所有客户端请求都不作ACL检查。如果之前节点上设置有权限限制，一旦服务器上打开这个开头，那么也将失效。\(Java system property: zookeeper.skipACL\)

24    forceSync    这个参数确定了是否需要在事务日志提交的时候调用 FileChannel.force来保证数据完全同步到磁盘。\(Java system property: zookeeper.forceSync\)

25    jute.maxbuffer    每个节点最大数据量，是默认是1M。这个限制必须在server和client端都进行设置才会生效。\(Java system property: jute.maxbuffer\)

在这挑选几个讲解：

clientPort：参数无默认值，必须配置，用于配置当前服务器对外的服务端口，客户端必须使用这端口才能进行连接

dataDir：用于存放内存数据库快照的文件夹，同时用于集群的myid文件也存在这个文件夹里（注意：一个配置文件只能包含一个dataDir字样，即使它被注释掉了。）

dataLogDir：用于单独设置transaction log的目录，transaction log分离可以避免和普通log还有快照的竞争

dataDir：新安装zk这文件夹里面是没有文件的，可以通过snapCount参数配置产生快照的时机

以下配置集群中才会使用，后面再讨论

tickTime：心跳时间，为了确保连接存在的，以毫秒为单位，最小超时时间为两个心跳时间

initLimit：多少个心跳时间内，允许其他server连接并初始化数据，如果ZooKeeper管理的数据较大，则应相应增大这个值

syncLimit：多少个tickTime内，允许follower同步，如果follower落后太多，则会被丢弃。

### 1.2.ZK的特性

Zk的特性会从会话、数据节点，版本，Watcher，ACL权限控制，集群角色这些部分来了解，其中重点需要掌握的数据节点与Watcher

#### 1.2.1.会话

客户端与服务端的一次会话连接，本质是TCP长连接，通过会话可以进行心跳检测和数据传输；

![](/assets/374jdfhjdshj0ik.png)

会话（session）是zookepper非常重要的概念，客户端和服务端之间的任何交互操作都与会话有关

会话状态

看下这图，Zk客户端和服务端成功连接后，就创建了一次会话，ZK会话在整个运行期间的生命周期中，会在不同的会话状态之间切换，这些状态包括：

CONNECTING、CONNECTED、RECONNECTING、RECONNECTED、CLOSE

一旦客户端开始创建Zookeeper对象，那么客户端状态就会变成CONNECTING状态，同时客户端开始尝试连接服务端，连接成功后，客户端状态变为CONNECTED，通常情况下，由于断网或其他原因，客户端与服务端之间会出现断开情况，一旦碰到这种情况，Zookeeper客户端会自动进行重连服务，同时客户端状态再次变成CONNCTING，直到重新连上服务端后，状态又变为CONNECTED，在通常情况下，客户端的状态总是介于CONNECTING和CONNECTED之间。但是，如果出现诸如会话超时、权限检查或是客户端主动退出程序等情况，客户端的状态就会直接变更为CLOSE状态

#### 1.2.2.ZK数据模型

ZooKeeper的视图结构和标准的Unix文件系统类似，其中每个节点称为“数据节点”或ZNode,每个znode可以存储数据，还可以挂载子节点，因此可以称之为“树”

第二点需要注意的是，每一个znode都必须有值，如果没有值，节点是不能创建成功的。

![](/assets/7yh5hs0ij.png)

* 在Zookeeper中，znode是一个跟Unix文件系统路径相似的节点，可以往这个节点存储或获取数据
* 通过客户端可对znode进行增删改查的操作，还可以注册watcher监控znode的变化。

#### 1.2.3.Zookeeper节点类型

节点类型非常重要，是后面项目实战的基础。

a、Znode有两种类型：

短暂（ephemeral）（create -e /app1/test1 “test1” 客户端断开连接zk删除ephemeral类型节点）

持久（persistent） （create -s /app1/test2 “test2” 客户端断开连接zk不删除persistent类型节点）

b、Znode有四种形式的目录节点（默认是persistent ）

PERSISTENT

PERSISTENT\_SEQUENTIAL（持久序列/test0000000019 ）

EPHEMERAL

EPHEMERAL\_SEQUENTIAL

c、创建znode时设置顺序标识，znode名称后会附加一个值，顺序号是一个单调递增的计数器，由父节点维护

![](/assets/8iushgf0.png)

d、在分布式系统中，顺序号可以被用于为所有的事件进行全局排序，这样客户端可以通过顺序号推断事件的顺序

#### 1.2.4.Zookeeper节点状态属性

#### ![](/assets/43859jfhj909i.png)1.2.5.ACL保障数据的安全

ACL机制，表示为scheme:id:permissions，第一个字段表示采用哪一种机制，第二个id表示用户，permissions表示相关权限（如只读，读写，管理等）。

zookeeper提供了如下几种机制（scheme）：

world: 它下面只有一个id, 叫anyone, world:anyone代表任何人，zookeeper中对所有人有权限的结点就是属于world:anyone的

auth: 它不需要id, 只要是通过authentication的user都有权限（zookeeper支持通过kerberos来进行authencation, 也支持username/password形式的authentication\)

digest: 它对应的id为username:BASE64\(SHA1\(password\)\)，它需要先通过username:password形式的authentication

ip: 它对应的id为客户机的IP地址，设置的时候可以设置一个ip段，比如ip:192.168.1.0/16, 表示匹配前16个bit的IP段

现在看这可能懵懵懂懂，不过没有关系，等会在客户端操作的时候会有详细的操作

### 1.3.命令行

#### 1.3.1.服务端常用命令

在准备好相应的配置之后，可以直接通过zkServer.sh 这个脚本进行服务的相关操作

启动ZK服务:       sh bin/zkServer.sh start 

查看ZK服务状态: sh bin/zkServer.sh status 

停止ZK服务:       sh bin/zkServer.sh stop 

重启ZK服务:       sh bin/zkServer.sh restart

#### 1.3.2.客户端常用命令

使用 zkCli.sh -server 127.0.0.1:2181 连接到 ZooKeeper 服务，连接成功后，系统会输出 ZooKeeper 的相关环境以及配置信息。 命令行工具的一些简单操作如下：

* 显示根目录下、文件： ls / 使用 ls 命令来查看当前 ZooKeeper 中所包含的内容
* 显示根目录下、文件： ls2 / 查看当前节点数据并能看到更新次数等数据
* 创建文件，并设置初始内容： create /zk "test" 创建一个新的 znode节点“ zk ”以及与它关联的字符串  \[-e\] \[-s\] 【-e 零时节点】 【-s 顺序节点】
* 获取文件内容： get /zk 确认 znode 是否包含我们所创建的字符串  \[watch\]【watch 监听】
* 修改文件内容： set /zk "zkbak" 对 zk 所关联的字符串进行设置 
* 删除文件： delete /zk 将刚才创建的 znode 删除，如果存在子节点删除失败
* 递归删除：rmr  /zk将刚才创建的 znode 删除，子节点同时删除
* 退出客户端： quit 
* 帮助命令： help

#### 1.3.3.ACL命令常用命令

再回过头来看下ACL权限

Zookeeper的ACL\(Access Control List\)，分为三个维度：scheme、id、permission

通常表示为：scheme:id:permission

* schema:代表授权策略
* id:代表用户
* permission:代表权限

#### 1.3.3.1.Scheme

world：

默认方式，相当于全世界都能访问

auth：代表已经认证通过的用户\(可以通过addauth digest user:pwd 来添加授权用户\)

digest：即用户名:密码这种方式认证，这也是业务系统中最常用的

ip：使用Ip地址认证

1.3.3.2.id

id是验证模式，不同的scheme，id的值也不一样。

scheme为auth时：username:password

scheme为digest时:username:BASE64\(SHA1\(password\)\)

scheme为ip时:客户端的ip地址。

scheme为world时:anyone。

#### 1.3.3.3.Permission

CREATE、READ、WRITE、DELETE、ADMIN 也就是 增、删、改、查、管理权限，这5种权限简写为crwda\(即：每个单词的首字符缩写\)

CREATE\(c\)：创建子节点的权限

DELETE\(d\)：删除节点的权限

READ\(r\)：读取节点数据的权限

WRITE\(w\)：修改节点数据的权限

ADMIN\(a\)：设置子节点权限的权限

#### 1.3.3.4.ACL命令

getAcl

获取指定节点的ACL信息

create /testDir/testAcl deer  \# 创建一个子节点

getAcl /testDir/testAcl      \# 获取该节点的acl权限信息

setAcl: 设置指定节点的ACL信息

setAcl /testDir/testAcl world:anyone:crwa   \# 设置该节点的acl权限

getAcl /testDir/testAcl   \# 获取该节点的acl权限信息，成功后，该节点就少了d权限

create /testDir/testAcl/xyz xyz-data   \# 创建子节点

delete /testDir/testAcl/xyz    \# 由于没有d权限，所以提示无法删除

addauth

注册会话授权信息

Auth

addauth digest user1:123456                \# 需要先添加一个用户

setAcl /testDir/testAcl auth:user1:123456:crwa   \# 然后才可以拿着这个用户去设置权限

getAcl /testDir/testAcl    \# 密码是以密文的形式存储的

create /testDir/testAcl/testa aaa

delete /testDir/testAcl/testa   \# 由于没有d权限，所以提示无法删除

退出客户端后：

ls /testDir/testAcl  \#没有权限无法访问

create /testDir/testAcl/testb bbb \#没有权限无法访问

addauth digest user1:123456  \# 重新新增权限后可以访问了

Digest

auth与digest的区别就是，前者使用明文密码进行登录，后者使用密文密码进行登录

create /testDir/testDigest  data

addauth digest user1:123456

setAcl /testDir/testDigest digest:user1:HYGa7IZRm2PUBFiFFu8xY2pPP/s=:crwa   \# 使用digest来设置权限

注意：这里如果使用明文，会导致该znode不可访问

通过明文获得密文

shell&gt;

java -Djava.ext.dirs=/soft/zookeeper-3.4.12/lib -cp /soft/zookeeper-3.4.12/zookeeper-3.4.12.jar org.apache.zookeeper.server.auth.DigestAuthenticationProvider deer:123456

deer:123456-&gt;deer:ACFm5rWnnKn9K9RN/Oc8qEYGYDs=

使用CMD 运行Java命令得到加密的字符串![](/assets/2187hdshfjhs0.png)acl命令行ip

create  /testDir/testIp data

setAcl  /testDir/testIp ip:192.168.30.10:cdrwa

getAcl  /testDir/testIp

#### 1.3.4.常用四字命令

ZooKeeper 支持某些特定的四字命令字母与其的交互。用来获取 ZooKeeper 服务的当前状态及相关信息。可通过 telnet 或 nc 向 ZooKeeper 提交相应的命令 ：

当然，前提是安装好了nc

echo stat\|nc 127.0.0.1 2181 来查看哪个节点被选择作为follower或者leader

使用echo ruok\|nc 127.0.0.1 2181 测试是否启动了该Server，若回复imok表示已经启动。

echo dump\| nc 127.0.0.1 2181 ,列出未经处理的会话和临时节点。

echo kill \| nc 127.0.0.1 2181 ,关掉server

echo conf \| nc 127.0.0.1 2181 ,输出相关服务配置的详细信息。

echo cons \| nc 127.0.0.1 2181 ,列出所有连接到服务器的客户端的完全的连接 / 会话的详细信息

echo envi \|nc 127.0.0.1 2181 ,输出关于服务环境的详细信息（区别于 conf 命令）。

echo reqs \| nc 127.0.0.1 2181 ,列出未经处理的请求。

echo wchs \| nc 127.0.0.1 2181 ,列出服务器 watch 的详细信息。

echo wchc \| nc 127.0.0.1 2181 ,通过 session 列出服务器 watch 的详细信息，它的输出是一个与 watch 相关的会话的列表。

echo wchp \| nc 127.0.0.1 2181 ,通过路径列出服务器 watch 的详细信息。它输出一个与 session 相关的路径。

#### 1.3.5.ZooKeeper 日志可视化

前面以及讲了两个非常重要的配置一个是dataDir，存放的快照数据，一个是dataLogDir，存放的是事务日志文件

java -cp /soft/zookeeper-3.4.12/zookeeper-3.4.12.jar:/soft/zookeeper-3.4.12/lib/slf4j-api-1.7.25.jar org.apache.zookeeper.server.LogFormatter log.1

java -cp /soft/zookeeper-3.4.12/zookeeper-3.4.12.jar:/soft/zookeeper-3.4.12/lib/slf4j-api-1.7.25.jar org.apache.zookeeper.server.SnapshotFormatter log.1

1.4.Java客户端框架（\*重要）

1.4.1.Zookeeper原生客户端

1.4.2.ZkClient

1.4.3.Curator

