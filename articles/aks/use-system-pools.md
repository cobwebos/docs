---
title: 使用 Azure Kubernetes 服务中的系统节点池（AKS）
description: 了解如何在 Azure Kubernetes Service （AKS）中创建和管理系统节点池
services: container-service
ms.topic: article
ms.date: 04/28/2020
ms.openlocfilehash: 85cc699d6ef8c632663775e91f2b5cad6ca7a7b6
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83125241"
---
# <a name="manage-system-node-pools-in-azure-kubernetes-service-aks"></a>在 Azure Kubernetes Service （AKS）中管理系统节点池

在 Azure Kubernetes 服务 (AKS) 中，采用相同配置的节点分组成节点池。** 节点池包含运行应用程序的基础 Vm。 系统节点池和用户节点池是 AKS 群集的两个不同的节点池模式。 系统节点池提供托管关键系统箱（例如 CoreDNS 和 tunnelfront）的主要目的。 用户节点池服务于托管应用程序箱的主要目的。 但是，如果你想要在 AKS 群集中只有一个池，可以在系统节点池上计划应用程序盒。 每个 AKS 群集必须至少包含一个具有至少一个节点的系统节点池。 

> [!Important]
> 如果在生产环境中为 AKS 群集运行单个系统节点池，则建议使用至少三个节点作为节点池。

## <a name="before-you-begin"></a>开始之前

* 需要安装并配置 Azure CLI 版本2.3.1 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][install-azure-cli]。

## <a name="limitations"></a>限制

此限制适用于创建和管理支持系统节点池的 AKS 群集。

* 请参阅[Azure Kubernetes Service （AKS）中的配额、虚拟机大小限制和区域可用性][quotas-skus-regions]。
* 必须采用虚拟机规模集作为 VM 类型构建 AKS 群集。
* 节点池的名称只能包含小写字母数字字符，且必须以小写字母开头。 对于 Linux 节点池，长度必须在1到12个字符之间。 对于 Windows 节点池，长度必须介于1到6个字符之间。
* 必须使用 API 版本2020-03-01 或更高版本来设置节点池模式。
* 节点池的模式是必需的属性，并且在使用 ARM 模板或直接 API 调用时必须显式设置。

## <a name="system-and-user-node-pools"></a>系统和用户节点池

系统节点池节点每个节点都具有标签**kubernetes.azure.com/mode： system**。 每个 AKS 群集都包含至少一个系统节点池。 系统节点池具有以下限制：

* 系统池 osType 必须为 Linux。
* 用户节点池 osType 可以是 Linux 或 Windows。
* 系统池必须至少包含一个节点，并且用户节点池可能包含零个或多个节点。
* 系统节点池需要至少具有2个个 vcpu 和4GB 内存的 VM SKU。
* 对于 pod 的[最小值和最大值公式，][maximum-pods]系统节点池必须至少支持30个 pod。
* 专色节点池需要用户节点池。

可以通过节点池执行以下操作：

* 将系统节点池更改为用户节点池，前提是你有另一个要在 AKS 群集中使用的系统节点池。
* 将用户节点池更改为系统节点池。
* 删除用户节点池。
* 你可以删除系统节点池，前提是你有另一个要在 AKS 群集中使用的系统节点池。
* AKS 群集可以有多个系统节点池，并且至少需要一个系统节点池。
* 如果要更改现有节点池的各种不可变设置，可以创建新的节点池来替换它们。 例如，使用新的 maxPods 设置添加新的节点池，并删除旧的节点池。

## <a name="create-a-new-aks-cluster-with-a-system-node-pool"></a>使用系统节点池创建新的 AKS 群集

创建新的 AKS 群集时，会自动创建具有单个节点的系统节点池。 初始节点池默认为类型为 "系统" 的模式。 当你通过 az aks nodepool add 创建新的节点池时，这些节点池为用户节点池，除非显式指定模式参数。

以下示例在*eastus*区域中创建名为*myResourceGroup*的资源组。

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

使用 [az aks create][az-aks-create] 命令创建 AKS 群集。 以下示例创建一个名为*myAKSCluster*的群集，其中包含一个包含一个节点的系统池。 对于生产工作负荷，请确保使用至少包含三个节点的系统节点池。 此操作可能需要几分钟才能完成。

```azurecli-interactive
az aks create -g myResourceGroup --name myAKSCluster --node-count 1 --generate-ssh-keys
```

## <a name="add-a-system-node-pool-to-an-existing-aks-cluster"></a>将系统节点池添加到现有 AKS 群集

可以将一个或多个系统节点池添加到现有的 AKS 群集。 以下命令将添加模式类型系统的节点池，其默认计数为三个节点。

```azurecli-interactive
az aks nodepool add -g myResourceGroup --cluster-name myAKSCluster -n mynodepool --mode system
```
## <a name="show-details-for-your-node-pool"></a>显示节点池的详细信息

可以通过以下命令查看节点池的详细信息。  

```azurecli-interactive
az aks nodepool show -g myResourceGroup --cluster-name myAKSCluster -n mynodepool
```

系统节点池的**类型为系统的**模式，并且为用户节点池定义了**type 类型的**模式。

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

可以更改系统和用户节点池的模式。 仅当 AKS 群集上已存在另一个系统节点池时，才能将系统节点池改为用户池。

此命令会将系统节点池更改为用户节点池。

```azurecli-interactive
az aks nodepool update -g myResourceGroup --cluster-name myAKSCluster -n mynodepool --mode user
```

此命令会将用户节点池更改为系统节点池。

```azurecli-interactive
az aks nodepool update -g myResourceGroup --cluster-name myAKSCluster -n mynodepool --mode system
```

## <a name="delete-a-system-node-pool"></a>删除系统节点池

> [!Note]
> 若要在 API 版本2020-03-02 之前使用 AKS 群集上的系统节点池，请添加新的系统节点池，并删除原始默认节点池。

以前，你无法删除系统节点池，这是 AKS 群集中的初始默认节点池。 你现在可以灵活地从群集中删除任何节点池。 由于 AKS 群集需要至少一个系统节点池，因此必须在 AKS 群集上至少有两个系统节点池，才能删除其中的一个池。

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster -n mynodepool
```

## <a name="next-steps"></a>后续步骤

本文介绍了如何在 AKS 群集中创建和管理系统节点池。 有关如何使用多个节点池的详细信息，请参阅[使用多节点池][use-multiple-node-pools]。

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
[maximum-pods]: configure-azure-cni.md#maximum-pods-per-node