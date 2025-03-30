加锁
使用setnx
set key request_id nx px expire_time

解锁 
使用lua脚本 保证判断语句和删除语句是原子操作

redis主从/集群架构下的问题：
获取锁后主节点宕机，消息还没有被发送到从节点
此时从节点作为主节点，记录丢失


最佳实践：
redisson 
相比较于setnx 好在哪
支持可重入锁、公平锁及读写锁，提供自动续期（看门狗机制）

redisson的原理？


zookeeper如何实现分布式锁
**临时顺序节点**和**Watcher监听机制**
结合ZAB协议提供的强一致性保

zookeeper如何实现可重入的分布式锁
写临时节点时 把线程信息写入进去

zookeeper和redis的分布式锁区别在哪
基于业务场景选型

​**ZooKeeper（CP模型）​**强一致性 性能瓶颈
​**Redis（AP模型）​**  ​脑裂风险 最终一致性