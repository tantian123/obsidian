用于线程同步的工具，它允许一个或多个线程等待，直到在其他线程中执行的一组操作完成为止。

`CountDownLatch` 被初始化为一个给定的计数。等待方法（`await`）会阻塞，直到当前计数由于调用 `countDown` 方法而达到零。此后，所有等待的线程都会被释放，任何后续的 `await` 调用会立即返回。这是一个一次性的现象，即计数无法重置。如果需要可以重置计数的版本，请考虑使用 `CyclicBarrier`。


CountDownLatch的应用实例
实现目标：实现主线程开启后启动子线程，子线程全部完成后主线程才能完成。
实现方式：使用两个CountDownLatch，一个设置为1，另一个设置为子线程数量，被子线程和主线程共用。
主线程 （Latch2.await）完成自己的目标后 调用Latch1.countDown,从而打开了门阀2，子线程全部开始启动。
子线程（Latch1.await） 完成自己的目标后 调用Latch2.countDown 等到计数从N到0后，主线程才被释放，继续执行其他
``` java
CountDownLatch countDownLatch1 = new CountDownLatch(1);  
CountDownLatch countDownLatch2 = new CountDownLatch(100);  
  
countDownLatch1.countDown();  
// doSomething  
System.out.println("父线程启动");  
  
  
for (int i = 0; i < 100; i++) {  
    new Thread(() -> {  
        try {  
            System.out.println("子线程启动");  
            countDownLatch1.await();  
        } catch (InterruptedException e) {  
            e.printStackTrace();  
        }  
        // doSomething  
        System.out.println("子线程结束");  
        countDownLatch2.countDown();  
    }).start();  
}  
  
try {  
    countDownLatch2.await();  
} catch (InterruptedException e) {  
    e.printStackTrace();  
}  
System.out.println("父线程结束");
```

CountDownLatch源码
```
属性是继承AQS的sync类
调用的也是AQS的tryAcquireSharedNanos方法和releaseShared方法
tryAcquireSharedNanos尝试获取共享资源 返回state为0才是成功
releaseShared释放资源 state递减

public class CountDownLatch {
private static final class Sync extends AbstractQueuedSynchronizer{}
private final Sync sync;

public boolean await(long timeout, TimeUnit unit)  
    throws InterruptedException {  
    return sync.tryAcquireSharedNanos(1, unit.toNanos(timeout));  
}  
public void countDown() {  
    sync.releaseShared(1);  
}

```

总结一下

CountDownLatch实现 线程等待 ，直到其他线程完成某组操作后再执行

基于AQS实现 将count传入作为state
countdown 调用aqs的方法 将state--
await调用aqs的方法 尝试获取