---
title: 使用 Azure Kubernetes 服务 (AKS) 中的群集自动缩放程序
description: 了解如何使用群集自动缩放程序自动缩放群集以满足 Azure Kubernetes 服务 (AKS) 群集中的应用程序需求。
services: container-service
ms.topic: article
ms.date: 07/18/2019
ms.openlocfilehash: 732c405cad20aef3485b521fa245cb504a809c40
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "82129060"
---
# <a name="automatically-scale-a-cluster-to-meet-application-demands-on-azure-kubernetes-service-aks"></a>自动缩放群集以满足 Azure Kubernetes 服务 (AKS) 中的应用程序需求

若要满足 Azure Kubernetes 服务 (AKS) 中的应用程序需求，可能需要调整运行工作负载的节点数。 群集自动缩放程序组件可以监视群集中由于资源约束而无法进行计划的 Pod。 检测到问题时，节点池中的节点数会增加，以满足应用程序需求。 还会定期检查节点是否缺少正在运行的 Pod，随后根据需要减少节点数。 这种自动增加或减少 AKS 群集中的节点数的功能使你可以运行具有成本效益的高效群集。

本文演示如何在 AKS 群集中启用和管理群集自动缩放程序。

## <a name="before-you-begin"></a>开始之前

本文要求运行 Azure CLI 2.0.76 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][azure-cli-install]。

## <a name="limitations"></a>限制

创建和管理使用群集自动缩放程序的 AKS 群集时存在以下限制：

* 无法使用 HTTP 应用程序路由加载项。

## <a name="about-the-cluster-autoscaler"></a>关于群集自动缩放程序

若要进行调整以适应不断变化的应用程序需求（如工作日与夜间或周末之间），群集通常需要一种自动缩放方式。 AKS 群集可以采用两种方式之一进行缩放：

* 群集自动缩放程序  会监视由于资源约束而无法在节点上计划的 Pod。 群集随后会自动增加节点数。
* 水平 Pod 自动缩放程序  会在 Kubernetes 群集中使用指标服务器来监视 Pod 的资源需求。 如果应用程序需要更多资源，则会自动增加 Pod 数以满足需求。

![群集自动缩放程序和水平 Pod 自动缩放程序通常协同工作以支持所需的应用程序需求](media/autoscaler/cluster-autoscaler.png)

水平 Pod 自动缩放程序和群集自动缩放程序随后还可以根据需要减少 Pod 和节点数。 当已有一段时间存在未使用的容量时，群集自动缩放程序会减少节点数。 要由群集自动缩放程序删除的节点上的 Pod 会在群集中的其他位置安全地进行计划。 如果无法移动 Pod，则群集自动缩放程序可能无法纵向缩减，如以下情况：

* Pod 是直接创建的，不受控制器对象（如部署或副本集）支持。
* Pod 中断预算 (PDB) 限制太多，不允许 Pod 数低于特定阈值。
* Pod 使用在不同节点上进行计划时无法遵循的节点选择器或反相关性。

有关群集自动缩放程序如何可能无法减少的详细信息，请参阅[哪些类型的 Pod 可能会阻止群集自动缩放程序删除节点？][autoscaler-scaledown]

群集自动缩放程序对诸如缩放事件与资源阈值之间的时间间隔等内容使用启动参数。 这些参数由 Azure 平台订阅，当前并未公开以供你调整。 有关群集自动缩放程序使用的参数的详细信息，请参阅[群集自动缩放程序参数是什么？][autoscaler-parameters]。

群集和水平 Pod 自动缩放程序可以协同工作，通常部署在一个群集中。 结合使用时，水平 Pod 自动缩放程序侧重于运行满足应用程序需求所需的 Pod 数。 群集自动缩放程序侧重于运行支持计划 Pod 所需的节点数。

> [!NOTE]
> 使用群集自动缩放程序时，会禁用手动缩放。 请让群集自动缩放程序确定所需的节点数。 如果要手动缩放群集，则[禁用群集自动缩放程序](#disable-the-cluster-autoscaler)。

## <a name="create-an-aks-cluster-and-enable-the-cluster-autoscaler"></a>创建 AKS 群集并启用群集自动缩放程序

如果需要创建 AKS 群集，请使用 [az aks create][az-aks-create] 命令。 若要在群集的节点池中启用和配置群集自动缩放程序，请使用 *--enable-cluster-autoscaler* 参数，并指定节点 *--min-count* 和 *--max-count*。

> [!IMPORTANT]
> 群集自动缩放程序是 Kubernetes 组件。 虽然 AKS 群集对节点使用虚拟机规模集，但请勿在 Azure 门户中或使用 Azure CLI 手动启用或编辑规模集自动缩放设置。 让 Kubernetes 群集自动缩放程序管理所需的规模设置。 有关详细信息，请参阅[是否可以修改节点资源组中的 AKS 资源？](faq.md#can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group)

以下示例创建一个 AKS 群集，其中包含一个虚拟机规模集支持的单一节点池。 它还在群集的节点池中启用群集自动缩放程序，并将节点的最小数目设置为 *1*，最大数目设置为 *3*：

```azurecli-interactive
# First create a resource group
az group create --name myResourceGroup --location eastus

# Now create the AKS cluster and enable the cluster autoscaler
az aks create \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --node-count 1 \
  --vm-set-type VirtualMachineScaleSets \
  --load-balancer-sku standard \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

创建群集并配置群集自动缩放程序设置需要几分钟时间。

## <a name="change-the-cluster-autoscaler-settings"></a>更改群集自动缩放程序设置

> [!IMPORTANT]
> 如果 AKS 群集包含多个节点池，请跳到[使用多个代理池进行自动缩放](#use-the-cluster-autoscaler-with-multiple-node-pools-enabled)部分。 包含多个代理池的群集要求使用 `az aks nodepool` 命令集而不是 `az aks` 来更改节点池特定的属性。

在创建 AKS 群集或更新现有节点池的上一步骤中，群集自动缩放程序最小节点计数设置为 *1*，且最大节点计数设置为 *3*。 随着应用程序需求发生变化，可能需要调整群集自动缩放程序节点计数。

若要更改节点计数，请使用 [az aks update][az-aks-update] 命令。

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --update-cluster-autoscaler \
  --min-count 1 \
  --max-count 5
```

以上示例将 *myAKSCluster* 中单个节点池上的群集自动缩放程序更新为最少 *1* 个和最多 *5* 个节点。

> [!NOTE]
> 目前，无法为节点池设置超过此限制的最小节点计数。 例如，如果当前将最小计数设置为 1  ，则不能将最小计数更新为 3  。

监视应用程序和服务的性能，并调整群集自动缩放程序节点计数以匹配所需性能。

## <a name="using-the-autoscaler-profile"></a>使用自动缩放程序配置文件

还可以通过更改群集范围内的自动缩放程序配置文件中的默认值，配置更详细的群集自动缩放程序的详细信息。 例如，在10分钟后，节点不可用后，将发生缩减事件。 如果你的工作负荷每15分钟运行一次，则可能需要更改自动缩放程序配置文件，以便在15到20分钟后缩减使用节点。 启用群集自动缩放程序后，除非指定不同的设置，否则将使用默认配置文件。 群集自动缩放程序配置文件具有以下可更新的设置：

| 设置                          | 说明                                                                              | 默认值 |
|----------------------------------|------------------------------------------------------------------------------------------|---------------|
| 扫描-间隔                    | 增加或减少群集的重新评估频率                                    | 10 秒    |
| 向下缩放-添加后延迟       | 缩减评估恢复后多长时间                               | 10 分钟    |
| 减少-删除后的延迟    | 缩减评估恢复的节点删除后多长时间                          | 扫描-间隔 |
| 在故障后缩减延迟   | 缩减评估恢复后缩减故障后多长时间                     | 3 分钟     |
| 缩减-不需要的时间         | 节点有资格缩减之前应等待多长时间                  | 10 分钟    |
| 缩减-处于未准备-时间          | 处于未准备节点有资格缩减之前应等待多长时间         | 20 分钟    |
| 缩减利用率-阈值 | 节点利用率级别，定义为所请求资源的总和除以容量，低于此级别可将节点视为缩小 | 0.5 |
| 最大-正常终止-秒     | 群集自动缩放程序尝试缩小节点时等待 pod 终止的最大秒数。 | 600秒   |
| 相似-节点组 | 检测类似节点池并平衡它们之间的节点数 | false |

> [!IMPORTANT]
> Cluster 自动缩放程序配置文件影响所有使用群集自动缩放程序的节点池。 不能为每个节点池设置自动缩放程序配置文件。

### <a name="install-aks-preview-cli-extension"></a>安装 aks-preview CLI 扩展

若要设置群集自动缩放程序设置配置文件，需要*aks* CLI 扩展版本0.4.30 或更高版本。 使用 [az extension add][az-extension-add] 命令安装 *aks-preview* Azure CLI 扩展，然后使用 [az extension update][az-extension-update] 命令检查是否有任何可用的更新：

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="set-the-cluster-autoscaler-profile-on-an-existing-aks-cluster"></a>在现有的 AKS 群集上设置群集自动缩放程序配置文件

将[az aks update][az-aks-update]命令与*自动缩放程序*参数一起使用，以便在群集上设置群集自动缩放程序配置文件。 以下示例在配置文件中将扫描间隔设置配置为30秒。

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --cluster-autoscaler-profile scan-interval=30s
```

在群集中的节点池上启用群集自动缩放程序后，这些群集也将使用群集自动缩放程序配置文件。 例如：

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name myAKSCluster \
  --name mynodepool \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

> [!IMPORTANT]
> 设置群集自动缩放程序配置文件时，启用了群集自动缩放程序的任何现有节点池都将立即开始使用配置文件。

### <a name="set-the-cluster-autoscaler-profile-when-creating-an-aks-cluster"></a>创建 AKS 群集时设置群集自动缩放程序配置文件

你还可以在创建群集时使用*自动缩放程序*参数。 例如：

```azurecli-interactive
az aks create \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --node-count 1 \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3 \
  --cluster-autoscaler-profile scan-interval=30s
```

上述命令将创建一个 AKS 群集，并为群集范围的自动缩放程序配置文件将扫描间隔定义为30秒。 该命令还会在初始节点池上启用群集自动缩放程序，并将最小节点计数设置为1，将最大节点数设置为3。

### <a name="reset-cluster-autoscaler-profile-to-default-values"></a>将群集自动缩放程序配置文件重置为默认值

使用[az aks update][az-aks-update]命令重置群集上的群集自动缩放程序配置文件。

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --cluster-autoscaler-profile ""
```

## <a name="disable-the-cluster-autoscaler"></a>禁用群集自动缩放程序

如果你不再想要使用群集自动缩放程序，可以使用 [az aks update][az-aks-update] 命令并指定 *--disable-cluster-autoscaler* 参数将其禁用。 群集自动缩放程序处于禁用状态时，不会删除节点。

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --disable-cluster-autoscaler
```

禁用群集自动缩放程序后，可以使用 [az aks scale][az-aks-scale] 命令手动缩放群集。 如果使用水平 Pod 自动缩放程序，则该功能会在群集自动缩放程序禁用的情况下继续运行，但 Pod 可能会在节点资源全部已使用时最终无法进行计划。

## <a name="re-enable-a-disabled-cluster-autoscaler"></a>重新启用已禁用的群集自动缩放程序

若要对现有的群集重新启用群集自动缩放程序，可以使用 [az aks update][az-aks-update] 命令并指定 *--enable-cluster-autoscaler*、*--min-count* 和 *--max-count* 参数。

## <a name="retrieve-cluster-autoscaler-logs-and-status"></a>检索群集自动缩放程序日志和状态

若要诊断和调试自动缩放程序事件，可以从自动缩放程序加载项检索日志和状态。

AKS 将代你管理群集自动缩放程序，并在托管控制平面中运行它。 必须配置主节点日志，才能将其作为结果查看。

若要将日志配置为从群集自动缩放程序推送到 Log Analytics，请执行以下步骤。

1. 设置资源日志规则，以便将群集自动缩放程序日志推送 Log Analytics。 [此处提供了详细说明](https://docs.microsoft.com/azure/aks/view-master-logs#enable-resource-logs)。选择“日志”的选项时，请确保选中 `cluster-autoscaler` 对应的复选框。
1. 在 Azure 门户中单击群集上的“日志”部分。
1. 将以下示例查询输入到 Log Analytics：

```
AzureDiagnostics
| where Category == "cluster-autoscaler"
```

只要有要检索的日志，就会看到类似于以下示例的日志。

![Log Analytics 日志](media/autoscaler/autoscaler-logs.png)

群集自动缩放程序还会将运行状况写出到名为 `cluster-autoscaler-status` 的 configmap。 若要检索这些日志，请执行`kubectl`以下命令。 将报告配置了群集自动缩放程序的每个节点池的运行状况。

```
kubectl get configmap -n kube-system cluster-autoscaler-status -o yaml
```

若要详细了解通过自动缩放程序记录的内容，请阅读有关 [Kubernetes/自动缩放程序 GitHub 项目](https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#ca-doesnt-work-but-it-used-to-work-yesterday-why)的常见问题解答。

## <a name="use-the-cluster-autoscaler-with-multiple-node-pools-enabled"></a>将群集自动缩放程序与启用的多个节点池结合使用

群集自动缩放程序可以与启用的[多个节点池](use-multiple-node-pools.md)一起使用。 请在该文档中了解如何启用多个节点池，以及如何将更多节点池添加到现有群集。 将这两个功能结合使用时，可对群集中的每个节点池启用群集自动缩放程序，并可将唯一的自动缩放规则传递到每个节点池。

以下命令假设已按照本文档前面提供的[初始说明](#create-an-aks-cluster-and-enable-the-cluster-autoscaler)操作，并且你要将现有节点池的最大计数从 *3* 更新为 *5*。 使用 [az aks nodepool update][az-aks-nodepool-update] 命令更新现有节点池的设置。

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name myAKSCluster \
  --name nodepool1 \
  --update-cluster-autoscaler \
  --min-count 1 \
  --max-count 5
```

可以使用 [az aks nodepool update][az-aks-nodepool-update] 并传递 `--disable-cluster-autoscaler` 参数来禁用群集自动缩放程序。

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name myAKSCluster \
  --name nodepool1 \
  --disable-cluster-autoscaler
```

若要对现有的群集重新启用群集自动缩放程序，可以使用 [az aks nodepool update][az-aks-nodepool-update] 命令并指定 *--enable-cluster-autoscaler*、*--min-count* 和 *--max-count* 参数。

## <a name="next-steps"></a>后续步骤

本文演示了如何自动缩放 AKS 节点数。 还可以使用水平 Pod 自动缩放程序自动调整运行应用程序的 Pod 数。 有关使用水平 Pod 自动缩放程序的步骤，请参阅[在 AKS 中缩放应用程序][aks-scale-apps]。

<!-- LINKS - internal -->
[aks-faq]: faq.md
[aks-scale-apps]: tutorial-kubernetes-scale.md
[aks-support-policies]: support-policies.md
[aks-upgrade]: upgrade-cluster.md
[autoscaler-profile-properties]: #using-the-autoscaler-profile
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-scale]: /cli/azure/aks#az-aks-scale
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register

<!-- LINKS - external -->
[az-aks-update]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview
[az-aks-nodepool-update]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview#enable-cluster-auto-scaler-for-a-node-pool
[autoscaler-scaledown]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-types-of-pods-can-prevent-ca-from-removing-a-node
[autoscaler-parameters]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-are-the-parameters-to-ca
