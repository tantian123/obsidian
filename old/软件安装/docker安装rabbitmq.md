```
docker pull rabbitmq
docker run -d -p 15672:15672 -p 5673:5672 --name myrabbitmq rabbitmq

docker ps -a 
查看mq容器id

docker exec -it 容器id /bin/bash

在容器内部运行
rabbitmq-plugins enable rabbitmq_management

现在就可以通过页面查看 

http://localhost:15672

输入用户名密码 都是guest
```