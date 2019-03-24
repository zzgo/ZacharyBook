## 容器创建详解

### 交互式创建容器并进入

**docker run -it --name tomcat tomcat:latest /bin/bash**

```
[root@VM_0_6_centos /]# docker run -it --name tomcat tomcat:latest /bin/bash
root@821e200f85e9:/usr/local/tomcat#
```

注意：这样创建的是一个前台进程，**exit **退出则容器也退出，**Ctrl+P+Q**退出，不关闭容器

### 后台启动容器

**docker run -dit --name tomcat tomcat:latest：-d 后台，-it 交互式shell**

```java
[root@VM_0_6_centos /]# docker run -dit --name tomcat tomcat:latest 
2085009a9b2868ad85ceebe1ac830a4f9721a6f8ba505fce2cf3b29f2444139a
```

返回的这一串，也就是容器的ID，前12位表示容器ID.

### 进入已运行的容器

使用**exec**命令：docker exec -it tomcat /bin/bash

查看元数据：docker **inspect **tomcat

```java
[root@VM_0_6_centos /]# docker exec -it tomcat /bin/bash
root@2085009a9b28:/usr/local/tomcat#
```

### 绑定容器端口到主机

-p port1:port2 -&gt; 将port2映射到port1上，外界就是访问port1端口

```java
[root@VM_0_6_centos /]# docker run -dit -p 8082:8080  --name tomcat tomcat:latest 
fc7165552f7f2541fceb01b8f749297b77caf4dd85c106899077ba8c64695137
[root@VM_0_6_centos /]# docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS                    NAMES
fc7165552f7f        tomcat:latest       "catalina.sh run"   4 seconds ago       Up 2 seconds        0.0.0.0:8082->8080/tcp   tomcat
```

### 挂载主机文件到目录容器内

使用 **-v 主机文件:容器内部文件**

```java
[root@VM_0_6_centos ~]# docker run -it -v /root/1.txt:/usr/local/tomcat/1.txt --name tomcat tomcat:latest /bin/bash
root@003d58362571:/usr/local/tomcat# ls
1.txt	      CONTRIBUTING.md  NOTICE	  RELEASE-NOTES  bin   include	logs		temp	 work
BUILDING.txt  LICENSE	       README.md  RUNNING.txt	 conf  lib	native-jni-lib	webapps
root@003d58362571:/usr/local/tomcat# cat 1.txt 
123123123123123123123123123123123123123123123123123123123123123123123123123123123123123123123123123123123123123123123123123123123123123123123123123123123123123123123123123123123123123123123123123123123123123123123123123123123123123123123123123123123123123123123123123123123123123123123123123123123123123123123123123123123123123123123123123123123123123123123123123123123
```

### 复制主机文件到容器内





