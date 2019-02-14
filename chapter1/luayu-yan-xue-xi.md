#### linux下安装

```
curl -R -O http://www.lua.org/ftp/lua-5.3.0.tar.gz
tar zxf lua-5.3.0.tar.gz
cd lua-5.3.0
make linux test
make install

注意如果执行make linux test 报错，错误如果如下：
```

![](/assets/32489fjkasjf.png)

```
lua.c:67:31: fatal error: readline/readline.h: No such file or directory
说明缺少libreadline-dev依赖包

centos(linux): yum install readline-devel
debian: apt-get install libreadline-dev
就可以安装好了~~~~
```

##### helloworld.lua

```
创建helloworld.lua文件，编辑，输入
print("hello world!") wq保存并退出

lua helloworld.lua
```

![](/assets/sahdjash192e1231.png)

#### 交互式编程

Lua提供了交互式编程模式，我们可以在命令行中输入程序并立即查看效果

Lua交互式编程模式可以通过命令_**lua -i **_或_** lua**_ 启用

![](/assets/89812jksjdka.png)

#### 脚本式编程

我们可以将Lua程序保持到一个以lua结尾的文件，并执行，该模式成为脚本编程，创建helloworld.lua文件，添加

```
print("hello world!");
```

使用lua名执行以上脚本，输出结果：

![](/assets/423819230qfajkajk.png)

我们也可以在代码修改为如下形式来执行脚本（在开头添加：\#!/usr/local/bin/lua）

```
#!/usr/local/bin/lua

print("hello world!");
```

以上代码中，我们指定了 Lua 的解释器 /usr/local/bin directory。加上 \# 号标记解释器会忽略它。接下来我们为脚本_**添加可执行权限**_，并执行：

![](/assets/2e189asdjais.png)

![](/assets/213879asfjkajgk.png)

#### 注释

##### 单行注释

两个减号是单行注释

```
--
```

##### 多行注释

```
--[[
    多行注释
    多行注释
--]]
```

#### 标示符

lua标示符用于定义一个变量，函数获取其他用户定义的项，标示符以一个字符A到Z或者a到z或下划线\_开头后加上0个或多个字母，下划线，数字（0-9）

最好不要使用下划线加大写字母的标示符，因为Lua的保留字也是这样的。

Lua 不允许使用特殊字符如 @, $, 和 % 来定义标示符。 Lua 是一个区分大小写的编程语言。因此在 Lua 中 Runoob 与 runoob 是两个不同的标示符。以下列出了一些正确的标示符：

```
mohd         zara      abc     move_name    a_123
myname50     _temp     j       a23b9        retVal
```

#### 关键词

以下列出了 Lua 的保留关键字。保留关键字不能作为常量或变量或其他用户自定义标示符：

| and | break | do | else |
| :--- | :--- | :--- | :--- |
| elseif | end | false | for |
| function | if | in | local |
| nil | not | or | repeat |
| return | then | true | until |
| while |  |  |  |

  


