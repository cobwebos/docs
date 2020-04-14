---
title: 在 Azure 库伯奈斯服务 （AKS） 中使用系统节点池
description: 了解如何在 Azure 库伯奈斯服务 （AKS） 中创建和管理系统节点池
services: container-service
ms.topic: article
ms.date: 04/06/2020
ms.openlocfilehash: b567d9e618877463e1e659f368d35fbb787a4ef2
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/13/2020
ms.locfileid: "81259062"
---
# <a name="manage-system-node-pools-in-azure-kubernetes-service-aks"></a>在 Azure 库伯奈斯服务 （AKS） 中管理系统节点池

在 Azure Kubernetes 服务 (AKS) 中，采用相同配置的节点分组成节点池。** 节点池包含运行应用程序的基础 VM。 系统节点池和用户节点池是 AKS 群集的两种不同的节点池模式。 系统节点池主要用于托管关键系统 pod（如 CoreDNS 和隧道前）。 用户节点池的主要目的是托管应用程序窗格。 但是，如果您希望在 AKS 群集中只具有一个池，则可以在系统节点池上安排应用程序窗格。 每个 AKS 群集必须至少包含一个至少具有一个节点的系统节点池。 

> [!Important]
> 如果在生产环境中为 AKS 群集运行单个系统节点池，我们建议您为节点池至少使用三个节点。

## <a name="before-you-begin"></a>开始之前

* 您需要 Azure CLI 版本 2.3.1 或更高版本安装和配置。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][install-azure-cli]。

## <a name="limitations"></a>限制

创建和管理支持系统节点池的 AKS 群集时，以下限制适用。

* 请参阅[Azure 库伯奈斯服务 （AKS） 中的配额、虚拟机大小限制和地区可用性][quotas-skus-regions]。
* 必须使用虚拟机缩放集作为 VM 类型构建 AKS 群集。
* 节点池的名称只能包含小写字母数字字符，且必须以小写字母开头。 对于 Linux 节点池，长度必须介于 1 到 12 个字符之间。 对于 Windows 节点池，长度必须介于 1 到 6 个字符之间。

## <a name="system-and-user-node-pools"></a>系统和用户节点池

系统节点池节点各有**标签kubernetes.azure.com/mode：系统**。 每个 AKS 群集至少包含一个系统节点池。 系统节点池具有以下限制：

* 系统池 osType 必须是 Linux。
* 用户节点池 osType 可能是 Linux 或 Windows。
* 系统池必须至少包含一个节点，并且用户节点池可能包含零个或多个节点。
* 系统节点池需要至少 2 个 vCPU 和 4GB 内存的 VM SKU。
* 系统节点池必须支持至少 30 个[pod，如窗格的最小值和最大值公式][maximum-pods]所述。
* Spot 节点池需要用户节点池。

您可以对节点池执行以下操作：

* 将系统节点池更改为用户节点池，前提是您有另一个系统节点池要将其置于 AKS 群集中的位置。
* 将用户节点池更改为系统节点池。
* 删除用户节点池。
* 您可以删除系统节点池，前提是您有另一个系统节点池要将其置于 AKS 群集中的位置。
* AKS 群集可能具有多个系统节点池，并且至少需要一个系统节点池。

## <a name="create-a-new-aks-cluster-with-a-system-node-pool"></a>使用系统节点池创建新的 AKS 群集

创建新 AKS 群集时，将自动创建具有单个节点的系统节点池。 初始节点池默认为类型系统模式。 当您使用 az aks 节点池添加创建新节点池时，这些节点池是用户节点池，除非您显式指定模式参数。

下面的示例在*东部*区域创建名为*myResourceGroup*的资源组。

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

使用 [az aks create][az-aks-create] 命令创建 AKS 群集。 下面的示例创建一个名为*myAKSCluster*的群集，其中一个系统池包含一个节点。 对于生产工作负载，请确保使用至少包含三个节点的系统节点池。 此操作可能需要几分钟才能完成。

```azurecli-interactive
az aks create -g myResourceGroup --name myAKSCluster --node-count 1 --generate-ssh-keys
```

## <a name="add-a-system-node-pool-to-an-existing-aks-cluster"></a>将系统节点池添加到现有 AKS 群集

您可以将一个或多个系统节点池添加到现有 AKS 群集。 以下命令添加默认计数为三个节点的模式类型系统的节点池。

```azurecli-interactive
az aks nodepool add -g myResourceGroup --cluster-name myAKSCluster -n mynodepool --mode system
```
## <a name="show-details-for-your-node-pool"></a>显示节点池的详细信息

您可以使用以下命令检查节点池的详细信息。  

```azurecli-interactive
az aks nodepool show -g myResourceGroup --cluster-name myAKSCluster -n mynodepool
```

为**系统**节点池定义了系统类型模式，并为用户节点池定义了"**用户"** 类型模式。

```output
{
  "agentPoolType": "VirtualMachineScaleSets",
  "availabilityZones": null,
  "count": 3,
  "enableAutoScaling": null,
  "enableNodePublicIp": false,
  "id": "/subscriptions/666d66d8-1e43-4136-be25-f25bb5de5883/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/agentPools/mynodepool",
  "maxCount": null,
  "maxPods": 110,
  "minCount": null,
  "mode": "System",
  "name": "mynodepool",
  "nodeLabels": {},
  "nodeTaints": null,
  "orchestratorVersion": "1.15.10",
  "osDiskSizeGb": 100,
  "osType": "Linux",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "scaleSetEvictionPolicy": null,
  "scaleSetPriority": null,
  "spotMaxPrice": null,
  "tags": null,
  "type": "Microsoft.ContainerService/managedClusters/agentPools",
  "vmSize": "Standard_DS2_v2",
  "vnetSubnetId": null
}
```

## <a name="update-system-and-user-node-pools"></a>更新系统和用户节点池

您可以更改系统和用户节点池的模式。 仅当 AKS 群集上已存在另一个系统节点池时，才能将系统节点池更改为用户池。

此命令将系统节点池更改为用户节点池。

```azurecli-interactive
az aks nodepool update -g myResourceGroup --cluster-name myAKSCluster -n mynodepool --mode user
```

此命令将用户节点池更改为系统节点池。

```azurecli-interactive
az aks nodepool update -g myResourceGroup --cluster-name myAKSCluster -n mynodepool --mode system
```

## <a name="delete-a-system-node-pool"></a>删除系统节点池

> [!Note]
> 要在 API 版本 2020-03-02 之前在 AKS 群集上使用系统节点池，请添加新的系统节点池，然后删除原始默认节点池。

以前无法删除系统节点池，这是 AKS 群集中的初始默认节点池。 现在，您可以灵活地从群集中删除任何节点池。 由于 AKS 群集至少需要一个系统节点池，因此必须在 AKS 群集上至少有两个系统节点池，然后才能删除其中一个节点池。

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster -n mynodepool
```

## <a name="next-steps"></a>后续步骤

在本文中，您学习了如何在 AKS 群集中创建和管理系统节点池。 有关如何使用多个节点池的详细信息，请参阅[使用多个节点池][use-multiple-node-pools]。

<!-- EXTERNAL LINKS -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-taint]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#taint
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubernetes-labels]: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/
[kubernetes-label-syntax]: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/#syntax-and-character-set

<!-- INTERNAL LINKS -->
[aks-windows]: windows-container-cli.md
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-nodepool-add]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-add
[az-aks-nodepool-list]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-list
[az-aks-nodepool-update]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-update
[az-aks-nodepool-upgrade]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-upgrade
[az-aks-nodepool-scale]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-scale
[az-aks-nodepool-delete]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-delete
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[gpu-cluster]: gpu-cluster.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[quotas-skus-regions]: quotas-skus-regions.md
[supported-versions]: supported-kubernetes-versions.md
[tag-limitation]: ../azure-resource-manager/resource-group-using-tags.md
[taints-tolerations]: operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations
[vm-sizes]: ../virtual-machines/linux/sizes.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[maximum-pods]: faq.md#why-cant-i-set-maxpods-below-30