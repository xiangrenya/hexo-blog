## 初始化项目

通过官方提供的hexo-cli命令行工具，可以很快速地搭建自己的博客网站。

``` sh
# 安装hexo-cli
npm i -g hexo-cli
# 初始化
hexo init xiangrenya.github.io 
# 启动服务
hexo server
```
## 部署到github上

1. 首先在个人的github下新建仓库：xiangrenya.github.io
2. 安装官方的部署git的插件hexo-deployer-git
3. 配置_config.yml中的deploy属性
4. 执行hexo deploy部署指令

``` yml
deploy:
  type: git
  repo: git@github.com:xiangrenya/xiangrenya.github.io.git
  branch: master
```

## 注意事项

启动服务时，出现一些包与最新的LTS v8.11.3不兼容。所以，我们需要通过node版本管理工具nvm，可以让我们在电脑上安装多个node版本，需要时可以切换。

``` sh
# 通过sh脚本安装nvm工具
curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.11/install.sh | bash
# 安装LTS低版本的v6.13.1
nvm install 6.13.1
# 切换到v6.13.1
nvm use 6.13.1
```

## 参考文档

- [hexo](https://github.com/hexojs/hexo)
- [hexo-cli](https://github.com/hexojs/hexo-cli)
- [hexo-deployer-git](https://github.com/hexojs/hexo-deployer-git)
- [nvm](https://github.com/creationix/nvm)