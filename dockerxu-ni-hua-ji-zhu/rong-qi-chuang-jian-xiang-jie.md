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



