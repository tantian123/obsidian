网络三要素:
ip地址 端口号（标志进程的逻辑地址） 协议

**IP地址 = 网络地址 + 主机地址**

对于IP，Java中专门设有一个类，来描述它 —— **InetAddress类**;
**常用API**：

> -   public static InetAddress getByName(String host):  
>     根据**名称**获取 **IP地址**
> -   public String getHostAddress()：  
>     获取IP
> -   public String getHostName()：  
>     获取主机名
> -   getLocalHost():  
>     获取本地IP地址



# Socket
**Socket = IP + 端口号**  
网络上具有唯一标识的**IP地址**和**端口号**组合在一起，  
才能构成**唯一能识别的标识符套接字**


**Socket的分类**：
1.  遵循**UDP协议**实现的 **DatagramSocket**
2.  遵循**TCP协议**实现的 **ServerSocket(服务端Socket)** 与 **Socket(客户端Socket)**




# ServerSocket与Socket

首先，本人来讲解下 ServerSocket 类：

## ServerSocket 类：

**概述**：

> 这个类实现了**服务器套接字**  
> 该类是遵循 TCP协议的，所以，必须要和客户端Socket建立连接，才能完成信息的接送  
> 服务器套接字**等待来自网络的请求**。  
> 它基于该请求执行某些操作，然后可能向请求者返回结果。  
> 服务器套接字的实际工作由SocketImpl类的一个实例进行。  
> 一个应用程序可以**更改创建套接字实现的套接字工厂**，  
> 以**配置**自己创建**适合本地防火墙的套接字**

那么，本人再来展示下这个类的**构造方法**：  
**构造方法**：

> -   ServerSocket()  
>     创建一个绑定服务器套接字
> -   ServerSocket(int port)  
>     创建一个服务器套接字，绑定到**指定的端口**
> -   ServerSocket(int port, int backlog)  
>     创建一个服务器套接字，并将其绑定到**指定的本地端口号**，并使用**指定的积压**
> -   ServerSocket(int port, int backlog, InetAddress bindAddr)  
>     用指定的端口创建一个服务器，听**积压**，和**本地IP地址**绑定

现在，本人再来展示下这个类的**API**：  
**API**：

> -   Socket accept()  
>     监听要对这个套接字作出的连接并接受它
> -   void bind(SocketAddress endpoint)  
>     ServerSocket绑定到一个特定的地址（IP地址和端口号）
> -   void bind(SocketAddress endpoint, int backlog)  
>     ServerSocket绑定到一个特定的地址（IP地址和端口号）
> -   void close()  
>     关闭这个套接字。
> -   ServerSocketChannel getChannel()  
>     返回与此套接字关联的独特的 ServerSocketChannel对象，如果任何
> -   InetAddress getInetAddress()  
>     返回此服务器套接字的本地地址
> -   int getLocalPort()  
>     返回此套接字正在侦听的端口号
> -   SocketAddress getLocalSocketAddress()  
>     返回此套接字绑定到的端点的地址
> -   int getReceiveBufferSize()  
>     得到这个 ServerSocket的 SO_RCVBUF期权的价值，即该缓冲区的大小，将用于接受来自这 ServerSocket插座
> -   boolean getReuseAddress()  
>     如果 SO_REUSEADDR启用
> -   int getSoTimeout()  
>     检索设置 SO_TIMEOUT
> -   protected void implAccept(Socket s)  
>     子类使用此方法重载ServerSocket（）返回自己的子类的插座
> -   boolean isBound()  
>     返回的ServerSocket绑定状态
> -   boolean isClosed()  
>     返回的ServerSocket关闭状态
> -   void setPerformancePreferences(int connectionTime, int latency, int bandwidth)  
>     设置此ServerSocket性能偏好
> -   void setReceiveBufferSize(int size)  
>     设置一个默认值为提出接受这 ServerSocket插座 SO_RCVBUF选项
> -   void setReuseAddress(boolean on)  
>     启用/禁用 SO_REUSEADDR套接字选项
> -   static void setSocketFactory(SocketImplFactory fac)  
>     设置服务器套接字实现工厂为应用程序。
> -   void setSoTimeout(int timeout)  
>     启用/禁用 SO_TIMEOUT以指定的超时时间，以毫秒为单位
> -   String toString()  
>     返回此套接字作为 String实现的地址与端口

由于该类必须与Socket类对象建立连接后才能进行正常的网络通信，所以，本人在讲解完Socket类之后再来展示部分API的使用。

---

## Socket类：

**概述**：

> 这个类实现了**客户端套接字**（也被称为“套接字”）  
> 该类遵循TCP协议，所以必须与ServerSocket建立连接后，才能进行信息的接送  
> 套接字是两台机器之间的通信的**一个端点**  
> 套接字的实际工作是由该类的一个实例进行SocketImpl  
> 一个应用程序，通过改变创建套接字实现的套接字工厂，可以配置自己创建适合本地防火墙的套接字

现在，本人来展示下这个类的**构造方法**：  
**构造方法**：

> -   Socket()  
>     创建一个连接的套接字，与socketimpl系统默认的类型。
> -   Socket(InetAddress address, int port)  
>     创建一个流套接字，并将其与指定的IP地址中的指定端口号连接起来。
> -   Socket(InetAddress host, int port, boolean stream)  
>     过时的。  
>     使用UDP传输DatagramSocket。
> -   Socket(InetAddress address, int port, InetAddress localAddr, int localPort)  
>     创建一个套接字，并将其与指定的远程端口上的指定的远程地址连接起来。
> -   Socket(Proxy proxy)  
>     创建一个连接的套接字类型，指定代理，如果有，应该使用无论任何其他设置。
> -   protected Socket(SocketImpl impl)  
>     创建一个用户指定的socketimpl连接插座。
> -   Socket(String host, int port)  
>     创建一个流套接字，并将其与指定的主机上的指定端口号连接起来。
> -   Socket(String host, int port, boolean stream)  
>     过时的。  
>     使用UDP传输DatagramSocket。
> -   Socket(String host, int port, InetAddress localAddr, int localPort)  
>     创建一个套接字，并将其连接到指定的远程端口上的指定的远程主机上

那么，本人再来展示下这个类的**API**：  
**API**：

> -   void bind(SocketAddress bindpoint)  
>     将套接字绑定到本地地址。
> -   void close()  
>     关闭这个套接字。
> -   void connect(SocketAddress endpoint)  
>     将此套接字连接到服务器。
> -   void connect(SocketAddress endpoint, int timeout)  
>     将此套接字与指定的超时值连接到服务器。
> -   SocketChannel getChannel()  
>     返回与此套接字关联的独特的 SocketChannel对象，如果任何。
> -   InetAddress getInetAddress()  
>     返回套接字连接的地址。
> -   InputStream getInputStream()  
>     返回此套接字的输入流。
> -   boolean getKeepAlive()  
>     如果 SO_KEEPALIVE启用。
> -   InetAddress getLocalAddress()  
>     获取绑定的套接字的本地地址。
> -   int getLocalPort()  
>     返回此套接字绑定的本地端口号。
> -   SocketAddress getLocalSocketAddress()  
>     返回此套接字绑定到的端点的地址。
> -   boolean getOOBInline()  
>     如果 SO_OOBINLINE启用。
> -   OutputStream getOutputStream()  
>     返回此套接字的输出流。
> -   int getPort()  
>     返回此套接字连接的远程端口号。
> -   int getReceiveBufferSize()  
>     得到这个 Socket的 SO_RCVBUF选项的值，是由平台用于该 Socket输入缓冲区的大小。
> -   SocketAddress getRemoteSocketAddress()  
>     返回此套接字连接的端点的地址，或如果它是无关的 null。
> -   boolean getReuseAddress()  
>     如果 SO_REUSEADDR启用。
> -   int getSendBufferSize()  
>     得到这个 Socket的 SO_SNDBUF期权价值，即缓冲区的大小由平台用于输出在这 Socket。
> -   int getSoLinger()  
>     返回设置 SO_LINGER。
> -   int getSoTimeout()  
>     返回设置 SO_TIMEOUT。
> -   boolean getTcpNoDelay()  
>     如果 TCP_NODELAY启用。
> -   int getTrafficClass()  
>     获取从这个套接字发送的数据包的IP头中的业务类或服务类型
> -   boolean isBound()  
>     返回套接字的绑定状态。
> -   boolean isClosed()  
>     返回套接字的关闭状态。
> -   boolean isConnected()  
>     返回套接字的连接状态。
> -   boolean isInputShutdown()  
>     返回套接字连接的读半是否关闭。
> -   boolean isOutputShutdown()  
>     返回套接字连接的写是否关闭的是否关闭。
> -   void sendUrgentData(int data)  
>     在套接字上发送一个字节的紧急数据。
> -   void setKeepAlive(boolean on)  
>     启用/禁用 SO_KEEPALIVE。
> -   void setOOBInline(boolean on)  
>     启用/禁用 SO_OOBINLINE（TCP紧急数据收据）默认情况下，此选项是禁用TCP套接字上接收紧急数据是默默丢弃。
> -   void setPerformancePreferences(int connectionTime, int latency, int bandwidth)  
>     设置此套接字的性能首选项。
> -   void setReceiveBufferSize(int size)  
>     集 SO_RCVBUF选项，这 Socket指定值。
> -   void setReuseAddress(boolean on)  
>     启用/禁用 SO_REUSEADDR套接字选项。
> -   void setSendBufferSize(int size)  
>     设置这个 Socket指定值的 SO_SNDBUF选项。
> -   static void setSocketImplFactory(SocketImplFactory fac)  
>     设置客户端套接字实现工厂的应用程序。
> -   void setSoLinger(boolean on, int linger)  
>     启用/禁用 SO_LINGER与指定的逗留的时间秒。
> -   void setSoTimeout(int timeout)  
>     启用/禁用 SO_TIMEOUT以指定的超时时间，以毫秒为单位。
> -   void setTcpNoDelay(boolean on)  
>     启用/禁用 TCP_NODELAY（禁用/启用Nagle的算法）。
> -   void setTrafficClass(int tc)  
>     集交通类或从该套接字发送数据包的IP报头字节型服务。
> -   void shutdownInput()  
>     将此套接字的输入流放在“流结束”中。
> -   void shutdownOutput()  
>     禁用此套接字的输出流。
> -   String toString()  
>     将这一 String插座



