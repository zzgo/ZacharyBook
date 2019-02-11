### 安装

> 官网下载安装介质：https://www.mongodb.com/download-center，选择适当的版本，这里以linux版本mongodb-linux-x86\_64-3.4.18为例；
>
> 解压到系统某路径， tar -xvzf mongodb-linux-x86\_64-3.4.18.tgz
>
>      并在安装目录创建data目录，以及logs目录和logs/mongodb.log文件
>
> 使用vim在mongodb的bin目录创建mongodb的配置文件，如：vim bin/mongodb.conf，mongodb.conf内容请见下一页课件；
>
> 编写shell脚本，命名为start-mongodb.sh，脚本内容如下：
>
>      nohup ./mongod -f mongodb.conf &
>
> 使用start-mongodb.sh启动mongodb实例，如：./start-mongodb
>
> 使用mongoClient进行测试，通过restAPI地址测试（端口加1000）

mongodb.conf

> storage:
>
>    dbPath: "/usr/local/apache/mongoDB/mongodb-linux-x86\_64-rhel70-3.4.10/data"
>
> systemLog:
>
>    destination: file
>
>    path: "/usr/local/apache//mongoDB/mongodb-linux-x86\_64-rhel70-3.4.10/logs/mongodb.log"
>
> net:
>
>    port: 27022
>
>    http:
>
>       RESTInterfaceEnabled: true
>
> processManagement:
>
>    fork: false

说明

> /usr/local/apache//mongoDB/mongodb-linux-x86\_64-rhel70-3.4.10/logs/mongodb.log 文件是保存日志信息的文件，需要自己创建文件
>
> /usr/local/apache/mongoDB/mongodb-linux-x86\_64-rhel70-3.4.10/data 这个data文件是自己创建的存放数据的文件



