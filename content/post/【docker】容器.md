---
title: "【Docker】容器"
date: 2023-03-04T14:02:14+08:00
menu: "tech"
weight: 50
tags: []
categories: []
draft: false
typora-root-url: ../../static/
typora-copy-images-to: ../../static/
---

## 创建&关闭容器

```shell
#-i:交互式操作，使得容器保持运行 -t:分配给容器一个终端来运行 --name容器名字 /bin/bash可以不加,加上会进入容器shell
docker run -i -t --name=test redis:5.0 /bin/bash
# 进入容器之后执行exit退出容器，如果是-t创建的容器，exit会关闭容器
exit
#-d:守护式容器，后台运行容器，exit执行不会关闭容器
docker run -i -d --name=test redis:5.0
```

## 查看&进入容器

```shell
#查看当前运行的容器
docker ps
#查看全部容器，包含未运行中的容器
docker ps -a
# 进入后台运行的容器test
docker exec -it test /bin/bash
```

## 运行&停止&删除&查看容器

```shell
#停止容器test
docker stop test
#启动容器test
docker start test
#删除容器test
docker rm test
#查看容器test的信息
docker inspect test
```

