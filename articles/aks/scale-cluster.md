---
title: 缩放 Azure Kubernetes 服务 (AKS) 群集
description: 了解如何在 Azure Kubernetes 服务 (AKS) 群集中缩放节点数。
services: container-service
author: iainfoulds
ms.topic: article
ms.date: 05/31/2019
ms.author: iainfou
ms.openlocfilehash: 55d7a00a0a8c0b655f06810f8bcea7126bb9167f
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/14/2020
ms.locfileid: "79368411"
---
# <a name="scale-the-node-count-in-an-azure-kubernetes-service-aks-cluster"></a>在 Azure Kubernetes 服务 (AKS) 群集中缩放节点数

如果资源需要更改应用程序，可以手动缩放 AKS 群集以运行不同数量的节点。 缩小时，节点会被仔细[封锁和排出][kubernetes-drain]，以最大程度地减少运行应用程序的中断。 向上扩展时，AKS 将等待，直到 Kubernetes 群集 `Ready` 将节点标记为在其上计划 pod。

## <a name="scale-the-cluster-nodes"></a>缩放群集节点

首先，使用[az aks show][az-aks-show]命令获取节点池的*名称*。 以下示例在*myResourceGroup*资源组中获取名为*myAKSCluster*的群集的节点池名称：

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query agentPoolProfiles
```

以下示例输出表明名称为 nodepool1：

```output
[
  {
    "count": 1,
    "maxPods": 110,
    "name": "nodepool1",
    "osDiskSizeGb": 30,
    "osType": "Linux",
    "storageProfile": "ManagedDisks",
    "vmSize": "Standard_DS2_v2"
  }
]
```

使用[az aks scale][az-aks-scale]命令缩放群集节点。 以下示例将名为 *myAKSCluster* 的群集缩放为单个节点。 提供前一个命令中自己的 --nodepool-name，如 nodepool1：

```azurecli-interactive
az aks scale --resource-group myResourceGroup --name myAKSCluster --node-count 1 --nodepool-name <your node pool name>
```

以下示例输出显示群集已成功缩放为一个节点，如 agentPoolProfiles 部分中所示：

```json
{
  "aadProfile": null,
  "addonProfiles": null,
  "agentPoolProfiles": [
    {
      "count": 1,
      "maxPods": 110,
      "name": "nodepool1",
      "osDiskSizeGb": 30,
      "osType": "Linux",
      "storageProfile": "ManagedDisks",
      "vmSize": "Standard_DS2_v2",
      "vnetSubnetId": null
    }
  ],
  [...]
}
```

## <a name="next-steps"></a>后续步骤

在本文中，手动缩放 AKS 群集以增加或减少节点数。 你还可以使用[群集自动缩放程序][cluster-autoscaler]自动缩放群集。

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-scale]: /cli/azure/aks#az-aks-scale
[cluster-autoscaler]: cluster-autoscaler.md
