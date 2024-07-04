# [C#多线程](https://www.cnblogs.com/dotnet261010/p/6159984.html)

一、基本概念

1、进程

首先打开任务管理器，查看当前运行的进程：

![](https://images2018.cnblogs.com/blog/1033738/201805/1033738-20180530224252704-1834001918.png#id=akk7M&originHeight=594&originWidth=666&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

从任务管理器里面可以看到当前所有正在运行的进程。那么究竟什么是进程呢？

进程（Process）是Windows系统中的一个基本概念，它包含着一个运行程序所需要的资源。一个正在运行的应用程序在操作系统中被视为一个进程，进程可以包括一个或多个线程。线程是操作系统分配处理器时间的基本单元，在进程中可以有多个线程同时执行代码。进程之间是相对独立的，一个进程无法访问另一个进程的数据（除非利用分布式计算方式），一个进程运行的失败也不会影响其他进程的运行，Windows系统就是利用进程把工作划分为多个独立的区域的。进程可以理解为一个程序的基本边界。是应用程序的一个运行例程，是应用程序的一次动态执行过程。

二、线程

在任务管理器里面查询当前总共运行的线程数：

![](https://images2018.cnblogs.com/blog/1033738/201805/1033738-20180530224534305-772722615.png#id=ULSkN&originHeight=590&originWidth=662&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

线程（Thread）是进程中的基本执行单元，是操作系统分配CPU时间的基本单位，一个进程可以包含若干个线程，在进程入口执行的第一个线程被视为这个进程的主线程。在.NET应用程序中，都是以Main()方法作为入口的，当调用此方法时系统就会自动创建一个主线程。线程主要是由CPU寄存器、调用栈和线程本地存储器（Thread Local Storage，TLS）组成的。CPU寄存器主要记录当前所执行线程的状态，调用栈主要用于维护线程所调用到的内存与数据，TLS主要用于存放线程的状态信息。

二、多线程

多线程的优点：可以同时完成多个任务；可以使程序的响应速度更快；可以让占用大量处理时间的任务或当前没有进行处理的任务定期将处理时间让给别的任务；可以随时停止任务；可以设置每个任务的优先级以优化程序性能。

那么可能有人会问：为什么可以多线程执行呢？总结起来有下面两方面的原因：

1、CPU运行速度太快，硬件处理速度跟不上，所以操作系统进行分时间片管理。这样，从宏观角度来说是多线程并发的，因为CPU速度太快，察觉不到，看起来是同一时刻执行了不同的操作。但是从微观角度来讲，同一时刻只能有一个线程在处理。

2、目前电脑都是多核多CPU的，一个CPU在同一时刻只能运行一个线程，但是多个CPU在同一时刻就可以运行多个线程。

然而，多线程虽然有很多优点，但是也必须认识到多线程可能存在影响系统性能的不利方面，才能正确使用线程。不利方面主要有如下几点：

（1）线程也是程序，所以线程需要占用内存，线程越多，占用内存也越多。

（2）多线程需要协调和管理，所以需要占用CPU时间以便跟踪线程。

（3）线程之间对共享资源的访问会相互影响，必须解决争用共享资源的问题。

（4）线程太多会导致控制太复杂，最终可能造成很多程序缺陷。

当启动一个可执行程序时，将创建一个主线程。在默认的情况下，C#程序具有一个线程，此线程执行程序中以Main方法开始和结束的代码，Main（）方法直接或间接执行的每一个命令都有默认线程（主线程）执行，当Main（）方法返回时此线程也将终止。

一个进程可以创建一个或多个线程以执行与该进程关联的部分程序代码。在C#中，线程是使用Thread类处理的，该类在System.Threading命名空间中。使用Thread类创建线程时，只需要提供线程入口，线程入口告诉程序让这个线程做什么。通过实例化一个Thread类的对象就可以创建一个线程。创建新的Thread对象时，将创建新的托管线程。Thread类接收一个ThreadStart委托或ParameterizedThreadStart委托的构造函数，该委托包装了调用Start方法时由新线程调用的方法，示例代码如下：

Thread thread=new Thread(new ThreadStart(method));//创建线程

thread.Start();                              //启动线程

上面代码实例化了一个Thread对象，并指明将要调用的方法method()，然后启动线程。ThreadStart委托中作为参数的方法不需要参数，并且没有返回值。ParameterizedThreadStart委托一个对象作为参数，利用这个参数可以很方便地向线程传递参数，示例代码如下：

Thread thread=new Thread(new ParameterizedThreadStart(method));//创建线程

thread.Start(3);                                       //启动线程

创建多线程的步骤：
1、编写线程所要执行的方法
2、实例化Thread类，并传入一个指向线程所要执行方法的委托。（这时线程已经产生，但还没有运行）
3、调用Thread实例的Start方法，标记该线程可以被CPU执行了，但具体执行时间由CPU决定

2.1 System.Threading.Thread类

Thread类是是控制线程的基础类，位于System.Threading命名空间下，具有4个重载的构造函数：

| 名称 | 说明 |
| --- | --- |
| [Thread(ParameterizedThreadStart)](https://msdn.microsoft.com/zh-cn/library/1h2f2459(v=vs.110).aspx) | 初始化 Thread 类的新实例，指定允许对象在线程启动时传递给线程的委托。要执行的方法是有参的。 |
| [Thread(ParameterizedThreadStart, Int32)](https://msdn.microsoft.com/zh-cn/library/ms149581(v=vs.110).aspx) | 初始化 Thread 类的新实例，指定允许对象在线程启动时传递给线程的委托，并指定线程的最大堆栈大小 |
| [Thread(ThreadStart)](https://msdn.microsoft.com/zh-cn/library/xx3ezzs2(v=vs.110).aspx) | 初始化 Thread 类的新实例。要执行的方法是无参的。 |
| [Thread(ThreadStart, Int32)](https://msdn.microsoft.com/zh-cn/library/5cykbwz4(v=vs.110).aspx) | 初始化 Thread 类的新实例，指定线程的最大堆栈大小。 |


ThreadStart是一个无参的、返回值为void的委托。委托定义如下：

public delegate void ThreadStart()

通过ThreadStart委托创建并运行一个线程：

[![](https://common.cnblogs.com/images/copycode.gif#id=skpuy&originHeight=20&originWidth=20&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)](javascript:void(0);)

按 Ctrl+C 复制代码

按 Ctrl+C 复制代码

[![](https://common.cnblogs.com/images/copycode.gif#id=PNXrm&originHeight=20&originWidth=20&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)](javascript:void(0);)

运行结果

![](https://images2015.cnblogs.com/blog/1033738/201612/1033738-20161211113130210-1401793011.png#id=g6sx0&originHeight=157&originWidth=591&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

除了可以运行静态的方法，还可以运行实例方法

[![](https://common.cnblogs.com/images/copycode.gif#id=oC2hi&originHeight=20&originWidth=20&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)](javascript:void(0);)

![](https://common.cnblogs.com/images/copycode.gif#id=VcGZn&originHeight=20&originWidth=20&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

```
 1  class Program
 2     {
 3         static void Main(string[] args)
 4         {
 5             //创建ThreadTest类的一个实例
 6             ThreadTest test=new ThreadTest();
 7             //调用test实例的MyThread方法
 8             Thread thread = new Thread(new ThreadStart(test.MyThread));
 9             //启动线程
10             thread.Start();
11             Console.ReadKey();
12         }
13     }
14 
15     class ThreadTest
16     {
17         public void MyThread()
18         {
19             Console.WriteLine("这是一个实例方法");
20         }
21     }
```

![](https://common.cnblogs.com/images/copycode.gif#id=HxMMi&originHeight=20&originWidth=20&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

[![](https://common.cnblogs.com/images/copycode.gif#id=HoaDm&originHeight=20&originWidth=20&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)](javascript:void(0);)

运行结果：

![](https://images2015.cnblogs.com/blog/1033738/201612/1033738-20161211114013069-1607557625.png#id=wymnD&originHeight=111&originWidth=591&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

如果为了简单，也可以通过匿名委托或Lambda表达式来为Thread的构造方法赋值

[![](https://common.cnblogs.com/images/copycode.gif#id=YLy6s&originHeight=20&originWidth=20&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)](javascript:void(0);)

![](https://common.cnblogs.com/images/copycode.gif#id=P4LnA&originHeight=20&originWidth=20&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

```
 1  static void Main(string[] args)
 2  {
 3        //通过匿名委托创建
 4        Thread thread1 = new Thread(delegate() { Console.WriteLine("我是通过匿名委托创建的线程"); });
 5        thread1.Start();
 6        //通过Lambda表达式创建
 7        Thread thread2 = new Thread(() => Console.WriteLine("我是通过Lambda表达式创建的委托"));
 8        thread2.Start();
 9        Console.ReadKey();
10  }
```

![](https://common.cnblogs.com/images/copycode.gif#id=ksoC4&originHeight=20&originWidth=20&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

[![](https://common.cnblogs.com/images/copycode.gif#id=yIAxg&originHeight=20&originWidth=20&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)](javascript:void(0);)

运行结果：

![](https://images2015.cnblogs.com/blog/1033738/201612/1033738-20161211114654257-1719449786.png#id=GVI45&originHeight=139&originWidth=583&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

ParameterizedThreadStart是一个有参的、返回值为void的委托，定义如下：

public delegate void ParameterizedThreadStart(Object obj)

[![](https://common.cnblogs.com/images/copycode.gif#id=E6ciE&originHeight=20&originWidth=20&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)](javascript:void(0);)

![](https://common.cnblogs.com/images/copycode.gif#id=v0HsA&originHeight=20&originWidth=20&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

```
 1  class Program
 2     {
 3         static void Main(string[] args)
 4         {
 5             //通过ParameterizedThreadStart创建线程
 6             Thread thread = new Thread(new ParameterizedThreadStart(Thread1));
 7             //给方法传值
 8             thread.Start("这是一个有参数的委托");
 9             Console.ReadKey();
10         }
11 
12         /// <summary>
13         /// 创建有参的方法
14         /// 注意：方法里面的参数类型必须是Object类型
15         /// </summary>
16         /// <param name="obj"></param>
17         static void Thread1(object obj)
18         {
19             Console.WriteLine(obj);
20         }
21     }
```

![](https://common.cnblogs.com/images/copycode.gif#id=IFP4j&originHeight=20&originWidth=20&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

[![](https://common.cnblogs.com/images/copycode.gif#id=AUpNs&originHeight=20&originWidth=20&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)](javascript:void(0);)

注意：ParameterizedThreadStart委托的参数类型必须是Object的。如果使用的是不带参数的委托，不能使用带参数的Start方法运行线程，否则系统会抛出异常。但使用带参数的委托，可以使用thread.Start()来运行线程，这时所传递的参数值为null。

![](https://images2015.cnblogs.com/blog/1033738/201612/1033738-20161211121057147-1154783457.png#id=yOnDh&originHeight=404&originWidth=1018&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

![](https://images2015.cnblogs.com/blog/1033738/201612/1033738-20161211121519632-383256724.png#id=feNr7&originHeight=448&originWidth=1223&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

2.2 线程的常用属性

| 属性名称 | 说明 |
| --- | --- |
| CurrentContext | 获取线程正在其中执行的当前上下文。 |
| CurrentThread | 获取当前正在运行的线程。 |
| ExecutionContext | 获取一个 ExecutionContext 对象，该对象包含有关当前线程的各种上下文的信息。 |
| IsAlive | 获取一个值，该值指示当前线程的执行状态。 |
| IsBackground | 获取或设置一个值，该值指示某个线程是否为后台线程。 |
| IsThreadPoolThread | 获取一个值，该值指示线程是否属于托管线程池。 |
| ManagedThreadId | 获取当前托管线程的唯一标识符。 |
| Name | 获取或设置线程的名称。 |
| Priority | 获取或设置一个值，该值指示线程的调度优先级。 |
| ThreadState | 获取一个值，该值包含当前线程的状态。 |


**2.2.1 线程的标识符**

ManagedThreadId是确认线程的唯一标识符，程序在大部分情况下都是通过Thread.ManagedThreadId来辨别线程的。而Name是一个可变值，在默认时候，Name为一个空值 Null，开发人员可以通过程序设置线程的名称，但这只是一个辅助功能。

**2.2.2 线程的优先级别**

当线程之间争夺CPU时间时，CPU按照线程的优先级给予服务。高优先级的线程可以完全阻止低优先级的线程执行。.NET为线程设置了Priority属性来定义线程执行的优先级别，里面包含5个选项，其中Normal是默认值。除非系统有特殊要求，否则不应该随便设置线程的优先级别。

| 成员名称 | 说明 |
| --- | --- |
| Lowest | 可以将 Thread 安排在具有任何其他优先级的线程之后。 |
| BelowNormal | 可以将 Thread 安排在具有 Normal 优先级的线程之后，在具有 Lowest 优先级的线程之前。 |
| Normal | 默认选择。可以将 Thread 安排在具有 AboveNormal 优先级的线程之后，在具有 BelowNormal 优先级的线程之前。 |
| AboveNormal | 可以将 Thread 安排在具有 Highest 优先级的线程之后，在具有 Normal 优先级的线程之前。 |
| Highest | 可以将 Thread 安排在具有任何其他优先级的线程之前。 |


**2.2.3 线程的状态**

通过ThreadState可以检测线程是处于Unstarted、Sleeping、Running 等等状态，它比 IsAlive 属性能提供更多的特定信息。

前面说过，一个应用程序域中可能包括多个上下文，而通过CurrentContext可以获取线程当前的上下文。

CurrentThread是最常用的一个属性，它是用于获取当前运行的线程。

**2.2.4 System.Threading.Thread的方法**

Thread 中包括了多个方法来控制线程的创建、挂起、停止、销毁，以后来的例子中会经常使用。

| 方法名称 | 说明 |
| --- | --- |
| Abort() | 终止本线程。 |
| GetDomain() | 返回当前线程正在其中运行的当前域。 |
| GetDomainId() | 返回当前线程正在其中运行的当前域Id。 |
| Interrupt() | 中断处于 WaitSleepJoin 线程状态的线程。 |
| Join() | 已重载。 阻塞调用线程，直到某个线程终止时为止。 |
| Resume() | 继续运行已挂起的线程。 |
| Start() | 执行本线程。 |
| Suspend() | 挂起当前线程，如果当前线程已属于挂起状态则此不起作用 |
| Sleep() | 把正在运行的线程挂起一段时间。 |


线程示例

[![](https://common.cnblogs.com/images/copycode.gif#id=IoOmM&originHeight=20&originWidth=20&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)](javascript:void(0);)

![](https://common.cnblogs.com/images/copycode.gif#id=x0eeW&originHeight=20&originWidth=20&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

```
 1     static void Main(string[] args)
 2         {
 3             //获取正在运行的线程
 4             Thread thread = Thread.CurrentThread;
 5             //设置线程的名字
 6             thread.Name = "主线程";
 7             //获取当前线程的唯一标识符
 8             int id = thread.ManagedThreadId;
 9             //获取当前线程的状态
10             ThreadState state= thread.ThreadState;
11             //获取当前线程的优先级
12             ThreadPriority priority= thread.Priority;
13             string strMsg = string.Format("Thread ID:{0}\n" + "Thread Name:{1}\n" +
14                 "Thread State:{2}\n" + "Thread Priority:{3}\n", id, thread.Name,
15                 state, priority);
16 
17             Console.WriteLine(strMsg);
18                       
19             Console.ReadKey();
20         }
```

![](https://common.cnblogs.com/images/copycode.gif#id=KM4D9&originHeight=20&originWidth=20&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

[![](https://common.cnblogs.com/images/copycode.gif#id=l515V&originHeight=20&originWidth=20&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)](javascript:void(0);)

运行结果:

![](https://images2015.cnblogs.com/blog/1033738/201612/1033738-20161211123413257-58776054.png#id=vlYAt&originHeight=512&originWidth=977&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

2.3 前台线程和后台线程

前台线程：只有所有的前台线程都结束，应用程序才能结束。默认情况下创建的线程
都是前台线程
后台线程：只要所有的前台线程结束，后台线程自动结束。通过Thread.IsBackground设置后台线程。必须在调用Start方法之前设置线程的类型，否则一旦线程运行，将无法改变其类型。

通过BeginXXX方法运行的线程都是后台线程。

[![](https://common.cnblogs.com/images/copycode.gif#id=trc5c&originHeight=20&originWidth=20&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)](javascript:void(0);)

![](https://common.cnblogs.com/images/copycode.gif#id=HG6Zi&originHeight=20&originWidth=20&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

```
 1 class Program
 2     {
 3         static void Main(string[] args)
 4         {                   
 5             //演示前台、后台线程
 6             BackGroundTest background = new BackGroundTest(10);
 7             //创建前台线程
 8             Thread fThread = new Thread(new ThreadStart(background.RunLoop));
 9             //给线程命名
10             fThread.Name = "前台线程";
11             
12 
13             BackGroundTest background1 = new BackGroundTest(20);
14             //创建后台线程
15             Thread bThread = new Thread(new ThreadStart(background1.RunLoop));
16             bThread.Name = "后台线程";
17             //设置为后台线程
18             bThread.IsBackground = true;
19 
20             //启动线程
21             fThread.Start();
22             bThread.Start();
23         }
24     }
25 
26     class BackGroundTest
27     {
28         private int Count;
29         public BackGroundTest(int count)
30         {
31             this.Count = count;
32         }
33         public void RunLoop()
34         {
35             //获取当前线程的名称
36             string threadName = Thread.CurrentThread.Name;
37             for (int i = 0; i < Count; i++)
38             {
39                 Console.WriteLine("{0}计数：{1}",threadName,i.ToString());
40                 //线程休眠500毫秒
41                 Thread.Sleep(1000);
42             }
43             Console.WriteLine("{0}完成计数",threadName);
44             
45         }
46     }
```

![](https://common.cnblogs.com/images/copycode.gif#id=LxRxc&originHeight=20&originWidth=20&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

[![](https://common.cnblogs.com/images/copycode.gif#id=qOFEv&originHeight=20&originWidth=20&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)](javascript:void(0);)

运行结果：前台线程执行完，后台线程未执行完，程序自动结束。

![](https://images2015.cnblogs.com/blog/1033738/201612/1033738-20161211150321319-1313375658.png#id=MM3Ao&originHeight=367&originWidth=978&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

把bThread.IsBackground = true注释掉，运行结果：主线程执行完毕后(Main函数)，程序并未结束，而是要等所有的前台线程结束以后才会结束。

![](https://images2015.cnblogs.com/blog/1033738/201612/1033738-20161211145848647-1856795810.png#id=cgo7C&originHeight=554&originWidth=961&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

后台线程一般用于处理不重要的事情，应用程序结束时，后台线程是否执行完成对整个应用程序没有影响。如果要执行的事情很重要，需要将线程设置为前台线程。

2.4 线程同步

所谓同步：是指在某一时刻只有一个线程可以访问变量。
如果不能确保对变量的访问是同步的，就会产生错误。
c#为同步访问变量提供了一个非常简单的方式，即使用c#语言的关键字Lock，它可以把一段代码定义为互斥段，互斥段在一个时刻内只允许一个线程进入执行，而其他线程必须等待。在c#中，关键字Lock定义如下：
Lock(expression)
{
statement_block
}

expression代表你希望跟踪的对象：
如果你想保护一个类的实例，一般地，你可以使用this；
如果你想保护一个静态变量（如互斥代码段在一个静态方法内部），一般使用类名就可以了
而statement_block就算互斥段的代码，这段代码在一个时刻内只可能被一个线程执行。

以书店卖书为例

[![](https://common.cnblogs.com/images/copycode.gif#id=tMqk3&originHeight=20&originWidth=20&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)](javascript:void(0);)

![](https://common.cnblogs.com/images/copycode.gif#id=xEyL1&originHeight=20&originWidth=20&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

```
 1 class Program
 2     {
 3         static void Main(string[] args)
 4         {                   
 5             BookShop book = new BookShop();
 6             //创建两个线程同时访问Sale方法
 7             Thread t1 = new Thread(new ThreadStart(book.Sale));
 8             Thread t2 = new Thread(new ThreadStart(book.Sale));
 9             //启动线程
10             t1.Start();
11             t2.Start();
12             Console.ReadKey();
13         }
14     }
15 
16    
17 
18     class BookShop
19     {
20         //剩余图书数量
21         public int num = 1;
22         public void Sale()
23         {
24             int tmp = num;
25             if (tmp > 0)//判断是否有书，如果有就可以卖
26             {
27                 Thread.Sleep(1000);
28                 num -= 1;
29                 Console.WriteLine("售出一本图书，还剩余{0}本", num);
30             }
31             else
32             {
33                 Console.WriteLine("没有了");
34             }
35         }
36     }
```

![](https://common.cnblogs.com/images/copycode.gif#id=TRW4e&originHeight=20&originWidth=20&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

[![](https://common.cnblogs.com/images/copycode.gif#id=EUis0&originHeight=20&originWidth=20&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)](javascript:void(0);)

运行结果：

![](https://images2015.cnblogs.com/blog/1033738/201612/1033738-20161211162002257-1617971456.png#id=xHeDE&originHeight=293&originWidth=596&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

从运行结果可以看出，两个线程同步访问共享资源，没有考虑同步的问题，结果不正确。

考虑线程同步，改进后的代码：

[![](https://common.cnblogs.com/images/copycode.gif#id=FeJ1p&originHeight=20&originWidth=20&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)](javascript:void(0);)

![](https://common.cnblogs.com/images/copycode.gif#id=gr91d&originHeight=20&originWidth=20&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

```
 1 class Program
 2     {
 3         static void Main(string[] args)
 4         {                   
 5             BookShop book = new BookShop();
 6             //创建两个线程同时访问Sale方法
 7             Thread t1 = new Thread(new ThreadStart(book.Sale));
 8             Thread t2 = new Thread(new ThreadStart(book.Sale));
 9             //启动线程
10             t1.Start();
11             t2.Start();
12             Console.ReadKey();
13         }
14     }
15 
16    
17 
18     class BookShop
19     {
20         //剩余图书数量
21         public int num = 1;
22         public void Sale()
23         {
24             //使用lock关键字解决线程同步问题
25             lock (this)
26             {
27                 int tmp = num;
28                 if (tmp > 0)//判断是否有书，如果有就可以卖
29                 {
30                     Thread.Sleep(1000);
31                     num -= 1;
32                     Console.WriteLine("售出一本图书，还剩余{0}本", num);
33                 }
34                 else
35                 {
36                     Console.WriteLine("没有了");
37                 }
38             }
39         }
40     }
```

![](https://common.cnblogs.com/images/copycode.gif#id=kH62n&originHeight=20&originWidth=20&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

[![](https://common.cnblogs.com/images/copycode.gif#id=K82I4&originHeight=20&originWidth=20&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)](javascript:void(0);)

运行结果：

![](https://images2015.cnblogs.com/blog/1033738/201612/1033738-20161211162532429-1303317997.png#id=iBY27&originHeight=230&originWidth=545&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

2.5 跨线程访问

![](https://images2015.cnblogs.com/blog/1033738/201612/1033738-20161211165843304-141162350.png#id=KzH98&originHeight=169&originWidth=433&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

点击“测试”，创建一个线程，从0循环到10000给文本框赋值，代码如下：

[![](https://common.cnblogs.com/images/copycode.gif#id=pdoAD&originHeight=20&originWidth=20&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)](javascript:void(0);)

![](https://common.cnblogs.com/images/copycode.gif#id=QM0e1&originHeight=20&originWidth=20&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

```
 1  private void btn_Test_Click(object sender, EventArgs e)
 2         {
 3             //创建一个线程去执行这个方法:创建的线程默认是前台线程
 4             Thread thread = new Thread(new ThreadStart(Test));
 5             //Start方法标记这个线程就绪了，可以随时被执行，具体什么时候执行这个线程，由CPU决定
 6             //将线程设置为后台线程
 7             thread.IsBackground = true;
 8             thread.Start();
 9         }
10 
11         private void Test()
12         {
13             for (int i = 0; i < 10000; i++)
14             {               
15                 this.textBox1.Text = i.ToString();
16             }
17         }
```

![](https://common.cnblogs.com/images/copycode.gif#id=FTgOM&originHeight=20&originWidth=20&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

[![](https://common.cnblogs.com/images/copycode.gif#id=NTQzZ&originHeight=20&originWidth=20&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)](javascript:void(0);)

运行结果：

![](https://images2015.cnblogs.com/blog/1033738/201612/1033738-20161211170247710-1207822491.png#id=pG7ie&originHeight=158&originWidth=964&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

产生错误的原因：textBox1是由主线程创建的，thread线程是另外创建的一个线程，在.NET上执行的是托管代码，C#强制要求这些代码必须是线程安全的，即不允许跨线程访问Windows窗体的控件。

解决方案：

1、在窗体的加载事件中，将C#内置控件(Control)类的CheckForIllegalCrossThreadCalls属性设置为false，屏蔽掉C#编译器对跨线程调用的检查。

```
 private void Form1_Load(object sender, EventArgs e)
 {
        //取消跨线程的访问
        Control.CheckForIllegalCrossThreadCalls = false;
 }
```

使用上述的方法虽然可以保证程序正常运行并实现应用的功能，但是在实际的软件开发中，做如此设置是不安全的（不符合.NET的安全规范），在产品软件的开发中，此类情况是不允许的。如果要在遵守.NET安全标准的前提下，实现从一个线程成功地访问另一个线程创建的空间，要使用C#的方法回调机制。

2、使用回调函数

回调实现的一般过程：

C#的方法回调机制，也是建立在委托基础上的，下面给出它的典型实现过程。

（1）、定义、声明回调。

```
1 //定义回调
2 private delegate void DoSomeCallBack(Type para);
3 //声明回调
4 DoSomeCallBack doSomaCallBack;
```

可以看出，这里定义声明的“回调”（doSomaCallBack）其实就是一个委托。

（2）、初始化回调方法。

doSomeCallBack=new DoSomeCallBack(DoSomeMethod);

所谓“初始化回调方法”实际上就是实例化刚刚定义了的委托，这里作为参数的DoSomeMethod称为“回调方法”，它封装了对另一个线程中目标对象（窗体控件或其他类）的操作代码。

（3）、触发对象动作

Opt obj.Invoke(doSomeCallBack,arg);

其中Opt obj为目标操作对象，在此假设它是某控件，故调用其Invoke方法。Invoke方法签名为：

object Control.Invoke(Delegate method,params object[] args);

它的第一个参数为委托类型，可见“触发对象动作”的本质，就是把委托doSomeCallBack作为参数传递给控件的Invoke方法，这与委托的使用方式是一模一样的。

最终作用于对象Opt obj的代码是置于回调方法体DoSomeMethod()中的，如下所示：

private void DoSomeMethod(type para)

{

//方法体

Opt obj.someMethod(para);

}

如果不用回调，而是直接在程序中使用“Opt obj.someMethod(para);”，则当对象Opt obj不在本线程（跨线程访问）时就会发生上面所示的错误。

从以上回调实现的一般过程可知：C#的回调机制，实质上是委托的一种应用。在C#网络编程中，回调的应用是非常普遍的，有了方法回调，就可以在.NET上写出线程安全的代码了。

使用方法回调，实现给文本框赋值：

[![](https://common.cnblogs.com/images/copycode.gif#id=CPwSv&originHeight=20&originWidth=20&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)](javascript:void(0);)

![](https://common.cnblogs.com/images/copycode.gif#id=viyr0&originHeight=20&originWidth=20&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

```
 1 namespace MultiThreadDemo
 2 {
 3     public partial class Form1 : Form
 4     {
 5         public Form1()
 6         {
 7             InitializeComponent();
 8         }
 9 
10         //定义回调
11         private delegate void setTextValueCallBack(int value);
12         //声明回调
13         private setTextValueCallBack setCallBack;
14 
15         private void btn_Test_Click(object sender, EventArgs e)
16         {
17             //实例化回调
18             setCallBack = new setTextValueCallBack(SetValue);
19             //创建一个线程去执行这个方法:创建的线程默认是前台线程
20             Thread thread = new Thread(new ThreadStart(Test));
21             //Start方法标记这个线程就绪了，可以随时被执行，具体什么时候执行这个线程，由CPU决定
22             //将线程设置为后台线程
23             thread.IsBackground = true;
24             thread.Start();
25         }
26 
27         private void Test()
28         {
29             for (int i = 0; i < 10000; i++)
30             {               
31                 //使用回调
32                 textBox1.Invoke(setCallBack, i);
33             }
34         }
35 
36         /// <summary>
37         /// 定义回调使用的方法
38         /// </summary>
39         /// <param name="value"></param>
40         private void SetValue(int value)
41         {
42             this.textBox1.Text = value.ToString();
43         }
44     }
45 }
```

![](https://common.cnblogs.com/images/copycode.gif#id=Wty8t&originHeight=20&originWidth=20&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

[![](https://common.cnblogs.com/images/copycode.gif#id=MLY3H&originHeight=20&originWidth=20&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)](javascript:void(0);)

2.6 终止线程

若想终止正在运行的线程，可以使用Abort（）方法。

三、同步和异步

同步和异步是对方法执行顺序的描述。

同步：等待上一行完成计算之后，才会进入下一行。

例如：请同事吃饭，同事说很忙，然后就等着同事忙完，然后一起去吃饭。

异步：不会等待方法的完成，会直接进入下一行，是非阻塞的。

例如：请同事吃饭，同事说很忙，那同事先忙，自己去吃饭，同事忙完了他自己去吃饭。

下面通过一个例子讲解同步和异步的区别

1、新建一个winform程序，上面有两个按钮，一个同步方法、一个异步方法，在属性里面把输出类型改成控制台应用程序，这样可以看到输出结果，代码如下：

[![](https://common.cnblogs.com/images/copycode.gif#id=iM5OW&originHeight=20&originWidth=20&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)](javascript:void(0);)

![](https://common.cnblogs.com/images/copycode.gif#id=glibl&originHeight=20&originWidth=20&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

```
 1 using System;
 2 using System.Collections.Generic;
 3 using System.ComponentModel;
 4 using System.Data;
 5 using System.Drawing;
 6 using System.Linq;
 7 using System.Text;
 8 using System.Threading;
 9 using System.Threading.Tasks;
10 using System.Windows.Forms;
11 
12 namespace MyAsyncThreadDemo
13 {
14     public partial class Form1 : Form
15     {
16         public Form1()
17         {
18             InitializeComponent();
19         }
20 
21         /// <summary>
22         /// 异步方法
23         /// </summary>
24         /// <param name="sender"></param>
25         /// <param name="e"></param>
26         private void btnAsync_Click(object sender, EventArgs e)
27         {
28             Console.WriteLine($"***************btnAsync_Click Start {Thread.CurrentThread.ManagedThreadId}");
29             Action<string> action = this.DoSomethingLong;
30             // 调用委托(同步调用)
31             action.Invoke("btnAsync_Click_1");
32             // 异步调用委托
33             action.BeginInvoke("btnAsync_Click_2",null,null);
34             Console.WriteLine($"***************btnAsync_Click End    {Thread.CurrentThread.ManagedThreadId}");
35         }
36 
37         /// <summary>
38         /// 同步方法
39         /// </summary>
40         /// <param name="sender"></param>
41         /// <param name="e"></param>
42         private void btnSync_Click(object sender, EventArgs e)
43         {
44             Console.WriteLine($"****************btnSync_Click Start {Thread.CurrentThread.ManagedThreadId.ToString("00")} {DateTime.Now.ToString("yyyy-MM-dd HH:mm:ss.fff")}***************");
45             int j = 3;
46             int k = 5;
47             int m = j + k;
48             for (int i = 0; i < 5; i++)
49             {
50                 string name = string.Format($"btnSync_Click_{i}");
51                 this.DoSomethingLong(name);
52             }
53         }
54 
55 
56         private void DoSomethingLong(string name)
57         {
58             Console.WriteLine($"****************DoSomethingLong {name} Start {Thread.CurrentThread.ManagedThreadId.ToString("00")} {DateTime.Now.ToString("yyyy-MM-dd HH:mm:ss.fff")}***************");
59             long lResult = 0;
60             for (int i = 0; i < 1000000000; i++)
61             {
62                 lResult += i;
63             }
64             Console.WriteLine($"****************DoSomethingLong {name}   End {Thread.CurrentThread.ManagedThreadId.ToString("00")} {DateTime.Now.ToString("yyyy-MM-dd HH:mm:ss.fff")} {lResult}***************");
65         }
66     }
67 }
```

![](https://common.cnblogs.com/images/copycode.gif#id=JqLQ7&originHeight=20&originWidth=20&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

[![](https://common.cnblogs.com/images/copycode.gif#id=WxNxF&originHeight=20&originWidth=20&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)](javascript:void(0);)

2、启动程序，点击同步，结果如下：

![](https://images2018.cnblogs.com/blog/1033738/201805/1033738-20180531000901060-1531337026.png#id=mXozV&originHeight=190&originWidth=976&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

从上面的截图中能够很清晰的看出：同步方法是等待上一行代码执行完毕之后才会执行下一行代码。

点击异步，结果如下：

![](https://images2018.cnblogs.com/blog/1033738/201805/1033738-20180531001146112-1822723215.png#id=kGpdd&originHeight=137&originWidth=978&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

从上面的截图中看出：当执行到action.BeginInvoke("btnAsync_Click_2",null,null);这句代码的时候，程序并没有等待这段代码执行完就执行了下面的End，没有阻塞程序的执行。

在刚才的测试中，如果点击同步，这时winform界面不能拖到，界面卡住了，是因为主线程(即UI线程)在忙于计算。

点击异步的时候，界面不会卡住，这是因为主线程已经结束，计算任务交给子线程去做。

在仔细检查上面两个截图，可以看出异步的执行速度比同步执行速度要快。同步方法执行完将近16秒，异步方法执行完将近6秒。

在看下面的一个例子，修改异步的方法，也和同步方法一样执行循环，修改后的代码如下：

[![](https://common.cnblogs.com/images/copycode.gif#id=Ahc2c&originHeight=20&originWidth=20&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)](javascript:void(0);)

![](https://common.cnblogs.com/images/copycode.gif#id=LM4ui&originHeight=20&originWidth=20&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

```
 1 private void btnAsync_Click(object sender, EventArgs e)
 2 {
 3       Console.WriteLine($"***************btnAsync_Click Start {Thread.CurrentThread.ManagedThreadId}");
 4       //Action<string> action = this.DoSomethingLong;
 5       //// 调用委托(同步调用)
 6       //action.Invoke("btnAsync_Click_1");
 7       //// 异步调用委托
 8       //action.BeginInvoke("btnAsync_Click_2",null,null);
 9       Action<string> action = this.DoSomethingLong;
10       for (int i = 0; i < 5; i++)
11       {
12            //Thread.Sleep(5);
13            string name = string.Format($"btnAsync_Click_{i}");
14            action.BeginInvoke(name, null, null);
15       }
16       Console.WriteLine($"***************btnAsync_Click End    {Thread.CurrentThread.ManagedThreadId}");
17 }
```

![](https://common.cnblogs.com/images/copycode.gif#id=zvEnd&originHeight=20&originWidth=20&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

[![](https://common.cnblogs.com/images/copycode.gif#id=BuIu6&originHeight=20&originWidth=20&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)](javascript:void(0);)

结果如下：

![](https://images2018.cnblogs.com/blog/1033738/201805/1033738-20180531005521273-405746523.png#id=NcKkd&originHeight=484&originWidth=981&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

从截图中能够看出：同步方法执行是有序的，异步方法执行是无序的。异步方法无序包括启动无序和结束无序。启动无序是因为同一时刻向操作系统申请线程，操作系统收到申请以后，返回执行的顺序是无序的，所以启动是无序的。结束无序是因为虽然线程执行的是同样的操作，但是每个线程的耗时是不同的，所以结束的时候不一定是先启动的线程就先结束。从上面同步方法中可以清晰的看出：btnSync_Click_0执行时间耗时不到3秒，而btnSync_Click_1执行时间耗时超过了3秒。可以想象体育比赛中的跑步，每位运动员听到发令枪起跑的顺序不同，每位运动员花费的时间不同，最终到达终点的顺序也不同。

总结一下同步方法和异步方法的区别：

1、同步方法由于主线程忙于计算，所以会卡住界面。

异步方法由于主线程执行完了，其他计算任务交给子线程去执行，所以不会卡住界面，用户体验性好。

2、同步方法由于只有一个线程在计算，所以执行速度慢。

异步方法由多个线程并发运算，所以执行速度快，但并不是线性增长的（资源可能不够）。多线程也不是越多越好，只有多个独立的任务同时运行，才能加快速度。

3、同步方法是有序的。

异步多线程是无序的：启动无序，执行时间不确定，所以结束也是无序的。一定不要通过等待几毫秒的形式来控制线程启动/执行时间/结束。

四、回调

先来看看异步多线程无序的例子：

在界面上新增一个按钮，实现代码如下：

[![](https://common.cnblogs.com/images/copycode.gif#id=Kfy4J&originHeight=20&originWidth=20&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)](javascript:void(0);)

![](https://common.cnblogs.com/images/copycode.gif#id=TBEdc&originHeight=20&originWidth=20&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

```
1 private void btnAsyncAdvanced_Click(object sender, EventArgs e)
2 {
3       Console.WriteLine($"****************btnAsyncAdvanced_Click Start {Thread.CurrentThread.ManagedThreadId.ToString("00")} {DateTime.Now.ToString("yyyy-MM-dd HH:mm:ss.fff")}***************");
4       Action<string> action = this.DoSomethingLong;
5       action.BeginInvoke("btnAsyncAdvanced_Click", null, null);
6       // 需求：异步多线程执行完之后再打印出下面这句
7       Console.WriteLine($"到这里计算已经完成了。{Thread.CurrentThread.ManagedThreadId.ToString("00")}。");
8       Console.WriteLine($"****************btnAsyncAdvanced_Click End {Thread.CurrentThread.ManagedThreadId.ToString("00")} {DateTime.Now.ToString("yyyy-MM-dd HH:mm:ss.fff")}***************");
9 }
```

![](https://common.cnblogs.com/images/copycode.gif#id=IR76c&originHeight=20&originWidth=20&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

[![](https://common.cnblogs.com/images/copycode.gif#id=P0UgU&originHeight=20&originWidth=20&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)](javascript:void(0);)

运行结果：

![](https://images2018.cnblogs.com/blog/1033738/201805/1033738-20180531222556509-934483516.png#id=wV8Sg&originHeight=124&originWidth=978&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

从上面的截图中看出，最终的效果并不是我们想要的效果，而且打印输出的还是主线程。

既然异步多线程是无序的，那我们有没有什么办法可以解决无序的问题呢？办法当然是有的，那就是使用回调，.NET框架已经帮我们实现了回调：

![](https://images2018.cnblogs.com/blog/1033738/201805/1033738-20180531222937262-978905414.png#id=i9tqV&originHeight=51&originWidth=754&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

BeginInvoke的第二个参数就是一个回调，那么AsyncCallback究竟是什么呢？F12查看AsyncCallback的定义：

![](https://images2018.cnblogs.com/blog/1033738/201805/1033738-20180531223410355-1241571657.png#id=CU8RW&originHeight=291&originWidth=778&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

发现AsyncCallback就是一个委托，参数类型是IAsyncResult，明白了AsyncCallback是什么以后，将上面的代码进行如下的改造：

[![](https://common.cnblogs.com/images/copycode.gif#id=YbOlV&originHeight=20&originWidth=20&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)](javascript:void(0);)

![](https://common.cnblogs.com/images/copycode.gif#id=Ie0n5&originHeight=20&originWidth=20&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

```
 1 private void btnAsyncAdvanced_Click(object sender, EventArgs e)
 2 {       
 3     Console.WriteLine($"****************btnAsyncAdvanced_Click Start {Thread.CurrentThread.ManagedThreadId.ToString("00")} {DateTime.Now.ToString("yyyy-MM-dd HH:mm:ss.fff")}***************");
 4     Action<string> action = this.DoSomethingLong;
 5     // 定义一个回调
 6     AsyncCallback callback = p => 
 7     {
 8        Console.WriteLine($"到这里计算已经完成了。{Thread.CurrentThread.ManagedThreadId.ToString("00")}。");
 9     };
10     // 回调作为参数
11     action.BeginInvoke("btnAsyncAdvanced_Click", callback, null);          
12     Console.WriteLine($"****************btnAsyncAdvanced_Click End {Thread.CurrentThread.ManagedThreadId.ToString("00")} {DateTime.Now.ToString("yyyy-MM-dd HH:mm:ss.fff")}***************");
13  }
```

![](https://common.cnblogs.com/images/copycode.gif#id=V4r2T&originHeight=20&originWidth=20&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

[![](https://common.cnblogs.com/images/copycode.gif#id=TF9tq&originHeight=20&originWidth=20&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)](javascript:void(0);)

运行结果：

![](https://images2018.cnblogs.com/blog/1033738/201805/1033738-20180531220700037-2010340340.png#id=Wx0kI&originHeight=136&originWidth=978&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

上面的截图中可以看出，这就是我们想要的效果，而且打印是子线程输出的，但是程序究竟是怎么实现的呢？我们可以进行如下的猜想：

程序执行到BeginInvoke的时候，会申请一个基于线程池的线程，这个线程会完成委托的执行(在这里就是执行DoSomethingLong()方法)，在委托执行完以后，这个线程又会去执行callback回调的委托，执行callback委托需要一个IAsyncResult类型的参数，这个IAsyncResult类型的参数是如何来的呢？鼠标右键放到BeginInvoke上面，查看返回值：

![](https://images2018.cnblogs.com/blog/1033738/201805/1033738-20180531224915455-1536213041.png#id=xpxIg&originHeight=71&originWidth=693&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

发现BeginInvoke的返回值就是IAsyncResult类型的。那么这个返回值是不是就是callback委托的参数呢？将代码进行如下的修改：

[![](https://common.cnblogs.com/images/copycode.gif#id=BKfuM&originHeight=20&originWidth=20&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)](javascript:void(0);)

![](https://common.cnblogs.com/images/copycode.gif#id=whyNG&originHeight=20&originWidth=20&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

```
 1 private void btnAsyncAdvanced_Click(object sender, EventArgs e)
 2 {
 3             // 需求：异步多线程执行完之后再打印出下面这句
 4             Console.WriteLine($"****************btnAsyncAdvanced_Click Start {Thread.CurrentThread.ManagedThreadId.ToString("00")} {DateTime.Now.ToString("yyyy-MM-dd HH:mm:ss.fff")}***************");
 5             Action<string> action = this.DoSomethingLong;
 6             // 无序的
 7             //action.BeginInvoke("btnAsyncAdvanced_Click", null, null);
 8 
 9             IAsyncResult asyncResult = null;
10             // 定义一个回调
11             AsyncCallback callback = p =>
12             {
13                 // 比较两个变量是否是同一个
14                 Console.WriteLine(object.ReferenceEquals(p,asyncResult));
15                 Console.WriteLine($"到这里计算已经完成了。{Thread.CurrentThread.ManagedThreadId.ToString("00")}。");
16             };
17             // 回调作为参数
18             asyncResult= action.BeginInvoke("btnAsyncAdvanced_Click", callback, null);           
19             Console.WriteLine($"****************btnAsyncAdvanced_Click End {Thread.CurrentThread.ManagedThreadId.ToString("00")} {DateTime.Now.ToString("yyyy-MM-dd HH:mm:ss.fff")}***************");
20 }
```

![](https://common.cnblogs.com/images/copycode.gif#id=bjzYm&originHeight=20&originWidth=20&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

[![](https://common.cnblogs.com/images/copycode.gif#id=HfvQr&originHeight=20&originWidth=20&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)](javascript:void(0);)

结果：

![](https://images2018.cnblogs.com/blog/1033738/201805/1033738-20180531225633112-1377569667.png#id=efpJb&originHeight=170&originWidth=981&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

这里可以看出BeginInvoke的返回值就是callback委托的参数。

现在我们可以使用回调解决异步多线程无序的问题了。

2、获取委托异步调用的返回值

使用EndInvoke可以获取委托异步调用的返回值，请看下面的例子：

[![](https://common.cnblogs.com/images/copycode.gif#id=jzbVb&originHeight=20&originWidth=20&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)](javascript:void(0);)

![](https://common.cnblogs.com/images/copycode.gif#id=PEroW&originHeight=20&originWidth=20&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

```
 1 private void btnAsyncReturnVlaue_Click(object sender, EventArgs e)
 2 {
 3        // 定义一个无参数、int类型返回值的委托
 4        Func<int> func = () =>
 5        {
 6              Thread.Sleep(2000);
 7              return DateTime.Now.Day;
 8        };
 9        // 输出委托同步调用的返回值
10        Console.WriteLine($"func.Invoke()={func.Invoke()}");
11        // 委托的异步调用
12        IAsyncResult asyncResult = func.BeginInvoke(p => 
13        {
14             Console.WriteLine(p.AsyncState);
15        },"异步调用返回值");
16        // 输出委托异步调用的返回值
17        Console.WriteLine($"func.EndInvoke(asyncResult)={func.EndInvoke(asyncResult)}");
18 }
```

![](https://common.cnblogs.com/images/copycode.gif#id=Qckus&originHeight=20&originWidth=20&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

[![](https://common.cnblogs.com/images/copycode.gif#id=TqrX4&originHeight=20&originWidth=20&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)](javascript:void(0);)

结果：

![](https://images2018.cnblogs.com/blog/1033738/201805/1033738-20180531233248436-1383129954.png#id=so407&originHeight=92&originWidth=980&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
