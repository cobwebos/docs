---
title: 在 Azure 库伯奈斯服务 （AKS） 中使用可用性区域
description: 了解如何创建群集，该群集在 Azure 库伯奈斯服务 （AKS） 中跨可用性区域分发节点
services: container-service
ms.custom: fasttrack-edit
ms.topic: article
ms.date: 06/24/2019
ms.openlocfilehash: 5693d9e90de9ba68e7b76e0f2bd5b75141dbda71
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77596804"
---
# <a name="create-an-azure-kubernetes-service-aks-cluster-that-uses-availability-zones"></a>创建使用可用性区域的 Azure 库伯奈斯服务 （AKS） 群集

Azure 库伯奈斯服务 （AKS） 群集将资源（如节点和存储）分布在基础 Azure 计算基础结构的逻辑部分。 此部署模型可确保节点在单个 Azure 数据中心中的单独更新域和容错域之间运行。 使用此默认行为部署的 AKS 群集提供了高级别的可用性，以防止硬件故障或计划维护事件。

为了向应用程序提供更高级别的可用性，AKS 群集可以跨可用性区域分发。 这些区域是给定区域内的物理独立的数据中心。 当群集组件分布在多个区域时，AKS 群集能够容忍其中一个区域中的故障。 即使整个数据中心都有问题，您的应用程序和管理操作仍可用。

本文介绍如何创建 AKS 群集并跨可用性区域分发节点组件。

## <a name="before-you-begin"></a>开始之前

需要安装并配置 Azure CLI 2.0.76 或更高版本。 运行  `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅 [安装 Azure CLI][install-azure-cli]。

## <a name="limitations-and-region-availability"></a>限制和地区可用性

AKS 群集当前可以使用以下区域的可用性区域创建：

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

* 只能在创建群集时启用可用性区域。
* 创建群集后无法更新可用性区域设置。 您也不能更新现有的非可用区域群集以使用可用性区域。
* 创建 AKS 群集后，无法禁用其可用性区域。
* 选定的节点大小 （VM SKU） 必须在所有可用性区域中可用。
* 启用了可用性区域的群集需要使用 Azure 标准负载均衡器跨区域进行分发。
* 您必须使用 Kubernetes 版本 1.13.5 或更高版本才能部署标准负载均衡器。

使用可用性区域的 AKS 群集必须使用 Azure 负载均衡器*标准*SKU，这是负载均衡器类型的默认值。 此负载均衡器类型只能在群集创建时定义。 有关详细信息和标准负载均衡器的限制，请参阅[Azure 负载均衡器标准 SKU 限制][standard-lb-limitations]。

### <a name="azure-disks-limitations"></a>Azure 磁盘限制

使用 Azure 托管磁盘的卷当前不是区资源。 在与其原始区域不同的区域中重新计划的 Pod 无法重新连接其以前的磁盘。 建议运行不需要可能遇到地区问题的持久存储的无状态工作负载。

如果必须运行有状态的工作负载，请在窗格规范中使用污点和托架，告诉 Kubernetes 调度程序在磁盘相同的区域中创建 pod。 或者，使用基于网络的存储，如 Azure 文件，这些文件可以在区域之间安排时附加到窗格。

## <a name="overview-of-availability-zones-for-aks-clusters"></a>AKS 群集的可用性区域概述

可用性区域是一种高可用性产品，可保护您的应用程序和数据免受数据中心故障的影响。 区域是 Azure 区域中唯一的物理位置。 每个区域由一个或多个数据中心组成，这些数据中心配置了独立电源、冷却和网络。 为确保能够进行复原，所有已启用的区域中必须至少有三个单独的区域。 数据中心发生故障时，区域中的可用性区域的物理隔离可保护应用程序和数据。 区域冗余服务跨可用性区域复制应用程序和数据，以防止单点故障。

有关详细信息，请参阅[Azure 中有哪些可用性区域？][az-overview]

使用可用性区域部署的 AKS 群集可以在单个区域内跨多个区域分发节点。 例如， *美国东部 2* 区域中的群集可以在*东美国 2*中的所有三个可用性区域中创建节点。 AKS 群集资源的此分布提高了群集可用性，因为它们能够抵御特定区域的故障。

![跨可用性区域的 AKS 节点分布](media/availability-zones/aks-availability-zones.png)

在区域中断中，可以手动重新平衡节点或使用群集自动缩放器。 如果单个区域不可用，则应用程序将继续运行。

## <a name="create-an-aks-cluster-across-availability-zones"></a>跨可用性区域创建 AKS 群集

使用[az aks 创建][az-aks-create]命令创建群集时，`--zones`参数将定义部署到哪些区域代理节点。 在群集创建时定义参数时，`--zones`群集的 AKS 控制平面组件也会以最高可用配置跨区域分布。

如果在创建 AKS 群集时未为默认代理池定义任何区域，则群集的 AKS 控制平面组件将不会使用可用性区域。 您可以使用[az aks 节点池添加][az-aks-nodepool-add]命令添加其他节点池，并为`--zones`这些新节点指定，但控制平面组件仍然没有可用性区域感知。 部署节点池或 AKS 控制平面组件后，无法更改它们的区域感知。

下面的示例在名为*myResourceGroup*的资源组中创建名为*myAKSCluster*的 AKS 群集。 总共创建了*3*个节点 - 区域*1*中的一个代理 *，2*个代理中的 1 个，然后创建一个在*3*个节点中。 AKS 控制平面组件也分布在区域之间，配置最高，因为它们被定义为群集创建过程的一部分。

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

群集准备就绪后，在规模集中列出代理节点，以查看它们部署在哪些可用性区域。

首先，使用[az aks 获取凭据][az-aks-get-credentials]命令获取 AKS 群集凭据：

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

接下来，使用[kubectl 描述][kubectl-describe]命令列出群集中的节点。 筛选*failure-domain.beta.kubernetes.io/zone*值，如以下示例所示：

```console
kubectl describe nodes | grep -e "Name:" -e "failure-domain.beta.kubernetes.io/zone"
```

下面的示例输出显示了分布在指定区域和可用性区域的三个节点，例如第一个可用性区的*Eastus2-1*和第二个可用性区域的*Eastus2-2：*

```console
Name:       aks-nodepool1-28993262-vmss000000
            failure-domain.beta.kubernetes.io/zone=eastus2-1
Name:       aks-nodepool1-28993262-vmss000001
            failure-domain.beta.kubernetes.io/zone=eastus2-2
Name:       aks-nodepool1-28993262-vmss000002
            failure-domain.beta.kubernetes.io/zone=eastus2-3
```

将其他节点添加到代理池时，Azure 平台会自动跨指定的可用性区域分发基础 VM。

请注意，在较新的 Kubernetes 版本（1.17.0 及更高版本）中，AKS 除了弃用之外，`failure-domain.beta.kubernetes.io/zone`还使用较新的标签`topology.kubernetes.io/zone`。

## <a name="verify-pod-distribution-across-zones"></a>验证跨区域的窗格分布

正如在[已知标签、注释和污点][kubectl-well_known_labels]中所记录的那样，Kubernetes 使用`failure-domain.beta.kubernetes.io/zone`该标签在复制控制器或服务中自动分发跨可用区域的窗格。 为了测试这一点，您可以将群集从 3 个节点扩展到 5 个节点，以验证正确的 pod 展开：

```azurecli-interactive
az aks scale \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 5
```

当缩放操作在几分钟后完成时，该命令`kubectl describe nodes | grep -e "Name:" -e "failure-domain.beta.kubernetes.io/zone"`应提供类似于此示例的输出：

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

如您所见，我们现在在区域 1 和 2 中增加了两个节点。 可以部署由三个副本组成的应用程序。 我们将使用 NGINX 作为示例：

```console
kubectl run nginx --image=nginx --replicas=3
```

如果验证窗格运行的节点，您将看到该窗格在对应于三个不同可用性区域的窗格上运行。 例如，使用 该`kubectl describe pod | grep -e "^Name:" -e "^Node:"`命令，您将获得类似于此的输出：

```console
Name:         nginx-6db489d4b7-ktdwg
Node:         aks-nodepool1-28993262-vmss000000/10.240.0.4
Name:         nginx-6db489d4b7-v7zvj
Node:         aks-nodepool1-28993262-vmss000002/10.240.0.6
Name:         nginx-6db489d4b7-xz6wj
Node:         aks-nodepool1-28993262-vmss000004/10.240.0.8
```

从前面的输出中可以看出，第一个 Pod 正在位于可用性区域`eastus2-1`的节点 0 上运行。 第二个 pod 在节点 2 上运行`eastus2-3`，该节点 2 对应于 ，第`eastus2-2`3 个在节点 4 中运行。 在没有任何其他配置的情况下，Kubernetes 正在所有三个可用性区域中正确扩展窗格。

## <a name="next-steps"></a>后续步骤

本文详细介绍了如何创建使用可用性区域的 AKS 群集。 有关高可用群集的更多注意事项，请参阅[AKS 中有关业务连续性和灾难恢复的最佳做法][best-practices-bc-dr]。

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
