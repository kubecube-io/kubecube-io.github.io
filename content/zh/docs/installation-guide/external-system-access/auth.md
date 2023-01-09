---
title: "第三方认证系统"
weight: 3
---

# 外部认证系统接入

KubeCube 中包含一套自有的认证系统，同时也支持多种类型的外部认证系统的接入。本文档介绍了如何在 KubeCube 中接入 GitHub、Ldap 以及通用认证接口三种认证系统的操作步骤。

## GitHub 认证

### 1. 登记应用信息

在 [GitHub](https://github.com/settings/applications/new) 注册一个 Oauth 应用，Homepage URL 和 Authorization callback URL 均填写 `http://{kubecube_host}/#/login`，创建成功后 Github 生成一个 ClientId，再手动创建一个 Client secret。

### 2. 修改配置文件

在管控集群修改 configmap：`kubectl edit cm kubecube-auth-config -n kubecube-system`，修改内容如下：

```yaml
apiVersion: v1
kind: ConfigMap
data:
  github: |
    enabled: true
    clientId: 80b802dc59eeb847ed00
    clientSecret: 83dc8eb788f706de3449d45e61f45ebdca433de2
    host: http://10.219.196.107:30080
```

参数说明如下：

| 参数         | 说明                        | 类型    | 默认值 |
| ------------ | --------------------------- | ------- | ------ |
| enabled      | 是否开启 Github 登录        | boolean | false  |
| clientId     | GitHub 授权的 ClientId      | string  |        |
| clientSecret | GitHub 授权的 Client secret | string  |        |
| host         | KubeCube 服务器地址         | string  |        |

### 3. 访问前端登录页面

访问 KubeCube 前端登录页面，选择使用GitHub账号登录；

![external-auth](/imgs/installation-guide/external-auth.png)

授权应用，点击 Authorize xxxapp，即可登录到 KubeCube。

KubeCube 会使用 Github 返回的信息自动在集群中创建该用户，并标记该用户的 “LOGINTYPE” 为 “github”。

## Ldap 认证

KubeCube 支持接入用户已部署的 LDAP 来进行认证。具体步骤如下。

### 1. 添加启动参数

为 KubeCube Deployment 添加启动参数，参数说明如下：

| 参数                    | 说明                | 类型    | 是否必填 | 默认值 | 示例                       |
| ----------------------- | ------------------- | ------- | -------- | ------ | -------------------------- |
| ldap-is-enable          | 是否开启 Ldap 登录  | boolean | 是       | false  | true                       |
| ldap-server             | Ldap 服务器地址     | string  | 是       |        | 10.219.196.107             |
| ldap-port               | Ldap 服务器端口号   | string  | 否       | 389    | 389                        |
| ldap-base               | Ldap 查询分区       | string  | 是       |        | dc=example,dc=com          |
| ldap-admin-user-account | Ldap 管理员账号     | string  | 是       |        | cn=admin,dc=example,dc=com |
| ldap-admin-password     | Ldap 管理员密码     | string  | 是       |        | admin123456                |
| ldap-object-class       | Ldap 对象类         | string  | 否       | person | person                     |
| ldap-login-name-config  | 用户名所在配置      | string  | 否       | uid    | cn                         |
| ldap-object-category    | Ldap objectcategory | string  | 否       |        | dc=example,dc=com          |

示例如下：

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: kubecube
  namespace: kubecube-system
spec:
  template:
    spec:
      containers:
      - args:
        - -ldap-is-enable=true
        - -ldap-object-class=person
        - -ldap-server=10.219.196.107
        - -ldap-base=dc=example,dc=com
        - -ldap-admin-user-account=cn=admin,dc=example,dc=com
        - -ldap-admin-password=admin123456
        - -ldap-login-name-config=cn
```

### 2. 请求登录

##### 接口路径

```
/api/v1/cube/login
```

##### 接口方法

POST

##### 请求参数

| 参数名称  | 参数说明 | 参数来源 | 参数类型 | 是否必须 | 备注 |
| --------- | -------- | -------- | :------: | -------- | ---- |
| name      | 用户名   | `body`   |  string  | 是       |      |
| password  | 密码     | `body`   |  string  | 是       |      |
| loginType | 登录方式 | `body`   |  string  | 是       | ldap |

##### 响应

如果 Ldap 返回认证成功，KubeCube 在集群中创建该用户，并标记该用户的 “LOGINTYPE” 为 “ldap”。

| 响应码 | 状态 | 描述                                 | 响应体        |
| ------ | ---- | ------------------------------------ | ------------- |
| 200    | OK   | 请求成功，返回在集群中创建的User信息 | [User](#User) |

##### 数据模型

###### User

| 参数名称   | 参数说明            |         参数类型          | 是否必须 | 备注 |
| ---------- | ------------------- | :-----------------------: | -------- | ---- |
| kind       | User                |          string           | 是       |      |
| apiVersion | user.kubecube.io/v1 |          string           | 是       |      |
| metadata   | 元数据              |   [Metadata](#Metadata)   | 是       |      |
| spec       |                     |   [UserSpec](#UserSpec)   | 是       |      |
| status     |                     | [UserStatus](#UserStatus) | 是       |      |

###### Metadata

| 参数名称          | 参数说明                   |     参数类型      | 是否必须 | 备注                             |
| ----------------- | -------------------------- | :---------------: | -------- | -------------------------------- |
| name              | 该用户在集群中的名称       |      string       | 是       |                                  |
| labels            | 标签                       | map[string]string | 否       | Ldap返回的真实用户名保存在标签中 |
| creationTimestamp | 创建此对象时的时间戳       |       Time        | 否       |                                  |
| generation        | 所需状态的特定生成的序列号 |      integer      | 否       |                                  |
| uid               | 资源对象在集群中的唯一标识 |      string       | 否       |                                  |
| selfLink          | 资源关联的url              |      string       | 否       |                                  |

###### UserSpec

| 参数名称  | 参数说明 | 参数类型 | 是否必须 | 备注 |
| --------- | -------- | :------: | -------- | ---- |
| loginType | 登录方式 |  string  | 是       | ldap |

###### UserStatus

| 参数名称      | 参数说明     | 参数类型 | 是否必须 | 备注 |
| ------------- | ------------ | :------: | -------- | ---- |
| lastLoginTime | 上次登录时间 |   Time   | 否       |      |
| lastLoginIP   | 上次登录IP   |  string  | 否       |      |

##### 请求示例

```shell
curl https://0.0.0.0:7443/api/v1/cube/login -X POST -d '{"name": "test123","password":"123456","loginType":"ldap"}' --header "Content-Type: application/json" 
```

##### 返回示例

```json
{
	"kind": "User",
	"apiVersion": "user.kubecube.io/v1",
  "metadata": {
		"labels": {
			"name": "test123"
		},
	"spec": {
		"loginType": "ldap",
	},
	"status": {
		"lastLoginTime": "2022-07-06T06:25:37Z",
		"lastLoginIP": "10.219.196.107"
	}
}
```

## 通用认证

通用认证指的是通过请求第三方接口的方式进行认证。KubeCube 提供了一种通用的接入方式，但对接口的返回有一定要求。具体步骤如下。

### 1. 接口准备

第三方认证接口要求：

1. 请求 url 为配置的固定值，KubeCube 会将请求携带的 header 进行转发，因此第三方接口对 header 进行认证；
2. 接口返回格式为 `map[string]interface{}`，其中包含一组值为 key=“name”，value=“{用户名}”。

### 2. 添加启动参数

为 KubeCube Deployment 添加启动参数，参数说明如下：

| 参数                              | 说明                 | 类型    | 是否必填 | 默认值 | 示例                                     |
| --------------------------------- | -------------------- | ------- | -------- | ------ | ---------------------------------------- |
| generic-auth-is-enable            | 是否开启通用认证方式 | boolean | 是       | false  | true                                     |
| generic-auth-url                  | 第三方认证url        | string  | 是       |        | https://kubecube123.com/api/v1/demo/auth |
| generic-auth-method               | 第三方认证请求方式   | string  | 是       |        | GET                                      |
| generic-auth-scheme               | 请求协议             | string  | 否       | http   | https                                    |
| generic-auth-insecure-skip-verify | 是否跳过安全校验     | string  | 否       |        | true                                     |
| generic-auth-tls-cert             | tls证书              | string  | 否       |        | LS0NGc9PQotLS0tLUVOFURS0tLS0t            |
| generic-auth-tls-key              | tls密钥              | string  | 否       |        | LS0NsfGcQotfdLS0tLUVOFURtLS0ta1          |

示例如下：

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: kubecube
  namespace: kubecube-system
spec:
  template:
    spec:
      containers:
      - args:
        - -generic-auth-is-enable=true
        - -generic-auth-url=https://kubecube123.com/api/v1/demo/auth
        - -generic-auth-method=GET
        - -generic-auth-scheme=https
        - -generic-auth-insecure-skip-verify=true
```

开启通用认证方式后，用户可跳过登录直接访问 KubeCube。KubeCube 会将请求携带的 header 转发给配置的第三方认证平台，由第三方认证平台返回的结果决定请求是否通过认证。