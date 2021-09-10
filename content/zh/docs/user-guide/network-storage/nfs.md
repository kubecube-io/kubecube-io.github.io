---
title: "NFS服务"
weight: 1
---

本文档介绍了如何在 KubeCube 上接入 NFS 服务。

## 准备工作

**登录 NFS 服务器**

1. 通过 NFS 导出文件为 KubeCube 分配服务器访问权限

   修改 `/etc/exports` 文件，添加 `{导出目录} {KubeCube节点IP}(rw,sync,no_subtree_check,insecure)`。如：

   ![nfs-6](/imgs/user-guide/network-storage/nfs/nfs-6.png)

2. 重新启动 NFS 服务器

   ```ssh
   systemctl restart nfs-kernel-server
   ```

3. 为 KubeCube 打开防火墙

   ```ssh
   ufw allow from {KubeCube节点IP} to any port nfs
   ```

**登录 KubeCube 节点**

1. 安装 NFS Common

   `apt-get install nfs-common` 或 `yum install nfs-utils`等。

2. 修改`/etc/kubernetes/manifests/kube-apiserver.yaml`文件，添加`- --feature-gates=RemoveSelfLink=false`。

## 创建 StorageClass

登录 KubeCube 节点，创建以下文件并 apply，请根据实际环境修改部分参数。

### 配置 account 及相关权限

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: nfs-client-provisioner
  # replace with namespace where provisioner is deployed
  namespace: default        #根据实际环境设定namespace,下面类同
---
kind: ClusterRole
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: nfs-client-provisioner-runner
rules:
  - apiGroups: [""]
    resources: ["persistentvolumes"]
    verbs: ["get", "list", "watch", "create", "delete"]
  - apiGroups: [""]
    resources: ["persistentvolumeclaims"]
    verbs: ["get", "list", "watch", "update"]
  - apiGroups: ["storage.k8s.io"]
    resources: ["storageclasses"]
    verbs: ["get", "list", "watch"]
  - apiGroups: [""]
    resources: ["events"]
    verbs: ["create", "update", "patch"]
---
kind: ClusterRoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: run-nfs-client-provisioner
subjects:
  - kind: ServiceAccount
    name: nfs-client-provisioner
    # replace with namespace where provisioner is deployed
    namespace: default
roleRef:
  kind: ClusterRole
  name: nfs-client-provisioner-runner
  apiGroup: rbac.authorization.k8s.io
---
kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: leader-locking-nfs-client-provisioner
    # replace with namespace where provisioner is deployed
  namespace: default
rules:
  - apiGroups: [""]
    resources: ["endpoints"]
    verbs: ["get", "list", "watch", "create", "update", "patch"]
---
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: leader-locking-nfs-client-provisioner
subjects:
  - kind: ServiceAccount
    name: nfs-client-provisioner
    # replace with namespace where provisioner is deployed
    namespace: default
roleRef:
  kind: Role
  name: leader-locking-nfs-client-provisioner
  apiGroup: rbac.authorization.k8s.io
```

### 创建 NFS Provisioner

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nfs-client-provisioner
  labels:
    app: nfs-client-provisioner
  # replace with namespace where provisioner is deployed
  namespace: default  #与RBAC文件中的namespace保持一致
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nfs-client-provisioner
  strategy:
    type: Recreate
  template:
    metadata:
      labels:
        app: nfs-client-provisioner
    spec:
      serviceAccountName: nfs-client-provisioner
      containers:
        - name: nfs-client-provisioner
          image: quay.io/external_storage/nfs-client-provisioner:latest
          volumeMounts:
            - name: nfs-client-root
              mountPath: /persistentvolumes
          env:
            - name: PROVISIONER_NAME
              value: qgg-nfs-storage  #provisioner名称,请确保该名称与 nfs-StorageClass.yaml文件中的provisioner名称保持一致
            - name: NFS_SERVER
              value: 10.173.32.164   #NFS Server IP地址
            - name: NFS_PATH  
              value: /mnt/linuxidc    #NFS挂载卷
      volumes:
        - name: nfs-client-root
          nfs:
            server: 10.173.32.164  #NFS Server IP地址
            path: /mnt/linuxidc     #NFS 挂载卷
```

### 创建 StorageClass

方式一：命令行操作

```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: managed-nfs-storage
provisioner: qgg-nfs-storage #这里的名称要和provisioner配置文件中的环境变量PROVISIONER_NAME保持一致
parameters:  	      
  archiveOnDelete: "false"
```

方式二：页面操作

1. 以平台管理员身份登录 KubeCube；
2. 展开左侧菜单栏里的【资源管理】，点击【集群管理】进入集群管理页面，点击需要创建  StorageClass 的集群名称，进入集群详情页面，点击【存储类别】进入存储类别管理页面，点击【创建存储类别】，将方式一中的文件内容写入，点击【确定】，即创建出该 StorageClass。

![nfs-1](/imgs/user-guide/network-storage/nfs/nfs-1.png)

## 创建 PVC

在控制台页面，选择租户和项目，选择集群和空间，点击左侧菜单栏【存储】进入存储管理页面。点击【创建存储声明】，存储类别可以选择已创建过的 StorageClass。

![nfs-2](/imgs/user-guide/network-storage/nfs/nfs-2.png)

创建后可以看到 PVC 状态为 Bound。具体配置说明见 [PVC管理](https://www.kubecube.io/docs/user-guide/ns-scoped-res/storage/pvc/)。

![nfs-3](/imgs/user-guide/network-storage/nfs/nfs-3.png)

## 创建工作负载

在创建工作负载时，点击【展开更多配置】-【挂载数据卷】-【PVC】-【参数】，可以选择上述创建的 PVC。

![nfs-4](/imgs/user-guide/network-storage/nfs/nfs-4.png)

具体配置说明见 [工作负载管理](https://www.kubecube.io/docs/user-guide/ns-scoped-res/workload/)。

## 检查结果

创建挂载 PVC 的工作负载后，登录 NFS 服务器，进入导出目录，可以看到已经创建出一个新的文件夹，文件夹命名为 ${namespace}-${pvcName}-${pvName}。

![nfs-5](/imgs/user-guide/network-storage/nfs/nfs-5.png)