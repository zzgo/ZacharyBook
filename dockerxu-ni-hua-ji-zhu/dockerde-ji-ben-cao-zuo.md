### 容器操作

docker \[ run \| start \| stop \| restart \| kill \| rm \| pause \| unpause \]

* run/create \[镜像名\]：创建一个新的容器并运行一个命令
* start/stop/restart\[容器名\]：启动/停止/重启一个容器
* kill \[容器名\]：直接杀掉容器，不给进程响应时间
* rm \[容器名\]：删除已经停止的容器 
* pause/unpause\[容器名\]：暂停、恢复容器中的进程

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

```java
[root@VM_0_6_centos ~]# docker create hello-world
e7c4a834d114cc51150408e839e5870871ffe93e79f601f1197c9c05f8374b34
```

```java
[root@VM_0_6_centos ~]# docker start tomcat
tomcat
```

```java
[root@VM_0_6_centos ~]# docker stop tomcat 
tomcat
```

```java
[root@VM_0_6_centos ~]# docker restart tomcat 
tomcat
```



