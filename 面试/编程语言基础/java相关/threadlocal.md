是什么 为什么  使用场景   类比设计  常见问题（结构 内存泄露）

我的理解 threadLocal只是一个类似指针或者键的概念

1.看名字：**线程本地变量**，线程自己的私有变量，线程间隔离 
```
// 创建时指定泛型
static ThreadLocal<String> threadLocal=new ThreadLocal<>();

public void test(){
	threadLocal.set("hello");
	String result=threadLocal.get();
	threadLocal.remove();
}
```

2.结构：
每个类都有一个静态成员变量ThreadLocalMap，ThreadLocalMap  组成是一个静态内部类Entry和Entry的数组  
ThreadLocal作为key从静态的ThreadLocalMap中取出值

疑问：threadLocal是静态变量，那么是如何保证隔离的？
每个线程内部维护了一个ThreadLocalMap，是线程私有的，threadLocal是相同的。

总结就是 **每个线程Thread都维护了自己的threadLocals变量，获取私有的ThreadLocalMap，使用当前ThreadLocal作为key获取value** 
```
class ThreadLocal<T>{
static class ThreadLocalMap{
	private Entry[] table;
	static class Entry extends WeakReference<ThreadLocal<?>>{
	Object value;
	}
}
}

public void get(){
	Map map=getMap(Thread.currentThread());
	 // 通过ThreadLocal实例对象作为key，在Entry数组中查找数据
     ThreadLocalMap.Entry e = map.getEntry(this);
	 T result = (T)e.value;

}
ThreadLocalMap getMap(Thread t){
return t.threadLocals;
}
```
Entry的key是Thread ，value是value
2. 一是解决线程安全的问题，二是解决方法之间传值的问题



ThreadLocal是什么 解决了什么问题

ThreadLocal是如何实现线程隔离的 它的数据结构是

为什么是弱引用
如果某些人使用ThreadLocal时没有static修饰，使用弱引用能避免泄漏
因为**弱引用关联的对象只能存活到下一次GC** 但是此时value仍然在Map上且无法访问，
这样做是发生了内存泄漏，但是我们**在set get和扩容时都会清理掉这些泄漏**的Entry

内存泄漏的原因和解决方案
Entry继承自WeakReference，并且会将ThreadLocal添加到弱引用队列中
**弱引用关联的对象只能存活到下一次GC**，会出现key被回收，value还在的情况

开发者主动调用`ThreadLocal#remove`清除无用变量


怎么实现父子线程共享ThreadLocal数据
InheritableThreadLocal :当初始化子线程的时候，会从父线程拷贝ThreadLocal数据

如何处理哈希冲突
线性探测法/开放地址法  在发生哈希冲突时，会逐个检查下一个槽位