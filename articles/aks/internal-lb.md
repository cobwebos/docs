---
title: 创建 Azure Kubernetes 服务 (AKS) 内部负载均衡器
description: 使用包含 Azure Kubernetes 服务 (AKS) 的内部负载均衡器。
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 3/29/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 6a657df82e1670f7a9d604dd5166ab53bb38bf74
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
---
# <a name="use-an-internal-load-balancer-with-azure-kubernetes-service-aks"></a>使用包含 Azure Kubernetes 服务 (AKS) 的内部负载均衡器

内部负载均衡使得 Kubernetes 群集所在的同一虚拟网络中运行的应用程序能够访问 Kubernetes 服务。 本文档详述如何创建包含 Azure Kubernetes 服务 (AKS) 的内部负载均衡器。

## <a name="create-internal-load-balancer"></a>创建内部负载均衡器

若要创建内部负载均衡器，请生成服务类型为 `LoadBalancer` 且附带注释 `azure-load-balancer-internal` 的服务清单，如以下示例所示。

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

部署后，将会创建一个可在 AKS 群集所在的同一虚拟网络中使用的 Azure 负载均衡器。

![AKS 内部负载均衡器图像](media/internal-lb/internal-lb.png)

检索服务详细信息时，`EXTERNAL-IP` 列中的 IP 地址是内部负载均衡器的 IP 地址。

```console
$ kubectl get service azure-vote-front

NAME               TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.248.59   10.240.0.7    80:30555/TCP   10s
```

## <a name="specify-an-ip-address"></a>指定 IP 地址

若要对内部负载均衡器使用特定的 IP 地址，请将 `loadBalancerIP` 属性添加到负载均衡器规范。指定的 IP 地址必须位于 AKS 群集所在的同一子网，并且必须尚未分配给某个资源。

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
spec:
  type: LoadBalancer
  loadBalancerIP: 10.240.0.25
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

检索服务详细信息时，`EXTERNAL-IP` 中的 IP 地址应反映指定的 IP 地址。

```console
$ kubectl get service azure-vote-front

NAME               TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.184.168   10.240.0.25   80:30225/TCP   4m
```

## <a name="delete-the-load-balancer"></a>删除负载均衡器

删除使用该内部负载均衡器的所有服务后，该负载均衡器本身也会一并删除。

## <a name="next-steps"></a>后续步骤

在 [Kubernetes 服务文档][kubernetes-services]中详细了解 Kubernetes 服务。

<!-- LINKS - External -->
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/