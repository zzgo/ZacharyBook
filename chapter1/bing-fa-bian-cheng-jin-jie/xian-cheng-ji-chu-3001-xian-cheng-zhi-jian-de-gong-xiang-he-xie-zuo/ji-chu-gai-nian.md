CPU核心数与线程数目

> 一般来说是 1:1
>
> 使用超线程后，变成了1:2

CPU时间片轮转机制

> CPU调度到线程上的一种算法，称作RR算调度，会导致上下文切换，是一个耗时操作。

什么是线程和进程

> 进程程：是CPU分配资源的最小单位，是一次程序的顺序执行，进程内部有多个线程，会共享这个线程的资源
>
> 线程：是CPU调度的最小单位，必须依赖进程存在

并行和并发

> 并行：同一时间内，可以处理事情的能力
>
> 并发：与单位时间相关，在单位时间内处理事情的能力

高并发编程的意义、好处和注意事项

> 好处：
>
> 充分利用CPU的资源
>
> 加快用户响应的时间
>
> 程序模块化，异步化
>
> 问题：
>
> 线程共享资源，存在冲突
>
> 容易导致死锁
>
> 启用太多的线程，就有搞垮机器的可能

线程启动的方式

> 三种

UseThread.java

```
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

UseRunnable.java

```
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

UseCallable.java

```
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

> stop\(\)，resume\(\),suspend\(\)已不建议使用
>
> stop\(\)会导致线程不会正确释放资源
>
> suspend\(\)容易导致死锁。

理解

> java的线程是协作式，并非抢占式
>
> thread.interrupt\(\) //调用线程的interrupt\(\)方法，不是强行去关闭这个线程，只是跟该线程打个招呼，
>
> 将线程的中断标志位置为true，线程是否中断，由线程本身决定
>
> 提供了两个方法去获取该标志位信息
>
> isInterrupted\(\)   判断当前线程是否处于中断状态
>
> static 方法的 interrupted\(\)  判断当前线程是否处于中断状态，并且重置该中断标志位的值false
>
> 根据我们的逻辑去结束该线程
>
> while\(isInterrupted\(\)\){
>
> //逻辑
>
> }
>
> 结束



> 注意一个问题：
>
> 如果在run里面跑出了InterruptException异常，这时候线程的中断标志位会被重置为false
>
> 如果还需要中断该线程的话，需要手动的调用interrupt\(\)方法



