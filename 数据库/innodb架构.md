内存和磁盘
[MySQL :: MySQL 5.7 Reference Manual :: 14.4 InnoDB Architecture](https://dev.mysql.com/doc/refman/5.7/en/innodb-architecture.html)
[InnoDB 架构（InnoDB Architecture） - 懒惰的星期六 - 博客园](https://www.cnblogs.com/sunupo/p/17131332.html)



![[Pasted image 20241209225544.png]]

## 内存
InnoDB 内存结构包括：`Buffer Pool`，`Change Buffer`，`Adaptive Hash Index`，`Log buffer`。

**Buffer Pool**
MySQL 不会直接取修改磁盘的数据，因为这样做太慢了，MySQL 会先改内存，然后记录 redo log，等有空了再刷磁盘，如果内存里没有数据，就去磁盘 load。
而这些数据存放的地方，就是 Buffer Pool

MySQL 是以「页」（page）为单位从磁盘读取数据的，Buffer Pool 里的数据也是如此，实际上，Buffer Pool 是`a linked list of pages`，一个以页为元素的链表

缓冲池被分成可能包含多行（row）的页面（page）。缓冲池由页面链表实现。使用最近使用次数最少（LRU）算法的变体 LRU--K

**Change Buffer**
如果 MySQL 发现你要修改的页，不在内存里，就把你要对页的修改，先记到一个叫 Change Buffer 的地方，同时记录 redo log，然后再慢慢把数据 load 到内存，load 过来后，再把 Change Buffer 里记录的修改，应用到内存（Buffer Pool）中，这个动作叫做 **merge**；而把内存数据刷到磁盘的动作，叫 **purge**

- **merge：Change Buffer -> Buffer Pool**
- **purge：Buffer Pool -> Disk**

**Adaptive Hash Index**
自适应哈希索引（加快热点数据的查询）
MySQL 会自动评估使用自适应索引是否值得，如果观察到建立哈希索引可以提升速度，则建立

**Log Buffer**
存放要写入磁盘上的日志文件的数据的内存区域。
Log Buffer 里的 redo log，会被刷到磁盘里


Operating System Cache
在内存和磁盘之间，你看到 MySQL 画了一层叫做 Operating System Cache 的东西，其实这个不属于 InnoDB 的能力，而是操作系统为了提升性能，在磁盘前面加的一层高速缓存 Page Cache

## 磁盘
除了表结构定义和索引，还有一些为了高性能和高可靠而设计的角色，比如 redo log undo log、Change Buffer，以及 Doublewrite Buffer 等等

**1、表空间（Tablespaces）**

2.**Doublewrite Buffer** files
当 innodb 要将数据落盘的时候，先将页数据拷贝到 Doublewrite Buffer 中，然后 Doublewrite Buffer 再刷盘到 Doublewrite Buffer Files，这时候数据已经落盘了
然后再将数据页刷盘到本该到文件上。
从这个步骤我们得知，数据是写了两次磁盘，所以这玩意叫 double write
在崩溃恢复的时候，如果发现页损坏，就可以从 Doublewrite Buffer Files 里面找到页副本，然后恢复即可

如果在页面写入过程中出现操作系统、存储子系统或意外的 mysqld 进程退出，InnoDB 可以在崩溃恢复期间从双写缓冲区（Doublewrite Buffer）找到该页面的良好副本
注意：redo log 只能通过完整的页面恢复。页面写入过程出现意外导致出现`坏页`，这个时候redo log 就无法恢复了。

**如果说 Change Buffer 是提升性能，那么 Doublewrite Buffer 就是保证数据页的可靠性**
MySQL 以「页」为读取和写入单位，一个「页」里面有多行数据，写入数据时，MySQL 会先写内存中的页，然后再刷新到磁盘中的页
这时问题来了，假设在某一次从内存刷新到磁盘的过程中，一个「页」刷了一半，突然操作系统或者 MySQL 进程奔溃了，这时候，内存里的页数据被清除了，而磁盘里的页数据，刷了一半，处于一个中间状态，不尴不尬，可以说是一个「不完整」，甚至是「坏掉的」的页
其实这个时候 Redo Log 也已经无力回天，Redo Log 是要在磁盘中的页数据是正常的、没有损坏的情况下，才能把磁盘里页数据 load 到内存，然后应用 Redo Log。而如果磁盘中的页数据已经损坏，是无法应用 Redo Log 的
所以，MySQL 在刷数据到磁盘之前，要先把数据写到另外一个地方，也就是 Doublewrite Buffer，写完后，再开始写磁盘。Doublewrite Buffer 可以理解为是一个备份（recovery），万一真的发生 crash，就可以利用 Doublewrite Buffer 来修复磁盘里的数据

3.undo logs


4,redo log
