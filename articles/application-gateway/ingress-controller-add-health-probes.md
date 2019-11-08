---
title: 向 AKS 箱添加运行状况探测
description: 本文提供了有关如何使用应用程序网关将运行状况探测（准备情况和/或活动）添加到 AKS 的信息。
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 5d0543a3a43d53e462a6406312faddf37d2653c6
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/07/2019
ms.locfileid: "73795600"
---
# <a name="add-health-probes-to-your-service"></a>向服务添加运行状况探测
默认情况下，入口控制器将为公开的 pod 设置 HTTP GET 探测。
可以通过向 `deployment`/`pod` 规范添加[就绪或活动探测](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/)来自定义探测属性。

## <a name="with-readinessprobe-or-livenessprobe"></a>与 `readinessProbe` 或 `livenessProbe`
```yaml
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: aspnetapp
spec:
  replicas: 3
  template:
    metadata:
      labels:
        service: site
    spec:
      containers:
      - name: aspnetapp
        image: mcr.microsoft.com/dotnet/core/samples:aspnetapp
        imagePullPolicy: IfNotPresent
        ports:
        - containerPort: 80
        readinessProbe:
          httpGet:
            path: /
            port: 80
          periodSeconds: 3
          timeoutSeconds: 1
```

Kubernetes API 参考：
* [容器探测](https://kubernetes.io/docs/concepts/workloads/pods/pod-lifecycle/#container-probes)
* [HttpGet 操作](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.14/#httpgetaction-v1-core)

> [!NOTE]
> * 当配置了 `httpGet`时，支持 `readinessProbe` 和 `livenessProbe`。
> * 当前不支持对 pod 上公开的端口进行探测。
> * 不支持 `HttpHeaders`、`InitialDelaySeconds`和 `SuccessThreshold`。

##  <a name="without-readinessprobe-or-livenessprobe"></a>无 `readinessProbe` 或 `livenessProbe`
如果未提供上述探测器，则入口控制器就会假定服务可在 `Path` 为 `backend-path-prefix` 批注指定或在服务的 `ingress` 定义中指定的 `path` 上访问。

## <a name="default-values-for-health-probe"></a>运行状况探测的默认值
对于不能由就绪/活动探测器推断的任何属性，将设置默认值。

| 应用程序网关探测属性 | 默认值 |
|-|-|
| `Path` | / |
| `Host` | localhost |
| `Protocol` | HTTP |
| `Timeout` | 30 |
| `Interval` | 30 |
| `UnhealthyThreshold` | 3 |