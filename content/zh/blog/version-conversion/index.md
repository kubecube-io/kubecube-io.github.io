---
author: 蔡鑫涛
date: 2022-09-13
summary: KubeCube 版本转换：大幅提升K8s版本适配能力
tags:
- KubeCube
title: KubeCube 版本转换：大幅提升K8s版本适配能力
---

KubeCube 迎来了新版本的发布，新增了 K8s 版本转化、HNC GA 版本适配、审计信息国际化、warden 主动上报模式，为集群和项目设置 Ingress 域名后缀等特性，也修复了若干已知问题，详见 [ChangeLog](https://github.com/kubecube-io/KubeCube/blob/release-v1.4/docs/changelog.md)。

该版本中最主要的特性是 Version-Conversion 能力的支持，使得接入 KubeCube 的用户无需感知被 KubeCube 接管的 K8s 集群版本，可以使用指定版本的 K8s API 来操作 K8s 资源，KubeCube 会做自适应转化；同时 KubeCube 也将这个能力包装成 SDK 供外部使用。

## 为什么需要多 K8s 版本转化？

在实际的生产场景中，用户的 K8s 版本往往固置于某一稳定版本，随着时间的推移，用户往往在该 k8s 集群中沉淀了大量的业务、工具、方案等，同时 K8s 社区又会不断的推出更高的版本，此时的 k8s 版本升级往往需要比较高的代价。

然而K8s 的版本升级，并不总是保证 API 的完美兼容，绝大多数的 API 会经历从 Development level --> Alpha level --> Beta level --> Stable level 的发展阶段，理想情况下，用户应该使用 Stable level 的 api 用于生产环境，但是现实中，用户所使用的某一资源的 API 很可能处于 Stable level 以下的阶段，比如 extensions/v1beta1 的 Deployment 和 apps/v1 的 Deployment。详见 [K8S API 变动规划](https://kubernetes.io/docs/reference/using-api/deprecation-guide/#migrate-to-non-deprecated-apis)。

当用户需要在控制面纳管多 K8s 集群时，用户暂时不希望升级老的稳定的 K8s 集群，又希望新增的 K8s 集群是比较高的版本，这时，管控面的 KubeCube，就能够提供访问多版本 K8s 的能力，对外暴露统一的 K8s 风格的 RESTfule API，用户既可以使用精确的 GVR 去访问不同版本的 K8s 资源，也可用使用统一版本的 GVR 去访问不同版本的 K8s 资源，KubeCube 会做自适应转化。

![multi-k8s-version](imgs/multi-k8s-version.png)

## K8s native convert

### 1. K8s api workflow

![k8s-api-flow](imgs/k8s-api-flow.png)

### 2. K8s version convert

![k8s-version-convert](imgs/k8s-version-convert.png)

**k8s 版本转换原则**

1. 同一个 group 的不同 version 都可以转换成该 group 的 internalVersion
2. 某一 group 的 internalVersion 可以转换成该 group 下的任一 version

**K8s 版本转化的核心——scheme**

Scheme 中拥有 `concerter` 转化器，其内部存放了各个 API 注册的版本转化函数。

```go
type Scheme struct {
	...

	// converter stores all registered conversion functions. It also has
	// default converting behavior.
	converter *conversion.Converter

	...
}

// Converter knows how to convert one type to another.
type Converter struct {
	// Map from the conversion pair to a function which can
	// do the conversion.
	conversionFuncs          ConversionFuncs
	generatedConversionFuncs ConversionFuncs

	// Set of conversions that should be treated as a no-op
	ignoredUntypedConversions map[typePair]struct{}
}
```

我们已经知道 internalVersion 和指定 version 之间的转换规则，它们的转换函数位于 k8s apis 的定义文件夹下，如：pkg/apis/apps/v1/zz_generated.conversion.go

```go
// Code generated by conversion-gen. DO NOT EDIT.

package v1

import (...)

func init() {
	localSchemeBuilder.Register(RegisterConversions)
}

// RegisterConversions adds conversion functions to the given scheme.
// Public to allow building arbitrary schemes.
func RegisterConversions(s *runtime.Scheme) error {...}
```

这些转化函数，一般由 install 包下的 `Install(scheme)`函数注册到 `Scheme` 中。

```go
// Package install installs the apps API group, making it available as
// an option to all of the API encoding/decoding machinery.
package install

import (...)

func init() {
	Install(legacyscheme.Scheme)
}

// Install registers the API group and adds types to a scheme
func Install(scheme *runtime.Scheme) {
	utilruntime.Must(apps.AddToScheme(scheme))
	utilruntime.Must(v1beta1.AddToScheme(scheme))
	utilruntime.Must(v1beta2.AddToScheme(scheme))
	utilruntime.Must(v1.AddToScheme(scheme))
	utilruntime.Must(scheme.SetVersionPriority(v1.SchemeGroupVersion, v1beta2.SchemeGroupVersion, v1beta1.SchemeGroupVersion))
}
```

注册完的转会函数，将会在 Convert() 方法中使用。

```go
// Convert will translate src to dest if it knows how. Both must be pointers.
// If no conversion func is registered and the default copying mechanism
// doesn't work on this type pair, an error will be returned.
// 'meta' is given to allow you to pass information to conversion functions,
// it is not used by Convert() other than storing it in the scope.
// Not safe for objects with cyclic references!
func (c *Converter) Convert(src, dest interface{}, meta *Meta) error {
  // 转换函数 map 的 key
	pair := typePair{reflect.TypeOf(src), reflect.TypeOf(dest)}
  
  // 实际的 convert 句柄
	scope := &scope{
		converter: c,
		meta:      meta,
	}

	// ignore conversions of this type
	if _, ok := c.ignoredUntypedConversions[pair]; ok {
		return nil
	}
  // 使用预先注册的转换函数进行转换
	if fn, ok := c.conversionFuncs.untyped[pair]; ok {
		return fn(src, dest, scope)
	}
	if fn, ok := c.generatedConversionFuncs.untyped[pair]; ok {
		return fn(src, dest, scope)
	}

	dv, err := EnforcePtr(dest)
	if err != nil {
		return err
	}
	sv, err := EnforcePtr(src)
	if err != nil {
		return err
	}
	return fmt.Errorf("converting (%s) to (%s): unknown conversion", sv.Type(), dv.Type())
}
```

## KubeCube version conversion

了解了 k8s 版本转换的大致思路后，KubeCube 如果需要做版本转换的能力，需要做到以下几点：

1. 维护版本转换专用的 Scheme
2. 注册所有的 k8s 的 api 转换函数，并提供拓展方法
3. 使用 discovery client 提早做 src api 和 dest api 的转换检查

### 1. Conversion func register

![conversion-func-register](imgs/conversion-func-register.png)

KubeCube 会默认注册所有 k8s 原生资源的转换函数，同时也提供注册自定义资源转换函数的入口。

### 2. Greeting target cluster

![version-greeting](imgs/version-greeting.png)

### 3. Controller-runtime client support

KubeCube 的版本转化 SDK 提供了 Wrap controller-runtime 的 client.Client 的能力，可以将 client.Client 升级为具有版本转化能力的句柄。

## 写在最后

未来我们会持续提供更多功能，帮助企业简化容器化落地。也欢迎大家参与贡献，提出宝贵的建议。添加以下微信进入 KubeCube 交流群。

<img src="/imgs/kubecube-wechat.png" alt="kubecube微信" style="zoom:40%;" />

**作者简介：** 蔡鑫涛，网易数帆轻舟容器平台资深开发，KubeCube Committer

