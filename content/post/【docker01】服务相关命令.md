---
title: "【Docker01】服务相关命令"
date: 2023-03-03T23:26:50+08:00
menu: "main"
weight: 50
tags: []
categories: []
draft: false
typora-root-url: ../../static/
typora-copy-images-to: ../../static/
---

## docker 安装

```shell
yum install -y yum-utils device-mapper-persistent-data lvm2 # 安装docker依赖的包
yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo#设置docker源
yum list docker-ce --showduplicates | sort -r # 查看全部docker版本
yum -y install docker-ce-18.03.1.ce # 安装某一个版本
```
## 服务相关命令

```shell
systemctl start docker # 启动docker
systemctl enable docker # 设置开机启动docker
systemctl status docker #查看docker状态
systemctl stop docker # 关闭docker
systemctl restart docker # 重启docker
```

