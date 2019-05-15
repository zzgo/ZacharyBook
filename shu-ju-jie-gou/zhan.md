## 栈（后进先出表）

出的一头是栈顶，另一端为栈底。

进栈，新元素放在栈顶；出栈，退栈，将栈顶元素进行删除，使之与其相邻的元素成为栈顶元素

![](/assets/skaki91919sjask.png)

### 栈的实现

顺序表，数组实现，用一个标志位标识栈顶的位置

![](/assets/19328jakjdka.png)

**MyStack.java**

```
/**
 * @Title:使用顺序表值数组实现栈
 * @Author:Zachary
 * @Desc:
 * @Date:2019/5/14
 **/
public class MyStack<T> {
    T[] datas;
    int size;
    static final int DEFAULT_SIZE = 10;
    static final double LOAD_FACTORY = 0.8d;

    public MyStack() {
        this.size = 0;
        ensureCapacity(DEFAULT_SIZE);
    }


    //push
    public void push(T data) {
        if (datas.length == size()) {
            ensureCapacity(size() * 2);
        }
        datas[size++] = data;
    }

    //pop
    public T pop() {
        if (isEmpty()) {
            throw new EmptyStackException();
        }
        T data = datas[size - 1];
        datas[--size] = null;
        return data;
    }

    //peek
    public T peek() {
        if (isEmpty()) {
            return null;
        }
        return datas[size];
    }

    private void ensureCapacity(int capacity) {
        if (capacity < size) {
            return;
        }
        //System.out.println("capacity: " + capacity + ",size: " + size);
        T[] oldDatas = datas;
        datas = (T[]) new Object[capacity];
        for (int i = 0; i < size(); i++) {
            datas[i] = oldDatas[i];
        }
    }

    public int size() {
        return size;
    }

    public boolean isEmpty() {
        return size == 0;
    }

    public static void main(String[] args) {
        MyStack<Integer> myStack = new MyStack<>();
        for (int i = 0; i < 100; i++) {
            myStack.push(i);
        }
        for (int i = 0; i < 10; i++) {
            System.out.println(myStack.pop());
        }
    }
}
```

顺序表，链表实现

```
import java.util.EmptyStackException;

/**
 * @Title:利用链表实现栈
 * @Author:Zachary
 * @Desc:
 * @Date:2019/5/15
 **/
public class MyLinkedStack<E> {

    int size;
    //根节点
    Node top;

    public MyLinkedStack() {
        this.size = 0;
    }
    // push

    public void push(E data) {
        top = new Node(data, top);
        size++;
    }

    // pop
    public E pop() {
        if (isEmpty()) {
            throw new EmptyStackException();
        }
        E data = top.data;
        Node cur = top;
        top = top.next;
        cur.next = null;//gc
        size--;
        return cur.data;
    }


    public E peek() {
        if (isEmpty()) {
            throw new EmptyStackException();
        }
        return top.data;
    }

    public boolean isEmpty() {
        return size == 0;
    }

    public int size() {
        return size;
    }

    //头结点
    class Node {
        Node next;
        E data;

        public Node(E data, Node next) {
            this.next = next;
            this.data = data;
        }

        public Node getNext() {
            return next;
        }

        public void setNext(Node next) {
            this.next = next;
        }

        public E getData() {
            return data;
        }

        public void setData(E data) {
            this.data = data;
        }
    }

    public static void main(String[] args) {
        MyLinkedStack<Object> objectMyLinkedStack = new MyLinkedStack<>();
        objectMyLinkedStack.push(1);
        objectMyLinkedStack.push(2);
        objectMyLinkedStack.push(3);
        objectMyLinkedStack.push(4);
        System.out.println(objectMyLinkedStack.pop());
        System.out.println(objectMyLinkedStack.peek());
        System.out.println(objectMyLinkedStack.pop());
    }

}
```

### 面试题

**Java中的Stack是通过Vector来实现的，这种设计被认为是不良的设计，说说你的看法？  
**

栈的特性，后进先出表。因为Vector 里面有一个add方法，严格的打破了栈的定义。所以从栈的特性来看是一个不良的设计。

只是用到了其中的  
，addElement\(item\);  
elementAt\(len - 1\);  
removeElementAt\(len - 1\);

### **利用栈解决+-\*/\(\)计算问题，逆波兰问题**

Calc.java

```
/**
 * @Title:利用栈实现加减剩除运算。
 * @Author:Zachary
 * @Desc:计算器类
 * @Date:2019/5/14
 **/
public class Calc {

    /**
     * 核心计算类
     *
     * @param express
     * @return 结果
     */
    public int calc(String express) {
        //定义操作数的栈
        Stack<Integer> dataStack = new Stack<>();
        //定义操作符的一个栈
        Stack<Character> charStack = new Stack<>();
        express = express.trim().replaceAll(" ", "");
        char[] chars = express.toCharArray();
        boolean preNum = false;
        for (int i = 0; i < chars.length; i++) {
            //遇到数字
            if (chars[i] >= '0' && chars[i] <= '9') {
                if (preNum) {
                    //输入的是 01 - 02 这种不符的字符。
                    Integer num = dataStack.pop();
                    dataStack.push(num * 10 + Integer.parseInt(chars[i] + ""));
                } else {
                    dataStack.push(Integer.parseInt(chars[i] + ""));
                }
                preNum = true;
                continue;
            } else if (chars[i] == '(') {
                //遇到'(' 直接放在操作符栈顶
                charStack.push(chars[i]);
            } else if (chars[i] == ')') {
                //遇到')'

                while (!charStack.isEmpty() && (charStack.peek() != '(')) {
                    //进行计算
                    calc(dataStack, charStack);
                }
                charStack.pop();
            } else {
                //遇到符号，判断优先级
                while (!charStack.isEmpty() && priority(charStack.peek(), chars[i])) {
                    calc(dataStack, charStack);
                }
                charStack.push(chars[i]);
            }
            preNum = false;
        }

        while (!charStack.isEmpty()) {
            calc(dataStack, charStack);
        }

        return dataStack.peek();
    }

    private boolean priority(Character target, char source) {
        //说明下 +是43 -是35 *是42 /是47
        // 将+-弄成一样的码值，*/一样的码值
        if (target == '*') {
            target = '*' + 5;
        }
        if (source == '*') {
            source = '*' + 5;
        }
        if (target == '+') {
            target = '+' + 2;
        }
        if (source == '+') {
            source = '+' + 2;
        }
        //比较大小知道优先级
        if (source > target) {
            return false;
        } else {
            return true;
        }
    }

    private void calc(Stack<Integer> dataStack, Stack<Character> charStack) {
        Character cs = charStack.pop();
        Integer ds02 = dataStack.pop();
        Integer ds01 = dataStack.pop();
        Integer num = 0;
        switch (cs) {
            case '+':
                num = ds01 + ds02;
                break;
            case '-':
                num = ds01 - ds02;
                break;
            case '*':
                num = ds01 * ds02;
                break;
            case '/':
                num = ds01 / ds02;
                break;
            default:
                ;
        }
        dataStack.push(num);
    }

    public static void main(String[] args) {
        Calc calc = new Calc();
        int num = calc.calc("(1+2)-3+3*4-10/2");
        System.out.println(num);
        System.out.println(calc.calc("3-4+5*6+100/10/2+(2-1)*10"));
        System.out.println(calc.calc("100*10/20-10*10+6+9-40-80+(10-5)*10/2+(1+(2-(5+5)*2))+10-2"));
        System.out.println(calc.calc("(1+(2-(5+5)*2))"));
    }

}
```

Stack.java

```
public class Stack<T> {
    //节点类  
    public class Node{  
        public T data;  
        public Node next;  
        public Node(){}  
        public Node(T data,Node next){  
            this.data = data;  
            this.next = next;  
        }  
    }//Node  

    public Node top = new Node();  
    public int size;  
    public Node oldNode;  

    //入栈  
    public void push(T element){  
        top = new Node(element,top);  
        size++;  
    }  

    //出栈  
    public T pop(){  
        oldNode = top;  
        top = top.next;  
        //oldNode = null;  
        size--;  
        return oldNode.data;  
    }  

    //返回栈顶对象的数据域，但不出栈  
    public T peek(){  
        return top.data;  
    }  

    //栈长  
    public int length(){  
        return size;  
    }  

    //判断栈是否为空  
    public boolean isEmpty(){  
        return size == 0;  
    }  

}
```

### JVM里面的栈

StackOverflowError ：栈溢出

OutOfMemoryError ：堆溢出

栈和堆的区别（不是太正规说法）

栈解决了程序如何运行问题，即程序如何执行，如何处理数据的问题

堆解决的是数据存储的问题，即数据怎么样，放在那里

栈负责运行逻辑

堆负责的是存储逻辑

### JVM内存栈

**Stack与heap都运行在内存上，在内存空间上字节码指令不必担心不同机器上的区别，所以JVM实现了与平台无关的特性。**

**JVM栈的每个栈帧（slot）大小都是4bytes，而一个slot恰好可以保持一个对象引用，所以引用永远是4bytes**

**价值2千万的总结，吃饱了就是 队列，喝高了就是 堆栈**

