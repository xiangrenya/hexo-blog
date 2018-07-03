---
layout: post
title:  "JWT认证"
date:   2017-10-17
categories: 后端
tags: token
comments: true
---

## 一、JWT的定义

JSON Web Token（JWT）是一个非常轻巧的规范。这个规范允许我们使用JWT在用户和服务器之间传递安全可靠的信息。

## 二、基于Token认证的优势

基于Token的身份验证是无状态的，我们不将用户信息存在服务器或缓存中，适合为移动应用提供Restful Api。

相比原始的Cookie+Session方式，Token更适合分布式系统的用户认证，绕开了传统的分布式Session一致性等问题。

安全性高，不依赖Cookie，避免了CSRF（跨站请求伪造）

## 三、JWT的组成部分

JWT有三个部分组成，以字符.拼接在一起

1. Header
2. Payload
3. Signature

eg：xxxxx.yyyyy.zzzzz

### 3.1 Header

Header有两个部分组成，包括token类型和hash算法

``` javascript
{
  type: 'JWT',
  alg: 'HS256'
}
```

然后，对Header进行Base64Url编码，作为JWT的第一个部分。

### 3.2 Payload

Payload由JWT的标准所定义的五个字段，如下：

- iss: 该JWT的签发者
- sub: 该JWT所面向的用户
- aud: 接收该JWT的一方
- exp(expires): 什么时候过期，这里是一个Unix时间戳
- iat(issued at): 在什么时候签发的

另外，还需要添加自定义的user信息，传递安全的信息，不要写例如密码这样的敏感信息。

最后，对Payload进行Base64Url编码，作为JWT的第二个部分。

### 3.3 Signature

生成签名，需要对用Base64编码过的报头、负载和密钥，进行算法加密，加密过程如下：

``` javascript
HMACSHA256(
  base64UrlEncode(header) + "." +
  base64UrlEncode(payload),
  secret)
```

通过签名可以验证JWT的有效性，如果被篡改过，就会返回401未授权的错误提示。

## 四、JWT认证的实现过程

![image](/assets/images/jwt-diagram.png)

JWT可以保存在localStorage中，每次请求在http请求时，在Header中加入JWT信息，格式如下：

``` javascript
Authorization: token
```

## 五、在Node平台下使用JWT

安装：jsonwebtoken

``` bash
npm install --save jsonwebtoken
```

### 5.1 生成JWT

签名方法：jwt.sign

``` javascript
jwt.sign(payload, secret,  [options, callback])
```

common/tool.js中的示例代码：

``` javascript
exports.generateToken = function (user_id) {
  var token = jwt.sign({
      user: user_id
  }, config.jwt_secret, { expiresIn: '2h' });
  return token;
}
```

### 5.2 验证JWT

验证方法：jwt.verify

``` javascript
jwt.verify(token, secret, [options, callback])
```

common/tool.js中的示例代码：

``` javascript
exports.verifyToken = function (token, cb) {
  jwt.verify(token, config.jwt_secret, function (err, decoded) {
      if(decoded){
          return cb(err, decoded.user.user_id);
      }
      cb(err, null);
  });
};
```

### 5.3 认证中间件

在Api路由执行业务之前，先执行认证的中间件，并将传递的信息挂载在req对象中。

middlewares/auth.js中的示例代码：

``` javascript
exports.authToken = function(req, res, next){
  var token = req.headers.authorization;
  tools.verifyToken(token, function(err, userId){
      if(err){
          return next(err);
      }
      req.userId = userId;
      next();
  });
}
```

## 六、参考资料：

- Introduction to JSON Web Tokens： https://jwt.io/introduction/
- 在Web应用间安全地传递信息: https://leon_lizi.gitbooks.io/json-web-token/content/chapter1.html
- node-jsonwebtoken： https://github.com/auth0/node-jsonwebtoken