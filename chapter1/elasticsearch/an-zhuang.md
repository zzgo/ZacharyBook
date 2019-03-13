### 安装

### 下载

在官文进行下载[elasticsearch](https://www.elastic.co/downloads/elasticsearch)文件包，选择linux版本的，可以下载到本地将文件传输到linux上，也可以使用wget将文件直接在linux内下载

### 解压

```
tar -zxvf elasticsearch #你的elasticsearch文件名称
```

### 运行

```
进入到 bin目录下使用
> ./elasticsearch 运行
```

### 注意

elasticsearch是使用的java开发，所以确保你安装**jdk软件**

以**root用户运行会报错**

```java
[root@localhost bin]# ./elasticsearch
[2019-03-13T09:43:45,775][WARN ][o.e.b.ElasticsearchUncaughtExceptionHandler] [unknown] uncaught exception in thread 
org.elasticsearch.bootstrap.StartupException: java.lang.RuntimeException: can not run elasticsearch as root
	at org.elasticsearch.bootstrap.Elasticsearch.init(Elasticsearch.java:163) ~[elasticsearch-6.6.2.jar:6.6.2]
	at org.elasticsearch.bootstrap.Elasticsearch.execute(Elasticsearch.java:150) ~[elasticsearch-6.6.2.jar:6.6.2]
	at org.elasticsearch.cli.EnvironmentAwareCommand.execute(EnvironmentAwareCommand.java:86) ~[elasticsearch-6.6
	at org.elasticsearch.cli.Command.mainWithoutErrorHandling(Command.java:124) ~[elasticsearch-cli-6.6.2.jar:6.6
	at org.elasticsearch.cli.Command.main(Command.java:90) ~[elasticsearch-cli-6.6.2.jar:6.6.2]
	at org.elasticsearch.bootstrap.Elasticsearch.main(Elasticsearch.java:116) ~[elasticsearch-6.6.2.jar:6.6.2]
	at org.elasticsearch.bootstrap.Elasticsearch.main(Elasticsearch.java:93) ~[elasticsearch-6.6.2.jar:6.6.2]
Caused by: java.lang.RuntimeException: can not run elasticsearch as root
	at org.elasticsearch.bootstrap.Bootstrap.initializeNatives(Bootstrap.java:103) ~[elasticsearch-6.6.2.jar:6.6.
	at org.elasticsearch.bootstrap.Bootstrap.setup(Bootstrap.java:170) ~[elasticsearch-6.6.2.jar:6.6.2]
	at org.elasticsearch.bootstrap.Bootstrap.init(Bootstrap.java:333) ~[elasticsearch-6.6.2.jar:6.6.2]
	at org.elasticsearch.bootstrap.Elasticsearch.init(Elasticsearch.java:159) ~[elasticsearch-6.6.2.jar:6.6.2]
	... 6 more

```

这是出于系统安全考虑设置的条件。由于ElasticSearch可以接收用户输入的脚本并且执行，为了系统安全考虑，

建议创建一个单独的用户用来运行ElasticSearch

解决方案

创建elsearch用户组及elsearch用户

```bash
[root@localhost bin]# groupadd elsearch
[root@localhost bin]# useradd elsearch -g elsearch -p elasticsearch
```

切换到elsearch用户再启动

```
[root@localhost bin]# su elsearch
[root@localhost bin]# ./elasticsearch

运行报了一下错误的话，是由于用户没有全权限
Exception in thread "main" java.nio.file.AccessDeniedException: /data/zachary/elasticsearch-6.6.2/config/jvm.options
	at sun.nio.fs.UnixException.translateToIOException(UnixException.java:84)
	at sun.nio.fs.UnixException.rethrowAsIOException(UnixException.java:102)
	at sun.nio.fs.UnixException.rethrowAsIOException(UnixException.java:107)
	at sun.nio.fs.UnixFileSystemProvider.newByteChannel(UnixFileSystemProvider.java:214)
```

原因：当前用户没有执行权限 

解决方法： chown linux用户名 elasticsearch安装目录 -R 

例如：chown ealsticsearch /data/wwwroot/elasticsearch-6.2.4 -R 

PS：其他Java软件报.AccessDeniedException错误也可以同样方式解决，给 执行用户相应的目录权限即可

```
切换到root用户，给elsearch赋权限
[root@localhost elasticsearch-6.6.2]# chown elsearch /data/zachary/elasticsearch-6.6.2 -R 
[root@localhost elasticsearch-6.6.2]# su elsearch
[elsearch@localhost elasticsearch-6.6.2]$ ./bin/elasticsearch
[2019-03-13T09:59:22,826][INFO ][o.e.e.NodeEnvironment    ] [_wP3r_O] using [1] data paths, mounts [[/ (rootfs)]], net usable_space [30.4gb], net total_space [36.9gb], types [rootfs]
[2019-03-13T09:59:22,871][INFO ][o.e.e.NodeEnvironment    ] [_wP3r_O] heap size [1015.6mb], compressed ordinary object pointers [true]
[2019-03-13T09:59:22,873][INFO ][o.e.n.Node               ] [_wP3r_O] node name derived from node ID [_wP3r_O2RvigxQOoBmpYFg]; set [node.name] to override
[2019-03-13T09:59:22,873][INFO ][o.e.n.Node               ] [_wP3r_O] version[6.6.2], pid[1967], build[default/tar/3bd3e59/2019-03-06T15:16:26.864148Z], OS[Linux/3.10.0-862.el7.x86_64/amd64], JVM[Oracle Corporation/Java HotSpot(TM) 64-Bit Server VM/1.8.0_201/25.201-b09]
[2019-03-13T09:59:22,874][INFO ][o.e.n.Node               ] [_wP3r_O] JVM arguments [-Xms1g, -Xmx1g, -XX:+UseConcMarkSweepGC, -XX:CMSInitiatingOccupancyFraction=75, -XX:+UseCMSInitiatingOccupancyOnly, -Des.networkaddress.cache.ttl=60, -Des.networkaddress.cache.negative.ttl=10, -XX:+AlwaysPreTouch, -Xss1m, -Djava.awt.headless=true, -Dfile.encoding=UTF-8, -Djna.nosys=true, -XX:-OmitStackTraceInFastThrow, -Dio.netty.noUnsafe=true, -Dio.netty.noKeySetOptimization=true, -Dio.netty.recycler.maxCapacityPerThread=0, -Dlog4j.shutdownHookEnabled=false, -Dlog4j2.disable.jmx=true, -Djava.io.tmpdir=/tmp/elasticsearch-8310282720009846186, -XX:+HeapDumpOnOutOfMemoryError, -XX:HeapDumpPath=data, -XX:ErrorFile=logs/hs_err_pid%p.log, -XX:+PrintGCDetails, -XX:+PrintGCDateStamps, -XX:+PrintTenuringDistribution, -XX:+PrintGCApplicationStoppedTime, -Xloggc:logs/gc.log, -XX:+UseGCLogFileRotation, -XX:NumberOfGCLogFiles=32, -XX:GCLogFileSize=64m, -Des.path.home=/data/zachary/elasticsearch-6.6.2, -Des.path.conf=/data/zachary/elasticsearch-6.6.2/config, -Des.distribution.flavor=default, -Des.distribution.type=tar]
[2019-03-13T09:59:31,014][INFO ][o.e.p.PluginsService     ] [_wP3r_O] loaded module [aggs-matrix-stats]
[2019-03-13T09:59:31,015][INFO ][o.e.p.PluginsService     ] [_wP3r_O] loaded module [analysis-common]

```

就可以了

### 测试

```java
[root@localhost ~]# curl http://localhost:9200/?pretty
{
  "name" : "_wP3r_O",
  "cluster_name" : "elasticsearch",
  "cluster_uuid" : "qahgYZ4eRLOUxhe9yD8aug",
  "version" : {
    "number" : "6.6.2",
    "build_flavor" : "default",
    "build_type" : "tar",
    "build_hash" : "3bd3e59",
    "build_date" : "2019-03-06T15:16:26.864148Z",
    "build_snapshot" : false,
    "lucene_version" : "7.6.0",
    "minimum_wire_compatibility_version" : "5.6.0",
    "minimum_index_compatibility_version" : "5.0.0"
  },
  "tagline" : "You Know, for Search"
}
```

说明我们的elasticsearch已经成功了

