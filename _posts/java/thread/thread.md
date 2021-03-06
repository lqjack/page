---
layout: post
title: 线程
date: 2016-02-24 22:44
image: /assets/images/markdown.jpg
headerImage: false
tag:
- Java
- Thread
star: true
category: blog
author: Jack
description: 线程
---

<small>多线程同时读写同一个对象，若不需要额外的同步，称为线程安全。</small>

### 线程安全的实例：
1. 无状态类，不包含其他类的引用。每次构建一个独立的对象，直到生命周期结束，对象属性不发生变更。（引用变化？）
2. 原子操作，其中自增不是线程安全。
3. 存在竞争的对象，使用检查-通过-运行。


### 保证线程安全的方法：
1. 内置锁
	<p>Java提供内置的强制同步的synchronized块，遵循检查-通过-运行原则。</p>
2. 同步块
	
3. 共享对象
* （1）内存可见性
	每个线程对象操作对象时候，是针对当前线程内的副本进行操作处理，若不与内存状态进行同步，则会出现状态不一致。
	CPU运行时候，是基于基本操作单元进行操作的，若不与内存对象同步，CPU内的操作对象状态与其他CPU处理对象状态会出现不一致。
	默认情况下，编译器,JIT以及处理器会优化处理流程，操作顺序不一定遵循实际业务逻辑。
* （2）失效数据
		缺少同步产生的错误
* （3）加锁和可见性
		加锁可以以可以预测的方式查看运行的结果。
* （4）发布与逸出
	
* （5）线程封闭
		不使用共享数据，在线程内部使用
* （6）不变量
		对象创建出来后（通过构造方法）无论如何对此对象进行非暴力操作（不用反射），此对象的状态（实例域的值）都不会发生变化，那么此对象就是不可变的，相应类就是不可变类，跟是否用 final 修饰没关系。
	
## 基本策略
* 线程封闭。线程封闭的对象只能由一个线程拥有。
* 只读共享。在没有额外同步情况下，共享的只读对象可以由多个线程并发访问。
* 线程共享安全。线程安全的对象在其内部实现同步。因此多个线程可以通过对象的公有接口来访问而不需要进一步的同步。


### 对象组合
####（1）设计线程安全类
* 找出构造对象状态的所有变量。
* 约束状态变量的不变性条件。
* 建立对象状态的并发访问管理策略。
####（2）对象封闭
* Collections.synchronizedList(list)、Collections.synchronizedMap(m)
####（3）对象监视器
* 所有可变对象封装起来，由锁来维护

### 基础构建块
##### （1）同步容器
* synchronizedList
#####（2）同步工具
* 阻塞队列，调节生产者、消费者的处理速度适配问题
* 信号量，控制同时操作对象的数量

##### 任务执行
* 确定任务的边界，任务之间独立，没有依赖。


#### a、执行策略：
任务在什么（What）线程中执行
任务以什么（What）顺序执行（FIFO/LIFO/优先级等）
同时有多少个（How Many）任务并发执行
允许有多少个（How Many）个任务进入执行队列
系统过载时选择放弃哪一个（Which）任务，如何（How）通知应用程序这个动作
任务执行的开始、结束应该做什么（What）处理

#### b、线程池：
线程池和工作者队列密切相关，工作者线程的任务：从工作队列中获取一个任务，执行任务，然后返回线程池并等待下一个任务。
Executors类里面提供了一些静态工厂，生成一些常用的线程池。
newSingleThreadExecutor：创建一个单线程的线程池。这个线程池只有一个线程在工作，也就是相当于单线程串行执行所有任务。如果这个唯一的线程因为异常结束，那么会有一个新的线程来替代它。此线程池保证所有任务的执行顺序按照任务的提交顺序执行。
newFixedThreadPool：创建固定大小的线程池。每次提交一个任务就创建一个线程，直到线程达到线程池的最大大小。线程池的大小一旦达到最大值就会保持不变，如果某个线程因为执行异常而结束，那么线程池会补充一个新线程。
newCachedThreadPool：创建一个可缓存的线程池。如果线程池的大小超过了处理任务所需要的线程，那么就会回收部分空闲（60秒不执行任务）的线程，当任务数增加时，此线程池又可以智能的添加新线程来处理任务。此线程池不会对线程池大小做限制，线程池大小完全依赖于操作系统（或者说JVM）能够创建的最大线程大小。
newScheduledThreadPool：创建一个大小无限的线程池。此线程池支持定时以及周期性执行任务的需求。
newSingleThreadScheduledExecutor：创建一个单线程的线程池。此线程池支持定时以及周期性执行任务的需求。

#### c、线程池Executor任务拒绝策略
java.util.concurrent.RejectedExecutionHandler描述的任务操作。
第一种方式直接丢弃（DiscardPolicy）
第二种丢弃最旧任务（DiscardOldestPolicy）
第三种直接抛出异常（AbortPolicy）
第四种任务将有调用者线程去执行(CallerRunsPolicy)

#### d、生命周期
java.util.concurrent.ExecutorService 接口对象来执行任务，该接口对象通过工具类java.util.concurrent.Executors的静态方法来创建。 Executors此包中所定义的 Executor、ExecutorService、ScheduledExecutorService、ThreadFactory 和 Callable 类的工厂和实用方法。
ExecutorService扩展了Executor并添加了一些生命周期管理的方法。一个Executor的生命周期有三种状态，运行 ，关闭 ，终止。Executor创建时处于运行状态。当调用ExecutorService.shutdown()后，处于关闭状态，isShutdown()方法返回true。这时，不应该再想Executor中添加任务，所有已添加的任务执行完毕后，Executor处于终止状态，isTerminated()返回true。
shutdown()：执行平缓的关闭过程，不再接受新的任务，同时等待已经提交的任务执行完成。
shutdownNow();执行粗暴的关闭过程，尝试取消所有运行中的任务，并且不再启动队列中尚未开始启动的任务。
awaitTermination： 这个方法有两个参数，一个是timeout即超时时间，另一个是unit即时间单位。这个方法会使线程等待timeout时长，当超过timeout时间后，会监测ExecutorService是否已经关闭，若关闭则返回true，否则返回false。一般情况下会和shutdown方法组合使用。


### 3、携带结果的任务callable 和Future

七：取消和关闭
要想使任务和线程安全、快速、可靠的停下来，并不是件容易的事情，java没有提供任何机制来安全终止线程。但他提供了中断（Interruption），一种协作机制，能使线程终止另外一个线程的当前工作。
任务取消
中断
中断策略
响应中断

八、线程池的使用
参考：http://blog.csdn.net/cdl2008sky/article/details/17719715

九、避免活跃性危险
我们使用加锁机制来确保线程安全，但如果过度使用，会导致顺序死锁(Lock-Ordering Deadlock)。我们使用线程池和信号量来限制对资源的限制。但这些被限制的行为可能导致资源死锁(Resource Deadlock)。
死锁：每个人拥有其他人需要的资源，同时等待其他人已经拥有的资源，并且每个人在获取所有需要的资源之前都不放弃已经拥有的资源。
饥饿：当线程无法访问到它所需要的资源而不能继续执行时，就会发生饥饿。引发饥饿的最常见资源就是CUP的时钟周期。
活锁：liveLock。改问题尽管不会阻塞线程，但也不能继续执行，因为线程将不断重复执行相同的操作，而且总会失败。

十、性能与可伸缩性
可伸缩性：当增加计算资源时，(CPU，内存，存储容量和I/O宽带)，程序的吞吐量和处理能力响应的增加。
吞吐量：指一组并发任务中已完成任务所占的比例。
响应性：指请求从发出到完成所需要的时间。
引入线程存在的开销：
(1)、上下文切换。cpu在做线程切换的时候，需要保存当前线程执行的上下文，并且新调度进来的线程执行上下文设置为当前上下文。发生越多的上下文切换，增加了调度开销，并因此降低吞吐量。
(2)、内存同步。synchronized 发生锁竞争的地方带来的开销会影响其它线程的性能。
(3)、阻塞。当在锁上发生竞争时，竞争失败的线程会阻塞，JVM 通过循环，不断的尝试获取锁，直到成功。或者通过操作系统挂起阻塞的线程。如果时间短，采用等待方式，如果时间长才适合采用线程挂起的方式。
串行操作降低可伸缩性，并行切换上下文也会降低性能。在锁发生竞争时，会同时导致上面两种问题，因此，减少锁的竞争能够提高性能和收缩性。在并发程序中，对可伸缩性最主要的威胁就是独占方式的资源锁。
两个因素将影响锁上面发生竞争的可能性：锁的请求频率以及每次持有该锁的时间。如果两者的乘积很小，那么大多数获取锁操作都不会发生竞争。
三种方式可以降低锁的竞争程度：
(1)、降低锁的请求频率。
降低线程请求锁的频率，可以通过锁分解和锁分段等技术来实现。即减小锁的粒度。如果一个锁同时需要保护好几个状态变量，那么可以把这个锁分解成多个锁，并且每个锁只保护一个状态变量，从而提高可伸缩性，并最终降低每个锁的请求频率。但是使用的锁越多，发生死锁的风险也会越高。

(2)、减少锁的持有时间。
缩小锁的范围(快进快出)，可以将一些与锁无关的代码移出同步代码块，尤其是开销较大的操作，以及可能被阻塞的操作，比如I/O 操作。

(3)、放弃使用独占锁，并发容器，读-写锁，不可变对象以及原子变量，

显示锁
(1)、轮询锁( tryLock())
(2)、定时锁(tryLock(timeout, NANOSECONDS))。如果操作不能在指定的时间内给出结果，那么就会使程序提前结束
(3)、定时以及可中断的锁(lockInterruptibly)
(4)、读写锁(ReadWriteLock)。可以被多个读者访问或者被一个写者访问。
ReentrantLock 每次只能一个线程访问加锁的数据，从而达到维护数据完整性的目的。通过这种策略可以避免写/写和写/读冲突。但是也同时避免了读/读冲突。但是有些时候读操
作是可以被并发进行的。所以需要读写锁。



