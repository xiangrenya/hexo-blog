---
layout: post
title:  "REST 接口定义规范"
date:   2019-4-31
categories: 前端
tags: 学习
comments: true
---

## REST API

REST 全称 Representational State Transfer（表现层状态转换），是 Roy Thomas Fielding 博士于 2000 年在他的博士论文中提出来的一种软件构建风格。

我们先来看两个不好的接口设计

```
/getAllcustomers
/createCustomer
/updateCustomer
/deleteCustomer
```

符合 REST 风格的接口，应该遵循 url 表示资源的定位，http method 请求方式表示对资源的操作，请求响应的状态码，表示请求的结果。

下面我们来举个简单的示例，来演示一下正确的接口定义规范

客户列表
`GET /customers`
状态码: 201

过滤选项，可以通过 query 参数，追加在地址后面，如：`/customers?userName=allen&orderBy=createTime&sortBy=desc&pageNum=1&pageSize=10`

创建客户
`POST /customers`
状态码: 201

单个客户详情
`GET /customers/:customerId`
状态码: 200

更新客户
`PUT /customers/:customerId`
状态码: 200

删除客户
`DELETE /customers/:customerId`
状态码: 204

多级资源：获取某个客户下的所有订单
`/customers/:customerId/orders`

其它注意事项

- 使用 - 来分割单词让 url 可读性更好
- 使用 ? 用来过滤资源
- 避免使用多级 url
```
/customers/:customerId/orders/:orderId // Avoid
/orders/:orderId // Good
```

## 参考资料

- [Yapi官网](https://yapi.ymfe.org/)
- [API design](https://docs.microsoft.com/en-us/azure/architecture/best-practices/api-design)