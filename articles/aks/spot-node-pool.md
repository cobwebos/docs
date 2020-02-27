---
title: 预览-将专色节点池添加到 Azure Kubernetes 服务（AKS）群集
description: 了解如何向 Azure Kubernetes 服务（AKS）群集添加一个专色节点池。
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: article
ms.date: 02/25/2020
ms.author: zarhoads
ms.openlocfilehash: 466ad7c88547b6676ba0ae263b74d14059322f1c
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/26/2020
ms.locfileid: "77622044"
---
# <a name="preview---add-a-spot-node-pool-to-an-azure-kubernetes-service-aks-cluster"></a>预览-将专色节点池添加到 Azure Kubernetes 服务（AKS）群集

点节点池是由[点虚拟机规模集][vmss-spot]支持的节点池。 通过将点 Vm 用于 AKS 群集，可以在 Azure 中充分利用未利用容量，同时节省大量成本。 可用的未利用容量取决于许多因素，包括节点大小、区域和当天的时间。

部署点节点池时，如果有可用的容量，Azure 将分配点节点。 但对于专色节点没有 SLA。 用于支持点节点池的位置规模集部署在单个容错域中，不提供高可用性保证。 Azure 在 Azure 基础结构将逐出点节点时，可以随时将其恢复。

专色节点非常适用于可处理中断、提前终止或逐出的工作负荷。 例如，工作负荷（如批处理作业、开发和测试环境以及大型计算工作负荷）可能是计划在点节点池上的候选项。

本文介绍如何向现有的 Azure Kubernetes 服务（AKS）群集添加辅助点节点池。

本文假定你基本了解 Kubernetes 和 Azure 负载均衡器的概念。 有关详细信息，请参阅 [Azure Kubernetes 服务 (AKS) 的 Kubernetes 核心概念][kubernetes-concepts]。

此功能目前处于预览状态。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="before-you-begin"></a>开始之前

当你创建群集以使用点节点池时，该群集还必须将虚拟机规模集用于节点池和*标准*SKU 负载均衡器。 创建群集后，还必须添加其他节点池，才能使用点节点池。 稍后的步骤中介绍了添加其他节点池，但首先需要启用预览功能。

> [!IMPORTANT]
> AKS 预览功能是自助服务，选择加入。 提供这些信息是为了从我们的社区收集反馈和 bug。 在预览版中，这些功能并不用于生产。 公共预览版中的功能低于 "最大努力" 支持。 仅在太平洋时区（PST）期间，AKS 技术支持团队提供协助。 有关其他信息，请参阅以下支持文章：
>
> * [AKS 支持策略][aks-support-policies]
> * [Azure 支持常见问题][aks-faq]

### <a name="register-spotpoolpreview-preview-feature"></a>注册 spotpoolpreview 预览功能

若要创建使用点节点池的 AKS 群集，必须在订阅上启用*spotpoolpreview*功能标志。 此功能在配置群集时提供了最新的服务增强功能。

> [!CAUTION]
> 在订阅上注册功能时，当前无法注册该功能。 启用某些预览功能后，默认值可用于在订阅中创建的所有 AKS 群集。 不要对生产订阅启用预览功能。 使用单独的订阅来测试预览功能并收集反馈。

使用[az feature register][az-feature-register]命令注册*spotpoolpreview*功能标志，如以下示例中所示：

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "spotpoolpreview"
```

状态显示为“已注册”需要几分钟时间。 您可以使用[az feature list][az-feature-list]命令检查注册状态：

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/spotpoolpreview')].{Name:name,State:properties.state}"
```

准备就绪后，请使用[az provider register][az-provider-register]命令刷新*ContainerService*资源提供程序的注册：

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

### <a name="install-aks-preview-cli-extension"></a>安装 aks-preview CLI 扩展

若要创建使用点节点池的 AKS 群集，需要*AKS* CLI 扩展版本0.4.32 或更高版本。 使用[az extension add][az-extension-add]命令安装*aks-preview* Azure CLI 扩展，然后使用[az extension update][az-extension-update]命令检查是否有任何可用的更新：

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview
 
# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="limitations"></a>限制

使用点节点池创建和管理 AKS 群集时，有以下限制：

* 点节点池不能是群集的默认节点池。 点节点池只能用于辅助池。
* 无法升级点节点池，因为点节点池无法保证 cordon 和排出。 必须将现有的专色节点池替换为新的点节点，以执行操作，例如升级 Kubernetes 版本。 若要替换点节点池，请使用不同版本的 Kubernetes 创建新的专色节点池，等待其状态为 "*就绪*"，然后删除旧节点池。
* 不能同时升级控制平面和节点池。 您必须单独升级它们，或者删除点节点池以同时升级控制面和剩余节点池。
* 专色节点池必须使用虚拟机规模集。
* 创建后，不能更改 ScaleSetPriority 或 SpotMaxPrice。
* 设置 SpotMaxPrice 时，值必须为-1，或者为正值，最多包含五个小数位数。
* 一个点节点池具有标签*kubernetes.azure.com/scalesetpriority:spot*、破坏*kubernetes.azure.com/scalesetpriority=spot:NoSchedule*和系统箱将具有抗关联。
* 必须添加相应的[toleration][spot-toleration]来计划点节点池中的工作负荷。

## <a name="add-a-spot-node-pool-to-an-aks-cluster"></a>将专色节点池添加到 AKS 群集

必须将一个专色节点池添加到已启用多个节点池的现有群集。 有关创建具有多个节点池的 AKS 群集的详细信息，请参阅[此处][use-multiple-node-pools]。

使用[az aks nodepool add][az-aks-nodepool-add]创建节点池。
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

默认情况下，当你创建具有多个节点池的群集时，将在 AKS 群集中创建*优先级*为 "*常规*" 的节点池。 上述命令将辅助节点池添加到具有 "*点*"*优先级*的现有 AKS 群集。 *点*的*优先级*使节点池成为一个点节点池。 在上面的示例中，*逐出策略*参数设置为*Delete* ，这是默认值。 将[逐出策略][eviction-policy]设置为 "*删除*" 时，节点池的底层规模集中的节点会在被逐出时删除。 你还可以将逐出策略设置为*解除分配*。 将逐出策略设置为*解除分配*时，基础规模集中的节点会在逐出时设置为已停止释放的状态。 已停止解除分配状态中的节点会根据计算配额进行计数，并可能会导致群集缩放或升级问题。 *优先级*和*逐出策略*值只能在创建节点池的过程中设置。 以后不能更新这些值。

该命令还启用[群集自动缩放程序][cluster-autoscaler]，这是建议用于污点节点池的。 根据群集中运行的工作负荷，群集自动缩放程序扩展并缩小节点池中的节点数。 对于点节点池，如果仍需要其他节点，则群集自动缩放程序将在逐出之后增加节点数。 如果更改节点池可以具有的最大节点数，还需要调整与群集自动缩放程序关联的 `maxCount` 值。 如果不使用群集自动缩放程序，则在逐出时，点池最终将减小到零，并需要手动操作才能接收任何其他的专色节点。

> [!Important]
> 仅在可处理中断的点节点池（如批处理作业和测试环境）上计划工作负荷。 建议在点节点池上设置[taints 和 tolerations][taints-tolerations] ，以确保仅在点节点池上安排可处理节点逐出的工作负荷。 例如，上述命令默认情况下会添加*kubernetes.azure.com/scalesetpriority=spot:NoSchedule*的破坏，因此在此节点上只计划具有相应 toleration 的 pod。

## <a name="verify-the-spot-node-pool"></a>验证点节点池

验证是否已将节点池添加为点节点池：

```azurecli
az aks nodepool show --resource-group myResourceGroup --cluster-name myAKSCluster --name spotnodepool
```

确认*scaleSetPriority*为 "*点*"。

若要将 pod 计划为在专色节点上运行，请添加与应用到您的点节点的破坏相对应的 toleration。 下面的示例显示了 yaml 文件的一部分，该部分定义了与上一步中使用的*kubernetes.azure.com/scalesetpriority=spot:NoSchedule*破坏相对应的 toleration。

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

部署具有此 toleration 的 pod 时，Kubernetes 可以成功地在应用了破坏的节点上计划 pod。

## <a name="max-price-for-a-spot-pool"></a>某个位置池的最大价格
基于区域和 SKU，[专色实例的定价是可变][pricing-spot]的。 有关详细信息，请参阅适用于[Linux][pricing-linux]和[Windows][pricing-windows]的定价。

使用可变定价，可以选择设置最大价格（美元），最多可使用5个小数位数。 例如，值*0.98765*的最大价格为 $0.98765 美元/小时。 如果将最大价格设置为 *-1*，则不会根据价格收回实例。 如果有可用容量和配额，则实例的价格将是当前的长期价格或标准实例的价格。

## <a name="next-steps"></a>后续步骤

本文介绍了如何将专色节点池添加到 AKS 群集。 有关如何跨节点池控制 pod 的详细信息，请参阅[AKS 中高级计划程序功能的最佳实践][operator-best-practices-advanced-scheduler]。

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