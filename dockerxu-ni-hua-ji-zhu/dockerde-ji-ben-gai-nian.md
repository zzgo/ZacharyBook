### Docker的基本概念

#### Docker架构

![](/assets/123e89ajdjak.png)

| Images | Docker镜像，用于创建Docker容器的模板。 |
| :--- | :--- |
| Container | Docker容器，独立运行的一个或一组应用。 |
| Client | Docker客户端，使用DockerApi与Docker的守护进程通信。 |
| Host | Docker主机，一个物理或者虚拟的机器用于执行Docker守护进程和容器。 |
| Registry | Docker仓库，用来保存镜像 |
| Machine | 一个简化Docker安装的命令行工具，比如VirtualBox、 Digital Ocean、Microsoft Azure。 |

host是主机的载体，是docker安装的地方

继承类比：

Class  extends Class1    Object o = new Class2   -&gt; 此时o的对象结构中，有class1的成员结构

image2 extends image1   Container c = new image2 -&gt; 此时，c容器中，有image1的文件，但是重名文件会被覆盖掉（类似于子类实现父类的方法）

#### Docker镜像

#### Docker容器

#### docker仓库

#### 总结

![](/assets/2189sjdaj.png)

三个部分：镜像（Image\)、容器（Container）、仓库（Respository）

```linux
# docker pull redis 
# docker run -d --name redis redis
```

#### 容器、镜像的运行关系

![](/assets/27812sdaha.png)



