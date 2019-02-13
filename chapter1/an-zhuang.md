安装过程

```
当完成Centos7操作系统安装后，先安装依赖包(确保虚拟机能上外网，不然不能安装)
1，依赖包检查
　1 yum install cpp -y  

　2 yum install binutils -y

　3 yum install glibc-kernheaders -y

　4 yum install glibc-common -y

　5 yum install glibc-devel -y

　6 yum install gcc -y

　7 yum install make -y

2、下载Redis源码，解压缩后编译源码。 
$ cd /usr/james
$ wget http://download.redis.io/releases/redis-4.0.6.tar.gz
$ tar xzf redis-4.0.6.tar.gz
$ cd redis-4.0.6
$ make

mkdir /usr/local/redis
cp redis-server /usr/james/redis 
cp redis-benchmark /usr/james/redis 
cp redis-check-rdb /usr/james/redis
cp redis-sentinel /usr/james/redis
cp redis-cli /usr/james/redis 
cp redis.conf /usr/james/redis 
----------------------------------------------------------------------------------------------

远程访问6379节点(后期做为主节点使用)：
redis.conf  修改 requirepass 12345678    注释掉bind 127.0.0.1 

从节点redis6380.conf， redis6381.conf:
修改 requirepass 12345678 ,注释掉bind 192.168.42.111, 加上masterauth 12345678 ,
加上slaveof 192.168.42.111 6379

redis sentinel哨兵机制配置(也是3个节点)：
   /usr/local/bin/conf/sentinel_26379.conf  
   /usr/local/bin/conf/sentinel_26380.conf
   /usr/local/bin/conf/sentinel_26381.conf
将三个文件的端口改成: 26379   26380   26381
然后：sentinel monitor mymaster 127.0.0.1 6379 2  //监听主节点6379
sentinel auth-pass mymaster 12345678     //连接主节点时的密码
三个配置除端口外，其它一样。
启动sentinel服务:
            ./redis-sentinel conf/sentinel_26379.conf &
            ./redis-sentinel conf/sentinel_26380.conf &
            ./redis-sentinel conf/sentinel_26381.conf &
测试：kill -9 6379  杀掉6379的redis服务
看日志是分配6380 还是6381做为主节点，当6379服务再启动时，已变成从节点

如果6380升级为主节点:进入6380>info replication     可以看到role:master
打开sentinel_26379.conf等三个配置，sentinel monitor mymaster 127.0.0.1 6380 2
外部应用连接sentinel时， sentinel.conf的protected-mode no改成no
./redis-cli -p 26380 shutdown //关闭

```



