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

##### login登录dockerhub

```java
[root@localhost ~]# docker login
Login with your Docker ID to push and pull images from Docker Hub. If you don't have a Docker ID, head over to https://hub.docker.com to create one.
Username: zachary123
Password: 
WARNING! Your password will be stored unencrypted in /root/.docker/config.json.
Configure a credential helper to remove this warning. See
https://docs.docker.com/engine/reference/commandline/login/#credentials-store

Login Succeeded
```

##### 

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

```java
[root@localhost ~]# docker push zachary123/hello:2.0 
The push refers to repository [docker.io/zachary123/hello]
af0b15c8625b: Pushing [==================================================>]  3.584kB
af0b15c8625b: Pushed 
2.0: digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a size: 524
```

![](/assets/1223fdsfds.png)

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

拉取镜像

##### docker pull registry

```java
[root@localhost ~]# docker pull registry
Using default tag: latest
latest: Pulling from library/registry
c87736221ed0: Pull complete 
1cc8e0bb44df: Pull complete 
54d33bcb37f5: Pull complete 
e8afc091c171: Pull complete 
b4541f6d3db6: Pull complete 
Digest: sha256:3b00e5438ebd8835bcfa7bf5246445a6b57b9a50473e89c02ecc8e575be3ebb5
Status: Downloaded newer image for registry:latest
```

启动：

```java
[root@localhost ~]# docker run -d --name reg -p 5000:5000 registry
6ce7844282cf0e9e82813c5b88ce7eed14fd312cd0642429f512d1f94792d695
```

##### 然后通过restful接口查看仓库中的镜像（当前仓库是空的）

```java
[root@localhost ~]# curl http://192.168.111.128:5000/v2/_catalog
{"repositories":[]}
```

#### 配置http传输

私服默认只能使用https，需要配置开放http

**"insecure-registries":\["192.168.111.128:5000"\]**

```java
[root@localhost ~]# vi /etc/docker/daemon.json 

{
  "registry-mirrors": ["https://registry.docker-cn.com"],
  "insecure-registries":["192.168.111.128:5000"]
}
```

配置完成重启服务

```java
[root@localhost ~]# systemctl daemon-reload
[root@localhost ~]# systemctl restart docker
```

#### 私服仓库推送镜像

【注意】：这个**名字必须完整，带有链接，该链接指向私服仓库服务器**。

```java
[root@localhost ~]# docker tag hello-world:latest 192.168.111.128:5000/hello-world:2.0.0
[root@localhost ~]# docker images
REPOSITORY                         TAG                 IMAGE ID            CREATED             SIZE
registry                           latest              f32a97de94e1        3 weeks ago         25.8MB
nginx                              latest              8c9ca4d17702        4 weeks ago         109MB
tomcat                             latest              168588387c68        7 weeks ago         463MB
my/tomcat                          v1                  168588387c68        7 weeks ago         463MB
192.168.111.128:5000/hello-world   2.0.0               fce289e99eb9        3 months ago        1.84kB
hello-world                        latest              fce289e99eb9        3 months ago        1.84kB
```

推送到私服仓库里

使用 **push 命令，并查看信息**

```
[root@localhost ~]# docker push 192.168.111.128:5000/hello-world:2.0.0 
The push refers to repository [192.168.111.128:5000/hello-world]
af0b15c8625b: Pushed 
2.0.0: digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a size: 524
[root@localhost ~]# curl http://192.168.111.128:5000/v2/_catalog 
{"repositories":["hello-world"]}
[root@localhost ~]# curl http://192.168.111.128:5000/v2/hello-world/tags/list
{"name":"hello-world","tags":["2.0.0"]}

```



