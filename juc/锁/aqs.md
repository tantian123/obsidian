AbstractQueuedSynchronizer
```
volatile int state; // 1表示已被占有 

线程等待序列 //state为1时来加锁的线程会放入这个队列

AQS支持两种同步模式：独占模式和共享模式。独占模式意味着同一时刻只允许一个线程持有同步状态，例如ReentrantLock。共享模式允许多个线程同时持有同步状态，例如ReadWriteLock中的读锁

AQS提供了一系列主要方法，包括获取同步状态、释放同步状态以及等待/通知机制。这些方法分为独占模式和共享模式两类
a. 独占模式： - acquire(int arg)：尝试获取同步状态，若失败则进入等待队列 - release(int arg)：尝试释放同步状态，并唤醒等待队列中的后继节点

b. 共享模式： - acquireShared(int arg)：尝试以共享模式获取同步状态，若失败则进入等待队列 - releaseShared(int arg)：尝试释放同步状态，并唤醒等待队列中的后继节点

```

state相关的操作都是通过CAS来保证其并发修改的安全性


ReentrantLock是一个典型的基于AQS实现的锁。此外，还有其他一些同步组件，如Semaphore、CountDownLatch、ReadWriteLock等，也是基于AQS实现的