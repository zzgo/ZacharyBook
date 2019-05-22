## 类加载机制和双亲委派模型

![](/assets/8888ijjjjjooopl.png)

类的生命周期，就是这7个阶段

使用阶段就是new关键字，创建一个对象，卸载阶段，从虚拟机中移除掉

初始化，不是我们所说的初始化，这个初始化，是虚拟机给一些变量赋值默认值，比如int默认为0

在初始化后，在真正开始执行java代码，static{}代码块，在使用阶段后，才会执行我们的构造方法 construct\(\){}

加载器名称

| **类加载器名称** | **加载的范围** |
| :--- | :--- |
| 启动类加载器Bootstrap ClassLoader | 存放在&lt;JAVA\_HOME&gt;\lib目录中的，并且是虚拟机识别的类库加载到虚拟机内存中 |
| 扩展类加载器Extension ClassLoader | 存放在&lt;JAVA\_HOME&gt;\lib\ext目录中的所有类库，开发者可以直接使用； |
| 应用程序加Class载器Application Loader | 加载用户类路径上指定的类库，开发者可以直接使用，一般情况下这个就是程序中默认的类加载器； |

使用最多的是，应用程序加载器，也是默认的类加载器

还可以自定义类加载器
