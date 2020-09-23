---
title: 在 Azure Kubernetes 服务 (AKS) 中使用系统节点池
description: 了解如何在 Azure Kubernetes 服务 (AKS) 中创建和管理系统节点池
services: container-service
ms.topic: article
ms.date: 06/18/2020
ms.author: mlearned
ms.custom: fasttrack-edit
ms.openlocfilehash: 2cb6ed265d3e94c2c162381dfb80ba0c5427a71f
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90888946"
---
# <a name="manage-system-node-pools-in-azure-kubernetes-service-aks"></a>在 Azure Kubernetes 服务 (AKS) 中管理系统节点池

在 Azure Kubernetes 服务 (AKS) 中，采用相同配置的节点分组成节点池。 节点池包含运行应用程序的底层 VM。 系统节点池和用户节点池是 AKS 群集的两种不同的节点池模式。 系统节点池提供托管关键系统箱（如和）的主要 `CoreDNS` 目的 `metrics-server` 。 用户节点池主要用于托管应用程序 Pod。 但是，如果希望在 AKS 群集中只有一个池，可以在系统节点池上计划应用程序 Pod。 每个 AKS 群集必须至少包含一个系统节点池，该池至少包含一个节点。

> [!Important]
> 如果在生产环境中为 AKS 群集运行单个系统节点池，则建议至少将三个节点用作节点池。

## <a name="before-you-begin"></a>准备阶段

* 需要安装并配置 Azure CLI 2.3.1 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][install-azure-cli]。

## <a name="limitations"></a>限制

创建和管理支持系统节点池的 AKS 群集时存在以下限制。

* 请参阅 [Azure Kubernetes 服务 (AKS) 中可用的配额、虚拟机大小限制和区域][quotas-skus-regions]。
* 必须将虚拟机规模集用作 VM 类型并使用标准 SKU 负载均衡器来生成 AKS 群集。
* 节点池的名称只能包含小写字母数字字符，且必须以小写字母开头。 对于 Linux 节点池，长度必须为 1 到 12 个字符。 对于 Windows 节点池，长度必须在 1 到 6 个字符之间。
* 必须使用 2020-03-01 版或更高版的 API 版本设置节点池模式。 在 2020-03-01 之前的 API 版本上创建的集群仅包含用户节点池，但可以按照[更新池模式步骤](#update-existing-cluster-system-and-user-node-pools)进行迁移，通过这种方式包含系统节点池。
* 节点池模式是必需属性，当使用 ARM 模板或直接 API 调用时，必须显式设置该属性。

## <a name="system-and-user-node-pools"></a>系统节点池和用户节点池

对于系统节点池，AKS 会自动为其节点分配“kubernetes.azure.com/mode: system”标签。 这使 AKS 倾向于在包含此标签的节点池上计划系统 Pod。 此标签不会阻止你在系统节点池上计划应用程序 Pod。 但是，我们建议将关键系统 Pod 与应用程序 Pod 隔离，以防配置错误或未授权的应用程序 Pod 意外终止系统 Pod。 可以通过创建专用系统节点池来强制执行此行为。 使用 `CriticalAddonsOnly=true:NoSchedule` 污点可防止在系统节点池上计划应用程序 Pod。

系统节点池存在以下限制：

* 系统池 osType 必须为 Linux。
* 用户节点池 osType 可以是 Linux 或 Windows。
* 系统池必须至少包含一个节点，而用户节点池则可能包含零个或零个以上的节点。
* 系统节点池需要一个至少具有 2 个 vCPU 和 4GB 内存的 VM SKU。
* 系统节点池必须支持至少 30 个 Pod，如 [Pod 的最小值和最大值公式][maximum-pods]中所述。
* 现成节点池需要用户节点池。
* 添加其他系统节点池或更改哪个节点池为系统节点池 *不* 会自动移动系统箱。 即使将系统箱更改为用户节点池，系统盒也可以继续在同一节点池上运行。 如果删除或缩小运行系统箱的节点池，而该节点池以前是系统节点池，则会将这些系统箱重新部署到新的系统节点池。

对于节点池，可以执行以下操作：

* 创建专用系统节点池（优先将系统 Pod 计划到 `mode:system` 的节点池）
* 将系统节点池更改为用户节点池，但前提是 AKS 群集中有另一个可以取代它的系统节点池。
* 将用户节点池更改为系统节点池。
* 删除用户节点池。
* 可以删除系统节点池，前提是在 AKS 群集中有另一个可取代它的系统节点池。
* 一个 AKS 群集可以包含多个系统节点池，并且至少需要一个系统节点池。
* 如果希望更改现有节点池上的各种不可变设置，可以新建节点池将其替换。 例如，添加具有全新 maxPods 设置的全新节点池，然后删除旧节点池。

## <a name="create-a-new-aks-cluster-with-a-system-node-pool"></a>创建包含系统节点池的新 AKS 群集

创建新的 AKS 群集时，会自动创建包含单个节点的系统节点池。 初始节点池默认为某种类型模式的系统。 使用 `az aks nodepool add` 创建新节点池时，除非显式指定模式参数，否则这些节点池为用户节点池。

以下示例在“eastus”区域创建名为“myResourceGroup”的资源组 。

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

使用 [az aks create][az-aks-create] 命令创建 AKS 群集。 以下示例创建包含一个专用系统池（包含一个节点）的名为 myAKSCluster 的群集。 对于生产工作负荷，请确保使用至少包含三个节点的系统节点池。 此操作可能需要几分钟才能完成。

```azurecli-interactive
# Create a new AKS cluster with a single system pool
az aks create -g myResourceGroup --name myAKSCluster --node-count 1 --generate-ssh-keys
```

## <a name="add-a-dedicated-system-node-pool-to-an-existing-aks-cluster"></a>将专用系统节点池添加到现有 AKS 群集

> [!Important]
> 创建节点池后，无法通过 CLI 更改节点污点。

可将一个或多个系统节点池添加到现有 AKS 群集。 建议在用户节点池上计划应用程序 Pod，并将系统节点池专用于关键系统 Pod。 这样可防止非授权应用程序 Pod 意外终止系统 Pod。 通过 `CriticalAddonsOnly=true:NoSchedule` [污点][aks-taints]为系统节点池强制执行此行为。 

以下命令添加模式类型系统的专用节点池，其默认计数为三个节点。

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name systempool \
    --node-count 3 \
    --node-taints CriticalAddonsOnly=true:NoSchedule \
    --mode System
```
## <a name="show-details-for-your-node-pool"></a>显示节点池的详细信息

可以使用以下命令查看节点池的详细信息。  

```azurecli-interactive
az aks nodepool show -g myResourceGroup --cluster-name myAKSCluster -n systempool
```

将为系统节点池定义“系统”类型的模式，并为用户节点池定义“用户”类型的模式。  对于系统池，请验证污点是否设置为 `CriticalAddonsOnly=true:NoSchedule`，这将防止在此节点池上计划应用程序 Pod。

```output
{
  "agentPoolType": "VirtualMachineScaleSets",
  "availabilityZones": null,
  "count": 1,
  "enableAutoScaling": null,
  "enableNodePublicIp": false,
  "id": "/subscriptions/yourSubscriptionId/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/agentPools/systempool",
  "maxCount": null,
  "maxPods": 110,
  "minCount": null,
  "mode": "System",
  "name": "systempool",
  "nodeImageVersion": "AKSUbuntu-1604-2020.06.30",
  "nodeLabels": {},
  "nodeTaints": [
    "CriticalAddonsOnly=true:NoSchedule"
  ],
  "orchestratorVersion": "1.16.10",
  "osDiskSizeGb": 128,
  "osType": "Linux",
  "provisioningState": "Failed",
  "proximityPlacementGroupId": null,
  "resourceGroup": "myResourceGroup",
  "scaleSetEvictionPolicy": null,
  "scaleSetPriority": null,
  "spotMaxPrice": null,
  "tags": null,
  "type": "Microsoft.ContainerService/managedClusters/agentPools",
  "upgradeSettings": {
    "maxSurge": null
  },
  "vmSize": "Standard_DS2_v2",
  "vnetSubnetId": null
}
```

## <a name="update-existing-cluster-system-and-user-node-pools"></a>更新现有的群集系统和用户节点池

> [!NOTE]
> 必须使用 2020-03-01 或更高版本的 API 版本设置系统节点池模式。 而在 2020-03-01 之前的 API 版本上创建的集群仅包含用户节点池。 若要在较旧的群集上获得系统节点池功能和权益，请在最新的 Azure CLI 版本上，用以下命令更新现有节点池的模式。

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

你必须在 AKS 群集上至少有两个系统节点池，然后才能删除其中的一个。

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster -n mynodepool
```

## <a name="clean-up-resources"></a>清理资源

若要删除群集，请使用 [az group delete][az-group-delete] 命令删除 AKS 资源组：

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
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
[aks-taints]: use-multiple-node-pools.md#schedule-pods-using-taints-and-tolerations
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
[tag-limitation]: ../azure-resource-manager/management/tag-resources.md
[taints-tolerations]: operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations
[vm-sizes]: ../virtual-machines/sizes.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[maximum-pods]: configure-azure-cni.md#maximum-pods-per-node
