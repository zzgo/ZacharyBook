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

curl [http://192.168.111.128:5000/v2/\_catalog](http://192.168.111.128:5000/v2/_catalog)  
   查看仓库

curl [http://192.168.111.128:5000/v2/hello-world/tags/list](http://192.168.111.128:5000/v2/hello-world/tags/list) 查看版本

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

### commit 镜像并上传仓库

#### 创建一个centos容器

启动并进入此容器，默认去library/centos拉取

```java
[root@localhost ~]# docker run -it --name centos centos /bin/bash/
Unable to find image 'centos:latest' locally
latest: Pulling from library/centos
8ba884070f61: Pull complete 
Digest: sha256:8d487d68857f5bc9595793279b33d082b03713341ddec91054382641d14db861
Status: Downloaded newer image for centos:latest
```

结果进不去。

```
docker run -dit --name  centos centos:latest
```

启动容器，exec进入容器

```
docker exec -it centos /bin/bash
```

#### 容器内安装nginx服务

添加nginx源：

```
rpm -ivh http://nginx.org/packages/centos/7/noarch/RPMS/nginx-release-centos-7-0.el7.ngx.noarch.rpm
yum search nginx    ##搜索一下看看
yum install nginx -y    ## 安装
```

启动nginx服务

```
[root@78035124e536 /]# whereis nginx   ##找一下服务
nginx: /usr/sbin/nginx /usr/lib64/nginx /etc/nginx /usr/share/nginx
[root@78035124e536 /]# /usr/sbin/nginx  ##启动
[root@78035124e536 /]# curl 172.17.0.3  ## 查看，ip 可以在外面由 docker inspect centos 得到
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
    body {
        width: 35em;
        margin: 0 auto;
        font-family: Tahoma, Verdana, Arial, sans-serif;
    }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
```

#### commit服务为一个nginx镜像

现在将cent容器提交成为一个镜像，命令如下

docker commit centos centos-nginx:1.0，得到新的镜像centos-nginx:1.0

```java
[root@localhost ~]# docker commit centos centos-nginx:1.0
sha256:0828df89613a8af9ea1b8c8d899053860347fad2466c2d5f0a9865b48e903a6f
[root@localhost ~]# docker images
REPOSITORY                         TAG                 IMAGE ID            CREATED             SIZE
centos-nginx                       1.0                 0828df89613a        8 seconds ago       292MB
```

#### 启动此nginx镜像

```java
[root@localhost ~]# docker run -dit --name nginx centos-nginx:1.0 
87c462bbe29ab537311d9c60163920a55045dbbe5778e7d7b33ff3ea25c32a11[root@localhost ~]# docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                    NAMES
87c462bbe29a        centos-nginx:1.0    "/bin/bash"              5 seconds ago       Up 2 seconds                                 nginx
[root@localhost ~]# docker exec -it nginx /bin/bash
[root@87c462bbe29a /]# curl 172.17.0.4
curl: (7) Failed connect to 172.17.0.4:80; Connection refused
```

进入容器发现并没有启动服务

```java
[root@87c462bbe29a /]# /usr/sbin/nginx
[root@87c462bbe29a /]# curl 172.17.0.4
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
    body {
        width: 35em;
        margin: 0 auto;
        font-family: Tahoma, Verdana, Arial, sans-serif;
    }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
```

我们希望启动容器的时候，直接启动nginx服务，怎么做？

```java
docker run -d --name nginx centos-nginx:1.0 /usr/sbin/nginx -g "daemon 0ff"
```

启动起来：

```java
[root@localhost ~]# docker run -dit --name nginx centos-nginx:1.0 /usr/sbin/nginx -g "daemon off;"
ae341c78f1d4a614090a39c50bd630d3e56c1b640f75244ce06e67485fad465b
[root@localhost ~]# docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                    NAMES
ae341c78f1d4        centos-nginx:1.0    "/usr/sbin/nginx -g …"   4 seconds ago       Up 2 seconds                                 nginx
```

【注意】

后面运行的命令都是容器命令，由于nginx命令没有设置到path中，所以全路径启动，

而nginx -g这个参数是指可以在外面添加指令到nginx的配置文件中，

daemon off是指nginx服务不运行在后端，而是在前台运行（container中的服务必须运行在前台）

**启动并在容器内nginx也启动，并且在外面也可以访问**

```java
[root@localhost ~]# docker run -p 80:80  -dit --name nginx centos-nginx:1.0 /usr/sbin/nginx -g "daemon off;"
cd7c7175cbd9c17ad1a480cb4161a05d5d8fc89f7d81e6ffb0b1123cecc5ba7a
[root@localhost ~]# docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                    NAMES
cd7c7175cbd9        centos-nginx:1.0    "/usr/sbin/nginx -g …"   2 seconds ago       Up 1 second         0.0.0.0:80->80/tcp       nginx
78035124e536        centos:latest       "/bin/bash"              42 minutes ago      Up 42 minutes                                centos
6ce7844282cf        registry            "/entrypoint.sh /etc…"   23 hours ago        Up 9 hours          0.0.0.0:5000->5000/tcp   reg
[root@localhost ~]# curl 192.168.111.128
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
    body {
        width: 35em;
        margin: 0 auto;
        font-family: Tahoma, Verdana, Arial, sans-serif;
    }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
```

#### commit创建镜像方式的本质

![](/assets/w839hasjdhaj.png)





