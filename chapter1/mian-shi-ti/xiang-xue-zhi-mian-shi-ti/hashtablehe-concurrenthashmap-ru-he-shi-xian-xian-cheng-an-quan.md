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

在高并发下，其效率不高

#### ConcurrentHashMap是如何实现线程安全的呢？

JDK1.7之前，**分段锁**的实现，

采用了16段，16个锁，首先根据hashcode%16来获取到拿那一把锁，然后在去拿数据

JDK1.8以后，采用了每一个Node都是一把锁，锁的粒度变得更小了

