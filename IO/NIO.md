|          | BIO             | NIO                 | AIO                 |
| -------- | --------------- | ------------------- | ------------------- |
| 数据交互 | 面向流 单向传输 | 面向缓冲区 双向传输 | 面向缓冲区 双向传输 |
| IO模型   | 同步阻塞        | 同步非阻塞          | 异步非阻塞          |
|          |                 |                     |                     |

![[Pasted image 20221115235817.png]]
![[Pasted image 20221115235823.png]]


NIO的核心
通道Channel 缓冲区Buffer 多路复用选择器Selector




缓冲区Buffer
由 java.nio 包定义的，所有 **缓冲区** 都是 **Buffer抽象类** 的 **子类**
![[Pasted image 20221116000241.png]]

看源码 
