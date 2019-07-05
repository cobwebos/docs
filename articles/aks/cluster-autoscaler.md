---
title: 使用 Azure Kubernetes 服务 (AKS) 中的群集自动缩放程序
description: 了解如何使用群集自动缩放程序自动缩放群集以满足 Azure Kubernetes 服务 (AKS) 群集中的应用程序需求。
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 05/31/2019
ms.author: iainfou
ms.openlocfilehash: c4fe05c96b1006a7d110caa019619ce8be396fe8
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491554"
---
# <a name="preview---automatically-scale-a-cluster-to-meet-application-demands-on-azure-kubernetes-service-aks"></a>预览-自动缩放以满足应用程序的需求在 Azure Kubernetes 服务 (AKS) 群集

若要满足 Azure Kubernetes 服务 (AKS) 中的应用程序需求，可能需要调整运行工作负载的节点数。 群集自动缩放程序组件可以监视群集中由于资源约束而无法进行计划的 Pod。 检测到问题时，节点数会增加，以满足应用程序需求。 还会定期检查节点是否缺少正在运行的 Pod，随后根据需要减少节点数。 这种自动增加或减少 AKS 群集中的节点数的功能使你可以运行具有成本效益的高效群集。

本文演示如何在 AKS 群集中启用和管理群集自动缩放程序。 群集自动缩放程序只应在单个节点池与 AKS 群集上的预览版中进行测试。

> [!IMPORTANT]
> AKS 预览版功能是自助服务的选择加入。 提供这些项目是为了从我们的社区收集反馈和 bug。 在预览版中，这些功能不是用于生产环境中使用。 公共预览版中的功能属于最大努力支持。 AKS 技术支持团队的协助营业时间太平洋时区 （太平洋标准时间） 仅将提供。 有关其他信息，请参阅以下支持文章：
>
> * [AKS 支持策略][aks-support-policies]
> * [Azure 支持常见问题][aks-faq]

## <a name="before-you-begin"></a>开始之前

本文要求运行 Azure CLI 版本 2.0.65 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][azure-cli-install]。

### <a name="install-aks-preview-cli-extension"></a>安装 aks-preview CLI 扩展

若要使用群集自动缩放程序，需要*aks 预览版*CLI 扩展版本 0.4.1 或更高版本。 安装*aks 预览版*Azure CLI 扩展使用[az 扩展添加][az-extension-add]command, then check for any available updates using the [az extension update][az-extension-update]命令::

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-scale-set-feature-provider"></a>注册规模集功能提供程序

要创建使用规模集的 AKS，还必须在订阅上启用功能标志。 若要注册*VMSSPreview*功能标志，请使用[az 功能注册][az-feature-register]命令，在下面的示例所示：

> [!CAUTION]
> 注册时对某一订阅功能，目前你无法取消注册该功能。 启用某些预览功能后，可能会对所有 AKS 群集，然后在订阅中创建使用默认值。 不要启用预览功能在生产订阅。 使用单独的订阅来测试预览功能和收集反馈。

```azurecli-interactive
az feature register --name VMSSPreview --namespace Microsoft.ContainerService
```

状态显示为“已注册”需要几分钟时间  。 您可以检查注册状态使用[az 功能列表][az-feature-list]命令：

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/VMSSPreview')].{Name:name,State:properties.state}"
```

准备就绪后，刷新的注册*Microsoft.ContainerService*使用的资源提供程序[az provider register][az-provider-register]命令：

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="limitations"></a>限制

创建和管理 AKS 群集中使用群集自动缩放程序时，应考虑以下限制：

* 不能使用 HTTP 应用程序路由外接程序。

## <a name="about-the-cluster-autoscaler"></a>关于群集自动缩放程序

若要进行调整以适应不断变化的应用程序需求（如工作日与夜间或周末之间），群集通常需要一种自动缩放方式。 AKS 群集可以采用两种方式之一进行缩放：

* 群集自动缩放程序  会监视由于资源约束而无法在节点上计划的 Pod。 群集随后会自动增加节点数。
* 水平 Pod 自动缩放程序  会在 Kubernetes 群集中使用指标服务器来监视 Pod 的资源需求。 如果服务需要更多资源，则会自动增加 Pod 数以满足需求。

![群集自动缩放程序和水平 Pod 自动缩放程序通常协同工作以支持所需的应用程序需求](media/autoscaler/cluster-autoscaler.png)

水平 Pod 自动缩放程序和群集自动缩放程序随后还可以根据需要减少 Pod 和节点数。 当已有一段时间存在未使用的容量时，群集自动缩放程序会减少节点数。 要由群集自动缩放程序删除的节点上的 Pod 会在群集中的其他位置安全地进行计划。 如果无法移动 Pod，则群集自动缩放程序可能无法减少，如以下情况：

* Pod 直接创建，不由控制器对象（如部署或副本集）提供支持。
* Pod 中断预算 (PDB) 限制太多，不允许 Pod 数低于特定阈值。
* Pod 使用在不同节点上进行计划时无法遵循的节点选择器或反相关性。

有关如何群集自动缩放程序可能无法向下缩放的详细信息，请参阅[哪些类型的 pod 可以阻止群集自动缩放程序中删除节点？][autoscaler-scaledown]

群集自动缩放程序对诸如缩放事件与资源阈值之间的时间间隔等内容使用启动参数。 这些参数由 Azure 平台订阅，当前并未公开以供你调整。 群集自动缩放程序使用的参数的详细信息，请参阅[群集自动缩放程序参数是什么？][autoscaler-parameters]。

两个自动缩放程序可以协同工作，通常部署在一个群集中。 结合使用时，水平 Pod 自动缩放程序侧重于运行满足应用程序需求所需的 Pod 数。 群集自动缩放程序侧重于运行支持计划 Pod 所需的节点数。

> [!NOTE]
> 使用群集自动缩放程序时，会禁用手动缩放。 请让群集自动缩放程序确定所需的节点数。 如果要手动缩放群集，则[禁用群集自动缩放程序](#disable-the-cluster-autoscaler)。

## <a name="create-an-aks-cluster-and-enable-the-cluster-autoscaler"></a>创建 AKS 群集并启用群集自动缩放程序

如果需要创建 AKS 群集，使用[az aks 创建][az-aks-create]命令。 指定的 --kubernetes-version  应满足或超过在前面[开始之前](#before-you-begin)部分中概述最低版本号。 若要启用和配置群集自动缩放程序，请使用 --enable-cluster-autoscaler  参数，并指定节点 --min-count  和 --max-count  。

> [!IMPORTANT]
> 群集自动缩放程序是 Kubernetes 组件。 虽然 AKS 群集对节点使用虚拟机规模集，但请勿在 Azure 门户中或使用 Azure CLI 手动启用或编辑规模集自动缩放设置。 让 Kubernetes 群集自动缩放程序管理所需的规模设置。 有关详细信息，请参阅[我可以修改 MC_ 资源组中的 AKS 资源吗？](faq.md#can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-mc_-resource-group)

以下示例创建启用了虚拟机规模集和群集自动缩放程序的 AKS 群集，并使用最少 1  个且最多 3  个节点：

```azurecli-interactive
# First create a resource group
az group create --name myResourceGroup --location canadaeast

# Now create the AKS cluster and enable the cluster autoscaler
az aks create \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --node-count 1 \
  --enable-vmss \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

创建群集并配置群集自动缩放程序设置需要几分钟时间。

### <a name="enable-the-cluster-autoscaler-on-an-existing-aks-cluster"></a>在现有 AKS 群集上启用群集自动缩放程序

可以在满足前面[开始之前](#before-you-begin)部分中所概述要求的现有 AKS 群集上启用群集自动缩放程序。 使用[更新 az aks][az-aks-update]命令，然后选择 *-自动缩放群集启用程序*，然后指定一个节点 *-最小计数*并 *-最大计数*. 下面的示例在使用最少 1  个且最多 3  个节点的现有群集上启用群集自动缩放程序：

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

如果最小节点计数大于群集中的现有节点数，则创建附加节点需要花费几分钟时间。

## <a name="change-the-cluster-autoscaler-settings"></a>更改群集自动缩放程序设置

在创建或更新现有 AKS 群集的上一步中，群集自动缩放程序最小节点计数设置为 1  ，且最大节点计数设置为 3  。 随着应用程序需求发生变化，可能需要调整群集自动缩放程序节点计数。

若要更改的节点计数，请使用[az aks 更新][az-aks-update]命令并指定最小值和最大值。 下面的示例将 --min-count  设置为 1  ，并将 --max-count  设置为 5  ：

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --update-cluster-autoscaler \
  --min-count 1 \
  --max-count 5
```

> [!NOTE]
> 在预览版期间，设置的最小节点计数不能高于当前为群集设置的计数。 例如，如果当前将最小计数设置为 1  ，则不能将最小计数更新为 3  。

监视应用程序和服务的性能，并调整群集自动缩放程序节点计数以匹配所需性能。

## <a name="disable-the-cluster-autoscaler"></a>禁用群集自动缩放程序

如果不再想要使用群集自动缩放程序，则可以禁用它使用[az aks 更新][az-aks-update]命令。 群集自动缩放程序处于禁用状态时，不会删除节点。

若要删除群集自动缩放程序，请指定 --disable-cluster-autoscaler  参数，如下面的示例所示：

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --disable-cluster-autoscaler
```

你可以手动缩放群集使用[az aks 规模][az-aks-scale]命令。 如果使用水平 Pod 自动缩放程序，则该功能会在群集自动缩放程序禁用的情况下继续运行，但 Pod 可能会在节点资源全部已使用时最终无法进行计划。

## <a name="next-steps"></a>后续步骤

本文演示了如何自动缩放 AKS 节点数。 还可以使用水平 Pod 自动缩放程序自动调整运行应用程序的 Pod 数。 有关使用水平 pod 自动缩放程序的步骤，请参阅[缩放应用程序在 AKS 中][aks-scale-apps]。

<!-- LINKS - internal -->
[aks-upgrade]: upgrade-cluster.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-extension-add]: /cli/azure/extension#az-extension-add
[aks-scale-apps]: tutorial-kubernetes-scale.md
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-scale]: /cli/azure/aks#az-aks-scale
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update

<!-- LINKS - external -->
[az-aks-update]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview
[autoscaler-scaledown]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-types-of-pods-can-prevent-ca-from-removing-a-node
[autoscaler-parameters]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-are-the-parameters-to-ca
