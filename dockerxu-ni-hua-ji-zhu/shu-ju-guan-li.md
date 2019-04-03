## 数据管理

docker容器运行，产生一些数据、文件等持久化的东西，不应该放在容器内部，应当以挂载的形式存在主机文件系统中

### docker文件系统

![](/assets/00kafai921.png)

镜像与容器读写层，通过联合文件系统，组成系统文件视角

容器服务运行中，一定会产生数据

容器只是运行态的服务器，是瞬时的，不承载数据的持久功能

### volume文件挂载的探究

volume参数创建容器数据卷

```java
启动并进入容器，-v 挂载目录，即数据
[root@localhost ~]# docker run --name data -v /opt/data -it centos:latest /bin/bash
[root@b654b96ac630 /]# 
```

使用 docker inspect data 查看data的元信息，其中包括：

```java
"Mounts": [
            {
                "Type": "volume",
                "Name": "21e3bad43d39b42fe175edbd28feb04d2d586b860726ec83f47403bfdbf78974",
                "Source": "/var/lib/docker/volumes/21e3bad43d39b42fe175edbd28feb04d2d586b860726ec83f47403bfdbf78974/_data",
                "Destination": "/opt/data",
                "Driver": "local",
                "Mode": "",
                "RW": true,
                "Propagation": ""
            }
        ],

```

在容器端添加一个文件

```java
[root@localhost ~]# docker exec  -it data /bin/bash
[root@b654b96ac630 /]# cd opt/data/ 
[root@b654b96ac630 data]# ll
total 0
[root@b654b96ac630 data]# echo 123 >a
[root@b654b96ac630 data]# ll
total 4
-rw-r--r--. 1 root root 4 Apr  3 09:42 a
```

回到主机目录查看，果然存在此文件

```java
[root@localhost ~]# cd /var/lib/docker/volumes/
[root@localhost volumes]# ls
21e3bad43d39b42fe175edbd28feb04d2d586b860726ec83f47403bfdbf78974  bce5172549bf49d4442c0ec92a93b94287e42b0c3061f7f94be1667f20ccbe93
757eb3bc6766c439aa483c07ac215dc87ca7de26fc8f52ce7f271c4a1f7e65f4  metadata.db
[root@localhost volumes]# cd 21e3bad43d39b42fe175edbd28feb04d2d586b860726ec83f47403bfdbf78974/
[root@localhost 21e3bad43d39b42fe175edbd28feb04d2d586b860726ec83f47403bfdbf78974]# ls
_data
[root@localhost 21e3bad43d39b42fe175edbd28feb04d2d586b860726ec83f47403bfdbf78974]# cd _data/
[root@localhost _data]# ll
total 4
-rw-r--r--. 1 root root 4 Apr  3 17:42 a
```



