---
layout: post
title: '前端技术沙龙：BigPipe 的应用'
date: 2019-3-23
categories: 前端
comments: true
---

## BigPipe 是什么？

2010 年的 Facebook 提出 BigPipe 技术，通过将站点分解为多个 pagelet 小块，每个 pagelet 获取数据与渲染均是独立的。

BigPipe 模式可以实现 pagelet 的数据一旦返回，就可以无阻塞的在浏览器端进行渲染，以此来实现大型复杂页面的性能加速。

![facebook.jpg](/assets/images/bigpipe/facebook.png)

## 分块传输编码

分块传输编码是在 HTTP/1.1 版本中引入的一种数据传输机制，允许服务器为返回的内容维持持久连接，向客户端发送多个分块的数据 。

当我们在请求一个图片资源的时候，浏览器可以通过响应头中 `Content-Length` 长度信息，判断出响应体结束 。

但是，大多数情况下，服务端输出的内容长度不能确定，无法通过长度信息，来判断实体的边界，这个时候就就需要使用分块传输编码，在响应头中会出现了 `Transfer-Encoding: chunked` 这样的标识。

每个经过 chunked 编码后的分块包含两个部分：数据以及数据的长度信息，当遇到分块长度为 0，表示该分块没有内容，实体结束。

`Content-Encoding` 和 `Transfer-Encoding` 二者经常会结合来用，对传输的内容编码压缩，提高传输效率。

BigPipe 就是基于分块传输编码，实现页面的分块加载。

## 服务端首屏渲染

首屏的显示时间，直接关系到的用户体验，时间太长，就有可能造成用户流失。 下面我们来比对一下两种不同的首屏加载方式：

### 同步加载首屏方式

![sync.png](/assets/images/bigpipe/sync.png)

传统的后端模板渲染，属于同步加载的方式 当我们打开首页的时候，后端模板中的各个模块，同时调用请求数据，渲染模板，需要当所有模板都渲染完成后，才能将整个网页输出到浏览器。

缺点就是首屏的显示时间，受限于处理最慢的模块，如图中的 B 模块，白屏的时间会比较长，同时首屏内容也过于笨重，性能不好，用户体验差。

### 分块加载首屏方式

![chunk.png](/assets/images/bigpipe/chunk.png)

通过将页面切割成不同的模块，然后向客户端发送多个分块数据。

如上图所示，首先让整体布局 layout 输出给浏览器，然后 AB 模块在后端同时发起请求数据，渲染模板。那个模块先处理完，就可以优先输出到浏览器，无需等到所有的模块都渲染完。

首屏显示的时间，几乎就是 layout 的处理时间，所以首屏显示是毫秒级的，用户几乎感知不到白屏时间的存在，但是要等到所有模块都处理完，响应体才能结束。

## 基于 Node.js 实现 BigPipe

BigPipe 的服务端可以用各种语言实现，我们这里介绍的是 Node.js，因为有了 Node BFF，Bigpipe 方案可以有前端来实现。

- response.write(chunk[, encoding][, callback])
- response.end([data][, encoding][, callback])

当我们多次调用 `response.write`，数据自动被流式传输，向浏览器提供多了连续的响应体片段。
chunk 可以是字符串或 buffer 类型， `res.end` 用来告诉服务器，已发送所有响应头和主体，callback 是成功执行后的回调方法。

下面是一个最小化的代码示例：

```javascript
const server = http.createServer(async(req, res) = >{
    res.statusCode = 200;
    res.setHeader('Content-Type', 'text/html');
    res.write(` < !DOCTYPE html >
                <html lang = "en" >
                <head ><title > BigPipe < /title></head>
                <body>`);
    res.write(` < div > 1 < /div>`);
    await sleep(1000);
    res.write(`<div>2</div > `);
    res.end(` < /body></html > `);
}).listen(3000);
```

通过 `htttp.createServer` 启动一个端口是 3000 的 web 服务，通过 res.write 连续发送多个响应体片段，首先输出的是 body 以上片段，然后发送 `<div>1</div>`，间隔 1s，发送`<div>2</div>`，最后调用 `res.end`，闭合标签，结束响应体传输。在页面上，我们先看到 1，1s 之后，会出现 2。

## 基于 Express 实现的 BigPipe 方案

layout.html

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <meta http-equiv="X-UA-Compatible" content="ie=edge" />
    <title>BigPipe Demo</title>
    <script>
      const BigPipe = {
        view(selector, temp) {
          document.querySelector(selector).innerHTML = temp;
        }
      };
    </script>
  </head>
  <body>
    <div id="a" class="box">loading...</div>
    <div id="b" class="box">loading...</div>
    <div id="c" class="box">loading...</div>
  </body>
</html>
```

index.js

```javascript
const express = require('express');
const fs = require('fs');
const app = express();

const renderModule = (moduleId, res) => {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      const template = `<span>${moduleId.toUpperCase()}</span>`;
      res.write(
        `<script>BigPipe.view('#${moduleId}', '${template}');</script>`
      );
      resolve();
    }, 1000 * (Math.random() * 3 + 1));
  });
};

app.get('/', (req, res, next) => {
  const layoutHtml = fs.readFileSync(__dirname + '/layout.html').toString();
  res.write(layoutHtml);
  const moduleIds = ['a', 'b', 'c'];
  const promises = moduleIds.map(moduleId => renderModule(moduleId, res));
  Promise.all(promises).then(() => {
    res.end();
  });
});

app.listen(3000, () => {
  console.log('server started at : 3000');
});
```

## 前端如何处理返回的分块数据

一般的 ajax 请求的处理，只有两种情况：成功、失败。如何处理持续分块返回的数据呢？

我们先来看一个 `readyState` 属性，`XMLHttpRequest` 的属性 `readState` 表示请求的状态。

该属性一共有 5 个状态，如下：

- 0: 请求未初始化
- 1: 服务器连接已建立
- 2: 请求已接收
- 3: 请求处理中
- 4: 请求已完成，且响应已就绪

我们经常用到是 `xhr.readyState === 4` ，然后结合 `xhr.status` 来判断请求的成功或者失败，执行对应的回调方法。

这里我们需要监听是 `xhr.readyState === 3`，每当有分块数据返回的时候，都会触发 `xhr.onreadystatechage` 的方法，进入 `this.readyState === 3` 的判断执行语句，实现分块数据的成功回调方法。

注意：当分块数据返回的时间较短的时候，会出现多个分块一起返回的情况，所以我们不能用字符串截取的方式，把已结束的 chunk 存放在数组中。

实现原理如下：

```javascript
let xhr = new XMLHttpRequest();
let chunked = [];
xhr.onreadystatechange = function() {
    if (this.readyState === 3) {
        if (options.onData) {
            if (this.response) {
                const chunks = this.response.match(/<chunk>(.*?)<\/chunk>?/g).map(item = >item.replace(/<\/?chunk>/g, ''));
                const data = _.difference(chunks, chunked);
                data.forEach(item = >{
                    try {
                        options.onData(JSON.parse(item));
                    } catch(e) {
                        options.onData(item);
                    }
                });
                chunked = chunks;
            }
        }
    }
}
```

使用方法如下：

```javascript
ajax
  .post(
    '/bigpipe',
    {
      a: 1
    },
    {
      onData: this.onData
    }
  )
  .then(() => {
    console.log(end);
  });
onData = data => {};
```

## 应用场景

下面我们来讨论一下 BigPipe 的应用场景，来提高页面性能和用户体验。

### 场景一：批量处理

背景：在请假单中，当我们勾选 100 个员工，进行批量需要校验的时候，后端处理的业务比较复杂，处理时间大约需要一分钟左右，页面一直处于校验中状态，没有任务进度反馈，用户不知道什么时候能返回结果。

方案：当我们运用了 BigPipe 方案之后，每个员工的校验结果可以通过一个分块及时的返回，然后在页面上实时的渲染出来，100 个员工校验，需要持续的返回 100 个分块数据，用户能看到整个校验的过程与进度，这样的用户体验会好很多。

### 场景二：页面出现大量模块

背景：之前参与的一个公司自助平台的项目，打开页面，首先是获取每个卡片的位置信息，显示整体布局，然后每个卡片，独自发起 api 请求获取数据，渲染卡片，遇到一个问题，当页面卡片很多的时候，在网络中会出现大量的 api 请求，消耗了网络来回传输的时间，同时也远远超出了浏览器的并发限制，Chrome 浏览器并发的限制是 6，超出这个限制，会造成请求的阻塞和滞后。

方案：通过 Node BFF 转发合并接口，实现服务端的 BigPipe 方案，各个卡片独自完成数据获取，分块输出，ajax 发起请求，处理分块返回的数据，实时渲染在页面上，解决了 api 请求数过多带来的一系列问题。

## 总结

分块传输编码，允许服务端为返回的内容维持持久链接，向客户端返回多个分块的数据，BigPipe 方案就是基于这一点实现的。

服务端首屏渲染，通过 BigPipe 的方案，减轻首屏的内容，首屏显示的时间几乎等于 layout 的处理时间，毫秒级的。

通过 `xhr.reayState === 3`，请求处理中的判断条件，来执行分块数据返回时的回调。

我们现在前端有些项目已经在用 Node BFF，所以服务端的 BigPipe 方案，完全可以在 Node 层，让服务端更加专注于服务化，提供数据接口。

应用场景，当我们遇到批量处理，后端处理时间比较长的时候，我们可以引入 BigPipe 方案，将每条记录的结果，一个一个的分块返回的，实时渲染出来；当我们遇到一个页面出现很多模块，大量 api 请求的时候，就可以考虑 BigPipe 方案，分块返回数据。

## 关于本次技术沙龙

主题：BigPipe 的应用

日期：2019-03-23

地点：公司灵岩山会议室

分享嘉宾：Allen.Xiang 基础架构部前端工程师

前后参与过基础排班、累计、新首页、自助平台、考勤推送、聚合查询、邮件后台系统等项目的开发和维护工作，热爱前端技术，对新事物有强烈的好奇心和学习的欲望。

议题简介：

使用 BigPipe 方案，减少首屏显示时间，提高大型复杂页面的性能加速，提升用户体验。在 node 中间层的基础上，实现 BigPipe 方案，通过 Node 层请求多个接口，获取数据，然后分块传输给客户端，可以有效地解决页面出现大量 api 请求，消耗网络传输时间，以及并发限制带来的请求阻塞和滞后等问题。

听众收益：

1. 介绍了 BigPipe 的工作原理
2. 分析了 BigPipe 在首屏渲染中的优势
3. 了解前端如何处理分块返回的数据
4. 结合现有项目剖析 BigPipe 的应用场景

下面来张现场的照片，感受一下现场的氛围。

![live](/assets/images/bigpipe/live.jpg)
