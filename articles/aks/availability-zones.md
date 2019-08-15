---
title: 使用 Azure Kubernetes 服务中的可用性区域 (AKS)
description: 了解如何创建在 Azure Kubernetes Service (AKS) 中跨可用性区域分发节点的群集
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 06/24/2019
ms.author: mlearned
ms.openlocfilehash: 4c2058072df4fcb068257c3e265dfe365c6d7e65
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/15/2019
ms.locfileid: "69033148"
---
# <a name="preview---create-an-azure-kubernetes-service-aks-cluster-that-uses-availability-zones"></a>预览-创建使用可用性区域的 Azure Kubernetes 服务 (AKS) 群集

Azure Kubernetes Service (AKS) 群集跨底层 Azure 计算基础结构的逻辑部分分配节点和存储等资源。 此部署模型确保节点在单个 Azure 数据中心内跨单独的更新域和容错域运行。 使用此默认行为部署的 AKS 群集提供高级别的可用性, 以防止出现硬件故障或计划内维护事件。

若要为应用程序提供更高级别的可用性, 可以在可用性区域之间分布 AKS 群集。 这些区域是给定区域中的物理上独立的数据中心。 当群集组件分布在多个区域时, AKS 群集能够容忍其中某个区域发生故障。 即使整个数据中心出现问题, 你的应用程序和管理操作仍将继续可用。

本文介绍如何创建 AKS 群集, 以及如何在可用性区域之间分发节点组件。 此功能目前处于预览状态。

> [!IMPORTANT]
> AKS 预览功能是可选的自助服务。 预览按 "原样" 提供, 并从服务级别协议和有限担保中排除。 AKS 预览版是以最大努力为基础的客户支持部分覆盖的。 因此, 这些功能并不用于生产。 有关其他信息, 请参阅以下支持文章:
>
> * [AKS 支持策略][aks-support-policies]
> * [Azure 支持常见问题][aks-faq]

## <a name="before-you-begin"></a>开始之前

需要安装并配置 Azure CLI 版本2.0.66 或更高版本。 运行  `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅 [安装 Azure CLI][install-azure-cli]。

### <a name="install-aks-preview-cli-extension"></a>安装 aks-preview CLI 扩展

若要创建使用可用性区域的 AKS 群集, 需要*AKS* CLI 扩展版本0.4.1 或更高版本。 使用[az extension add][az-extension-add]命令安装*aks-preview* Azure CLI 扩展, 然后使用[az extension update][az-extension-update]命令检查是否有任何可用更新:

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-feature-flags-for-your-subscription"></a>注册订阅的功能标志

若要创建可用性区域的 AKS 群集, 请先在订阅上启用一些功能标志。 群集使用虚拟机规模集来管理 Kubernetes 节点的部署和配置。 还需要 Azure 负载均衡器的*标准*SKU, 为网络组件提供可复原性, 以将流量路由到群集。 使用[az feature register][az-feature-register]命令注册*AvailabilityZonePreview*、 *AKSAzureStandardLoadBalancer*和*VMSSPreview*功能标志, 如以下示例中所示:

> [!CAUTION]
> 在订阅上注册功能时, 当前无法注册该功能。 启用某些预览功能后, 默认值可用于在订阅中创建的所有 AKS 群集。 不要对生产订阅启用预览功能。 使用单独的订阅来测试预览功能并收集反馈。

```azurecli-interactive
az feature register --name AvailabilityZonePreview --namespace Microsoft.ContainerService
az feature register --name AKSAzureStandardLoadBalancer --namespace Microsoft.ContainerService
az feature register --name VMSSPreview --namespace Microsoft.ContainerService
```

状态显示为“已注册”需要几分钟时间。 您可以使用[az feature list][az-feature-list]命令检查注册状态:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AvailabilityZonePreview')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKSAzureStandardLoadBalancer')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/VMSSPreview')].{Name:name,State:properties.state}"
```

准备就绪后, 请使用[az provider register][az-provider-register]命令刷新*ContainerService*资源提供程序的注册:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="limitations-and-region-availability"></a>限制和区域可用性

目前可以使用以下区域中的可用性区域创建 AKS 群集:

* 美国东部 2
* 北欧
* 东南亚
* 西欧
* 美国西部 2

使用可用性区域创建 AKS 群集时, 以下限制适用:

* 创建群集时, 只能启用可用性区域。
* 创建群集后, 无法更新可用性区域设置。 还不能更新现有的非可用性区域群集来使用可用性区域。
* 创建 AKS 群集后, 无法对其禁用可用性区域。
* 所选的节点大小 (VM SKU) 必须可在所有可用性区域中使用。
* 启用了可用性区域的群集要求使用 Azure 标准负载均衡器跨区域分发。
* 若要部署标准负载均衡器, 必须使用 Kubernetes 版本1.13.5 或更高版本。

使用可用性区域的 AKS 群集必须使用 Azure 负载均衡器*标准*SKU。 Azure 负载均衡器的默认*基本*SKU 不支持跨可用性区域的分发。 有关标准负载均衡器的详细信息和限制, 请参阅[Azure 负载均衡器标准 SKU 预览限制][standard-lb-limitations]。

### <a name="azure-disks-limitations"></a>Azure 磁盘限制

使用 Azure 托管磁盘的卷当前不是区域资源。 在不同区域中, 从其原始区域重新计划的 pod 无法重新连接以前的磁盘。 建议运行无状态工作负载, 这些工作负载不需要持久性存储, 这可能会跨区域性问题。

如果必须运行有状态工作负荷, 请在 pod 规范中使用 taints 和 tolerations, 告知 Kubernetes 计划程序在磁盘所在的同一区域中创建 pod。 另外, 还可以使用基于网络的存储 (如 Azure 文件), 因为它们在区域之间进行了安排。

## <a name="overview-of-availability-zones-for-aks-clusters"></a>AKS 群集可用性区域概述

可用性区域是一个高可用性产品/服务，在数据中心发生故障时可以保护应用程序和数据。 区域是 Azure 区域内的唯一物理位置。 每个区域由一个或多个数据中心组成，这些数据中心配置了独立电源、冷却和网络。 为确保能够进行复原，所有已启用的区域中必须至少有三个单独的区域。 区域中可用性区域的物理隔离可以在发生数据中心故障的情况下保护应用程序和数据。 区域冗余服务可跨可用性区域复制应用程序和数据，以防范单点故障。

有关详细信息, 请参阅[什么是 Azure 中的可用性区域？][az-overview]。

使用可用性区域部署的 AKS 群集可以在单个区域内的多个区域之间分布节点。 例如, *美国东部 2* 区域的群集可以在*美国东部 2*的所有三个可用性区域中创建节点。 这种 AKS 群集资源的分布可提高群集的可用性, 因为它们可以在特定区域发生故障时进行恢复。

![跨可用性区域的 AKS 节点分布](media/availability-zones/aks-availability-zones.png)

发生区域故障时, 可以手动或使用群集自动缩放程序重新平衡节点。 如果单个区域变得不可用, 应用程序将继续运行。

## <a name="create-an-aks-cluster-across-availability-zones"></a>跨可用性区域创建 AKS 群集

使用[az aks create][az-aks-create]命令创建群集时, 该`--node-zones`参数定义要将代理节点部署到的区域。 当你创建指定参数的`--node-zones`群集时, 群集的 AKS 控制平面组件还会在最高可用配置中跨区域分布。

如果在创建 AKS 群集时未为默认代理池定义任何区域, 则群集的 AKS 控制平面组件将不使用可用性区域。 你可以使用[az AKS nodepool add][az-aks-nodepool-add]命令添加更多节点池 (目前在 AKS 中为预览版), `--node-zones`并为这些新代理节点指定, 但控制平面组件仍在没有可用性区域识别的情况下。 部署节点池或 AKS 控制平面组件后, 无法更改其区域感知。

以下示例在名为*myResourceGroup*的资源组中创建名为*myAKSCluster*的 AKS 群集。 总共创建*3*个节点-一个区域*1*中的一个代理, 一个在*2*中, 另一个在*3*中。 AKS 控制平面组件还在最高可用配置中跨区域分布, 因为它们被定义为群集创建过程的一部分。

```azurecli-interactive
az group create --name myResourceGroup --location eastus2

az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --generate-ssh-keys \
    --enable-vmss \
    --load-balancer-sku standard \
    --node-count 3 \
    --node-zones 1 2 3
```

创建 AKS 群集需要几分钟时间。

## <a name="verify-node-distribution-across-zones"></a>验证跨区域的节点分布

群集准备就绪后, 请列出规模集中的代理节点, 以查看它们所部署到的可用性区域。

首先, 使用[az AKS get 凭据][az-aks-get-credentials]命令获取 AKS 群集凭据:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

接下来, 使用[kubectl 说明][kubectl-describe]命令列出群集中的节点。 按以下示例所示筛选*failure-domain.beta.kubernetes.io/zone*值:

```console
kubectl describe nodes | grep -e "Name:" -e "failure-domain.beta.kubernetes.io/zone"
```

下面的示例输出显示了在指定的区域和可用性区域之间分布的三个节点, 如第一个可用性区域的*eastus2-1*和第二个可用性区域的*eastus2-2* :

```console
Name:       aks-nodepool1-28993262-vmss000000
            failure-domain.beta.kubernetes.io/zone=eastus2-1
Name:       aks-nodepool1-28993262-vmss000001
            failure-domain.beta.kubernetes.io/zone=eastus2-2
Name:       aks-nodepool1-28993262-vmss000002
            failure-domain.beta.kubernetes.io/zone=eastus2-3
```

向代理池添加其他节点时, Azure 平台会自动在指定的可用性区域内分发基础 Vm。

## <a name="next-steps"></a>后续步骤

本文详细介绍了如何创建使用可用性区域的 AKS 群集。 有关高可用性群集的更多注意事项, 请参阅[AKS 中业务连续性和灾难恢复的最佳实践][best-practices-bc-dr]。

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
