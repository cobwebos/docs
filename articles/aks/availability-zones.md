---
title: 使用 Azure Kubernetes Service （AKS）中的可用性区域
description: 了解如何创建在 Azure Kubernetes Service （AKS）中跨可用性区域分发节点的群集
services: container-service
ms.custom: fasttrack-edit
ms.topic: article
ms.date: 06/24/2019
ms.openlocfilehash: 5693d9e90de9ba68e7b76e0f2bd5b75141dbda71
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/25/2020
ms.locfileid: "77596804"
---
# <a name="create-an-azure-kubernetes-service-aks-cluster-that-uses-availability-zones"></a>创建使用可用性区域的 Azure Kubernetes 服务（AKS）群集

Azure Kubernetes Service （AKS）群集跨底层 Azure 计算基础结构的逻辑部分分配节点和存储等资源。 此部署模型确保节点在单个 Azure 数据中心内跨单独的更新域和容错域运行。 使用此默认行为部署的 AKS 群集提供高级别的可用性，以防止出现硬件故障或计划内维护事件。

若要为应用程序提供更高级别的可用性，可以在可用性区域之间分布 AKS 群集。 这些区域是给定区域中的物理上独立的数据中心。 当群集组件分布在多个区域时，AKS 群集能够容忍其中某个区域发生故障。 即使整个数据中心出现问题，你的应用程序和管理操作仍将继续可用。

本文介绍如何创建 AKS 群集，以及如何在可用性区域之间分发节点组件。

## <a name="before-you-begin"></a>开始之前

需要安装并配置 Azure CLI 版本2.0.76 或更高版本。 运行  `az --version` 即可查找版本。 如果需要安装或升级，请参阅 [安装 Azure CLI][install-azure-cli]。

## <a name="limitations-and-region-availability"></a>限制和区域可用性

目前可以使用以下区域中的可用性区域创建 AKS 群集：

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

* 创建群集时，只能启用可用性区域。
* 创建群集后，无法更新可用性区域设置。 还不能更新现有的非可用性区域群集来使用可用性区域。
* 创建 AKS 群集后，无法对其禁用可用性区域。
* 所选的节点大小（VM SKU）必须可在所有可用性区域中使用。
* 启用了可用性区域的群集要求使用 Azure 标准负载均衡器跨区域分发。
* 若要部署标准负载均衡器，必须使用 Kubernetes 版本1.13.5 或更高版本。

使用可用性区域的 AKS 群集必须使用 Azure 负载均衡器*标准*SKU，这是负载均衡器类型的默认值。 此负载均衡器类型仅可在创建群集时定义。 有关标准负载均衡器的详细信息和限制，请参阅[Azure 负载均衡器标准 SKU 限制][standard-lb-limitations]。

### <a name="azure-disks-limitations"></a>Azure 磁盘限制

使用 Azure 托管磁盘的卷当前不是区域资源。 在不同区域中，从其原始区域重新计划的 pod 无法重新连接以前的磁盘。 建议运行无状态工作负载，这些工作负载不需要持久性存储，这可能会跨区域性问题。

如果必须运行有状态工作负荷，请在 pod 规范中使用 taints 和 tolerations，告知 Kubernetes 计划程序在磁盘所在的同一区域中创建 pod。 另外，还可以使用基于网络的存储（如 Azure 文件），因为它们在区域之间进行了安排。

## <a name="overview-of-availability-zones-for-aks-clusters"></a>AKS 群集的可用性区域概述

可用性区域是一种高可用性产品，可保护应用程序和数据免受数据中心故障的影响。 区域是 Azure 区域内的唯一物理位置。 每个区域由一个或多个数据中心组成，这些数据中心配置了独立电源、冷却和网络。 为确保能够进行复原，所有已启用的区域中必须至少有三个单独的区域。 数据中心发生故障时，区域中的可用性区域的物理隔离可保护应用程序和数据。 区域冗余服务跨可用性区域复制应用程序和数据，防止单点故障。

有关详细信息，请参阅[什么是 Azure 中的可用性区域？][az-overview]。

使用可用性区域部署的 AKS 群集可以在单个区域内的多个区域之间分布节点。 例如， *美国东部 2* 区域中的群集可以在*美国东部 2*的所有三个可用性区域中创建节点。 这种 AKS 群集资源的分布可提高群集的可用性，因为它们可以在特定区域发生故障时进行恢复。

![跨可用性区域的 AKS 节点分布](media/availability-zones/aks-availability-zones.png)

发生区域故障时，可以手动或使用群集自动缩放程序重新平衡节点。 如果单个区域变得不可用，应用程序将继续运行。

## <a name="create-an-aks-cluster-across-availability-zones"></a>跨可用性区域创建 AKS 群集

当使用[az aks create][az-aks-create]命令创建群集时，`--zones` 参数定义了代理节点部署到的区域。 当你在创建群集时定义 `--zones` 参数时，群集的 AKS 控制平面组件还会在最高可用配置中跨区域分布。

如果在创建 AKS 群集时未为默认代理池定义任何区域，则群集的 AKS 控制平面组件将不使用可用性区域。 您可以使用[az aks nodepool add][az-aks-nodepool-add]命令添加更多节点池，并为这些新节点指定 `--zones`，但控制平面组件在没有可用性区域识别功能的情况下仍保持不变。 部署节点池或 AKS 控制平面组件后，无法更改其区域感知。

以下示例在名为*myResourceGroup*的资源组中创建名为*myAKSCluster*的 AKS 群集。 总共创建*3*个节点-一个区域*1*中的一个代理，一个在*2*中，另一个在*3*中。 AKS 控制平面组件还在最高可用配置中跨区域分布，因为它们被定义为群集创建过程的一部分。

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

## <a name="verify-node-distribution-across-zones"></a>验证跨区域的节点分布

群集准备就绪后，请列出规模集中的代理节点，以查看它们所部署到的可用性区域。

首先，使用[az AKS get 凭据][az-aks-get-credentials]命令获取 AKS 群集凭据：

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

接下来，使用[kubectl 说明][kubectl-describe]命令列出群集中的节点。 按以下示例所示筛选*failure-domain.beta.kubernetes.io/zone*值：

```console
kubectl describe nodes | grep -e "Name:" -e "failure-domain.beta.kubernetes.io/zone"
```

下面的示例输出显示了在指定的区域和可用性区域之间分布的三个节点，如第一个可用性区域的*eastus2-1*和第二个可用性区域的*eastus2-2* ：

```console
Name:       aks-nodepool1-28993262-vmss000000
            failure-domain.beta.kubernetes.io/zone=eastus2-1
Name:       aks-nodepool1-28993262-vmss000001
            failure-domain.beta.kubernetes.io/zone=eastus2-2
Name:       aks-nodepool1-28993262-vmss000002
            failure-domain.beta.kubernetes.io/zone=eastus2-3
```

向代理池添加其他节点时，Azure 平台会自动在指定的可用性区域内分发基础 Vm。

请注意，在较新的 Kubernetes 版本（1.17.0 及更高版本）中，AKS 除了使用已弃用的 `failure-domain.beta.kubernetes.io/zone`以外，还使用较新的 `topology.kubernetes.io/zone` 标签。

## <a name="verify-pod-distribution-across-zones"></a>验证跨区域的 pod 分布

如众所周知的[标签、注释和 Taints][kubectl-well_known_labels]中所述，Kubernetes 使用 `failure-domain.beta.kubernetes.io/zone` 标签自动在可用的不同区域内的复制控制器或服务中分配 pod。 为了对此进行测试，可以从3个节点中纵向扩展群集，以验证正确的 pod 传播：

```azurecli-interactive
az aks scale \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 5
```

当缩放操作在几分钟后完成后，命令 `kubectl describe nodes | grep -e "Name:" -e "failure-domain.beta.kubernetes.io/zone"` 应显示类似于以下示例的输出：

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

正如您所看到的，在区域1和2中现在有两个附加节点。 你可以部署包含三个副本的应用程序。 我们将使用 NGINX 作为示例：

```console
kubectl run nginx --image=nginx --replicas=3
```

如果验证你的 pod 正在其中运行的节点，你将看到该 pod 在对应于三个不同的可用性区域的盒上运行。 例如，使用命令 `kubectl describe pod | grep -e "^Name:" -e "^Node:"` 会获得类似于下面的输出：

```console
Name:         nginx-6db489d4b7-ktdwg
Node:         aks-nodepool1-28993262-vmss000000/10.240.0.4
Name:         nginx-6db489d4b7-v7zvj
Node:         aks-nodepool1-28993262-vmss000002/10.240.0.6
Name:         nginx-6db489d4b7-xz6wj
Node:         aks-nodepool1-28993262-vmss000004/10.240.0.8
```

从前面的输出中可以看到，第一个 pod 在节点0上运行，该节点位于可用性区域 `eastus2-1`中。 第二个 pod 在节点2上运行，该节点对应于 `eastus2-3`，而第三个盒在节点4中 `eastus2-2`。 如果没有任何其他配置，Kubernetes 将在所有三个可用性区域中正确地分配盒箱。

## <a name="next-steps"></a>后续步骤

本文详细介绍了如何创建使用可用性区域的 AKS 群集。 有关高可用性群集的更多注意事项，请参阅[AKS 中业务连续性和灾难恢复的最佳实践][best-practices-bc-dr]。

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

<!-- LINKS - external -->
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-well_known_labels]: https://kubernetes.io/docs/reference/kubernetes-api/labels-annotations-taints/
