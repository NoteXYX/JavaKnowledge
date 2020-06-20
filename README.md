感谢牛客网！
## Java基础知识
1. **HashTable、HashMap、ConcurrentHashMap** 底层实现	，hashmap的hash计算方式？为什么要右移16位？HashMap扩容？ ConcurrentHashMap统计数据总量时的流程？put的流程，get流程、并发问题分析、哈希冲突、作为HashMap的key需要做什么？**TreeMap是通过红黑树实现的** ，**HashSet底层为HashMap**，存的value默认都为一个常量Object对象，名称为PRESENT。ConcurrentHashMap 1.8以后为CAS+同步锁，1.7以前为分段锁。
**HashMap为何设置链表达到8个、且数组长度到达64就转红黑树**？	答：1、TreeNodes(红黑树)占用空间是普通Nodes(链表)的两倍，为了时间和空间的权衡。2、节点的分布频率会遵循泊松分布，链表长度达到8个元素的概率为0.00000006，几乎是不可能事件。为什么转化为红黑树的阈值8和转化为链表的阈值6不一样：是为了避免频繁来回转化。（13次）
2. **Synchronized** 的锁，Synchronized加锁流程？syn为什么效率低？	Synchronized是重量级的锁吗？能实现轻量锁吗？Synchronized是不是可重入锁？synchronized和lock区别？Synchronized用在静态和非静态方法的区别？（9次）
（1）**synchronized和Lock都具备可重入性**。（**重入锁**是可重复获得资源的锁，已经获得锁的线程可以对当前的资源重入加锁而不会引起阻塞；**不可重入锁**是不可重复获得资源的锁，当已经获得锁的线程对当前资源再次加锁时，会把自己阻塞。）（2）synchronized不是可中断锁，而Lock是可中断锁。（3）synchronized是非公平锁，而对于ReentrantLock和ReentrantReadWriteLock，它默认情况下是非公平锁，但是可以设置为公平锁，ReentrantLock lock = new ReentrantLock(true); 如果参数为true表示为公平锁，为fasle为非公平锁。
3. **垃圾回收算法**，GC算法、分代收集、如何判断一个对象是否可回收？垃圾回收的优缺点？为什么默认使用G1垃圾收集器？	（7次）
 - jdk1.7 默认垃圾收集器Parallel Scavenge（新生代）+Parallel Old（老年代）
 - **jdk1.8 默认垃圾收集器Parallel Scavenge（新生代）+Parallel Old（老年代）**
 - **jdk1.9 默认垃圾收集器G1**，G1跟踪各个Region获得其收集价值大小，在后台维护一个优先列表；每次根据允许的收集时间，优先回收价值最大的Region
**常用GC算法：** 标记清除算法、复制算法 （新生代）、标记整理算法 （老年代）、分代收集算法
判断对象是否死亡：
 - **引用计数法**。无法解决对象之间相互循环引用的问题。
 - **可达性分析法**。可以作为GC Root的有：1、虚拟机栈（栈帧中的本地变-量表）中引用的对象。2、方法区中类静态属性引用的对象。3、方法区中常量引用的对象。4、本地方法栈中 JNI（即一般说的 Native 方法）引用的对象
 [7种垃圾收集器](https://www.cnblogs.com/cxxjohnson/p/8625713.html)
4. **BIO**、**NIO**、**AIO**，Netty线程机制，protoBuf和其他序列化框架的对比、**IO**模型  select poll epoll区别	（7次） 
- <font color=red>**BIO**</font>： 同步阻塞式IO。应用进程被阻塞，直到数据从内核缓冲区复制到应用进程缓冲区中才返回。
- <font color=red>**NIO**</font>： 同步非阻塞式IO。应用进程执行系统调用之后，内核返回一个错误码。应用进程可以继续执行，但是需要不断的执行系统调用来获知 I/O 是否完成，这种方式称为<font color=red>**轮询**</font>。
- <font color=red>**AIO**</font>：异步非堵塞的 IO 操作方式，异步 IO 是基于事件和回调机制实现的，也就是应用操作之后会直接返回，不会堵塞在那里，当后台处理完成，操作系统会通知相应的线程进行后续的操作。
- <font color=red>**I/O 多路复用**</font> 的特点是通过一种机制一个进程能同时等待多个文件描述符，**这一过程会被阻塞**，而这些文件描述符（套接字描述符）其中的任意一个进入读就绪状态，select()函数就可以返回。它可以让单个进程具有处理多个 I/O 事件的能力。
- <font color=red>**信号驱动IO：**</font> 应用进程使用 sigaction 系统调用，内核立即返回，应用进程可以继续执行，也就是说等待数据阶段应用进程是非阻塞的。内核在数据到达时向应用进程发送 SIGIO 信号，应用进程收到之后在信号处理程序中调用 recvfrom 将数据从内核复制到应用进程中。异步 I/O 与信号驱动 I/O 的区别在于，**异步 I/O 的信号是通知应用进程 I/O 完成**，**而信号驱动 I/O 的信号是通知应用进程可以开始 I/O**。
**select，poll，epoll**都是IO多路复用的机制。
- <font color=red>**select**</font> ：调用后select函数会阻塞，直到有描述符就绪（有数据 可读、可写、或者有except），或者超时（timeout指定等待时间，如果立即返回设为null即可），函数返回。当select函数返回后，通过遍历所有描述符，来找到就绪的描述符。select目前几乎在所有的平台上支持，其**良好跨平台支持**也是它的一个优点。select的一 个缺点在于**单个进程能够监视的文件描述符的数量存在最大限制**，文件描述符存储使用**数组**实现,在Linux上一般为**1024**。
- <font color=red>**poll**</font> ：select和poll都需要在返回后，通过遍历文件描述符来获取已经就绪的socket。**poll 没有描述符数量的限制；**
- <font color=red>**epoll**</font> ：通过回调函数内核会将 I/O 准备好的描述符加入到一个链表中管理，进程调用 epoll_wait() 便可以得到事件完成的描述符,进程**不需要通过轮询来获得事件完成的描述符**。**epoll 仅适用于 Linux OS**。epoll 比 select 和 poll 更加灵活而且**没有描述符数量限制**。<font color=red>**epoll工作模式：**</font> **LT 模式**：当 epoll_wait() 检测到描述符事件到达时，将此事件通知进程，**进程可以不立即处理该事件**，下次调用 epoll_wait() 会再次通知进程。是**默认**的一种模式；**ET 模式：** 通知之后进程必须立即处理事件，下次再调用 epoll_wait() 时不会再得到事件到达的通知。
- **应用场景：** select 的 timeout 参数精度为微秒，而 poll 和 epoll 为毫秒，所以**select 更加适用于实时性要求比较高**的场景，比如核反应堆的控制；**poll** 没有最大描述符数量的限制，如果平台支持并且对**实时性要求不高**，应该使用 poll 而不是 select；**epoll**只需要运行在 Linux 平台上，有**大量的描述符**需要同时轮询，并且这些连接最好是**长连接**。
- **文件描述符**：在linux系统中打开文件就会获得文件描述符，它是个很小的非负整数。每个进程在PCB中保存着一份文件描述符表，文件描述符就是这个表的索引，每个表项都有一个指向已打开文件的指针。
5. **JVM类加载机制**	延伸：父类和子类中都有静态变量、静态代码块、非静态变量、构造函数。new这个子类的时候，以上四个执行顺序是什么样的？ 顺序为：父类静态变量 --> 父类静态代码块 --> 子类静态变量 --> 子类静态代码块 --> 父类非静态代码块 --> 父类构造函数 --> 子类非静态代码块 --> 子类构造函数
<font color=red>**类加载过程：**</font> 
- <font color=red>**加载：**</font> 通过类的完全限定名称获取定义该类的二进制字节流。将该字节流表示的静态存储结构转换为方法区的运行时存储结构。在内存中生成一个代表该类的 Class 对象，作为方法区中该类各种数据的访问入口。
- <font color=red>**验证**</font> 确保 Class 文件的字节流中包含的信息符合当前虚拟机的要求，并且不会危害虚拟机自身的安全。
- <font color=red>**准备**</font> 准备阶段为类变量分配内存并设置初始值，使用的是方法区的内存，类变量是被 static 修饰的变量。
- <font color=red>**解析**</font> 将常量池的符号引用替换为直接引用的过程。**符号引用**与虚拟机实现的内存布局无关，引用的目标并不一定已经加载到内存中。**直接引用**可以是直接指向目标的指针，是和虚拟机实现的内存布局相关的，引用的目标必定已经在内存中存在。
- <font color=red>**初始化**</font> 初始化阶段是虚拟机执行类构造器 <clinit>() 方法的过程。根据程序员通过程序制定的主观计划去初始化类变量和其它资源。
**双亲委派模式**？启动类加载器 --> 扩展类加载器 --> 应用程序类加载器 --> 自定义类加载器（4次）
6. **JVM内存模型**	、JVM内存区域划分、堆中的异常，栈中的异常（4次）
7. **ThreadLocal** 原理、使用场景、内存泄漏	（4次）
<font color=red>**ThreadLocal提供了线程内存储变量的能力**</font>，这些变量不同之处在于每一个线程读取的变量是对应的互相独立的。ThreadLocal的静态内部类<font color=red>ThreadLocalMap为每个Thread都维护了一个Entry<key, value>型数组table</font>，**key**存储线程本地对象，**value**为线程的变量副本，<font color=red>key为弱引用（生命周期只能存活到下次GC前），value并非弱引用</font>。在调用ThreadLocal的get()、set()方法时完成后<font color=red>**再调用remove方法可避免内存泄漏**</font>。每个ThreadLocal实例在table中的索引i是不同的。ThreadLocal的典型场景正如上面的**数据库连接管理**，**线程会话管理**等场景
8. **设计模式**：工厂模式、单例模式、适配器模式、懒汉模式、恶汉模式、观察者模式	、每种模式的问题（4次）
9. **线程池**原理、线程池的好处、如何确定线程池大小（3次）
10. java又是怎么保证线程同步（安全）的呢？**volatile** 可见性原理	，volatile不保证原子性是为啥？（3次）
	<font color=red>**volatile**</font>：1、保证了不同线程对这个变量进行操作时的可见性，即一个线程修改了某个变量的值，这新值对其他线程来说是立即可见的。（实现**可见性**）2、禁止进行指令重排序。（实现**有序性**）3、volatile 只能保证对单次读/写的**原子性**。i++ 这种操作不能保证原子性。
11. 内存泄漏原因、举例子、如何排查内存泄漏的原因（2次）
12. **重载与重写** 、 多态。	（2次）
1、<font color=red>**重写**</font>：（1）**方法名、参数列表必须完全与被重写方法的相同**。（2）返回类型与被重写方法的返回类型必须兼容，即返回值必须类型相同，或子类返回值必须是父类返回值的派生类（java5 及更早版本返回类型要一样，java7 及更高版本可以不同）。（3）重写方法不能抛出新的检查异常或者比被重写方法申明更加宽泛的异常。（4）访问权限不能比父类中被重写的方法的访问权限更低。例如：如果父类的一个方法被声明为 public，那么在子类中重写该方法就不能声明为 protected。（5）声明为 final 的方法不能被重写。（6）构造方法不能被重写。	
2、<font color=red>**重载**</font>：（1）**方法名必须相同**、**参数的数量不同，或顺序不同（参数类型不同时），或参数类型不同** 都是重载。（2）**不能根据返回类型区分重载**，返回类型可以相同也可以不同。（3）被重载的方法可以改变访问修饰符；（4）被重载的方法可以声明新的或更广的检查异常。
3、在编译阶段，只是检查参数的引用类型。然而在运行时，Java 虚拟机(JVM)指定对象的类型并且运行该对象的方法。
4、方法重载是一个类的多态性表现，而方法重写是子类与父类的一种多态性表现。
[Java重载与重写](https://www.runoob.com/java/java-override-overload.html)

13. collection 和 Collections的区别	（2次）
14. **List、Set** ：List是不是有序的，能不能重复，ArrayList和LinkedList区别、List中如何删除元素：迭代器、为什么不能在遍历list的时候删除其中的元素呢（2次）
15. ==和equals的区别？	（2次）
16. 抽象类与接口的联系与区别？（2次）
有抽象方法的类一定是抽象类,抽象类中的方法可以不是抽象的;

| 参数 | 抽象类 | 接口 |
|:---------:|:-------------------:|:-------------:|
| 构造器 | 抽象类可以有构造器 | 接口不能有构造器 |
| 成员变量 | 可以有普通成员变量 | 接口中定义的变量只是public static final 类型，并且默认即为 public static final 类型，并且需要给出初始值 |
| 方法 | 可以包含非抽象的普通方法 | 所有方法必须都是抽象的 |
| 静态方法| 可以包含静态方法|不能包含静态方法| 
|方法访问类型|抽象方法可以有public、protected和default这些修饰符|接口方法默认修饰符是public。你不可以使用其它修饰符。|
|实现|子类使用**extends**关键字来继承抽象类。如果子类不是抽象类的话，它需要提供抽象类中所有声明的方法的实现|子类使用关键字**implements**来实现接口。它需要提供接口中所有声明的方法的实现|


17. JDK动态代理和Cglib代理的区别、底层是怎么实现的、哪个性能更好、cglib代理和jdk动态代理的优缺点？	（2次）
<font color=red>**反射：**</font> JAVA反射机制是在运行状态中，对于任意一个类，都能够知道这个类的所有属性和方法；对于任意一个对象，都能够调用它的任意一个方法；这种动态获取的以及动态调用对象的方法的功能称为java语言的反射机制。
<font color=red>**Java动态代理的优势**</font>是实现无侵入式的代码扩展，也就是方法的增强；让你可以在不用修改源码的情况下，增强一些方法；在方法的前后你可以做你任何想做的事情。
- <font color=red>**JDK动态代理**</font> 
	- 通过实现 InvocationHandler 接口，重写invoke方法创建自己的调用处理器类；
	- 通过为 Proxy 类指定 ClassLoader 对象和一组 interface 、调用处理器类的对象实例来创建动态代理类；
	` HelloInterface proxyHello = (HelloInterface) Proxy.newProxyInstance(hello.getClass().getClassLoader(), hello.getClass().getInterfaces(), handler);`
	其中的3个参数通过反射机制获得：**被代理的类的类加载器**、**被代理类的接口数组**、**调用处理器类的对象实例**；
	- **优点** ：Java原生支持的动态代理，无需外部依赖。 <font color=red>**缺点**：**被JDK动态代理的类一定要是实现了某个接口的**</font>，如果有些类并没有实现接口，则不能使用JDK代理，这时就要使用cglib动态代理了。
- <font color=red>**cglib动态代理** </font>
	- cglib动态代理类需要实现MethodInterceptor接口重写intercept方法
	- 通过`enhancer.setSuperclass(this.target.getClass());`对指定的目标类生成一个子类，并覆盖其中方法实现增强，但因为采用的是继承，所以不能对final修饰的类进行代理。
	- **优点：** 无论目标对象有没有实现接口都可以代理。**缺点：** 不能对final修饰的类进行代理。
	[java动态代理（JDK和cglib）](https://www.cnblogs.com/jqyp/archive/2010/08/20/1805041.html)
18. 一致性hash	（1次）[一致性哈希](https://www.jianshu.com/p/735a3d4789fc)
19. 锁升级的过程？	（1次）
- <font color=red>**偏向锁：**</font> 当一个线程访问同步代码块并获取锁时，会在Mark Word里存储锁偏向的线程ID。偏向锁是指一段同步代码一直被一个线程所访问，那么该线程会自动获取锁，降低获取锁的代价。
- <font color=red>**轻量级锁：**</font> 当锁是偏向锁的时候，被另外的线程所访问，偏向锁就会升级为轻量级锁，其他线程会通过自旋的形式尝试获取锁，不会阻塞，从而提高性能。若当前只有一个等待线程，则该线程通过自旋进行等待。针对的是多个线程在不同时间段申请同一把锁的情况
- <font color=red>**重量级锁：**</font> 当自旋超过一定的次数，或者一个线程在持有锁，一个在自旋，又有第三个来访时，轻量级锁升级为重量级锁。针对的是多个线程同时竞争同一把锁的情况。
20. 非公平锁和公平锁？优缺点？非公平锁抢占流程？	（1次）
21. 释放锁的流程？	（1次）
22. Java并发，并发并行的区别	（1次）
23. CountDownLatch和CyclicBarrier使用过吗？什么原理？	（1次）
24. 深拷贝浅拷贝的区别？ 
<font color=red>**浅拷贝：**</font> 1) 对于基本数据类型的成员对象，因为基础数据类型是值传递的，所以是直接将属性值赋值给新的对象。基础类型的拷贝，其中一个对象修改该值，不会影响另外一个。(2) 对于引用类型，比如数组或者类对象，因为引用类型是引用传递，所以浅拷贝只是把内存地址赋值给了成员变量，它们指向了同一内存空间。改变其中一个，会对另外一个也产生影响。
<font color=red>**深拷贝：**</font> (1) 对于基本数据类型的成员对象，因为基础数据类型是值传递的，所以是直接将属性值赋值给新的对象。基础类型的拷贝，其中一个对象修改该值，不会影响另外一个（和浅拷贝一样）。(2) 对于引用类型，比如数组或者类对象，深拷贝会新建一个对象空间，然后拷贝里面的内容，所以它们指向了不同的内存空间。改变其中一个，不会对另外一个也产生影响。(3) 对于有多层对象的，每个对象都需要实现 Cloneable 并重写 clone() 方法，进而实现了对象的串行层层拷贝。(4) 深拷贝相比于浅拷贝速度较慢并且花销较大。
**浅拷贝：** 直接调用父类的clone()方法
```java
public class Student implements Cloneable {
    //引用类型
    private Subject subject;
    @Override
    public Object clone() {
        //浅拷贝
        try {
            // 直接调用父类的clone()方法
            return super.clone();
        } catch (CloneNotSupportedException e) {
            return null;
        }
    }
}
```

**深拷贝：** （1）对于有多层对象的，每个对象都需要实现 Cloneable 并重写 clone() 方法，进而实现了对象的串行层层拷贝。（2）使用序列化。
```java
public class Subject implements Cloneable {
    private String name;
    @Override
    protected Object clone() throws CloneNotSupportedException {
        //Subject 如果也有引用类型的成员属性，也应该和 Student 类一样实现
        return super.clone();
    }
}

public class Student implements Cloneable {
    //引用类型
    private Subject subject;
	public Object clone() {
	        //深拷贝
	        try {
	            // 直接调用父类的clone()方法
	            Student student = (Student) super.clone();
	            student.subject = (Subject) subject.clone();
	            return student;
	        } catch (CloneNotSupportedException e) {
	            return null;
	        }
    }
}
```

[Java 浅拷贝和深拷贝](https://www.jianshu.com/p/94dbef2de298)（1次）
25. string几种拼接方式区别，+和append底层有没有区别	（1次）
26. 怎么优化才能既不用加锁又能提高并发访问的效率？（1次）
27. 如何控制这个程序使其占用的内存不能超过6G呢？	（1次）
28. 为什么要引入双亲委派机制	（1次）
29. JVM的启动参数有哪些？在jvm上运行一段java程序输出一条语句，它是怎么输出到屏幕上的？	（1次）
30. 对于一个容器，比如set，你想实现自定义排序规则的话怎么做	（1次）
31. CAS底层实现、CAS造成的问题	（1次）
32. UTF-8和utf8mb4的区别	（1次）
33. Java为什么生成字节码	（1次）
34. 知道const吗？详细说说	（1次）
35. String、StringBuffer、StringBuilder三者的区别？	（1次）
36. hashset容器下的分类及各自特点？	（1次）
37. 讲一讲你对hashcode和equal这两个函数的认识	（1次）
38. comparator排序	（1次）
39. 强引用、软引用、弱引用、虚引用？	（1次）
- **强引用**： 被强引用关联的对象不会被回收。使用 new 一个新对象的方式来创建强引用。
- **软引用**： 被软引用关联的对象只有在内存不够的情况下才会被回收。使用 SoftReference 类来创建软引用。
- **弱引用**： 被弱引用关联的对象一定会被回收，也就是说它只能存活到下一次垃圾回收发生之前。使用 WeakReference 类来创建弱引用。
- **虚引用**： 一个对象是否有虚引用的存在，不会对其生存时间造成影响。设置虚引用的唯一目的是**能在这个对象被回收时收到一个系统通知**。
40. **Java的线程状态：**
- New：新创建的线程，尚未执行；
- Runnable：运行中的线程，正在执行run()方法的Java代码；
- Blocked：运行中的线程，因为某些操作被阻塞而挂起；
- Waiting：进入该状态的线程需要等待其他线程做出一些特定动作（通知或中断）。
- Timed Waiting：该状态不同于Waiting，它可以在指定的时间后自行返回。
- Terminated：线程已终止，因为run()方法执行完毕。

	**Thread.sleep和Object.wait()的区别：**
	- sleep来自Thread类，和wait来自Object类。
	- sleep方法没有释放锁，而wait方法释放了锁：sleep不出让系统资源；wait是进入线程等待池等待，出让系统资源，其他线程可以占用CPU。
	- Thread.Sleep(0)的作用是“触发操作系统立刻重新进行一次CPU竞争”。Object.wait()一般不指定时间。
	- wait，notify和notifyAll只能在同步控制方法或者同步控制块里面使用，要等待其他线程调用notify/notifyAll唤醒等待池中的所有线程，才会进入就绪队列等待OS分配系统资源。而sleep可以在任何地方使用。
41. **Integer和int的区别？**	（1次）
1、Integer是int的包装类，int则是java的一种基本数据类型 2、Integer变量必须实例化后才能使用，而int变量不需要 3、Integer实际是对象的引用，当new一个Integer时，实际上是生成一个指针指向此对象；而int则是直接存储数据值 4、Integer的默认值是null，int的默认值是0
[int和Integer的区别](https://www.cnblogs.com/guodongdidi/p/6953217.html)
42. 继承和组合的区别以及优缺点？	（1次）
- **继承：** 继承就是子类继承父类的特征和行为，使得子类对象（实例）具有父类的实例域和方法。**优点**：支持扩展，通过继承父类实现，但会使系统结构较复杂；易于修改被复用的代码。**缺点：** 代码白盒复用，父类的实现细节暴露给子类，破坏了封装性；子类缺乏独立性，依赖于父类，耦合度较高；当父类的实现代码修改时，可能使得子类也不得不修改，增加维护难度。
- **组合：** 组合是通过对现有对象进行拼装即组合产生新的具有更复杂的功能。**优点：** 代码黑盒复用，被包括的对象内部实现细节对外不可见，封装性好；整体类与局部类之间松耦合，相互独立。**缺点：** 创建整体类对象时，需要创建所有局部类对象。导致系统对象很多。
[继承和组合的区别](https://blog.csdn.net/zymx14/article/details/79605926)
43. 原子类保证原子操作的原理？	（1次）
原子类主要利用 **CAS + volatile 和 native 方法**来保证原子操作，从而避免 synchronized 的高开销，执行效率大为提升。
44. finalize()是Object中的方法，当垃圾回收器将要回收对象所占内存之前被调用，即当一个对象被虚拟机宣告死亡时会先调用它finalize()方法，让此对象处理它生前的最后事情。	（1次）
45. Java作用域有哪些？	（1次）
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200504174051114.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3h5eF9IRlVU,size_16,color_FFFFFF,t_70)


## 计算机网络知识
1. TCP四次挥手为什么是四次，三次握手 为什么不是两次（4次）
2.  UDP/TCP区别，TCP如何可靠传输，拥塞控制如何实现、拥塞窗口和滑动窗口的使用	（4次）
3. TCP三次握手和四次挥手的详细过程	（3次）
4. http协议，http报头	（2次）[HTTP协议超详细解析](https://www.cnblogs.com/an-wen/p/11180076.html)
5. 网页输入一个url都发生了什么？DNS->IP->TCP->HTTP->ARP->MAC	（2次）
1、浏览器查找域名的IP地址，DNS域名解析(先递归查询、后迭代查询)。
2、根据默认端口80，通过3次握手建立TCP连接。
3、浏览器给web服务器发送一个HTTP请求
4、服务器对浏览器的请求做出响应，并把对应的HTML文本发送给浏览器。
5、关闭TCP连接。
6、浏览器解析HTML，渲染展示给用户。
[输入URL（如www.baidu.com）会发生什么](https://blog.csdn.net/ypshowm/article/details/89175907?depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-2&utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-2)
7. 视频用UDP还是TCP 为什么	（1次）
8. HTTP 1.0／1.1／2.0的区别以及http和https的区别（2次）[解答1](https://blog.csdn.net/qq_39207948/article/details/80969968)、[解答2](https://blog.csdn.net/weixin_34235371/article/details/88910200)
9. ssl层作用 证书有什么用	（1次）
10. 常见错误码(301、302、500、404、403)	（1次）
11. SYN泛洪攻击 	（1次）SYN攻击利用的是TCP的三次握手机制，攻击端利用伪造的IP地址向被攻击端发出请求，而被攻击端发出的响应 报文将永远发送不到目的地，那么被攻击端在等待关闭这个连接的过程中消耗了资源，如果有成千上万的这种连接，主机资源将被耗尽，从而达到攻击的目的。
12. 谈下你对servlet的认识？(好像重点要知道它是单例的)	（1次）
13. 七层网络协议和四层网络协议的区别	（1次）
14. 讲讲restful（面向资源，一个资源一个url，http层，四种操作）	（1次）
15. htttps 的加密方式（我说了一个ssl，因为没用过，所以也没法细说）	（1次）
SSL协议握手过程：
- 客户端给出协议版本号、一个客户端生成的随机数（Client random），以及客户端支持的加密方法。
- 服务端确认双方使用的加密方法，并给出数字证书、以及一个服务器生成的随机数（Server random）。
- 客户端确认数字证书有效，然后生成一个新的随机数（Premaster secret），并使用数字证书中的公钥，加密这个随机数，发给服务端。
- 服务端使用自己的私钥，获取客户端发来的随机数（即Premaster secret）。
- 客户端和服务端根据约定的加密方法，使用前面的三个随机数，生成"对话密钥"（session key），用来加密接下来的整个对话过程。
参考：[图解SSL/TLS协议](http://www.ruanyifeng.com/blog/2014/09/illustration-ssl.html)
16. http端口:80？https端口:443	（1次）
17. 如何实现可靠的UDP？	（1次）简单来讲，要使用UDP来构建可靠的面向连接的数据传输，就要实现类似于TCP协议的超时重传，有序接受，应答确认，滑动窗口流量控制等机制，等于说要在传输层的上一层（或者直接在应用层）实现TCP协议的可靠数据传输机制，比如使用UDP数据包+序列号，UDP数据包+时间戳等方法，在服务器端进行应答确认机制
18. post 和get的区别？（1次）

|       |  get | post|
|:--------:|:--------| :-------------|
|   可见性   | 数据在 URL 中对所有人都是可见的。 | 数据不会显示在 URL 中。 |
|   安全性   | 与 POST 相比，GET 的安全性较差，因为所发送的数据是 URL 的一部分。 | POST 比 GET 更安全，因为参数不会被保存在浏览器历史或 web 服务器日志中。 |
|   缓存   | 能被缓存 | 不能被缓存 |
|   对数据长度的限制   | 是的。当发送数据时，GET 方法向 URL 添加数据；受浏览器对URL长度的限制的（IE的URL 的最大长度是 2048 个字符）。 | 受web服务器对post数据处理长度的限制，可设置为无限制 |
|   书签   | 可收藏为书签 | 不可收藏为书签 |
19. TCP粘包原因？（1次）答：1、发送端需要等缓冲区满才发送出去，造成粘包（发送数据时间间隔很短，数据了很小，会合到一起，产生粘包）2、接收方不及时接收缓冲区的包，造成多个包接收（客户端发送了一段数据，服务端只收了一小部分，服务端下次再收的时候还是从缓冲区拿上次遗留的数据，产生粘包） 。<font color=red>总结：接收方不知道该接收多大的数据才算接收完毕，造成粘包。</font>
粘包解决方案？答：1、分两次通讯分别传递内容大小和内容。2、一次通讯直接传递内容大小和内容，把报头做成字典，字典里包含将要发送的真实数据的详细信息，然后json序列化。
20. **session和cookie的区别？**
	1、**存储位置不同：** cookie数据存放在客户的浏览器上，session数据放在服务器上。
	2、**存储容量不同：** 单个cookie保存的数据不能超过4K，很多浏览器都限制一个站点最多保存20个cookie。对于Session并没有上限，但出于对服务器端的性能考虑，Session内不要存放过多的东西，并设置session删除机制。
	3、**存取方式不同：** cookie中只能保管ASCII字符串，并需要通过编码方式存储为Unicode字符或者二进制数据。session中能够存储任何类型的数据，包括且不限于string，integer，list，map等。
	4、**隐私策略不同：** Cookie对客服端是可见的，别有用心的人可以分析放在本地的Cookie上面并进行Cookie欺骗，所以它是不安全的。
	5、**有效期不同：** 开发可以通过设置Cookie的属性，达到Cookie长期有效的效果，只要关闭窗口该Session就会失效，因为假如设置Session的超过时间过长，服务器累计的Session就会越多，越容易导致内存溢出。
	6、**跨域支持上的不同：** Cookie 支持跨域名访问，例如，将 domain 属性设置为“.biaodianfu.com”，则以“.biaodianfu.com”为后缀的一切域名均能够访问该Cookie。Session则不会支持跨域名访问。Session仅在它所在的域名内有效。
	[Cookie和Session区别](https://blog.csdn.net/test_bat/article/details/93196938)
21. 如果客户端禁用了cookie，怎么使用session？	（1次）
（1）**使用URL重写**，将sessionid附在URL的后面，传给服务器。
（2）**表单隐藏字段**，服务器会自动修改表单，添加一个隐藏字段，以便在表单提交时能够把sessionid传回服务器。
22. 服务端出现大量TIME_OUT？解决？	（1次）
	答：在**高并发短连接**的TCP服务器上，当服务器处理完请求后立刻主动正常关闭连接。这个场景下会出现大量socket处于TIME_WAIT状态。短连接表示“业务处理+传输数据的时间 远远小于 TIMEWAIT超时的时间”的连接。解决：编辑内核文件/etc/sysctl.conf **打开系统的TIMEWAIT重用和快速回收。**
23. session默认失效时间？（1次） 30min
## MySQL、数据库
1. 存储的数据结构、索引类型、索引底层、 B+树与B-树的区别 、索引是怎么提高效率的、B+ 树和B树、红黑树，为什么MySql索引使用B+树、B+树作为索引有什么缺点、“like”查询在什么时候能够用上索引（7次）
2. Innodb、Myisam 区别、引擎选型，不同引擎的索引类型、存储日志，用什么存储引擎比较合适	（4次）
3. 常见的锁（表锁、行锁）及功能	，锁的粒度划分以及原理、性能、使用时注意事项。（2次）
4. 什么是慢查询？怎么解决？	（1次）
5. 主键索引和普通索引	（1次）
6. 存储日志文件（一般来说日志文件只有在出错的时候才会去看它，平时基本上不会去查）应该如何设计索引的数据结构？	（1次）
7. ACID	原子性、一致性、隔离性、可持续性	（1次）
8. mysql提高读取效率得方式? 索引，分表分区	（1次）
9. 什么是事务	（1次）	事务是**数据库操作的最小工作单元**，是作为单个逻辑工作单元执行的一系列操作；这些操作作为一个整体一起向系统提交，要么都执行、要么都不执行；事务是一组不可再分割的操作集合（工作逻辑单元）
10. 索引io是什么io模型，为什么？	（1次）
11. MySQL的主从复制原理，如何实现 	（1次）
12. 数据库中一行记录大小10K，一个表只有主键索引，可以存多少条数据	（1次）
13. 数据库一个表中最多可以插入多少数据	（1次）
14. 数据库中存储密码是怎么存的（MD5加密），还知道其他加密方式吗？	一个6位的密码，使用MD5加密，破解需要多久：说的是这样的，因为6位的密码排列组合有一定的种数，然后每一种通过hash函数去破解，需要多久	（1次）
15. 悲观锁，乐观锁在数据库中如何实现的	（1次）
16. sql注入攻击了解吗？讲讲你对它的理解？那如何解决？	（1次）
17. 谈谈你对数据库中join操作的理解	（1次）
18. 你了解数据库中可重复读隔离级别如何实现吗？	（1次）(MVCC)	<font color=red>MVCC其实在MySQL可重复读的隔离级别中并不是完全解决了幻读的问题</font>，而是<font color=red>**解决了读数据情况下的幻读问题**</font>。而对于<font color=red>**修改的操作依旧存在幻读问题**</font>。
<font color=red>**next-key锁（行锁+间隙锁）可以解决幻读。**</font>当索引为唯一索引时，next-key锁自动优化为行锁，行锁锁定的是索引，而不是行数据，也就是说锁定的是key。
[透彻解读mysql的可重复读、幻读及实现原理](https://blog.csdn.net/sanyuesan0000/article/details/90235335)
[Next-Key Lock 浅谈](https://www.cnblogs.com/zhoujinyi/p/3435982.html)
19. 快照读，当前读？	（1次）读取历史数据的方式，我们叫它**快照读** (snapshot read)，当执行select操作时innodb默认会执行快照读；而读取数据库最新版本数据的方式，叫**当前读** (current read)，对于会对数据修改的操作(update、insert、delete)都是采用当前读的模式
20. JDBC	（1次）
21. 数据库查询很慢，如何优化，问到具体查询索引性能的sql语句	（1次）
22. 回表？	（1次）
23. 估算一下，一张表字段有多少字节？	**一行大小最大为65535字节**（1次）
24. char和varchar的区别和细节？（1次）[CHAR和VARCHAR有哪些区别？](https://learn.blog.csdn.net/article/details/103341778)
25. join用法：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200331140639229.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3h5eF9IRlVU,size_16,color_FFFFFF,t_70)
26. 删除表中除主键外完全重复的数据，保留id最小的那一条数据	（1次）

```sql
DELETE t1
from t_a t1, t_a t2
WHERE t1.mail=t2.mail and t1.name=t2.name and t1.id>t2.id;
```
27. 索引失效的原因？	（1次）
1、有or必须所有条件都有索引才会用索引; 2、复合索引未用左列字段; 3、like以%开头; 4、需要类型转换; 5、where中索引列有运算; 6、where中索引列使用了函数; 7、如果mysql觉得全表扫描更快时（数据少）;
28. Mysql分页查询实现：
使用limit 参数1，参数2; 参数1为开始查询的数据行（从0开始），参数2为需要的行数。
```sql
查询第10条到第20条的数据的sql是： select * from table limit 10,10;  
->对应我们的需求就是查询第二页的数据：select * from table limit (2-1)*10,10;
```
29. **Innodb每个数据页的大小为16KB**，每个Page使用一个32位（一位表示的就是0或1）的int值来表示，正好对应<font color=red>**Innodb最大64TB的存储容量**</font>(16kb * 2^32=64tib)
30. redo log、undo log	(1次)
	[MySQL日志系统：redo log、binlog、undo log 区别与作用](https://blog.csdn.net/u010002184/article/details/88526708)

31. [常用MySQL语句](https://www.runoob.com/sql/sql-having.html)
## 常用数据结构
1. 红黑树、红黑树的查找时间复杂度	（2次）
2. 和高度平衡二叉树	（1次）
3. 哈希冲突解决办法	（1次）
4. 所有排序的时间复杂度、空间复杂度、最坏复杂度	（1次）
5. 二叉排序树，二叉平衡树，树的广度优先遍历	（1次）
6. B+树、B树的插入删除（1次）[B树和B+树的插入、删除图文详解](https://www.cnblogs.com/nullzx/p/8729425.html)
7. **堆跟栈的区别**	、堆的实现原理？（3次）
堆是完全二叉树结构，完全二叉树除了最底层，每一层都是满的，最后一层节点集中在左侧。最大堆：孩子节点要小于等于父亲节点。最小堆：孩子节点要大于等于其父亲节点。**PriorityQueue** 是基于二叉堆原理的优先队列，队列用动态数组实现。它是非阻塞的、非线程安全的；
堆的知识点：1、<font color=red>可用数组来存储</font>：其中某个节点为$i$，则其父节点为$(i-1)/2$，其左子节点为$i*2+1$，右子节点为$i*2+2$；2、（最大堆）<font color=red>插入堆</font>：先插入到数组最后一个位置，然后不断上浮，不断跟其父节点比较，若比父节点大，则交换，若小于等于则停止操作。3、（最大堆）<font color=red>取出堆顶元素时</font>：先去出数组第0个元素，再将数组最后一个元素复制到第0个位置上，删除最后一个元素，将新的第0个元素下沉，即比较其左右子节点，哪个较大，并且比自己的值还大，则跟哪个子节点进行交换。4、<font color=red>数组堆化</font>：找到最后一个非叶节点，即$(arrSize-1)/2$，然后开始逐个对非叶子节点进行下沉操作。
[堆的实现原理](https://www.cnblogs.com/hello-shf/p/11393655.html)
## 操作系统
1. **线程**和**进程**和**协程**的区别、既然线程会共享进程的程序和数据，那么如何保证各个线程之间互不干扰相互独立呢	？ 进程是操作系统资源分配的最小单位；线程为轻量级进程，是操作系统调度（CPU调度）执行的最小单位；协程是一种用户态的轻量级线程，一个线程可以拥有多个协程，协程调用完全由程序所控制，协程能保留上一次调用时的状态，每次过程重入时，就相当于进入上一次调用的状态（3次）
2. 简述一下什么是死锁，死锁出现的原因、死锁产生的必要条件。[死锁详解](https://blog.csdn.net/wljliujuan/article/details/79614019)	（2次）
死锁的必要条件：（1）**互斥条件**：指进程对所分配到的资源进行排它性使用，即在一段时间内某资源只由一个进程占用。如果此时还有其它进程请求资源，则请求者只能等待，直至占有资源的进程用毕释放。
（2）**请求和保持条件**：指进程已经保持至少一个资源，但又提出了新的资源请求，而该资源已被其它进程占有，此时请求进程阻塞，但又对自己已获得的其它资源保持不放。
（3）**不剥夺条件**：指进程已获得的资源，在未使用完之前，不能被剥夺，只能在使用完时由自己释放。
（4）**环路等待条件**：指在发生死锁时，必然存在一个进程——资源的环形链，即进程集合{P0，P1，P2，···，Pn}中的P0正在等待一个P1占用的资源；P1正在等待P2占用的资源，……，Pn正在等待已被P0占用的资源。
3. **进程间通信的方式**：
    - **无名管道**：半双工的、只能用于具有**亲缘关系的进程之间的通信**（也就是父子进程或者兄弟进程之间）、只存在于**内存中的文件**。
    - **命名管道**：遵循先进先出(first in first out)。命名管道以**磁盘文件**的方式存在，可以实现本机**任意两个进程通信**。
    - **消息队列** ：消息的链接表，存放在**内核中**，消息队列可以实现消息的随机查询,消息不一定要以先进先出的次序读取,也可以按消息的类型读取。
    - **信号量** ：信号量是一个计数器，用于多进程对共享数据的访问，信号量的意图在于进程间同步。这种通信方式主要用于解决与同步相关的问题并避免竞争条件。
    - **共享内存**： 使得多个进程可以访问同一块内存空间，不同进程可以及时看到对方进程中对共享内存中数据的更新。
    - **套接字**： 此方法主要用于在客户端和服务器之间通过网络进行通信。套接字是支持 TCP/IP 的网络通信的基本操作单元，可以看做是不同主机之间的进程进行双向通信的端点
[进程间通信的方式](https://www.cnblogs.com/CheeseZH/p/5264465.html)（2次）
4. 内存碎片	（1次）
5. 线程安全	（1次）
6. 操作系统大小端字节序	（1次）
7. 进程调度算法	[进程调度算法详解](https://blog.csdn.net/qq_35642036/article/details/82809812)（1次）
8. 僵尸进程和孤儿进程？	（1次）
<font color=red>**僵尸进程：**</font>即子进程先于父进程退出后，子进程的PCB需要其父进程释放，但是父进程并没有释放子进程的PCB（父进程是死循环）。
<font color=red>**孤儿进程：**</font>一个父进程退出，而它的一个或多个子进程还在运行，那么那些子进程将成为孤儿进程。孤儿进程将被init进程(进程号为1)所收养，并由init进程对它们完成状态收集工作。**所以孤儿进程实际上是不占用资源的，因为它终究是被系统回收了。不会像僵尸进程那样占用ID,损害运行系统。**
## Spring
1. AOP实现原理	（3次）**动态代理**
2. SpringMVC和Spring之间的关系？	（3次）
3. 动态代理实现原理	（1次）
4. springboot中的注解？对于注解是怎么理解的？	（1次）
5. Autowired底层原理？什么时候做Autowired的注入？
**在服务器启动的时候**，会加载配置文件，配置文件中有包扫描器和注解驱动，系统会根据配置进行**扫描注入** bean。XML文件中`<context:component-scan base-package=“com.zxt”/>` 用来定义扫描的包；**@Service**用来声明一个类是一个bean，该类在bean中的id是类名且首字母小写。**@Scope**("自定义名")用来自定义bean的名称。
6. Autowired下边有多个实现类，可不可以编译？	
**不可以编译**，除非：1、变量名用userService1,userService2，而不是userService。通常情况下@Autowired是通过byType的方法注入的，可是在多个实现类的时候，byType的方式不再是唯一，而需要通过byName的方式来注入，而这个name默认就是根据变量名来的。2、通过@Qualifier注解来指明使用哪一个实现类，实际上也是通过byName的方式实现。@Autowired默认按照byType方式进行bean匹配，@Resource默认按照byName方式进行bean匹配（1次）
[@Autowired、@Resource、@Inject和@Service介绍](https://www.jianshu.com/p/931cdba58cf7)
7. SpringMVC流程：
1、  用户发送请求至<font color=red>**前端控制器** </font>DispatcherServlet。
2、  DispatcherServlet收到请求调用HandlerMapping<font color=red>**处理器映射器**</font>。
3、  处理器映射器找到具体的处理器(可以根据xml配置、注解进行查找)，生成处理器对象及处理器拦截器(如果有则生成)一并返回给DispatcherServlet。
4、  DispatcherServlet调用HandlerAdapter<font color=red>**处理器适配器**</font>。
5、  HandlerAdapter经过适配调用具体的<font color=red>**处理器(Controller**</font>，也叫后端控制器)。
6、  Controller执行完成返回<font color=red>**ModelAndView**</font>。
7、  HandlerAdapter将controller执行结果ModelAndView返回给DispatcherServlet。
8、  DispatcherServlet将ModelAndView传给ViewReslover<font color=red>**视图解析器**</font>。
9、  ViewReslover解析后返回<font color=red>**具体View**</font>。
10、DispatcherServlet根据View进行渲染视图（即将模型数据填充至视图中）。
11、 DispatcherServlet响应用户。
8. Spring 中的 IoC 的实现原理？	（1次）**工厂模式加反射机制。**
9. **Bean的生命周期**？	（1次）
	- Spring对bean进行实例化
	- Spring为bean的属性设置值和对其他bean的引用
	- 如果bean实现了BeanNameAware接口，Spring将bean的ID传递给setBean-Name()方法；
	- 如果bean实现了BeanFactoryAware接口，Spring将调用setBeanFactory()方法，将BeanFactory容器实例传入；
	- 如果bean实现了ApplicationContextAware接口，Spring将调用setApplicationContext()方法，将bean所在的应用上下文的引用传入进来；
	- 如果bean实现了BeanPostProcessor接口，Spring将调用它们的postProcessBeforeInitialization()方法；
	- bean初始化
	- 如果bean实现了BeanPostProcessor接口，Spring将调用它们的post-ProcessAfterInitialization()方法；
	- 此时，bean已经准备就绪，可以被应用程序使用了，它们将一直驻留在应用上下文中，直到该应用上下文被销毁；
	- 销毁bean，如果bean实现了DisposableBean接口，Spring将调用它的destroy()接口方法。
10. **Spring Ioc容器初始化过程**？	（1次）
1、<font color=red>**Resource 定位**</font>。我们一般用外部资源来描述 Bean 对象，所以在初始化 IOC 容器的第一步就是需要定位这个外部资源。
2、<font color=red>**BeanDefinition 的载入和解析**</font>。装载就是 BeanDefinition 的载入。BeanDefinitionReader 读取、解析 Resource 资源，也就是将用户定义的 Bean 表示成 IOC 容器的内部数据结构：BeanDefinition。
3、<font color=red>**BeanDefinition 注册**</font>。**将BeanDefinition 注入到一个 HashMap 容器中**，IOC 容器就是通过这个 HashMap 来维护这些 BeanDefinition 的，**key为BeanName，value为BeanDefinition**。在这里需要注意的一点是这个过程并没有完成依赖注入，依赖注册是发生在应用第一次调用 getBean() 向容器索要 Bean 时。当然我们可以通过设置预处理，即对某个 Bean 设置 lazyinit 属性，那么这个 Bean 的依赖注入就会在容器初始化的时候完成。
[Spring 之 IOC 初始化总结](https://my.oschina.net/u/3847203/blog/2251399?from=groupmessage&isappinstalled=0)

## 设计模式
1. <font color=red>**单例模式：**</font> 确保一个类只有一个实例，并提供该实例的全局访问点。可以直接访问，不需要实例化该类的对象。**解决**：一个全局使用的类频繁地创建与销毁。**使用场景**：1、要求生产唯一序列号。2、WEB 中的计数器，不用每次刷新都在数据库里加一次，用单例先缓存起来。3、创建的一个对象需要消耗的资源过多，比如 I/O 与数据库的连接等。
- **懒汉式，线程不安全**：如果多个线程能够同时进入 `if (uniqueInstance == null) `，并且此时 instance 为 null，那么会有多个线程执行 `instance = new Singleton();` 语句，这将导致实例化多次 instance。

```java
public class Singleton {  
    private static Singleton instance;  
    private Singleton (){}  
    public static Singleton getInstance() {  
	    if (instance == null) {  
	        instance = new Singleton();  
	    }  
	    return instance;  
    }  
}
```
- **懒汉式，线程安全**：只需要对 getInstance() 方法加锁，那么在一个时间点只能有一个线程能够进入该方法，从而避免了实例化多次 instance。**优点**：第一次调用才初始化，避免内存浪费。**缺点**：必须加锁 synchronized 才能保证单例，但加锁会影响效率。

```java
public class Singleton {  
    private static Singleton instance;  
    private Singleton() {}  
    public static synchronized Singleton getInstance() {  
	    if (instance == null) {  
	        instance = new Singleton();  
	    }  
	    return instance;  
    }  
}
```
- **饿汉式，线程安全：** **优点：** 没有加锁，执行效率会提高。**缺点：** 类加载时就初始化，浪费内存。

```java
public class Singleton {  
    private static Singleton instance = new Singleton();  
    private Singleton() {}  
    public static Singleton getInstance() {  
    	return instance;  
    }  
}
```
- **双重校验锁，线程安全：** 这种方式采用双锁机制，安全且在多线程情况下能保持高性能。双重校验锁先判断 instance 是否已经被实例化，如果没有被实例化，那么才对实例化语句进行加锁。
```java
public class Singleton {
    private volatile static Singleton instance;
    private Singleton() {}
    public static Singleton getInstance() {
        if (instance == null) {
            synchronized (Singleton.class) {
                if (instance == null) {
                    instance = new Singleton();
                }
            }
        }
        return instance;
    }
}
```
- **静态内部类，线程安全：** 当 Singleton 类被加载时，静态内部类 SingletonHolder 没有被加载进内存。只有当调用 getInstance() 方法从而触发 SingletonHolder.INSTANCE 时 SingletonHolder 才会被加载，此时初始化 INSTANCE 实例，并且 JVM 能确保 INSTANCE 只被实例化一次。这种方式不仅具有**延迟初始化**的好处，而且由 JVM 提供了对**线程安全**的支持。

```java
public class Singleton {  
    private static class SingletonHolder {  
    	private static final Singleton INSTANCE = new Singleton();  
    }  
    private Singleton(){}  
    public static final Singleton getInstance() {  
    	return SingletonHolder.INSTANCE;  
    }  
}
```
- **枚举，线程安全：** 

```java
public enum Singleton {  
    INSTANCE;  
    public void whateverMethod() {  
    }  
}
```
参考：[单例模式|菜鸟教程](https://www.runoob.com/design-pattern/singleton-pattern.html)

2. <font color=red>**工厂模式：**</font>

```java
public interface People {
    public void eat();
    public void run();
```

```java
public class Xiaoming implements People{
    @Override
    public void eat() {
        System.out.println("小明吃饭");
    }
    @Override
    public void run() {
        System.out.println("小明跑步");
    }
}
public class Xiaohong implements People{
    @Override
    public void eat() {
        System.out.println("小红吃饭");
    }
    @Override
    public void run() {
        System.out.println("小红跑步");
    }
}
```

```java
public class PeopleFactory {
    public People getPeople(String name){
        if(name.equals("Xiaoming")){
            return new Xiaoming();
        }else if(name.equals("Xiaohong")){
            return new Xiaohong();
        }
        return null;
    }
}
public class Main {
	public void testSign(){
        PeopleFactory peopleFactory = new PeopleFactory();
        People people = peopleFactory.getPeople("Xiaohong");
    }
}
```

3. <font color=red>**策略模式**</font>与工厂模式的区别？	（1次）
   - 工厂是创建型模式,它的作用就是创建对象；策略是行为型模式,它的作用是让一个对象在许多行为中选择一种行为。
   - 工厂模式关注对象创建，策略模式关注行为的封装。
   -  工厂模式中只管生产实例，具体怎么使用工厂实例由调用方决定，策略模式是将生成实例的使用策略放在策略类中配置后才提供调用方使用。 工厂模式调用方可以直接调用工厂实例的方法属性等，策略模式不能直接调用实例的方法属性，需要在策略类中封装策略后调用。
   [个人理解简单工厂模式和策略模式的区别](https://blog.csdn.net/lmx125254/article/details/86625960)
## Redis
1. 怎么用redis？都在什么场景下用了？Redis存储的基本数据类型、底层数据结构（3次）
2. 删除策略	（1次）
3. redis持久化？	（1次）
4. 详细讲一下aof怎么做记录备份？aof和rdb的使用场景（1次）
5. 介绍一下redis的过期策略？	（1次）
6. redis的单线程模型机制（1次）
7. 讲讲redis  sorted set  （有序的set）	（1次）
8. 跳表？	（1次）
## Mybatis 
1. Mybatis一级缓存和二级缓存	（1次）
2. Mybatis的String结构如何实现的（int、embstr、row （sds动态内存））	（1次）
3. Mybatis的Set如何实现（zlist、hashtable），一个key可以存储的最大数量	（1次）
4. Mybatis工作原理	（1次）
5. 什么是MyBits三剑客？	（1次）
## RocketMq
1. 用来解决什么问题（流量削峰）	（1次）
2. 如何保证通信（底层使用的是Netty通信框架，Netty包装的NIO）（1次）
3. 为什么使用Channel（NIO相关）（1次）
4. 线程池+RocketMQ+设计模式	（1次）
## 场景题
1. 32位的机器 文件里每行有一个整型数字 找出不重复的个数(之前没看过bitmap,说了hashmap分析了一下时间空间复杂度,面试官引导我最后也只说到了bitmap没说出来怎么实现)	（1次）
2. 一个数量为1000的线程池，如何使用固定的线程去实现特定的任务。	（1次）
3. 自己实现RPC怎么做。 反射+代理+序列化+socket	（1次）
4. 注册+审核流程 多线程审核，如何实现并发控制？	（1次）
5. 如何在很多的用户ID中找出登录次数最多的那个用户。	（1次）
6. 如何设计一个微博热搜？	（1次）
7. 扫码登录如何实现，微信支付怎么实现	（1次）
8.  kmp算法	（1次）
9. 如果让你实现一个用户在另一台手机上登录微信后把他之前登录的那台手机上微信号顶掉你该怎么设计？	（1次）
10. 如果让你来实现一个对高考成绩的分类下的前100名成绩你该怎么实现？说说你的思路(我也不知道对不对 我答的是使用hash函数将不同科目的成绩分配到不同的文件然后在使用堆排序或者快排求解topk问题) 
11. 假设你这个博客项目要应对高并发的场景，你从那些方面进行优化？
（页面静态化、CDN加速、Nginx负载均衡、服务器集群、MySQL分区、集群、读写分离、Redis缓存、mq削峰限流......）	（1次）
12. 网页访问人数设计	（1次）
13. [海量场景题](https://blog.csdn.net/v_JULY_v/article/details/6279498)
## 手撕代码
1. 字符串A去掉字符串B出现字符	（1次）
2. 二叉树从根到叶节点是否等于一个值	（1次）
3. 一个二维数组，横向递增，纵向递增，求解是否某个数存在	（1次）
4. 给出N，求1~N中每一位数乘起来乘积最大，比如220最大的就是199，288最大的是288	（1次）
5. topK问题	（1次）
6. 设计一个论坛的数据库表，从范式，索引角度思考，要考虑到大量的读写需求，写完给面试官讲。	（1次）
7. 写一个死锁。	（1次）
```java
package com.jianzhi;

public class DeadLock {
    Object a = new Object();
    Object b = new Object();
    public static void main(String[] args) {
        DeadLock lock=new DeadLock();
        lock.deadLock();
    }
    public void deadLock(){
        Thread thread=new Thread(new Runnable() {
            @Override
            public void run() {
                synchronized (a){
                    System.out.println("我有笔，没有本");
                    try {
                        Thread.sleep(1000);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                    synchronized (b){
                        System.out.println("我有笔，也有本");
                    }
                }
            }
        });
        Thread thread1=new Thread(new Runnable() {
            @Override
            public void run() {
                synchronized (b){
                    System.out.println("我有本，没有笔");
                    try {
                        Thread.sleep(1000);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                    synchronized (a){
                        System.out.println("我有本，也有笔");
                    }
                }
            }
        });
        thread1.start();
        thread.start();
    }
}
```

8. 写一个OOM	（1次）

```java
    public static void main(String[] args) {
        ArrayList<int[]> list = new ArrayList<>();
        while (true) {
            list.add(new int[2000000000]);
        }
    }
```
9. 用Java实现LRU。	（1次）
用带有头节点和尾节点的双向链表和Hashmap实现，HashMap里存放的是<K k, Node node>

```java
import java.util.HashMap;
import java.util.Iterator;

public class LRU<K> implements Iterable<K> {
    private Node head;
    private Node tail;
    private HashMap<K, Node> map;
    private int maxSize;

    private class Node {

        Node pre;
        Node next;
        K k;

        public Node(K k) {
            this.k = k;
        }
    }

    public LRU(int maxSize) {
        this.maxSize = maxSize;
        this.map = new HashMap<>(maxSize * 4 / 3);
        head = new Node(null);
        tail = new Node(null);

        head.next = tail;
        tail.pre = head;
    }

    public K get(K key) {

        if (!map.containsKey(key)) {
            return null;
        }

        Node node = map.get(key);
        unlink(node);
        appendHead(node);

        return node.k;
    }

    public void put(K key) {

        if (map.containsKey(key)) {
            Node node = map.get(key);
            unlink(node);
        }

        Node node = new Node(key);
        map.put(key, node);
        appendHead(node);

        if (map.size() > maxSize) {
            Node toRemove = removeTail();
            map.remove(toRemove.k);
        }
    }

    private void unlink(Node node) {

        Node pre = node.pre;
        Node next = node.next;

        pre.next = next;
        next.pre = pre;

        node.pre = null;
        node.next = null;
    }
    private void appendHead(Node node) {
        Node next = head.next;
        node.next = next;
        next.pre = node;
        node.pre = head;
        head.next = node;
    }
    private Node removeTail() {

        Node node = tail.pre;

        Node pre = node.pre;
        tail.pre = pre;
        pre.next = tail;

        node.pre = null;
        node.next = null;

        return node;
    }
    @Override
    public Iterator<K> iterator() {

        return new Iterator<K>() {
            private Node cur = head.next;

            @Override
            public boolean hasNext() {
                return cur != tail;
            }

            @Override
            public K next() {
                Node node = cur;
                cur = cur.next;
                return node.k;
            }
        };
    }
    public static void main(String[] args) {
        LRU<String> lru = new LRU<>(3);
        lru.put("one");
        lru.put("two");
        lru.put("three");
        lru.put("two");
        lru.put("four");
    }
}
```

 
11. 最长公共子串与最长公共子序列	（1次）  [最长公共子串与最长公共子序列](https://www.cnblogs.com/fengziwei/p/7827959.html)

12. 撕一个线程安全的单例懒汉模式	（1次）
13. 层次遍历二叉树	（1次）
14. 链表是否相交	（1次）
15. 某字符串的所有排列组合。	（1次）
16. 两个线程交替执行，一个输出偶数，一个输出奇数	(1次)
[方法一](https://blog.csdn.net/qq_21106239/article/details/74937500)
## Linux
1. 内存的使用情况如何实现定时任务？	（1次）
2. ps -ef | grep -i redis
## Tomcat
1. Tomcat怎么切换IO模型？	（1次）
2. Tomcat启动模型？	（1次）
## 其他技能相关
1. docker底层原理	（1次）
2. maven熟吗，如果遇到版本不匹配的问题怎么解决？	（1次）
3. Hadoop，MapReduce，HDFS	（1次）
4. 什么是跨域问题？（1次）
