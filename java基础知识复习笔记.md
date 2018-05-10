### java基础知识复习笔记
* 细节
```
<!-- 代码段 -->
int i = 0;
i = i++;
<!-- 结果i还是0  -->

/*代码段*/
int i = 0;
i = ++ i;
<!-- 结果i是1 -->

<!-- 基本数值类型转换问题 -->
int a = 9;
int b = 4;
double c = a/b;
<!-- 结果是2.0 -->

<!-- 控制sout的小数输出位数 -->
sout("%.2f",doubleNum);
<!-- 结果输出的double数字会保留两位小数 -->
```

* 关于java中的值调用和引用调用(java核心技术p120)
	引用调用是引用地址的拷贝,虽然能够实际影响到引用对象的状态,但是一个方法无法让一个对象引用一个新的对象,比如:
```	
public static void swap(Person p1,Person p2){
	Person temp = p1;
	p1 = p2;
	p2 = temp;
}
```
	以上代码是无法实现交换功能的.
	
* 关于new对象的位置很有讲究,如果在循环的外面new的话,相当于add进list中的都是同一个对象
```
List<String[]> list = new ArrayList<>();    //关于课程信息的一行数据
    //关于课程的所有字段
    String[] fieldNames = {DbHelper.COURSE_NAME, DbHelper.START_DATE, DbHelper.END_DATE, DbHelper.DAY_OF_WEEK, DbHelper.START_TIME, DbHelper.END_TIME, DbHelper.TEACHER_NAME, DbHelper.ADDRESS,DbHelper.COURSE_ID};
    //从数据库取出的一行数据
    String[] fieldValues ;//千万不能在这里new ,不然后面add进list的全是这个地址= new String[fieldNames.length];
    Cursor cursor = db.query(DbHelper.TABLE_NAME_COURSE, null, null, null, null, null, null);
    if (cursor.moveToFirst()) {
        do {
            fieldValues = new String[fieldNames.length];
            for(int i = 0; i < fieldNames.length; i ++) {
                fieldValues[i] = cursor.getString(cursor.getColumnIndex(fieldNames[i]));
            }
            Log.d("hn", fieldValues.toString());
            System.out.println(fieldValues.toString());
            list.add(fieldValues);
        } while (cursor.moveToNext());
    }
    cursor.close();
```

* java基础,
	* 线程
	* 面向对象
	* 集合
	* 异常
	* IO/NIO

* servlet容器
	* 部署配置使用

* jdk原生线程池
参考地址:http://www.importnew.com/19011.html
1. 构造函数中各个参数的作用(四个构造函数中前三个构造函数都调用了最后一个构造器进行初始化)
	1. corePoolSize:核心池的大小,创建线程池之后默认情况下,线程池中是没有线程的,除非调用了prestartCoreThread()方法预创建线程,然后当有任务进来的时候,就会开始创建线程去执行任务,当线程池中的线程数目达到corePoolSize之后,就会把到达的任务放到缓存队列中
	2. maximumPoolSize:线程池的最大线程数,
	3. keepAliveTime:表示空闲线程能够存活的时间,默认情况下,只有当线程数大于corePoolSize的时候此参数才会生效,即当一个线程空闲的时间超过此参数时,线程就会终止.如果调用allowCoreThreadTimeOut(true)方法,那么线程数只要大于0,次参数就会生效.次参数的单位可以从天到纳秒
	4. workQueue:一个阻塞队列,用来存储等待执行的任务,重要参数,对线程池的运行过程产生重大影响,这里的阻塞队列一般有以下几种选择:
		1. ArrayBlockingQueue;
		2. LinkedBlockingQueue;
		3. SynchronousQueue;
	线程池的排队策略和阻塞队列的选择有关
	5. threadFactory:线程工厂,用来创建线程
	6. handler:当线程池满了的时候选取的拒绝处理任务的策略:
		1. ThreadPoolExecutor.AbortPolicy:丢弃任务并抛出RejectedExecutionException异常。 
		2. ThreadPoolExecutor.DiscardPolicy：也是丢弃任务，但是不抛出异常。 
		3. ThreadPoolExecutor.DiscardOldestPolicy：丢弃队列最前面的任务，然后重新尝试执行任务（重复此过程）
		4. ThreadPoolExecutor.CallerRunsPolicy：由调用线程处理该任务
2. ThreadPoolExecutor类中几个重要的方法:
	1. execute():实现了Executor接口中的方法,通过此方法想线程池提交任务,交由线程池去执行
	2. submit():继承而来的方法,也是向线程池提交任务,不过可以返回任务执行的结果,其实内部实现还是调用了execute()方法,只是利用Future来获取任务执行结果
	3. shutdown():用来关闭线程池
	4. shutdownNow():同上
3. 深入理解线程池的实现
	1. 线程池的状态:
```
volatile int runState;	//用来保证线程之间可见
static final int RUNNING    = 0;	//创建线程池后的初始状态
static final int SHUTDOWN   = 1;	//调用shutdown()方法,此时线程池不接受新的任务,会等待现有的任务执行完毕
static final int STOP       = 2;	//调用shutdownNow()方法,不接受新任务并且尝试终止正在执行的任务
static final int TERMINATED = 3;	//当线程池处于STOP或者SHUTDOWN状态,然后所有的工作线程都已经销毁,任务缓存队列中被清空或者执行结束,线程池被置为TERMINATED
```
	2. 任务的执行
	ThreadPoolExecutor类中比较重要的几个成员变量
```
private final BlockingQueue<Runnable> workQueue;              //任务缓存队列，用来存放等待执行的任务
private final ReentrantLock mainLock = new ReentrantLock();   //线程池的主要状态锁，对线程池状态（比如线程池大小、runState等）的改变都要使用这个锁
private final HashSet<Worker> workers = new HashSet<Worker>();  //用来存放工作集
private volatile long  keepAliveTime;    //线程存货时间   
private volatile boolean allowCoreThreadTimeOut;   //是否允许为核心线程设置存活时间
private volatile int   corePoolSize;     //核心池的大小（即线程池中的线程数目大于这个参数时，提交的任务会被放进任务缓存队列）
private volatile int   maximumPoolSize;   //线程池最大能容忍的线程数
private volatile int   poolSize;       //线程池中当前的线程数
private volatile RejectedExecutionHandler handler; //任务拒绝策略
private volatile ThreadFactory threadFactory;   //线程工厂，用来创建线程
private int largestPoolSize;   //用来记录线程池中曾经出现过的最大线程数
private long completedTaskCount;   //用来记录已经执行完毕的任务个数
```
	ThreadPoolExecutor类中的execute()的实现原理:
```
public void execute(Runnable command) {
        if (command == null)
            throw new NullPointerException();
        /*
         * Proceed in 3 steps:
         */
        int c = ctl.get();
        //如果当前线程数小于核心线程数,则尝试启动新线程,调用的addWorker()方法会原子性检查runState和workCount,防止错误添加线程
        //如果无法添加则重新获取当前线程数
        if (workerCountOf(c) < corePoolSize) {
        	//addWorker()方法中使用了ReentrantLock锁来保证线程池的状态
            if (addWorker(command, true))
                return;
            c = ctl.get();
        }
        //如果当前线程处于RUNNING状态,则将新任务加入缓存队列
        if (isRunning(c) && workQueue.offer(command)) {
            int recheck = ctl.get();
            //如果线程池变成非RUNNING状态,移除新任务,并reject()
            if (! isRunning(recheck) && remove(command))
                reject(command);
            else if (workerCountOf(recheck) == 0)
                addWorker(null, false);
        }
        //如果当前线程不处于RUNNING状态,尝试新建线程,创建线程失败则reject()
        else if (!addWorker(command, false))
            reject(command);
    }
```
	总结一下任务提交给线程池之后到执行的过程:
		1. 弄清楚corePoolSize和maximumPoolSize的含义
		2. Worker的作用
		3. 任务交给线程池的处理策略
			* 当前线程数小于核心线程数,创建新的线程处理任务
			* 如果当前线程数大于核心线程数,那么会尝试将新任务添加到缓存队列,如果添加成功,那么新任务等待空闲线程来处理;如果添加失败,一般是队列已经满了,那么会尝试创建新的线程去处理
			* 当前线程数达到maximumPoolSize时,会采取任务拒绝策略处理
			* 如果当前线程数量大于corePoolSize,那么当有线程的空闲时间超过keepAliveTime,线程将被终止,知道当前线程数不大于corePoolSize;如果允许核心线程设置空闲存活时间的话,那核心线程的空闲时间超出也会被终止.
	3. 线程池的初始化
		1. prestartCoreThread():初始化一个核心线程,
		2. prestartAllCoreThread():初始化所有核心线程.
		如何合理配置线程池的大小(N为cpu个数)
```
1. 如果是CPU密集型应用，则线程池大小设置为N+1
2. 如果是IO密集型应用，则线程池大小设置为2N+1
```
		以上公式只是最简单的情况(假设一台服务器上),一般线程池大小还要结合部署的系统中的CPU个数,内存大小,主要的任务执行的是计算还是I/O,还是混合操作
		比如I/O优化中,如下公式更合适:
```
最佳线程数目 = （（线程等待时间+线程CPU时间）/线程CPU时间 ）* CPU数目
```
	因为很显然，线程等待时间所占比例越高，需要越多线程。线程CPU运行时间所占比例越高，需要越少线程。
	4. 任务缓存队列及排队策略
	在前面我们多次提到了任务缓存队列，即workQueue，它用来存放等待执行的任务。
	workQueue的类型为BlockingQueue<Runnable>，通常可以取下面三种类型：
		1. ArrayBlockingQueue：基于数组的先进先出队列，此队列创建时必须指定大小；
		2. LinkedBlockingQueue：基于链表的先进先出队列，如果创建时没有指定此队列大小，则默认为Integer.MAX_VALUE；
		3. synchronousQueue：这个队列比较特殊，它不会保存提交的任务，而是将直接新建一个线程来执行新来的任务。
	5. 任务拒绝策略:上面提到的4中
	6. 线程池的关闭:上面提到的2种方法
	7. 线程池容量的动态调整
	ThreadPoolExecutor提供了动态调整线程池容量大小的方法：setCorePoolSize()和setMaximumPoolSize()，
	当上述参数从小变大时，ThreadPoolExecutor进行线程赋值，还可能立即创建新的线程来执行任务。


* 设计模式的介绍和应用场景
	1. 介绍
	2. 六大原则
	3. 创建型模式
		1. 单例模式
		保证内存中只存在一个对象:
```
public class Singleton{
	private static final Singleton instance = new Singleton();
	//构造函数必须是私有的
	private Singleton(){}
	public static Singleton getInstance(){
		return instance;
	}
}
```
		需要全局共享资源的时候可以使用,频繁实例化然后又销毁的对象,有状态的工具类对象,日志,配置文件
		2. 工厂方法模式
			1. 简单工厂模式:就是根据传入不同的参数的来返回不同的类实例,这些类都继承了相同的接口,比如画图接口中有画图方法
			2. 工程方法模式:就是为每种类单独写一个工厂类
		3. 抽象工厂模式
			3. 抽象工厂模式:就是定义一个抽象工厂来返回多个工厂,然后再有返回的每一种工厂返回对象实例
		4. 建造者模式
			1. 当遇到结构比较复杂的类的时候可以考虑使用
	4. 结构型模式
		1. 代理模式
		2. 装饰模式
		3. 适配器模式
	5. 行为型模式
		1. 命令模式
		2. 模板方法模式
		3. 策略模式
		就是当有几个类的方法相似的时候,比如两数的加减乘除,可以将方法抽取到一个接口中,然后分别实现,然后在一个Condition类中定义这些类的public final static对象 实例,使用的时候只要用类名取出对象调用相应的方法就行了
		4. 终结者模式
		5. 观察者模式
		6. 备忘录模式
		7. 访问者模式

* 线程安全的集合
	1. Vector

	2. HashTable

	3. StringBuffer

* 线程不安全的集合
	1. ArrayList
	`elementData[size++] = e;`
	从上面的源码中可以看出,ArrayList中增加一个元素的时候会分层两步进行:
		1. 先在elementData[size++]的地方存放元素,
		2. 然后再增大size.
		因此如果是多线程进行访问的话,有可能两个线程在同一个位置存放了元素.
	2. HashMap
	参考地址:http://www.importnew.com/22011.html
	HashMap的底层实现原理
		* HashMap的结构:是一个Node<K,V>[]的数组,Node实现了Map.Entry,初始容量16(代码中是`1<<4`),最大是2^30,当检查到插入数据时容量超出`capacity*loadFactor`,就要增大Hash表的尺寸,
		* 放入规则:计算A的键的hash值,然后%数组长度就是index,放在数组的index位置,如果后面put进来B的键的hash值和A相等的,那就是`B.next=A,Node[index]=B`
		* 为什么capacity的值是2的次幂:因为`index = hash & (length - 1)`,相当于取余,这样length-1的二进制就是1,和hash进行&操作之后不容易产生相同的结果,也就是尽量减少在同一个index放置多个元素,这样不但空间利用率最高,同时为了利用数组查找元素速度,我们当然是希望每一个位置只有一个元素,这样就不用遍历链表去比对key了.
		* resize()死循环的问题:当HashMap进行扩容的时候,原来的链表就要进行重排,这个时候如果并发操作,由于transfer()函数,就可能形成环,然后下次get元素的时候就可能进入死循环,
		jdk1.8好像解决了这个问题,进行优化之后省去了重新计算hash值的时间,而是通过判断扩容之后hash中新增的最高一位范围是1还是0(0则是原位置,1则是原位置+oldCap),均匀地将之前冲突的节点分散到新的桶上,
		* 支持NULL键和NULL值,而且null值的key总是放在数组第一个位置
		* 遍历的方法,取出keySet().iterator(),遍历iterator,即Map.entry,从entry中取key和value比较高效.


	3. StringBuilder
	


* 访问私有变量和私有方法
	1. 调用类的getDeclaredFields()和getDeclaredMethods()方法获得相应的field和method
	2. 调用method和field的setAccessible()方法允许访问私有成员属性
	3. field调用get(实例)方法,获得对应field的值
	4. method调用invoke()方法执行私有方法

* 泛型
	1. 泛型方法和泛型类的<>互不相干
	2. 泛型擦除如果泛型参数没有指定上限(如<T extends String>),那类型擦除之后T就被替换成Object,否则就是上限,如String
	3. 类型擦除也是为什么泛型类和泛型方法中不接受基本数据类型的原因





