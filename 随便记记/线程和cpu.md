CPU并不知道线程、进程之类的概念。
CPU只知道两件事:
1. 从内存中取出指令
2. 执行指令，然后回到1

cpu从PC寄存器中取到下一个指令的地址
让CPU执行某个函数，那么只需要把函数对应的第一条机器执行装入PC寄存器就可以了，**这样即使没有操作系统我们也可以让CPU执行程序**
，虽然可行但这是一个非常繁琐的过程，我们需要：
- 在内存中找到一块大小合适的区域装入程序
- 找到函数入口，设置好PC寄存器让CPU开始执行程序

进程的缺点在于只有一个入口函数，也就是main函数，因此进程中的机器指令**只能被一个CPU执行**，那么有没有办法让多个CPU来执行同一个进程中的机器指令呢
**我们可以把PC寄存器指向main函数，就可以把PC寄存器指向任何一个函数**。
**当我们把PC寄存器指向非main函数时，线程就诞生了**
至此我们解放了思想，一个进程内可以有多个入口函数，**也就是说属于同一个进程中的机器指令可以被多个CPU同时执行**。  


注意，这是一个和进程不同的概念，创建进程时我们需要在内存中找到一块合适的区域以装入进程，然后把CPU的PC寄存器指向main函数，也就是说进程中只有一个**执行流**。

但是现在不一样了，多个CPU可以在同一个屋檐下(进程占用的内存区域)同时执行属于该进程的多个入口函数，也就是说现在一个进程内可以有**多个执行流**了。（协程是在线程内有多个执行流 ）
总是叫执行流好像有点太容易理解了，再次祭出”弄不懂原则“，起个不容易懂的名字，就叫线程吧。

这就是线程的由来。
操作系统为每个进程维护了一堆信息，用来记录进程所处的内存空间等，这堆信息记为数据集A。
同样的，操作系统也需要为线程维护一堆信息，用来记录线程的入口函数或者栈信息等，这堆数据记为数据集B。
有了线程这个概念后，我们只需要进程开启后创建多个线程就可以让所有CPU都忙起来，**这就是所谓高性能、高并发的根本所在**。



- 充分理解你的任务，是长任务还是短任务、是CPU密集型还是I/O密集型，如果两种都有，那么一种可能更好的办法是把这两类任务放到不同的线程池中，这样也许可以更好的确定线程数量
    
- 如果线程池中的任务有I/O操作，那么务必对此任务设置超时，否则处理该任务的线程可能会一直阻塞下去
    
- 线程池中的任务最好不要**同步**等待其它任务的结果


在单核上真正的并行是不可能实现的，然而即便在这种情况下多线程程序依然有用。
例如UI界面