Log-structured merge-tree
Log-structured:以日志的形式存储数据
merge-tree:compaction 机制 将树进行merge合并 

核心的设计思路是以日志的形式存储数据，这也充分利用了磁盘的顺序读写性能高的特点，相比之下，B-tree 需要原地更新数据，所以每次写入就涉及到磁盘寻道、树分割、移动等操作，使得写入性能存在瓶颈

优点：
LSM-tree 在写入优势和数据存储量上有明显优势
SSD 等高性能存储介质普及，LSM-tree 能充分利用其性能
分布式需求增加

缺点：
相比于 b-tree， LSM-tree 最大的劣势就是查询性能，LSM-tree 的查询流程需要从上到下依次查询，直到查询到对应的 key 为止，最坏的情况要查询所有层，此时性能会急剧下降。对此，一些常规的优化手段是给 sstable 文件增加索引并且将其放到内存中，以加快 key 查询
compaction 对性能的影响，compaction 期间会消耗节点的 CPU 和内存，如果资源不足对正常的写入、查询都会有影响。



涉及三个重要的数据组件：`memtable`，`log`，`SSTable`

`memtable`是红黑树或者跳表这样的有序内存数据结构，起到缓存和排序的作用，把新写入的数据按照键的大小进行排序

`SSTable`（Sorted String Table）说白了就是一个特殊格式的文件，其中的数据按照键的大小排列，你可以把它类比成一个有序数组。而 LSM 树，说白了就是若干`SSTable`的集合

`log`文件记录操作日志，在数据写入`memtable`的同时也会刷盘写入到`log`文件，作用是数据恢复。比如在`memtable`中的数据还没转化成`SSTable`持久化到磁盘时，如果突然断电，那么`memtable`里面的数据都会丢失，但有`log`文件在，就可以恢复这些数据

LSM 树的`set`写入过程并不复杂：写入`log`和`memtable`，最后转化成一个`SSTable`持久化到磁盘就行了。








但只有 append-only 还不够，因为数据库不是只有 insert 操作，还有大量的 update、delete 操作，对于 delete 则向 log 中记录一条删除记录，同时查询时只需使用最新的记录就可以满足 delete 和 update 的需求。不难发现，这样的设计非常有利于写入性能，但会占用很多额外的存储空间，即存储空间放大。所以，LSM-tree 还有额外的 compaction 机制，通过 compaction 将重复主键的数据进行清理，同时将数据按照主键进行排序以便后续查询使用，这就是名字中 merge 的来历

以多层的形式组织，越上层的 sstable 越小，越下层的 sstable 越大