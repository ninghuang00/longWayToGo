### java虚拟机学习笔记

qingfeng82面试官的建议:
1. 识别和定义问题的能力
2. 解决问题的能力
3. 思辨能力
4. 学习能力
5. 体系化的思考和总结能力
6. 自我认知能力
7. 认知扩展的能力

* 最好结合实际场景
* 



* JVM相关
	class编译,加载,执行原理过程,内存管理,GC,



* StackOverflow和OutOfMemory的区别
	1. StackOverflow
		启动一个新的线程的时候,虚拟机会分配一个栈,java栈以栈帧的方式保存线程运行状态,然后线程调用方法的时候,虚拟机就会压入新的栈帧,只要方法没有返回,栈帧就会存在,当栈帧太多就会导致溢出.
	2. OutOfMemory
		1. 栈内存溢出
			就是启动的线程太多,没有足够的空间分配java栈了.一个线程的java栈大小由-Xss参数设置
		2. 堆内存溢出
			java堆中存放对象实例的空间不足,通过-Xmx设置最大值.
		3. 方法区和运行时常量池溢出
			-XX:permSize=10M -XX:MaxPermSize=10M
		4. 本机直接内存溢出
			-XX:MaxDirectMemorySize

* 判断对象是否存活的方法
	1. 引用计数法
		当对象被引用的时候计数加一,不用的时候减一,缺点是无法解决对象之间相互循环引用的问题,比如两个对象中的成员变量相互引用的时候
	2. 可达性分析算法
		当一个对象到GC Roots没有任何引用链相连的时候,就是该对象不可用
		GC Roots包括:
			1. 虚拟机栈(栈帧中的本地变量表)中的引用对象
			2. 方法区中的类的静态引用属性
			3. 方法区中的常量引用的对象
			4. 本地方法栈中JNI(即一般说的Native方法)引用的对象

* 引用的分类
	1. 强引用
		类似`Object obj = new Object()`这类,只要强引用在,永远不会被回收
	2. 软引用
		有用但是非必须的,只有内存不够的时候会被回收,如果回收之后还是不够才会抛出内存溢出异常
	3. 弱引用
		只能存活到下一次垃圾回收
	4. 虚引用
		唯一目的就是在一个关联了虚引用的对象被回收的时候收到系统通知

* 回收方法区(HotSpot中的永久带)
	回收性价比比较低,主要回收弃用常量和无用的类
	1. 判断弃用常量
		比如字面量"abc",当没有String对象叫"abc"的时候
	2. 判断无用的类(同时满足以下三个条件)
		1. 当java堆中没有该类的实例
		2. 加载该类的ClassLoader被回收
		3. 该类的java.lang.Class对象没有在任何地方被引用,也就是无法在任何地方通过反射访问该类的方法

* 垃圾回收算法
	1. 标记清除算法
		不足:标记和清除的效率不高,然后就是清除之后会产生大量不连续的内存碎片
	2. 复制算法
		直接将内存一分为二,当其中一块内存用完了,将其中还存活的对象复制到另一块内存上,比较浪费空间.
	3. 复制收集算法(回收新生代)
		将内存分为一块Eden空间和两块Survivor空间,8:1:1,回收时将Eden和Survivor中还存活的对象复制到另一块Survivor空间中,如果Survivor空间内存不够,就需要用到老年代
	4. 标记整理算法(回收老年代)
	5. 分代收集算法
		一般将java堆分成新生代和老年代,再使用合适的回收算法

* 垃圾收集器(书上列举了七种)
	1. Serial收集器
		单线程收集器,垃圾回收时必须暂停其他所有的工作线程
	2. ParNew收集器

* 内存模型
	根据某种特定的操作协议,对特定的内存或者高速缓存进行读写访问的过程抽象
	8中原子操作:
		* lock:作用于主内存的变量,将一个变量标记为一条线程独占
		* unlock:主内存,施放线程独占的变量
		* read:主内存,将变量值从主内存传输到线程的工作内存,之后要使用load操作
		* load:作用于工作内存,将从主内存中read得到的变量值放入工作内存的变量副本中
		* use:工作内存,将工作内存中一个变量的值传递给执行引擎
		* assign:工作内存,将从执行引擎接收到的值赋值给工作内存中的变量
		* store:工作内存,将工作内存中的变量值从工作内存传送到主内存,供之后的write操作使用 
		* write:主内存,包store操作从工作内存中取得的值放入主内存的变量中

* volatile的使用(有两种语义)
	1. 能够保证修饰的变量对所有线程的可见性,当一条线层修改了了这个变量,其他线程可以马上得知.线程A修改了普通变量的值之后需要先回写到主内存之后,新的变量值才能被线程B读取到.但是并不能保证基于volatile变量的运算在并发下是安全的.
		使用volatile进行安全的并发运算的规则
		1. 运算结果不依赖变量的当前值,或者确保只有单一线程修改变量的值
		2. 变量不需要与其他状态变量共同参与不变约束
		比如多线程的累加是不安全的,因为累加不是原子操作;
		比如布尔值的判断可以是安全的,多个线程判断一个flag是true还是false是可以线程安全的.
	2. 禁止指令重排序优化,普通变量不能保证普通的赋值操作和代码中的顺序是一致的.
		比如说线程a进行初始化工作,初始化完之后将初始化完成的标志置为true,线程b中根据初始化完成标志开始其他工作,如果线程a,b并发,这个时候就有可能出错,因为指令重排序优化的关系,线程a中的初始化标志的指令可能会提前执行,
* 线程安全的实现方法
	1. 互斥同步(阻塞同步)
	互斥同步对性能最大的影响就是对阻塞的实现,挂起和恢复线程的操作需要切换到内核态中完成
		1. synchronized
		关键是要分清楚是给类加锁还是给对象实例加锁
		2. java.util.concurrent.ReentrantLock(注意锁的声明要放在需要同步的方法的外面)
		和synchronized基本用法类似,使用lock()和unlock()方法配合try/finally语句块来完成,但是有以下三个特色:
			1. 等待可中断
			2. 可实现公平锁
			3. 锁可以绑定多个条件
	2. 非阻塞同步
	不断重试
	3. 无同步方案
		1. 可重入代码
		可以在代码执行的任何时刻中断去执行其他代码(包括递归调用自己),并且在回来的时候原来的程序不会出现任何错误
		2. 线程本地存储
		将共享数据的代码保证在同一个线程中完成

* 锁优化
	1. 自旋锁和自适应自旋锁
	就是当后一个线程请求锁的时候,不要一请求不到就马上挂起,而是等待一下(前提是物理机器有一个以上的处理器),但不放弃处理器的执行时间,看看持有锁的线程是否会很快释放锁,为了让线程等待,只需要让线程执行一个忙循环(自旋)
	2. 锁消除(虚拟机优化) 
	虚拟机即时编译器在运行的时候会对一些代码上要求同步但是实际上不会发生数据共享竞争的锁进行消除,根据逃逸分析的数据支持,如果堆上的数据不会逃逸出去从而被其他线程访问到,就可以把他们当做是栈上的数据对待,认为他们是线程私有的,同步加锁自然无需进行,比如说StringBuilder.append()方法.
	3. 锁粗化
	就是一串连续的零碎操作都对同一个对象加锁,那么就会将加锁同步的范围扩大
	4. 轻量级锁
	5. 偏向锁
* 线程安全是相对的(深入理解Java虚拟机P390)
比如说Vector是一个线程安全的集合容器,因为它的add(),get(),size(),remove()方法都是被synchronized修饰过的,但是并不意味着调用这些方法就永远不需要额外的同步手段了,一下代码就有可能会出现ArrayIndexOutOfBoundsException.
```
public class VectorDemo {
    private static Vector<Integer> vector = new Vector();
    public static void main(String[] args) {
        while (true) {
            for (int i = 0; i<10;i++) {
                vector.add(i);
            }
            Thread removeTask = new Thread(new Runnable() {
                public void run() {
                    for(int i = 0;i < 10; i ++) {
                        vector.remove(i);
                    }
                }
            });
            Thread printTask = new Thread(new Runnable() {
                public void run() {
                    for(int i = 0;i < 10; i ++) {
                        System.out.println(vector.get(i));
                    }
                }
            });
            removeTask.start();
            printTask.start();
            //防止线程过多
            while (Thread.activeCount() > 20) ;
        }
    }
}
```








