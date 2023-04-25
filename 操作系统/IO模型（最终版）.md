常见的IO模型
阻塞式I/O和非阻塞式I/O可以看作是同步I/O的两种实现方式，而I/O复用、信号驱动式I/O和异步I/O则是异步I/O的三种实现方式。


1.  阻塞式I/O模型（Blocking I/O）：当用户线程发起一个I/O操作后，内核会立即进行I/O操作并且阻塞该线程，直到I/O操作完成。
    
2.  非阻塞式I/O模型（Non-blocking I/O）：当用户线程发起一个I/O操作后，内核会立即返回一个错误码（EWOULDBLOCK或EAGAIN），告诉用户线程I/O操作仍在进行中，用户线程可以不断地重试该操作直到I/O操作完成。
    
3.  I/O复用模型（I/O Multiplexing）：用户线程通过调用select或poll等函数，向内核注册多个I/O操作，并在这些操作中等待任意一个操作完成，然后进行处理。
    
4.  信号驱动式I/O模型（Signal-driven I/O）：用户线程通过调用sigaction函数，向内核注册一个信号处理函数，并在函数中处理I/O操作完成的信号。
    
5.  异步I/O模型（Asynchronous I/O）：用户线程通过调用aio_read或aio_write等函数，向内核注册一个I/O操作，并指定一个回调函数，在I/O操作完成时由内核调用该回调函数进行处理。


同步I/O在进行I/O操作时，当前线程会被阻塞，因此在高并发的情况下可能会导致性能问题。而异步I/O则可以在进行I/O操作时，不会阻塞当前线程，因此可以更好地支持高并发和高吞吐量的应用场景。但是异步I/O的编程模型比较复杂，需要一定的编程技巧和经验。


在java中，阻塞式I/O模型的API主要包括java.io包和java.net包下的一些类和方法
InputStream和OutputStream类、Reader和Writer类、 Socket类
非阻塞式I/O模型的API主要包括java.nio包和java.nio.channels包下的一些类和方法
Selector类  Channel类 ByteBuffer类 SelectionKey类 CompletionHandler接口