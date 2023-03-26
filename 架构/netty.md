what
基于Java NIO的网络编程框架
why
高性能、易于使用 在大量的连接的情况下保持低延迟和高吞吐量，支持多种网络协议
when where
how
创建bootstrap对象，设置参数，然后创建 Channel 对象，并设置对应的 ChannelHandler 实现，即可开始监听和处理连接请求和数据传输


1.Java NIO 的相关概念和使用方法
2.Netty 支持的多种网络协议，包括 TCP、UDP、HTTP、WebSocket 等
3.Netty 提供了多种编解码器，用于将数据从二进制格式转换为 Java 对象或其他格式
4.Netty的重要组件
5.应用实践



1.为什么不使用NIO
学习成本高

2.搭建一个netty的hello world工程
```

```




netty组件

Channnel 通道 
（类似工厂的生产线的传送带）
负责传输数据

EventLoop与EventLoopGroup 
（类似工人小组和工人，负责处理需求）
当有连接请求时，会安排响应的处理器进行处理
EventLoopGroup 是一个线程池，包含多个 EventLoop 实例 ，实现负载均衡和多路复用
为什么叫group 因为group是一个死循环，不断监听注册的事件，并进行处理


ServerBootStrap与BootStrap
（类似工厂的策划团队，负责生产前的各种基础设施和准备工作）
用于配置和启动 Netty 服务器，负责初始化并配置 EventLoopGroup、Channel、ChannelInitializer 等组件，并绑定网络端口，开始监听客户端的连接请求

一个是服务端的 一个是客户端的

ChannelHandler
类似包装工人
将原始数据转换成其他的格式，支持了复杂的数据处理

ChannelPipeline
类似生产线，包括多个ChannelHandler

ChannelFuture
类似快递包裹
用于表示网络操作的执行结果和状态

NioEventLoop 本身就是一个 Executor

![[Pasted image 20230326221129.png]]

