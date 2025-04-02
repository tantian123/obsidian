加锁
使用setnx
set key request_id nx px expire_time

为了防止死锁 需要设置过期时间

解锁 
使用lua脚本 保证判断语句和删除语句是原子操作

redis主从/集群架构下的问题：
获取锁后主节点宕机，消息还没有被发送到从节点
此时从节点作为主节点，记录丢失


最佳实践：
redisson 
相比较于setnx 好在哪
支持**可重入锁、公平锁及读写锁**，提供**自动续期**（看门狗机制）

redisson的原理？
**原理就是lua脚本**
可重入是通过hash结构，key是线程id ，value是重入次数
自动续期 是定时任务 定时去续期 只要没有主动释放锁 就给它续期
公平锁（RFairLock）​：基于Redis的List结构维护请求队列，按顺序分配锁资源
读写锁（RReadWriteLock）​：通过Redis的Hash结构区分读锁（共享）和写锁（独占），读锁计数为0时允许写锁获取



zookeeper如何实现分布式锁
**临时顺序节点**和**Watcher监听机制**
结合ZAB协议提供的强一致性保

zookeeper如何实现可重入的分布式锁
写临时节点时 把线程信息写入进去

zookeeper和redis的分布式锁区别在哪
基于业务场景选型

​**ZooKeeper（CP模型）​**强一致性 性能瓶颈
​**Redis（AP模型）​**  ​脑裂风险 最终一致性