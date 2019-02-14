### Lua数据类型

Lua是动态类型语言，变量不要类型定义，只需要为变量赋值，值可以存储在变量中，作为参数传递或结果返回

Lua中有8个基本类型分别为：nil、boolean、numebr、string、userdata、function、thread、table

| 数据类型 | 描述 |
| :--- | :--- |
| nil | 这个最简单，只有值nil属于该类，表示一个无效值（在条件表达式中相当于false）。 |
| boolean | 包含两个值：false和true。 |
| number | 表示双精度类型的实浮点数 |
| string | 字符串由一对双引号或单引号来表示 |
| function | 由 C 或 Lua 编写的函数 |
| userdata | 表示任意存储在变量中的C数据结构 |
| thread | 表示执行的独立线路，用于执行协同程序 |
| table | Lua 中的表（table）其实是一个"关联数组"（associative arrays），数组的索引可以是数字或者是字符串。在 Lua 里，table 的创建是通过"构造表达式"来完成，最简单构造表达式是{}，用来创建一个空表。 |

我们可以使用type函数测试给定变量或者值的类型：

![](/assets/21378fjkfhjakhfjka.png)

#### nil（空）

nil类型表示一种没有任何有效值，他只有一个值--nil，例如打印一个没有赋值的变量，便会输出值nil：

```
> print(type(abc))
nil
>
```

对于全局变量和table，nil还有一个删除作用，给全局变量或者table表里的变量赋一个nil值，等同于把它删掉，执行下面代码：

![](/assets/2189jfkajfjka.png)

![](/assets/12390qwdjaksjdk.png)

nil作比较是应该加上双引号_**" **_:

```
> type(X)
nil
> type(X)==nil
false
> type(X)=="nil"
true
>
```

_**type\(X\)==nil 结果为 false 的原因是因为 type\(type\(X\)\)==string**_

#### boolean（布尔）

boolean类型只有两个可选值：true（真）和false（假），Lua把false和nil看作是假，其他都是真

![](/assets/vcpbco32478.png)

![](/assets/kjohpgkj90.png)

number（数字）

Lua 默认只有一种 number 类型 -- double（双精度）类型（默认类型可以修改 luaconf.h 里的定义），以下几种写法都被看作是 number 类型：

```
print(type(2))
print(type(2.2))
print(type(0.2))
print(type(2e+1))
print(type(0.2e-1))
print(type(7.8263692594256e-06))
```

结果：

```
number
number
number
number
number
number
```

String（字符串）

_**字符串由一对双引号或单引号来表示。**_

```
str1 = "this is string1"
str2 = 'this is string2'
```

也可以用2个方括号_**"\[\[\]\]"**_来表示一块字符串

```
html = [[
<html>
<head></head>
<body>
    <a href="http://www.baidu.com">百度</a>
<body>
</html>
]]
print(html);
```

执行：

```
<html>
<head></head>
<body>
    <a href="http://www.baidu.com">百度</a>
<body>
</html>
```

在对一个数字字符串进行算术操作时，_**Lua会尝试将这个数字字符串转换成一个数字**_

```
> printf("2" + 3)
6.0
> print("2" + 3 )
5.0
> print("2 + 3")
2 + 3
> print("-2e2" * "6")
-1200
> print("error" + 1)
stdin:1: attempt to perform arithmetic on a string value
stack traceback:
    stdin:1: in main chunk
    [C]: in ?
>
```

以上代码中”error“ + 1执行报错了，字符串连接使用的是 _**".."**_，如：

```
> print("a" .. "b")
ab
> print(123 .. 456)
123456
>
```

使用_**\#来计算字符串的长度**_，放在字符串前面，如下实例

```
>len = "www.baidu.com"
> print(#len)
13
>print(#"www.baidu.com")
13
>
```

#### table（表）

在Lua里，table的创建是通过“构造表达式”来完成，最简单构造表达式是{}，用来创建一个空表，也可以在表里添加一些数据

```
-- 创建一个空的table
local tab = {}

--直接初始化表
local tab2 = {"a","b","c","d"}
```

Lua中的表（table）其实是一个“关联数组”（associative arrays），数组的索引可以是数字或者是字符串

```
-- table_test.lua 脚本文件
a = {}
a["key"] = "value"
key = 10
a[key] = 22
a[key] = a[key] + 11
for k,v in pairs(a) do
    print(k .. " : " .. v)
end
```

脚本执行结果：

```
$ lua table_test.lua
key : value
10 : 33
```

不同于其他语言的数组把0作为数组的初始索引，在Lua里表的默认初始索引一般以1开始

```
-- table_test2.lua 脚本文件
local tab = {"a","b","c","d"}
for key,val in pairs(tab) do
    print("key",key)
end
```

脚本执行结果

```
$ lua table_test2.lua
key 1
key 2
key 3
key 4
```

table不会固定长度大小，有新数据添加时table长度会自动增长，没初始的table都是nil

```
-- table_test3.lua 脚本文件
a = {}
for i = 1,10 do
    a[i] = i
end
a["key"] = "val"
print(a["key"])
print(a["none"])
```

脚本执行结果是：

```
$ lua table_test3.lua 
val
nil
```

#### function（函数）

在Lua中，函数是被看做是“第一类值（First-Class Value）”，函数可以存在变量

```
-- function_test.lua 脚本文件
function factorial(n)
    if n == 0 then
        return 1
    else
        return n * factorial(n - 1)
    end
end
print(factorial(5))
f2 = factorial
print(f2(5))
```

执行结果：

```
$ lua function_test.lua
120
120
```

function函数可以以匿名函数（anonymous function）的方式通过参数传递

```
-- function_test2.lua 脚本文件
function testFun(tab,fun)
    for k,v in pairs(tab) do
        print(fun(k,v))
    end
end

tab = {k1="v1",k2="v2"};
testFun(tab,
    function(k,v)
        return k .. "=" .. v;
    end
);
```



