---
title: "Azure 上的 Kubernertes 教程 - 缩放应用程序 | Microsoft Docs"
description: "AKS 教程 - 缩放应用程序"
services: container-service
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: aks, azure-container-service
keywords: "Docker, 容器, 微服务, Kubernetes, Azure"
ms.assetid: 
ms.service: container-service
ms.devlang: aurecli
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/24/2017
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: c60bdf18f80f6922631e02855b83adeb876daa4c
ms.sourcegitcommit: c5eeb0c950a0ba35d0b0953f5d88d3be57960180
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/24/2017
---
# <a name="scale-application-in-azure-container-service-aks"></a>在 Azure 容器服务 (AKS) 中缩放应用程序

如果已按照教程执行，则在 AKS 中已有可正常工作的 Kubernetes 群集，并且已部署了 Azure 投票应用。

在本教程第 5 部分（共 8 部分）中，会在应用中扩大 Pod 并尝试 Pod 自动缩放。 还会了解如何缩放 Azure VM 节点数以更改群集用于托管工作负荷的容量。 已完成的任务包括：

> [!div class="checklist"]
> * 缩放 Kubernetes Azure 节点
> * 手动缩放 Kubernetes Pod
> * 配置运行应用前端的自动缩放 Pod

在后续教程中，将更新 Azure 投票应用程序，并将 Operations Management Suite 配置为监视 Kubernetes 群集。

## <a name="before-you-begin"></a>开始之前

在前面的教程中，我们已将应用程度打包到容器映像中，将此映像上传到 Azure 容器注册表，并创建了 Kubernetes 群集。 应用程序随后在 Kubernetes 群集上运行。

如果尚未完成这些步骤，并且想要逐一完成，请返回到[教程 1 – 创建容器映像](./tutorial-kubernetes-prepare-app.md)。

## <a name="scale-aks-nodes"></a>缩放 AKS 节点

如果在前面的教程中使用命令创建了 Kubernetes 群集，则它具有一个节点。 如果计划在群集上有更多或更少的容器工作负荷，则可以手动调整节点数。

下面的示例将名为 myK8sCluster 的 Kubernetes 群集中的节点数增加到 3 个。 该命令需要几分钟时间完成。

```azurecli
az aks scale --resource-group=myResourceGroup --name=myK8SCluster --agent-count 3
```

输出类似于：

```
"agentPoolProfiles": [
  {
    "count": 3,
    "dnsPrefix": null,
    "fqdn": null,
    "name": "myK8sCluster",
    "osDiskSizeGb": null,
    "osType": "Linux",
    "ports": null,
    "storageProfile": "ManagedDisks",
    "vmSize": "Standard_D2_v2",
    "vnetSubnetId": null
  }
```

## <a name="manually-scale-pods"></a>手动缩放 Pod

到目前为止，Azure 投票前端和 Redis 实例已部署，每个都有一个副本。 若要进行验证，请运行 [kubectl get](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#get) 命令。

```azurecli
kubectl get pods
```

输出：

```
NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-2549686872-4d2r5   1/1       Running   0          31m
azure-vote-front-848767080-tf34m   1/1       Running   0          31m
```

使用 [kubectl scale](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#scale) 命令手动 `azure-vote-front` 部署中的 Pod 数。 此示例将该数量增加到 5。

```azurecli
kubectl scale --replicas=5 deployment/azure-vote-front
```

运行 [kubectl get pods](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#get) 以验证 Kubernetes 是否在创建 Pod。 一分钟左右之后，其他 Pod 在运行：

```azurecli
kubectl get pods
```

输出：

```
NAME                                READY     STATUS    RESTARTS   AGE
azure-vote-back-2606967446-nmpcf    1/1       Running   0          15m
azure-vote-front-3309479140-2hfh0   1/1       Running   0          3m
azure-vote-front-3309479140-bzt05   1/1       Running   0          3m
azure-vote-front-3309479140-fvcvm   1/1       Running   0          3m
azure-vote-front-3309479140-hrbf2   1/1       Running   0          15m
azure-vote-front-3309479140-qphz8   1/1       Running   0          3m
```

## <a name="autoscale-pods"></a>自动缩放 Pod

Kubernetes 支持[水平 Pod 自动缩放](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/)以根据 CPU 利用率或其他选择指标调整部署中的 Pod 数。

若要使用自动缩放程序，Pod 必须定义了 CPU 请求和限制。 在 `azure-vote-front` 部署中，前端容器请求 0.25 个 CPU，限制为 0.5 个 CPU。 设置与下面类似：

```YAML
resources:
  requests:
     cpu: 250m
  limits:
     cpu: 500m
```

下面的示例使用 [kubectl autoscale](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#autoscale) 命令自动缩放 `azure-vote-front` 部署中的 Pod 数。 在此处，如果 CPU 利用率超过 50%，则自动缩放程序会将 Pod 增加到最多 10 个。


```azurecli
kubectl autoscale deployment azure-vote-front --cpu-percent=50 --min=3 --max=10
```

若要查看自动缩放程序的状态，请运行以下命令：

```azurecli
kubectl get hpa
```

输出：

```
NAME               REFERENCE                     TARGETS    MINPODS   MAXPODS   REPLICAS   AGE
azure-vote-front   Deployment/azure-vote-front   0% / 50%   3         10        3          2m
```

在 Azure 投票应用处于最小负荷状态几分钟之后，Pod 副本数会自动减少到 3 个。

## <a name="next-steps"></a>后续步骤

在本教程中，在 Kubernetes 群集中使用了不同的缩放功能。 涉及的任务包括：

> [!div class="checklist"]
> * 手动缩放 Kubernetes Pod
> * 配置运行应用前端的自动缩放 Pod
> * 缩放 Kubernetes Azure 节点

继续学习下一个教程，了解如何在 Kubernetes 中更新应用程序。

> [!div class="nextstepaction"]
> [在 Kubernetes 中更新应用程序](./tutorial-kubernetes-app-update.md)