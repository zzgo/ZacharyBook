下面的程序输出的结果是

```java
public class TestSync implements Runnable {
    int b = 100;

    public synchronized void m1() throws InterruptedException {
        b = 1000;
        Thread.sleep(500);
        System.out.println(" b = " + b);
    }

    public synchronized void m2() throws InterruptedException {
        Thread.sleep(250);
        b = 2000;
    }


    @Override
    public void run() {
        try {
            m1();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }

    public static void main(String[] args) throws InterruptedException {
        TestSync ts = new TestSync();
        Thread t = new Thread(ts);
        t.start();
        ts.m2();
        System.out.println("main  b = " + ts.b);
    }
}
```

输出结果

```java
main  b = 1000
 b = 1000
或者
main  b = 2000
 b = 1000
```

分析

考点：

* synchronized 关键字
* 并发下内存的可见性

synchhronized锁的是对象，可以发现我们使用的同一个对象，必然会产生访问带有synchronized 关键字方法的时候，必定会排斥

所以访问方法m2时，m1等待。线程启动需要时间，交由CPU执行，所以m2执行在它之前，并且锁住对象，所以m1会等待。

所以先打印 main b = 1000\|2000 这完全取决于，当前内存的值更新的时间间隔，有可能是1000，也有可能是2000，最后答应b = 1000





