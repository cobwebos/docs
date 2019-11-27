---
title: 缩放 Azure Kubernetes 服务 (AKS) 群集
description: 了解如何在 Azure Kubernetes 服务 (AKS) 群集中缩放节点数。
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 05/31/2019
ms.author: iainfou
ms.openlocfilehash: 719f45aeeb5c7aa7e9b5e597ed461808c9d2b005
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73472586"
---
# <a name="scale-the-node-count-in-an-azure-kubernetes-service-aks-cluster"></a>在 Azure Kubernetes 服务 (AKS) 群集中缩放节点数

如果资源需要更改应用程序，可以手动缩放 AKS 群集以运行不同数量的节点。 节点数减少时，节点会被仔细[封锁和排除][kubernetes-drain]，尽量避免对正在运行的应用程序造成中断。 纵向扩展时，AKS 会一直等待，直到节点被 Kubernetes 群集标记为 `Ready`，然后才在这些节点上计划 Pod。

## <a name="scale-the-cluster-nodes"></a>缩放群集节点

首先，使用 *az aks show* 命令获取节点池的名称[][az-aks-show]。 以下示例获取 myResourceGroup 资源组中名为 myAKSCluster 的群集的节点池名称：

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query agentPoolProfiles
```

以下示例输出表明名称为 nodepool1：

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

使用 [az aks scale][az-aks-scale] 命令缩放群集节点。 以下示例将名为 *myAKSCluster* 的群集缩放为单个节点。 提供前一个命令中自己的 --nodepool-name，如 nodepool1：

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

在本文中，你手动缩放了 AKS 群集以增加或减少节点数量。 你还可以使用[群集自动缩放程序][cluster-autoscaler]自动缩放群集。

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-scale]: /cli/azure/aks#az-aks-scale
[cluster-autoscaler]: cluster-autoscaler.md
