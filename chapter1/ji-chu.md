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



