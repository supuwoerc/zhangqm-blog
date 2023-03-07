---
title: "【Docker】容器转为镜像"
date: 2023-03-07T22:46:15+08:00
draft: false
categories: ["后端"]
tags: ["工具","docker"]
card: false
weight: 0
---

## 容器转为镜像

```shell
#将容器转为镜像
docker commit 容器id 镜像名称:版本号 
#将镜像压缩为文件
dokcer save -o 压缩文件名称 镜像名称:版本号
#将镜像压缩文件还原成镜像
docker load -i 文件
```

> 容器挂载到数据卷是不会被带入到镜像中的，但是在容器内部的文件是可以被带入还原为镜像的
