### 线程间的协作

采用轮训的方式，去检测一个线程是否执行完，难以保证及时性，以及系统资源开销大

java提供了**等待/通知**机制来实现线程间的协作，即`wait()/notify()|notifyAll()`

通常我们使用`wait()/notifyAll()`。`notify()`容易丢失信号，因为只会唤醒其中的一个线程。

等待/通知范式

**等待方：**

* 获取对象的锁
* 循环里判断条件是否满足，不满足调用wait方法
* 条件满足执行业务逻辑

**通知方：**

* 获取对象锁
* 改变条件
* 通知所有等待在对象的线程
* 注意 `wait`、`notify`和`notifyAll` 必须作用在有锁的方法和锁代码块上，不然会抛异常

**Express.java**

```java
/**
 * @Title:
 * @Author:Zachary
 * @Desc: 快递实体
 * @Date:2019/1/29
 **/
public class Express {
    public final static String CITY = "ShangHai";

    private int km;//公里数
    private String site;//千米数


    public Express(int km, String site) {
        this.km = km;
        this.site = site;
    }

    public int getKm() {
        return km;
    }

    public void setKm(int km) {
        this.km = km;
    }

    public String getSite() {
        return site;
    }

    public void setSite(String site) {
        this.site = site;
    }

    //改变公里数，通知等待线程进行判断
    public synchronized void changeKm() {
        km = 101;
        notifyAll();
    }

    //改变站点，通知等待线程进行判断
    public synchronized void changeSite() {
        site = "BeiJing";
        notifyAll();
    }


    public synchronized void waitKm() {
        while (km < 101) {
            try {
                wait();
                System.out.println(Thread.currentThread().getId() + " km thread  is notifed .");
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
        System.out.println(Thread.currentThread().getId() + " km   is  " + km + " i opt db.");
    }


    public synchronized void waitSite() {
        while (CITY.equals(this.site)) {
            try {
                wait();
                System.out.println(Thread.currentThread().getId() + " site thread  is notifed .");
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
        System.out.println(Thread.currentThread().getId() + " site   is  " + site + " i call user.");
    }

}
```

```java
/**
 * @Title:
 * @Author:Zachary
 * @Desc: 测试类
 * @Date:2019/1/29
 **/
public class UseWaitNotify {
    private static Express express = new Express(0, Express.CITY);


    static class KmThread extends Thread {
        @Override
        public void run() {
            express.waitKm();
        }
    }


    static class SiteThread extends Thread {
        @Override
        public void run() {
            express.waitSite();
        }
    }

    public static void main(String[] args) throws InterruptedException {
        for (int i = 0; i < 3; i++) {
            new KmThread().start();
        }
        for (int i = 0; i < 3; i++) {
            new SiteThread().start();
        }
        Thread.sleep(1000);
        express.changeKm();

        express.changeSite();
    }
}
```

```java
16 site thread  is notifed .
15 site thread  is notifed .
14 site thread  is notifed .
13 km thread  is notifed .
13 km   is  101 i opt db.
12 km thread  is notifed .
12 km   is  101 i opt db.
11 km thread  is notifed .
11 km   is  101 i opt db.
14 site thread  is notifed .
14 site   is  BeiJing i call user.
15 site thread  is notifed .
15 site   is  BeiJing i call user.
16 site thread  is notifed .
16 site   is  BeiJing i call user.
```

等待超时模型完成一个连接池

**ConnectionPool.java**

```java
import java.sql.Connection;
import java.util.LinkedList;

/**
 * @Title:
 * @Author:Zachary
 * @Desc:
 * @Date:2019/1/29
 **/
public class ConnectionPool {
    //声明一个变量存贮连接池
    private static LinkedList<Connection> pool = new LinkedList<>();


    public ConnectionPool(int capity) {
        capity = capity <= 0 ? 10 : capity;
        for (int i = 0; i < capity; i++) {
            pool.addLast(SqlConnectionImpl.fetchConnection());
        }
    }

    // 从连接池中拿链接
    public Connection fetchConn(long mills) throws InterruptedException {
        synchronized (pool) {
            // mills < 0 标识永不不超时
            if (mills < 0) {
                while (pool.isEmpty()) {
                    pool.wait();
                }
                return pool.removeFirst();
            } else {
                //计算超时时间
                long overtime = System.currentTimeMillis() + mills;
                //等待时间
                long remain = mills;
                while (pool.isEmpty() && remain > 0) {
                    //设置等待时间
                    pool.wait(remain);
                    //计算还剩余多少等待时间
                    remain = overtime - System.currentTimeMillis();
                }
                Connection result = null;
                if (!pool.isEmpty()) {
                    result = pool.removeFirst();
                }
                return result;
            }
        }
    }

    public void releaseConn(Connection connection) {
        if (connection != null) {
            synchronized (pool) {
                pool.addLast(connection);
                pool.notifyAll();
            }
        }
    }

}
```

**SqlConnectionImpl.java**

```java
import java.sql.*;
import java.util.Map;
import java.util.Properties;
import java.util.concurrent.Executor;

/**
 * 【注意】 模拟 createStatement 和 commit 操作耗时。Thread.sleep()
 */
public class SqlConnectionImpl implements Connection {

    public static final Connection fetchConnection() {
        return new SqlConnectionImpl();
    }

    @Override
    public Statement createStatement() throws SQLException {
        try {
            Thread.sleep(70);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        return null;
    }

    @Override
    public PreparedStatement prepareStatement(String sql) throws SQLException {
        return null;
    }

    @Override
    public CallableStatement prepareCall(String sql) throws SQLException {
        return null;
    }

    @Override
    public String nativeSQL(String sql) throws SQLException {
        return null;
    }

    @Override
    public void setAutoCommit(boolean autoCommit) throws SQLException {

    }

    @Override
    public boolean getAutoCommit() throws SQLException {
        return false;
    }

    @Override
    public void commit() throws SQLException {
        try {
            Thread.sleep(100);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }

    @Override
    public void rollback() throws SQLException {

    }

    @Override
    public void close() throws SQLException {

    }

    @Override
    public boolean isClosed() throws SQLException {
        return false;
    }

    @Override
    public DatabaseMetaData getMetaData() throws SQLException {
        return null;
    }

    @Override
    public void setReadOnly(boolean readOnly) throws SQLException {

    }

    @Override
    public boolean isReadOnly() throws SQLException {
        return false;
    }

    @Override
    public void setCatalog(String catalog) throws SQLException {

    }

    @Override
    public String getCatalog() throws SQLException {
        return null;
    }

    @Override
    public void setTransactionIsolation(int level) throws SQLException {

    }

    @Override
    public int getTransactionIsolation() throws SQLException {
        return 0;
    }

    @Override
    public SQLWarning getWarnings() throws SQLException {
        return null;
    }

    @Override
    public void clearWarnings() throws SQLException {

    }

    @Override
    public Statement createStatement(int resultSetType, int resultSetConcurrency) throws SQLException {
        return null;
    }

    @Override
    public PreparedStatement prepareStatement(String sql, int resultSetType, int resultSetConcurrency) throws SQLException {
        return null;
    }

    @Override
    public CallableStatement prepareCall(String sql, int resultSetType, int resultSetConcurrency) throws SQLException {
        return null;
    }

    @Override
    public Map<String, Class<?>> getTypeMap() throws SQLException {
        return null;
    }

    @Override
    public void setTypeMap(Map<String, Class<?>> map) throws SQLException {

    }

    @Override
    public void setHoldability(int holdability) throws SQLException {

    }

    @Override
    public int getHoldability() throws SQLException {
        return 0;
    }

    @Override
    public Savepoint setSavepoint() throws SQLException {
        return null;
    }

    @Override
    public Savepoint setSavepoint(String name) throws SQLException {
        return null;
    }

    @Override
    public void rollback(Savepoint savepoint) throws SQLException {

    }

    @Override
    public void releaseSavepoint(Savepoint savepoint) throws SQLException {

    }

    @Override
    public Statement createStatement(int resultSetType, int resultSetConcurrency, int resultSetHoldability) throws SQLException {
        return null;
    }

    @Override
    public PreparedStatement prepareStatement(String sql, int resultSetType, int resultSetConcurrency, int resultSetHoldability) throws SQLException {
        return null;
    }

    @Override
    public CallableStatement prepareCall(String sql, int resultSetType, int resultSetConcurrency, int resultSetHoldability) throws SQLException {
        return null;
    }

    @Override
    public PreparedStatement prepareStatement(String sql, int autoGeneratedKeys) throws SQLException {
        return null;
    }

    @Override
    public PreparedStatement prepareStatement(String sql, int[] columnIndexes) throws SQLException {
        return null;
    }

    @Override
    public PreparedStatement prepareStatement(String sql, String[] columnNames) throws SQLException {
        return null;
    }

    @Override
    public Clob createClob() throws SQLException {
        return null;
    }

    @Override
    public Blob createBlob() throws SQLException {
        return null;
    }

    @Override
    public NClob createNClob() throws SQLException {
        return null;
    }

    @Override
    public SQLXML createSQLXML() throws SQLException {
        return null;
    }

    @Override
    public boolean isValid(int timeout) throws SQLException {
        return false;
    }

    @Override
    public void setClientInfo(String name, String value) throws SQLClientInfoException {

    }

    @Override
    public void setClientInfo(Properties properties) throws SQLClientInfoException {

    }

    @Override
    public String getClientInfo(String name) throws SQLException {
        return null;
    }

    @Override
    public Properties getClientInfo() throws SQLException {
        return null;
    }

    @Override
    public Array createArrayOf(String typeName, Object[] elements) throws SQLException {
        return null;
    }

    @Override
    public Struct createStruct(String typeName, Object[] attributes) throws SQLException {
        return null;
    }

    @Override
    public void setSchema(String schema) throws SQLException {

    }

    @Override
    public String getSchema() throws SQLException {
        return null;
    }

    @Override
    public void abort(Executor executor) throws SQLException {

    }

    @Override
    public void setNetworkTimeout(Executor executor, int milliseconds) throws SQLException {

    }

    @Override
    public int getNetworkTimeout() throws SQLException {
        return 0;
    }

    @Override
    public <T> T unwrap(Class<T> iface) throws SQLException {
        return null;
    }

    @Override
    public boolean isWrapperFor(Class<?> iface) throws SQLException {
        return false;
    }
}
ConnectionPoolClient.java
import java.sql.Connection;
import java.util.concurrent.CountDownLatch;
import java.util.concurrent.atomic.AtomicInteger;

/**
 * @Title:
 * @Author:Zachary
 * @Desc:
 * @Date:2019/1/29
 **/
public class ConnectionPoolClient {
    //定义一个全局的连接池
    public static final ConnectionPool connectionPool = new ConnectionPool(10);

    //所有线程执行完毕后，会被停留在 end.await() 才会执行下去
    public static CountDownLatch end;


    public static void main(String[] args) throws InterruptedException {
        int threadNum = 50;
        int count = 20;
        end = new CountDownLatch(threadNum);
        //计数器 原子操作，线程安全
        AtomicInteger get, noGet;
        get = new AtomicInteger();
        noGet = new AtomicInteger();
        for (int i = 0; i < threadNum; i++) {
            new Thread(new Worker(get, noGet, count), " Woker_" + i).start();
        }
        end.await();//计数器减到0 放行
        System.out.println(" total thread number is \t" + threadNum * count);
        System.out.println(" get connection number is \t" + get.intValue());
        System.out.println(" noGet connection number is \t" + noGet.intValue());
    }


    static class Worker implements Runnable {
        private int count;//执行次数
        private AtomicInteger get;
        private AtomicInteger noGet;

        public Worker(
                AtomicInteger get,
                AtomicInteger noGet,
                int count) {
            this.count = count;
            this.get = get;
            this.noGet = noGet;
        }


        @Override
        public void run() {
            while (count > 0) {

                try {
                    //从连接池中获取链接
                    Connection connection = connectionPool.fetchConn(1000);
                    if (connection != null) {
                        //链接不为空，进行数据库操作
                        try {
                            connection.createStatement();
                            connection.commit();
                        } finally {
                            //记得将链接放回连接池
                            connectionPool.releaseConn(connection);
                            //计数器加1
                            get.incrementAndGet();
                        }
                    } else {
                        //没有拿到链接，没有拿到链接计数器加1
                        noGet.incrementAndGet();
                        System.out.println(Thread.currentThread().getName() + " \t等待超时");
                    }
                } catch (Exception e) {
                } finally {
                    //循环减1
                    count--;
                }
            }
            end.countDown();//完成一个线程，内部计数器减1
        }
    }

}
Join 使用
在A线程中使用B线程的join方法，则A线程会等到B线程执行完了才会执行。
public class UseJoin {


    static class A extends Thread {
        private B b;

        public A(B b) {
            this.b = b;
        }

        @Override
        public void run() {
            System.out.println(" A thread run ");
            System.out.println(" B thread use join method start ");
            try {
                b.start();
                b.join();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println(" A wait B exec over end ");
        }
    }

    static class B extends Thread {
        @Override
        public void run() {
            System.out.println(" B thread run ");
        }
    }

    public static void main(String[] args) throws InterruptedException {
        B b = new B();
        A a = new A(b);
        a.start();
    }
}
```

调用`yield() `、`sleep()`、`wait()`、`notify()`等方法对锁有何影响？

* 线程在执行yield\(\)以后，持有的锁是不会释放的
* sleep\(\)方法被调用以后，持有的锁是不会释放的
* 调动方法之前，必须要持有锁，调用了wait\(\)方法以后，锁会被释放，当wait方法返回的时候，线程回重新持有锁
* 调动方法之前，不需持有锁，调用notify\(\)方法本身不会释放锁的



