前置知识：
- 用户空间和内核空间  
    操心系统将虚拟空间划分为两部分，一部分为内核空间，一部分为用户空间
- 进程切换  
    **进程切换很消耗资源**
- 进程阻塞  
    正在执行的进程 如请求系统资源失败、等待某种操作的完成、新数据尚未到达或无新工作做等，则由系统自动执行阻塞原语(Block)，使自己由运行状态变为阻塞状态。可见，进程的阻塞是进程自身的一种主动行为，也因此只有处于运行态的进程（获得CPU）
		**当进程进入阻塞状态，是不占用CPU资源的**
- 文件描述符  
    
- 缓存IO
缓存 I/O 又被称作标准 I/O，大多数文件系统的默认 I/O 操作都是缓存 I/O
**数据会先被拷贝到操作系统内核的缓冲区中，然后才会从操作系统内核的缓冲区拷贝到应用程序的地址空间**。数据在传输过程中需要在应用程序地址空间和内核进行多次数据拷贝操作，这些数据拷贝操作所带来的 CPU 以及内存开销是非常大的。

一次IO的过程分为两个步骤
发起IO请求
实际的IO读写(内核态与用户态的数据拷贝)


阻塞是？
在没有数据包时，会通过阻塞进程等待数据到来，通过进程调度切换进程，实现进程阻塞
非阻塞IO在没有数据包时，通过轮询方式等待数据包到来

同步 异步 
区别同步IO和异步IO方法很简单，就是用户空间和内核空间数据同步由谁发起。
- 由用户程序发起则是同步IO。
- 由内核发起则是异步IO

同步IO和异步IO都可能会阻塞进程

在编程的方法调用上也存在同步调用和异步调用的说法（和之前同步异步的概念层级不同）。就拿RPC来说吧：
如果同步调用，则调用的结果会在本次调用后返回。
如果异步调用，则调用的结果不会直接返回。会返回一个Future或者Promise对象来供调用方主动/被动的获取本次调用的结果。



linux下的io模型
- 同步阻塞IO（blocking IO）
- 同步非阻塞IO（nonblocking IO)
- IO多路复用（IO multiplexing）
- 信号驱动IO（signal driven IO）
- 异步IO（asynchronous IO）

1.线程在内核进行IO操作时被阻塞
```
read(socket,buffer);
process(buffer);
```
2.将socket设置为NONBLOCK。这样做用户线程可以在发起IO请求后可以立即返回。通过轮询来实现操作
```
while(read(socket,buffer)!=SUCCESS){
	WAIT
}
process(buffer)
```
3.使用select函数可以避免同步非阻塞IO模型中轮询等待的问题
使用select函数进行IO请求和同步阻塞模型**没有太大的区别**，甚至还多了添加监视socket，以及调用select函数的额外操作，**效率更差**。但是，使用select以后最大的优势是用户可以在**一个线程内同时处理多个socket的IO请求**
```
select(socket)

while(1){
	sockets=select()
	for(socket:sockets){
	if(can_read(socket)){
		read(socket)
		process(buffer)
	}else if(can_write(socket)){
		write(socket)
		process(buffer)
	}else{
	...
	}
	}
}

```

4.**信号驱动IO在实际中并不常用**
使用信号，让内核在文件描述符就绪的时候使用 SIGIO 信号来通知我们

5.Linux下的异步IO其实用得很少 ，著名的高性能网络框架netty 5.0版本被废弃的原因便是：使用异步IO提升效率，增加了复杂性，却并且没有显示出明显的性能优势

**信号驱动IO和异步IO区别**
信号驱动IO属于同步IO
信号驱动IO发送信号通知用户程序发起IO请求。
异步IO发送信号通知用户程序IO请求已由内核发起并完成

同步IO和异步IO的区别就在于第二步，实际的IO读写(内核态与用户态的数据拷贝)是否需要进程参与
例如这两种都是返回事件，
如果是同步IO，则状态事件为读写就绪 此时的数据仍在内核态中，但是已经准备就绪
如果是异步IO，则状态事件为读写完成  此时的数据已经存在于用户态

- **同步 I/O（Blocking I/O）**：
    - **比喻**：就像厨师做饭，当饭做好的时候，厨师告诉你“饭做好了，你来拿”。你必须**主动去取**，直到你取到饭才能继续做其他事情。所以在这个过程中，**你在等待**，而厨师在完成任务后才通知你。
    - **对应的 I/O 操作**：进程发起 I/O 操作后，会**被阻塞**，直到 I/O 操作完成。例如，调用 `read()` 或 `write()` 后，进程需要等待内核完成数据传输，才能继续执行下一个操作。在此期间，进程无法做其他事情。
- **异步 I/O（Asynchronous I/O）**：
    - **比喻**：就像厨师做饭，当饭做好了，厨师不仅告诉你“饭好了”，还直接端到你的桌子上。你不需要**自己去取**，饭已经自动送到你面前，进程继续做其他事情，直到收到饭已送到桌子的通知。
    - **对应的 I/O 操作**：进程发起 I/O 操作后，**不需要等待**，它可以继续做其他事情。内核会在 I/O 完成时，通过信号或回调通知进程操作已经完成。在这期间，进程不被阻塞，可以继续执行其他任务，直到 I/O 完成。




java的io模型
BIO NIO AIO
举例就是 
自选称重的餐厅（需要自己端菜，需要一直等待）
点菜叫号的餐厅（需要自己端菜，不需要一直等待）
正式的餐厅（不需要自己端菜，不需要一直等待）

AIO其实很早就支持了，但是业界主流IO框架却很少使用呢，比如netty， 从 2.x 开始引入AIO,随后，3.x也继续保持，但到了4.x却删除对AIO的支持，能让一款世界级最优秀的IO框架之一做出舍弃AIO，那肯定是有迫不得已的原因，那原因到底是啥呢？
- 对AIO支持最好的是Windows系统，但是很少用Windows做服务器；
- linux常用来做服务器，但AIO的实现不够成熟；
- linux下AIO相比NIO性能提升并不明显；
- 维护成本过高；
