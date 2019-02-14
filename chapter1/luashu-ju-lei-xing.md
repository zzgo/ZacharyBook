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



