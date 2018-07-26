---
title: 创建 Azure Kubernetes 服务 (AKS) 内部负载均衡器
description: 了解如何通过 Azure Kubernetes 服务 (AKS) 创建和使用内部负载均衡器以公开服务。
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 07/12/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 123fc08995416e0ff9c7e12a526deadc34b3a4a2
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/13/2018
ms.locfileid: "39001389"
---
# <a name="use-an-internal-load-balancer-with-azure-kubernetes-service-aks"></a>使用包含 Azure Kubernetes 服务 (AKS) 的内部负载均衡器

内部负载均衡使得 Kubernetes 群集所在的同一虚拟网络中运行的应用程序能够访问 Kubernetes 服务。 本文介绍如何通过 Azure Kubernetes 服务 (AKS) 创建和使用内部负载均衡器。 Azure 负载均衡器以两种 SKU 提供：“基本”和“标准”。 AKS 使用基本 SKU。

## <a name="create-an-internal-load-balancer"></a>创建内部负载均衡器

若要创建内部负载均衡器，请使用服务类型 *LoadBalancer* 和 *azure-load-balancer-internal* 注释创建服务清单，如以下示例所示：

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

使用 `kubetctl apply` 部署后，将会创建一个 Azure 负载均衡器，并使其在与 AKS 群集相同的虚拟网络上可用。

![AKS 内部负载均衡器图像](media/internal-lb/internal-lb.png)

查看服务详细信息时，“EXTERNAL-IP”列中的 IP 地址是内部负载均衡器的 IP 地址，如以下示例所示：

```
$ kubectl get service azure-vote-front

NAME               TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.248.59   10.240.0.7    80:30555/TCP   10s
```

## <a name="specify-an-ip-address"></a>指定 IP 地址

若要对内部负载均衡器使用特定的 IP 地址，请将 *loadBalancerIP* 属性添加到负载均衡器规范。指定的 IP 地址必须位于 AKS 群集所在的同一子网，并且必须尚未分配给某个资源。

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

查看服务详细信息时，“EXTERNAL-IP”中的 IP 地址反映指定的 IP 地址：

```
$ kubectl get service azure-vote-front

NAME               TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.184.168   10.240.0.25   80:30225/TCP   4m
```

## <a name="use-private-networks"></a>使用专用网络

创建 AKS 群集时，可以指定高级网络设置。 此方法允许将群集部署到现有 Azure 虚拟网络和子网中。 一种方案是将 AKS 群集部署到连接到本地环境的专用网络，并运行仅在内部可访问的服务。 有关详细信息，请参阅 [AKS 中的高级网络配置][advanced-networking]。

在使用专用网络的 AKS 群集中部署内部负载均衡器时，不需要更改先前的步骤。 负载均衡器在与 AKS 群集相同的资源组中创建，但连接到专用虚拟网络和子网，如以下示例所示：

```
$ kubectl get service azure-vote-front

NAME               TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.1.15.188   10.0.0.35     80:31669/TCP   1m
```

> [!NOTE]
> 可能需要向 AKS 群集的服务主体授予针对部署了 Azure 虚拟网络资源的资源组的“网络参与者”角色。 使用 [az aks show][az-aks-show] 查看服务主体，例如 `az aks show --resource-group myResourceGroup --name myAKSCluster --query "servicePrincipalProfile.clientId"`。 若要创建角色分配，请使用 [az role assignment create][az-role-assignment-create] 命令。

## <a name="specify-a-different-subnet"></a>指定其他子网

若要为负载均衡器指定子网，请将 *azure-load-balancer-internal-subnet* 注释添加到服务中。 指定的子网必须与 AKS 群集位于同一虚拟网络中。 部署后，负载均衡器“EXTERNAL-IP”地址是指定子网的一部分。

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
    service.beta.kubernetes.io/azure-load-balancer-internal-subnet: "apps-subnet"
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

## <a name="delete-the-load-balancer"></a>删除负载均衡器

删除使用该内部负载均衡器的所有服务后，该负载均衡器本身也会一并删除。

## <a name="next-steps"></a>后续步骤

在 [Kubernetes 服务文档][kubernetes-services]中详细了解 Kubernetes 服务。

<!-- LINKS - External -->
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/

<!-- LINKS - Internal -->
[advanced-networking]: networking-overview.md
[deploy-advanced-networking]: networking-overview.md#configure-networking---cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create