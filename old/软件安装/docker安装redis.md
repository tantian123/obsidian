```
docker pull redis
docker run --name myRedis -p 6379:6379 -d redis

进入容器内部
docker exec -it myRedis redis-cli



```