## 事务

#### 在UsersService.java 新增batchAdd方法，并实现，然后故意产生异常

```java
boolean batchAdd();
```

实现

```java
@Override
public boolean batchAdd() {
    Users a = new Users();
    a.setUsername("exceptionA");
    a.setPasswd("A");
    usersMapper.insertSelective(a);
    int i = 1;
    i = i / 0;
    Users b = new Users();
    b.setUsername("exceptionB");
    b.setPasswd("B");
    usersMapper.insertSelective(b);
    return true;
}
```

#### 编写测试类，首先不启动事务

结果：

![](/assets/dhdf8991sss.png)

数据库出现了这条记录。所以尽管出现异常但是还没有回滚。

SpringBoot怎么添加事务呢？



