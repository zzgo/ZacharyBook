参考

[http://gitbook.zhangjikai.com/commands.html](http://gitbook.zhangjikai.com/commands.html)

[http://blog.csdn.net/wirelessqa/article/details/72616471](http://blog.csdn.net/wirelessqa/article/details/72616471)

[https://www.v2ex.com/amp/t/345331](https://www.v2ex.com/amp/t/345331)

https://blog.csdn.net/fghsfeyhdf/article/details/88403548

初始化cli

**npm install gitbook-cli -g**

切换到对应目录

gitbook init 初始化

gitbook build 重建

gitbook serve 启动

如果报错

gitbook -V 查看 版本

缺失什么插件的话

使用

npm install gitbook-plugin-接插件名称

如配置了 可折叠目录，在根目录下 新建book.json内容

```java
{
    "plugins": ["expandable-chapters-small"],
    "pluginsConfig": {
        "expandable-chapters-small":{}
    }
}
```

会提示没有这个js或者css

运行** npm install gitbook-plugin-expandable-chapters-small** 即下载该插件

