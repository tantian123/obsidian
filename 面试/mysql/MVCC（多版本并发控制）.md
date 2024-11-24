1.基础知识
并发控制策略：
有2PL OCC TO
MVCC有基于时间戳(oracle)和基于活跃事务表(mysql)两种方式 它需要与前述的几种并发控制技术组合，才能提供完整的并发控制能力。

分为悲观乐观
悲观的：事务在执行前必须获取lock
乐观的：让每个事务执行，但是commit前必须check并处理冲突


悲观的并发性能很差

事务：

冲突操作：先读后写 先写后读 先写后写

当前读和快照读：
当前读：select lock in share mode(共享锁), select for update ; update, insert ,delete(排他锁)这些操作都是一种当前读
快照读： select操作就是快照读

记录的额外字段：
InnoDB 为存储在数据库中的每一行添加三个字段
DB _ TRX _ ID 字段 **事务ID** 创建这条记录或者修改这条记录的最新事务ID
DB _ ROLL _ PTR 字段  **回滚指针** 指向写入回滚段的撤消日志记录
DB _ ROW _ ID字段包含一个行 ID **隐式主键**


undo日志
作用是保留数据的历史版本


read view 读视图
用作可见性判断
当一个事务 执行快照读时，会对这个记录创建一个readView

readView包括 
**trx_list** 未提交事务ID列表（即活跃的事务id）
**up_limit_id** 记录trx_list列表中事务ID最小的ID
**low_limit_id** ReadView生成时刻系统尚未分配的下一个事务ID，也就是目前已出现过的事务ID的最大值+1

通过比较这行记录的DB_TRX_ID和readview中的三个属性来决定
如果不符合 就通过记录的DB_ROLL_PTR 回滚指针去取出Undo Log中的DB_TRX_ID再比较
即遍历链表



判断可见性：
如果DB_TRX_ID小于最小事务id 说明该记录对该事务可见  
如果DB_TRX_ID大于最大事务id 说明该条记录比事务晚 说明该该记录对该事务不可见
如果DB_TRX_ID，在活跃事务id列表里  说明该记录对事务不可见
**不可见进入下一轮判断，可见就返回这行数据**
2.总结
MVCC模型在MySQL中的具体实现则是由 **4个隐式字段**，**undo日志** ，**Read View** 等去完成的


针对于不同的隔离级别（read commit和repeatable read），无非就是read commit隔离级别下，每次都获取一个新的read view，repeatable read隔离级别则每次事务只获取一个read view
即可重复读的情况下 一个事务只会在一开始生成一个reaDVIEW


举例说明： 隔离级别为读未提交和序列化显然不需要MVCC机制
