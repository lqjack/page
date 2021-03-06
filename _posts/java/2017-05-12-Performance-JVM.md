---
layout: post
title: JVM架构
date: 2016-02-24 22:44
image: /assets/images/markdown.jpg
headerImage: false
tag:
- JVM
- Performance
star: true
category: blog
author: Jack
description: JVM架构
---

# JVM内存分配与使用受限于物理内存以及操作系统
`-XX:UserCompressedOops` 压缩指针
`-XX:+AggressiveOpts` 添加设置额外的性能优化


# VM生命周期：

+ 启动器
<p>java ,javaw (windows platform),JNI_CreateJavaJVM,javaws (java web start)</p>
	
+ 解析命令行
<p>-client/-server ,决定加载加载哪个JIT
	设置堆大小和JIT编译器
	若没有明确设置堆大小和JIT编译器，启动器会通过自动优化进行设置。
	设置环境变量（LD_LIBARY_PATH,CLASSPATH)
	若存在-jar ,启动器会从指定的JAR的manifest中查找Main-Class,否则从命令行读取
	使用标准的Java本地接口(JNI)创建	HotSpot VM。
	一旦创建并初始化HotSpot VM,就会加载Java Main-Class,启动器也会从中得到main方法参数。
	通过JNI调用CallStaticVoidMethod调用Java main方法，将命令行传递。
</p>
	
	
# 类加载
<p>对于给定的Java类或者接口，类加载会根据全称加载二进制类文件，定义Java类，然后创建类或接口的Java.lang.Class对象。若未能找到，则跑出NoClassDefFound。此外，类加载会对类的格式进行检查，若出错，则抛出ClassFormatError或UnSupportedClassVersionError。
Java类加载器前，必须先加载所有超类和接口。
若类的继承关系出错，抛出ClassCircularityError。若超类是接口或者接口是类，抛出InCompatibleClassChangeError
</p>	
* 链接
<p>检查文件的语义，常量池符号和类型，若出错抛出VerifyError.</p>
* 准备
<p>创建静态字段，初始化为默认值，分配方法表</p>
* 解析
<p>可选，</p>
* 初始化
<p>运行类构造器（需要先初始化超类，接口不需要）</p>
	
*基于性能考量，直到初始化阶段，才会加载和链接类*

# 类加载委托
<p>请求类加载器查找个加载某个类时，可以转而请求别的加载器来加载，称为加载委托。	
	类的首个加载器为初始加载器，最终定义类的加载器为自定义类加载器。
	类加载器之间是层次化关系，每个类加载器都一个委托其上一级的类加载器。
	J2SE的加载顺序为：启动类加载器、扩展类加载器、系统加载器。其中系统类加载器是默认加载器，加载Main方法并从classpath加载类。
	应用加载器是可以是自带加载器，也可以进行扩展。
	启动类加载器由HotSpot VM实现，负责加载BOOTCLASSPATH中的类。
	为加快启动顺序，Client模式可以通过类数据共享使用已经加载的类（-Xshare:on,仅Serial支持）
</p>
	
	
# 类型安全
<p>Java类由类加载器和全限量名称类共同决定。</p>

# 元数据
<p>类加载时，会在永久代创建instanceKlass(arrayKlass)。instanceKClass引用java.lang.Class,后者是前者的镜像，VM内部使用klassOop(普通对象指针)访问instanceKlass</p>

# 内部类加载数据
<p>加载过程，VM维护三个散列表，建立类名/类加载器（初始加载器和自定义加载器）/klassOop对象映射。PlaceHolderTable包含当前正在加载的类，用于检查ClassCircularityError。LoaderConstraintTable用于追踪类型安全检查的约束</p>

# 字节码验证
<p>静态验证和动态验证。
	有些参数个数和参数类型在运行时候动态分析代码，方法如下：
		方法推导：
			对每个字节码进行抽象解释并在目标分支上进行类型合并。若未发现隐形类型或者适配，抛出VerfiyError。
		类型检查：
			编译器将目标分支或异常分支设置在code属性的StackMapTable中。StackMapTable包含多个栈映射帧，每个映射都会用字节码偏移量表示栈和局部变量的类型。
</p>

# 数据共享
<p>使用JRE时，安装程序会加载Jar中部分类，变成私有的内部表示并转储文件，称为共享文档。若没有该文件，也可以手动生成，之后调用VM，共享文档会映射到内存，减少加载数量。
	VM在共享代引入新的子空间，用以包含共享数据，VM启动时，共享文档classes.jsa作为内存映射加载到永久代，随后VM内存管理系统管理该区域。
</p>
	
# 解释器
<p>基于模板解释器，内部使用TemplateTable在内存中生成解释器，包含每个字节对应的机器代码，每个模板对应一个字节码。该方式由于switch.
VM解释器自适应优化的一部分，直接用解释器运行程序，并检测热点，使用全局机器代码优化。
</p>

# 异常处理
<p>遇到与Java语义约束冲突，会触发异常处理程序。由VM解释器、JIT解释器和VM协同完成。处理方式：
	本方法处理
		同一方法抛出、捕获异常
	方法外处理
		退栈后调用异常处理程序
	VM处理
		VM查找异常处理程序
</p>

# 同步
<p>并发操作机制，用于预防、避免对资源的不当的交替使用（竞争），保证交替使用资源安全。Java使用线程实现并发。互斥是特殊的同步机制（同一时间仅最多有一个线程访问资源）。
	VM使用monitor保证运行代码的互斥，可以解锁或加锁，任意时刻仅有一个线程获得monitor所有权（由synchronized block）。
	线程去试图锁定拥有被线程占用的monitor时，等待直到所有者是否锁，才能获得。
	大多数对象在生命周期最多只有一个线程，可以开启-XX:UseBiasedLocking允许线程自身使用偏向锁，开启后就不需要昂贵的原子操作来维护。
	VM中由C1（-client JIT编译器）、C2 (-server JIT编译器)和解释器。在没有竞争情况下直接生产fast-path；需要阻塞和解锁(monitor-enter,monitor-exit),fast-path调用slow-path(c++实现)。
	
	VM内部表示的Java对象第一个字(word)包含同步状态编码：
		中立：已经解锁
		偏向：已经锁定/已经解锁且无共享
		栈锁：已锁定且共享（共享意思是该标记指向锁对象在线程中标记子副本）
		膨胀：已经锁定/已经解锁且共享和竞争。线程在moniter-enter(wait)阻塞，指向重的Object-monitor对象。
</p>
	
# 线程对象
## 线程管理：

<p>从创建到终止的整个生命周期、HotSpot VM线程间协调。线程管理实现细节和平台相关。包括：
	Java代码创建的线程
	直接与HotSpot VM关联的线程
	HotSpot其他目的建立的线程
</p>

## 线程模型：
<p>Java程序启动时候会创建一个本地操作的系统线程，当终止时，操作系统线程也会被回收。不同平台实现中的线程优先级与Java的映射不尽相同。</p>
		
## 线程创建：
### VM引入线程方式：
<p>java.lang.Thread对象的start()
JNI将已经存在的本地线程关联到VM上。</p>
		
### 线程表示：
<p>java.lang.Thread实例以Java代码表示
	VM内部以C++ JavaThread实例表示java.lang.Thread实例，其中包含其他线程的追踪状态。JavaThread内部指针指向Thread对象，也保存了OSThread的引用；Thread对象包含JavaThread的原始整数(Raw Int)。
	OSThread表示操作系统线程。
</p>
		
### 线程启动过程：
<p>-》java.lang.Thread启动
	-》VM使用attachCurrentThread关联本地线程，创建关联的JavaThread和OSThread，本地线程，
	-》VM分配资源（本地存储和缓存、同步对象等）
	-》本地线程初始化
	-》Thead.run()
	-》线程返回，处理未被捕获的异常
	-》终止线程，检测是否终止VM
	-》终止线程会是否分配的资源，移除JavaThread
	-》调用OSThread和Java的析构函数
	-》终止
</p>
		
### 线程状态：
<p>	新线程：线程在初始化过程中
	线程在Java中：在执行Java代码
	线程在VM中：在VM中执行
	线程阻塞：由于（获取锁、阻塞IO等待条件等）被阻塞
	Monitor_wait:等待获取竞争的监视锁
	Condition_wait:等待VM的内部条件变量
	Object_wait:Java线程在执行Object.wait()</p>
	
### VM内部线程：
<p>	VM线程：C++单例对象，负责VM操作
	周期任务操作：C++单例对象（WatchedThread）,模拟计时器中断在VM中可以周期操作
	垃圾回收线程：
	JIT编译器线程：运行时编译，编译为字节码
	信号分发线程：</p>

### VM操作和安全点：
<p>	VM内部VMThread监控VMOperationQueue的C++对象，等待对象出现VM操作，操作等到安全点才执行，所有的线程都阻塞。
	垃圾回收是最出名的VM安全点操作（Stop-world阶段）、偏向锁撤销、线程栈转储、线程的挂起和停止以及JVMTI请求检查和更改操作。
	VM通过协作、轮询创建安全点。
	在安全点，VMThread用Threads_locks阻塞所有正在运行的线程，VM操作完成后，释放Threads_lock。</p>
	
	
## C++堆管理
<p>VM使用C++堆存储VM内部结构和数据。Arena的一些子类负责管理VM C++堆操作，只提供给VM 使用，不会暴露给VM使用者。
	Arena子类家里的malloc/free之上的一层，包含3个全局的ChunkPool，安装请求分配的大小由不同的部分高效操作。
	Arena是一些缓存了一定数量内存空间的ThreadLocal对象。由于不需要共享全局锁，这就使得快速路径的分配成为可能。
	Arena还用于ThreadLocal的资源和句柄管理。client和server模式的JIT compilers都使用Arena。<<p>p>
			

## Java本地接口
<p>运行VM可以使用Java语言和其他语言进行协作。
	JNI可以用来创建、检测和更新Java对象，调用Java对象、捕获并抛出异常、加载类并获取类信息以及执行运行时类型检查。
	JNI和Invocation API，任意本地应用都可以嵌入Java VM。
	但是，丧失平台独立性；Java时强语言类型，C/C++不是，调用前会进行安全检查。
	VM命令行选项（-Xcheck：jni）可以辅助调试JNI本地方法。
	VM追踪执行本地方法的线程必须小心。VM线程执行本地代码到达安全点时，继续执行本地代码，直到返回Java代码或JNI调用结束。</p>
	
	
## VM致命错误处理：
<p>VM为开发者提供足够信息在诊断和修复VM致命错误。会产生hs_err_pid<pid>.log,包括内存镜像，执行输出位置-XX：ErrorFile;OOE也可以触发生成文件。
	JKD1.6后，-XX:OnOutOfMemeoryError=<cmd> 抛出OOE时，执行<cmd>。
	-XX：HeapDumpOnOutOfMemory -XX：HeapDump-Path=<path> 指定转储的文件路径</p>
	
	
# 垃圾收集器
* Serial收集器
* Parallel收集器
* CMS
* G1

# JIT
