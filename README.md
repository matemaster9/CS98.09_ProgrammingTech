# MySQL

创建容器，并拉取镜像

```
docker run --name mastercs -p 3306:3306 -e MYSQL_ROOT_PASSWORD=wFj9LMRCbeE1gJKZ mysql:8.0.32
```

使用com.docker.cli登陆mysql

```
docker exec it mastercs
```

mysql密码登陆

```
mysql -u root -p
```
