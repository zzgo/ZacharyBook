### HashMap

#### 存储结构

数组、链表、红黑树（jdk1.8）

#### 特点

* 快速存储：Key:Value
* 快速查找（时间复杂度O\(1\)）
* 可伸缩：数组可以变长，链表超过8可以变红黑树

#### hash算法

所有对象都有hashCode\(使用key计算hash值\)

hash值的计算

\(hashCode\) ^ \(hashCode&gt;&gt;&gt;16\)：异或运算，hashCode向右移16位

#### 数组下标

数组默认大小16

数组下标：hash&\(16-1\) = hash%16 ：前面的计算效率高，与运算

