# docker

1.拉取镜像

```
docker pull 镜像名
docker pull redis
```

2.查看镜像名

```
docker images
```

3.运行镜像

```
docker run -d(后台方式运行) -p(暴露端口号) 6379（虚拟机的）:6379（容器的）--name 自己去得别名 镜像名
docker run -d -p 6379:6379 --name myredis redis
```

4.查看容器运行状况

```
docker ps -a（全部）
```

5若后面要启动

```
docker start 第四步查看的id号
```

