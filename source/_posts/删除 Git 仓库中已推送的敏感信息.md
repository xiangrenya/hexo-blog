---
layout: post
title:  "删除 Git 仓库中已推送的敏感信息"
date:   2017-08-20
categories: 工具
tags: git
comments: true
---

在个人项目提交推送至 github 仓库后，才发现配置文件里含有敏感隐私的信息，如 github 账号的 id 和 secret，云服务上数据库的账号和密码，还有网易邮箱的账号和密码，这些信息就这样被公开了，非常不安全。

所以我们需要把配置文件从 github 上删除，一开始在本地删除，再提交并推送至 github 仓库，但是别人还是可以通过提交的历史记录，查阅之前版本的代码，形同虚设。

想要彻底地删除配置文件，我们需要借助 `git filter-branch`这个命令，它允许对git历史进行复杂的shell脚本重写，具体操作请见下面的代码：

```bash
// 从所有提交中删除文件（包含机密信息或版权侵犯）：
git filter-branch --index-filter 'git rm --cached --ignore-unmatch ./config.js'
// 强制提交入库
git push -f
```

最后记得在 .gitignore 文件中添加 config.js，忽略该文件。

## 参考文档

- [Removing sensitive data from a repository](https://help.github.com/articles/removing-sensitive-data-from-a-repository/)
- [git-filter-branch](https://git-scm.com/docs/git-filter-branch)
- [廖雪峰老师的git教程](https://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000)