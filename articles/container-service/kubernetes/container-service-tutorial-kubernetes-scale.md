---
title: "Azure 容器服务教程 - 缩放应用程序 | Microsoft Docs"
description: "Azure 容器服务教程 - 缩放应用程序"
services: container-service
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "Docker, 容器, 微服务, Kubernetes, Azure"
ms.assetid: 
ms.service: container-service
ms.devlang: aurecli
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/14/2017
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: bb528c424bc71f0309439e741c30e16d0d13c7d7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="scale-kubernetes-pods-and-kubernetes-infrastructure"></a>缩放 Kubernetes Pod 和 Kubernetes 基础结构

如果已按照教程执行，则你在 Azure 容器服务中具有工作 Kubernetes 群集，并且部署了 Azure 投票应用。 

在本教程第 5 部分（共 7 部分）中，会在应用中扩大 Pod 并尝试 Pod 自动缩放。 还会了解如何缩放 Azure VM 代理节点数以更改群集用于托管工作负荷的容量。 已完成的任务包括：

> [!div class="checklist"]
> * 手动缩放 Kubernetes Pod
> * 配置运行应用前端的自动缩放 Pod
> * 缩放 Kubernetes Azure 代理节点

在后续教程中，将更新 Azure 投票应用程序，并将 Operations Management Suite 配置为监视 Kubernetes 群集。

## <a name="before-you-begin"></a>开始之前

在前面的教程中，我们已将应用程度打包到容器映像中，将此映像上传到 Azure 容器注册表，并创建了 Kubernetes 群集。 应用程序随后在 Kubernetes 群集上运行。 

如果尚未完成这些步骤，并且想要逐一完成，请返回到[教程 1 – 创建容器映像](./container-service-tutorial-kubernetes-prepare-app.md)。 

## <a name="manually-scale-pods"></a>手动缩放 Pod

到目前为止，Azure 投票前端和 Redis 实例已部署，每个都有一个副本。 若要进行验证，请运行 [kubectl get](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#get) 命令。

```azurecli-interactive
kubectl get pods
```

输出：

```bash
NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-2549686872-4d2r5   1/1       Running   0          31m
azure-vote-front-848767080-tf34m   1/1       Running   0          31m
```

使用 [kubectl scale](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#scale) 命令手动 `azure-vote-front` 部署中的 Pod 数。 此示例将该数量增加到 5。

```azurecli-interactive
kubectl scale --replicas=5 deployment/azure-vote-front
```

运行 [kubectl get pods](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#get) 以验证 Kubernetes 是否在创建 Pod。 一分钟左右之后，其他 Pod 在运行：

```azurecli-interactive
kubectl get pods
```

输出：

```bash
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


```azurecli-interactive
kubectl autoscale deployment azure-vote-front --cpu-percent=50 --min=3 --max=10
```

若要查看自动缩放程序的状态，请运行以下命令：

```azurecli-interactive
kubectl get hpa
```

输出：

```bash
NAME               REFERENCE                     TARGETS    MINPODS   MAXPODS   REPLICAS   AGE
azure-vote-front   Deployment/azure-vote-front   0% / 50%   3         10        3          2m
```

在 Azure 投票应用处于最小负荷状态几分钟之后，Pod 副本数会自动减少到 3 个。

## <a name="scale-the-agents"></a>缩放代理

如果在前面的教程中使用默认命令创建了 Kubernetes 群集，则它具有三个代理节点。 如果计划群集上具有更多或更少的容器工作负荷，则可以手动调整代理数。 使用 [az acs scale](/cli/azure/acs#scale) 命令，并使用 `--new-agent-count` 参数指定代理数。

下面的示例将名为 myK8sCluster 的 Kubernetes 群集中的代理节点数增加到 4 个。 该命令需要几分钟时间完成。

```azurecli-interactive
az acs scale --resource-group=myResourceGroup --name=myK8SCluster --new-agent-count 4
```

命令输出在 `agentPoolProfiles:count` 值中显示代理节点数：

```azurecli
{
  "agentPoolProfiles": [
    {
      "count": 4,
      "dnsPrefix": "myK8SCluster-myK8SCluster-e44f25-k8s-agents",
      "fqdn": "",
      "name": "agentpools",
      "vmSize": "Standard_D2_v2"
    }
  ],
...

```

## <a name="next-steps"></a>后续步骤

在本教程中，在 Kubernetes 群集中使用了不同的缩放功能。 涉及的任务包括：

> [!div class="checklist"]
> * 手动缩放 Kubernetes Pod
> * 配置运行应用前端的自动缩放 Pod
> * 缩放 Kubernetes Azure 代理节点

继续学习下一个教程，了解如何在 Kubernetes 中更新应用程序。

> [!div class="nextstepaction"]
> [在 Kubernetes 中更新应用程序](./container-service-tutorial-kubernetes-app-update.md)

