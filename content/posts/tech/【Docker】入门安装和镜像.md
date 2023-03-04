---
title: "【Docker】入门安装和镜像"
date: 2023-03-04T18:57:47+08:00
draft: false
categories: ["后端"]
tags: ["工具","docker"]
card: false
weight: 0
---
## 安装

```shell
yum install -y yum-utils device-mapper-persistent-data lvm2 # 安装docker依赖的包
yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo#设置docker源
yum list docker-ce --showduplicates | sort -r # 查看全部docker版本
yum -y install docker-ce-18.03.1.ce # 安装某一个版本
```
## 服务

```shell
systemctl start docker # 启动docker
systemctl enable docker # 设置开机启动docker
systemctl status docker #查看docker状态
systemctl stop docker # 关闭docker
systemctl restart docker # 重启docker
```

## 镜像

```shell
docker images # 查看安装的全部镜像
docker search redis # 搜索全部redis相关镜像
docker pull redis # 下载redis 默认latest 可以指定版本，如：docker pull reids:5.0
docker rmi f9c173b0f012 # 卸载id为f9c173b0f012的镜像 remove image
```

