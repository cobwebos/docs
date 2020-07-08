---
title: 在 Azure Kubernetes 服务 (AKS) 中使用可用性区域
description: 了解如何在 Azure Kubernetes 服务 (AKS) 中创建跨可用性区域分发节点的群集
services: container-service
ms.custom: fasttrack-edit, references_regions
ms.topic: article
ms.date: 02/27/2020
ms.openlocfilehash: 06507c75d486717a77676154818f2032b7e8c807
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84195563"
---
# <a name="create-an-azure-kubernetes-service-aks-cluster-that-uses-availability-zones"></a>创建使用可用性区域的 Azure Kubernetes 服务 (AKS) 群集

Azure Kubernetes 服务 (AKS) 群集跨基础 Azure 基础结构的逻辑部分分配节点和存储等资源。 在使用可用性区域时，此部署模型确保给定可用性区域中的节点与另一个可用性区域中定义的节点在物理上分离。 使用在群集中配置的多个可用性区域部署的 AKS 群集可提供更高级别的可用性，以防止出现硬件失败或计划内维护事件。

通过在群集中定义跨越多个区域的节点池，即使单个区域已关闭，给定节点池中的节点也可以继续运行。 如果应用程序经协调允许节点子集故障，即使单个数据中心出现物理故障，应用程序仍可以继续运行。

本文介绍如何创建 AKS 群集，以及如何在可用性区域之间分发节点组件。

## <a name="before-you-begin"></a>开始之前

需要安装并配置 Azure CLI 2.0.76 或更高版本。 运行  `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅 [安装 Azure CLI][install-azure-cli]。

## <a name="limitations-and-region-availability"></a>限制和地区可用性

目前可以在以下地区使用可用性区域创建 AKS 群集：

* 美国中部
* 美国东部 2
* 美国东部
* 法国中部
* 日本东部
* 北欧
* 东南亚
* 英国南部
* 西欧
* 美国西部 2

使用可用性区域创建 AKS 群集时，以下限制适用：

* 只有在创建群集或节点池时，才能定义可用性区域。
* 创建群集后，无法更新可用性区域设置。 也不能更新现有非可用性区域群集来使用可用性区域。
* 选定的“所选节点大小 (VM SKU)”必须在所选的所有可用性区域中可用。
* 启用了可用性区域的群集要求使用 Azure 标准负载均衡器进行跨区域分发。 此负载均衡器类型仅可在创建群集时定义。 有关标准负载均衡器的详细信息和限制，请参阅 [Azure 负载均衡器标准 SKU 限制][standard-lb-limitations]。

### <a name="azure-disks-limitations"></a>Azure 磁盘限制

使用 Azure 托管磁盘的卷当前不是区域冗余资源。 卷不能跨区域附加，并且必须与承载目标 pod 的给定节点位于同一区域中。

如果必须运行有状态的工作负载，请使用 pod 规范中的节点池排斥和容许将 pod 计划分组到磁盘所在的同一区域中。 另外，还可以使用基于网络的存储（如 Azure 文件存储），在区域间对该存储进行调度时可将其附加到 pod。

## <a name="overview-of-availability-zones-for-aks-clusters"></a>AKS 群集的可用性区域概述

可用性区域是一种高可用性产品/服务，在数据中心发生故障时可以保护应用程序和数据。 这些区域是 Azure 地区中独特的物理位置。 每个区域由一个或多个数据中心组成，这些数据中心配置了独立电源、冷却和网络。 为确保能够进行复原，所有已启用的地区中必须至少有三个单独的区域。 数据中心发生故障时，区域中的可用性区域的物理隔离可保护应用程序和数据。

有关详细信息，请参阅 [Azure 中的可用性区域是什么？][az-overview]。

使用可用性区域部署的 AKS 群集可以在单个地区内的多个区域之间分发节点。 例如，美国东部 2 **   地区中的群集可在美国东部 2 的所有三个可用性区域中创建节点。 这种 AKS 群集资源的分布可提高群集的可用性，因为它们可以在特定区域发生故障时进行恢复。

![跨可用性区域的 AKS 节点分布](media/availability-zones/aks-availability-zones.png)

如果单个区域不可用，则当群集分布在多个区域时，应用程序将继续运行。

## <a name="create-an-aks-cluster-across-availability-zones"></a>跨可用性区域创建 AKS 群集

使用 [az aks create][az-aks-create] 命令创建群集时，`--zones` 参数定义代理节点部署到的区域。 如果在创建群集时定义 `--zones` 参数，则控制平面组件（如 etcd）将分布在三个区域。 控制平面组件所分布到的特定区域与为初始节点池选择的显式区域无关。

如果在创建 AKS 群集时未为默认代理池定义任何区域，则不能保证控制平面组件分布到可用性区域。 可以使用 [az aks nodepool add][az-aks-nodepool-add] 命令添加更多节点池，并为新节点指定 `--zones`，但这不会更改控制平面在各个区域中的分布方式。 可用性区域设置只能在群集或节点池创建时定义。

以下示例在名为 myResourceGroup 的资源组中创建名为 myAKSCluster 的 AKS 群集。 总共已创建 3 个节点 - 区域 1 中一个代理，区域 2 中一个代理，然后区域 3 中一个代理。

```azurecli-interactive
az group create --name myResourceGroup --location eastus2

az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --generate-ssh-keys \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --node-count 3 \
    --zones 1 2 3
```

创建 AKS 群集需要几分钟时间。

确定新节点所属的区域时，给定的 AKS 节点池将使用[由基础 Azure 虚拟机规模集提供的最佳区域均衡][vmss-zone-balancing]。 如果每个区域中的 VM 数相同或与规模集的所有其他区域中的 VM 数只相差 +\- 1，则可以认为给定 AKS 节点池“已实现均衡”。

## <a name="verify-node-distribution-across-zones"></a>验证跨区域的节点分布

群集准备就绪后，请列出规模集中的代理节点，以查看它们所部署到的可用性区域。

首先，使用 [az aks get-credentials][az-aks-get-credentials] 命令获取 AKS 群集凭据：

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

接下来，使用 [kubectl describe][kubectl-describe] 命令来列出群集中的节点。 对 failure-domain.beta.kubernetes.io/zone 值进行筛选，如以下示例所示：

```console
kubectl describe nodes | grep -e "Name:" -e "failure-domain.beta.kubernetes.io/zone"
```

以下示例输出显示了在指定地区和可用性区域之间分布的三个节点，如第一个可用性区域的 eastus2-1 和第二个可用性区域的 eastus2-2：

```console
Name:       aks-nodepool1-28993262-vmss000000
            failure-domain.beta.kubernetes.io/zone=eastus2-1
Name:       aks-nodepool1-28993262-vmss000001
            failure-domain.beta.kubernetes.io/zone=eastus2-2
Name:       aks-nodepool1-28993262-vmss000002
            failure-domain.beta.kubernetes.io/zone=eastus2-3
```

向代理池添加其他节点时，Azure 平台会自动在指定的可用性区域内分发基础 VM。

请注意，在新的 Kubernetes 版本（1.17.0 及更高版本）中，除了已弃用的 `failure-domain.beta.kubernetes.io/zone` 之外，AKS 还使用新标签 `topology.kubernetes.io/zone`。

## <a name="verify-pod-distribution-across-zones"></a>验证跨区域的 pod 分布

如[已知标签、注释和排斥][kubectl-well_known_labels]中所述，Kubernetes 使用 `failure-domain.beta.kubernetes.io/zone` 标签在可用的不同区域中自动分发复制控制器或服务中的 pod。 为了对此进行测试，可以从 3 到 5 个节点中纵向扩展群集，以验证是否已正确分布 pod：

```azurecli-interactive
az aks scale \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 5
```

当缩放操作在几分钟后完成时，命令 `kubectl describe nodes | grep -e "Name:" -e "failure-domain.beta.kubernetes.io/zone"` 应显示类似于以下示例的输出：

```console
Name:       aks-nodepool1-28993262-vmss000000
            failure-domain.beta.kubernetes.io/zone=eastus2-1
Name:       aks-nodepool1-28993262-vmss000001
            failure-domain.beta.kubernetes.io/zone=eastus2-2
Name:       aks-nodepool1-28993262-vmss000002
            failure-domain.beta.kubernetes.io/zone=eastus2-3
Name:       aks-nodepool1-28993262-vmss000003
            failure-domain.beta.kubernetes.io/zone=eastus2-1
Name:       aks-nodepool1-28993262-vmss000004
            failure-domain.beta.kubernetes.io/zone=eastus2-2
```

现在，区域 1 和 2 中有两个额外的节点。 你可以部署包含三个副本的应用程序。 我们以 NGINX 为例：

```console
kubectl run nginx --image=nginx --replicas=3
```

通过查看运行 pod 的节点，可以看到 pod 在与三个不同的可用性区域相对应的节点上运行。 例如，使用命令 `kubectl describe pod | grep -e "^Name:" -e "^Node:"` 将获得类似于以下内容的输出：

```console
Name:         nginx-6db489d4b7-ktdwg
Node:         aks-nodepool1-28993262-vmss000000/10.240.0.4
Name:         nginx-6db489d4b7-v7zvj
Node:         aks-nodepool1-28993262-vmss000002/10.240.0.6
Name:         nginx-6db489d4b7-xz6wj
Node:         aks-nodepool1-28993262-vmss000004/10.240.0.8
```

从前面的输出中可以看到，第一个 pod 在节点 0 上运行，该节点位于可用性区域 `eastus2-1` 中。 第二个 pod 在节点 2 上运行（该节点对应 `eastus2-3`），第三个 pod 则在 `eastus2-2` 的节点 4 上运行。 如果没有任何其他配置，Kubernetes 将在所有三个可用性区域中正确分配 pod。

## <a name="next-steps"></a>后续步骤

本文详细介绍了如何创建使用可用性区域的 AKS 群集。 有关高可用性群集的更多注意事项，请参阅 [AKS 中业务连续性和灾难恢复的最佳做法][best-practices-bc-dr]。

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-overview]: ../availability-zones/az-overview.md
[best-practices-bc-dr]: operator-best-practices-multi-region.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[standard-lb-limitations]: load-balancer-standard.md#limitations
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-aks-nodepool-add]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-add
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[vmss-zone-balancing]: ../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md#zone-balancing

<!-- LINKS - external -->
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-well_known_labels]: https://kubernetes.io/docs/reference/kubernetes-api/labels-annotations-taints/
