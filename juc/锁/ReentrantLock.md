```
1.  What（什么）： ReentrantLock是JUC中提供的一种可重入锁，用于实现对临界区的互斥访问。
    
2.  Why（为什么）： ReentrantLock提供了比synchronized关键字更高的灵活性和性能，适用于需要对锁进行精细控制的场景。
    
3.  Where（在哪里）： ReentrantLock用于保护需要互斥访问的临界区代码，以确保数据一致性和线程安全。
    
4.  When（何时）： 当需要实现对临界区的精细控制（如公平性、超时等待等）时，可以使用ReentrantLock替代synchronized关键字。
    
5.  Who（谁）： Java开发者可以使用ReentrantLock实现多线程环境下的同步控制。
    
6.  Which（哪一个）： 在JUC中，除了ReentrantLock之外，还有其他同步组件（如Semaphore、ReadWriteLock等）。根据具体需求，可以选择合适的同步组件来实现线程同步。
    
7.  How（怎么样）： 使用ReentrantLock时，需要显式地获取和释放锁。通常，可以使用try-finally代码块来确保锁的正确释放。
    
8.  How much（多少）： 在实际应用中，ReentrantLock的性能取决于锁的竞争程度、实现策略（如公平性、非公平性）等因
```

1.  原理： ReentrantLock基于AQS（AbstractQueuedSynchronizer）实现，利用state变量表示锁的持有状态。当一个线程尝试获取锁时，它会调用AQS的acquire()方法；当线程释放锁时，调用AQS的release()方法。ReentrantLock还支持公平锁和非公平锁，以及可重入性。
    
2.  应用场景： ReentrantLock适用于需要对临界区进行精细控制的场景，例如：
    
    -   需要实现锁的公平性或非公平性
    -   需要实现锁的可重入性
    -   需要实现锁的超时等待或可中断等待
    -   需要与Condition对象配合使用，实现等待/通知机制


```
import java.util.concurrent.locks.ReentrantLock;

public class ReentrantLockDemo {
    private final ReentrantLock lock = new ReentrantLock(); // 创建可重入锁

    public void performTask() {
        lock.lock(); // 获取锁
        try {
            // 临界区代码
            // ...
        } finally {
            lock.unlock(); // 释放锁
        }
    }

    public static void main(String[] args) {
        ReentrantLockDemo demo = new ReentrantLockDemo();

        // 多线程同时调用performTask方法
        new Thread(() -> demo.performTask()).start();
        new Thread(() -> demo.performTask()).start();
    }
}

```

什么是可重入 
**Re-entrant**
可以反复进入的锁，仅限当前线程

为什么是final类型
```
 线程安全：final关键字可以确保锁对象的引用在初始化后不会被改变。这样可以防止在多线程环境下由于锁对象被意外修改而导致的线程安全问题。  
 可见性：final关键字为锁对象提供了正确的内存语义。根据Java内存模型（JMM），final字段的写入操作对其他线程是可见的。这意味着，当一个线程初始化了一个final类型的锁对象后，其他线程可以立即看到该锁对象的最新状态。  
  
private final ReentrantLock reentrantLock = new ReentrantLock();
```