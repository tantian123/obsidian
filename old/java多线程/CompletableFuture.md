
技术背景


源码
```
public class CompletableFuture<T> implements Future<T>, CompletionStage<T>
```
CompletionStage表示计算的一个步骤 Future表示异步计算的结果

我们可以使用thenApply和thenCompose来编排这些步骤



使用
零依赖的future

1.使用runAsync或者是supplyAsync
2.或者是使用CompletableFuture.completedFuture返回一个完成的
3.或者是new 一个CompletableFuture 然后调用complete方法


第三种常用于转化回调方法作为一个CompletableFuture 再使用它的编排能力
即创建一个返回CompletableFuture的方法

一元依赖
对于单个CompletableFuture的依赖可以通过thenApply thenAccept thenCompose来实现

二元依赖
通过thenCombine来实现

多元依赖
通过allOf或者anyOf来实现







## 原理
两个字段 result和stack
result表示异步回调的结果 stack表示完成后需要触发的依赖动作


设计思想就是观察者模式



## 使用
1.一定要传线程池 ，如何实现线程池隔离呢
如果是默认的线程池，会调用forkjoinpool中的commonpool 核心线程数=处理器数量-1
所有的异步回调都会共用该commonpool 

2.由于异步执行的任务在其他线程上执行，异常信息存储在线程栈中，无法通过trycatch捕获异常，所以就有了exceptionally 相当于同步程序中的trycatch