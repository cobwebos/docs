---
title: 升级 Azure Kubernetes 服务 (AKS) 群集
description: 了解如何升级 Azure Kubernetes 服务 (AKS) 群集以获取最新的功能和安全更新。
services: container-service
ms.topic: article
ms.date: 05/28/2020
ms.openlocfilehash: da46c44dc9cc16dfa44aacb15b35b652c0c912a9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87050615"
---
# <a name="upgrade-an-azure-kubernetes-service-aks-cluster"></a>升级 Azure Kubernetes 服务 (AKS) 群集

在 AKS 群集的生命周期中，经常需要升级到最新的 Kubernetes 版本。 必须应用最新的 Kubernetes 安全版本，或者通过升级来获取最新功能。 本文演示如何在 AKS 群集中升级主组件或单个默认的节点池。

对于使用多个节点池或 Windows Server 节点的 AKS 群集，请参阅[升级 AKS 中的节点池][nodepool-upgrade]。

## <a name="before-you-begin"></a>准备阶段

本文要求运行 Azure CLI 2.0.65 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][azure-cli-install]。

> [!WARNING]
> AKS 群集升级会触发节点的隔离和排空。 如果可用计算配额较低，则升级可能会失败。 有关详细信息，请参阅[增加配额](../azure-portal/supportability/resource-manager-core-quotas-request.md)。

## <a name="check-for-available-aks-cluster-upgrades"></a>检查是否有可用的 AKS 群集升级

若要检查哪些 Kubernetes 版本可用于群集，请使用 [az aks get-upgrades][az-aks-get-upgrades] 命令。 以下示例在名为 *myResourceGroup* 的资源组中检查是否有可供名为 *myAKSCluster* 的群集使用的升级：

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster --output table
```

> [!NOTE]
> 升级受支持的 AKS 群集时，不能跳过 Kubernetes 次要版本。 例如，允许从 1.12.x 升级到 1.13.x，或者从 1.13.x 升级到 1.14.x，但不允许从 1.12.x 升级到 1.14.x。
>
> 若要从 1.12.x 升级到 1.14.x，请先从 1.12.x 升级到 1.13.x，然后再从 1.13.x 升级到 1.14.x。
>
> 仅当从不受支持的版本升级回受支持的版本时，才可以跳过多个版本。 例如，可以从不受支持的 1.10.x 升级到受支持的 1.15.x 。

以下示例输出表明，群集可以升级到版本 1.13.9 和 1.13.10：

```console
Name     ResourceGroup     MasterVersion    NodePoolVersion    Upgrades
-------  ----------------  ---------------  -----------------  ---------------
default  myResourceGroup   1.12.8           1.12.8             1.13.9, 1.13.10
```
如果没有可用的升级，你将获得：
```console
ERROR: Table output unavailable. Use the --query option to specify an appropriate query. Use --debug for more info.
```

## <a name="customize-node-surge-upgrade-preview"></a>自定义节点浪涌升级 (预览版) 

> [!Important]
> 节点浪涌需要针对每个升级操作请求的最大浪涌计数的订阅配额。 例如，具有5个节点池（每个节点的计数为4个节点）的群集总共包含20个节点。 如果每个节点池的最大电涌值为50%，则需要10个节点 (2 个节点的附加计算和 IP 配额，) 需要完成升级。
>
> 如果使用 Azure CNI，请验证子网中是否存在可用 ip，以 [满足 AZURE CNI 的 IP 要求](configure-azure-cni.md)。

默认情况下，AKS 将升级配置为浪涌，并另外添加一个节点。 "最大冲击" 设置的默认值为1时，AKS 通过在现有应用程序的 cordon/排出替换较早版本的节点之前创建一个附加节点来最大程度地减少工作负载中断。 可以为每个节点池自定义最大浪涌值，以便在升级速度和升级中断之间进行权衡。 增加最大的浪涌值后，升级过程的速度将更快，但是为 max 电涌设置较大的值可能会导致在升级过程中发生中断。 

例如，max 电涌值100% 提供最快的升级过程 (将节点计数翻倍) ，同时还会导致节点池中的所有节点同时排出。 你可能想要使用较高的值（例如）来测试环境。 对于生产节点池，建议使用33% 的 max_surge 设置。

AKS 接受整数值和最大冲击的百分比值。 整数（如 "5"）指示向浪涌增加了五个节点。 如果值为 "50%"，则表示池中当前节点计数的半电涌值为一半。 最大浪涌百分比值的最小值为1%，最大值为100%。 百分比值将向上舍入到最近的节点计数。 如果 max 电涌值低于升级时的当前节点计数，则使用当前节点计数来表示最大浪涌值。

在升级过程中，最大电涌值最小为1，最大值可以等于节点池中的节点数。 你可以设置较大的值，但在升级时，最大的最大可用节点数不会高于池中的节点数。

### <a name="set-up-the-preview-feature-for-customizing-node-surge-upgrade"></a>设置用于自定义节点浪涌升级的预览功能

```azurecli-interactive
# register the preview feature
az feature register --namespace "Microsoft.ContainerService" --name "MaxSurgePreview"
```

注册需要花费几分钟时间。 使用以下命令验证该功能是否已注册：

```azurecli-interactive
# Verify the feature is registered:
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/MaxSurgePreview')].{Name:name,State:properties.state}"
```

预览期间，需要使用 *aks* CLI 扩展来使用最大冲击。 使用 [az extension add][az-extension-add] 命令，然后使用 [az extension update][az-extension-update] 命令来查找任何可用的更新：

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

> [!Important]
> 节点池上的最大冲击设置是永久性的。  后续的 Kubernetes 升级或节点版本升级将使用此设置。 你可以随时更改节点池的最大浪涌值。 对于生产节点池，建议使用最大-电涌设置33%。

使用以下命令为新的或现有的节点池设置最大浪涌值。

```azurecli-interactive
# Set max surge for a new node pool
az aks nodepool add -n mynodepool -g MyResourceGroup --cluster-name MyManagedCluster --max-surge 33%
```

```azurecli-interactive
# Update max surge for an existing node pool 
az aks nodepool update -n mynodepool -g MyResourceGroup --cluster-name MyManagedCluster --max-surge 5
```

## <a name="upgrade-an-aks-cluster"></a>升级 AKS 群集

如果有一系列适用于 AKS 群集的版本，则可使用 [az aks upgrade][az-aks-upgrade] 命令进行升级。 在升级过程中，AKS 将向运行指定 Kubernetes 版本的群集添加一个新节点，然后仔细地一次[隔离并清空][kubernetes-drain]一个旧节点，将对正在运行的应用程序造成的中断情况降到最低。 确认新节点运行应用程序 Pod 以后，就会删除旧节点。 此过程会重复进行，直至群集中的所有节点都已升级完毕。

```azurecli-interactive
az aks upgrade \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --kubernetes-version KUBERNETES_VERSION
```

升级群集需要几分钟时间，具体取决于有多少节点。

> [!NOTE]
> 允许群集升级完成的总时间。 此时间是通过取 `10 minutes * total number of nodes in the cluster` 的乘积来计算的。 例如，在 20 节点群集中，升级操作必须在 200 分钟内成功，否则 AKS 将使操作失败，以避免出现无法恢复的群集状态。 若要在升级失败时恢复，请在达到超时值后重试升级操作。

若要确认升级是否成功，请使用 [az aks show][az-aks-show] 命令：

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --output table
```

以下示例输出表明群集现在运行 1.13.10：

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
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
