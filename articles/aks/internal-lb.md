---
title: 在 Azure Kubernetes 服务 (AKS) 中创建内部负载均衡器
description: 了解如何通过 Azure Kubernetes 服务 (AKS) 创建和使用内部负载均衡器以公开服务。
services: container-service
author: rockboyfor
ms.service: container-service
ms.topic: article
origin.date: 03/04/2019
ms.date: 04/08/2019
ms.author: v-yeche
ms.openlocfilehash: a26eab83f567a46f613e3bfda95fd99aba2b79c0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60465540"
---
# <a name="use-an-internal-load-balancer-with-azure-kubernetes-service-aks"></a>使用包含 Azure Kubernetes 服务 (AKS) 的内部负载均衡器

若要限制访问 Azure Kubernetes 服务 (AKS) 中的应用程序，可以创建和使用内部负载均衡器。 内部负载均衡使得仅 Kubernetes 群集所在的同一虚拟网络中运行的应用程序能够访问 Kubernetes 服务。 本文介绍如何通过 Azure Kubernetes 服务 (AKS) 创建和使用内部负载均衡器。

> [!NOTE]
> Azure 负载均衡器以两种 SKU 提供：“基本”和“标准”。 AKS 目前支持“基本”SKU。 如果想要使用“*标准*”SKU，可以使用上游 [aks-引擎][aks-engine]。 有关详细信息，请参阅 [Azure 负载均衡器 SKU 比较][azure-lb-comparison]。

## <a name="before-you-begin"></a>开始之前

本文假定你拥有现有的 AKS 群集。 如果需要 AKS 群集，请参阅 AKS 快速入门[使用 Azure CLI][aks-quickstart-cli] 或[使用 Azure 门户][aks-quickstart-portal]。

还需安装并配置 Azure CLI 2.0.59 或更高版本。 运行  `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅 [安装 Azure CLI][install-azure-cli]。

## <a name="create-an-internal-load-balancer"></a>创建内部负载均衡器

若要创建内部负载均衡器，请使用服务类型 LoadBalancer 和 azure-load-balancer-internal 注释创建名为 `internal-lb.yaml` 的服务清单，如以下示例所示：

```yaml
apiVersion: v1
kind: Service
metadata:
  name: internal-app
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: internal-app
```

使用 [kubectl apply]kubectl-apply] 部署内部负载均衡器，并指定 YAML 清单的名称：

```console
kubectl apply -f internal-lb.yaml
```

Azure 负载均衡器在节点资源组中创建，并连接到 AKS 群集所在的虚拟网络。

查看服务详细信息时，内部负载均衡器的 IP 地址显示在“EXTERNAL-IP”列中。 在此上下文中，*External* 是指负载均衡器的外部接口，不是指收到公共的外部 IP 地址。 可能需要一两分钟，IP 地址才会从 \<pending\> 更改为实际的内部 IP 地址，如以下示例所示：

```
$ kubectl get service internal-app

NAME           TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
internal-app   LoadBalancer   10.0.248.59   10.240.0.7    80:30555/TCP   2m
```

## <a name="specify-an-ip-address"></a>指定 IP 地址

若要对内部负载均衡器使用特定的 IP 地址，请将 loadBalancerIP 属性添加到负载均衡器 YAML 清单。 指定的 IP 地址必须位于 AKS 群集所在的同一子网，并且必须尚未分配给某个资源。

```yaml
apiVersion: v1
kind: Service
metadata:
  name: internal-app
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
spec:
  type: LoadBalancer
  loadBalancerIP: 10.240.0.25
  ports:
  - port: 80
  selector:
    app: internal-app
```

在部署后查看服务详细信息时，“EXTERNAL-IP”列中的 IP 地址反映了指定的 IP 地址：

```
$ kubectl get service internal-app

NAME           TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
internal-app   LoadBalancer   10.0.184.168   10.240.0.25   80:30225/TCP   4m
```

## <a name="use-private-networks"></a>使用专用网络

创建 AKS 群集时，可以指定高级网络设置。 此方法允许将群集部署到现有 Azure 虚拟网络和子网中。 一种方案是将 AKS 群集部署到连接到本地环境的专用网络，并运行仅在内部可访问的服务。 有关详细信息，请参阅“使用 [Kubenet][use-kubenet] 或 [Azure CNI][advanced-networking] 配置你自己的虚拟网络子网”。

在使用专用网络的 AKS 群集中部署内部负载均衡器时，不需要更改先前的步骤。 负载均衡器在与 AKS 群集相同的资源组中创建，但连接到专用虚拟网络和子网，如以下示例所示：

```
$ kubectl get service internal-app

NAME           TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
internal-app   LoadBalancer   10.1.15.188   10.0.0.35     80:31669/TCP   1m
```

> [!NOTE]
> 可能需要向 AKS 群集的服务主体授予针对部署了 Azure 虚拟网络资源的资源组的“网络参与者”角色。 使用 [az aks show][az-aks-show] 查看服务主体，例如 `az aks show --resource-group myResourceGroup --name myAKSCluster --query "servicePrincipalProfile.clientId"`。 若要创建角色分配，请使用 [az role assignment create][az-role-assignment-create] 命令。

## <a name="specify-a-different-subnet"></a>指定其他子网

若要为负载均衡器指定子网，请将 *azure-load-balancer-internal-subnet* 注释添加到服务中。 指定的子网必须与 AKS 群集位于同一虚拟网络中。 部署后，负载均衡器“EXTERNAL-IP”地址是指定子网的一部分。

```yaml
apiVersion: v1
kind: Service
metadata:
  name: internal-app
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
    service.beta.kubernetes.io/azure-load-balancer-internal-subnet: "apps-subnet"
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: internal-app
```

## <a name="delete-the-load-balancer"></a>删除负载均衡器

删除使用该内部负载均衡器的所有服务后，该负载均衡器本身也会一并删除。

也可直接删除含有任何 Kubernetes 资源的服务，例如 `kubectl delete service internal-app`，它还将删除基础 Azure 负载均衡器。

## <a name="next-steps"></a>后续步骤

在 [Kubernetes 服务文档][kubernetes-services]中详细了解 Kubernetes 服务。

<!-- LINKS - External -->
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/
[aks-engine]: https://github.com/Azure/aks-engine

<!-- LINKS - Internal -->
[advanced-networking]: configure-azure-cni.md
[az-aks-show]: https://docs.azure.cn/zh-cn/cli/aks?view=azure-cli-latest#az-aks-show
[az-role-assignment-create]: https://docs.azure.cn/zh-cn/cli/role/assignment?view=azure-cli-latest#az-role-assignment-create
[azure-lb-comparison]: ../load-balancer/load-balancer-overview.md#skus
[use-kubenet]: configure-kubenet.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: https://docs.azure.cn/zh-cn/cli/install-azure-cli?view=azure-cli-latest