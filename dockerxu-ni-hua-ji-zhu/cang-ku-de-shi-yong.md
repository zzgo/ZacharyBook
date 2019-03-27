## 仓库的使用

### docker 官方仓库

#### 注册

[https://hub.docker.com，自由注册，邮件激活即可使用](https://hub.docker.com，自由注册，邮件激活即可使用)

![](/assets/2178ashjhaj.png)创建一个仓库，hello。

#### 命令使用

docker **pull/search/login/push/tag**

tag \[镜像名：版本\]  \[仓库\]/\[镜像名：版本\]：标记本地镜像，将其归入某一仓库

push \[仓库\]/\[镜像名：版本\]: 推送镜像到仓库  --需要登陆

search \[镜像名\]：在仓库中查询镜像 – 无法查询到tag版本

pull \[镜像名：版本\]： 下载镜像到本地

login：登陆仓库

##### tag 命令标记一个镜像

docker tag 镜像名称\|镜像ID:版本 自己的镜像名称:版本

```java
[root@VM_0_6_centos ~]# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
tomcat              latest              168588387c68        6 weeks ago         463MB
hello-world         latest              fce289e99eb9        2 months ago        1.84kB
[root@VM_0_6_centos ~]# docker tag tomcat:latest my/tomcat:latest
[root@VM_0_6_centos ~]# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
tomcat              latest              168588387c68        6 weeks ago         463MB
my/tomcat           latest              168588387c68        6 weeks ago         463MB
hello-world         latest              fce289e99eb9        2 months ago        1.84kB
```

注意：这里的自己的镜像名称，默认使用的docker的镜像话，它会自动将前面的http:/docker.io补全，如果是自己的仓库的，需要这里这个名称加上去链接。比如 http:/localhost/your/imagesname

##### push命令推送镜像到仓库

docker push 镜像名称：版本

### 私有仓库

#### 搭建

下载registry镜像：**docker pull registry**

配置加速器加速下载

vi /etc/docker/daemon.json

```java
{
    "registry-mirrors": ["https://9cpn8tt6.mirror.aliyuncs.com"]
}
```

修改一下：

```java
{
    "registry-mirrors": ["https://registry.docker-cn.com"]
}
```



