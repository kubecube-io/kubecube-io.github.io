---
title: "密钥管理"
weight: 1
---

本文档介绍了如何在 KubeCube 上进行密钥管理。

密钥管理提供 AccessKey 和 SecretKey 供用户系统调用 OpenAPI 接口时进行认证。


## 管理密钥

用户登录后，鼠标移动到右上角用户名称，在下拉菜单中点击【密钥管理】，进入密钥管理页面。在密钥管理页面可以添加密钥、删除密钥和查看密钥信息。

![keymanage.png](/imgs/user-guide/ns-scoped-res/others/key-manage/keymanage.png)

## 使用密钥

使用 AccessKey 和 SecretKey 可以获取用户 token，将token放到请求头可以请求 KubeCube 的 OpenAPI 接口。 

HTTP Request：

```
GET /api/v1/cube/key/token
```

Query Parameters：

| Parameter   | Description |
| :---------- | :---------- |
| `accessKey` | AccessKey   |
| `secretKey` | SecretKey   |

Response：

| Code  | Info                    |
| :---- | :---------------------- |
| `200` | {"token": "token info"} |