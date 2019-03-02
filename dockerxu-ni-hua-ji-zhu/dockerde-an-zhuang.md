## Docker的安装

Docker支持CentOS6以后的版本

### 卸载

查询安装过的包

```java
[root@localhost /]# yum list installed | grep docker
docker-ce.x86_64                     18.06.1.ce-3.el7               @docker-ce-stable
```

删除安装包的软件包

```
yum -y remove docker-ce.x86_64
```

删除镜像、容器等

```
rm -rf /var/lib/docker
```

### CentOS6安装

对于CentOS6，可以使用EPEL库安装Docker，命令如下

```java
$ sudo yum install http://mirrors.yun-idc.com/epel/6/i386/epel-release-6-8.noarch.rpm
$ sudo yum install docker-io
```

### CentOS7安装

CentOS7系统CentOS-Extras库中已带Docker，可以直接安装

```
$ sudo yum install docker  ## 安装的不是最新版本
```

最新版安装

```
sudo yum install -y yum-utils device-mapper-persistent-data lvm2
sudo yum-config-manager --add-repo https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo 
sudo yum install docker-ce 

```

### 查看docker版本

**docker version**

```java
[root@VM_0_6_centos ~]# docker version
Client:
 Version:           18.09.2
 API version:       1.39
 Go version:        go1.10.6
 Git commit:        6247962
 Built:             Sun Feb 10 04:13:27 2019
 OS/Arch:           linux/amd64
 Experimental:      false

Server: Docker Engine - Community
 Engine:
  Version:          18.09.2
  API version:      1.39 (minimum version 1.12)
  Go version:       go1.10.6
  Git commit:       6247962
  Built:            Sun Feb 10 03:47:25 2019
  OS/Arch:          linux/amd64
  Experimental:     false
```

**docker info**

```java
[root@VM_0_6_centos ~]# docker info
Containers: 0
 Running: 0
 Paused: 0
 Stopped: 0
Images: 0
Server Version: 18.09.2
Storage Driver: overlay2
 Backing Filesystem: extfs
 Supports d_type: true
 Native Overlay Diff: false
Logging Driver: json-file
Cgroup Driver: cgroupfs
Plugins:
 Volume: local
 Network: bridge host macvlan null overlay
 Log: awslogs fluentd gcplogs gelf journald json-file local logentries splunk syslog
Swarm: inactive
Runtimes: runc
Default Runtime: runc
Init Binary: docker-init
containerd version: 9754871865f7fe2f4e74d43e2fc7ccd237edcbce
runc version: 09c8266bf2fcf9519a651b04ae54c967b9ab86ec
init version: fec3683
Security Options:
 seccomp
  Profile: default
Kernel Version: 3.10.0-514.26.2.el7.x86_64
Operating System: CentOS Linux 7 (Core)
OSType: linux
Architecture: x86_64
CPUs: 1
Total Memory: 1.797GiB
Name: VM_0_6_centos
ID: 72E4:JH6F:CUDB:D2Z4:OX5T:S4AR:QZNK:TPMH:STKO:NC65:NJFG:FEEB
Docker Root Dir: /var/lib/docker
Debug Mode (client): false
Debug Mode (server): false
Registry: https://index.docker.io/v1/
Labels:
Experimental: false
Insecure Registries:
 127.0.0.0/8
Registry Mirrors:
 https://9cpn8tt6.mirror.aliyuncs.com/
Live Restore Enabled: false
Product License: Community Engine
```

### 启动docker

**service docker start**

```java
[root@VM_0_6_centos ~]# service docker start
Redirecting to /bin/systemctl start  docker.service
```

### 设置随系统启动

**chkconfig docker on**

```java
[root@VM_0_6_centos ~]# chkconfig docker on
Note: Forwarding request to 'systemctl enable docker.service'.
Created symlink from /etc/systemd/system/multi-user.target.wants/docker.service to /usr/lib/systemd/system/docker.service.
```

### Docker 初体验

docker run hello-world     \#\#进入docker世界

```java
[root@VM_0_6_centos ~]# docker run hello-world
Unable to find image 'hello-world:latest' locally    ##本地没有这个镜像
latest: Pulling from library/hello-world             ##去远程仓库拉取这个镜像
1b930d010525: Pull complete 
Digest: sha256:2557e3c07ed1e38f26e389462d03ed943586f744621577a99efb77324b0fe535
Status: Downloaded newer image for hello-world:latest     ##拉取完成
#下面是这个docker 运行打印的结果
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



