---
layout: post
title:  "基于JWT的Token认证机制"
date:   2017-12-25
categories: 后端
tags: token
comments: true
---

现在大多数公司都是前后端完全分离，一个后端Api服务，对应多个前端（iOS, Andriod, H5, Web)，在这种情况下，是无法使用Cookie+Session进行会话管理的，可以Token进行用户认证鉴权，因为Token是无状态的。

## JWT的定义

JSON Web Token（JWT）是一个开放标准（RFC 7519），它定义了一种紧凑且独立的方式，可以在各方之间作为JSON对象安全地传输信息。此信息可以通过数字签名进行验证和信任。JWT可以使用密钥（使用HMAC算法）或使用RSA或ECDSA的公钥/私钥对进行签名。

## 基于Token认证的优势

基于Token的身份验证是无状态的，我们不将用户信息存在服务器或缓存中，减轻了服务器的压力；相比原始的 Cookie+Session 方式，Token更适合分布式系统的用户认证，绕开了传统的分布式Session一致性等问题；安全性高，不依赖Cookie，避免了CSRF攻击。

## JWT的组成部分

一个JWT实际上就是一个字符串，它由三部分组成，头部、载荷与签名，之间以点拼接在一起，如xxxxx.yyyyy.zzzzz。

1. Header
2. Payload
3. Signature

### 头部 Header

Header有两个部分组成，包括token类型和hash算法

``` javascript
{
  type: 'JWT',
  alg: 'HS256'
}
```

然后，对Header进行Base64Url编码，作为JWT的第一个部分。

### 载荷 Payload

Payload由JWT的标准所定义的五个字段，如下：

- iss: 该JWT的签发者
- sub: 该JWT所面向的用户
- aud: 接收该JWT的一方
- exp(expires): 什么时候过期，这里是一个Unix时间戳
- iat(issued at): 在什么时候签发的

我们可以在这里添加B/S之间要传递的信息（如userId），切记不要写例如密码这样的敏感信息。然后对Payload进行Base64Url编码，作为JWT的第二个部分。

### 签名 Signature

生成签名，需要对用Base64编码过的报头、负载和密钥，进行算法加密，加密过程如下：

``` javascript
HMACSHA256(
  base64UrlEncode(header) + "." +
  base64UrlEncode(payload),
  secret)
```

通过签名可以验证JWT的有效性，如果被篡改过，就会返回401未授权的错误提示。

## JWT认证的实现过程

![image](/assets/images/jwt-diagram.png)

JWT可以保存在localStorage中，每次请求在http请求时，在Header中加入JWT信息，格式如下：

``` javascript
Authorization: token
```

## JWT在Node.js环境下的应用

首先在项目中安装所需的jsonwebtoken依赖包
``` sh
npm i -S jsonwebtoken
```

签名和验证Token语法如下：
``` javascript
// 签名
jwt.sign(payload, secret,  [options, callback])
// 验证
jwt.verify(token, secret, [options, callback])
```

示例代码如下：
``` javascript
// 签名
exports.signToken = userId => jwt.sign({ userId }, config.secret, { expiresIn: '2h' });
// 验证
exports.verifyToken = token => new Promise((resolve, reject) => {
  jwt.verify(token, config.secret, (err, decoded) => {
    if(err) reject(err);
    resolve(decoded.userId);
  });
});
```

通过Token验证中间件，保证每次Api请求，都是带有效Token的合法请求，并将传递的信息（如userId）挂载在req对象中，然后参与到业务的处理当中。

``` javascript
exports.authToken = function(req, res, next){
  const token = req.headers.authorization;
  utils.verifyToken(token, config.secret)
    .then(userId => {
      req.userId = userId;
      next()
    })
    .catch(err => next(err));
}
```

## 客户端处理Token的有效性

用户每次发出Api请求，可以通过Axois全局配置响应拦截器，检测到401未授权时，清空本地存储中的Token，重定向至登录页。

``` javascript
axios.interceptors.response.use(
  response => {
    return response;
  },
  error => {
    if (error.response.status === 401) {
      // 清空本地存储中的token，重定向至登录页
      localStorage.removeItem('token');
      location.href = `/login?redirect=${location.href}`;
    }
    return Promise.reject(error.response.data);
  }
);
```

## 参考资料：

- Introduction to JSON Web Tokens： https://jwt.io/introduction/
- 在Web应用间安全地传递信息: https://leon_lizi.gitbooks.io/json-web-token/content/chapter1.html
- node-jsonwebtoken： https://github.com/auth0/node-jsonwebtoken