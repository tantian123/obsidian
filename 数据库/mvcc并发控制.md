数据库并发场景有三种，分别为：
- `读-读`：没有冲突，不需要并发控制
- `读-写`：有线程安全问题，可能会造成事务隔离性问题，可能遇到脏读，幻读，不可重复读
- `写-写`：有线程安全问题，可能会存在更新丢失问题，比如第一类更新丢失，第二类更新丢失

我们采取
`MVCC + 悲观锁`  
MVCC解决读写冲突，悲观锁解决写写冲突


1.读不阻塞写 写不阻塞读  读写不冲突，只有写于写是冲突的,可以很大程度上提升性能

2.同时还可以解决脏读，幻读，不可重复读等事务隔离问题，但不能解决更新丢失问题







通过维护单个逻辑版本的多个物理版本，当事务访问逻辑元组时，选择物理元组中符合要求的那一个（可见性判断）




数据结构  Timestamp简称ts
![[Pasted image 20241123204016.png]]

txn-id一般会被设置成当前修改事务的Tid，可以被当成锁使用
- `begin-ts`：该版本开始生效的时间戳。
- `end-ts`：该版本失效的时间戳
而begin-ts 和 end-ts用于和事务的timestamp搜索匹配
pointer用于链接多个版本



并发控制 控制事务是否能够访问或者修改当前tuple



三种多版本的并发控制策略（其实只有乐观和悲观 乐观分为使用时间戳和OCC）
乐观就是认为没冲突，其中OCC是基于检查的方法 分为三个阶段
1. Read Phase: 读对象，把对象 copy 到事务内写
2. Validation Phase: txn commit 的时候, 检查事务是否和别的事务产生冲突
3. Write Phase: 把记录写入




1.Timestamp Ordering (MVTO) 保证事务按照事务Id（时间戳）的先后来串行化
实现方式：在tuple中会额外记录read-ts（记录读取该版本的事务中最大的时间戳）的 

1.当有事务需要读取tuple的时候，会判断tid是否在begin-ts和end-ts中间
且txn-id为当前事务id或者是0
```
1）即事务Tid在对应物理元组的begin-ts和end-ts之间（2）还需要确保Ax的写锁没被其他事务占住（即txn-id是0）


if(tid>=begin_ts&&tid<end_ts&&(txn_id==tid||txn_id==0)){
readTuple();
read_ts=max(read_ts,tid)
}else{
abort
}
```
2.当事务需要修改tuple的时候，会判断txn-id是否为0且tid是否大于end-ts,
此时会新增tuple2（txn-id为tid），将tuple2的begin-ts设置为tuple的end-ts，将tuple1的end-ts设置为tid
```
1）没有active的事务持有Bx的写锁。（2）Tid要大于Bx的read-ts。满足条件后，DBMS创建一个新的版本Bx+1

if(tid>tuple.read_ts&&txn_id==0){
Tuple tuple2= tuple1;
tuple2.txn_id=tid;
tuple2.begin_ts=tuple1.end_ts;
tuple2.end_ts=INF;
tuple1.end_ts=tid;
}else{
abort
}
```

总结一下就是记录每个tuple的max_read_ts，修改的时候事务tid必须大于这个max_read_ts，否则当前事务abort

2.Optimistic Concurrency Control (MVOCC)
基于OCC的思想，DBMS认为事务大概率不会冲突，因此一个事务在读或更新元组时，不需要获取元组的锁
MVOCC协议将事务分成三个阶段。
1）事务开始的时候，属于读阶段（read phase），在这个阶段事务可以进行读和更新的操作，如同MVTO，对于元组A进行读操作时，也是根据begin-ts和end-ts找到可见版本Ax，不同的是写操作，在没有获取写锁的情况下，事务T允许更改元组的版本。例如事务T要更改元组Bx，DBMS直接创建新版本Bx+1，txn-id设置为Tid。
2）当事务决定提交时，事务来到了验证阶段（validation phase）。首先DBMS给事务另一个时间戳（Tcommit）从而决定事务的序列化。DBMS决定事务在read set里的元组是否被已提交的其他事务更新过。
3）如果事务通过这些检查，那么将来到写阶段（write phase）。DBMS将生效所有新版本，并将begin-ts设置为Tcommit，end-ts设置为INF


著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。


3.Two-phase Locking (MV2PL)
为什么叫二阶段锁，因为分为两个阶段，第一个阶段获取锁，第二个阶段是在事务结束前一直持有锁

**元组的写锁就是txn-id字段，读锁就是read-cnt字段记录正在读该元组的活跃事务的数量**

二阶段锁，每个tuple的header前会维护一个read-cnt，读的时候需要加一，代表上读锁。而update时候需要tuple的read-cnt等于0



版本存储Version storage
![[Pasted image 20241123215919.png]]

1.Append-only Storage   版本信息存储在一块
2.Time-Travel Storage   分为主版本和其它版本
3.Delta Storage  只会存tuple的部分attribute集合，而不是这个tuple的拷贝
例如mysql的undolog的回滚段rollback segment，
优势是减少了内存申请的压力，缺点是需要rollback拼回要访问的tuple信息


![[Pasted image 20241123215621.png]]





在innodb的实现中
通过undo log保存每条数据的多个版本

MVCC只在`已提交读`（Read Committed）和`可重复读`（Repeatable Read）两个隔离级别下工作，其他两个隔离级别和MVCC是不兼容的。因为未提交读，总数读取最新的数据行，而不是读取符合当前事务版本的数据行。而串行化（Serializable）则会对读的所有数据多加锁

### MVCC的实现原理
主要是依赖每一行记录中两个隐藏字段**trx_id**、**roll_pointer**，undolog，ReadView

多个事务并行操作某一行数据时，不同事务对该行数据的修改会产生多个版本，通过roll_pointer连成一个链表

 快照读和当前读
**快照读**：
读取的是记录数据的可见版本（有旧的版本）。不加锁,普通的select语句都是快照读
```sql
select * from user where id = 1;
```
**当前读**：读取的是记录数据的最新版本，显式加锁的都是当前读
```sql
select * from user where id = 1 for update;
select * from user where id = 1 lock in share mode;
```

ReadView(服务于 **快照读**)是事务在进行**快照读**的时候生成的记录快照, 可以帮助我们解决可见性问题的
利用 ReadView，InnoDB 实现了： 
- **无锁读**：可以实现大多数读操作无需加锁，提升并发性能。
- **事务隔离**：在 `REPEATABLE READ` 下，事务看到的数据是一致的快照

如果一个事务要查询行记录，需要读取哪个版本，ReadView就是处理这个问题的

可重复读和读已提交的 MVCC 判断版本的过程是一模一样的，唯一的差别在生成 readView 上
在 RC 隔离级别下，是每个快照读都会生成并获取最新的 Read View；而在 RR 隔离级别下，则是同一个事务中的第一个快照读才会创建 Read View, 之后的快照读获取的都是同一个 Read View
**trx_ids**  **low_limit_id** **up_limit_id** **creator_trx_id**
```
if(tid==creator_trx_id){
当前版本可见,直接返回该版本
}else if(tid<low_limit_id){
直接返回该版本
}else if(tid<up_limit_id&&tid in trx_ids) ){
根据roll_pointer去查找undo log日志链，找到之前版本的数据
}
```



https://zhuanlan.zhihu.com/p/115437885
