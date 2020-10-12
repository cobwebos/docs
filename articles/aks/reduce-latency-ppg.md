---
title: 使用邻近组来减少 Azure Kubernetes Service (AKS) 群集的延迟
description: 了解如何使用邻近组来减少 AKS 群集工作负荷的延迟。
services: container-service
manager: gwallace
ms.topic: article
ms.date: 07/10/2020
author: jluk
ms.openlocfilehash: 5b3dc3803cfb89f4a74d082b5913e69df1d03a00
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87986706"
---
# <a name="reduce-latency-with-proximity-placement-groups-preview"></a>通过邻近组 (预览降低延迟) 

> [!Note]
> 在 AKS 上使用邻近位置组时，归置仅适用于代理节点。 节点到节点和对应的托管 pod 到 pod 的延迟得到改进。 归置不会影响群集的控制平面的放置。

在 Azure 中部署应用程序时，跨区域或可用性区域 (VM) 实例分配虚拟机将产生网络延迟，这可能会影响应用程序的总体性能。 邻近性放置组是一种逻辑分组，用于确保 Azure 计算资源的物理位置彼此接近。 有些应用程序（如游戏、工程模拟和高频交易） (HFT) 需要较低的延迟和快速完成的任务。 为实现高性能计算 (HPC) 方案，请考虑使用群集节点池 (PPG) 的 [邻近性放置组](../virtual-machines/linux/co-location.md#proximity-placement-groups) 。

## <a name="limitations"></a>限制

* 邻近位置组最多只能映射到一个可用性区域。
* 节点池必须使用虚拟机规模集来关联邻近位置组。
* 节点池只能在节点池创建时间关联邻近位置组。

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="before-you-begin"></a>在开始之前

必须已安装以下资源：

- Aks-preview 0.4.53 扩展

### <a name="set-up-the-preview-feature-for-proximity-placement-groups"></a>设置邻近位置组的预览功能

> [!IMPORTANT]
> 使用 AKS 节点池的邻近组时，归置仅适用于代理节点。 节点到节点和对应的托管 pod 到 pod 的延迟得到改进。 归置不会影响群集的控制平面的放置。

```azurecli-interactive
# register the preview feature
az feature register --namespace "Microsoft.ContainerService" --name "ProximityPlacementGroupPreview"
```

注册可能需要几分钟时间。 使用以下命令验证该功能是否已注册：

```azurecli-interactive
# Verify the feature is registered:
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/ProximityPlacementGroupPreview')].{Name:name,State:properties.state}"
```

预览期间，需要使用 *aks* CLI 扩展来使用邻近位置组。 使用 [az extension add][az-extension-add] 命令，然后使用 [az extension update][az-extension-update] 命令来查找任何可用的更新：

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="node-pools-and-proximity-placement-groups"></a>节点池和邻近位置组

使用邻近位置组部署的第一个资源会附加到特定的数据中心。 部署了相同邻近位置组的其他资源将在同一数据中心中进行定位。 使用邻近位置组的所有资源停止 (释放) 或删除后，就不再附加。

* 许多节点池可以与单个邻近布局组相关联。
* 节点池只能与单个邻近布局组相关联。

### <a name="configure-proximity-placement-groups-with-availability-zones"></a>通过可用性区域配置邻近位置组

> [!NOTE]
> 尽管近程布局组要求一个节点池使用最多一个可用性区域，但对于单个区域中的 Vm， [99.9% 的基准 AZURE VM SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_9/) 仍有效。

邻近性放置组是一个节点池概念，并与每个单独的节点池相关联。 使用 PPG 资源对 AKS 控制面可用性没有影响。 这可能会影响使用区域设计群集的方式。 若要确保群集跨多个区域分布，建议采用以下设计。

* 使用3个区域预配具有第一个系统池的群集，而不会对任何邻近的放置组进行关联。 这可确保在专用节点池中的系统箱土地分散到多个区域。
* 添加具有唯一区域的其他用户节点池和与每个池关联的邻近位置组。 例如，区域1中的 nodepool1、区域2中的 PPG1、nodepool2 和区域3中的 PPG2、nodepool3 和 PPG3。 这可以确保在群集级别，节点分散到多个区域，而每个单独的节点池在指定的区域中与专用 PPG 资源协同工作。

## <a name="create-a-new-aks-cluster-with-a-proximity-placement-group"></a>使用邻近位置组创建新的 AKS 群集

以下示例使用[az group create][az-group-create]命令在*centralus*区域中创建名为*myResourceGroup*的资源组。 然后使用 [az AKS create][az-aks-create] 命令创建名为 *myAKSCluster* 的 AKS 群集。

加速网络极大地提高了虚拟机的网络性能。 理想情况下，将邻近组与加速网络结合使用。 默认情况下，AKS 使用支持的 [虚拟机实例](../virtual-network/create-vm-accelerated-networking-cli.md?toc=/azure/virtual-machines/linux/toc.json#limitations-and-constraints)上的加速网络，其中包含具有两个或更多个 vcpu 的大多数 Azure 虚拟机。

使用与第一个系统节点池关联的邻近位置组创建新的 AKS 群集：

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location centralus
```
运行以下命令，并存储返回的 ID：

```azurecli-interactive
# Create proximity placement group
az ppg create -n myPPG -g myResourceGroup -l centralus -t standard
```

此命令生成输出，其中包括即将推出的 CLI 命令所需的 *id* 值：

```output
{
  "availabilitySets": null,
  "colocationStatus": null,
  "id": "/subscriptions/yourSubscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.Compute/proximityPlacementGroups/myPPG",
  "location": "centralus",
  "name": "myPPG",
  "proximityPlacementGroupType": "Standard",
  "resourceGroup": "myResourceGroup",
  "tags": {},
  "type": "Microsoft.Compute/proximityPlacementGroups",
  "virtualMachineScaleSets": null,
  "virtualMachines": null
}
```

在下面的命令中，使用 " *myPPGResourceID* " 值的邻近位置组资源 ID：

```azurecli-interactive
# Create an AKS cluster that uses a proximity placement group for the initial system node pool only. The PPG has no effect on the cluster control plane.
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --ppg myPPGResourceID
```

## <a name="add-a-proximity-placement-group-to-an-existing-cluster"></a>向现有群集添加邻近布局组

可以通过创建一个新的节点池来向现有群集添加邻近布局组。 然后，可以选择将现有工作负荷迁移到新的节点池，然后删除原始节点池。

使用之前创建的邻近位置组，这将确保 AKS 群集的两个节点池中的代理节点物理位于同一数据中心。

使用前面创建的邻近位置组中的资源 ID，并使用以下命令添加新的节点池 [`az aks nodepool add`][az-aks-nodepool-add] ：

```azurecli-interactive
# Add a new node pool that uses a proximity placement group, use a --node-count = 1 for testing
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 1 \
    --ppg myPPGResourceID
```

## <a name="clean-up"></a>清除

若要删除群集，请使用 [`az group delete`][az-group-delete] 命令删除 AKS 资源组：

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>后续步骤

* 了解有关 [邻近位置组][proximity-placement-groups]的详细信息。

<!-- LINKS - Internal -->
[azure-ad-rbac]: azure-ad-rbac.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-upgrades]: /cli/azure/aks#az-aks-get-upgrades
[az-aks-upgrade]: /cli/azure/aks#az-aks-upgrade
[az-aks-show]: /cli/azure/aks#az-aks-show
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[proximity-placement-groups]: ../virtual-machines/linux/co-location.md#proximity-placement-groups
[az-aks-create]: /cli/azure/aks#az-aks-create
[system-pool]: ./use-system-pools.md
[az-aks-nodepool-add]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-add
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
