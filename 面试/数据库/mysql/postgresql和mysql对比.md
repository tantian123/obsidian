pg的主从复制是物理复制  通过WAL日志记录数据的变化
mysql是通过binlog来记录sql
Postgres 也支持通过其发布/订阅模式进行逻辑复制

pg的性能远远好于mysql

在SQL的标准实现上要比MySQL完善 更加学院派

MySQL 的事务隔离级别 repeatable read 并不能阻止常见的并发更新, 得加锁才可以, 但悲观锁会影响性能, 手动实现乐观锁又复杂. 而 Postgre SQL 的列里有隐藏的乐观锁 version 字段, 默认的 repeatable read 级别就能保证并发更新的正确性, 并且又有乐观锁的性能.
