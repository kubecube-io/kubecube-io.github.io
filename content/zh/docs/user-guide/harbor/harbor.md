---
title: "Harbor 集成"
weight: 1
---

本文详述了如何在 KubeCube 接管的集群中，集成 Harbor

## 安装 helm3

参考 [helm安装](https://helm.sh/zh/docs/intro/install/)

## 确认 StorageClass

你可以选择 KubeCube 内置的 local-path 作为默认的 StorageClass，也可以使用自定义的 StorageClass

```bash
-> kubectl get storageclasses
NAME                   PROVISIONER             RECLAIMPOLICY   VOLUMEBINDINGMODE      ALLOWVOLUMEEXPANSION   AGE
local-path (default)   rancher.io/local-path   Delete          WaitForFirstConsumer   false                  28d
```

## 创建 Harbor 的 Namespace

创建指定的 namespace 来部署 Harbor 的相关组件

```bash
kubectl create namespace harbor-system
```

## 创建自定义证书（可选）

安装 Harbor 推荐使用 HTTPS 协议，需要 TLS 证书，如果不提供证书，Harbor 将会自己生成一个，不过它的有效期仅为一年

- 生成证书  

⚠️ Common Name 必须要设置为和你要给 Harbor 的域名保持一致
```bash
# 获得证书
openssl req -newkey rsa:4096 -nodes -sha256 -keyout ca.key -x509 -days 3650 -out ca.crt

# 生成证书签名请求
openssl req -newkey rsa:4096 -nodes -sha256 -keyout tls.key -out tls.csr

# 生成证书
openssl x509 -req -days 3650 -in tls.csr -CA ca.crt -CAkey ca.key -CAcreateserial -out tls.crt
```

- 使用证书生成 secret 
```bash
kubectl create secret generic harbor-tls --from-file=tls.crt --from-file=tls.key --from-file=ca.crt -n harbor-system
```

## 设置 Harbor 的自定义安装参数

通过编辑 values.yaml 来复写 Harbor Chart 的安装参数，完整的 values.yaml 文件可以参考 [goharbor](https://github.com/goharbor/harbor-helm/blob/master/values.yaml)

> KubeCube 内置了 nginx-ingress controller

```yaml
#Ingress 网关入口配置
expose:
  type: ingress
  tls:
    ### 是否启用 https 协议
    enabled: true
  ingress:
    hosts:
      ### 配置 Harbor 的访问域名，需要注意的是配置 notary 域名要和 core 处第一个单词外，其余保持一致
      core: harbor.kubecube.io
      notary: notary.kubecube.io
    annotations:
      ingress.kubernetes.io/ssl-redirect: "true"
      ingress.kubernetes.io/proxy-body-size: "0"
      #### 如果是 traefik ingress，则按下面配置：
#      kubernetes.io/ingress.class: "traefik"
#      traefik.ingress.kubernetes.io/router.tls: 'true'
#      traefik.ingress.kubernetes.io/router.entrypoints: websecure
      #### 如果是 nginx ingress，则按下面配置：
      nginx.ingress.kubernetes.io/ssl-redirect: "true"
      nginx.ingress.kubernetes.io/proxy-body-size: "0"
      nginx.org/client-max-body-size: "0"

## 如果Harbor部署在代理后，将其设置为代理的URL，这个值一般要和上面的 Ingress 配置的地址保存一致
externalURL: https://harbor.kubecube.io

### Harbor 各个组件的持久化配置，并设置各个组件 existingClaim 参数为上面创建的对应 PVC 名称
persistence:
  enabled: true
  ### 存储保留策略，当PVC、PV删除后，是否保留存储数据
  resourcePolicy: "keep"
  persistentVolumeClaim:
    registry:
      storageClass: "local-path"
    chartmuseum:
      storageClass: "local-path"
    jobservice:
      storageClass: "local-path"
    database:
      storageClass: "local-path"
    redis:
      storageClass: "local-path"
    trivy:
      storageClass: "local-path"

### 默认用户名 admin 的密码配置，注意：密码中一定要包含大小写字母与数字
harborAdminPassword: "admin@123"

### 设置日志级别
logLevel: info
```

## 安装 Harbor

- 添加 Helm 仓库
```bash
helm repo add harbor https://helm.goharbor.io
```

- 部署 Harbor
```bash
helm install harbor harbor/harbor -f values.yaml -n harbor-system
```

- 域名配置

如果没有 DNS 服务，需要自行在 hosts 文件中配置域名和 node ip 的映射

```bash
-> cat /etc/hosts
...
x.x.x.x harbor.kubecube.io
```

## 访问 harbor

在浏览器中输入 harbor.kubecube.io 来访问 Harbor 仓库

```
用户：admin
密码：admin@123
```

![harbor-view](/imgs/user-guide/harbor/harbor/harbor-view.png)

## 下载 Harbor 证书并在 Docker 中配置

在 Harbor 管理页面中证书

![ca-down](/imgs/user-guide/harbor/harbor/ca-down.png)

进入服务器，创建以 Harbor 域名为名的文件夹

```bash
mkdir -p /etc/docker/certs.d/harbor.kubecube.io
```

将下载下来 ca.crt 放到该文件夹下面，然后登陆 Harbor 仓库

```bash
docker login -u admin -p admin@123 harbor.kubecube.io
```

## 测试

```bash
docker pull ubuntu:16.04
docker tag ubuntu:16.04 harbor.kubecube.io/library/ubuntu:16.04
docker push harbor.kubecube.io/library/ubuntu:16.04
```






