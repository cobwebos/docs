---
title: 缩放 Azure Kubernetes 服务 (AKS) 群集
description: 了解如何在 Azure Kubernetes 服务 (AKS) 群集中缩放节点数。
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 05/31/2019
ms.author: iainfoulds
ms.openlocfilehash: de3f8613c93715aecf7e9e066a8ad1d82e4379e3
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2019
ms.locfileid: "66475128"
---
# <a name="scale-the-node-count-in-an-azure-kubernetes-service-aks-cluster"></a>在 Azure Kubernetes 服务 (AKS) 群集中缩放节点数

如果资源需要更改应用程序，可以手动缩放 AKS 群集以运行不同数量的节点。 节点数减少时，节点会被仔细[封锁和排除][kubernetes-drain]，尽量避免对正在运行的应用程序造成中断。 当规模扩大时，AKS 等待，直到节点标`Ready`Kubernetes 群集然后 pod 安排它们。

## <a name="scale-the-cluster-nodes"></a>缩放群集节点

首先，获取*名称*的节点池使用[az aks show] [ az-aks-show]命令。 下面的示例获取名为群集的节点池名称*myAKSCluster*中*myResourceGroup*资源组：

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query agentPoolProfiles
```

以下示例输出表明名称为 nodepool1   ：

```console
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query agentPoolProfiles

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

使用[az aks 规模][ az-aks-scale]命令缩放群集节点。 以下示例将名为 *myAKSCluster* 的群集缩放为单个节点。 提供前一个命令中自己的 --nodepool-name，如 nodepool1   ：

```azurecli-interactive
az aks scale --resource-group myResourceGroup --name myAKSCluster --node-count 1 --nodepool-name <your node pool name>
```

以下示例输出显示群集已成功缩放为一个节点，如 agentPoolProfiles 部分中所示  ：

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

在本文中，你手动缩放 AKS 群集来增加或减少的节点数。 此外可以使用[群集自动缩放程序][ cluster-autoscaler] （目前以预览版在 AKS 中） 进行自动缩放你的群集。

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-scale]: /cli/azure/aks#az-aks-scale
[cluster-autoscaler]: cluster-autoscaler.md
