
名词汇总


经典的 隔离级别 ：读未提交 读已提交 可重复读 串行化
还有一种Snapshot Isolation（快照隔离）
异常现象和它们对应的解决方案：
经典的 异常现象：脏读、不可重复读、幻读
其他的 异常现象：Dirty Write脏写 Lost Update丢失更新  Read Skew读倾斜（也是不可重复读） Write Skew写倾斜

当前读 快照读
是什么 如何触发 有什么用
不加锁都是快照读 加锁的都是当前读 for update，会在扫描的过程加上间隙锁和临键锁，等于提升了隔离级别到可串行化， 也不会影响普通查询的性能别








隐藏字段
ReadView
undolog


概念解释
什么是事务 事务的作用 
什么是隔离级别 为什么需要它 数据库是如何实现它的
并发问题（异常现象）的实际例子
为什么叫多版本并发控制
什么是并发控制，控制什么
幻读是如何解决的 是MVCC吗


InnoDB同样以MVCC+Lock的方式实现隔离级别

**隔离级别定义的是数据库事务间的隔离程度**

事务是一系列操作的集合，具有 ACID 的特性
 SQL-92 提出了最经典的隔离级别定义，包括读未提交（Read Uncommitted）、读提交（Read Committed）、可重复读（Repeatable Read）和可序列化（Serializable）





mysql为什么是可重复读的隔离级 也能解决幻读问题（数据库厂家对标准的实现不同）
标准的可重复读是没有解决幻读的问题，
MySQL（innodb）为了解决这个问题，强行把 read 分成了 _snapshot read_（快照读）和 _locking read_ （当前读）。在 UPDATE 或者 SELECT ... FOR UPDATE 的时候，innodb 引擎实际执行的是当前读，在扫描过程中加上行锁和区间锁（_gap locks_，_next-key locks_），相当于变相提升到了 serializable 隔离级别（但是仍然没有解决Lost Update）
既解决了 UPDATE write-skew 问题，又保证了绝大多数场景 SELECT 的性能，特殊情况还可以用 SELECT ... FOR UPDATE





6种读写异常现象（其实就是现实中的并发问题），解决难度从低到高
1. 脏读 Dirty Reads
2. 脏写 Dirty Writes
3. 读扭曲/不可重复读 Read Skew / Non-Repeatable Reads
4. 更新丢失 Lost Updates
5. 写扭曲 Write Skew
6. 幻读 Phantom Reads


1.脏读，指一个事务读到了另一个事务写了但未提交（Not committed）的数据。
脏写，指一个事务在写的过程中覆盖了另一个事务已写但未提交的数据
![[Pasted image 20241124224338.png]]


解法：读已提交
读已提交 防止脏写的方式是行锁（Row Lock）
读已提交 防止脏读的实现方式是记录一个正在被写的行的新老两个版本

2.读扭曲/不可重复读 Read Skew / Non-Repeatable Reads
**读事务先后阶段读到了数据库的不同的状态**

解法：快照隔离 也叫**可重复读**

3.更新丢失 Lost Updates
更新丢失多发生于一种叫做“读-改-写 (read-modify-write)”的业务模式中
指事务先从数据库中读取状态值，基于这些值进行运算，再将修改后的值更新进数据库的过程

场景：两个事务同时读取同一数据，分别修改后提交，导致一个事务的修改被覆盖
![[Pasted image 20241124224321.png]]


Lost Update问题和ConcurrentHashMap的问题是类似的，分布式系统中端到端的一致性问题也是一种广义的Lost Update，缓存与数据库之间的同步问题同样有可能导致Lost Update

解决方案是
悲观锁 SELECT ... FOR UPDATE
乐观锁 CAS  
UPDATE accounts SET balance = 1100 WHERE ... AND balance = 1000;
或者是有些实现会额外使用一个版本号字段 
UPDATE accounts SET balance = 1100, version = 11 WHERE ... AND version = 10;

原子操作  computeIfAbsent()或AtomicInteger 体现在数据库中就是UPDATE accounts SET balance = balance + 100 WHERE ...;

4.写扭曲 Write Skew、幻读 Phantom Reads
写扭曲：
场景：双账户转账安全检查 
x和y账户分别有50块钱，加起来共100块。假设存在某种约束，x和y账户的钱加起来不得少于60块。
事务 1读取账户1 和 2 的余额，总余额为100
规则允许 账户1转出30
事务2进行同样操作，规则允许 账户2转出30
两个事务都成功提交，但是结果违反了规则

对查询范围显式加锁可以解决这个问题 或者引入业务级别的全局锁

幻读：
场景：
select ... where age>10
第一次查询是x条，在第二次查询之前，插入了一条age=100的数据，第二次查询就是x+1条

 因为我们的判断条件是**SELECT的行不存在**。你不能对不存在的行加锁。这种异常我们称之为幻读
