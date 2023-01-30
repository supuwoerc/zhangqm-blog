---
title: "使用jenkins自动部署hugo博客"
date: 2023-01-19T16:43:12+08:00
lastmod: 2023-01-19T16:43:12+08:00
menu: "tech"
tags: ["琐碎"]
categories: ["技术"]
weight: 50
---

### 服务器安装jdk
jenkins最新版本要求jdk版本要11或者17的版本，我自己最开始服务器上部署过jdk1.8所以得先卸载掉

* 查询和卸载已经安装过的jdk

```
rpm -qa | grep java #查看已经安装到jdk信息
rpm -e --nodeps java-* #卸载上一步查询到的jdk相关信息<和openjdk相关的jdk全部卸载>
```

* 安装jdk17到服务器 

```
cd /usr/local # 找一个目录安装jdk
wget https://download.oracle.com/java/17/latest/jdk-17_linux-x64_bin.tar.gz
tar -zxvf jdk-17_linux-x64_bin.tar.gz 
```

* 配置环境变量

```
vim /etc/profile #进入profile文件,在文件的最后面添加java老三样的配置
# 在文件最下方添加
export JAVA_HOME=/usr/local/java-17.0.6 #配置下java_home就行,下面的两个变量依赖于它
export PATH=$PATH:$JAVA_HOME/bin;
export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar;
source /etc/profile #刷新环境变量
java -version #检查安装结果 能输出java的版本信息就算安装成功了
```

* jdk准备好之后就可以安装jenkins了

```
yum install -y jenkins #yum安装jenkins
systemctl start jenkins #启动服务
#jenkins在启动之后会在输出信息里面将初始密码打印出来,但是我没注意到,没关系，可以在对应文件里面找到
#jenkins初始密码在这个位置/jenkins_home/secrets/initialAdminPassword
```

* jenkins之后在面板上选择默认的全家桶安装就行了<当然你也可以自己挑选需要的插件和工具>
* 开始配置git

```
#先看自己的服务器有没有生成过ssh-key,没有的话需要先生成
ssh-keygen -t rsa -C "test@gmail.com" #生成ssh-key
```

* 将生成的公钥配置到github/gitlab/gitee上
* 将生成的私钥部署到jenkins的credentials里面
* 配置项目的git地址和构建要执行的脚本，这里我就很简单的配置了一下

```
#!/bin/sh -l
echo "开始构建任务-进入/use/blog"
cd /usr/blog
echo "删除文件夹中全部文件"
rm -rf *
echo "开始克隆远程仓库内容到本地"
git clone git@github.com:supuwoerc/zhangqm-blog.git
echo "进入本地源码文件夹"
cd zhangqm-blog
echo "删除旧的版本"
cd public
rm -rf *
cd ../
echo "执行hugo打包静态资源"
hugo --theme=paper --baseUrl="https://www.supuwoerc.xyz"
echo "移动产物目录到Nginx指向"
cp -rf /usr/blog/zhangqm-blog/public /home
echo "构建成功"
```

* 构建发布
