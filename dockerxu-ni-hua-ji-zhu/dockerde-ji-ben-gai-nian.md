### Docker的基本概念

#### Docker架构

![](/assets/123e89ajdjak.png)

host是主机的载体，是docker安装的地方

继承类比：

Class  extends Class1    Object o = new Class2   -&gt; 此时o的对象结构中，有class1的成员结构

image2 extends image1   Container c = new image2 -&gt; 此时，c容器中，有image1的文件，但是重名文件会被覆盖掉（类似于子类实现父类的方法）

