---
layout: post
title:  "Node.js入门"
date:   2017-09-28
categories: 后端
tags: node
comments: true
---

## Node.js简介

[官网上](http://www.nodejs.org)给Node下的定义是:“ 一个搭建在Chrome JavaScript运行时 上的平台，用于构建高速、可伸缩的网络程序。Node.js采用的事件驱动、非阻塞I/O模型，使它既轻量又高效，并成为构建运行在分布式设备上的DIRT数据密集型实时程序的完美选择。”

## 安装配置

- [Node](http://nodejs.cn)
- [MySQL](https://www.mysql.com)
- [Redis](https://redis.io)
- [Git](https://git-scm.com)
- [VSCode](https://code.visualstudio.com)
- [Navicat for MySQL](http://pan.baidu.com/s/1gfgoIUB)
- [Redis-Desktop-Manager](http://pan.baidu.com/s/1jHFeS9C)
- [Postman](https://chrome.google.com/webstore/detail/postman/fhbjgbiflinjbdggehcddcbncdddomop?hl=zh-CN)
- [Source Code Pro](https://github.com/adobe-fonts/source-code-pro)

## 技术选型

- UI
    - [bootstrap](http://www.bootcss.com)
    - [themes](https://bootswatch.com])

- Web
    - [express](http://www.expressjs.com.cn)

- MySQL
    - [mysqljs](https://github.com/mysqljs/mysql)

- View
    - [ejs-mate](https://github.com/JacksonTian/ejs-mate)

- Middlewares
    - [body-parser](https://github.com/expressjs/body-parser)
    - [cookie-parser](https://github.com/expressjs/cookie-parser)
    - [errorhandler](https://github.com/expressjs/errorhandler)
    - [connect-redis](https://github.com/tj/connect-redis)
    - [helmet](https://github.com/helmetjs/helmet)

- Utils：
    - [eventproxy](https://github.com/JacksonTian/eventproxy)
    - [log4js](https://github.com/nomiddlename/log4js-node)
    - [moment](http://momentjs.cn)

- Server
    - [supervisor](https://github.com/petruisfan/node-supervisor)
    - [pm2](https://github.com/Unitech/PM2)

## 快速启动项目

``` sh
# 安装package.json里的项目依赖包
cnpm install
# 启动redis服务
redis-server
```

在Mac电脑中，打开系统偏好设置，找到MySQL，启动服务。

``` sh
# 开发环境启动
nodemon app
# 产线部署
pm2 start app
```

## 参考资料

- 《深入浅出Node.js》朴灵
