关键词 park unpark 


-   `LockSupport` 类是 JUC 提供的一个工具类，用于支持线程的阻塞和唤醒操作。
-   `park` 方法会将当前线程阻塞，直到以下事件之一发生：
    -   当前线程被唤醒；
    -   当前线程被中断；
    -   虚假唤醒。
-   `unpark` 方法可以唤醒指定的线程，即使该线程还没有被阻塞。

应用：

-   `LockSupport` 类常用于实现同步器（如 `ReentrantLock`、`Semaphore` 等）中的线程阻塞和唤醒操作。
-   由于 `LockSupport` 可以不依赖于任何对象进行线程的阻塞和唤醒操作，因此它比 `wait` 和 `notify` 更加灵活、安全和高效。

```
当一个线程在执行`ReentrantLock`的`lock()`方法时，如果此时锁已经被占用，那么该线程会进入等待状态，同时会调用`LockSupport.park()`方法将该线程挂起。当其他线程释放锁并且调用`LockSupport.unpark()`方法唤醒该线程时，该线程会被唤醒，然后继续执行`lock()`方法下面的代码
```

```
LockSupport和Condition在多线程编程中都是用于线程的阻塞和唤醒的工具，但它们的应用场景略有不同。

LockSupport是JUC中提供的工具类，主要用于线程的阻塞和唤醒。它是基于线程的park和unpark方法实现的。park方法可以使线程进入等待状态，而unpark方法可以将被阻塞的线程唤醒。LockSupport是比较底层的工具，可以用于实现更高级别的同步组件，如AQS（AbstractQueuedSynchronizer）。

Condition则是在ReentrantLock的基础上提供的一种高级同步工具，它可以让线程在等待某个条件成立时被阻塞，并且在条件满足时自动唤醒。与LockSupport不同的是，Condition可以和ReentrantLock进行配合使用，它是ReentrantLock提供的一种高级同步工具。

因此，LockSupport和Condition的应用场景略有不同。LockSupport更适合于实现一些底层的同步工具，如AQS；而Condition则更适合于实现一些高级别的同步组件，如阻塞队列等。它们并不是互相排斥的，而是可以结合使用，从而实现更为复杂的同步逻辑。
```