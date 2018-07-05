---
layout: post
title:  "Git使用技巧"
date:   2017-08-20
categories: 工具
tags: git
---

SVN是集中式版本控制软件，所有的动作都是在对服务器操作，万一服务器断电了，所有的操作都得中断。Git是分布式版本控制软件，每位开发者在本地都有自己版本库，只有在需要对外公开的时候，才需要推送到远程库，通过工作区、暂存区、本地版本库、远程版本库能够更颗粒化得多管理项目中的代码。

## 移除敏感数据

在项目qcloud被提交推送至github仓库后，我才发现配置文件里含有敏感隐私的信息，如github账号的id和secret，云服务上数据库的账号和密码，还有163邮箱的账号和密码，这些信息就这样被公开了，非常不安全。

所以我们需要把配置文件从github上删除，一开始在本地删除，再提交并推送至github仓库，但是别人还是可以通过提交的历史记录，查阅之前版本的代码，形同虚设。

想要彻底地删除配置文件，我们需要借助git filter-branch这个命令，它允许对git历史进行复杂的shell脚本重写，具体操作请见下面的代码：

```bash
// 从所有提交中删除文件（包含机密信息或版权侵犯）：
git filter-branch --index-filter 'git rm --cached --ignore-unmatch ./config.js'
// 强制提交入库
git push -f
```

最后记得在.gitignore文件中添加config.js，忽略该文件。

## 参考文档

- [Removing sensitive data from a repository](https://help.github.com/articles/removing-sensitive-data-from-a-repository/)
- [git-filter-branch](https://git-scm.com/docs/git-filter-branch)
- [廖雪峰老师的git教程](https://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000)