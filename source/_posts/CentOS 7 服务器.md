---
layout: post
title:  "CentOS 7 服务器"
date:   2017-09-28
categories: 服务器
tags: centos
comments: true
---

## 第 1 章 安装node

步骤1：更新rpm软件包

``` bash
yum update
```

步骤2：安装所需环境

- 编译依赖的环境

``` bash
yum install gcc gcc-c++
```

步骤3：安装node

1. 下载node压缩包
2. 解压
3. 进入node目录
4. 配置
5. 编译
6. 安装

``` bash
wget https://nodejs.org/dist/v8.6.0/node-v8.6.0.tar.gz
tar zxvf https://nodejs.org/dist/v8.6.0/node-v8.6.0.tar.gz
cd node-v8.6.0
./configure
make
make install
```

步骤4：利用淘宝镜像，提高npm下载速度

``` bash
npm install -g cnpm --registry=https://registry.npm.taobao.org
```

参考资料：
- yum命令管理软件包：http://man.linuxde.net/yum
- wget命令用来从指定的URL下载文件：http://man.linuxde.net/wget
- tar命令解压缩备份文件：http://man.linuxde.net/tar
- su命令切换用户身份：http://man.linuxde.net/su

## 第 2 章 安装n

通过n工具，可以安装、删除、切换node版本。

步骤1：安装n

``` bash
npm install -g n
```

步骤2：安装latest、stable、LTS的node

``` bash
n latest
n stable
n lts
```

步骤3：切换node环境

``` bash
n use 8.6.0
```

参考资料：

- n工具管理node.js的版本：https://github.com/tj/n

## 第 3 章 安装mongodb

步骤1：配置yum

在/etc/yum.repos.d目录下新建mongodb-org-3.4.repo文件

```
[mongodb-org-3.4]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/3.4/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://www.mongodb.org/static/pgp/server-3.4.asc
```

步骤2：获取root身份权限
``` bash
su root
```

步骤3：安装MongoDB包和相关的工具

``` bash
yum install -y mongodb-org
```

步骤4：启动、停止、重启MongoDB

``` bash
service mongod start 或 mongod
service mongod stop
service mongod restart
```

步骤5：开机自启动MongoDB服务

``` bash
chkconfig mongod on
```

参考资料：
- 安装MongoDB社区版在Red Hat企业版或CentOS linux系统：https://docs.mongodb.com/master/tutorial/install-mongodb-on-red-hat
- chkconfig命令检查、设置系统的各种服务：http://man.linuxde.net/chkconfig

## 第 4 章 安装git

步骤1：获取root身份权限
``` bash
su root
```

步骤2：安装git

``` bash
yum install git
```

## 第 5 章 安装nginx

nginx 是一个高性能的 Web 和反向代理服务器, 它具有有很多非常优越的特性:

作为 Web 服务器：相比 Apache，Nginx 使用更少的资源，支持更多的并发连接，体现更高的效率，这点使 Nginx 尤其受到虚拟主机提供商的欢迎。能够支持高达 50,000 个并发连接数的响应，感谢 Nginx 为我们选择了 epoll and kqueue 作为开发模型。

作为负载均衡服务器：Nginx 既可以在内部直接支持 Rails 和 PHP，也可以支持作为 HTTP代理服务器 对外进行服务。Nginx 用 C 编写, 不论是系统资源开销还是 CPU 使用效率都比 Perlbal 要好的多。

作为邮件代理服务器: Nginx 同时也是一个非常优秀的邮件代理服务器（最早开发这个产品的目的之一也是作为邮件代理服务器），Last.fm 描述了成功并且美妙的使用经验。

Nginx 安装非常的简单，配置文件 非常简洁（还能够支持perl语法），Bugs非常少的服务器: Nginx 启动特别容易，并且几乎可以做到7*24不间断运行，即使运行数个月也不需要重新启动。你还能够在 不间断服务的情况下进行软件版本的升级。

步骤1：安装所需环境

- 编译依赖的环境：gcc
- 解析正则表达式库：pcre pcre-devel
- 压缩和解压缩库：zlib
- 安全套接字层密码库：openssl openssl-devel

``` bash
yum install gcc-c++
yum install -y pcre pcre-devel
yum install -y zlib zlib-devel
yum install -y openssl openssl-devel
```

步骤2： 安装nginx

1. 下载nginx压缩包
2. 解压
3. 进入nginx目录
4. 配置
5. 编译
6. 安装

``` bash
wget -c https://nginx.org/download/nginx-1.10.1.tar.gz
tar -zxvf nginx-1.10.1.tar.gz
cd nginx-1.10.1
./configure
make
make install
```

常用的命令

启动、停止nginx

``` bash
cd /usr/local/nginx/sbin/
./nginx 
./nginx -s stop  //此方式停止步骤是待nginx进程处理任务完毕进行停止
./nginx -s quit  //此方式相当于先查出nginx进程id再使用kill命令强制杀掉进程
```

重启nginx

方式1：先停止再启动

``` bash
./nginx -s stop
./nginx	
```

方式2：重载配置文件

``` bash
./nginx -s reload
```

查询nginx进程

``` bash
ps aux|grep nginx
```

参考资料：

- CentOS 7 下安装 Nginx：http://www.linuxidc.com/Linux/2016-09/134907.htm
