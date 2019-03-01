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



