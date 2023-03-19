what 远程过程调用，它允许不同节点之间的应用程序像调用本地函数一样调用远程函数
why 分布式系统中，调用其他服务中的方法
when where 广泛应用于微服务架构和分布式系统中
how RPC协议的实现方式包括定义RPC协议、实现通信模块、实现服务注册和发现、实现服务调用和实现服务端等方面。常用的RPC协议包括Google的Protobuf、Apache的Thrift、Facebook的TChannel等。常用的通信模块包括Netty、Apache的HttpClient等。常用的服务注册和发现框架包括ZooKeeper、Consul、Etcd等。



rpc和http的对比（rpc不是一种协议！）
rpc是一种设计模式 而http是个具体的通讯协议


实现：
1.定义通讯协议
2.实现序列化 反序列化
3.实现服务注册和发现
4.实现客户端代理
5.实现服务端返回
例子
1.使用JSON格式作为通信协议
2.使用FastJSON实现序列化和反序列化功能
3.使用ZooKeeper实现服务注册和发现功能 
4.使用Java的动态代理技术实现客户端代理功能
5.使用Netty作为通信框架实现服务端处理功能

架构可以借鉴dubbo的架构
即注册中心+服务器端+客户端

ip+port注册服务，netty发送要调用的类名 方法名 方法参数
序列化方式有hession2 kyro protostuff
动态代理

拓展点：支持两种注册中心 nacos和zooleeper 可以根据配置切换
最初使用BIO进行通信，后续换成netty
序列化可以采用protobuf hessian
负载均衡可以使用带权重的随机和轮询的方式


最后进行测试
机器配置：
启动几个线程 发送多少个请求 多久完成
qps是多少
