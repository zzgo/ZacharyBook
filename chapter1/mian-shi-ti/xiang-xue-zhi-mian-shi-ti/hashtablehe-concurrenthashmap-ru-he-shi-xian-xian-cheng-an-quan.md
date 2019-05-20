### Hashtable和ConcurrentHashMap如何实现线程安全？

#### 未做同步控制时，代码在多线程下是安全的吗？

在最简单的代码下，在多线程中中进行运算，它也是线程不安全的

#### 由此及彼，看看HashMap的代码，它是线程线程安全的吗？

```java
public V put(K key, V value) {
        return putVal(hash(key), key, value, false, true);
}

public V get(Object key) {
        Node<K,V> e;
        return (e = getNode(hash(key), key)) == null ? null : e.value;
}
```

没有相关的java内置锁或者锁机制，所以HashMap是不安全的

#### 那么线程安全的Map-HashTable是如何实现线程安全的呢？

```java
public synchronized V put(K key, V value) {
    ....
}
public synchronized V get(Object key) {
    ....
}
```

可以看到方法上加了内置锁，以此来实现线程安全，但是在大并发量是其效率很低

#### 为什么有了HashTable为何还要有个ConcurrentHashMap?

在高并发下，Hashtable效率比较低下

#### ConcurrentHashMap是如何实现线程安全的呢？

JDK1.7之前，**分段锁**的实现，

1000桶，划分为16个段，

每个段有独立的锁，每个段加锁。，段与段之间是没有关系的。当你在修改第一个段的数据时，你取第二个段的数据，是可以取到的。不会受影响。

![](/assets/12389dsahaj.png)

JDK1.8以后，1000桶，则有1000把锁，也就是每一个桶有一把锁，锁的粒度变小了。

总结

Hashtable 使用 一把锁保证线程安全

ConcurrentHashMap 分段，多把锁来保证线程安全，并且性能更高![](/assets/12389qhd.png)

另外它提供了关于volatile可见性

可以清楚看见，当前的值和下一个指针都是volatile关键字修饰

```
 static class Node<K,V> implements Map.Entry<K,V> {
        final int hash;
        final K key;
        volatile V val;
        volatile Node<K,V> next;
```

使用**CAS以及可见性获取元素**

```
private static final sun.misc.Unsafe U;
    private static final long SIZECTL;
    private static final long TRANSFERINDEX;
    private static final long BASECOUNT;
    private static final long CELLSBUSY;
    private static final long CELLVALUE;
    private static final long ABASE;
    private static final int ASHIFT;

    static {
        try {
            U = sun.misc.Unsafe.getUnsafe();
            Class<?> k = ConcurrentHashMap.class;
            SIZECTL = U.objectFieldOffset
                (k.getDeclaredField("sizeCtl"));
            TRANSFERINDEX = U.objectFieldOffset
                (k.getDeclaredField("transferIndex"));
            BASECOUNT = U.objectFieldOffset
                (k.getDeclaredField("baseCount"));
            CELLSBUSY = U.objectFieldOffset
                (k.getDeclaredField("cellsBusy"));
            Class<?> ck = CounterCell.class;
            CELLVALUE = U.objectFieldOffset
                (ck.getDeclaredField("value"));
            Class<?> ak = Node[].class;
            ABASE = U.arrayBaseOffset(ak);
            int scale = U.arrayIndexScale(ak);
            if ((scale & (scale - 1)) != 0)
                throw new Error("data type scale not a power of two");
            ASHIFT = 31 - Integer.numberOfLeadingZeros(scale);
        } catch (Exception e) {
            throw new Error(e);
        }
    }
```

```
  static final <K,V> Node<K,V> tabAt(Node<K,V>[] tab, int i) {
        return (Node<K,V>)U.getObjectVolatile(tab, ((long)i << ASHIFT) + ABASE);
    }

    static final <K,V> boolean casTabAt(Node<K,V>[] tab, int i,
                                        Node<K,V> c, Node<K,V> v) {
        return U.compareAndSwapObject(tab, ((long)i << ASHIFT) + ABASE, c, v);
    }

    static final <K,V> void setTabAt(Node<K,V>[] tab, int i, Node<K,V> v) {
        U.putObjectVolatile(tab, ((long)i << ASHIFT) + ABASE, v);
    }
```



