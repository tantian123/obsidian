
是Lock的一个辅助对象，可以通过Lock.newCondition()方法获取，提供了类似于Object.wait()和Object.notify()的功能，可以用于线程等待和唤醒的场景

就是线程自带的遥控器 可以控制线程的休眠和唤醒


使用Condition来实现线程之间的通信
```

public class ConditionDemo {  
  
    private static final ReentrantLock reentrantLock = new ReentrantLock();  
    private static final Condition condition = reentrantLock.newCondition();  
  
    public static void main(String[] args) {  
  
        Thread thread1 = new Thread(() -> {  
            reentrantLock.lock();  
            try {  
                System.out.println("thread1--startAndAwait");  
                condition.await();  
                System.out.println("thread1--continuing");  
            } catch (InterruptedException e) {  
                e.printStackTrace();  
            } finally {  
                reentrantLock.unlock();  
            }  
        });  
  
        Thread thread2 = new Thread(() -> {  
  
            reentrantLock.lock();  
            try {  
                Thread.sleep(2000);  
                System.out.println("thread2--startAndSignal");  
                condition.signal();  
            } catch (InterruptedException e) {  
                e.printStackTrace();  
            } finally {  
                reentrantLock.unlock();  
            }  
        });  
  
        thread1.start();  
        thread2.start();  
    }  
}
```