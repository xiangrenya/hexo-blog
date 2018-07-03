---
layout: post
title:  "Web服务器环境安装"
date:   2017-09-28
categories: 服务器
tags: centos
---

国内大多数企业Web服务器都是Centos系统，包括云服务器也是如此。可见其系统的安全性、稳定性是经过市场检验的。我们可以通过在windows或mac os系统下安装虚拟机，再去官网下载iso镜像文件，安装至虚拟机里，当然也可以买个低配的云服务器，做一些有趣的实验。

## 安装Node.js

首先要安装编译环境，接下来才能进行编译和安装。

``` sh
yum install gcc gcc-c++
```

依次执行以下的步骤，完成所有操作，即可安装成功。

``` sh
# 下载node.js压缩包
wget https://nodejs.org/dist/v8.6.0/node-v8.6.0.tar.gz
# 解压
tar zxvf https://nodejs.org/dist/v8.6.0/node-v8.6.0.tar.gz
# 进入工作目录
cd node-v8.6.0
# 配置
./configure
# 编译
make
# 安装
make install
```

因为npm的资源在国外服务器，为了能提高包的下载速度，我们可以利用淘宝提供的镜像，实现快速下载。

``` sh
npm install -g cnpm --registry=https://registry.npm.taobao.org
```

## 安装MongoDB

安装过程如下

```
# 进入yum.repos.d目录
cd /etc/yum.repos.d
# 创建mongodb-org-3.6.repo
vi mongodb-org-3.6.repo
# 插入如下配置
[mongodb-org-3.6]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/3.6/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://www.mongodb.org/static/pgp/server-3.6.asc
# 安装
yum install -y mongodb-org
```

常用的命令

``` sh
# 启动
service mongod start 或 mongod
# 停止
service mongod stop
# 重启
service mongod restart
# 设置MongoDB服务开机启动
chkconfig mongod on
```

## 安装Nginx

nginx 是一个高性能的Web和反向代理服务器, 使用更少的资源，支持更多的并发连接，同时也支持负载均衡。

首先我们得安装依赖的编译环境和工具类

``` sh
# 编译的环境
yum install gcc-c++
# 解析正则表达式库
yum install -y pcre pcre-devel
# 压缩和解压缩库
yum install -y zlib zlib-devel
# 安全套接字层密码库
yum install -y openssl openssl-devel
```

安装过程如下

``` sh
# 进入yum.repos.d目录
cd /etc/yum.repos.d
# 创建ngin.repo
vi nginx.repo
# 插入如下配置
[nginx]
name=nginx repo
baseurl=http://nginx.org/packages/centos/7/x86_64/
gpgcheck=0
enabled=1
# 安装
yum install -y nginx
```

常用的命令

``` sh
# 启动
systemctl start nginx
# 处理任务完毕后停止
systemctl stop nginx
# 强制杀掉进程
systemctl quit nginx
# 重载配置文件
systemctl reload nginx
# 查询nginx进程
ps aux|grep nginx
```

## 安装Shadowshocks

注意事项：1、购买海外的云服务器（如：[vultr](https://www.vultr.com/?ref=7364552)，支持支付宝，很方便交易。2、选择centos6系统，因为centos7默认的防火墙可能会干扰ssr的正常连接。

``` sh
# 安装wget工具
yum -y install wget
# 下载安装脚本
wget -N --no-check-certificate https://raw.githubusercontent.com/ToyoDAdoubi/doubi/master/ssr.sh
# 修改执行权限
chmod +x ssr.sh
# 运行安装脚本
bash ssr.sh
# 安装过程配置：加密方式：aes-256-cfb，协议插件：auth_sha1_v4，设置协议插件兼容原版，混淆插件：plain
# ------
# 谷歌BBR加速
# 下载安装脚本
wget -N --no-check-certificate https://github.com/teddysun/across/raw/master/bbr.sh
# 修改执行权限 
chmod +x bbr.sh
# 运行安装脚本
bash bbr.sh
# 安装完成后重启电脑
# 检查bbr是否已安装并启动成功
lsmod | grep bbr
```

## 参考资料

- [CentOS 7下安装Nginx](http://www.linuxidc.com/Linux/2016-09/134907.htm)
- [在Red Hat企业版或CentOS linux系统上安装MongoDB社区版](https://docs.mongodb.com/master/tutorial/install-mongodb-on-red-hat)
- [Linux命令大全](http://man.linuxde.net/)
- [安装Shadowsocks](https://github.com/Alvin9999/new-pac/wiki/%E8%87%AA%E5%BB%BAss%E6%9C%8D%E5%8A%A1%E5%99%A8%E6%95%99%E7%A8%8B)
