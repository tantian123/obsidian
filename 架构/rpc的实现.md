注册中心 服务端启动时将服务名 ip port 注册到注册中心
服务消费端根据服务名来查询ip和port
然后就能通过网络来请求了

网络传输 使用netty

序列化 hession2、kyro、protostuff

动态代理



1、第一天 极简版本
服务提供者
服务中心
服务消费者

掌握
serversocket socket
和socket通信的api


客户端方面，由于在客户端这一侧我们并没有接口的具体实现类，就没有办法直接生成实例对象。这时，我们可以通过动态代理的方式生成实例，并且调用方法时生成需要的RpcRequest对象并且发送给服务端。

[Java实现简单的RPC框架 - 苍穹2018 - 博客园 (cnblogs.com)](https://www.cnblogs.com/codingexperience/p/5930752.html#!comments)

[从零开始实现简单 RPC 框架 1：RPC 框架的结构和设计 - 小新是也 - 博客园 (cnblogs.com)](https://www.cnblogs.com/chenchuxin/p/15116332.html)

