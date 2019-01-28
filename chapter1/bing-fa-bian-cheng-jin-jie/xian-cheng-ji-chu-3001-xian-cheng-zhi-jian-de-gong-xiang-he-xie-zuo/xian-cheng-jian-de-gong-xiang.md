初步阶段

synchronized 关键字，重量级的锁

> Java 提供的内置锁，使用在方法上，或者同步块上，最大的作用是确保在多个线程在同一时刻只能有一个线程处于方法或者同步块中，这样它就确保了线程对变量访问的可见性和排差性，锁的是对象，不是代码块。
>
> 深一点理解，每个对象在内存的对象头上有一个标志位，标志位上有1-2个字节标志它为一个锁，synchronized的作用就是当所有线程去抢这个对象的标志位时，谁把这个标志位指向了自己，那么就认为这个线程抢到了这个锁

对象锁和类锁

> java的对象锁和类锁在锁概念上基本上和内置锁一致的，但是，两个锁实际是有很大的区别的，对象锁是用于对象实例方法，或者一个对象实例上的，类锁是用于类的静态方法或者一个类的class对象上的，我们知道，类的对象实例可以是很多个，但是每个类只有一个class对象，所以不同的对象实例的对象锁是互不干扰，但是每个类的只有一个类锁，但是有一点必须注意的是，其实类锁只是一个概念上的东西，并不是真实存在的，它只是用来帮助我们理解锁定实例方法和静态方法的区别的。

代码演示

```
public class MyInnerLock {
    static class ObjectLock extends Thread {
        private MyInnerLock lock;

        public ObjectLock(MyInnerLock lock) {
            this.lock = lock;
        }

        @Override
        public void run() {
            try {
                lock.objF();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }

    static class ClassLock extends Thread {
        @Override
        public void run() {
            try {
                classF();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }

    public synchronized void objF() throws InterruptedException {
        //加上睡眠使我们看的更清楚
        Thread.sleep(1000);
        System.out.println(" object lock 1");
        //加上睡眠使我们看的更清楚
        Thread.sleep(3000);
        System.out.println(" object lock 2");
    }


    public synchronized static void classF() throws InterruptedException {
        //加上睡眠使我们看的更清楚
        Thread.sleep(1000);
        System.out.println(" class lock 1");
        //加上睡眠使我们看的更清楚
        Thread.sleep(3000);
        System.out.println(" class lock 2");
    }

    public static void main(String[] args) {
        //演示对象锁，不同的对象之间，拥有不同的锁
//        MyInnerLock lock1 = new MyInnerLock();
//        MyInnerLock lock2 = new MyInnerLock();
//        ObjectLock objectLock1 = new ObjectLock(lock1);
//        ObjectLock objectLock2 = new ObjectLock(lock2);
//        objectLock1.start();
//        objectLock2.start();
        //同一对象就是同一个锁
//        MyInnerLock lock = new MyInnerLock();
//        ObjectLock objectLock1 = new ObjectLock(lock);
//        ObjectLock objectLock2 = new ObjectLock(lock);
//        objectLock1.start();
//        objectLock2.start();

        //类锁
//        MyInnerLock lock = new MyInnerLock();
//        ClassLock classLock1 = new ClassLock();
//        ClassLock classLock2 = new ClassLock();
//        classLock1.start();
//        classLock2.start();
    }
}
```

volilate关键字，轻量级的锁



