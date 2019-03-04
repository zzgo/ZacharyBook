### 镜像操作

**docker \[ images \| rmi \| tag \| build \| history \| save \| load \]**

* images：列出本地镜像列表
* rmi \[镜像名称:版本/镜像ID\]：删除镜像
* tag \[镜像名:版本\] \[仓库\]/\[镜像名\]：标记本地镜像，将其归入某一仓库
* build -t \[镜像名:版本\] \[path\]：Dockerfile创建镜像
* history \[镜像名:版本/镜像ID\] ：查看指定镜像的创建历史
* save -o xxx.tar \[镜像名:版本\]  / save \[镜像名:版本\]  &gt;xxx.tar ：将镜像保存成tar归档文件
* load -input xxx.tar  / docker load &lt;xxx.tar : 从归档文件加载镜像

**docker images 列出镜像**

```java
[root@localhost /]# docker images 
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
my/tomcat           v2                  25c509fe6cfd        About an hour ago   453MB
nginx               latest              8c9ca4d17702        4 days ago          109MB
tomcat              latest              168588387c68        3 weeks ago         463MB
```

**docker rmi 移除镜像**

```java
[root@localhost /]# docker rmi my/tomcat:v2
Untagged: my/tomcat:v2
Deleted: sha256:25c509fe6cfdee8a1861168b9abcfd4ce3a8ca65aab9915af2addc9187c74cd5
Deleted: sha256:4d1289365ebd636d9d23714b1f27c06ccec9d52acc9662e73f9aac2617118a7b
[root@localhost /]# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
nginx               latest              8c9ca4d17702        4 days ago          109MB
tomcat              latest              168588387c68        3 weeks ago         463MB
```

**docker tag 标记本地镜像，将其归入某一个仓库**

将tomcat:latest 标记为 my/tomcat:v2版本

```
[root@localhost /]# docker tag tomcat:latest my/tomcat:v1
[root@localhost /]# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
nginx               latest              8c9ca4d17702        4 days ago          109MB
tomcat              latest              168588387c68        3 weeks ago         463MB
my/tomcat           v1                  168588387c68        3 weeks ago         463MB
```

**docker build -t 使用Dockerfile来制造镜像**

学习了Dockerfile会笔记

**docker history 查看指定镜像的创建历史**

```java
[root@localhost /]# docker history my/tomcat:v1 
IMAGE               CREATED             CREATED BY                                      SIZE                COMMENT
168588387c68        3 weeks ago         /bin/sh -c #(nop)  CMD ["catalina.sh" "run"]    0B                  
<missing>           3 weeks ago         /bin/sh -c #(nop)  EXPOSE 8080                  0B                  
<missing>           3 weeks ago         /bin/sh -c set -e  && nativeLines="$(catalin…   0B                  
<missing>           3 weeks ago         /bin/sh -c set -eux;   savedAptMark="$(apt-m…   18.1MB              
<missing>           3 weeks ago         /bin/sh -c #(nop)  ENV TOMCAT_ASC_URLS=https…   0B                  
<missing>           3 weeks ago         /bin/sh -c #(nop)  ENV TOMCAT_TGZ_URLS=https…   0B                  
<missing>           3 weeks ago         /bin/sh -c #(nop)  ENV TOMCAT_SHA512=3a3e624…   0B                  
<missing>           3 weeks ago         /bin/sh -c #(nop)  ENV TOMCAT_VERSION=8.5.38    0B                  
<missing>           3 weeks ago         /bin/sh -c #(nop)  ENV TOMCAT_MAJOR=8           0B                  
<missing>           3 weeks ago         /bin/sh -c #(nop)  ENV GPG_KEYS=05AB33110949…   0B                  
<missing>           3 weeks ago         /bin/sh -c apt-get update && apt-get install…   1.86MB              
<missing>           3 weeks ago         /bin/sh -c set -ex;  currentVersion="$(dpkg-…   0B                  
<missing>           3 weeks ago         /bin/sh -c #(nop)  ENV OPENSSL_VERSION=1.1.0…   0B                  
<missing>           3 weeks ago         /bin/sh -c #(nop)  ENV LD_LIBRARY_PATH=/usr/…   0B                  
<missing>           3 weeks ago         /bin/sh -c #(nop)  ENV TOMCAT_NATIVE_LIBDIR=…   0B                  
<missing>           3 weeks ago         /bin/sh -c #(nop) WORKDIR /usr/local/tomcat     0B                  
<missing>           3 weeks ago         /bin/sh -c mkdir -p "$CATALINA_HOME"            0B                  
<missing>           3 weeks ago         /bin/sh -c #(nop)  ENV PATH=/usr/local/tomca…   0B                  
<missing>           3 weeks ago         /bin/sh -c #(nop)  ENV CATALINA_HOME=/usr/lo…   0B                  
<missing>           3 weeks ago         /bin/sh -c set -ex;   if [ ! -d /usr/share/m…   309MB               
<missing>           3 weeks ago         /bin/sh -c #(nop)  ENV JAVA_DEBIAN_VERSION=8…   0B                  
<missing>           3 weeks ago         /bin/sh -c #(nop)  ENV JAVA_VERSION=8u181       0B                  
<missing>           3 weeks ago         /bin/sh -c #(nop)  ENV JAVA_HOME=/docker-jav…   0B                  
<missing>           3 weeks ago         /bin/sh -c ln -svT "/usr/lib/jvm/java-8-open…   33B                 
<missing>           3 weeks ago         /bin/sh -c {   echo '#!/bin/sh';   echo 'set…   87B                 
<missing>           3 weeks ago         /bin/sh -c #(nop)  ENV LANG=C.UTF-8             0B                  
<missing>           3 weeks ago         /bin/sh -c apt-get update && apt-get install…   2.05MB              
<missing>           3 weeks ago         /bin/sh -c set -ex;  if ! command -v gpg > /…   7.81MB              
<missing>           3 weeks ago         /bin/sh -c apt-get update && apt-get install…   23.2MB  ###以上 安装软件，配置环境变量            
<missing>           3 weeks ago         /bin/sh -c #(nop)  CMD ["bash"]                 0B                  
<missing>           3 weeks ago         /bin/sh -c #(nop) ADD file:4fec879fdca802d69…   101MB  ##基础镜像 centos
```





