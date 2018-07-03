---
layout: post
title:  "Node.js应用"
date:   2017-09-28
categories: 后端
tags: node
comments: true
---

## 第 1 章 Node简介

### 1.1 Node.js是什么

[官网上](http://www.nodejs.org)给Node下的定义是:“ 一个搭建在Chrome JavaScript运行时 上的平台，用于构建高速、可伸缩的网络程序。Node.js采用的事件驱动、非阻塞I/O模型，使它既轻量又高效，并成为构建运行在分布式设备上的DIRT数据密集型实时程序的完美选择。”

#### 2、安装配置



### 一、环境安装

- [Node](http://nodejs.cn)
- [MySQL](https://www.mysql.com)
- [Redis](https://redis.io)
- [Git](https://git-scm.com)
- [VSCode](https://code.visualstudio.com)
- [Navicat for MySQL](http://pan.baidu.com/s/1gfgoIUB)
- [Redis-Desktop-Manager](http://pan.baidu.com/s/1jHFeS9C)
- [Postman](https://chrome.google.com/webstore/detail/postman/fhbjgbiflinjbdggehcddcbncdddomop?hl=zh-CN)
- [Source Code Pro](https://github.com/adobe-fonts/source-code-pro)

### 二、技术选型

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

### 三、快速启动项目

步骤1：安装package.json里的项目依赖文件

``` bash
cnpm install
```

步骤2：启动redis服务

```
redis-server
```

步骤3：启动mysql服务

打开系统偏好设置中MySQL，启动服务

步骤4：启动应用

``` bash
node app
```

步骤5：产线部署

``` bash
pm2 start app.js
```

### 四、参考资料

#### 1、书籍

- 《深入浅出Node.js》朴灵
