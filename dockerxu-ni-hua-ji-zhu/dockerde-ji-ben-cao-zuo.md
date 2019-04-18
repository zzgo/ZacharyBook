### 容器操作

**docker \[ run \| start \| stop \| restart \| kill \| rm \| pause \| unpause \]**

* run/create \[镜像名\]：创建一个新的容器并运行一个命令
* start/stop/restart\[容器名\]：启动/停止/重启一个容器
* kill \[容器名\]：直接杀掉容器，不给进程响应时间
* rm \[容器名\]：删除已经停止的容器 
* pause/unpause\[容器名\]：暂停、恢复容器中的进程

**docker run \[镜像名称\]**

```java
[root@VM_0_6_centos ~]# docker run hello-world

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/
```

**docker create \[容器名称/容器ID\]**

```
[root@VM_0_6_centos ~]# docker create hello-world
e7c4a834d114cc51150408e839e5870871ffe93e79f601f1197c9c05f8374b34
```

docker start \[**容器名称/容器ID**\]

```
[root@VM_0_6_centos ~]# docker start tomcat
tomcat
```

**docker stop \[容器名称/容器ID\]**

```
[root@VM_0_6_centos ~]# docker stop tomcat 
tomcat
```

**docker restart \[容器名称/容器ID\]**

```java
[root@VM_0_6_centos ~]# docker restart tomcat 
tomcat
```

**docker kill \[容器名称/容器ID\]**

```java
[root@VM_0_6_centos ~]# docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
623122f311d4        tomcat:latest       "catalina.sh run"   5 hours ago         Up 5 hours          8080/tcp            tomcat
[root@VM_0_6_centos ~]# docker kill tomcat 
tomcat
[root@VM_0_6_centos ~]# docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
```

**docker rm \[容器名称/容器ID\]**

```java
[root@VM_0_6_centos ~]# docker ps -a
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                        PORTS               NAMES
623122f311d4        tomcat:latest       "catalina.sh run"   6 hours ago         Exited (137) 58 seconds ago                       tomcat
[root@VM_0_6_centos ~]# docker rm tomcat 
tomcat
[root@VM_0_6_centos ~]# docker ps -a
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
```

**docker \[ ps \| inspect \| exec \| logs \| export \| import \]**

* ps：查看容器列表（默认查看正在运行的容器，-a 查看所有容器）
* inspect \[容器名称/容器ID \]：查看容器配置元数据
* exec -it \[容器名称/容器ID \] /bin/bash：进入容器环境中交互操作
* logs --since=“2019-02-01” -f --tail=10 \[容器名称/容器ID\]：查看容器日志
* cp path1 \[容器名称/容器ID\]：path 容器与主机间的数据拷贝
* export -o test.tar \[容器名称/容器ID\] / docker export \[容器名称/容器ID\]&gt;test.tar ：文件系统作为一个tar归档文件
* import test.tar \[镜像名:版本号\]：导入归档文件，成为一个镜像

**docker ps \[-a\]  查看运行容器/所有容器**

```java
[root@VM_0_6_centos ~]# docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
a31d0c5a5e89        tomcat:latest       "catalina.sh run"   21 hours ago        Up 21 hours         8080/tcp            tomcat
[root@VM_0_6_centos ~]# docker ps -a
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
a31d0c5a5e89        tomcat:latest       "catalina.sh run"   21 hours ago        Up 21 hours         8080/tcp            tomcat
```

**docker inspect  查看容器配置元数据**

```java
[root@VM_0_6_centos ~]# docker inspect tomcat
[
    {
        "Id": "a31d0c5a5e89819fb240c076d8eabd4322a1b2cc8d34968cb9977d72c68345df",
        "Created": "2019-03-02T15:15:29.497558021Z",
        "Path": "catalina.sh",
        "Args": [
            "run"
        ],
        "State": {
            "Status": "running",
            "Running": true,
            "Paused": false,
            "Restarting": false,
            "OOMKilled": false,
            "Dead": false,
            "Pid": 5328,
            "ExitCode": 0,
            "Error": "",
            "StartedAt": "2019-03-02T15:15:29.911895961Z",

......................................................
]
```

注意：**inspect **该命令可以查看到docker容器的配置属性，以及**一些挂载的文件**

**doccker exec 进入容器环境中交互操作**

```java
[root@VM_0_6_centos ~]# docker exec -it tomcat /bin/bash
root@a31d0c5a5e89:/usr/local/tomcat#
```

进入到容器中，如果要退出容器的话，可以使用 `Ctrl + P + Q` 或者 `exit` 退出

**docker logs 查看日志**

查看从`2019-02-01 起`的日志，从`最后开始数10条`数据

```java
[root@VM_0_6_centos ~]# docker logs --since="2019-02-01" -f --tail=10 tomcat
02-Mar-2019 15:17:39.274 INFO [localhost-startStop-1] org.apache.catalina.startup.HostConfig.deployDirectory Deployment of web application directory [/usr/local/tomcat/webapps/manager] has finished in [50] ms
02-Mar-2019 15:17:39.274 INFO [localhost-startStop-1] org.apache.catalina.startup.HostConfig.deployDirectory Deploying web application directory [/usr/local/tomcat/webapps/ROOT]
02-Mar-2019 15:17:39.312 INFO [localhost-startStop-1] org.apache.catalina.startup.HostConfig.deployDirectory Deployment of web application directory [/usr/local/tomcat/webapps/ROOT] has finished in [38] ms
02-Mar-2019 15:17:39.312 INFO [localhost-startStop-1] org.apache.catalina.startup.HostConfig.deployDirectory Deploying web application directory [/usr/local/tomcat/webapps/docs]
02-Mar-2019 15:17:39.340 INFO [localhost-startStop-1] org.apache.catalina.startup.HostConfig.deployDirectory Deployment of web application directory [/usr/local/tomcat/webapps/docs] has finished in [28] ms
02-Mar-2019 15:17:39.341 INFO [localhost-startStop-1] org.apache.catalina.startup.HostConfig.deployDirectory Deploying web application directory [/usr/local/tomcat/webapps/host-manager]
02-Mar-2019 15:17:39.380 INFO [localhost-startStop-1] org.apache.catalina.startup.HostConfig.deployDirectory Deployment of web application directory [/usr/local/tomcat/webapps/host-manager] has finished in [39] ms
02-Mar-2019 15:17:39.388 INFO [main] org.apache.coyote.AbstractProtocol.start Starting ProtocolHandler ["http-nio-8080"]
02-Mar-2019 15:17:39.397 INFO [main] org.apache.coyote.AbstractProtocol.start Starting ProtocolHandler ["ajp-nio-8009"]
02-Mar-2019 15:17:39.406 INFO [main] org.apache.catalina.startup.Catalina.start Server startup in 128243 ms
```

查看容器名称为tomcat的88条日志信息（从最后开始算起，倒推88条数据）

```java
[root@VM_0_6_centos ~]# docker logs --tail 88 -f tomcat
```

**docker cp**

将主机/testcp/data目录拷贝到容器803a180e0562:/testcp/data/下

```java
##宿主主机文件
[root@localhost /]# cd testcp/data/
[root@localhost data]# ls
123.txt
##拷贝到容器中，容器的路径必须是存在的
root@803a180e0562:/# mkdir testcp
root@803a180e0562:/# cd testcp/
root@803a180e0562:/testcp# mkdir data
root@803a180e0562:/testcp# ls
data
##进行拷贝
[root@localhost data]# docker cp /testcp/data/ 803a180e0562:/testcp/data/ 
##进入容器看一看结果，是否有123.txt文件
[root@localhost data]# docker exec -it 803a180e0562 /bin/bash
root@803a180e0562:/# cd testcp/data/
root@803a180e0562:/testcp/data# ls
data
root@803a180e0562:/testcp/data# cd data/
root@803a180e0562:/testcp/data/data# ls
123.txt
### 容器最后带有”/“结果，是将主机的路径包含data复制到到容器data下
```

总结一下以下区别

```java
1、 docker cp /testcp/data 803a180e0562:/testcp/data
2、 docker cp /testcp/data 803a180e0562:/testcp/data/
3、 docker cp /testcp/data/ 803a180e0562:/testcp/data
4、 docker cp /testcp/data/ 803a180e0562:/testcp/data/
这四种写法，在容器内的路径都会是这样的
/testcp/data/data/123.txt
会将文件data 放在 容器路径下
5、docker cp /testcp/data/123.txt 803a180e0562:/testcp/data/
/testcp/data/123.txt 直接将文件放在容器路径下
```

从容器内拷贝到宿主主机上

```java
[root@localhost /]# docker cp 803a180e0562:/testcp/data testdata/
[root@localhost /]# cd testdata/
[root@localhost testdata]# ls
data
[root@localhost testdata]# cd data/
[root@localhost data]# ls
123.txt

###它一样是将容器的文件夹，在宿主上创建一个，放在指定的宿主机路径下
```

**docker export 系统文件作为一个tar归档文件**

```java
### 将镜像名称/镜像ID为tomcat导出为一个tomcat.tar文件
[root@localhost testcp]# docker export -o tomcat.tar tomcat
[root@localhost testcp]# ls
tomcat.tar
```

**docker import  导入归档文件，成为一个镜像**

```
### 将tomcat.war 制作一个镜像 名称 my/tomcat:v2 版本v2
[root@localhost testcp]# docker import tomcat.tar my/tomcat:v2
sha256:25c509fe6cfdee8a1861168b9abcfd4ce3a8ca65aab9915af2addc9187c74cd5
[root@localhost testcp]# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
my/tomcat           v2                  25c509fe6cfd        43 seconds ago      453MB
tomcat              latest              168588387c68        3 weeks ago         463MB
### 说明我们的镜像导入成功
```

**docer save 保存镜像成tar包**

```
docker save  6fa2a7f3d028  -o pathplan.tar
```



