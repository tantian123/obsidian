线程的状态
new  Runnable Running Blocked Dead

区别：
new  new之后

Runnable是调用start方法之后，但是还没获取时间片即cpu的使用权

Running 获取了cpu的使用权

Blocked 阻塞 三种阻塞的情况
执行wait()之后 
获取锁的时候 
执行sleep()或join()方法

Dead
线程执行完了或者因异常退出了run()方法




线程的方法
exit run  interrupt join
native方法：yield sleep 