# Redis

创建redis挂载目录

```
mkdir /usr/local/docker/redis
```

下载最新redis配置文件，并将其转移至挂载目录

```
mv /Users/matemaster/Downloads/redis-7.0.8/redis.conf /usr/local/docker/redis/redis.conf
```

创建容器，并配置启动

```
docker run --name infrastructure-redis -p 6379:6379 -v /usr/local/docker/redis/redis.conf:/etc/redis/redis.conf -v /usr/local/docker/redis/data:/data -d redis redis-server /etc/redis/redis.conf
```

配置redis.conf密码

```
requirepass redis-password
```
