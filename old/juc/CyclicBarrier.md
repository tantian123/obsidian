（循环屏障）是一种用于线程同步的辅助工具，它允许一组线程等待彼此达到一个共同的屏障点。

CyclicBarrier支持一个可选的Runnable命令（称作**屏障动作**barrierAction），该命令在线程组的最后一个线程到达屏障点后执行，但在任何线程被释放之前执行。这个屏障动作对于在任何线程继续之前更新共享状态非常有用。



``` java
使用了`ReentrantLock` 和 `Condition`,
因为CyclicBarrier需要循环使用
`ReentrantLock` 提供了灵活的锁机制，可以确保在需要的时候进行精确的同步，同时 `Condition` 提供了等待和通知的机制，可以实现线程在屏障点等待和唤醒的功能，非常适合 `CyclicBarrier` 的需求。


内部类
private static class Generation {  
    boolean broken = false;  
}
属性  lock重入锁 条件对象trip
parties计数器  count等待线程的数量
private final ReentrantLock lock = new ReentrantLock();  

private final Condition trip = lock.newCondition();  

private final int parties;  

private final Runnable barrierCommand;  

private Generation generation = new Generation();  
  
private int count;
```


dowait方法

``` java
如果当前线程是最后一个到达屏障的线程，就执行 `barrierCommand`（如果存在），然后更新屏障的状态，唤醒等待的线程
如果不是最后一个线程，就进入一个循环，等待直到屏障被触发、中断、超时或取消

ReentrantLock保证了count--的原子性


private int dowait(boolean timed, long nanos)  
    throws InterruptedException, BrokenBarrierException,  
           TimeoutException {  
    final ReentrantLock lock = this.lock;  
    lock.lock();  
    try {  
        final Generation g = generation;  
  
        if (g.broken)  
            throw new BrokenBarrierException();  
  
        if (Thread.interrupted()) {  
            breakBarrier();  
            throw new InterruptedException();  
        }  
  
        int index = --count;  
        if (index == 0) {  // tripped  
            boolean ranAction = false;  
            try {  
                final Runnable command = barrierCommand;  
                if (command != null)  
                    command.run();  
                ranAction = true;  
                nextGeneration();  
                return 0;  
            } finally {  
                if (!ranAction)  
                    breakBarrier();  
            }  
        }  
  
        // loop until tripped, broken, interrupted, or timed out  
        for (;;) {  
            try {  
                if (!timed)  
                    trip.await();  
                else if (nanos > 0L)  
                    nanos = trip.awaitNanos(nanos);  
            } catch (InterruptedException ie) {  
                if (g == generation && ! g.broken) {  
                    breakBarrier();  
                    throw ie;  
                } else {  
     
                }  
            }  
  
            if (g.broken)  
                throw new BrokenBarrierException();  
  
            if (g != generation)  
                return index;  
  
            if (timed && nanos <= 0L) {  
                breakBarrier();  
                throw new TimeoutException();  
            }  
        }  
    } finally {  
        lock.unlock();  
    }  
}
```


总结一下
CyclicBarrier 当线程到达某种状态后  暂停等待其他线程 所有线程都到达后 继续执行

CyclicBarrier使用了condition和ReentrantLock
每次调用await都会将count-- 并将线程放到condition上去，
等到count为0时，就通知释放