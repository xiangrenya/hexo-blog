---
layout: post
title:  "静态博客框架Hexo"
date:   2017-4-12
categories: 前端
tags: hexo
description: Hexo一款非常快速强大的静态博客框架，支持Markdown语法写作，拥有丰富的主题和第三方插件，并且可以一键部署到Github上。
---

## 快速起步

通过hexo-cli命令行工具，可以快速的初始化hexo项目，以及在本地启动web服务，用于开发，支持实时更新。

- 在全局安装hexo命令行工具
- 用hexo命令初始化项目
- 进入工作目录，安装依赖包
- 启动hexo服务
- 在浏览器访问：[localhost:4000](localhost:4000)

``` bash
npm install hexo-cli -g
hexo init blog
cd blog
npm install
hexo server
```

## 项目部署

- 在github下新建仓库：[username].github.io
- 在根目录下的_config.yml中配置deploy属性
- 安装hexo-deployer-git插件
- 清除之前的旧文件
- 执行hexo的部署命令

``` javascript
deploy:
  type: git
  repo: git@github.com:renyaxiang/renyaxiang.github.io.git
  branch: master
```

``` bash
npm install hexo-deployer-git --save
hexo clean
hexo deploy --generate
```

## 安装主题

- 用git命令下载主题到themes目录下
- 修改_config.yml中的theme属性
- 通过修改下载主题里的_config.yml定制自己的主题

``` bash
git clone https://github.com/iissnan/hexo-theme-next themes/next
```

## 参考资料

- [Hexo官方文档](https://hexo.io/docs/)
- [NextT使用文档](http://theme-next.iissnan.com/)


