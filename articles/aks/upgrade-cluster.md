---
title: 升级 Azure Kubernetes 服务 (AKS) 群集
description: 了解如何升级 Azure Kubernetes 服务 (AKS) 群集
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/31/2019
ms.author: mlearned
ms.openlocfilehash: 4cf959c5218160a8fe341e6ffdfdf459c1a19247
ms.sourcegitcommit: b12a25fc93559820cd9c925f9d0766d6a8963703
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2019
ms.locfileid: "69019169"
---
# <a name="upgrade-an-azure-kubernetes-service-aks-cluster"></a>升级 Azure Kubernetes 服务 (AKS) 群集

在 AKS 群集的生命周期中，经常需要升级到最新的 Kubernetes 版本。 必须应用最新的 Kubernetes 安全版本，或者通过升级来获取最新功能。 本文演示如何在 AKS 群集中升级主组件或单个默认的节点池。

对于使用多个节点池或 Windows Server 节点的 AKS 群集 (当前在 AKS 中为预览版), 请参阅[升级 AKS 中的节点池][nodepool-upgrade]。

## <a name="before-you-begin"></a>开始之前

本文要求运行 Azure CLI 2.0.65 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][azure-cli-install]。

> [!WARNING]
> AKS 群集升级触发 cordon 并排出节点。 如果可用的计算配额较低, 升级可能会失败。  有关详细信息, 请参阅[增加配额](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request?branch=pr-en-us-83289)。

## <a name="check-for-available-aks-cluster-upgrades"></a>检查是否有可用的 AKS 群集升级

若要检查哪些 Kubernetes 版本可用于群集，请使用 [az aks get-upgrades][az-aks-get-upgrades] 命令。 以下示例在名为 *myResourceGroup* 的资源组中检查是否有可供名为 *myAKSCluster* 的群集使用的升级：

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster --output table
```

> [!NOTE]
> 升级 AKS 群集时，不能跳过 Kubernetes 次要版本。 例如, 允许在*1.12* -> . x*1.13.*  -> x 或*1.13.* x*1.14*之间进行升级, 但*1.12* -> *1.14*不是。
>
> 若要从*1.12.*  -> x*1.14*升级, 请 -> 首先从*1.12.* x*1.13.* x 升级, 然后从*1.13. x* -> *1.14*升级。

以下示例输出显示可将群集升级到版本*1.13.9*:

```console
Name     ResourceGroup    MasterVersion    NodePoolVersion    Upgrades
-------  ---------------  ---------------  -----------------  --------------
default  myResourceGroup  1.12.8           1.12.8             1.13.9
```

## <a name="upgrade-an-aks-cluster"></a>升级 AKS 群集

如果有一系列适用于 AKS 群集的版本，则可使用 [az aks upgrade][az-aks-upgrade] 命令进行升级。 在升级过程中，AKS 将向运行指定 Kubernetes 版本的群集添加一个新节点，然后仔细地一次[隔离并清空][kubernetes-drain]一个旧节点，将对正在运行的应用程序造成的中断情况降到最低。 确认新节点运行应用程序 Pod 以后，就会删除旧节点。 此过程会重复进行，直至群集中的所有节点都已升级完毕。

下面的示例将群集升级到版本*1.13.9*:

```azurecli-interactive
az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version 1.13.9
```

升级群集需要几分钟时间，具体取决于有多少节点。

若要确认升级是否成功，请使用 [az aks show][az-aks-show] 命令：

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --output table
```

以下示例输出显示群集现在运行*1.13.9*:

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ---------------------------------------------------------------
myAKSCluster  eastus      myResourceGroup  1.13.9               Succeeded            myaksclust-myresourcegroup-19da35-90efab95.hcp.eastus.azmk8s.io
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
