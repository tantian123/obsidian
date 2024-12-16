volatile 的作用是保证可见性和有序性 是通过在变量的读写操作前后加上屏障保证的，因为cpu和编译器会对指令进行重排序 同时cpu由于缓存的问题会出现可见性问题，通过加入屏障 会阻止指令的重排和强制缓存刷新和同步 保证有序性和可见性 虽然存在mesi协议 但是CPU对INVAILD消息的发送和接受过程进行了异步的优化，对于缓存行的修改使用了buffer 对于INVAILD消息的接受执行使用了queue 将这两个操作异步了 大大提高了效率，但是也产生了问题，X86架构下默认保证了读读 读写 写写的内存屏障 但是不保证写读 ，于是JMM 就通过 volatile关键字，对这个变量的读写前后都会加上屏障，原理是编译时加上带有LOCK前缀指令（ 锁总线或者是缓存行 达到读写屏障的效果 并不等同） 强制buffer的刷入和queue中消息的执行，保证了可见性 ，同时屏障也会阻碍指令的重排 ，保证了有序性

synchronized 和lock区别？synchronized 怎么加锁的底层

olatile？MESI？为什么MESI还要volatile？（语言层面volatile，lock前缀？系统层面MESI，JMM模型映射，内存屏障

volatile 关键字是用来解决内存可见性和指令重排序(编译器和CPU)问题

保证可见性 保证有序性，禁止指令重排 不保证原子性（需要借助synchronized或者CAS)

有序性：通过内存屏障禁止指令重排序 ->编译过程中关键是使用lock关键字，使得本线程的CPU的cache写入内存 可见性：

Thread A: SET DATA=1 SET FLAG=TRUE Thread B: IF FLAG: PRINT(DATA) 分别执行了LOAD STORE指令

考虑编译器乱序和CPU乱序

因为内存的速度和 CPU 匹配不上，所以在内存和 CPU 之间加了多级缓存。 单核 CPU 独享不会出现数据不一致的问题，但是多核情况下会有缓存一致性问题。 缓存一致性协议有两种实现方式，一个是基于目录的，一个是基于总线嗅探的。 基于目录的方式延迟高，但是占用总线流量小，适合 CPU 核数多的系统。 基于总线嗅探的方式延迟低，但是占用总线流量大，适合 CPU 核数小的系统。 常见的 MESI 协议就是基于总线嗅探实现的。 MESI 解决了缓存一致性问题，但是还是不能将 CPU 性能压榨到极致。 为了进一步压榨 CPU，所以引入了 store buffer 和 invalidate queue store buffer 和 invalidate queue 的引入导致不满足全局有序，所以需要有写屏障和读屏障 lock 前缀指令直接锁缓存行，也能达到内存屏障的效果。 x86 架构下，volatile 的底层实现就是 lock 前缀指令。 JMM 是一个模型，是一个便于 Java 开发人员开发的抽象模型。

区分操作系统层面和java层面的内存屏障

how 语义是：屏障前的写都要写入内存而不是缓存，屏障后的读 读到的都是 在实际的应用程序开发中，开发者可能完全不知道内存屏障， 这主要是因为各种同步机制中已隐含了内存屏障， 编写诸如无锁数据结构，那么内存屏障意义重大。

why什么时候需要内存屏障 为了解决cpu，高速缓存，主内存带来的的指令之间的可见性和重序性问题。

内存屏障的引入，本质上是由于CPU重排序指令引起的。重排序问题无时无刻不在发生，主要源自以下几种场景：

编译器编译时的优化； 处理器执行时的多发射和乱序优化；（现代处理器基本上都是支持多发射的，也就是在一个指令周期内可以同时执行多条指令） 读取和存储指令的优化； 缓存同步顺序（导致可见性问题）

四种读写屏障 loadload保证屏障前的读在屏障后的读之前完成 loadstore storestore storeload

读屏障本质是拉取别人的修改 让当前CPU缓存里该变量成为最新值 写屏障本质是将自己storeBuffer中的修改刷入主存 其实就是让变量的修改对其他CPU可见

对于我们常见的x86 架构的CPU来说，它有一个相对强大的内存模型。它能直接保证前面三种屏障，也就是说不需要去写汇编指令去阻止CPU对前面三种类型读写操作的重排

CPU无法保证StoreLoad类型的屏障

内存屏障还有其他功能：

写类型的内存屏障还能触发内存的强制更新，让Store Buffer中的数据立刻回写到内存中。读类型的内存屏障会让Invalidate Queue中的缓存行在后面的load之前全部标记为失效

volatile内存屏障 final内存屏障

多核下采用了Write back模式的数据写入（多核下每个cpu的高速缓存之间的同步问题） Cache一致性协议 MESI 最终一致性 基于总线嗅探实现，用额外的两位给每个缓存行标记状态，并且维护状态的切换，达到缓存一致性的目的

MESI代表“Modified”、“Exclusive”、“Shared”和“Invalid 每个Cache line有4个状态 其实就是一个有限状态机

M E S I
从主存第一次取数据的时候，如果有其他CPU也取了这个数据， 就会标记为S（代表是共享状态）,否则标记为E(意味着独享该数据)

当CPU对缓存行进行修改是， 如果是E，可以直接修改，因为此时没有其他CPU在用它，并将状态改为M， 如果是S，那么它就需要去让其他CPU里面的该缓存行全部变为I失效状态，然后再改为M状态 如果是M 意味着当前CPU已经改过了，可以随便改 不用管其他CPU 这些消息通过总线传输 消息种类 READ READ RESPONSE INVAILD INVAILD ACK READ INVAILD（原子性的发送 Read + Invalidate） WRITEBACK

M-E 某个CPU将它修改后的数据WRITEBACK回主存 M-I 某个CPU收到READ INVAILD，会把它的数据返回给请求方 I-M CPU read-modify-write 操作一个状态为 I 的缓存行后，状态可以变为 M。变为 M 之前会发出 Read Invalidate 消息，等到INVAILD ACK之后才会改变状态 S-M 最常见的修改，在修改前需要发送INVAILD消息并等待其他CPU返回INVAILD ACK M-S 当前CPU修改过，此时其他CPU发起read操作，这个cpu会返回修改过的数据并修改状态 E-I 当前CPU独占，此时另一个cpu发起read-modify-write的原子操作

MESI的本意，就是“减少核间通信的次数“ 状态规定为4种 修改、独占、共享、无效

画图解释

CPU1   CPU2    CPU3
CACHE1 CACHE2 CACHE3
        BUS
        MEMORY
通过状态和消息发送维护缓存数据的一致性

因为MESI是通过同步操作来保证一致性，牺牲了性能 其实不用等到ACK返回也能修改状态，从而继续执行后面的指令，提高性能（采用了延迟写入的概念 来提高性能）

对于一个修改操作来讲，即S-M操作，CPU0需要发送INVAILD消息并等待其他CPU的ACK 性能差:所以有了 存储缓冲Store Buffer 无效队列Invalidation Queue 前者是对发送的优化 后者是对返回的优化 前者是发送方不需要等到返回就可以执行后面的步骤，而是将修改后写入buffer，异步执行 后着是返回方不需要修改缓存行状态就可以返回数据，而是写入一个队列，异步执行

Store Buffer 是位于每个CPU核心内部的一个小队列，用于暂存待写入的数据，等到其他CPU返回ACK之后，再写入缓存行（有点像存储引擎log buffer）

是对MESI的优化策略 store buffer/invalid queue： 一种避免写操作导致的不必要的停顿的方式是在每个 CPU 与其 cache 间添加一个 store buffer invalidate acknowledge 消息耗时过长的一种原因是，它必须确保对应缓存行真的失效。如果 cache 繁忙，例如 CPU 正在密集地读写在其缓存内的数据，可能会延迟失效

CPU的性能是提高了，但缓存一致性就遭到了一定程度的破坏 （所以问题还是这个store buffer导致的 因为mesi协议中的有些操作效率不高，CPU又引入了storebuffer/invalid queue或类似的东西， 而这些东西又不归mesi管，所以还需要内存屏障来保障可见性 ）

X86 平台下，Java volatile 关键字是如何实现防止指令重排序的： 把内存屏障插入到指令序列中

那么内存屏障又是如何实现的？ volatile 变量的写在经过 JIT 编译后, 会产生一个带 LOCK 前缀的汇编代码，在CPU层级没有“volatile”的概念

通过汇编的Lock 指令 锁总线 或 a 的缓存行 LOCK 指令会锁定总线或缓存行，以确保该指令的操作是原子的，并且在此期间不允许其他处理器访问相同的内存位置

为什么 volatile 写不具备原子性？ volatile 变量写入并不保证线程安全，也不具备原子性。原因很简单，在执行内存屏障之前，不同 CPU 依旧可以对同一个缓存行持有，一个 CPU 对同一个缓存行的修改不能让另一个 CPU 及时感知，因此出现并发冲突。线程安全还是需要用锁来保障，锁能有效的让 CPU 在同一个时刻独占某个缓存行，执行完并释放锁后，其他CPU才能访问该缓存行。锁既能保证线程安全又能保证内存可见，而 volatile 只能保证内存可见。



https://kongjun18.github.io/posts/memory-barriers-a-hardware-view-for-software-hackers/
http://www.rdrop.com/users/paulmck/scalability/paper/whymb.2010.06.07c.pdf
https://zhuanlan.zhihu.com/p/446963883 
https://zhuanlan.zhihu.com/p/659120436
https://www.zhihu.com/question/65372648/answer/415311977
https://www.zhihu.com/question/583090138/answer/2893436709
https://zhuanlan.zhihu.com/p/351550104
https://zhuanlan.zhihu.com/p/529360148