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

```
[root@VM_0_6_centos ~]# docker restart tomcat 
tomcat
```

**docker kill \[容器名称/容器ID\]**

```
[root@VM_0_6_centos ~]# docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
623122f311d4        tomcat:latest       "catalina.sh run"   5 hours ago         Up 5 hours          8080/tcp            tomcat
[root@VM_0_6_centos ~]# docker kill tomcat 
tomcat
[root@VM_0_6_centos ~]# docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
```

**docker rm \[容器名称/容器ID\]**

```
[root@VM_0_6_centos ~]# docker ps -a
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                        PORTS               NAMES
623122f311d4        tomcat:latest       "catalina.sh run"   6 hours ago         Exited (137) 58 seconds ago                       tomcat
[root@VM_0_6_centos ~]# docker rm tomcat 
tomcat
[root@VM_0_6_centos ~]# docker ps -a
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
```

**docker \[ ps \| inspect \| exec \| logs \| export \| import \]**

ps：查看容器列表（默认查看正在运行的容器，-a 查看所有容器）

inspect \[容器名称/容器ID \]：查看容器配置元数据

exec -it \[容器名称/容器ID \] /bin/bash：进入容器环境中交互操作

logs -since=“2019-02-01” -f --tail=10 \[容器名称/容器ID\]：查看容器日志

cp path1 \[容器名称/容器ID\]：path 容器与主机间的数据拷贝

export -o test.tar \[容器名称/容器ID\] / docker export \[容器名称/容器ID\]&gt;test.tar ：文件系统作为一个tar归档文件

import test.tar \[镜像名:版本号\]：导入归档文件，成为一个镜像



