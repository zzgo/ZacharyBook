## 1、数组

### 特点

简单，数据是一种简单的数据结构

占连续的内存，数组空间连续，按照申请的顺序存储，但是必须制定数组大小

数组空间效率低，数组中经常有空闲的区域没有得到充分的应用

操作麻烦，数据的增加和删除操作麻烦（增加指中部插入）

## 2、顺序表

代表，如ArrayList集合系列

插入操作，都会使得整个数据的数据整体后移，为这个元素的插入提供位置。

删除操作，都会使得整个数据的数据整体前移，为这个元素的将这个位置赋值给其他元素。

### 以ArrayList为例，增删改查

```
   /**
     * Default initial capacity.
     */
    private static final int DEFAULT_CAPACITY = 10;//默认初始化容量

    /**
     * Shared empty array instance used for empty instances.
     */
    private static final Object[] EMPTY_ELEMENTDATA = {};//空

    /**
     * Shared empty array instance used for default sized empty instances. We
     * distinguish this from EMPTY_ELEMENTDATA to know how much to inflate when
     * first element is added.
     */
    private static final Object[] DEFAULTCAPACITY_EMPTY_ELEMENTDATA = {};//默认空

    /**
     * The array buffer into which the elements of the ArrayList are stored.
     * The capacity of the ArrayList is the length of this array buffer. Any
     * empty ArrayList with elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA
     * will be expanded to DEFAULT_CAPACITY when the first element is added.
     */
    transient Object[] elementData; // non-private to simplify nested class access，保存的数据，不能被序列化

    /**
     * The size of the ArrayList (the number of elements it contains).
     *
     * @serial
     */
    private int size;//记录数据使用长度
```

### Add方法

1.判断数组是否满足能加入数据，不能则要扩充，扩充大小依据

```
//扩容完了后，容量是三倍，可用容量是以前的两倍
int newCapacity = oldCapacity + (oldCapacity >> 1);

public void add(int index, E element) {
        rangeCheckForAdd(index);//加入元素，数组是否越界

        ensureCapacityInternal(size + 1);  //填入元素，是否长度满足，不满足要进行扩容操作
        System.arraycopy(elementData, index, elementData, index + 1,
                         size - index);//在index 处后的元素进行拷贝后移操作，为插入的元素提供位置
        elementData[index] = element;//将元素值赋值给当前位置
        size++;//计数加加
    }

```

### Remove方法

```
public E remove(int index) {
        rangeCheck(index);//检查数据越界

        modCount++;//纪录被操作的次数
        E oldValue = elementData(index);//拿到当前元素

        int numMoved = size - index - 1;//计算移动的长度
        if (numMoved > 0)
            System.arraycopy(elementData, index+1, elementData, index,
                             numMoved);//从index+1开始，移动到index开始，移动numMoved长度
        elementData[--size] = null; // 将数组随后以为置为空，gc 回收
        return oldValue;//返回旧值
    }
```



