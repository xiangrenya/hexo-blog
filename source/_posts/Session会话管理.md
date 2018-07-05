---
layout: post
title:  "Session会话管理"
date:   2017-07-28
categories: 后端
tags: session
---

## 基于session的会话管理过程

1. 游客访问网站时，服务端会自动创建session，代表当前用户的一次会话过程，可以用来存放后续登陆成功的凭证。如果用户登录后再退出，还会重新创建session。

2. 创建session成功之后，会把session_id通过cookie返回给用户所在的浏览器，这时候浏览器的cookies里就多了个connect.sid，如下表。从此之后，每次请求都会带上这个cookie，以便服务端通过connect.sid找到对应的session对象。

3. 只有在用户登录认证成功之后，并且往sesssion对象里面放入了用户登录成功的凭证，才能用来管理会话。

4. session通常有过期时间，比如2个小时。在过期时间内，有新的请求给服务器，通常服务器都会把对应的session的过期时间再延长2个小时。

5. 当用户主动退出的时候，会把它的session对象里的登录凭证清掉。所以在用户登录前或退出后或者session对象失效时，肯定都是拿不到需要的登录凭证的。

Name | Value | Domain | Path | Expires / Max-Age | Size | Http | Secure | SameSite
---|---|---|---|---|---|---|---|---
connect.sid | s%3Ay6rNy4mso1W3RTBZ9cGF563sfnqgCTVb.FgBmfHYK0xHv6aiR7tRpL%2B9ICMpDmmj8L5Pxnnb6iuk | localhost | / | 2017-10-09T04:15:33.087Z | 93 | ✓ |  | 

## 在MySQL中存储sessions

在数据库中会多出一个表sessions，其中有三个字段session_id[主键]、expires、data

名 | 类型 | 长度
---|---|---
session-id | varchar | 128
expires | int | 11
data | text | 

初次访问应用的session对象，如下表：

session_id | expires | data
---|---|---
y6rNy4mso1W3RTBZ9cGF563sfnqgCTVb | 1507522533 | {"cookie":{"originalMaxAge":7200000,"expires":"2017-10-09T04:15:33.401Z","secure":false,"httpOnly":true,"path":"/"}}

登录认证成功之后的session对象，带有登录凭证，如下表：

session_id | expires | data
---|---|---
y6rNy4mso1W3RTBZ9cGF563sfnqgCTVb | 1507522533 | {"cookie":{"originalMaxAge":7200000,"expires":"2017-10-09T04:15:33.401Z","secure":false,"httpOnly":true,"path":"/"},"user":{"user_id":2,"user_name":"xiangry"}}

缺点：

1. 用户同时在线量比较多时，这些会话信息会占据比较多的内存。
2. 当应用采用集群部署的时候，因为session是由单个服务器创建的，多台服务器之间无法实现session的共享。

## 在Redis中存储sessions

优点：
- 减轻web服务器的负担
- 解决不同web服务器共享session的问题

通过Redis Desktop Manager客户端查看Redis数据库里的session数据，如下图：

![image](/assets/images/session-redis.jpg)



