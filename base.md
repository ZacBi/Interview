# Java base

## Java概览

### 2.4 Java的加载与运行

先编写java源文件，然后通过javac对源文件进行编译，生成A.class文件,这是一个字节码文件，无法直接在操作系统中运行。这是Java的编译过程。
此后我们通过java A.class，启动Java虚拟机(JVM), JVM通过**类加载器**将.class文件加载到虚拟机内部, 由JVM翻译为机器码, 再交给操作系统执行.

### 2.5 public class 和 class 的区别

- 一个java源文件可以定义多个class
- java文件中的public class不是必需的
- 一个class会生成一个.class字节码文件
- 一个java文件中如果定义pulic class的话, 只能定义一个, 并且该类的名称必须和该java文件的名称一致.

## 3

### 3.5 方法的重载

方法重载：一个类中可以定义多个方法名相同，但是参数不同的方法
调用时，会根据不同参数自动匹配相应的方法。
**雷区**：重载的方法，实际是完全不同的方法，只是名字相同

1. 不同含义：形式参数

## 4 面向对象

### 4.1 面向过程与面向对象

宏观上需要面向对象，微观上还是需要面向过程。

### 4.2 对象和类

类可以看作对象的模板，是对对象的一种抽象。

### 4.3 JVM内存知识

JVM内存可以分为三个区域：栈(stack), 堆(heap), 方法区(method heap). 其实只有栈和堆,
方法区在堆中.

#### 4.3.1 栈

1. 栈描述的是方法执行的内存模型, 每个方法被调用都会创建一个栈帧(stack frame), 存储局部变量, 操作数, 方法出口. 栈帧是一个抽象概念, 准确的说, 其实就是包含方法内部一些变量的一块区域(也不准确).
2. JVM为每一个线程都创建一个栈, 用于存放该线程的执行方法的信息(实际参数, 局部变量).
3. 栈属于线程私有, 不能实现线程之间的共享.
4. 栈是连续的空间, 是系统创建的, 很快.

#### 4.3.2 堆

1. 堆用于存储创建好的对象和数组(数组也是对象)
2. JVM只有一个堆, 被所有线程共享
3. 堆是一个不连续的内存空间, 分配灵活, 但是慢.

#### 4.3.3 方法区(静态区)

1. JVM只有一个方法区, 被所有线程共享(Q: 线程是一个进程的线程还是所有进程的线程?);
2. 方法区实际上也是堆, 知识用于存储类, 常量相关的信息;
3. 用来存放程序中永远是不变或者唯一的内容(类信息, 静态变量, 字符串常量.)

以某一个类举例, 方法区应该存在:

- 代码
- 静态变量
- 静态方法
- 字符串常量

### 4.5 构造器

1. 通过关键字new调用
2. 构造器虽然有返回值, 但是不能定义返回值的类型(返回值的类型肯定是本类), 不能在构造方法里面使用return返回某个值
3. 如果我们没有定义构造器, 则编译器会自动地构造一个无参的构造函数, 如果已经定义的话编译器不会自动添加.
4. 构造方法必须和类名一致!

### 4.6 构造方法的重载

经常需要根据参数的不同用不同的构造方法.

### 4.7 垃圾回收机制

#### 引用计数

堆中的每一个对象都有一个引用计数, 被引用一次, 计数加1, 当指向该对象的引用失效时, 则计数减1, 直到计数为0, 则表示变为无用对象. 优点是算法简单, 缺点是'循环引用的无用对象'无法识别

#### 引用可达(根搜索算法)

&emsp;&emsp;程序把所有的引用关系看作图, 从一个结点GC root开始, 寻找对应的引用结点, 找到结点以后, 继续寻找这个结点的引用结点, 当所有的引用结点寻找完毕之后, 剩余的结点被认为是没有引用到的结点, 即无用的结点.

#### 4.7.2 分代回收机制

&emsp;&emsp;把**堆内存**分为三个: Eden, Survivor, Tenured/Old.  

1. 新建一个对象时, 绝大多数时候会分为Eden里面
2. 当Eden满时(或者达到一个比例)不能新建对象, 则触发垃圾回收机制(GC, garbage collection), 将无用对象清理掉, 然后剩余的复制到survivor中, 比如说S1(哪个都行), 同时清空Eden区.
3. 当Eden再次满了, 回将S1中不能清空的对象复制到另一个survivor中, 如S2中, 同时将Eden中不能清空的对象也复制到S2中, 保证S1和Eden被清空.
4. 重复多次(默认15次), 将survivor中没有被清理的对象, 复制到老年区中
5. 当老年区满了, 则会触发一次完整的垃圾回收(Full GC), 之前新生代的垃圾回收称为minor GC

#### 4.7.3 JVM调优和Full GC

当触发Full GC时会对系统的性能产生影响, JVM调优的过程很大一部分就是堆Full GC的调优, 有如下原因可能导致Full GC:

1. 年老代(Tenured)被写满
2. 持久代被写满
3. System.gc()被显式调用

#### 4.7.4 内存泄露

##### 创建大量无用对象

比如, 我们在需要大量拼接字符串时, 使用了String而不是String Builder.

```java
String str = "";
for (int i = 0; i < 10000; i++){
    str += 1;   // 相当于产生了10000个String对象
}
```

##### 静态集合类的使用

##### 各种连接对象未关闭

##### 监听器的使用

释放对象时没有删除相应的监听器

**要点**:

1. 程序员无权调用垃圾回收器
2. 程序员可以调用System.gc(), 该方法只是通知JVM, 并不是运行垃圾回收机制, 尽量少用, 会申请调用Full GC, 成本高, 影响系统性能.
3. finalize方法, 是Java提供给程序员用来释放对象或资源的方法, 但是尽量少用.

### 4.8 This 关键字

创建一个对象的过程:

1. 分配对象空间, 并将对象成员变量初始化为0或者null
2. 执行属性值的显式初始化;
3. 执行构造方法
4. 返回对象的地址

### 4.9 Static 关键字

static修饰的变量和方法, 从属于类, 普通变量和方法从属于对象.

### 4.10 静态初始化块

### 4.11 参数传值机制

Java中所有的参数都是"值传递", 也就是"传递的是值的副本", 也就是说,
我们得到的是"原参数的复印件, 而不是原件", 因此, 复印件不会改变原件.

**注意**, 即使是值传递, 对于对象而言是对象地址的复制, 所以仍然会对对象进行更改.

### 4.12 包机制

### 4.13 重写(override)

方法的重写需要遵循一些规则:

1. "==": 方法名, 形参列表相同;
2. "≤": 返回值类型和声明一场类型, 子类小于等于弗雷
3. "≥": 访问权限, 子类大于等于父类

### 4.13 "==" 和 equals

"==" 代表比较双方是否相同. 如果是基本类型则表示值相等, 如果是引用类型则表示地址相等即使同一个对象.

`equals`方法, 提供定义"对象内容相等"的逻辑.

### 4.14 继承树的追溯

当用构造方法初始化某一个对象时, 无论写与不写`super()`, 总会调用`super()`对父类先行构造, 再构建当前类. 总的来说, 应该先创建`Object`, 再创建父类, 再创建子类.
换句话来说, 流程应该如此: 先向上追溯到`Object`, 然后再一次向下之心累的初始化块和构造方法, 直到当前子类为止.

### 4.15 封装

封装的优点

1. 提高代码的安全性;
2. 提高代码的复用性;
3. "高内聚": 封装细节, 便于修改内部代码, 提高可维护性
4. "低耦合": 简化外部调用, 便于调用者调用, 便于扩展和协作.

### 4.16 封装的细节

碰到属性, 先"吝啬"一下, 用`private`修饰符.

### 5.5 多态

多态是指同一个方法的调用, 由于对象不同可能会有不同的行为. 显示生活中, 同一个方法, 具体实现会完全不同.

- 多态是方法的多态, 和属性无关
- 多态存在的三个必要条件: 继承, 方法重写, 父类引用指向子类对象
- 父类引用指向子类对象以后, 用该父类引用调用子类重写的方法, 此时多态就出现了.

### 5.6 对象的转型

向上转型和向下转型
向上比如父类转为子类, 向下比如父类转为子类.

### 5.7 final

final 关键字

1. 修饰变量, 只能赋一次值
2. 修饰方法, 方法不能被子类重写, 但是可以重载
3. 修饰类, 修饰的类不能被继承, 比如Math, String等

### 5.8 数组的使用

1. 长度确定
2. 元素类型相同
3. 数组类型可以是任意类型

数组变量属于引用类型， 数组也可以看成是对象，  数组中的每个元素相当于该对象的成员变量

### 5.9 访问修饰符

--------

 | 访问修饰符 | 类  | 包  | 子类 | 其他包 |
 | ---------- | --- | --- | ---- | ------ |
 | public     | √   | √   | √    | √      |
 | protect    | √   | √   | √    | ×      |
 | default    | √   | √   | ×    | ×      |
 | private    | √   | ×   | ×    | ×      |

### 7.8 抽象类

### 7.9 接口

区别:  
普通类: 具体实现  
抽象类: 具体实现, 规范(抽象方法)  
接口: 规范(JDK8以后又可以加一些方法了)  

#### 7.9.1 JDK8以前的接口定义方法

```shell script
[访问修饰符] interface 接口名 [extends 父接口1, 父接口2, ...] {
    常量定义;
    方法定义;
}
```

需要说明的是:
访问修饰符: 只能是`pulic`或者`default`  
接口名: 和类相同  
extends: 可以多集成  
常量: 接口中的属性只能是常量, 即总是`public static final`, 不写也是  
方法: 接口中的方法只能是: `public abstract`, 不写也是

举例:

```java
/**飞行接口**/
interface Volant {
    int FLY_HIGHT = 100; // 总是public static final;
    void fly(); //总是: public abstract void fly()
}

interface Honest {
    void helpOther();
}

class Angel implements Volant, Honest {
    public void fly() {
    System.out.println('a');
    }

}
```

#### 7.9.2 JDK8以后的接口

Java8以后, 允许在接口里定义默认方法和类方法  
默认方法和抽象方法的区别是抽象方法必须被实现, 默认方法不是,
作为替代方法, 接口可以提供默认方法的实现, 所有的接口的实现类都会通过集成得到这个方法

## 8 String

- `String`为不可变字符序列

创建字符串对象代码如下, 当执行这段代码时, 首先会在JVM的堆区创造一个对象, 这个对象指向存放在方法区中的常量区的一些字符, 之后运行这段代码时(其实是方法)时会创造栈帧, 栈帧中的变量引用变量`str1`又会指向堆区的这个字符串对象. `str2`和`str1`有所不同, `str2`直接指向方法区中的常量池中的`"abc"`

```java
String str1 = new String("abc")
String str2 = "abc";
```

### 8.1 包装类

#### 8.1.1 基本知识

Java中并非所有的都是对象, 比如基本数据类型(8种), 这个时候
就需要进行类型转换(?)

#### 8.1.2 自动装箱与自动拆箱

```java
package AutoBox;

public class TestAutoBox {
    public static void main(String[] args) {
        Integer i = 100; // 自动装箱
        // 相当于下面的步骤
        Integer j = Integer.valueOf(100);

        // 拆箱
        Integer b = 100;
        int c = b;
        // 相当于下面步骤
        int d = b.intValue();

        Integer e = null;
        if (e != null){
            int f = e;  // 自动拆箱调用了c.intvalue()
        }

        // 缓存[-128, 127]之间的数字
        Integer in1 = -128;
        Integer in2 = -128;
        System.out.println(in1 == in2); // true
        System.out.println(in1.equals(in2));
        Integer in3 = 1234;
        Integer in4 = 1234;
        System.out.println(in3 == in4); // false
        System.out.println(in3.equals(in4));
    }
}
```

为什么上面的比较一个是`true`一个是`false`? 因为Java缓存了[-128, 127]之间的
包装对象, 当检查这个值在范围之内的话, 就直接返回缓存好的对象, 可以通过`Integer.valueOf`方法查看.

### 8.2 String回首

`String`类是一个不可变unicode序列, 为什么不可变? 需要观察`String`类的源码
在源码里面存储的是final修饰的字符数组, 即为常量, 所以是不可变.
此外, String类的比较请用**equals**.

#### 8.2.1 String陷阱

```java
public class TestString {
    public static void main(String[] args){
        String str = "";
        for (int i = 0; i < 5000; i++){
            str = str + i;
            // 因为是进行字符串的拼接, 所以`i`会
            // 先类型转换为String类型, 再进行拼接, 这是该条语句产生的第一个string对象
            // 拼接之后又会产生另一个对象, 所以该条语句一共产生两个对象
            // 5000次遍历以后会产生10000个对象, 所以容易爆内存(?存疑, 因为会启用GC?)
        }
    }
}
```

## 9 容器

### 9.1 容器的基本概念

### 9.2 泛型

泛型能帮助我们建立类型安全的集合

### 9.3 ArrayList

ArrayList 底层是用数组实现的, 查询效率高, 增删效率低, 线程不安全, 我们一般使用它.

ArrayList解决的两个问题:

- 什么时候扩容
- 怎么扩容

### 9.4 LinkedList

LinkedList查询效率低, 增删效率高, 线程不安全, 但是我们一般不使用它

### 9.5 Vector

Vector 底层是用数组实现的List, 相关方法啊增加了安全检查, 因此是'线程安全但效率低'

### 9.6 HashMap

### 9.7 TreeMap

TreeMap 底层使用的是红黑树, 相比HashMap来说, 效率比较低
但是线程安全.

### 9.8 HashSet

HashSet底层用的就是HashMap, 不过HashSet中的所有值, 都是HashMap中的键,
所以HashSet能够达到不重复的效果

### 9.9 TreeSet

## 10 异常与多线程

### 10.1 异常

&emsp;&emsp;需要注意一个ClassCastException, Java Doc 是这么描述的, 更通俗一点的讲这是**JVM在检测到两个类型间转换不兼容时引发的运行时异常**.
> 抛出表示代码尝试将对象转换为不属于实例的子类。 例如，以下代码生成一个ClassCastException:
>
> ```python
> Object x = new Integer(0);
> System.out.println((String)x);
> ```

### 10.2 多线程

### 10.2.3 多线程原理

首先main方法会压栈(栈帧)执行, main中运行的对象是在堆内存的,
这个对象是有一个地址值的, 每次调用start方法, 都会在JVM中开辟一个新的栈空间,
CPU就有了执行的权力, 可以选择不同的栈空间中的方法, 注意栈帧和栈空间的区别,
栈帧是比栈空间的概念要小.

### 10.2.4 Tread类常用方法

### 10.2.7 线程安全问题

为了保证每个线程都能执行原子操作, Java引入了线程同步机制.
有三种方式完成同步操作:

- 同步代码块
- 同步方法
- 锁机制

#### 10.2.7.1 同步代码块

`synchronized`关键字可以用于某个方法的某个区块中, 表示只对这个区块的资源实行互斥访问
格式

```java
synchronized (同步锁) {
// 需要同步操作的代码
}
```

同步技术的原理:
使用了一个锁对象, 这个锁对象叫同步锁, 也叫对象锁, 也叫对象监视器

假如三个线程一起抢占cpu的执行权, 谁抢到了谁执行run方法,
假如第一个线程抢到了cpu, 遇到`synchronized`代码块,
会检查`synchronized`中是否有锁对象, 如果有, 则获取锁对象, 并进入代码块中
此时, 第二个线程抢到了cpu的执行权, 执行run方法, 同样检查`synchronized`中是否有锁对象,
但是并没有发现锁对象(?), 随后第二个线程会进入到阻塞状态, 会一直等待直到第一个线程归还锁对象

总结: 同步中的线程, 没有执行完成不会释放锁, 同步外的进程没有锁进不去同步块.

### 10.2.4 线程状态

- 创建态
- 运行态(runnable)
- 阻塞态(blocked), 这里对应操作系统的就绪态

-

### 10.2.5 Java线程通信

### 10.2.6 线程池

线程的创建和销毁都需要消耗一定的资源, 如果线程执行的时间比较短,
那么这样的开销相对来说是比较大的, 所以需要一种方法使得线程可以复用,
就是执行完一个任务而不被销毁, 而是可以继续执行其他任务

线程池的本质就是一个容器.

1. 当程序第一次启动时, 创建多个线程, 保存到一个集合中
当我们想要使用线程的时候, 就可以从容器中取出线程使用, `Thread t = new list.remove(0)`,
返回的是被移除的元素, (线程只能被一个任务使用).
当我们使用完线程以后, 需要把线程归还给线程池, `list.add(t)` 或者 `linked.addLast(t)`

JDK1.5 之后, JDK内置了线程池, 不需要再自己创建容器了.

### 10.2.7 Lambda

Lambda的使用前提:

1. 使用Lambda必须具有接口, 且要求接口中有且仅有一个抽象方法,
    有多个抽象方法时, 编译器无法知道重写的是哪个方法, 所以会报错
2. 使用Lambda必须具有上下文推断
    也就是方法的参数或局部变量类型必须为Lambda对应的接口才行, 才能使用Lambda表达式.

## 11 IO 流

### 11.1 IO过程

为甚什么要做IO? 因为硬盘是永久存储的, 而内存是临时存储的.

- 输入(I): 从硬盘读取到内存中
- 输出(O): 将内存中的数据写入到硬盘中
- 流(Stream): 1个字符 = 2个字节 1个字节 = 8 个bit

### 11.2 字节流

### 11.4 缓冲流

之前提到的字节输入流或者字符输入流读取文件的过程比较慢, 为什么?
因为首先字节码向JVM发请求, 然后JVM向OS发请求, 然后再读取文件, 读取到文件的内容后
再沿原来的请求返回, 过程十分繁琐.
加入字节缓冲流之后, 给基本的字节输入流增加一个缓冲区来提高基本的字节输入流的读取效率.
BufferedInputStream(new FileInputStream())
int len = fis.read()
从原来的一次返回一个字符/字节, 变成了一次返回一个数组(缓冲区), 本质就是空间换时间的一个过程.

## 12 网络编程

## 12.1 TCP通信的文件上传案例

原理: 客户端读取本地的文件, 把文件上传到服务器, 服务器再把上传的服务器保存在服务器的硬盘上

1. 客户端使用本地的字节输入流, 读取要上传的文件
2. 客户端使用网络字节输出流, 把读取的文件上传到服务器
3. 服务器使用网络字节输入流, 读取客户端上传的文件
4. 服务器使用本地字节输入流, 把读取到的文件, 保存到服务器的硬盘上
5. 服务器使用网络字节输出流, 给客户端回写一个上传成功
6. 客户端使用网络字节输入流, 读取服务器回写的数据
7. 释放资源

注意网络流和本地流之间的区别
文件上传的原理, 就是文件的复制

## 13 Stream

拼接流式模型: 建立一个生产线, 按照生产线来生产商品
生产饮料:

## 14 Junit 测试

白盒测试需要写代码, 关注程序具体的程序流程.
黑盒测试不用写代码, 只需要点点. 看程序是否能够输出

Junit测试: 白盒测试

- 步骤:
    1. 定义一个测试类(测试用例)
        - 建议:
            - 测试类名: 被测试的类名 + Test
            - 包名: xxx.xxx.xx.test
    2. 定义测试方法: 可以独立运行
        - 建议:
            - 方法名: test + 测试的方法名
            - 返回值: void
            - 参数列表: 空参
    3. 给方法加@Test注解
    4. 导入Junit以来

## 15 反射

反射: 框架设计的灵魂, 将类的各个组成部分封装为其他对象, 这就是反射机制.
框架: 半成品软件. 可以在框架的基础上进行软件开发, 简化编码.

Java代码在计算机中经历的阶段: 三个阶段

### 获取Class的方式

1. CLass.forName(全类名, 即把类的路径写完): 将字节码加载进内存, 返回class对象
     - 多用于配置文件, 将类名定义在配置文件中. 读取文件, 加载类
2. 类.class
    - 多用于参数的传递
3. 对象.getClass()
    - 多用于对象的获取字节码的方式

结论: 同一个字节码文件(*.class)在一次程序运行过程中, 只会被加载一次, 不论通过哪一种方式获取的class对象都是同一个

### class对象功能

- 获取功能:
    1. 获取成员变量
        - Field[] getFields()
        - Filed getField(String name)
    2. 获取构造方法
    3. 获取成员方法
    4. 获取类名

#### Filed: 成员变量

操作

1. 设置值
    - void set(Object obj, Object value);
2. 获取值
    - get(Object obj)
3. 忽略访问权限修饰符的安全检查
    - setAccessible(true): 暴力反射

#### Constructor

1. 创建对象:
    - T newInstanc(Object... initargs)
    - 如果使用空参数来创建对象, 操作可以简化: Class对象的newInstance方法

#### Method

1 执行方法:
    - Object

### 案例

- 需求: 写一个"框架", 不能改变**该类**的任何代码的前提下, 可以帮我们创建任意类的对象, 并且执行其中任意方法
  - 实现:
        1. 配置文件
        2. 反射
  - 步骤:
        1. 将需要创建的对象的全类名和需要执行的方法定义在配置文件中
        2. 在程序中加载读取配置文件
        3. 使用反射技术来加载类文件进内存
        4. 创建对象
        5. 执行方法

## 16 注解

- 概念: 给计算机看的
- 定义: 注解, 也叫做元数据. 是一种代码级别的说明. 他是JDK1.5及以后版本引入的一个特性, 与类, 接口, 枚举是在同一个层次.
它可以声明在包, 类, 字段, 方法, 局部变量, 方法参数等的前面, 用来对这些元素进行说明, 注释.\

- 概念描述:
  - JDK 1.5之后的新特性
  - 说明程序的
  - 使用注解: @注解名称
- 作用分类:
    1. 编写文档: 通过代码里面标识的注解生成文档(生成doc文档)
    2. 代码分析: 通过代码里标识的注解对代码进行分析(使用反射)
    3. 编译检查: 通过代码里标识的注解让编译器能够实现基本的编译检查(Override)

- JDK中预定义的一些注解
  - @Override
  - @Deprecated
  - @SuppressWarnings
    - 一般传递参数"all"

- 自定义注解
  - 格式:
        元注解
        public @interface 注解名称{}
  - 本质: 注解本质上就是一个接口, 其继承了java.lang.annotation.Annotation
    - public interface annotation.MyAnno extends java.lang.annotation.Annotation
  - 属性: 接口中的抽象方法
    - 要求:
            1. 属性的返回值类型
                - 基本的数据类型
                - String
                - 枚举
                - 注解
                - 以上类型数组
            2. 定义了属性, 在使用时需要给属性赋值
                - 如果定义属性时, 使用default关键字给属性默认初始化值, 则使用注解时, 可以不进行属性的赋值
                - 如果只有一个属性需要赋值, 并且属性的名称是value, 则value可以省略, 直接定义值就行
                - 数组赋值时, 值使用{}包裹. 如果数组中只有一个值, 则{}可以省略
  - 元注解: 描述注解的注解
    - @Target: 描述注解能够作用的位置
      - ElementType取值:
        - TYPE: 作用于类上
        - METHOD: 作用于方法上
    - FIELD: 作用于成员变量上
    - @Retention: 描述注解被保留的阶段
      - @Retention(RetentionPolicy.RUNTIME): 当前被描述的注解, 会保留到class字节码文件中, 并被JVM读取到
    - @Documented: 描述注解是否被抽取到api文档中
    - @Inherited: 描述注解是否被子类继承

- 在程序中使用(解析)注解: 获取注解中定义的属性值
    1. 获取注解定义的位置的对象 (Class, Method, Field)
    2. 获取指定的注解
        - getAnnotation(Class)

        ```java
        // 其实就是在内存中生成了一个该注解接口的子类实现对象
       public class PropImpl implements Pro {
           public String className() {
               return "package.path";
           }
           public String methodName() {
               return "show";
           }
       }
    3. 调用注解中的抽象方法获取配置的属性值

- 小结:
    1. 大多数时候, 我们会使用注解, 而不是自定义注解
    2. 注解给谁用?
        1. 编译器
        2. 解析程序用
    3. 注解不是程序的一部分, 可以理解为注解就是一个标签

## 17 数据库部分

### 17.2 事务

#### 事务的四大特性: ACID

- atom(原子性) 一次事务的所有操作要么全部完成, 要么全部失败, 数据库的操作中断时应该归滚(rollback)事务发生前的状态
- consistency(一致性): 事务操作前后, 数据总量不变.
- isolated(隔离性): 多个事务之间. 相互独立.
- durability(持久性) 一旦事件提交或者回滚, 那么数据库会持久的保存数据

事务的操作

1. 开启事务: start transaction
2. 回滚: rollback
3. 提交: commit

**MySQL数据库中事务默认自动提交**
一条DML(增删改)语句回默认提交的.
事务提交有两种方式:

- 自动提交
  - MySql就是自动提交的, 一条增删改语句会自动提交一次事务
- 手动提交:
  - oracle 数据库是手动提交事务的
  - 需要先开启事务, Mysql中用的是`start transaction`语句, 然后再提交
- 修改事物的默认提交方式
  - 查看事物的默认提交方式: select @@autocommit; -- 1 自动提交 0 手动提交
  - 修改默认提交方式: set @@autocommit = 0;

#### 17.3 事务的隔离级别(了解)

多个事务之间是相互独立的, 如果多个事务操作同一批数据, 则会引发一些问题, 设置不同的隔离级别就可以解决这些问题.

存在的问题:

- 脏读: 一个事务, 读取到另一个事务中没有提交的数据
- 不可重复读(虚读): 同一个事务中, 两次读取到的数据库不一样
- 幻读: 一个事务操作(DML)数据表中所有记录, 另一个事务添加了一条数据, 前一个事务查询不到自己的修改

隔离级别:

1. read uncommitted: 读未提交
    - 问题: 脏读, 不可重复读, 幻读
2. read committed: 读已提交(oracle默认级别)
    - 问题: 不可重复读, 幻读
3. repeatable read: 可重复读 (MySQL默认级别)
    - 问题: 幻读
4. serializable: 串行化
    - 可以解决所有的问题

隔离级别从小到大, 安全性越来越高, 但是效率越来越低.

- 查询数据库隔离级别:
  - select @@transaction_isolation;
- 数据库设置隔离级别
  - set global transaction isolation level

- 演示:

```mysql
    SET GLOBAL TRANSACTION ISOLATION LEVEL READ UNCOMMITTED ;
    START TRANSACTION ;
    # 转账
    update account set balance = balance - 500 WHERE id = 1;
    UPDATE account set balance = balance + 500 WHERE id = 2;
```

### DCL 用户管理

SQL语句分类:

- DDL: 操作数据库和表
- DML: 增删改表中的数据
- DQL: 查询表中数据
- DCL: 管理用户和权限

DBA: 数据库管理员

#### DCL: 管理用户, 授权

1. 管理用户
    1. 增加用户:
        `creaste 'usename'@'host' IDENTIFIED BY 'pwd'`
    2. 删除用户
        `drop 'usename'@'host';`
    3. 修改用户密码
        - `UPDATE USER SET PASSWORD = PASSWORD('new_pwd') WHERE USER = 'USERNAME'`
        - `SET PASSWORD FOR 'username'@'host' = PASSWORD('new_pwd')`
    4. 查询用户
        `select * from table where cond`
2. 权限管理
    1. 查询权限
        ```mysql
        show grants for 'username'@'host';
        ```
    2. 授予权限
    `GRANT priviledge on database.table TO 'username'@'host'`
    3. 撤销权限
    `revoke priviledge on database.table from 'username'@'host'`

## 18 JDBC

### concept

JDBC: java Database connectivity Java数据库连接, 用java语言操作数据库

JDBC是sun公司定义的一套操作所有关系型数据库的规则(接口), 这一套规则(接口)就可以完成与各个不同的数据库的连接,
但是接口的实现需要数据库厂商自己完成. 我们把实现了接口的类称为**数据库驱动**, 数据库驱动是由那些厂商提供的.

### 快速入门

1. 导入驱动jar包
2. 注册驱动
3. 获取数据库连接对象connection
4. 定义sql
5. 获取执行sql语句的对象 Statement
6. 执行sql, 接受返回结果
7. 处理结果
8. 释放资源

### 具体对象

1. DriverManager: 驱动管理对象
    - 注册驱动: 真正注册驱动的是DriverManager, 在JDK5以后可以不用显式的注册驱动
        - static void registerDriver(Driver driver), 注册给定驱动 DriverManager, 写代码使用: Class.froName("com.mysql.jdbc
    .Driver")
    - 方法: static Connection getConnection(String url, String user, String password)
        - 参数:
        - url, 连接的路径: jdbc:mysql://ip_addr:port/database?query_field
2. Connection: 数据库连接
    1. 获取执行sql的对象
        - Statement createStatement()
        - PreparedStatement prepareStatement(String sql)
    2. 管理事务:
        - 开启事务: void setAutoCommit(boolean autoCommit): 调用该方法设置参数为false, 即开启事务
        - 提交事务: void commit()
        - 回滚事务: void rollback()
3. Statement: 执行sql的对象
    1. 执行sql
        - int executeUpdate(String sql): 执行DML(insert, update, delete)语句, DDL(创建表/数据库, 删除表/数据库)语句
            - 通过返回的影响的行数, 判断DML语句是否成功
        - ResultSet executeQuery(String sql): 执行DQL(select)语句
    2. 练习
        1. account表 添加一条记录
        2. account表 修改记录
        3. account表 删除记录
4. ResultSet: 结果集对象, 封装查询的结果
    - boolean next(): cursor 向下移动一行, 判断该行是否是最后一行末尾
    - getXXX(): 获取数据, XXX代表数据类型, 比如getInt()返回Int类型数据
        - 参数, int(代表列的编号, 从1开始), string(列的名字)
    - 使用步骤:
        1. 游标向下移动一行
        2. 判断是否有数据
        3. 获取数据
5. PreparedStatement: 执行sql的对象
    1. sql注入问题: 在凭借sql时, 有一些sql的特殊关键字参与字符串的凭借. 会造成安全性的问题
        1. 输入用户随便, 输入密码: a' or 'a' = 'a
        2. sql: select * from user where username = 'fadsfads' and password = 'a' or 'a' = 'a';
    2. 解决sql注入的问题: 使用PreparedStatement
    3. 预编译的SQL: 参数使用?作为占位符
    4. 步骤:
        1. 都如驱动jar包
        2. 注册驱动
        3. 获取数据库连接对象
        4. 定义sql
            - sql的参数使用?作为占位符. 如: select * from user where username = ? and password = ?;
        5. 获取执行sql语句的的对象 PreparedStatement Connection.prepareStatement(String sql)
        6. 给?赋值:
            - 方法: setXXX(param1, param2)
                - param21: ?的位置编号从1开始
                - param22: ?的值
        7. 执行sql, 接受返回结果, 不需要传递sql语句
        8. 处理结果
        9. 释放资源
    5. 注意: 后期都会使用PreparedStatement来完成增删改查的所有操作
        1. 可以防止SQL注入
        2. 效率更高

### 抽取JDBC工具类: util

- 目的: 简化书写
- 分析:
  1. 注册驱动也抽取
  2. 抽取一个方法获取连接对象
      - 需求: 不想传递参数(麻烦), 还得保证工具类的通用性
      - 解决: 配置文件
  3. 抽取一个方法释放资源

#### 练习

需求:
    1. 通过键入录入用户名和

### JDBC 控制事务

1. 事务: 一个包含多个步骤的业务操作. 如果这个业务操作被事务管理, 则这多个步骤要么同时成功, 要么同时失败
2. 操作:
    1. 开启事务
    2. 提交事务
    3. 回滚事务

## 19 数据连接池

1. 数据库连接池
    - 概念: 其实就是一个容器(集合), 存放数据库连接对象的一个容器, 当系统初始化之后, 容器被创建, 容器中会申请一些连接对象,
当 用户来访问数据库时, 从容器中获取丽娜姐对象, 用户访问完之后, 会将连接对象归还给容器.
2. 好处: 节约资源; 用户访问高效.
3. 实现:
    1. 标准接口: DataSource javax.sql 包下的
        1. 方法:
            - 获取连接: getConnection
            - 归还连接: 如果连接对象Connection是从连接池中获取的, 那么调用Connection.close()方法, 则不会关闭连接, 而是归还连接.
    2. 一般我们不去实现它, 有数据库厂商来实现
        1. C3P0: 数据库连接池技术
        2. Druid: 阿里巴巴实现的
4. C3P0: 数据库连接技术
    - 步骤:
        1. 导入jar包
        2. 定义配置文件:
            - 名称: C3P0.properties or C3P0-config.xml
            - 路径: 直接将文件放置在src文件目录下即可
        3. 加载配置文件(properties)
        4. 创建核心对象: 数据库连接池对象 ComboPooledDataSource
        5. 获取连接对象: getConnection()

5. Druid: alibaba提供
    1. 步骤. 同上
    2. 定义工具类
        1. 定义一个类 JDBCUtils
        2. 提供静态代码块加载配置文件, 初始化连接池对象
        3. 提供方法
            1. 获取连接方法: 通过数据库连接池获取链接
            2. 释放资源
            3. 获取连接池的方法

## 20 Spring JDBC

Spring框架对JDBC的简单封装, 提供了一个JDBCTemplate对象简化JDBC的开发

步骤:

1. 导入jar包
2. 创建jdbcTemplate对象. 依赖于数据源DataSource
    - JdbcTemplate template = new JdbcTemplate(ds);
