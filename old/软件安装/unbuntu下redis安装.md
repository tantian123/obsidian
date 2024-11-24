安装依赖项
```
sudo apt update 
sudo apt full-upgrade
sudo apt install build-essential tcl
```

安装redis
```
sudo apt-get install redis-server
```
启动redis,默认自动启动
```
redis-server
```
查看redis端口号
```
ps -ef|grep redis
```
查看redis安装地址
```
sudo ls -l /proc/45351/cwd
```
查看redis状态 可以看到安装地址
```
sudo systemctl status redis-server
```
查看配置文件位置
![[T1MIUSHB{K$LLSOLI(3~ANR.png]]
