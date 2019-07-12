---
title: 使用 Azure Kubernetes 服务 (AKS) 中的可用性区域
description: 了解如何创建在可用性区域中 Azure Kubernetes 服务 (AKS) 中分配节点的群集
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 06/24/2019
ms.author: iainfou
ms.openlocfilehash: 0f99386aa9eeb75a990507e383c32412fb39eceb
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67840675"
---
# <a name="preview---create-an-azure-kubernetes-service-aks-cluster-that-uses-availability-zones"></a>预览-创建使用可用性区域的 Azure Kubernetes 服务 (AKS) 群集

Azure Kubernetes 服务 (AKS) 群集分配资源，如计算基础结构的节点和跨逻辑部分的基础 Azure 存储。 这种部署模型可确保跨单独更新域和容错域在单个 Azure 数据中心中运行时节点。 随此默认行为一起部署 AKS 群集计划内维护事件或提供高级别的可用性，以针对硬件故障提供保护。

若要提供更高级别的到您的应用程序的可用性，可以跨可用性区域分发 AKS 群集。 这些区域是物理上独立的数据中心在给定区域内。 群集组件分布在多个区域中，在 AKS 群集时，能够承受一个这些区域中的失败。 你的应用程序和管理操作继续为可用，即使整个数据中心有问题。

本文介绍如何创建 AKS 群集，并将节点组件分布在可用性区域。 此功能目前处于预览状态。

> [!IMPORTANT]
> AKS 预览版功能是自助服务的选择加入。 提供这些项目是为了从我们的社区收集反馈和 bug。 在预览版中，这些功能不是用于生产环境中使用。 公共预览版中的功能属于最大努力支持。 AKS 技术支持团队的协助营业时间太平洋时区 （太平洋标准时间） 仅将提供。 有关其他信息，请参阅以下支持文章：
>
> * [AKS 支持策略][aks-support-policies]
> * [Azure 支持常见问题][aks-faq]

## <a name="before-you-begin"></a>开始之前

你需要 Azure CLI 版本 2.0.66 或更高版本安装和配置。 运行  `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅 [安装 Azure CLI][install-azure-cli]。

### <a name="install-aks-preview-cli-extension"></a>安装 aks-preview CLI 扩展

若要创建使用可用性区域的 AKS 群集，需要*aks 预览版*CLI 扩展版本 0.4.1 或更高版本。 安装*aks 预览版*Azure CLI 扩展使用[az 扩展添加][az-extension-add]command, then check for any available updates using the [az extension update][az-extension-update]命令::

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-feature-flags-for-your-subscription"></a>注册你的订阅的功能标志

若要创建 AKS 群集的可用性区域，请先启用你的订阅上的某些功能标志。 群集使用的虚拟机规模集来管理部署和 Kubernetes 节点的配置。 *标准*的 Azure 负载均衡器 SKU 也需要进入群集将流量路由到的网络组件提供复原能力。 注册*AvailabilityZonePreview*， *AKSAzureStandardLoadBalancer*，并*VMSSPreview*功能标志使用[az 功能注册][az-feature-register]命令，在下面的示例所示：

> [!CAUTION]
> 注册时对某一订阅功能，目前你无法取消注册该功能。 启用某些预览功能后，可能会对所有 AKS 群集，然后在订阅中创建使用默认值。 不要启用预览功能在生产订阅。 使用单独的订阅来测试预览功能和收集反馈。

```azurecli-interactive
az feature register --name AvailabilityZonePreview --namespace Microsoft.ContainerService
az feature register --name AKSAzureStandardLoadBalancer --namespace Microsoft.ContainerService
az feature register --name VMSSPreview --namespace Microsoft.ContainerService
```

状态显示为“已注册”需要几分钟时间  。 您可以检查注册状态使用[az 功能列表][az-feature-list]命令：

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AvailabilityZonePreview')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKSAzureStandardLoadBalancer')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/VMSSPreview')].{Name:name,State:properties.state}"
```

准备就绪后，刷新的注册*Microsoft.ContainerService*使用的资源提供程序[az provider register][az-provider-register]命令：

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="limitations-and-region-availability"></a>限制和区域可用性

当前可在以下区域中使用可用性区域创建 AKS 群集：

* 美国东部 2
* 北欧
* 东南亚
* 西欧
* 美国西部 2

创建使用可用性区域的 AKS 群集时，应考虑以下限制：

* 只有在创建群集时，只能启用可用性区域。
* 创建群集后，无法更新可用性区域设置。 您还不能更新现有的非可用性区域群集，以使用可用性区域。
* 创建它后，不能禁用 AKS 群集的可用性区域。
* 所选的节点大小 (VM SKU) 必须跨所有可用性区域可用。
* 使用可用性区域启用需要群集的 Azure 标准负载均衡器使用的分发跨区域。
* 必须使用 Kubernetes 版本 1.13.5 或更高版本，若要部署标准负载均衡器。

使用可用性区域的 AKS 群集必须使用 Azure 负载均衡器*标准*SKU。 默认值*基本*的 Azure 负载均衡器 SKU 不支持跨可用性区域分发。 有关详细信息和限制的标准负载均衡器，请参阅[Azure 负载均衡器标准 SKU 预览版限制][standard-lb-limitations]。

### <a name="azure-disks-limitations"></a>Azure 磁盘限制

卷使用 Azure 托管磁盘目前不局域性资源。 重新安排从其原始区域中不同的区域中的 pod 不能将其上一个磁盘重新连接。 建议运行不需要永久存储，可能会遇到局域性问题的无状态工作负荷。

如果您必须运行有状态工作负荷，使用 taints 和 tolerations pod 规范中告知 Kubernetes 计划程序在与你的磁盘位于相同区域中创建的 pod。 或者，使用基于网络的存储，如可以附加到 pod 中，因为它们在区域之间计划的 Azure 文件。

## <a name="overview-of-availability-zones-for-aks-clusters"></a>群集的适用于 AKS 的可用性区域概述

可用性区域是一个高可用性产品/服务，在数据中心发生故障时可以保护应用程序和数据。 区域是 Azure 区域中的唯一物理位置。 每个区域由一个或多个数据中心组成，这些数据中心配置了独立电源、冷却和网络。 为确保能够进行复原，所有已启用的区域中必须至少有三个单独的区域。 区域中可用性区域的物理隔离可以在发生数据中心故障的情况下保护应用程序和数据。 区域冗余服务可跨可用性区域复制应用程序和数据，以防范单点故障。

有关详细信息，请参阅[什么是 Azure 中的可用性区域？][az-overview]。

使用可用性区域部署 AKS 群集可以跨单个区域内的多个区域分配节点。 例如，在中的群集 *美国东部 2* 区域可以在所有三个可用性区域中创建节点*美国东部 2*。 此分发版 AKS 群集资源可以提高群集的可用性，因为它们能够灵活应对故障的特定区域。

![跨可用性区域的 AKS 节点分布](media/availability-zones/aks-availability-zones.png)

在区域服务中断时，节点可以重新平衡手动或使用群集自动缩放程序。 如果一个区域变得不可用，您的应用程序将继续运行。

## <a name="create-an-aks-cluster-across-availability-zones"></a>跨可用性区域中创建 AKS 群集

当创建群集使用[az aks 创建][az-aks-create]命令，`--node-zones`参数定义部署在哪些区域代理节点。 创建一个群集指定时，群集 AKS 控制平面组件也可分布在最高可用配置中的区域`--node-zones`参数。

如果未定义任何区域的默认代理池创建 AKS 群集时，你的群集的 AKS 控制平面组件不会使用可用性区域。 您可以添加其他节点池 （目前以预览版在 AKS 中） 使用[添加 az aks nodepool][az-aks-nodepool-add]命令并指定`--node-zones`对于这些新的代理节点，但控制平面组件保留为不可用性区域的情况下要知道。 不能更改区域意识的节点池或 AKS 控制平面组件后其部署。

下面的示例创建名为 AKS 群集*myAKSCluster*资源组中名为*myResourceGroup*。 总共*3*创建节点的区域中的一个代理*1*、 一个中*2*，然后一个在*3*。 由于它们定义为群集的一部分创建的进程，AKS 控制平面组件还会分布在最高可用配置中的区域。

```azurecli-interactive
az group create --name myResourceGroup --location eastus2

az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --kubernetes-version 1.13.5 \
    --generate-ssh-keys \
    --enable-vmss \
    --load-balancer-sku standard \
    --node-count 3 \
    --node-zones 1 2 3
```

创建 AKS 群集需要几分钟时间。

## <a name="verify-node-distribution-across-zones"></a>跨区域验证节点分布

群集准备就绪后，列出规模集以查看其部署在哪些可用性区域中的代理节点。

首先，获取使用 AKS 群集凭据[az aks get-credentials 来获取凭据][az-aks-get-credentials]命令：

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

接下来，使用[kubectl 描述][kubectl-describe]命令以列出群集中的节点。 筛选*failure-domain.beta.kubernetes.io/zone*值如以下示例中所示：

```console
kubectl describe nodes | grep -e "Name:" -e "failure-domain.beta.kubernetes.io/zone"
```

以下示例输出显示三个节点分布在指定的区域和可用性区域，如*eastus2 1*的第一个可用性区域和*eastus2 2*第二个可用性区域：

```console
Name:       aks-nodepool1-28993262-vmss000000
            failure-domain.beta.kubernetes.io/zone=eastus2-1
Name:       aks-nodepool1-28993262-vmss000001
            failure-domain.beta.kubernetes.io/zone=eastus2-2
Name:       aks-nodepool1-28993262-vmss000002
            failure-domain.beta.kubernetes.io/zone=eastus2-3
```

其他节点添加到代理池时，Azure 平台会自动将基础 Vm 分散指定的可用性区域。

## <a name="next-steps"></a>后续步骤

本文详细介绍如何创建使用可用性区域的 AKS 群集。 高度可用群集的更多注意事项，请参阅[在 AKS 中的业务连续性和灾难恢复的最佳做法][best-practices-bc-dr]。

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
