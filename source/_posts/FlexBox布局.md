---
layout: post
title:  "FlexBox布局"
date:   2017-6-20
categories: 前端
tags: flexbox
description: 弹性盒子模型更易于使用。弹性盒子中的子元素可以在各个方向上进行布局，并且能以弹性尺寸来适应显示空间。由于元素的显示顺序可以与它们在源代码中的顺序无关，定位子元素将变得更容易，并且能够用更简单清晰的代码来完成复杂的布局。
---

## 弹性盒子模型

![image](/assets/images/flexbox.png)

## 弹性盒子术语

- 弹性容器
- 弹性项目
- 轴（主轴 | 侧轴）
- 方向
- 行
- 尺寸

## 弹性盒子的属性

### 弹性容器

``` css
flex-direction：row [row | row-reverse | column | column-reverse]
flex-wrap: nowrap [wrap | wrap]
flex-flow: row nowrap <flex-direction flex-wrap>
justify-content: flex-start  [flex-start | flex-end | center | space-between | space-around]
align-items: stretch [flex-start | flex-end | center | baseline | stretch]
align-content: stretch  [flex-start | flex-end | center | space-between | space-around | stretch]
```

### 弹性项目

``` css
order: 0
flex-grow: 0
flex-shrink: 1
flex-basis: auto
flex: 0 1 auto <flex-grow flex-shrink flex-basis>
align-self: auto [auto | flex-start | flex-end | center | baseline | stretch]
```

## 案列

在JSFiddle里写的一个demo：https://jsfiddle.net/xiangry/vrmq0a6z/

## 参考资料

- Flex 布局教程：语法篇：http://www.ruanyifeng.com/blog/2015/07/flex-grammar.html
- Flex 布局示例：http://static.vgee.cn/static/index.html

## 拓展

- flexbox playground and code generator：http://the-echoplex.net/flexyboxes/
- 基于弹性盒子布局的栅格系统：https://github.com/kristoferjoseph/flexboxgrid
