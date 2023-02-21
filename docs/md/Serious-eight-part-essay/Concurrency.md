## 1. 线程状态

**要求**

* 掌握 Java 线程六种状态
* 掌握 Java 线程状态转换
* 能理解五种状态与六种状态两种说法的区别

![](BLOG/docs/assets/img/ext-img/线程6种状态.jpg)

`新建——>可运行`当线程new一个时，还是个Java对象，此时还没有和操作系统底层真正的线程关联起来，此时仅是个Java对象，当Java对象调用了线程的start()时，会变成可运行状态，此变了可运行状态后，才会与真正的线程关联起来。里面的代码也会由操作系统交给cpu去执行。

`可运行——>等待`争抢锁成功了，但是进来发现条件不满足，这时候调用wait()方法,持锁线程会进入到waitting状态，想等条件满足再执行，但是不会一直占着锁，应释放开锁，给其他线程去竞争。

`等待——>可运行`当一个处于 WAITING 状态的线程被另一个线程调用 notify() 方法后，该线程会从 WAITING 状态转换为 BLOCKED 状态，等待获得锁。

如果该线程获得了锁，则它会从 BLOCKED 状态转换为 RUNNABLE 状态，可以继续执行。如果该线程依然无法获得锁，则它会继续保持在 BLOCKED 状态，等待获得锁。在这种情况下，线程的状态将会一直保持为 BLOCKED，直到它获得了锁或者等待超时再转成RUNNABLE状态。



## 2.线程池的核心参数

**要求**

* 掌握线程池的 7 大核心参数

**七大参数**

1. corePoolSize 核心线程数目 - 池中会保留的最多线程数
2. maximumPoolSize 最大线程数目 - 核心线程+救急线程的最大数目
3. keepAliveTime 生存时间 - 救急线程的生存时间，生存时间内没有新任务，此线程资源会释放
4. unit 时间单位 - 救急线程的生存时间单位，如秒、毫秒等
5. workQueue - 当没有空闲核心线程时，新来任务会加入到此队列排队，队列满会创建救急线程执行任务
6. threadFactory 线程工厂 - 可以定制线程对象的创建，例如设置线程名字、是否是守护线程等
7. handler 拒绝策略 - 当所有线程都在繁忙，workQueue 也放满时，会触发拒绝策略
   1. 抛异常 java.util.concurrent.ThreadPoolExecutor.AbortPolicy
   2. 由调用者执行任务 java.util.concurrent.ThreadPoolExecutor.CallerRunsPolicy
   3. 丢弃任务 java.util.concurrent.ThreadPoolExecutor.DiscardPolicy
   4. 丢弃最早排队任务 java.util.concurrent.ThreadPoolExecutor.DiscardOldestPolicy
![ ](docs/assets/img/ext-img/线程池核心参数.jpg)

[代码示例👉https://github.com/Fengleitown/BLOG-code](https://github.com/Fengleitown/BLOG-code/tree/main/src/main/java/day02)![](https://badgen.net/github/stars/Fengleitown/fengleitown.github.io?icon=github&color=4ab8a1)中TestThreadPoolExecutor，TestThreadState类

## 3. wait vs sleep
**要求**

* 能够说出二者区别

**一个共同点，三个不同点**

共同点

* wait() ，wait(long) 和 sleep(long) 的效果都是让当前线程暂时放弃 CPU 的使用权，进入阻塞状态

不同点

**一个共同点，三个不同点**

共同点

* wait() ，wait(long) 和 sleep(long) 的效果都是让当前线程暂时放弃 CPU 的使用权，进入阻塞状态

不同点

* 方法归属不同
  * sleep(long) 是 Thread 的静态方法
  * 而 wait()，wait(long) 都是 Object 的成员方法，每个对象都有

* 醒来时机不同
  * 执行 sleep(long) 和 wait(long) 的线程都会在等待相应毫秒后醒来
  * wait(long) 和 wait() 还可以被 notify 唤醒，wait() 如果不唤醒就一直等下去
  * 它们都可以被打断唤醒

* 锁特性不同（重点）
  * wait 方法的调用`必须先获取 wait 对象的锁`，而 sleep 则无此限制
  * wait 方法执行后会释放对象锁，允许其它线程获得该对象锁（我放弃 cpu，但你们还可以用）
  * 而 sleep 如果在 synchronized 代码块中执行，并不会释放对象锁（我放弃 cpu，你们也用不了）

[代码示例👉https://github.com/Fengleitown/BLOG-code](https://github.com/Fengleitown/BLOG-code/tree/main/src/main/java/day02)中WaitVsSleep
## 4.lock vs synchronized

**要求**

* 掌握 lock 与 synchronized 的区别
* 理解 ReentrantLock 的公平、非公平锁
* 理解 ReentrantLock 中的条件变量

**三个层面**

不同点

* 语法层面
  * synchronized 是关键字，源码在 jvm 中，用 c++ 语言实现
  * Lock 是接口，源码由 jdk 提供，用 java 语言实现
  * 使用 synchronized 时，退出同步代码块锁会自动释放，而使用 Lock 时，需要手动调用 unlock 方法释放锁
* 功能层面
  * 二者均属于悲观锁、都具备基本的互斥、同步、锁重入功能
    - 互斥：多个线程争抢同一把锁，只有一个线程能成功，其他线程都失败，失败的陷入阻塞状态。
    - 同步：多个线程同时运行，当一个线程运行中需要其线程结果时，要等待那个线程的结果再往下运行。
      - 实现同步：synchronized可以用wait、notify来实现同步。Lock可以用条件变量eg：signal
    - 锁重入：持锁的线程能不能对这个对象重复加锁，eg：一个线程已经对一个对象加上锁了，接下来能不能继续对对象加第二道锁、第三道锁，可以则是可重入锁。加多少到解锁就要解多少道。
  * Lock 提供了许多 synchronized 不具备的功能，例如获取等待状态、公平锁、可打断、可超时、多条件变量
    - 等待状态：eg:线程阻塞的时候，可以知道哪些线程阻塞住了。（c++偏底层，实现起来不易，Lock用java写的，有相应接口可实现）
    - 公平锁：多个线程争抢锁失败后，将来再抢锁时，是先来先得还是可以插队的，先来先得就是公平，synchronized只支持非公平锁，Lock支持公平和非公平。注：非公平锁效率更高。
    - 可打断： 等不到锁就不要这个锁了。可超时：在时间内等不到锁就不要这个锁了。
    - 多条件变量：多个等待对流。synchronized只有一个条件变量，即一个等待队列，Lock有多个。
  * Lock 有适合不同场景的实现，如 ReentrantLock（基本的可重入锁）， ReentrantReadWriteLock（适合读多写少），synchronized只有一种实现。
* 性能层面
  * 在没有竞争/竞争很多少时，synchronized 做了很多优化，如偏向锁、轻量级锁，性能不赖
  * 在竞争激烈时，Lock 的实现通常会提供更好的性能

**公平锁**

* 公平锁的公平体现
  * **已经处在阻塞队列**中的线程（不考虑超时）始终都是公平的，先进先出
  * 公平锁是指**未处于阻塞队列**中的线程来争抢锁，如果队列不为空，则老实到队尾等待
  * 非公平锁是指**未处于阻塞队列**中的线程来争抢锁，与队列头唤醒的线程去竞争，谁抢到算谁的
* 公平锁会降低吞吐量，一般不用

**条件变量**

* ReentrantLock 中的条件变量功能类似于普通 synchronized 的 wait，notify，用在当线程获得锁后，发现条件不满足时，临时等待的链表结构
* 与 synchronized 的等待集合不同之处在于，ReentrantLock 中的条件变量可以有多个，可以实现更精细的等待、唤醒控制

