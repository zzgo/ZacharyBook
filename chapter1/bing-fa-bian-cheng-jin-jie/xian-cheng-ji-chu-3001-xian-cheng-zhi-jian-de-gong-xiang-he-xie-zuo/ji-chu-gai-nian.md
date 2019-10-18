CPU核心数与线程数目

* 一般来说是 1:1
* 使用超线程后，变成了1:2

CPU时间片轮转机制

* CPU调度到线程上的一种算法，称作RR算调度，会导致上下文切换，是一个耗时操作。

什么是线程和进程

* 进程：是CPU**分配资源**的最小单位，是一次程序的顺序执行，进程内部有多个线程，会共享这个线程的资源
* 线程：是CPU**调度**的最小单位，必须**依赖进程**存在

并行和并发

* 并行：同一时间内，可以处理事情的能力
* 并发：与单位时间相关，在单位时间内处理事情的能力

高并发编程的意义、好处和注意事项

好处：

* 充分利用CPU的资源
* 加快用户响应的时间
* 程序模块化，异步化

问题：

* 线程共享资源，存在冲突
* 容易导致死锁
* 启用太多的线程，就有搞垮机器的可能

线程启动的方式

* 三种

**UseThread.java**

```java
public class UseThread extends Thread {
    @Override
    public void run() {
        System.out.println("use extends Thread create Thread ");
    }

    public static void main(String[] args) {
        // 最简单的一种方式
        new UseThread().start();
    }
}
```

**UseRunnable.java**

```java
public class UseRunnable implements Runnable {
    @Override
    public void run() {
        System.out.println("use implements Runnable create Thread ");
    }

    public static void main(String[] args) {
        //Runnable 需要放在Thread里面启动一个线程
        new Thread(new UseRunnable()).start();
    }
}
```

**UseCallable.java**

```java
public class UseCallable implements Callable<String> {
    @Override
    public String call() throws Exception {
        System.out.println("use implements Callable create Thread");
        return "abc";
    }

    public static void main(String[] args) throws ExecutionException, InterruptedException {
        //Callable 需要结合FutureTask<V> 使用，在Thread中能接受FutureTask<V> Class 不能接受Callable<V> 的Class
        UseCallable callable = new UseCallable();
        FutureTask<String> futureTask = new FutureTask<String>(callable);
        new Thread(futureTask).start();
        System.out.println(futureTask.get());
    }
}
```

线程中断

知道

* stop\(\)，resume\(\),suspend\(\)已不建议使用
* stop\(\)会导致线程不会正确释放资源
* suspend\(\)容易导致死锁。

理解

```
java的线程是协作式，并非抢占式
thread.interrupt() //调用线程的interrupt()方法，不是强行去关闭这个线程，只是跟该线程打个招呼，
将线程的中断标志位置为true，线程是否中断，由线程本身决定
提供了两个方法去获取该标志位信息
isInterrupted()   判断当前线程是否处于中断状态
static 方法的 interrupted()  判断当前线程是否处于中断状态，并且重置该中断标志位的值false
根据我们的逻辑去结束该线程
while(isInterrupted()){
//逻辑
}
结束
注意一个问题：
如果在run里面跑出了InterruptException异常，这时候线程的中断标志位会被重置为false
如果还需要中断该线程的话，需要手动的调用interrupt()方法
```

**MyInterruptInThread.java**

```java
public class MyInterruptInThread extends Thread {
    @Override
    public void run() {
        //判断是否被打断了
        while (!isInterrupted()) {
            //逻辑
            System.out.println(" thread is run and flag is " + isInterrupted());
        }
        System.out.println(" thread is interrupt and flag is " + isInterrupted());
    }

    public static void main(String[] args) throws InterruptedException {
        MyInterruptInThread inThread = new MyInterruptInThread();
        inThread.start();
        Thread.sleep(500);
        inThread.interrupt();
    }
}
```

**MyInterruptInRunnable.java**

```java
public class MyInterruptInRunnable implements Runnable {
    @Override
    public void run() {
        Thread thread = Thread.currentThread();
        while (!thread.isInterrupted()) {
            //逻辑
            System.out.println(" thread is run and flag is " + thread.isInterrupted());
        }
        System.out.println(" thread is interrupt and flag is " + thread.isInterrupted());
    }

    public static void main(String[] args) throws InterruptedException {
        MyInterruptInRunnable runnable = new MyInterruptInRunnable();
        Thread thread = new Thread(runnable);
        thread.start();
        Thread.sleep(100);
        thread.interrupt();
    }
}
```

**MyInterruptExistInterruptException.java**

```java
public class MyInterruptExistInterruptException extends Thread {
    @Override
    public void run() {
        while (!isInterrupted()) {
            try {
                System.out.println(" thread is run and flag is " + isInterrupted());
                Thread.sleep(50);
            } catch (InterruptedException e) {
                System.out.println(" thread throw interrupt and flag is " + isInterrupted());
                interrupt();
                System.out.println(" thread throw interrupt ，handle use interrupt and  flag is " + isInterrupted());
                e.printStackTrace();
            }
        }
        System.out.println(" thread is interrupt and flag is " + isInterrupted());
    }

    public static void main(String[] args) throws InterruptedException {
        MyInterruptExistInterruptException exception = new MyInterruptExistInterruptException();
        exception.start();
        Thread.sleep(100);
        exception.interrupt();
    }
}
```

```java
thread is run and flag is false
thread is run and flag is false
java.lang.InterruptedException: sleep interrupted
at java.lang.Thread.sleep(Native Method)
at com.zachary.ch1.interrupt.MyInterruptExistInterruptException.run(MyInterruptExistInterruptException.java:15)
thread throw interrupt and flag is false
thread throw interrupt ，handle use interrupt and flag is true
thread is interrupt and flag is true
```

线程的状态

![](/assets/skak73287.png)

* 新建：通过new 关键字新建一个线程，该线程不会运行，只是new 了一个对象对象出来
* 就绪：当线程调用start\(\)方法时，处于就绪状态，但仍然不能够运行
* 运行：只有当cpu调度到了该线程后，该线程处于可运行状态
* 阻塞：使用sleep\(\) wait\(\)
* 死亡，销毁：执行完后run，stop，setDeamon\(true\) 随着主线程一起销毁

深入理解run和start

* 直接new Thread\(\).run\(\) 本质就是一个对象调用了自己的方法，是在主线程中执行的，不会创建线程去执行
* new Thread\(\).start\(\) 才会去创建一个线程，由cpu决定什么执行run方法

yield方法

* 线程从运行到可运行状态
* 使用了yield方法后，该线程让出cpu使用权，并且参与下一次争夺cpu使用，有可能会再次获取到cpu使用权

线程优先级

* 通过thread.setPriority\(\) 设置优先级，从1到10，默认是5

注意：

* 不要假定高优先级的线程一定先于低优先级的线程执行，不要有逻辑依赖于线程优先级，否则可能产生意外结果

守护线程

* 守护线程依赖于主线程，主线程结束后，守护线程就死亡了
* 通过thread.setDeamon\(true\) 设置问主线程
* 注意守护线程里面的 finally{ } 块 不一定能执行

**MyDeamon.java**

```java
public class MyDeamon extends Thread {
    @Override
    public void run() {
        try {

        } finally {
            System.out.println(" deamon thread exec finally ?");
        }
    }

    public static void main(String[] args) throws InterruptedException {
        MyDeamon deamon = new MyDeamon();
        deamon.setDaemon(true);
        deamon.start();
        //注释与注释区别
        // 如果主线程执行的很快，守护线程都还进入运行状态，就直接死亡了
        Thread.sleep(100);
    }
}
```



