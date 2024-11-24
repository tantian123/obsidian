netty的三种IO模式
BIO NIO AIO
netty现在只支持NIO 以前支持BIO AIO
（window下的AIO成熟 linux下的AIO不成熟 但是服务器通常是linux的 并且linux下AIO的性能提升不明显）



对于epoll，jdk的nio默认是水平触发 netty默认是边缘触发

阻塞与非阻塞


同步与异步

reactor是NIO的开发模式 例如BIO的开发模式是thread per connection
AIO的开发模式是proactor

reactor的三种模式
reactor单线程 一个线程负责所有过程
reactor多线程  多个线程负责所有过程
主从reactor多线程 一个或多个线程负责连接 其他线程负责处理请求

1.thread per connection模式
阻塞的连接越多占用的线程越多
![[Pasted image 20231221225225.png]]

2.reactor单线程模式
![[Pasted image 20231221225303.png]]

3.reactor多线程模式
![[Pasted image 20231221225334.png]]
4.主从reactor模式

![[Pasted image 20231221225359.png]]


在netty中实现：EVENTLOOPGROUP默认是cpu核数
![[Pasted image 20231221225446.png]]
