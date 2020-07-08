---
title: 在 Azure Kubernetes 服务 (AKS) 中使用系统节点池
description: 了解如何在 Azure Kubernetes 服务 (AKS) 中创建和管理系统节点池
services: container-service
ms.topic: article
ms.date: 06/18/2020
ms.author: mlearned
ms.openlocfilehash: 9b6270f81e7af8bd508d29510698e6cf9a5a2010
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85052655"
---
# <a name="manage-system-node-pools-in-azure-kubernetes-service-aks"></a>在 Azure Kubernetes 服务 (AKS) 中管理系统节点池

在 Azure Kubernetes 服务 (AKS) 中，采用相同配置的节点分组成节点池。 节点池包含运行应用程序的底层 VM。 系统节点池和用户节点池是 AKS 群集的两种不同的节点池模式。 系统节点池主要用于托管关键系统 Pod（例如 CoreDNS 和 tunnelfront）。 用户节点池主要用于托管应用程序 Pod。 但是，如果希望在 AKS 群集中只有一个池，可以在系统节点池上计划应用程序 Pod。 每个 AKS 群集必须至少包含一个系统节点池，该池至少包含一个节点。

> [!Important]
> 如果在生产环境中为 AKS 群集运行单个系统节点池，则建议至少将三个节点用作节点池。

## <a name="before-you-begin"></a>准备阶段

* 需要安装并配置 Azure CLI 2.3.1 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][install-azure-cli]。

## <a name="limitations"></a>限制

创建和管理支持系统节点池的 AKS 群集时存在以下限制。

* 请参阅 [Azure Kubernetes 服务 (AKS) 中可用的配额、虚拟机大小限制和区域][quotas-skus-regions]。
* 必须使用虚拟机规模集作为 VM 类型来构建 AKS 群集。
* 节点池的名称只能包含小写字母数字字符，且必须以小写字母开头。 对于 Linux 节点池，长度必须为 1 到 12 个字符。 对于 Windows 节点池，长度必须介于1到6个字符之间。
* 必须使用 2020-03-01 版或更高版的 API 版本设置节点池模式。 在超过2020-03-01 的 API 版本上创建的群集只包含用户节点池，但可以按照 "[更新池模式" 步骤](#update-existing-cluster-system-and-user-node-pools)迁移到包含系统节点池。
* 节点池模式是必需属性，当使用 ARM 模板或直接 API 调用时，必须显式设置该属性。

## <a name="system-and-user-node-pools"></a>系统节点池和用户节点池

每个系统节点池节点的标签为 **kubernetes.azure.com/mode: system**。 每个 AKS 群集至少包含一个系统节点池。 系统节点池存在以下限制：

* 系统池 osType 必须为 Linux。
* 用户节点池 osType 可以是 Linux 或 Windows。
* 系统池必须至少包含一个节点，而用户节点池则可能包含零个或零个以上的节点。
* 系统节点池需要一个至少具有 2 个 vCPU 和 4GB 内存的 VM SKU。
* 系统节点池必须支持至少 30 个 Pod，如 [Pod 的最小值和最大值公式][maximum-pods]中所述。
* 现成节点池需要用户节点池。

对于节点池，可以执行以下操作：

* 将系统节点池更改为用户节点池，但前提是 AKS 群集中有另一个可以取代它的系统节点池。
* 将用户节点池更改为系统节点池。
* 删除用户节点池。
* 可以删除系统节点池，前提是在 AKS 群集中有另一个可取代它的系统节点池。
* 一个 AKS 群集可以包含多个系统节点池，并且至少需要一个系统节点池。
* 如果希望更改现有节点池上的各种不可变设置，可以新建节点池将其替换。 例如，添加具有全新 maxPods 设置的全新节点池，然后删除旧节点池。

## <a name="create-a-new-aks-cluster-with-a-system-node-pool"></a>创建包含系统节点池的新 AKS 群集

创建新的 AKS 群集时，会自动创建包含单个节点的系统节点池。 初始节点池默认为某种类型模式的系统。 使用 az aks nodepool add 创建新节点池时，除非显式指定模式参数，否则这些节点池为用户节点池。

以下示例在“eastus”区域创建名为“myResourceGroup”的资源组 。

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

使用 [az aks create][az-aks-create] 命令创建 AKS 群集。 以下示例创建包含一个系统池的名为 *myAKSCluster* 的群集，该系统池包含一个节点。 对于生产工作负荷，请确保使用至少包含三个节点的系统节点池。 此操作可能需要几分钟才能完成。

```azurecli-interactive
az aks create -g myResourceGroup --name myAKSCluster --node-count 1 --generate-ssh-keys
```

## <a name="add-a-system-node-pool-to-an-existing-aks-cluster"></a>将系统节点池添加到现有 AKS 群集

可将一个或多个系统节点池添加到现有 AKS 群集。 以下命令将添加模式类型系统的节点池，其默认计数为三个节点。

```azurecli-interactive
az aks nodepool add -g myResourceGroup --cluster-name myAKSCluster -n mynodepool --mode system
```
## <a name="show-details-for-your-node-pool"></a>显示节点池的详细信息

可以使用以下命令查看节点池的详细信息。  

```azurecli-interactive
az aks nodepool show -g myResourceGroup --cluster-name myAKSCluster -n mynodepool
```

将为系统节点池定义“系统”类型的模式，并为用户节点池定义“用户”类型的模式。 

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

## <a name="update-existing-cluster-system-and-user-node-pools"></a>更新现有群集系统和用户节点池

> [!NOTE]
> 必须使用 API 版本2020-03-01 或更高版本来设置系统节点池模式。 在超过2020-03-01 的 API 版本上创建的群集只包含用户节点池。 若要接收系统节点池功能并使其受益于较旧的群集，请在最新的 Azure CLI 版本上，用以下命令更新现有节点池的模式。

可以更改系统节点池和用户节点池的模式。 仅当 AKS 群集上已存在另一个系统节点池时，才能将系统节点池更改为用户池。

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
> 若要在 API 版本 2020-03-02 之前的 AKS 群集上使用系统节点池，请添加新的系统节点池，并删除原始的默认节点池。

以前无法删除系统节点池，它是 AKS 群集中的初始默认节点池。 现在可以灵活地从群集中删除任何节点池。 由于 AKS 群集至少需要一个系统节点池，因此 AKS 群集上必须至少包含两个系统节点池，才能删除其中的一个。

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster -n mynodepool
```

## <a name="next-steps"></a>后续步骤

本文介绍了如何在 AKS 群集中创建和管理系统节点池。 若要详细了解如何使用多个节点池，请参阅[使用多个节点池][use-multiple-node-pools]。

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