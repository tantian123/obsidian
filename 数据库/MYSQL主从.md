https://zhuanlan.zhihu.com/p/67325171

why?
可用性 扩展性：读写分离 

主从复制和主从切换
数据从master复制到slave
master宕机时切换到一台slave上


主从复制
master写入数据后，需要数据同步给slave（binlog）

连接到master的每个副本都请求binlog的一个副本
它从master中提取数据，而不是由master将数据推送到副本


binlog又是怎么被传递的
MySQL 复制功能使用三个主线程实现，一个在源服务器上，两个在副本上:
mater dump thread 负责将binlog发送给slave  
二进制日志转储线程。源创建一个线程，在副本连接时将二进制日志内容发送到副本。这个线程可以在源代码上 SHOW PROCESSLIST 的输出中标识为 Binlog Dump 线程。

二进制日志转储线程在源的二进制日志上获取一个锁，用于读取要发送到副本的每个事件。一旦读取了事件，即使在事件发送到副本之前，锁也会被释放。

slave io thread 负责接收binlog 并转为relay log
复制 I/O 线程。当在副本服务器上发出 START SLAVE 语句时，副本将创建一个 I/O 线程，该线程连接到源并请求它发送记录在其二进制日志中的更新。
复制 I/O 线程读取源的 Binlog Dump 线程发送的更新(参见前一项) ，并将它们复制到组成复制的中继日志的本地文件

slave sql thread 负责执行relay log
复制 SQL 线程。副本创建一个 SQL 线程来读取由复制 I/O 线程写入的中继日志，并执行其中包含的事务。
具有多个副本的源为每个当前连接的副本创建一个二进制日志转储线程，并且每个副本具有自己的复制 I/O 和 SQL 线程。

这样的设计类似于使用消息中间件
1.使用异步通信解耦
2.使用类似消息缓冲区 提高处理效率


主从切换
如果要保证强一致性，就要牺牲可用性
1.master设为read-only,时间记做time1
2.等待slave把master发过来的binlog执行完成，此时为time2
3.此时可以把slave的read-only设为false，完成切换

在这个过程中，从time1到time2的时间，系统是无法被写入的，即不可用




binlog的格式：
在 MySQL 5.7.7之前，默认的格式是基于语句的，在 MySQL 5.7.7及更高版本中，默认的格式是基于行的

语句格式：记录执行的sql语句
缺点是now() uuid() 等执行的结果不一致

行格式：记录更改的行
缺点是如果一句sql的影响大，日志会很大

混合格式