
在实际生产中通常是通过GC的情况来分析并判断代码出现的问题，而不是修改GC的参数


使用各种JVM工具，查看当前日志，分析当前JVM参数设置，并且分析当前堆内存快照和gc日志，根据实际的各区域内存划分和GC执行时间，觉得是否进行优化



垃圾收集器 ：Java扫描堆内存 并清理掉不用的
分代：java垃圾回收机制，将堆内存分为不同的年龄区域，并对不同的区域使用不同的垃圾清理算法
垃圾回收算法：标记清除 复制 标记整理
引用计数
Stop The World



1.  垃圾收集器 (Garbage Collector)
2.  分代垃圾收集器 (Generational Garbage Collector)
3.  垃圾回收算法 (Garbage Collection Algorithm)
4.  标记-清除算法 (Mark and Sweep Algorithm)
5.  复制算法 (Copying Algorithm)
6.  标记-整理算法 (Mark and Compact Algorithm)
7.  内存分配器 (Memory Allocator)
8.  引用计数 (Reference Counting)
9.  内存泄漏 (Memory Leak)
10.  大对象 (Large Object)
11.  对象存活性分析 (Object Liveness Analysis)
12.  持久代 (Permanent Generation)
13.  年轻代 (Young Generation)
14.  老年代 (Old Generation)
15.  永久代 (PermGen)
16.  堆内存 (Heap Memory)
17.  非堆内存 (Non-Heap Memory)
18.  卡表 (Card Table)
19.  弱引用 (Weak Reference)
20.  软引用 (Soft Reference)
21.  强引用 (Strong Reference)
22.  虚引用 (Phantom Reference)
23.  STW (Stop The World)
24.  并发标记清除 (Concurrent Mark Sweep)
25.  G1垃圾收集器 (G1 Garbage Collector)
26.  ZGC垃圾收集器 (ZGC Garbage Collector)
27.  C4垃圾收集器 (C4 Garbage Collector)
28.  CMS回收器 (CMS Collector)
29.  Epsilon垃圾收集器 (Epsilon Garbage Collector)
30.  Shenandoah垃圾收集器 (Shenandoah Garbage Collector)
31.  GC日志 (GC Logs)
32.  垃圾回收器调优 (Garbage Collector Tuning)
33.  内存分配率 (Allocation Rate)
34.  垃圾回收暂停时间 (GC Pause Time)
35.  并发垃圾回收 (Concurrent Garbage Collection)
36.  垃圾收集器组合 (Garbage Collector Combination)
37.  永久代垃圾回收 (PermGen Garbage Collection)
38.  直接内存 (Direct Memory)
39.  内存池 (Memory Pool)
40.  内存分段 (Memory Segmentation)
41.  内存压缩 (Memory Compression)
42.  内存管理 (Memory Management)
43.  分配失败 (Allocation Failure)
44.  Full GC (Full Garbage Collection)
45.  Minor GC (Minor Garbage Collection)
46.  多线程垃圾回收 (Multithreaded Garbage Collection)
47.  G1垃圾收集器的堆区划分 (G1 Heap Partitioning)
48.  GC Roots (Garbage Collection Roots)
49.  元空间 (Metaspace)
50.  Java对象头 (Java Object Header)