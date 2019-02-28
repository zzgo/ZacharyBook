> ~~~~~

这是一位**同学面试阿里**之后的评价，大家可以看看呢，了解一下面试阿里需要掌握的那些知识，大家加油鸭！

缺点：

1. 代码能力不够，没有怎么写过业务代码，对边界没什么考虑，对异常情况没有考虑，对系统稳定性没有考虑，对基础的jdk方法没有本质理解。[http://alibole.alibaba-inc.com/static/alibole-manage/src/p/detail/index.html?codeId=31330](http://alibole.alibaba-inc.com/static/alibole-manage/src/p/detail/index.html?codeId=31330)

2. 对分布式架构没有多少了解，仅知道一个zookeeper来做服务注册和发现

3. 做缓存但是不知道软引用、弱引用、虚引用

4. jvm参数知道一些，但是不知道老jdk中的perm区和java 8的meta space的区别

5. 线上问题排查有一定了解，jvm命令和linux命令了解还不错：sar、top、free、iostat、jstack、jmap

6. 如何实现自定义spring注解：不知道

7. http协议格式了解的不太清晰

优点：

1. 对限流策略有一定的了解，包括：时间分段队列、漏斗、令牌桶。

> ~~~~

### 为什么要有hashcode方法？

哈希表这个数据结构想必大家都不陌生吧，而且很多地方会利用到hash表来提高查找效率。在Java的Objec类种有一个方法

```java
public native int hashCode();
```

根据这个方法可知，该方法返回int类型的数值，并且是本地方法，因此在Object类没有给出具体的实现

为什么Object类型需要这样一个方法呢？他有什么作用？今天我们就来具体探讨下hashCode的方法。

#### hashCode方法的作用

对于包含容器类型的程序设计语言来说，基本上都会设计到hashCode，在java中也一样，hashCode方法的主要作用是为了配合基于散列的集合一起正常运行，这样的散列集合包括HashSet，HashMap以及HashTable。

为什么这么说呢？考虑到一种情况，当向集合插入对象时，如何判别在集合中是否已经存在该对象了？（集合中不允许重复的元素存在）

也许大多数人都会想到调用equals方法来逐个进行比较，这个方法确实可行，但是如果集合中已经存在一万条数据或者更多的数据呢，如果采用Equals方法逐一进行比较，效率必然是一个问题，此时hashCode方法的作用就体现出来了，当集合要添加新的对象时，先调用这个对象的hashCode值，得到相应的hashcode值，实际上在HashMap的具体实现中会用到一个table保存已经存进去的对象的hashcode值，如果table中没有该hashcode值，它就可以直接村存进去，不用在进行任何比较，如果存在该hashcode值，就调用它的equals方法与新元素进行比较，相同的话就不存了，不相同就散列其他地址，所以这里存在一个冲突解决的问题，这样一来实际调用equals方法的次数就大大降低了，说通俗一点，java中的hashCode方法就是根据已定的规则将对象相关的信息（比如对象存储地址，对象的字段等）映射成一个数值，这个数值称作为散列值.

HashMap使用hashCode方法大大的减少了equals方法的调用次数，从而提高程序效率

#### 注意

hashCode放回的就是对象的存储地址，事实上这种看法是不全面的，确实有些JVM在现实是直接返回对象存储地址，但是大多数时候不是这样的，只能说可能与存储地址有一定关联

hashCode与equals的联系与区别

直接根据hashcode判断两个对象是否相等？是不可采取的，因为不同的对象可能会生成相同的hashcode值，虽然不能根据hashcode判断两个对象是否相等但是可以根据hashcode判断两个对象不等

两个对象，调用equals结果为true，则两个对象的hashcode值必定相等

如果equals得到结果为false，则两个对象的hashcode值不一定不同

如果两个对象的hashcode值不等，则equals方法得到的结果必定为false

如果两个对象的hashcode值相等，则equals方法得到的结果未知

#### 重写

在有些情况下，程序设计者在设计一个类的时候需要重写equals方法，比如String类，但是千万注意，在重写equals方法的同时，必须重写hashCode方法

```java
public class Person {
    private String name;
    private int age;

    public Person() {
    }

    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;

        Person person = (Person) o;

        if (age != person.age) return false;
        return name != null ? name.equals(person.name) : person.name == null;
    }

    public static void main(String[] args) {
        Person person = new Person("admin", 20);
        Person person2 = new Person("admin", 20);
        System.out.println(person.equals(person2));
        Map<Person, Integer> map = new HashMap<>();
        map.put(person, 1);
        System.out.println(map.get(person2));
    }
}
```

执行结果

```java
true
null
```

在这里我重写了equals方法，也就是说两个People对象，如果姓名和年龄相等，则认为是同一个人

这段代码原意是输出1，但是实际上是输出null，这是由于重写equals方法的同时，忘记重写hashCode方法

虽然重写了equals方法是的逻辑上姓名和年龄相同的两个对象判定为相等对象，但是要知道默认情况下，hashCode方法是将对象的存储地址进行映射，那么输出null就在不足为奇了，由于person和person虽然在逻辑的equals上相等吗，但是他们指向的地址值不相等。

如果要输出1，则需要同时重写hashCode和equals方法，始终在逻辑上保持一致

#### Effective Java一书

* 程序执行期间，只要equals方法的比较操作用到的信息没有被修改，那么对同一个对象调用多次，hashCode方法必须始终如一的返回同一个整数
* 如果两个对象根据equals方法比较是相等的，那么调用两个对象的hashCode方法必须返回相同的整数
* 如果两个对象根据equals方法比较是不等的，则hashCode方法不一定返回不同的整数

**针对第一点，我们需要注意：**

设计hashCode\(\)时最重要的因素就是：无论何时，对同一个对象调用hashCode\(\)都应该产生同样的值，如果在讲一个对象用put添加进HashMap时产生一个hashCode值，而用get\(\)取出时却产生了另一个hashCode值，那么就无法获取到该对象了

所以如果你的hashCode方法依赖于对象中易变的数据，因为在数据发生变化时，hashCode方法就会生成一个不同的散列码

```java
public class Person {
    private String name;
    private int age;

    public Person() {
    }

    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;

        Person person = (Person) o;

        if (age != person.age) return false;
        return name != null ? name.equals(person.name) : person.name == null;
    }

    @Override
    public int hashCode() {
        int result = name != null ? name.hashCode() : 0;
        result = 31 * result + age;
        return result;
    }

    public static void main(String[] args) {
        Person person = new Person("admin", 20);
        Map<Person, Integer> map = new HashMap<>();
        map.put(person, 1);
        person.setAge(10);//易变数据
        System.out.println(map.get(person));
    }
}
```

执行结果

```
null
```



### jvm的线程和操作系统的线程有什么区别？



