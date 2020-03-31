---
title: 预览 - 将点节点池添加到 Azure 库伯奈斯服务 （AKS） 群集
description: 了解如何将点节点池添加到 Azure 库伯奈斯服务 （AKS） 群集。
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: article
ms.date: 02/25/2020
ms.author: zarhoads
ms.openlocfilehash: 466ad7c88547b6676ba0ae263b74d14059322f1c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77622044"
---
# <a name="preview---add-a-spot-node-pool-to-an-azure-kubernetes-service-aks-cluster"></a>预览 - 将点节点池添加到 Azure 库伯奈斯服务 （AKS） 群集

点节点池是由[现场虚拟机规模集][vmss-spot]支持的节点池。 将现场 VM 用于 AKS 群集的节点，使您能够在显著节省成本时利用 Azure 中的未利用容量。 可用未用容量的数量将因许多因素而异，包括节点大小、区域和一天中的时间。

部署点节点池时，Azure 将分配存在可用容量的点节点。 但是没有用于点节点的 SLA。 支持点节点池的点标缩放集部署在单个容错域中，并且不提供高可用性保证。 在 Azure 需要返回容量的任何时候，Azure 基础结构将驱逐点节点。

Spot 节点非常适合处理中断、早期终止或逐出的工作负载。 例如，批处理作业、开发和测试环境以及大型计算工作负载等工作负载可能是在现场节点池上安排的候选工作。

在本文中，您将辅助点节点池添加到现有的 Azure 库伯奈斯服务 （AKS） 群集。

本文假设读者基本了解 Kubernetes 和 Azure 负载均衡器的概念。 有关详细信息，请参阅 [Azure Kubernetes 服务 (AKS) 的 Kubernetes 核心概念][kubernetes-concepts]。

此功能目前处于预览状态。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="before-you-begin"></a>开始之前

创建群集以使用点节点池时，该群集还必须对节点池和*标准*SKU 负载均衡器使用虚拟机缩放集。 在创建群集以使用点节点池后，还必须添加其他节点池。 稍后的步骤将介绍添加其他节点池，但首先需要启用预览功能。

> [!IMPORTANT]
> AKS 预览功能是自助服务、选择加入功能。 提供这些项目是为了从我们的社区收集反馈和 bug。 在预览版中，这些功能不用于生产。 公共预览版中的功能属于"尽力而为"支持。 AKS 技术支持团队仅在工作时间提供太平洋时区 （PST） 的帮助。 有关详细信息，请参阅以下支持文章：
>
> * [AKS 支持策略][aks-support-policies]
> * [Azure 支持常见问题][aks-faq]

### <a name="register-spotpoolpreview-preview-feature"></a>注册专时池预览预览功能

要创建使用即点节点池的 AKS 群集，必须在订阅上启用*spotpool 预览*功能标志。 此功能在配置群集时提供一组最新的服务增强功能。

> [!CAUTION]
> 在订阅上注册功能时，当前无法取消注册该功能。 启用某些预览功能后，可能会将默认值用于订阅中创建的所有 AKS 群集。 不要在生产订阅上启用预览功能。 使用单独的订阅测试预览功能并收集反馈。

使用[az 要素寄存器][az-feature-register]命令注册*spotpool 预览*功能标志，如以下示例所示：

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "spotpoolpreview"
```

状态显示为“已注册”需要几分钟时间**。 可以使用 [az feature list][az-feature-list] 命令检查注册状态：

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/spotpoolpreview')].{Name:name,State:properties.state}"
```

准备就绪后，使用 [az provider register][az-provider-register] 命令刷新 Microsoft.ContainerService 资源提供程序的注册状态**：

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

### <a name="install-aks-preview-cli-extension"></a>安装 aks-preview CLI 扩展

要创建使用点节点池的 AKS 群集，您需要*aks 预览*CLI 扩展版本 0.4.32 或更高版本。 使用 [az extension add][az-extension-add] 命令安装 *aks-preview* Azure CLI 扩展，然后使用 [az extension update][az-extension-update] 命令检查是否有任何可用的更新：

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview
 
# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="limitations"></a>限制

当您使用点节点池创建和管理 AKS 群集时，以下限制适用：

* 点节点池不能是群集的默认节点池。 点节点池只能用于辅助池。
* 无法升级点节点池，因为点节点池不能保证警戒线和排空。 您必须用新的点节点池替换现有点节点池，以便执行升级 Kubernetes 版本等操作。 要替换点节点池，请使用不同版本的 Kubernetes 创建新的点节点池，等待其状态*为"就绪"，* 然后删除旧节点池。
* 不能同时升级控制平面和节点池。 您必须单独升级它们或删除点节点池，以便同时升级控制平面和剩余节点池。
* 点节点池必须使用虚拟机缩放集。
* 创建后，您不能更改 ScaleSet 优先级或 SpotMaxPrice。
* 设置 SpotMaxPrice 时，该值必须为 -1 或最多五位小数位的正值。
* 点节点池将具有*kubernetes.azure.com/scalesetpriority:spot*标签，污染*kubernetes.azure.com/scalesetpriority=spot:NoSchedule，* 系统窗格将具有抗亲和力。
* 您必须添加[相应的容忍，][spot-toleration]以在点节点池上计划工作负载。

## <a name="add-a-spot-node-pool-to-an-aks-cluster"></a>将点节点池添加到 AKS 群集

您必须将点节点池添加到启用了多个节点池的现有群集。 有关创建具有多个节点池的 AKS 群集的更多详细信息[，请在此处][use-multiple-node-pools]获取。

使用[az aks 节点池添加][az-aks-nodepool-add]创建节点池。
```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name spotnodepool \
    --priority Spot \
    --eviction-policy Delete \
    --spot-max-price -1 \
    --enable-cluster-autoscaler \
    --min-count 1 \
    --max-count 3 \
    --no-wait
```

默认情况下，在创建具有多个节点池的群集时，在 AKS 群集中创建*优先级*为 *"常规"* 的节点池。 上述命令将辅助节点池添加到具有*Spot**优先级*的现有 AKS 群集。 *Spot*的*优先级*使节点池成为点节点池。 驱逐*策略*参数设置为*删除在上面的*示例中，这是默认值。 将[逐出策略][eviction-policy]设置为 *"删除*"时，节点池的基础规模集中的节点在被逐出时将被删除。 您还可以将逐出策略设置为 *"去分配*"。 将逐出策略设置为 *"去分配"* 时，基础比例集中的节点在逐出时设置为停止分配状态。 停止处理位置状态的节点会计入计算配额，并可能导致群集缩放或升级问题。 只能在节点池创建期间设置*优先级*和*逐出策略*值。 这些值以后无法更新。

该命令还启用[群集自动缩放器][cluster-autoscaler]，建议该自动缩放器与点节点池一起使用。 根据群集中运行的工作负载，群集自动缩放器可扩展并缩小节点池中的节点数。 对于点节点池，如果仍需要额外的节点，群集自动缩放器将在逐出后放大节点数。 如果更改节点池可以具有的最大节点数，则还需要调整与群集自动缩放器关联的`maxCount`值。 如果不使用群集自动缩放器，在逐出时，即期池最终将减少到零，并且需要手动操作才能接收任何其他点节点。

> [!Important]
> 仅计划可处理中断的现场节点池上的工作负载，例如批处理作业和测试环境。 建议您在点节点池上设置[污点和节要][taints-tolerations]，以确保仅将只能处理节点逐出的工作负载安排在现场节点池上。 例如，上述命令 ny 默认添加*kubernetes.azure.com/scalesetpriority=spot:NoSchedule*的污点，因此仅在此节点上计划具有相应容忍的 pod。

## <a name="verify-the-spot-node-pool"></a>验证点节点池

要验证节点池已添加为点节点池，请进行以下检查：

```azurecli
az aks nodepool show --resource-group myResourceGroup --cluster-name myAKSCluster --name spotnodepool
```

确认*比例设置优先级*是*Spot*。

要安排在点节点上运行的 pod，请添加与应用于点节点的污点对应的容忍。 下面的示例显示了 yaml 文件的一部分，该文件定义了对应于上一步中使用的*kubernetes.azure.com/scalesetpriority=spot:NoSchedule*污点的容忍。

```yaml
spec:
  containers:
  - name: spot-example
  tolerations:
  - key: "kubernetes.azure.com/scalesetpriority"
    operator: "Equal"
    value: "spot"
    effect: "NoSchedule"
   ...
```

部署具有此容忍的 pod 时，Kubernetes 可以成功将 pod 安排在应用了污点的节点上。

## <a name="max-price-for-a-spot-pool"></a>现货池的最高价格
[现货实例的定价是可变][pricing-spot]的，基于区域和 SKU。 有关详细信息，请参阅[Linux][pricing-linux]和[Windows][pricing-windows]的定价。

使用可变定价，您可以选择使用最多 5 个小数位设置最高价格（美元 （USD）。 例如，值*0.98765*将是每小时 0.98765 美元的最高价格。 如果将最高价格设置为 *-1，* 则实例不会根据价格被逐出。 实例的价格将是 Spot 的当前价格或标准实例的价格，以较低者为准，只要有容量和配额可用。

## <a name="next-steps"></a>后续步骤

在本文中，您学习了如何将点节点池添加到 AKS 群集。 有关如何跨节点池控制 pod 的详细信息，请参阅[有关 AKS 中的高级计划程序功能的最佳做法][operator-best-practices-advanced-scheduler]。

<!-- LINKS - External -->
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/

<!-- LINKS - Internal -->
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-group-deploy-create]: /cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create
[az-aks-nodepool-add]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-add
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-template-deploy]: ../azure-resource-manager/templates/deploy-cli.md#deployment-scope
[cluster-autoscaler]: cluster-autoscaler.md
[eviction-policy]: ../virtual-machine-scale-sets/use-spot.md#eviction-policy
[kubernetes-concepts]: concepts-clusters-workloads.md
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[pricing-linux]: https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/
[pricing-spot]: ../virtual-machine-scale-sets/use-spot.md#pricing
[pricing-windows]: https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/windows/
[spot-toleration]: #verify-the-spot-node-pool
[taints-tolerations]: operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations
[use-multiple-node-pools]: use-multiple-node-pools.md
[vmss-spot]: ../virtual-machine-scale-sets/use-spot.md