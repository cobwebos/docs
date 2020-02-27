---
title: 升级 Azure Kubernetes 服务 (AKS) 群集
description: 了解如何升级 Azure Kubernetes 服务 (AKS) 群集
services: container-service
ms.topic: article
ms.date: 05/31/2019
ms.openlocfilehash: 4520297e83f96f95b10ecafd5af52a913dc5f450
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/26/2020
ms.locfileid: "77621978"
---
# <a name="upgrade-an-azure-kubernetes-service-aks-cluster"></a>升级 Azure Kubernetes 服务 (AKS) 群集

在 AKS 群集的生命周期中，经常需要升级到最新的 Kubernetes 版本。 必须应用最新的 Kubernetes 安全版本，或者通过升级来获取最新功能。 本文介绍如何在 AKS 群集中升级主组件或单个默认节点池。

对于使用多个节点池或 Windows Server 节点的 AKS 群集（当前在 AKS 中为预览版），请参阅[在 AKS 中升级节点池][nodepool-upgrade]。

## <a name="before-you-begin"></a>开始之前

本文要求运行 Azure CLI 版本2.0.65 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][azure-cli-install]。

> [!WARNING]
> AKS 群集升级触发 cordon 并排出节点。 如果可用的计算配额较低，升级可能会失败。 有关详细信息，请参阅[增加配额](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request)。
> 如果运行自己的群集自动缩放程序部署，请在升级过程中禁用它（可将其扩展为零个副本），因为这样做可能会影响升级过程。 托管自动缩放程序会自动处理这种情况。 

## <a name="check-for-available-aks-cluster-upgrades"></a>检查是否有可用的 AKS 群集升级

若要查看可用于群集的 Kubernetes 版本，请使用[az aks get 升级][az-aks-get-upgrades]命令。 以下示例在名为 *myResourceGroup* 的资源组中检查是否有可供名为 *myAKSCluster* 的群集使用的升级：

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster --output table
```

> [!NOTE]
> 升级 AKS 群集时，不能跳过 Kubernetes 次要版本。 例如，允许*在 -> * *1.13. x*或*1.13.* x -> *1.14*之间进行升级，但不允许使用*1.12* -> *1.14* 。
>
> 若要升级，请从*1.12* -> *1.14*中首次从*1.12.* *x 升级 -> 2.x*，然后从*1.13. x*升级 -> *1.14*。

以下示例输出显示可将群集升级到版本*1.13.9*和*1.13.10*：

```console
Name     ResourceGroup     MasterVersion    NodePoolVersion    Upgrades
-------  ----------------  ---------------  -----------------  ---------------
default  myResourceGroup   1.12.8           1.12.8             1.13.9, 1.13.10
```
如果没有可用的升级，你将获得：
```console
ERROR: Table output unavailable. Use the --query option to specify an appropriate query. Use --debug for more info.
```

## <a name="upgrade-an-aks-cluster"></a>升级 AKS 群集

使用 AKS 群集的可用版本列表，使用[az AKS upgrade][az-aks-upgrade]命令升级。 在升级过程中，AKS 会向群集中添加运行指定 Kubernetes 版本的新节点，然后仔细[cordon 并排出][kubernetes-drain]其中一个旧节点，以最大程度地减少运行应用程序的中断。 当新节点被确认为正在运行的应用程序 pod 时，将删除旧节点。 此过程将重复，直至群集中的所有节点均已升级。

下面的示例将群集升级到版本*1.13.10*：

```azurecli-interactive
az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version 1.13.10
```

升级群集需要几分钟时间，具体取决于有多少节点。 

> [!NOTE]
> 完成群集升级所用的总时间。 这一次是通过采用 `10 minutes * total number of nodes in the cluster`的产品来计算的。 例如，在20个节点的群集中，在200分钟内升级操作必须成功，否则 AKS 将无法正常运行，以避免群集状态不可恢复。 若要在升级失败时恢复，请在达到超时值后重试升级操作。

若要确认升级是否成功，请使用[az aks show][az-aks-show]命令：

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --output table
```

以下示例输出显示群集现在运行*1.13.10*：

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ---------------------------------------------------------------
myAKSCluster  eastus      myResourceGroup  1.13.10               Succeeded            myaksclust-myresourcegroup-19da35-90efab95.hcp.eastus.azmk8s.io
```

## <a name="next-steps"></a>后续步骤

本文演示了如何升级现有的 AKS 群集。 若要详细了解如何部署和管理 AKS 群集，请参阅相关教程系列。

> [!div class="nextstepaction"]
> [AKS 教程][aks-tutorial-prepare-app]

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-upgrades]: /cli/azure/aks#az-aks-get-upgrades
[az-aks-upgrade]: /cli/azure/aks#az-aks-upgrade
[az-aks-show]: /cli/azure/aks#az-aks-show
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
