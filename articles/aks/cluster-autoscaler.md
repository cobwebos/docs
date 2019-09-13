---
title: 使用 Azure Kubernetes 服务 (AKS) 中的群集自动缩放程序
description: 了解如何使用群集自动缩放程序自动缩放群集以满足 Azure Kubernetes 服务 (AKS) 群集中的应用程序需求。
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 07/18/2019
ms.author: mlearned
ms.openlocfilehash: 5671c3e36a49680b72b1f7b138cbd6e9c0bc4313
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/11/2019
ms.locfileid: "70914852"
---
# <a name="preview---automatically-scale-a-cluster-to-meet-application-demands-on-azure-kubernetes-service-aks"></a>预览-自动缩放群集以满足 Azure Kubernetes 服务（AKS）上的应用程序需求

若要满足 Azure Kubernetes 服务 (AKS) 中的应用程序需求，可能需要调整运行工作负载的节点数。 群集自动缩放程序组件可以监视群集中由于资源约束而无法进行计划的 Pod。 当检测到问题时，将增加节点池中的节点数，以满足应用程序的需求。 还会定期检查节点是否缺少正在运行的 Pod，随后根据需要减少节点数。 这种自动增加或减少 AKS 群集中的节点数的功能使你可以运行具有成本效益的高效群集。

本文演示如何在 AKS 群集中启用和管理群集自动缩放程序。 群集自动缩放程序只能在 AKS 群集上以预览进行测试。

> [!IMPORTANT]
> AKS 预览功能是自助式选择加入功能。 预览版“按原样”提供，并且仅在“可用情况下”提供，不包含在服务级别协议和有限保障中。 AKS 预览版的内容部分包含在客户支持中，我们只能尽力提供支持。 因此，这些功能不应用于生产。 有关其他信息，请参阅以下支持文章：
>
> * [AKS 支持策略][aks-support-policies]
> * [Azure 支持常见问题][aks-faq]

## <a name="before-you-begin"></a>开始之前

本文要求运行 Azure CLI 2.0.65 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][azure-cli-install]。

### <a name="install-aks-preview-cli-extension"></a>安装 aks-preview CLI 扩展

若要使用群集自动缩放程序，需要*aks* CLI 扩展版本0.4.12 或更高版本。 使用[az extension add][az-extension-add]命令安装*aks-preview* Azure CLI 扩展，然后使用[az extension update][az-extension-update]命令检查是否有任何可用的更新：

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="limitations"></a>限制

创建和管理使用群集自动缩放程序的 AKS 群集时，有以下限制：

* 无法使用 HTTP 应用程序路由加载项。

## <a name="about-the-cluster-autoscaler"></a>关于群集自动缩放程序

若要进行调整以适应不断变化的应用程序需求（如工作日与夜间或周末之间），群集通常需要一种自动缩放方式。 AKS 群集可以采用两种方式之一进行缩放：

* 群集自动缩放程序会监视由于资源约束而无法在节点上计划的 Pod。 群集随后会自动增加节点数。
* 水平 Pod 自动缩放程序会在 Kubernetes 群集中使用指标服务器来监视 Pod 的资源需求。 如果服务需要更多资源，则会自动增加 Pod 数以满足需求。

![群集自动缩放程序和水平 Pod 自动缩放程序通常协同工作以支持所需的应用程序需求](media/autoscaler/cluster-autoscaler.png)

水平 Pod 自动缩放程序和群集自动缩放程序随后还可以根据需要减少 Pod 和节点数。 当已有一段时间存在未使用的容量时，群集自动缩放程序会减少节点数。 要由群集自动缩放程序删除的节点上的 Pod 会在群集中的其他位置安全地进行计划。 如果无法移动 Pod，则群集自动缩放程序可能无法减少，如以下情况：

* Pod 直接创建，不由控制器对象（如部署或副本集）提供支持。
* Pod 中断预算 (PDB) 限制太多，不允许 Pod 数低于特定阈值。
* Pod 使用在不同节点上进行计划时无法遵循的节点选择器或反相关性。

有关群集自动缩放程序如何无法缩减的详细信息，请参阅[哪些类型的 pod 可以阻止群集自动缩放程序删除节点？][autoscaler-scaledown]

群集自动缩放程序对诸如缩放事件与资源阈值之间的时间间隔等内容使用启动参数。 这些参数由 Azure 平台订阅，当前并未公开以供你调整。 有关群集自动缩放程序使用的参数的详细信息，请参阅[什么是群集自动缩放程序参数？][autoscaler-parameters]。

群集和水平 pod autoscalers 可以一起使用，并且通常在群集中一起部署。 结合使用时，水平 Pod 自动缩放程序侧重于运行满足应用程序需求所需的 Pod 数。 群集自动缩放程序侧重于运行支持计划 Pod 所需的节点数。

> [!NOTE]
> 使用群集自动缩放程序时，会禁用手动缩放。 请让群集自动缩放程序确定所需的节点数。 如果要手动缩放群集，则[禁用群集自动缩放程序](#disable-the-cluster-autoscaler)。

## <a name="create-an-aks-cluster-and-enable-the-cluster-autoscaler"></a>创建 AKS 群集并启用群集自动缩放程序

如果需要创建 AKS 群集，请使用[az AKS create][az-aks-create]命令。 若要在群集的节点池上启用并配置群集自动缩放程序，请使用 *--自动缩放程序*参数，并指定节点 *-最小计数*和 *--最大计数*。

> [!IMPORTANT]
> 群集自动缩放程序是 Kubernetes 组件。 虽然 AKS 群集对节点使用虚拟机规模集，但请勿在 Azure 门户中或使用 Azure CLI 手动启用或编辑规模集自动缩放设置。 让 Kubernetes 群集自动缩放程序管理所需的规模设置。 有关详细信息，请参阅可否[修改节点资源组中的 AKS 资源？](faq.md#can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group)

以下示例创建一个具有虚拟机规模集支持的单节点池的 AKS 群集。 它还会在群集的节点池中启用群集自动缩放程序，并将最小值设置为*1* ，最大值为*3*个节点：

```azurecli-interactive
# First create a resource group
az group create --name myResourceGroup --location eastus

# Now create the AKS cluster and enable the cluster autoscaler
az aks create \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --node-count 1 \
  --vm-set-type VirtualMachineScaleSets \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

> [!NOTE]
> 如果在运行`az aks create`时指定 *--kubernetes 版本*，则该版本必须满足或超过 "[开始之前](#before-you-begin)" 部分中所述的最低版本号。

创建群集并配置群集自动缩放程序设置需要几分钟时间。

## <a name="change-the-cluster-autoscaler-settings"></a>更改群集自动缩放程序设置

> [!IMPORTANT]
> 如果已在订阅上启用*多个代理池*功能，请跳到 "[使用多个代理池自动缩放" 部分](##use-the-cluster-autoscaler-with-multiple-node-pools-enabled)。 启用了多个代理池的群集要求使用`az aks nodepool`命令集来更改节点池特定属性， `az aks`而不是。 以下说明假定你尚未启用多个节点池。 若要检查是否已启用该功能， `az feature  list -o table`请运行， `Microsoft.ContainerService/multiagentpoolpreview`并查找。

在上一步中，若要创建 AKS 群集或更新现有节点池，请将 "群集自动缩放程序最小节点计数" 设置为 " *1*"，并将 "最大节点计数" 设置为*3*。 随着应用程序需求发生变化，可能需要调整群集自动缩放程序节点计数。

若要更改节点计数，请使用[az aks update][az-aks-update]命令。

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --update-cluster-autoscaler \
  --min-count 1 \
  --max-count 5
```

上面的示例将*myAKSCluster*中单节点池上的 cluster 自动缩放程序更新为最小值为*1* ，最大值为*5*节点。

> [!NOTE]
> 在预览期间，不能设置比当前为节点池设置的最小节点计数。 例如，如果当前将最小计数设置为 1，则不能将最小计数更新为 3。

监视应用程序和服务的性能，并调整群集自动缩放程序节点计数以匹配所需性能。

## <a name="disable-the-cluster-autoscaler"></a>禁用群集自动缩放程序

如果不再想要使用群集自动缩放程序，可以使用[az aks update][az-aks-update]命令禁用该群集，并指定 *--disable-自动缩放程序*参数。 群集自动缩放程序处于禁用状态时，不会删除节点。

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --disable-cluster-autoscaler
```

使用[az aks scale][az-aks-scale]命令禁用群集自动缩放程序后，可以手动缩放群集。 如果使用水平 pod 自动缩放程序，则该功能将继续运行，同时禁用群集自动缩放程序，但如果所有节点资源都在使用中，则无法计划盒箱。

## <a name="re-enable-a-disabled-cluster-autoscaler"></a>重新启用已禁用的群集自动缩放程序

如果要在现有群集上重新启用群集自动缩放程序，可以使用[az aks update][az-aks-update]命令重新启用该群集，并指定 *--自动缩放程序*参数。

## <a name="use-the-cluster-autoscaler-with-multiple-node-pools-enabled"></a>使用启用了多个节点池的群集自动缩放程序

群集自动缩放程序可以与启用的[多节点池预览功能](use-multiple-node-pools.md)一起使用。 按照该文档了解如何启用多个节点池，以及如何将其他节点池添加到现有群集。 同时使用这两个功能时，将在群集中的每个单独节点池中启用群集自动缩放程序，并可以将唯一自动缩放规则传递到每个节点。

以下命令假设你遵循本文档前面的[初始说明](#create-an-aks-cluster-and-enable-the-cluster-autoscaler)，并想要将现有节点池的最大计数从*3*更新为*5*。 使用[az aks nodepool update][az-aks-nodepool-update]命令更新现有节点池的设置。

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name multipoolcluster \
  --name mynodepool \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 5
```

可以通过[az aks nodepool update][az-aks-nodepool-update]和传递`--disable-cluster-autoscaler`参数来禁用群集自动缩放程序。

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name multipoolcluster \
  --name mynodepool \
  --disable-cluster-autoscaler
```

## <a name="next-steps"></a>后续步骤

本文演示了如何自动缩放 AKS 节点数。 还可以使用水平 Pod 自动缩放程序自动调整运行应用程序的 Pod 数。 有关使用横向 pod 自动缩放程序的步骤，请参阅[在 AKS 中缩放应用程序][aks-scale-apps]。

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
[az-aks-nodepool-update]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview#enable-cluster-auto-scaler-for-a-node-pool
[autoscaler-scaledown]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-types-of-pods-can-prevent-ca-from-removing-a-node
[autoscaler-parameters]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-are-the-parameters-to-ca
