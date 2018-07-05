---
layout: post
title:  "MySQL使用笔记"
date:   2017-10-10
categories: 后端
tags: mysql
comments: true
---

## 用命令行连接MySQL

首先配置一下PATH，可以在全局使用mysql命令

``` sh
# 编辑bash配置
vim .bash_profile
# 插入下面的代码
export PATH=${PATH}:/usr/local/mysql/bin
```

因为我用的是zsh，也要修改一下.zshrc配置
``` bash
vim .zshrc
source ~/.bash_profile
```

通过命令行连接mysql数据库
``` bash
mysql -u username -p dbname
password: Abc123
```

## 让MySQL支持Emoji表情

我们的数据库里面一般都是使用utf8编码，普通的字符串或者表情都是占位3个字节，所以utf8足够用了，但是移动端的表情符号占位是4个字节，普通的utf8就不够用了。为了应对无线互联网的机遇和挑战、避免 emoji 表情符号带来的问题、涉及无线相关的 MySQL 数据库建议都提前采用utf8mb4 字符集，这必须要作为移动互联网行业的一个技术选型的要点。所以推荐在数据库中使用utf8mb4编码。

检查默认安装的MySQL的字符集，登陆MySQL后，输入下面的命令：

``` sh
mysql> SHOW VARIABLES WHERE Variable_name LIKE 'character\_set\_%' OR Variable_name LIKE 'collation%';
```

停止MySQL服务，在系统偏好设置里面打开MySQL，点击 Stop MySQL Server。

``` sh
# 拷贝一份mysql配置文件
sudo cp /usr/local/mysql/support-files/my-default.cnf /etc/my.cnf  
# 编辑
vi /etc/my.cnf
# 插入如下代码
[client]
default-character-set=utf8mb4
[mysql]
default-character-set=utf8mb4
[mysqld]
character-set-client-handshake = FALSE 
character-set-server = utf8mb4 
collation-server = utf8mb4_unicode_ci 
init_connect='SET NAMES utf8mb4'
```

重新启动MySQL服务。系统偏好设置里面，打开MySQL，点击 Start MySQL Server。

现在可以查看修改后的mysql字符集，可以向数据库里插入Emoji表情。
``` sh
mysql> SHOW VARIABLES WHERE Variable_name LIKE 'character\_set\_%' OR Variable_name LIKE 'collation%';
# 打印结果如下：
+--------------------------+--------------------+
| Variable_name            | Value              |
+--------------------------+--------------------+
| character_set_client     | utf8mb4            |
| character_set_connection | utf8mb4            |
| character_set_database   | utf8mb4            |
| character_set_filesystem | binary             |
| character_set_results    | utf8mb4            |
| character_set_server     | utf8mb4            |
| character_set_system     | utf8               |
| collation_connection     | utf8mb4_unicode_ci |
| collation_database       | utf8mb4_unicode_ci |
| collation_server         | utf8mb4_unicode_ci |
+--------------------------+--------------------+
10 rows in set (0.08 sec)
```

参考资料：
- Mac下安装MySQL后, Shell不能识别mysql命令： http://blog.csdn.net/zhaoweizheng66/article/details/62215292
- mysql插入数据出错的解决方法： http://blog.csdn.net/fhzaitian/article/details/53168551
- macOSX下，如何永久更改Mysql的字符编码格式：http://blog.csdn.net/m13026178198/article/details/49993263
- Server Configuration Defaults：https://dev.mysql.com/doc/refman/5.7/en/server-configuration-defaults.html
- chmod命令用来变更文件或目录的权限：http://man.linuxde.net/chmod