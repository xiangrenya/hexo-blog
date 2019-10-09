---
layout: post
title:  "java 学习笔记"
date:   2019-08-30
categories: 前端
comments: true
---

环境安装

[Mac同时安装Java1.7和Java1.8](https://yq.aliyun.com/articles/206077)
[Maven 环境配置](https://www.runoob.com/maven/maven-setup.html)
[MacOS下Tomcat安装与配置简单方法](https://www.jianshu.com/p/b7cbb1d5f9aa)
[IntelliJ IDEA配置Tomcat（完整版教程）](https://blog.csdn.net/u012149181/article/details/80698208)
[IntelliJ IDEA 创建Spring+SpringMVC+mybatis+maven项目](https://juejin.im/post/5cefa4455188253f9e24df74#heading-8)


[Java protected 关键字详解](https://www.runoob.com/w3cnote/java-protected-keyword-detailed-explanation.html)

[原码, 反码, 补码 详解](https://www.cnblogs.com/zhangziqiu/archive/2011/03/30/ComputerCode.html)

要明白这些，首先要搞清楚float和double在内存结构
```java
    0.99999999f==1f //true 
    0.9f==1f //false
```

[Java 浮点数 float和double类型的表示范围和精度](?)

[浮点数的二进制表示](http://www.ruanyifeng.com/blog/2010/06/ieee_floating-point_representation.html)
[java int与integer的区别](https://www.cnblogs.com/shenliang123/archive/2011/10/27/2226903.html)

[onemail](https://github.com/YunaiV/onemall)

[Java 2019 学习路线](https://www.bilibili.com/read/cv3174527)
[Java 视频教程全集（376P） | 80 小时从入门到精通](https://www.bilibili.com/video/av59529105/?pikaqiu)
[Java 8/9/11 新特性视频教程全集（65P）| 15 小时从入门到精通](https://www.bilibili.com/video/av59549886/?pikaqiu)

[廖雪峰的官方网站Java教程](https://www.liaoxuefeng.com/wiki/1252599548343744)
这是专门针对小白的零基础

java基本语法
命名规范
基本数据类型（字节数、取值范围）
整数运算注意溢出问题

protected关键字可以把字段和方法的访问权限控制在继承树内部，一个protected字段和方法可以被其子类，以及子类的子类所访问
instanceof实际上判断一个变量所指向的实例是否是指定类型，或者这个类型的子类。如果一个引用变量为null，那么对任何instanceof的判断都为false
继承是is关系，组合是has关系
Java的实例方法调用是基于运行时的实际类型的动态调用，而非变量的声明类型。
多态是指，针对某个类型的方法调用，其真正执行的方法取决于运行时期实际类型的方法。多态的特性就是，运行期才能动态决定调用的子类方法。


## 项目实战
[微人事是一个前后端分离的人力资源管理系统，项目采用SpringBoot+Vue开发](https://github.com/lenve/vhr)
[mall项目是一套电商系统，包括前台商城系统及后台管理系统，基于SpringBoot+MyBatis实现](https://github.com/macrozheng/mall)
[mall学习教程](https://macrozheng.github.io/mall-learning/#/README)

## 教程
[IntelliJ IDEA 简体中文专题教程](https://github.com/judasn/IntelliJ-IDEA-Tutorial)
[Mybatis入门看这一篇就够了](https://juejin.im/post/5aa5c6fb5188255587232e5a)
[互联网 Java 工程师进阶知识完全扫盲](https://github.com/doocs/advanced-java)
[用动画的形式呈现解LeetCode题目的思路](https://github.com/MisterBooo/LeetCodeAnimation)
[Roadmap to becoming a web developer in 2019](https://github.com/kamranahmedse/developer-roadmap)
[2019年最新总结，阿里，腾讯，百度，美团，头条等技术面试题目，以及答案，专家出题人分析汇总](https://github.com/0voice/interview_internal_reference)
[线程最最基础的知识](https://juejin.im/post/5d82cc20e51d4561c6784138)

[Xmind 8 Pro for Mac v3.7.8 思维导图 安装激活详解](https://www.rjsos.com/archives/xmind8mac.html)

## 源码分析
