
b树的结构
索引与b树的联系
b树的并发控制


每一个record有key加value组成，非叶子节点的key保存子节点中的最小key
value指向叶子节点；
叶子节点的key指向该数据行的唯一属性组成的若干列，value指向其余列

同一page内的record形成record list
同一高度的page连接成双向链表 page list


聚簇索引、非聚簇索引，主键索引、二级索引（非主键索引）的区别：
按照物理层面索引放的位置（数据跟索引结构在一起），分为聚集索引和非聚集索引
按照包含的字段分为 主键索引 唯一索引 普通索引 前缀索引


在聚簇索引里，leaf page 的 value 为表中一条数据的某几个字段或所有字段，一定包含主键字段。而非聚簇索引 leaf page 的 value 是 record id，即指向一条数据的指针。

在使用聚簇索引时，主键索引的 leaf page 包含所有字段，二级索引的 leaf page 包含主键和索引字段。当使用主键查询时，查询到 leaf page 即可获得整条数据。当使用二级索引查询时，若查询字段包含在索引内，可以直接得到结果，但如果查询字段不包含在索引内，则需使用得到的主键字段在主键索引中再次查询，以得到所有的字段，进而得到需要查询的字段，这就是回表的过程

 在使用非聚簇索引时，无论是使用主键查询还是二级索引查询，最终得到的结果都是 record id，需要使用 record id 去查询真正对应的整条记录。 聚簇索引的优点是，整条记录直接存放在 leaf page，无需二次查询，且缓存命中率高，在使用主键查询时性能比较好。缺点则是二级索引可能需要回表，且由于整条数据存放在 leaf page，更新索引的代价很高，页分裂、合并等情况开销比较大。

非聚簇索引的优点是，由于 leaf page 仅存放 record id，更新的代价较低，二级索引的性能和主键索引几乎相同。缺点是查询时均需使用 record id 进行二次查询

 
```
treePage{
header
data:(k:v)
}


treeInternalPage extends treePage{
InternalPage的 KV 对中，k是索引 V是pageId，指向下一层

}
treeLeafPage extends treePage{
next_page_id 指向下一个 leaf page 用于range scan
leaf page 的 KV 对中，K 是实际的索引，V 是 record id，record id 用于识别表中的某一条数据
}


```





基于latch的并发控制
特殊的加锁方式，叫做 latch crabbing。顾名思义，就像螃蟹一样，移动一只脚，放下，移动另一只脚，再放下。基本思想是： 1. 先锁住 parent page， 2. 再锁住 child page， 3. 假设 child page 是_安全_的，则释放 parent page 的锁。安全指当前 page 在当前操作下一定不会发生 split/steal/merge。同时，安全对不同操作的定义是不同的，Search 时，任何节点都安全；Insert 时，判断 max size；Delete 时，判断 min size。












https://zhuanlan.zhihu.com/p/580014163



