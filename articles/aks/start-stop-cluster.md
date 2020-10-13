---
title: '启动和停止 Azure Kubernetes 服务 (AKS) '
description: 了解如何停止或启动 Azure Kubernetes 服务 (AKS) 群集。
services: container-service
ms.topic: article
ms.date: 09/24/2020
author: palma21
ms.openlocfilehash: bc756994cf0f6e12af1c1ad5a6c8db304b4253e3
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2020
ms.locfileid: "91968772"
---
# <a name="stop-and-start-an-azure-kubernetes-service-aks-cluster-preview"></a>停止并启动 Azure Kubernetes 服务 (AKS) 群集 (预览) 

AKS 工作负荷可能不需要连续运行，例如仅在工作时间使用的开发群集。 这会导致 Azure Kubernetes Service (AKS) 群集可能处于空闲状态的时间，而不是系统组件的运行。 可以通过将[所有 `User` 节点池缩放到0来](scale-cluster.md#scale-user-node-pools-to-0)降低群集占用量，但当群集运行时，仍需要[ `System` 池](use-system-pools.md)来运行系统组件。 若要在这段时间内进一步优化成本，可以完全关闭群集)  (停止。 此操作将完全停止控制平面和代理节点，使你可以节省所有计算成本，同时还可以在重新启动时保持存储的所有对象和群集状态。 然后，你可以选择在周末之后离开的位置，或仅在运行 batch 作业时才运行群集。

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="before-you-begin"></a>开始之前

本文假定你拥有现有的 AKS 群集。 如果需要 AKS 群集，请参阅 AKS 快速入门[使用 Azure CLI][aks-quickstart-cli] 或[使用 Azure 门户][aks-quickstart-portal]。


### <a name="limitations"></a>限制

使用群集启动/停止功能时，以下限制适用：

- 只有虚拟机规模集支持的群集才支持此功能。
- 预览期间，专用群集不支持此功能。
- 已停止的 AKS 群集的群集状态将保留最多12个月。 如果群集停止了12个月以上，则无法恢复群集状态。 有关详细信息，请参阅 [AKS 支持策略](support-policies.md)。
- 预览期间，在尝试停止群集之前，需要停止群集自动缩放程序 (CA) 。
- 只能启动或删除已停止的 AKS 群集。 若要执行任何操作（例如缩放或升级），请先启动群集。

### <a name="install-the-aks-preview-azure-cli"></a>安装 `aks-preview` Azure CLI 

还需要 *aks-preview* Azure CLI 扩展版本0.4.64 或更高版本。 使用[az extension add][az-extension-add]命令安装*aks-preview* Azure CLI 扩展。 或使用 [az extension update][az-extension-update] 命令安装任何可用的更新。

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
``` 

### <a name="register-the-startstoppreview-preview-feature"></a>注册 `StartStopPreview` 预览功能

要使用启动/停止群集功能，必须 `StartStopPreview` 在订阅上启用功能标志。

`StartStopPreview`使用[az feature register][az-feature-register]命令注册功能标志，如以下示例中所示：

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "StartStopPreview"
```

状态显示为“已注册”需要几分钟时间**。 使用 [az feature list][az-feature-list] 命令验证注册状态：

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/StartStopPreview')].{Name:name,State:properties.state}"
```

准备就绪后，请使用[az provider register][az-provider-register]命令刷新*ContainerService*资源提供程序的注册：

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="stop-an-aks-cluster"></a>停止 AKS 群集

可以使用 `az aks stop` 命令停止正在运行的 AKS 群集的节点和控制平面。 以下示例停止名为 *myAKSCluster*的群集：

```azurecli-interactive
az aks stop --name myAKSCluster --resource-group myResourceGroup
```

你可以使用 [az aks show][az-aks-show] 命令来验证群集停止的时间，并在 `powerState` `Stopped` 以下输出中确认显示内容：

```json
{
[...]
  "nodeResourceGroup": "MC_myResourceGroup_myAKSCluster_westus2",
  "powerState":{
    "code":"Stopped"
  },
  "privateFqdn": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
[...]
}
```

如果表明 `provisioningState` `Stopping` 群集尚未完全停止。

> [!IMPORTANT]
> 如果使用的是 [Pod 中断预算](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/) ，停止操作可能需要较长时间才能完成。


## <a name="start-an-aks-cluster"></a>启动 AKS 群集

可以使用 `az aks start` 命令启动已停止的 AKS 群集的节点和控制平面。 重新启动群集，其中包含上一个控制面状态和代理节点数。  
以下示例启动名为 *myAKSCluster*的群集：

```azurecli-interactive
az aks start --name myAKSCluster --resource-group myResourceGroup
```

可以使用 [az aks show][az-aks-show] 命令来验证群集的启动时间，并 `powerState` `Running` 在以下输出中确认显示内容：

```json
{
[...]
  "nodeResourceGroup": "MC_myResourceGroup_myAKSCluster_westus2",
  "powerState":{
    "code":"Running"
  },
  "privateFqdn": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
[...]
}
```

如果表明 `provisioningState` `Starting` 你的群集尚未完全启动。


## <a name="next-steps"></a>后续步骤

- 若要了解如何将 `User` 池缩放为0，请参阅 [ `User` 将池缩放为 0](scale-cluster.md#scale-user-node-pools-to-0)。
- 若要了解如何使用点实例节省成本，请参阅 [将专色节点池添加到 AKS](spot-node-pool.md)。
- 若要了解有关 AKS 支持策略的详细信息，请参阅 [AKS 支持策略](support-policies.md)。

<!-- LINKS - external -->

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli&preserve-view=true
[az-extension-add]: /cli/azure/extension?view=azure-cli-latest#az-extension-add&preserve-view=true
[az-extension-update]: /cli/azure/extension?view=azure-cli-latest#az-extension-update&preserve-view=true
[az-feature-register]: /cli/azure/feature?view=azure-cli-latest#az-feature-register&preserve-view=true
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest#az-feature-list&preserve-view=true
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest#az-provider-register&preserve-view=true
[az-aks-show]: /cli/azure/aks?view=azure-cli-latest#az_aks_show
