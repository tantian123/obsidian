```
docker pull mysql

docker run --name mymysql -e MYSQL_ROOT_PASSWORD=password -d mysql:latest

docker exec -it mymysql mysql -uroot -p

输入密码

进入mysql界面

```



如何使用可视化界面连接docker中的mysql容器

```
docker ps 查看容器是否在运行

使用可视化界面连接Docker中的MySQL与连接本地MySQL数据库的方式相似。只需在连接参数中指定正确的主机名、端口号和凭据即可
```


Docker容器和本地安装的MySQL实例可以在同一端口上运行，因为它们运行在不同的网络命名空间中，它们的IP地址不同，因此不会产生端口冲突。

端口映射功能：

```
docker run -d -p 3366:3306 --name mysqlWeb -e MYSQL_ROOT_PASSWORD=password mysql:latest

docker exec -it mysqlWeb mysql -uroot -p
```



Docker Desktop 在 Windows 操作系统上默认使用 WSL 2（Windows Subsystem for Linux 2）作为后端引擎。当你在 Docker Desktop 上创建一个容器时，Docker 实际上是在 WSL 2 中启动了一个 Linux 容器。