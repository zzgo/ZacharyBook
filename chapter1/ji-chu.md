## 基础

泛型

有三种书写方式：

```
//只接收T类型的数据类型
List<T> list = new ArrayList<T>();
//集合元素可以是任意类型，这种没有意义，一般是方法中，只是为了说明用法
List<?> list = new ArrayList<?>();
//  泛型的限定：
//           ? extends E:接收E类型或者E的子类型。
//           ? super E:接收E类型或者E的父类型。
List<? extends E> list = new ArrayList<? extends E>();
List<? super E> list = new ArrayList<? super E>();
//相对更加复杂一点的
<T extends Comparable<? super T>>
```

[public &lt;T&gt; void show\(T t\),void前面的泛型T是什么作用](https://www.cnblogs.com/hym-pcitc/p/6116489.html)

public &lt;T&gt;这个T是个修饰符的功能，表示是个**泛型方法**，就像有static修饰的方法是个静态方法一样。

&lt;T&gt; 不是返回值，表示传入参数有泛型

public static &lt;T&gt;list&lt;T&gt; aslist\(T...a\)  

第一个表示是泛型方法,第二个表示返回值是list类型，而这个list有泛型，只能存t类型的数据

