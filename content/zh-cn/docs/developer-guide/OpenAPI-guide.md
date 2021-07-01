---
title: "OpenAPI使用指南"
weight: 1
---

### 1、获取密钥

登录KubeCube，在右上角的下拉菜单中找到【密钥管理】页面，可以在该页面管理您账户的访问密钥对。

![密钥管理页面截图](/imgs/开发指南/OpenAPI使用指南/密钥管理页面截图.png)

> 若进入到该页面中无密钥则点击【添加密钥】按钮创建，最多可以创建5对密钥。

### 2、根据密钥对生成 token

> token有效期默认1小时，可以在启动时修改配置。

生成token的接口如下：

请求url

```
/api/v1/cube/key/token?accessKey=&secretKey=
```

Method：GET

描述：根据密钥对生成token，token有效期默认为1小时。

请求参数

| 参数名    | 说明     | 参数类型 | 是否必填 |
| --------- | -------- | -------- | -------- |
| accessKey | 密钥对Ak | string   | 是       |
| secretKey | 密钥对Sk | string   | 是       |

返回参数

| 参数名 | 说明                                | 参数类型 |
| ------ | ----------------------------------- | -------- |
| token  | 可以用于访问KubeCube OpenAPI的token | string   |

请求示例

```linux
curl https://kubecube.com/api/v1/cube/key/token?accessKey=0ad66675488c4855a07113a8e65719e3&secretKey=8f732a291795418f81cec6f1b064334a -X GET
```

返回示例

```
{"token":"eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJVc2VySW5mbyI6eyJ1c2VybmFtZSI6ImFkbWluIiwiZ3JvdXBzIjpbImt1YmVjdWJlIl19LCJleHAiOjE2MjQwMDMxMTJ9.DuR36vDDhLe_F5gw_T-8FCV7ZZVCJ1ye0dEpfELSa3g"}
```

### 3、使用 token 访问 API

在访问KubeCube openAPI时，在请求中加上请求头`Authorization:Bearer ${token}`以标识访问者的身份，其中${token}是第二步中获取到的token值，具体接口信息见下接口文档。

**请求示例**

```
curl -X GET -H 'Authorization:Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJVc2VySW5mbyI6eyJ1c2VybmFtZSI6ImFkbWluIiwiZ3JvdXBzIjpbImt1YmVjdWJlIl19LCJleHAiOjE2MjQwMDMxMTJ9.DuR36vDDhLe_F5gw_T-8FCV7ZZVCJ1ye0dEpfELSa3g' https://kubecube.com/api/v1/cube/proxy/clusters/pivot-cluster/api/v1/pods -k
```

**得到结果**

```
{"apiVersion":"v1","code":404,"details":{"kind":"pods","name":"kubecube"},"kind":"Status","message":"pods \"kubecube\" not found","metadata":{},"reason":"NotFound","status":"Failure"}
```

### 4、接口文档

部署好 KubeCube 后，访问 http://localhost:8080/swagger/index.html#/ 即为 KubeCube 的接口文档，该接口文档描述了 KubeCube 中的部分接口。

除此文档中的接口，如果需要访问 Kubernetes 资源，接口文档如下：

**请求url**：

```
/api/v1/cube/proxy/clusters/{clusterName}/*url?selector=&pageSize=&pageNum=&sortName=&sortOrder=sortFunc=
```

- `*url`指的是直接调用 Kubernetes 时的接口，

  接口文档为：https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.19/#watch-pod-v1-core。

- selector：查询条件，支持精准匹配和模糊匹配：

  - 精准匹配：eg. selector=key1=value1,key2=value2,key3=value3；
  - 模糊匹配：eg. selector=key1~value1,key2~value2,key3~value3；
  - 混合匹配：eg. selector=key1~value1,key2=value2,key3=value3；

- pageSize：查询结果每页的数量，默认值为10；

- pageNum：查询结果的页数，默认为1；

- sortName：查询结果排序依据的字段，默认为 "metadata.name"；

- sortOrder：查询结果正序或倒序显示：正序为 "asc"，倒序为 "desc"，默认为正序；

- sortFunc：sortName 的数据类型，默认为 "string"。

返回参数同 Kubernetes 的返回参数。

**请求示例**

如果需要查询某个namespace下的 deployment 列表，并希望结果以 deployment 的创建时间倒序排序、以20条结果为1页，返回第2页的结果：

1、查询 Kubernetes 接口文档：查询 deployment 列表的接口为 

```
GET /apis/apps/v1/namespaces/{namespace}/deployments
```

2、因此， KubeCube 查询 deployment 的对应接口为 

```
GET /api/v1/cube/proxy/clusters/{clusterName}/apis/apps/v1/namespaces/{namespace}/deployments？sortName=CreationTimestamp&sortOrder=desc&pageSize=20&pageNum=2
```

3、使用 token 访问则为：

```linux
curl -X GET -H 'Authorization:Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJVc2VySW5mbyI6eyJ1c2VybmFtZSI6ImFkbWluIiwiZ3JvdXBzIjpbImt1YmVjdWJlIl19LCJleHAiOjE2MjQ2MTY3MjZ9.FCfuVzADMAgYeOm39Wlhs-3B6kW-Z6bZ9js1lKoNub0' https://kubecube.com/api/v1/cube/proxy/clusters/pivot-cluster/apis/apps/v1/namespaces/namespaceA/deployments?sortName=metadata.creationTimestamp&sortOrder=desc&pageSize=20&pageNum=2 -k
```


