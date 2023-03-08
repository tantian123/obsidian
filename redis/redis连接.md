1.启动redis

2.虚拟机 ，开放端口号6379

```
1、查看端口状态，比如redis 6379

firewall-cmd --zone=public --query-port=6379/tcp
2、如果是no-表示关闭，yes-表示开启

[root@localhost ~]# firewall-cmd --zone=public --query-port=6379/tcp
no
3、开启状态
[root@localhost ~]# firewall-cmd --zone=public --add-port=6379/tcp --permanent
success -- 表示开启成功
4、防火墙重载

[root@localhost ~]# firewall-cmd --reload
success
5、再次查看端口状态

[root@localhost ~]# firewall-cmd --zone=public --query-port=6379/tcp
yes
```

3.springboot连接方式

SpringDataRedis的使用步骤
引入依赖，配置yml，注入RedisTemplate