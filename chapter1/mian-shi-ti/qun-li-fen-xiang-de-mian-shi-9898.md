问你们个问题 redis 搭建分布式集群A B C，B节点挂了，他要通过选举后才能把B节点的key值分配到其他槽 ，在这个过程中有大量的请求进来，如何预防缓存雪崩的问题？？

已知类名Person，请在控制台打印该类的所有属性名和方法名？

某业务模块统一采用“/app”为前缀，现在需要对该业务的所有异常入库记录，请给出你的技术解决方案

应用中，某个页面加载很慢，你会怎么处理

不同应用之间的交互设计，你会采取哪些方式，每种方式有哪些需要注意的点？

应用运行后，服务器CPU持续100%，你会怎么处理

请你用三句话，展示你的工作方式，工作态度，职业发展方向

140克盐，一个7克砝码，一个2克砝码，如何用两次砝码，秤出50克 和90克盐！

答：第一步用2克和7克的盐秤出9克的盐，用9克的盐去秤出90克的盐，砝码只用了两次。其余的使用盐去秤

java对象在虚拟机中的大小可以通过sizeof得到吗？

不可以

Anonymous Inner Class\(匿名内部类\)是否可以继承extends其他类？

可以

Anonymous Inner Class\(匿名内部类\)是否可以implements（实现）interface接口？

可以

数据库优化问答：

Customer表

```java
CREATE TABLE 'customer'(
    custid int(10) NOT NULL,
    custname varchar(100) NOT NULL,
    date datetime default NULL,
    money int(10) default NULL,
    PRIMARY KEY('custid'),
    KEY 'index_cutomer_custname' ('custname'),
    KEY 'index_customer_custname_union' ('money','date','custname')
)
```

secondinfo表定义如下

```java
CREATE TABLE secondinfo(
    secid int(10) NOT NULL,
    firstid int(10) NOT NULL,
    custid int(10) default NULL,
    PRIMARY KEY ('secid'),
    KEY 'Index_secondinfo_custid'('custid')
)
```

下面的SQL执行速度比较慢，请分析原因并做优化

1）SELECT \* FROM customer WHERE substring\(custname,1,6\)='beijin'

2）SELECT \* FROM customer WHERE money/30 &lt; 1000

3）SELECT \* FROM customer WHERE custname=3721

4）SELECT \* FROM customer WHERE custname &lt;&gt; 'like'

5） SELECT \* FROM secondinfo s WHERE s.custid NOT IN\(SELECT c.custid FROM customer c\)

6）

select \* from customer where money&lt;1000

union

select \* from customer where date &gt; '20180101'

7）select \* from customer where money &gt; 1000

