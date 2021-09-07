---
title: "快速体验"
weight: 2
---

## 创建租户、项目、空间

使用管理员账号登录后，展开左侧【组织管理】菜单，点击快速向导。

1、创建租户

- 租户名称：平台内展示的租户名。
- 租户标识：
  - 长度不得少于2位且不大于32位；
  - 只能包含小写字母、数字，以及中划线 ‘-’ ；
  - 全局唯一标识，不允许重复，不允许修改。
- 租户管理员：添加用户 admin 到租户中并作为租户管理员。

![create-tenant](/imgs/quick-start/quick-experience/create-tenant.png)

详细配置说明见 [租户管理](https://www.kubecube.io/docs/user-guide/administration/tenant/) 。

点击【创建】，如图，即创建了租户："tenant-1"。

2、租户配额

选择租户，以及集群，填写资源配额：

- 集群可分配：表示该集群剩余可分配资源。
- 租户已分配：表示该租户下所有 namespace 已分配的资源总和。

![tenant-quota](/imgs/quick-start/quick-experience/tenant-quota.png)

详细配置说明见 [配额管理](https://www.kubecube.io/docs/user-guide/administration/quota/) 。

点击【创建】，即完成该租户的配额设置。

3、创建项目

- 租户：该项目的所属租户；
- 项目名称：项目的展示名称；
- 项目标识：
  - 长度不得少于2位且不大于32位；
  - 只能包含小写字母、数字，以及中划线 ‘-’ ；
  - 全局唯一标识，不允许重复，不允许修改。
- 项目描述：对该项目的描述性语言。
- 项目管理员：选择平台内的用户，作为该项目的管理员。

![create-project](/imgs/quick-start/quick-experience/create-project.png)

详细配置说明见 [租户管理](https://www.kubecube.io/docs/user-guide/administration/tenant/) -【项目管理】。

点击【创建】，如图，即在租户 "tenant-1" 下创建了项目："project-1"。

4、添加项目成员

选择租户和项目，将用户添加到租户或项目中，并给其分配角色。

详细配置说明见 [租户管理](https://www.kubecube.io/docs/user-guide/administration/tenant/) -【成员管理】。

![add-project-member](/imgs/quick-start/quick-experience/add-project-member.png)

点击【创建】，如图，即为将用户 admin 添加到项目 "project-1" 中，并作为该项目的项目管理员。

5、创建空间

选择集群、租户和项目，填写空间名称以及资源配额，点击【创建】。

详细配置说明见 [配额管理](https://www.kubecube.io/docs/user-guide/administration/quota/) 中的【空间管理】。

![create-namespace](/imgs/quick-start/quick-experience/create-namespace.png)

点击创建，如图，即为在管控集群的 "project-1" 项目下创建了一个空间 "namespace-1"。

## 创建工作负载

### 创建 Deployment

点击右上角【切换到控制台】，进入空间展示的界面。在左上角选择租户和项目，并选择项目下的空间，进入控制台。

展开左侧【工作负载】菜单，点击【Deployments】，进入 Deployment 管理页面。

点击【部署】，进入 deployment 的具体设置，如下图所示。

![create-namespace](/imgs/quick-start/quick-experience/create-deploy-1.png)

![create-namespace](/imgs/quick-start/quick-experience/create-deploy-2.png)

![create-namespace](/imgs/quick-start/quick-experience/create-deploy-3.png)

上图展示的示例为，创建一个副本数为1的 Deployment：“deploy-1”， 容器中的镜像为：hub.c.163.com/kubecube/demo:v0。

创建成功后结果如下：

![deploy-manage](/imgs/user-guide/ns-scoped-res/workload/Deployment/deploy-manage.png)

详细配置说明见 [Deployment管理](https://www.kubecube.io/docs/user-guide/ns-scoped-res/workload/deployment/) 。

### 创建 Service

展开左侧【服务与发现】菜单，点击【Services】，进入 Service 管理页面。

点击【创建服务】，选择已创建的 Deployment，如下图创建 Service。点击【立即创建】。

![create-service](/imgs/quick-start/quick-experience/create-service.png)

详细配置说明见 [Service管理](https://www.kubecube.io/docs/user-guide/ns-scoped-res/service-discovery/service/) 。

### 创建Ingress

展开左侧【服务与发现】菜单，点击【Ingresses】，进入 Ingress 管理页面。

点击【创建负载均衡】，选择已创建的 Service，如下图创建 Ingress。点击【立即创建】。

![create-ingress](/imgs/quick-start/quick-experience/create-ingress.png)

详细配置说明见 [Ingress管理](https://www.kubecube.io/docs/user-guide/ns-scoped-res/service-discovery/ingress/) 。

在本地访问镜像中接口：

`curl -H 'Host:foo.bar.com' {部署ingress节点IP}/healthz`, 则会返回以下结果，说明以上内容部署成功：

![create-ingress](/imgs/quick-start/quick-experience/create-ingress-2.png)