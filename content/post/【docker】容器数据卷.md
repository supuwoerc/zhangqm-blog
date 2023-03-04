---
title: "【Docker】容器数据卷"
date: 2023-03-04T15:01:46+08:00
menu: "tech"
weight: 50
tags: []
categories: []
draft: false
typora-root-url: ../../static/
typora-copy-images-to: ../../static/
---

## 数据卷

* 数据卷是宿主机和容器存在映射关系的文件或者目录
* 宿主机对数据卷修改会立即同步到容器，反之依然，即映射关系 
* 数据卷和容器是多对多的关系，一个数据卷可以被多个容器挂载，一个容器也可以挂载多个数据卷

## 配置

```shell
#-v:配置数据卷 
docker run ... -v 宿主机目录/文件:容器目录/文件
```

* 目录/文件必须为绝对路径
* 目录不存在时将被创建
* 可以挂载多个数据卷 

## 数据卷容器

* 一个专门的容器，用来作为数据卷，将多个容器和宿主机文件做映射

```shell
# -v：挂载数据卷容器datas,docker将自动在宿主机上创建对应的数据映射目录/文件
docker run -it --name=datas -v /volume centos:7 /bin/bash
# 创建容器test1,和数据卷容器设置数据卷映射关系
docker run -it --name=test1 --volume-form datas centos:7 /bin/bash
# 创建容器test2,和数据卷容器设置数据卷映射关系
docker run -it --name=test2 --volume-form datas centos:7 /bin/bash
```

