## 镜像与容器原理及用法探究

### history 

查看镜像层，docker histroy 镜像名\|镜像ID

```java
[root@VM_0_6_centos /]# docker history tomcat:latest 
IMAGE               CREATED             CREATED BY                                      SIZE                COMMENT
168588387c68        6 weeks ago         /bin/sh -c #(nop)  CMD ["catalina.sh" "run"]    0B       #安装tomcat，及配置信息           
<missing>           6 weeks ago         /bin/sh -c #(nop)  EXPOSE 8080                  0B                  
<missing>           6 weeks ago         /bin/sh -c set -e  && nativeLines="$(catalin…   0B                  
<missing>           6 weeks ago         /bin/sh -c set -eux;   savedAptMark="$(apt-m…   18.1MB              
<missing>           6 weeks ago         /bin/sh -c #(nop)  ENV TOMCAT_ASC_URLS=https…   0B                  
<missing>           6 weeks ago         /bin/sh -c #(nop)  ENV TOMCAT_TGZ_URLS=https…   0B                  
<missing>           6 weeks ago         /bin/sh -c #(nop)  ENV TOMCAT_SHA512=3a3e624…   0B                  
<missing>           6 weeks ago         /bin/sh -c #(nop)  ENV TOMCAT_VERSION=8.5.38    0B                  
<missing>           6 weeks ago         /bin/sh -c #(nop)  ENV TOMCAT_MAJOR=8           0B                  
<missing>           6 weeks ago         /bin/sh -c #(nop)  ENV GPG_KEYS=05AB33110949…   0B                  
<missing>           6 weeks ago         /bin/sh -c apt-get update && apt-get install…   1.86MB              
<missing>           6 weeks ago         /bin/sh -c set -ex;  currentVersion="$(dpkg-…   0B                  
<missing>           6 weeks ago         /bin/sh -c #(nop)  ENV OPENSSL_VERSION=1.1.0…   0B                  
<missing>           6 weeks ago         /bin/sh -c #(nop)  ENV LD_LIBRARY_PATH=/usr/…   0B                  
<missing>           6 weeks ago         /bin/sh -c #(nop)  ENV TOMCAT_NATIVE_LIBDIR=…   0B                  
<missing>           6 weeks ago         /bin/sh -c #(nop) WORKDIR /usr/local/tomcat     0B                  
<missing>           6 weeks ago         /bin/sh -c mkdir -p "$CATALINA_HOME"            0B                  
<missing>           6 weeks ago         /bin/sh -c #(nop)  ENV PATH=/usr/local/tomca…   0B                  
<missing>           6 weeks ago         /bin/sh -c #(nop)  ENV CATALINA_HOME=/usr/lo…   0B                  
<missing>           6 weeks ago         /bin/sh -c set -ex;   if [ ! -d /usr/share/m…   309MB               
<missing>           6 weeks ago         /bin/sh -c #(nop)  ENV JAVA_DEBIAN_VERSION=8…   0B       #安装和配置JDK环境           
<missing>           6 weeks ago         /bin/sh -c #(nop)  ENV JAVA_VERSION=8u181       0B                  
<missing>           6 weeks ago         /bin/sh -c #(nop)  ENV JAVA_HOME=/docker-jav…   0B                  
<missing>           6 weeks ago         /bin/sh -c ln -svT "/usr/lib/jvm/java-8-open…   33B                 
<missing>           6 weeks ago         /bin/sh -c {   echo '#!/bin/sh';   echo 'set…   87B                 
<missing>           6 weeks ago         /bin/sh -c #(nop)  ENV LANG=C.UTF-8             0B                  
<missing>           6 weeks ago         /bin/sh -c apt-get update && apt-get install…   2.05MB              
<missing>           6 weeks ago         /bin/sh -c set -ex;  if ! command -v gpg > /…   7.81MB              
<missing>           6 weeks ago         /bin/sh -c apt-get update && apt-get install…   23.2MB              
<missing>           6 weeks ago         /bin/sh -c #(nop)  CMD ["bash"]                 0B                  
<missing>           6 weeks ago         /bin/sh -c #(nop) ADD file:4fec879fdca802d69…   101MB #centos 镜像
```

### 查看镜像文件

可以使用**docker inspect 镜像名称\|ID，其中：**

```java
 "GraphDriver": {
            "Data": {
                "LowerDir": "/var/lib/docker/overlay2/1dc7e4131027c0ff34966fbfdbe1702401aa84bc1a6674f85dbbd230a762881d/diff:/var/lib/docker/overlay2/2775ecf5e075ee0607ec29c27f8d53bb73f2075ea7ec25d1d1f9cffc51577ae0/diff:/var/lib/docker/overlay2/b9278196270a386eca9d1762c3240c160af3e5eb9e4c4865329f781511a65e36/diff:/var/lib/docker/overlay2/e2bb33df63a8922b843ec8daa181ae4831b9753dce4b9cb76f3a9cf2ba851855/diff:/var/lib/docker/overlay2/592ae489ecbc34744650b69f57fd0adf4071a01923a04263e16b6a605a3cf47f/diff:/var/lib/docker/overlay2/63c43c3336c851e06711fd0dfa47b4b63321974f9188b5e8bfdeacac96dbd35c/diff:/var/lib/docker/overlay2/71a29889c044577e542b074afe03ad4d921e5cb8439a622ad04c4189167a48d7/diff:/var/lib/docker/overlay2/2d22c18fe45584e676801f7a4d97ee97effa5e4876099183155a8a827d9c96f8/diff:/var/lib/docker/overlay2/f6d6b6e31bed87f97aece77e6ffa3b455f69663c9c726ec76413587a8f7ccc67/diff:/var/lib/docker/overlay2/37a1c44418359ccb9e9d9417e4d2645f4a7da0c9408b96b4e78f318e39c5a68c/diff",
                "MergedDir": "/var/lib/docker/overlay2/1084d205730569ab194829bf48bd5ce663e0c6c51135735e1f5e452c8cf987d6/merged",
                "UpperDir": "/var/lib/docker/overlay2/1084d205730569ab194829bf48bd5ce663e0c6c51135735e1f5e452c8cf987d6/diff",
                "WorkDir": "/var/lib/docker/overlay2/1084d205730569ab194829bf48bd5ce663e0c6c51135735e1f5e452c8cf987d6/work"
            },
            "Name": "overlay2"
        },
        "RootFS": {
            "Type": "layers",
            "Layers": [
                "sha256:13d5529fd232cacdd8cd561148560e0bf5d65dbc1149faf0c68240985607c303",
                "sha256:abc3250a6c7ff22a6a366d9c175033ef0b2859f9d03676410c2f21d0fe568da4",
                "sha256:578414b395b98d02c5f284e83c8db080afcbbde8012478054af22df2edb9336d",
                "sha256:8be692af5632366e9d96177b91c7b89f2dfee3972457fee96cd269bc83f14dc0",
                "sha256:699c7914defb637a055ce8d924bbc5ff029a39dcda82e8344caa008fe513364e",
                "sha256:73a5184b491e8352a5d56c7effc7bd6fcab733e6c264ceffeb64fc53079bf97f",
                "sha256:a6414350cc66470b974c7a54a310107be3abb4b2c8d7f9d314bbac8746617b6c",
                "sha256:2a26f9e31825549fe98a5d84fccb8a2806cefc359a60afba4a20952743e1c85e",
                "sha256:8bf71984452f98a783e2f307490dc2e4b1ace804b4afe56b89f5bff0a284c723",
                "sha256:54b63532b9653edd74ae2b3ef06718efffbe6b767b0d40f3e240cdd608a92df8",
                "sha256:9a69d19707032c0fc079919d0f1161618859aae0811d9f1aafb325965c09eea6"
            ]
        },

```

可以看到overlay2，可以看到这个根目录**/var/lib/docker/**

镜像存放在**imagedb**里面，一般在**/var/lib/docker/image/overlay2/imagedb/content/sha256/**

```java
[root@VM_0_6_centos /]# cd /var/lib/docker/image/overlay2/imagedb/content/sha256/
[root@VM_0_6_centos sha256]# ls
168588387c685909be8799b0e282d1da942ba8916f244af814c935f2831e861f  fce289e99eb9bca977dae136fbe2a82b6b7d4c372474c9235adc1741675f587e
[root@VM_0_6_centos sha256]# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
tomcat              latest              168588387c68        6 weeks ago         463MB
hello-world         latest              fce289e99eb9        2 months ago        1.84kB
```

**文件的前12位就是镜像的ID，该文件存放了配置信息。你可以【cat 文件名 】看一看**

其中，history数组内，标识了镜像的历史记录（与history命令内容对应） 

rootfs的diff\_ids中，对应了依赖使用中镜像层文件（history命令中size大于0的层）

### 查看镜像层文件

层文件在layerdb里，一般在/var/lib/docker/image/overlay2/layerdb/sha256/

```java
[root@VM_0_6_centos sha256]# cd /var/lib/docker/image/overlay2/layerdb/sha256/
[root@VM_0_6_centos sha256]# ls
03ab4405d55b60f458ca43972c790c1e1c5eabd08636ae787a3d61c38983231a  a179108277a4624379b27ea624234da329506ca18d5380df73c7b9cb35d34259
0cfeb05788c615d9a85c757580be66b2dbe088d5d3cc3d31fe90d5c98320947e  ac00eef3f557ea71d47b65f3112c0c3b6ae7113153b7205a94bedc496103a6c8
13d5529fd232cacdd8cd561148560e0bf5d65dbc1149faf0c68240985607c303  ad7da3b1e974035406b1f6d4bb8814264ac9e4850a61d8ee185051c35dcd3388
32cbdad586b72e25903b83c604944edd4e8bba003e2e82966a5d78c221dd46bc  af0b15c8625bb1938f1d7b17081031f649fd14e6b233688eea3c5483994a66a3
583fcd993af9c118477c8214783f25d978e5ce6745adce903a0b2ecd3f451204  b03861375a2739b4bb3c47a4191e01e6b968270f1f0d3a0622f478560a3a84cc
8f0ebc672b43403012c586feeac72ec83ce8347faab22a2826b952968aefc3d6  da5d02ff4f62d0ce92dbf27b405c1e449e1dea3b1f39a04746f03dab2de5e889
```

随便进入一个镜像文件

```java
[root@VM_0_6_centos sha256]# cd 03ab4405d55b60f458ca43972c790c1e1c5eabd08636ae787a3d61c38983231a/
[root@VM_0_6_centos 03ab4405d55b60f458ca43972c790c1e1c5eabd08636ae787a3d61c38983231a]# ll -s
total 652
  4 -rw-r--r-- 1 root root     64 Mar  2 17:34 cache-id
  4 -rw-r--r-- 1 root root     71 Mar  2 17:34 diff
  4 -rw-r--r-- 1 root root     71 Mar  2 17:34 parent
  4 -rw-r--r-- 1 root root      9 Mar  2 17:34 size #占用大小
636 -rw-r--r-- 1 root root 645925 Mar  2 17:34 tar-split.json.gz #镜像层文件包
```

一个镜像就是一层层的layer层文件，盖楼而成，上层文件叠于下层文件上，若上层文件有与下层文件重复的，则覆盖掉下层重复的部分，如下图：

![](/assets/1djausuda9a.png)

* 初始挂载时读写层为空。 
* 当需要修改镜像内的某个文件时，只对处于最上方的读写层进行了变动，不复写下层已有文件系统的内容，已有文件在只读层中的原始版本仍然存在，但会被读写层中的新版本文件所隐藏，当 docker commit 这个修改过的容器文件系统为一个新的镜像时，保存的内容仅为最上层读写文件系统中被更新过的文件。 
* 联合挂载是用于将多个镜像层的文件系统挂载到一个挂载点来实现一个统一文件系统视图的途径，是下层存储驱动\(aufs、overlay等\) 实现分层合并的方式。



