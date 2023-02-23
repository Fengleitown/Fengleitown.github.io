## 1. 线程状态

面试题：线程有哪些状态？

**要求**

* 掌握 Java 线程六种状态
* 掌握 Java 线程状态转换
* 能理解五种状态与六种状态两种说法的区别

![](/assets/img/ext-img/线程6种状态.jpg)

`新建——>可运行`当线程new一个时，还是个Java对象，此时还没有和操作系统底层真正的线程关联起来，此时仅是个Java对象，当Java对象调用了线程的start()时，会变成可运行状态，此变了可运行状态后，才会与真正的线程关联起来。里面的代码也会由操作系统交给cpu去执行。

`可运行——>等待`争抢锁成功了，但是进来发现条件不满足，这时候调用wait()方法,持锁线程会进入到waitting状态，想等条件满足再执行，但是不会一直占着锁，应释放开锁，给其他线程去竞争。

`等待——>可运行`当一个处于 WAITING 状态的线程被另一个线程调用 notify() 方法后，该线程会从 WAITING 状态转换为 BLOCKED 状态，等待获得锁。

如果该线程获得了锁，则它会从 BLOCKED 状态转换为 RUNNABLE 状态，可以继续执行。如果该线程依然无法获得锁，则它会继续保持在 BLOCKED 状态，等待获得锁。在这种情况下，线程的状态将会一直保持为 BLOCKED，直到它获得了锁或者等待超时再转成RUNNABLE状态。



## 2.线程池的核心参数

面试题：线程池的核心参数。

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

![](/assets/img/ext-img/线程池核心参数.jpg)

>[代码示例👈](https://github.com/Fengleitown/BLOG-code/tree/main/src/main/java/day02)![](https://badgen.net/github/stars/Fengleitown/fengleitown.github.io?icon=github&color=4ab8a1)中TestThreadPoolExecutor，TestThreadState类

## 3. wait vs sleep
面试题：对比sleep和wait方法。

**要求**

* 能够说出二者区别

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

>[代码示例👈](https://github.com/Fengleitown/BLOG-code/tree/main/src/main/java/day02)中WaitVsSleep

## 4.lock vs synchronized

面试题：对比lock 和 synchronized

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
    - 悲观锁：一种比较保守(悲观)的锁机制，它假设在多线程环境下，任何时候都可能会有其他线程对共享数据进行修改，因此在访问共享数据时会先进行加锁。
    - 乐观锁：它假设在一段时间内，数据不会被其他线程修改，因此在访问共享数据时不会对其进行加锁，而是通过版本控制的方式来保证数据的一致性和安全性。
    - 互斥：多个线程争抢同一把锁，只有一个线程能成功，其他线程都失败，失败的陷入阻塞状态。
    - 同步：多个线程同时运行，当一个线程运行中需要其线程结果时，要等待那个线程的结果再往下运行。
      - 实现同步：synchronized可以用wait、notify来实现同步。Lock可以用条件变量eg：signal
    - 锁重入：持锁的线程能不能对这个对象重复加锁，eg：一个线程已经对一个对象加上锁了，接下来能不能继续对对象加第二道锁、第三道锁，可以则是可重入锁。加多少到解锁就要解多少道。
  * Lock 提供了许多 synchronized 不具备的功能，例如获取等待状态、公平锁、可打断、可超时、多条件变量
    - 等待状态：eg:线程阻塞的时候，可以知道哪些线程阻塞住了。（c++偏底层，实现起来不易，Lock用java写的，有相应接口可实现）
    - 公平锁：多个线程争抢锁失败后，将来再抢锁时，是先来先得还是可以插队的，先来先得就是公平，synchronized只支持非公平锁，Lock支持公平和非公平。注：非公平锁效率更高。
    - 可打断： 等不到锁就不要这个锁了。可超时：在时间内等不到锁就不要这个锁了。
    - 多条件变量：多个等待对列。synchronized只有一个条件变量，即一个等待队列，Lock有多个。
  * Lock 有适合不同场景的实现，如 ReentrantLock（基本的可重入锁）， ReentrantReadWriteLock（适合读多写少），synchronized只有一种实现。
* 性能层面
  * 在没有竞争/竞争很多少时，synchronized 做了很多优化，如偏向锁、轻量级锁，性能不赖
    - 偏向锁：偏向锁的思路是在没有锁竞争的情况下，把对象偏向第一个访问它的线程，使得该线程在以后访问同一个对象时可以不必再去竞争锁。
    - 轻量级锁：
  * 在竞争激烈时，Lock 的实现通常会提供更好的性能
**公平锁**

* 公平锁的公平体现
  * **已经处在阻塞队列**中的线程（不考虑超时）始终都是公平的，先进先出
  * 公平锁是指**未处于阻塞队列**中的线程来争抢锁，如果队列不为空，则老实到队尾等待
  * 非公平锁是指**未处于阻塞队列**中的线程来争抢锁，与队列头唤醒的线程去竞争，谁抢到算谁的
* 公平锁会降低吞吐量，一般不用

**条件变量**

* ReentrantLock 中的条件变量功能类似于普通 synchronized 的 wait，notify，用在当线程获得锁后，发现条件不满足时，临时等待的链表结构。eg：此时t2获得锁，但是不具备往下运行的条件参数，此时可以c2.await()，放到c2的waiting queue队列中。后面条件满足可以继续运行时，可以c2.signal()唤醒c2中waiting queue队列的线程，被唤醒后的线程进入到blocked queue尾端。
* 与 synchronized 的等待集合不同之处在于，ReentrantLock 中的条件变量可以有多个，可以实现更精细的等待、唤醒控制
>[代码示例👈](https://github.com/Fengleitown/BLOG-code/tree/main/src/main/java/day02)中TestReentrantLock

## 5. volatile

面试题：volatile能否保证线程安全？

1.线程安全要考虑三个方面：**可见性、有序性、原子性**。

- 可见性指：一个线程对共享变量修改，另一线程能看到最新的结果。
- 有序性指：一个线程内代码按编写顺序执行
- 原子性指：一个线程内多行代码以一个整体运行，期间不能有其它线程代码插队

2.volatile能保证共享变量的可见性与有序性，但并不能保证原子性

**原子性**
* 起因：多线程下，不同线程的**指令发生了交错**导致的共享变量的读写混乱
* 解决：用悲观锁或乐观锁解决，volatile 并不能解决原子性

**可见性**

* 起因：由于**编译器优化、或缓存优化、或 CPU 指令重排序优化**导致的对共享变量所做的修改另外的线程看不到
* 解决：用 volatile 修饰共享变量，能够防止编译器等优化发生，让一个线程对共享变量的修改对另一个线程可见

**有序性**

* 起因：由于**编译器优化、或缓存优化、或 CPU 指令重排序优化**导致指令的实际执行顺序与编写顺序不一致
* 解决：用 volatile 修饰共享变量会在读、写共享变量时加入不同的屏障，阻止其他读写操作越过屏障，从而达到阻止重排序的效果
* 注意：
  * **volatile 变量写**加的屏障是阻止上方其它写操作越过屏障排到 **volatile 变量写**之下
  * **volatile 变量读**加的屏障是阻止下方其它读操作越过屏障排到 **volatile 变量读**之上
  * volatile 读写加入的屏障只能防止同一线程内的指令重排

> ***代码说明***

>[代码示例👈](https://github.com/Fengleitown/BLOG-code/tree/main/src/main/java/day02.threadsafe)中AddAndSubtract 演示原子性
>[代码示例👈](https://github.com/Fengleitown/BLOG-code/tree/main/src/main/java/day02.threadsafe)中ForeverLoop 演示可见性
>       注意：本例经实践检验是编译器优化导致的可见性问题
>[代码示例👈](https://github.com/Fengleitown/BLOG-code/tree/main/src/main/java/day02.threadsafe)中Reordering 演示有序性
>  * 需要打成 jar 包后测试
>* [可同时参考视频讲解👈](https://www.bilibili.com/video/BV15b4y117RJ?p=79&vd_source=add76bce03794ff30f98753a5213643b)



volatile是使用`内存屏障`来解决指令重排序，使用volatile修饰变量后，读和写的操作会分别加入不同的内存屏障。读时，内存屏障是这样的∨,阻止上面的代码越过屏障排上去，写时,内存屏障是这样∧，阻止下面代码越过屏障排下来。所以用volatile关键字修饰变量时，如果是读操作，修饰下面的变量，写操作，修饰上面的变量。

eg: x=1;													volatile x=1;

​	volatile  y=1;		这是读操作		    y=1;		  		这是写操作

## 6. 悲观锁 vs 乐观锁

面试题：

要求**

* 掌握悲观锁和乐观锁的区别

**对比悲观锁与乐观锁**

* 悲观锁的代表是 synchronized 和 Lock 锁，使用锁保证同步、原子性。
  * 其核心思想是【线程只有占有了锁，才能去操作共享变量，每次只有一个线程占锁成功，获取锁失败的线程，都得停下来等待】
  * 线程从运行到阻塞、再从阻塞到唤醒，涉及线程上下文切换，如果频繁发生，影响性能
  * 实际上，线程在获取 synchronized 和 Lock 锁时，如果锁已被占用，都会做几次重试操作，`减少阻塞`的机会

* 乐观锁的代表是 AtomicInteger，使用 cas 来保证原子性

  * 其核心思想是【无需加锁，每次只有一个线程能成功修改共享变量，其它失败的线程不需要停止，不断重试直至成功】
  * 由于线程一直运行，不需要阻塞，因此不涉及线程上下文切换
  * 它需要多核 cpu 支持，且线程数不应超过 cpu 核数

CAS一般和volatile一起使用。CAS自旋也没啥，就是一个while循环里面包了一个CAS，成功就break，失败继续while。paper tiger! 🐅 

主要方法就是 👇 

```java
account:一个对象	BALANCE:静态变量	old:旧值	news:新值	
U.compareAndSetInt(account, BALANCE, old, news)
//防止多线程情况下静态变量被篡改或者是覆盖新值，就引用了这个方法.
//拿到静态变量balance的值，与old多比较，相同则说明没有其他线程来改变静态变量的值。则我可以修改成功。
//如果拿到静态变量balance的值!=old，则compareAndSetInt()就修改失败
```

>[代码示例👈](https://github.com/Fengleitown/BLOG-code/tree/main/src/main/java/day02)中SyncVsCas


## 7. Hashtable vs ConcurrentHashMap

*面试题：Hashtable与ConcurrentHashMap有什么区别？*

1.Hashtable与ConcurrentHashMap都是线程安全的集合

2.key和value都不能为空

3.Hashtable并发度低，整个Hashtable对应一把锁，同一时刻，只能有一个线程操作它，其他线程到阻塞队列等待。

4.1.8开始之前ConcurrentHashMap使用了Segment+数组+链表的结构，每个Segmen对应一把锁，如果多个线程访问不同的Segment，则不会冲突。

5.1.8开始ConcurrentHashMap的数据结构类似HashMap，数组+链表+红黑树，将数组的每个头节点作为锁，如果多个线程访问的头节点不同，则不会冲突。

- 演示并发put。
- 演示并发扩容，说明三个问题forwardingNode，扩容时的get,扩容时的put。

**HashTable**

初始值clevel：11，扩容：容量乘2+1，加载因子：0.75。不需要二次hash，容量不是2的倍数，有比较好的hash分散性，不需要2次hash。

指定初始值clevel后segment数组大小就指定`不变`了，加载因子只影响小数组的扩容。

可以看到直接算出正整数的最大位最大值，然后直接取模运算了。

![](/assets/img/ext-img/hashTable.jpg)

