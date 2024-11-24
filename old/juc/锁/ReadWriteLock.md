关键词:读写锁 读多写少 



```
public interface ReadWriteLock {
    Lock readLock();
    Lock writeLock();
}
```

ReadWriteLock是一个读写锁，它允许多个线程同时读取一个共享资源，但只允许一个线程写入共享资源。在读多写少的场景下，使用ReadWriteLock可以提高程序的并发性能

在读写锁中，读锁之间是共享的，写锁与读锁和写锁都是互斥的。在读取数据时，多个线程可以同时访问同一份数据，从而提高程序的并发性能。在写入数据时，只能有一个线程访问数据，因为在此时需要确保数据的一致性。


demo
```
public class ReadWriteLockDemo {  
    private static final ReadWriteLock readWriteLock = new ReentrantReadWriteLock();  
    private static final ReentrantLock reentrantLock = new ReentrantLock();  
    private static int value;  
  
    private static void readValueNoReadWriteLock() {  
        reentrantLock.lock();  
        try {  
            System.out.println("readValue:" + value);  
        } finally {  
            reentrantLock.unlock();  
        }  
    }  
  
    private static void writeValueNoReadWriteLock() {  
        reentrantLock.lock();  
        try {  
            value++;  
            System.out.println("writeValue:" + value);  
        } finally {  
            reentrantLock.unlock();  
        }  
    }  
  
    private static void readValue() {  
        readWriteLock.readLock().lock();  
        try {  
            System.out.println("readValue:" + value);  
        } finally {  
            readWriteLock.readLock().unlock();  
        }  
    }  
  
    private static void writeValue() {  
        readWriteLock.writeLock().lock();  
        try {  
            value++;  
            System.out.println("writeValue:" + value);  
        } finally {  
            readWriteLock.writeLock().unlock();  
        }  
    }  
  
    public static void main(String[] args) throws InterruptedException {  
        long start = System.currentTimeMillis();  
        Thread[] threads = new Thread[2000];  
  
        for (int i = 0; i < 1000; i++) {  
            threads[i] = new Thread(ReadWriteLockDemo::readValueNoReadWriteLock);  
        }  
  
        for (int i = 1000; i < 2000; i++) {  
            threads[i] = new Thread(ReadWriteLockDemo::writeValueNoReadWriteLock);  
        }  
  
        for (Thread thread : threads) {  
            thread.start();  
        }  
  
        for (Thread thread : threads) {  
            thread.join();  
        }  
        long end = System.currentTimeMillis();  
        System.out.println("使用锁，总共耗时：" + (end - start) + "ms");  
  
  
        long start2 = System.currentTimeMillis();  
  
        for (int i = 0; i < 1000; i++) {  
            threads[i] = new Thread(ReadWriteLockDemo::readValue);  
        }  
  
        for (int i = 1000; i < 2000; i++) {  
            threads[i] = new Thread(ReadWriteLockDemo::writeValue);  
        }  
  
  
        for (Thread thread : threads) {  
            thread.start();  
        }  
  
        for (Thread thread : threads) {  
            thread.join();  
        }  
        long end2 = System.currentTimeMillis();  
        System.out.println("使用读写锁，总共耗时：" + (end2 - start2) + "ms");  
  
    }  
}
```