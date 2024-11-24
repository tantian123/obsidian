一些问题的推导：
1.Docker 是什么，优点是什么，解决了什么问题=与其他技术的对比=底层原理
2相关名词进行头脑风暴（讲述核心概念）
3.实践（相关指令）=如何使用


1.docker是容器化技术的一种，
优点：隔离 可移植 占用小 启动快
和虚拟机相比，容器使用了宿主机的操作系统 虚拟机是虚拟出了一个完整的操作系统
占用空间小，

 LXC（Linux Containers），即Linux容器，是一种**操作系统层级**的虚拟化技术
 创造出应用程序的独立沙箱运行环境，使得Linux用户可以容易的创建和管理系统或应用容器
核心还是linux 内核的cgroup + chroot + namespace 技术


和虚拟机相比，
底层原理是LXC,Docker或者其他的虚拟化容器都是基于LXC 的技术，在基础的lxc 上包了一层代码，让LXC 更简单、更友好，更加好推广
LXC 的三个技术:chroot: 创建一个虚拟的根目录文件系统（chroot 机制可以指定虚拟根目录，让不同的容器在不同的根目录下工作：不同的根目录导致不同的容器之间不能相互影响但是共享了底层的文件系统）
namespace : 命名空间可以隔离网络和进程
cgroups: 实现了对容器的资源分配和限制

UnionFS 联合文件系统（Union File System）
它可以把多个目录内容联合挂载到同一个目录下，而目录的物理位置是分开的。
即实现了层级的镜像结构，每一个外层是对内层的更改，
作用是将所有的目录都挂载在一个根目录上，我们对镜像的修改只是对外层，
通过共享底层，减少了内存占用

docker是如何利用UnionFS的：
已知新镜像就是从基础镜像上一层层叠加新的逻辑构成的，
想象这样一个场景，一台宿主机上运行了100个基于debian base镜像的容器，
借助Linux的unionFS，宿主机只需要在磁盘上保存一份base镜像，内存中也只需要加载一份，就能被所有基于这个镜像的容器共享

本质：
 **容器和镜像的区别就在于，所有的镜像都是只读的，而每一个容器其实等于镜像加上一个可读写的层，也就是同一个镜像可以对应多个容器**
![[Pasted image 20241022212658.png]]


核心概念：镜像 容器 仓库  docker架构 dockerfile docker compose
镜像是容器的静态模板，容器是镜像的运行实例，仓库是存放并共享镜像的地方
架构是cs模式，执行的命令都是在docker客户端发给服务端的

Dockerfile 是一个文本文件，其内包含了一条条的指令(Instruction)，每一条指令构建一层


dockercompose是容器编排工具，用来运行多个docker应用（这部分先不看）
例如一个项目需要前端 后端和负载均衡 缓存等多个服务器 ，这些服务是独立且互相关联的
这些服务的关联关系就是docker-compose.yaml配置文件，使用docker compose up就可以统一管理这些服务


Docker的四种网络模型
Bridge模式（默认），与宿主机共享一个局域网（Docker会从RFC1918所定义的私有IP网段中，选择一个和宿主机不同的IP地址和子网分配给docker0，连接到docker0的容器就从这个子网中选择一个未占用的IP使用）
Host模式，与宿主机共享一个网络（容器将不会获得一个独立的 Network Namespace，而是和宿主机共用一个 Network Namespace 容器将不会虚拟出自己的网卡，配置自己的IP等，而是使用宿主机的 IP 和端口）
Container模式，与另一个容器共享一个网络
None模式，封闭网络



3.创建dockerfile 
使用dockerfile创建镜像 
使用镜像创建和运行容器
使用镜像创建和运行容器
```
docker build -t hello-docker .
docker image ls
docker run hello-docker
```

指令
```
docker images
docker pull 镜像名
docker run 镜像名
docker ps

```









[【重识云原生】第六章容器6.1.8节——Docker核心技术UnionFS-CSDN博客](https://blog.csdn.net/junbaozi/article/details/126689860)
