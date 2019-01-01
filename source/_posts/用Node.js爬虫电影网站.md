---
layout: post
title: "用 Node.js 爬虫电影网站"
date: 2018-12-12
categories: 前端
tags: node.js
comments: true
---

通过 node.js 爬取电影网站中的资源，并且把数据存储在 mongodb 数据库中，后端接口是用基于 node.js 开源的 web 框架 express 实现的，提供给前端调用，前端通过 react 技术栈数据显示在表格中，通过下载按钮，可以直接调用迅雷下载。

## 数据的爬取

我们爬虫的目标网站是阳光电影，如下图所示：

![image](/assets/images/201812/movies.jpg)

先分页爬取电影列表，获得电影标题、详情地址（内含电影说明、种子下载地址），然后并发请求每个单独的电影详情页，全部获取后，再通过接口 `POST api/movies` 将数据批量插入 mongodb 数据库。

通过 superagent 库，在后台中发起资源请求，由于我们要爬虫的电影网站用的是 gbk 编码，导致我们加载的页面是乱码，所以还需要用到一个额外的 superagent-charset 来转换编码。

在我们获取到 `Content-Type: text/html` 的网页后，我们需要读取指定的列表标签，希望能够像 jquery 那样，通过选择器，直接拿到元素，读取其中的属性和值。这个时候，cheerio 库正好符合我们的需求，很方便爬虫网页中的数据，这是很重要的一环，下面用一个简单的示例来说明 cheerio 的基本用法。

```javascript
request
  .get("http://news.baidu.com")
  .then((res = res.json()))
  .then(html => {
    // 通过 cherrio.load 方法加载 html 页面
    const $ = cheerio.load(html);
    // 下面就可以像 jquery 一样选取元素了
    // 如获取热点要闻列表中所有标题
    let titles = [];
    $(".hotnews > ul > li").each(() => {
      titles.push($(this).find("a").text);
    });
  });
```

源码可查看：`server/bin/movie`

## 接口开发

基于 express 框架实现接口的开发，可以很简单的启动一个 web 服务，由于考虑前后端分离，没有用到 express 提供的模板引擎，以及静态资源服务，只用来开发接口。

下面使用最小化的 express 运行一个 web 服务，在命令行执行 `node ap.js` 即可启动服务， 然后在浏览器地址栏打开 localhost:3000，即可在网页中看到 hello world 。

```javascript
// app.js
const express = require("express");
const app = express();
app.get("/", (req, res) => {
  res.send("hello world");
});
app.listen(3000, err => {
  console.log("服务已启动在端口：3000");
});
```

驱动 mongodb 数据库，选用的 mongoose

数据库的模型：标题，说明，下载地址，源地址，创建时间。

```javascript
const MovieSchema = new mongoose.Schema({
  title: String,
  description: String,
  href: String,
  source: String,
  create_date: {
    type: Date,
    default: Date.now()
  }
});
```

### 项目结构

避免按技术角色对文件进行分组，如目录 controllers、models、services 等等，代码过于分散，随着项目的扩展，会出现眼花缭乱，难以管理维护的情况，推荐通过独立组件构建解决方案， 如下面的 modules 里的模块，相关业务更加集中，也方便定位问题。

```bash
├── README.md # 项目说明文档
├── app.js # 应用入口
├── bin # 可执行的脚本
│   └── movie.js # 爬虫数据，并插入数据库
├── config.js # 项目配置
├── modules # 项目模块
│   └── movie # 电影模块
│       ├── movie.controller.js # 业务层，处理接口业务
│       ├── movie.model.js # 定义数据模型
│       └── movie.service.js # 数据层，操作数据库
└── router.js # 项目路由
```

## 前端展示

通过 facebook 官方推荐的脚手架 create-react-app 快速启动一个 react 项目

```javascript
npx create-react-app spider
cd spider
npm start
```

当然，如果需要更加灵活地配置项目，可以通过 `npm run eject` 来释放项目中 webpack 打包配置文件，自己来维护。

## 总结

## 待办

- [ ] 字符集的区别
- [ ] 鸭子模型