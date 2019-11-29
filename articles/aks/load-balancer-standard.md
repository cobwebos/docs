---
title: 在 Azure Kubernetes 服务 (AKS) 中使用标准 SKU 负载均衡器
description: 了解如何在 Azure Kubernetes 服务 (AKS) 中使用标准 SKU 负载均衡器来公开服务。
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: article
ms.date: 09/27/2019
ms.author: zarhoads
ms.openlocfilehash: ef826239bc916b4ccf25785f92397286017d00f7
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/19/2019
ms.locfileid: "74171402"
---
# <a name="use-a-standard-sku-load-balancer-in-azure-kubernetes-service-aks"></a>在 Azure Kubernetes 服务 (AKS) 中使用标准 SKU 负载均衡器

若要通过 Azure Kubernetes Service （AKS）中 `LoadBalancer` 类型的 Kubernetes services 提供对应用程序的访问权限，可以使用 Azure 负载均衡器。 在 AKS 中运行的负载均衡器可用作内部或外部负载均衡器。 内部负载均衡器使得仅 AKS 群集所在的同一虚拟网络中运行的应用程序能够访问 Kubernetes 服务。 外部负载均衡器接收入口的一个或多个公共 IP，并使得 Kubernetes 服务可以通过公共 IP 在外部进行访问。

Azure 负载均衡器以两种 SKU 提供：“基本”和“标准”。 默认情况下，创建 AKS 群集时使用*标准*SKU。 使用*标准*SKU 负载均衡器可提供其他特性和功能，如更大的后端池大小和可用性区域。 在选择使用标准或基本负载均衡器之前，必须了解两者之间的差异。 创建 AKS 群集后，无法更改该群集的负载均衡器 SKU。 有关基本和标准 SKU 的详细信息，请参阅 *Azure 负载均衡器 SKU 的比较*。[][azure-lb-comparison]

本文假设读者基本了解 Kubernetes 和 Azure 负载均衡器的概念。 有关详细信息，请参阅 [Azure Kubernetes 服务 (AKS) 的 Kubernetes 核心概念][kubernetes-concepts]和[什么是 Azure 负载均衡器？][azure-lb]。

如果还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，本文要求运行 Azure CLI 2.0.74 或更高版本。 可以运行 `az --version` 来查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][install-azure-cli]。

## <a name="before-you-begin"></a>开始之前

本文假设你有一个带有*标准*SKU Azure 负载均衡器的 AKS 群集。 如果需要 AKS 群集，请参阅 AKS 快速入门[使用 Azure CLI][aks-quickstart-cli] 或[使用 Azure 门户][aks-quickstart-portal]。

如果使用现有的子网或资源组，AKS 群集服务主体还需要管理网络资源的权限。 通常，将“网络参与者”角色分配给委派资源上的服务主体。 有关权限的详细信息，请参阅[委派 AKS 访问其他 Azure 资源][aks-sp]。

### <a name="moving-from-a-basic-sku-load-balancer-to-standard-sku"></a>从基本 SKU 负载均衡器移动到标准 SKU

如果具有基本 SKU 负载均衡器的现有群集，则在迁移以使用带有标准 SKU 负载均衡器的群集时，需要注意一些重要的行为差异。

例如，如果在群集创建时只能定义群集的 `load-balancer-sku` 类型，则可以将蓝色/绿色部署迁移到群集。 但是，*基本*Sku 负载均衡器使用的*基本 sku* IP 地址与*标准 sku*负载平衡器不兼容，因为它们需要*标准 sku* ip 地址。 当迁移群集来升级负载平衡器 Sku 时，将需要一个具有兼容 IP 地址 SKU 的新 IP 地址。

有关如何迁移群集的更多注意事项，请访问[有关迁移注意事项的文档](acs-aks-migration.md)，以查看迁移时要考虑的重要主题的列表。 在 AKS 中使用标准 SKU 负载平衡器时，以下限制也是要注意的重要行为差异。

### <a name="limitations"></a>限制

创建和管理支持标准 SKU 负载均衡器的 AKS 群集时存在以下限制：

* 至少需要指定一个公共 IP 或 IP 前缀来允许 AKS 群集的出口流量。 此外，需要使用公共 IP 或 IP 前缀来保持控制平面与代理节点之间的连接，以及保持与旧版 AKS 的兼容性。 可以使用以下选项指定标准 SKU 负载均衡器的公共 IP 或 IP 前缀：
    * 提供自己的公共 IP。
    * 提供自己的公共 IP 前缀。
    * 指定最大为 100 的数字，以允许 AKS 群集在其所在的同一个资源组（名称通常以 *MC_* 开头）中创建多个标准 SKU 公共 IP。 AKS 会将公共 IP 分配到标准 SKU 负载均衡器。 默认情况下，如果未指定公共 IP、公共 IP 前缀或 IP 数目，系统会在 AKS 群集所在的同一个资源组中自动创建一个公共 IP。 此外，必须允许公共地址，并避免创建任何会阻止创建 IP 的 Azure 策略。
* 为负载均衡器使用*标准*SKU 时，必须使用 Kubernetes 版本*1.13 或更高*版本。
* 只能在创建 AKS 群集时定义负载均衡器 SKU。 创建 AKS 群集后，无法更改负载均衡器 SKU。
* 只能在单个群集中使用一种类型的负载均衡器 SKU （基本或标准）。
* *标准*SKU 负载平衡器仅支持*标准*SKU IP 地址。

## <a name="configure-the-load-balancer-to-be-internal"></a>将负载均衡器配置为内部负载均衡器

还可将负载均衡器配置为内部负载均衡器且不公开公共 IP。 若要将负载均衡器配置为内部负载均衡器，请添加 `service.beta.kubernetes.io/azure-load-balancer-internal: "true"` 作为 *LoadBalancer* 服务的注释。 可在[此处][internal-lb-yaml]查看示例 YAML 清单，以及有关内部负载均衡器的更多详细信息。

## <a name="scale-the-number-of-managed-public-ips"></a>缩放托管的公共 Ip 数

结合默认创建的托管出站公共 IP 使用标准 SKU 负载均衡器时，可以使用 *load-balancer-managed-ip-count* 参数来调整托管出站公共 IP 的数量。

若要更新现有群集，请运行以下命令。 还可以在创建群集时设置此参数，以指定多个托管出站公共 IP。

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-managed-outbound-ip-count 2
```

以上示例将 *myResourceGroup* 中 *myAKSCluster* 群集的托管出站公共 IP 数量设置为 *2*。 

还可以在创建群集时，通过追加 *参数并将其设置为所需的值，使用*load-balancer-managed-ip-count`--load-balancer-managed-outbound-ip-count` 参数来设置托管出站公共 IP 的初始数量。 托管出站公共 IP 的默认数量为 1。

## <a name="provide-your-own-public-ips-or-prefixes-for-egress"></a>为出口提供自己的公共 Ip 或前缀

使用标准 SKU 负载均衡器时，AKS 群集将自动在为它创建的同一个资源组中创建公共 IP，并将该公共 IP 分配给标准 SKU 负载均衡器。 或者，可以在创建群集时分配自己的公共 IP，或更新现有群集的负载均衡器属性。

通过引入多个 IP 地址或前缀，可以在单个负载均衡器对象后面定义 IP 地址时定义多个后备服务。 特定节点的出口终结点将依赖于与这些节点关联的服务。

> [!IMPORTANT]
> 必须将出口的标准 SKU 公共 IP 与负载均衡器的标准 SKU 配合使用。 可以使用 [az network public-ip show][az-network-public-ip-show] 命令验证公共 IP 的 SKU：
>
> ```azurecli-interactive
> az network public-ip show --resource-group myResourceGroup --name myPublicIP --query sku.name -o tsv
> ```

使用 [az network public-ip show][az-network-public-ip-show] 命令列出公共 IP 的 ID。

```azurecli-interactive
az network public-ip show --resource-group myResourceGroup --name myPublicIP --query id -o tsv
```

以上命令显示 *myResourceGroup* 资源组中 *myPublicIP* 公共 IP 的 ID。

结合 *load-balancer-outbound-ips* 参数使用 *az aks update* 命令，以更新群集中的公共 IP。

以下示例结合前一命令返回的 ID 使用 *load-balancer-outbound-ips* 参数。

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2>
```

你也可以将出口的公共 IP 前缀与标准 SKU 负载均衡器配合使用。 以下示例使用 [az network public-ip prefix show][az-network-public-ip-prefix-show] 命令列出公共 IP 前缀的 ID：

```azurecli-interactive
az network public-ip prefix show --resource-group myResourceGroup --name myPublicIPPrefix --query id -o tsv
```

以上命令显示 *myResourceGroup* 资源组中 *myPublicIPPrefix* 公共 IP 前缀的 ID。

以下示例将 *load-balancer-outbound-ip-prefixes* 参数与前一命令返回的 ID 配合使用。

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ip-prefixes <publicIpPrefixId1>,<publicIpPrefixId2>
```

> [!IMPORTANT]
> 公共 IP 和 IP 前缀必须位于同一区域，并且与 AKS 群集位于同一订阅中。 

### <a name="define-your-own-public-ip-or-prefixes-at-cluster-create-time"></a>在创建群集时定义自己的公共 IP 或前缀

在创建群集时，你可能想要使用自己的出口 IP 地址或 IP 前缀，以支持出口终结点白名单等方案。 将上面所示的相同参数追加到群集创建步骤，可在群集生命周期的起始部分定义自己的公共 IP 和 IP 前缀。

结合 *load-balancer-outbound-ips* 参数使用 *az aks create* 命令可在启动时使用你的公共 IP 创建新的群集。

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --vm-set-type VirtualMachineScaleSets \
    --node-count 1 \
    --load-balancer-sku standard \
    --generate-ssh-keys \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2>
```

结合 *load-balancer-outbound-ip-prefixes* 参数使用 *az aks create* 命令可在启动时使用你的公共 IP 前缀创建新的群集。

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --vm-set-type VirtualMachineScaleSets \
    --node-count 1 \
    --load-balancer-sku standard \
    --generate-ssh-keys \
    --load-balancer-outbound-ip-prefixes <publicIpPrefixId1>,<publicIpPrefixId2>
```

## <a name="show-the-outbound-rule-for-your-load-balancer"></a>显示负载均衡器的出站规则

若要显示在负载均衡器中创建的出站规则，请使用[az network lb list list][az-network-lb-outbound-rule-list]并指定 AKS 群集的节点资源组：

```azurecli-interactive
NODE_RG=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
az network lb outbound-rule list --resource-group $NODE_RG --lb-name kubernetes -o table
```

前面的命令将列出负载均衡器的出站规则，例如：

```console
AllocatedOutboundPorts    EnableTcpReset    IdleTimeoutInMinutes    Name             Protocol    ProvisioningState    ResourceGroup
------------------------  ----------------  ----------------------  ---------------  ----------  -------------------  -------------
0                         True              30                      aksOutboundRule  All         Succeeded            MC_myResourceGroup_myAKSCluster_eastus  
```

在示例输出中， *AllocatedOutboundPorts*为0。 *AllocatedOutboundPorts*的值表示 SNAT 端口分配会根据后端池大小恢复为自动分配。 有关更多详细信息，请参阅 Azure 中的[负载均衡器出站规则][azure-lb-outbound-rules]和[出站连接][azure-lb-outbound-connections]。

## <a name="next-steps"></a>后续步骤

在 [Kubernetes 服务文档][kubernetes-services]中详细了解 Kubernetes 服务。

<!-- LINKS - External -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/
[aks-engine]: https://github.com/Azure/aks-engine

<!-- LINKS - Internal -->
[advanced-networking]: configure-azure-cni.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-sp]: kubernetes-service-principal.md#delegate-access-to-other-azure-resources
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-aks-install-cli]: /cli/azure/aks?view=azure-cli-latest#az-aks-install-cli
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-group-create]: /cli/azure/group#az-group-create
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-network-lb-outbound-rule-list]: /cli/azure/network/lb/outbound-rule?view=azure-cli-latest#az-network-lb-outbound-rule-list
[az-network-public-ip-show]: /cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-show
[az-network-public-ip-prefix-show]: /cli/azure/network/public-ip/prefix?view=azure-cli-latest#az-network-public-ip-prefix-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[azure-lb]: ../load-balancer/load-balancer-overview.md
[azure-lb-comparison]: ../load-balancer/load-balancer-overview.md#skus
[azure-lb-outbound-rules]: ../load-balancer/load-balancer-outbound-rules-overview.md#snatports
[azure-lb-outbound-connections]: ../load-balancer/load-balancer-outbound-connections.md#snat
[install-azure-cli]: /cli/azure/install-azure-cli
[internal-lb-yaml]: internal-lb.md#create-an-internal-load-balancer
[kubernetes-concepts]: concepts-clusters-workloads.md
[use-kubenet]: configure-kubenet.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
