---
title: 使用标准 SKU 负载均衡器
titleSuffix: Azure Kubernetes Service
description: 了解如何使用采用标准 SKU 的负载均衡器通过 Azure Kubernetes 服务 (AKS) 公开你的服务。
services: container-service
author: zr-msft
ms.topic: article
ms.date: 09/27/2019
ms.author: zarhoads
ms.openlocfilehash: 14e80f6348772af77c5a53b1d5e9111c4ae8ba9b
ms.sourcegitcommit: 90d2d95f2ae972046b1cb13d9956d6668756a02e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/14/2020
ms.locfileid: "83402064"
---
# <a name="use-a-standard-sku-load-balancer-in-azure-kubernetes-service-aks"></a>在 Azure Kubernetes 服务 (AKS) 中使用标准 SKU 负载均衡器

若要通过 Azure Kubernetes 服务 (AKS) 中 `LoadBalancer` 类型的 Kubernetes 服务为应用程序提供访问权限，可以使用 Azure 负载均衡器。 在 AKS 上运行的负载均衡器可用作内部或外部负载均衡器。 内部负载均衡使 Kubernetes 服务仅可供与 AKS 群集在同一虚拟网络中运行的应用程序访问。 外部负载均衡器接收一个或多个用于入口通信的公共 IP，因此允许使用公共 IP 从外部访问 Kubernetes 服务。

Azure 负载均衡器以两种 SKU 提供：“基本”和“标准” 。 默认情况下，创建 AKS 群集时将使用标准  SKU。 使用标准 SKU 负载均衡器可提供更多特性和功能，例如更大的后端池大小和可用性区域。 在选择使用哪种负载均衡器之前，请务必了解标准与基本负载均衡器之间的区别。 创建 AKS 群集后，将无法更改该群集的负载均衡器 SKU。 有关基本与标准 SKU 的详细信息，请参阅 [Azure 负载均衡器 SKU 比较][azure-lb-comparison]。 

本文假定你基本了解 Kubernetes 和 Azure 负载均衡器的概念。 有关详细信息，请参阅 [Azure Kubernetes 服务 (AKS) 的 Kubernetes 核心概念][kubernetes-concepts]和[什么是 Azure 负载均衡器？][azure-lb]。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，本文要求运行 Azure CLI 2.0.81 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][install-azure-cli]。

## <a name="before-you-begin"></a>开始之前

本文假设你的 AKS 群集使用标准 SKU Azure 负载均衡器。 如果你需要 AKS 群集，请参阅[使用 Azure CLI][aks-quickstart-cli] 或[使用 Azure 门户][aks-quickstart-portal]时的 AKS 快速入门。

如果你使用现有的子网或资源组，则 AKS 群集服务主体还需要管理网络资源的权限。 通常，将向服务主体分配对委托资源的“网络参与者”角色。 你还可以使用系统分配的托管标识来获得权限，而非使用服务主体。 有关详细信息，请参阅[使用托管标识](use-managed-identity.md)。 有关权限的详细信息，请参阅[向 AKS 委派对其他 Azure 资源的访问权限][aks-sp]。

### <a name="moving-from-a-basic-sku-load-balancer-to-standard-sku"></a>从基本 SKU 负载均衡器移动到标准 SKU

如果你已有一个具有基本 SKU 负载均衡器的群集，则在进行迁移以使用具有标准 SKU 负载均衡器的群集时，需要注意一些重要的行为差异。

例如，由于群集的 `load-balancer-sku` 类型只能在创建群集时定义，因此，进行蓝/绿部署来迁移群集是一种常见的做法。 但是，基本 SKU 负载均衡器使用基本SKU IP地址，这些地址与标准 SKU 负载均衡器不兼容，因为后者需要标准 SKU IP地址。 迁移群集以升级负载均衡器 SKU 时，将需要一个具有兼容 IP 地址 SKU 的新 IP 地址。

有关如何迁移群集的更多注意事项，请访问[有关迁移注意事项的文档](aks-migration.md)，以便查看迁移时要考虑的重要主题的列表。 在 AKS 中使用标准 SKU 负载均衡器时，以下限制也是需要注意的重要行为差异。

### <a name="limitations"></a>限制

创建和管理支持标准 SKU 负载均衡器的 AKS 群集时，存在以下限制：

* 若要允许 AKS 群集的出口流量，至少需要一个公共 IP 或 IP 前缀。 若要维护控制平面与代理节点之间的连接，以及维护与早期 AKS 版本的兼容性，也需要公共 IP 或 IP 前缀。 使用标准 SKU 负载均衡器时用于指定公共 IP 或 IP 前缀的选项如下：
    * 提供你自己的公共 IP。
    * 提供你自己的公共 IP 前缀。
    * 指定一个最大为 100 的数字，这样 AKS 群集就会在其所在的已创建的资源组中创建这么多个标准 SKU 公共 IP，该群集在命名时通常以 *MC_* 开头。 AKS 将公共 IP 分配给标准 SKU 负载均衡器。 默认情况下，如果未指定公共 IP、公共 IP 前缀或 IP 数量，则会在 AKS 群集所在的资源组中自动创建一个公共 IP。 还必须允许公共地址，并避免创建任何禁止创建 IP 的 Azure Policy。
* 为负载均衡器使用标准 SKU 时，必须使用 Kubernetes 1.13 或更高版本。
* 只有在创建 AKS 群集时，才能定义负载均衡器 SKU。 创建 AKS 群集后，无法更改负载均衡器 SKU。
* 在单个群集中只能使用一种类型的负载均衡器 SKU （基本或标准）。
* 标准 SKU 负载均衡器仅支持标准 SKU IP 地址。

## <a name="use-the-standard-sku-load-balancer"></a>使用标准 SKU 负载均衡器

创建 AKS 群集时，默认情况下，在该群集中运行服务时将使用标准  SKU 负载均衡器。 例如，[使用 Azure CLI 的快速入门][aks-quickstart-cli]部署了一个使用标准 SKU 负载均衡器的示例应用程序。

> [!IMPORTANT]
> 可以通过自定义一个用户定义的路由 (UDR) 来避免公共 IP 地址。 将 AKS 群集的出站类型指定为 UDR 可以跳过 AKS 创建的 Azure 负载均衡器的 IP 预配和后端池设置。 请参阅[将群集的 `outboundType` 设置为“userDefinedRouting”](egress-outboundtype.md)。

## <a name="configure-the-load-balancer-to-be-internal"></a>将负载均衡器配置为内部负载均衡器

你还可以将负载均衡器配置为内部负载均衡器，而不是公开一个公共 IP。 若要将负载均衡器配置为内部负载均衡器，请将 `service.beta.kubernetes.io/azure-load-balancer-internal: "true"` 作为注释添加到 LoadBalancer 服务。 可以在[此处][internal-lb-yaml]查看 yaml 清单示例以及有关内部负载均衡器的更多详细信息。

## <a name="scale-the-number-of-managed-public-ips"></a>缩放托管公共 IP 的数量

使用具有默认创建的托管出站公共 IP 的标准 SKU 负载平衡器时，可以使用 load-balancer-managed-ip-count 参数缩放托管出站公共 IP 的数量。

若要更新现有群集，请运行以下命令。 还可以在创建群集时设置此参数，以获得多个托管出站公共 IP。

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-managed-outbound-ip-count 2
```

上面的示例将 myResourceGroup 中 myAKSCluster 群集的托管出站公共 IP 的数量设置为 2。   

还可以在创建群集时通过追加 `--load-balancer-managed-outbound-ip-count` 参数并将其设置为所需的值来使用 load-balancer-managed-ip-count 参数，以便设置托管出站公共 IP 的初始数量。 托管出站公共 IP 的默认数量为 1。

## <a name="provide-your-own-public-ips-or-prefixes-for-egress"></a>提供你自己的公共 IP 或前缀用于出口通信

使用标准 SKU 负载均衡器时，AKS 群集会自动在为 AKS 群集创建的资源组中创建公共 IP，并将公共 IP 分配给标准 SKU 负载均衡器。 另外，你还可以在创建群集时分配自己的公共 IP，或更新现有群集的负载均衡器属性。

通过引入多个 IP 地址或前缀，可以在定义单个负载均衡器对象后面的 IP 地址时定义多个支持服务。 特定节点的出口终结点将取决于与之关联的服务。

### <a name="pre-requisites-to-bring-your-own-ip-addresses-or-ip-prefixes"></a>创建自己的 IP 地址或 IP 前缀的先决条件
1. 使用标准 SKU 负载均衡器时，必须使用标准 SKU 公共 IP 进行出口通信。 可以使用 [az network public-ip show][az-network-public-ip-show] 命令验证公共 IP 的 SKU：

   ```azurecli-interactive
   az network public-ip show --resource-group myResourceGroup --name myPublicIP --query sku.name -o tsv
   ```
 1. 公共 IP 和 IP 前缀必须与 AKS 群集位于同一区域且属于同一订阅。
 1. 公共 IP 和 IP 前缀不能是由 AKS 创建为托管 IP 的 IP。 确保指定为自定义 IP 的任何 IP 都是手动创建的，而不是由 AKS 服务创建的。
 1. 公共 IP 和 IP 前缀不能被其他资源或服务使用。

 ### <a name="define-your-own-public-ip-or-prefixes-on-an-existing-cluster"></a>在现有群集上定义你自己的公共 IP 或 IP 前缀

使用 [az network public-ip show][az-network-public-ip-show] 命令列出你的公共 IP 的 ID。

```azurecli-interactive
az network public-ip show --resource-group myResourceGroup --name myPublicIP --query id -o tsv
```

上面的命令显示 myResourceGroup 资源组中的公共 IP“myPublicIP”的 ID。

将 az aks update 命令与 load-balancer-outbound-ips 参数配合使用，以便使用公共 IP 来更新群集。

以下示例将 load-balancer-outbound-ips 参数与来自上一命令的 ID 配合使用。

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2>
```

对于标准 SKU 负载均衡器，你还可以使用公共 IP 前缀进行出口通信。 以下示例使用 [az network public-ip prefix show][az-network-public-ip-prefix-show] 命令列出公共 IP 前缀的 ID：

```azurecli-interactive
az network public-ip prefix show --resource-group myResourceGroup --name myPublicIPPrefix --query id -o tsv
```

上面的命令显示 myResourceGroup 资源组中的公共 IP 前缀“myPublicIPPrefix”的 ID。

以下示例将 load-balancer-outbound-ip-prefixes 参数与来自上一命令的 ID 配合使用。

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ip-prefixes <publicIpPrefixId1>,<publicIpPrefixId2>
```

### <a name="define-your-own-public-ip-or-prefixes-at-cluster-create-time"></a>在创建群集时定义你自己的公共 IP 或 IP 前缀

你可能想在创建群集时引入自己的 IP 地址或 IP 前缀进行出口通信，以支持将出口终结点列入白名单之类的方案。 将上面显示的相同参数追加到群集创建步骤，以在群集生命周期开始时定义你自己的公共 IP 和 IP 前缀。

一开始就将 az aks create 命令与 load-balancer-outbound-ips 参数配合使用，以便使用公共 IP 创建新群集。

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

一开始就将 az aks create 命令与 load-balancer-outbound-ip-prefixes 参数配合使用，以便使用公共 IP 前缀创建新群集。

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

## <a name="configure-outbound-ports-and-idle-timeout"></a>配置出站端口和空闲超时

> [!WARNING]
> 以下部分用于较大规模网络的高级方案，或用于解决采用默认配置时的 SNAT 耗尽问题。 在将 AllocatedOutboundPorts 或 IdleTimeoutInMinutes 从默认值更改为其他值之前，你必须了解可用 VM 和 IP 地址的准确库存，以便使群集保持正常运行状态。
> 
> 更改 AllocatedOutboundPorts 和 IdleTimeoutInMinutes 的值可能会明显改变负载均衡器的出站规则的行为。 在更新这些值之前，请查看[负载均衡器出站规则][azure-lb-outbound-rules-overview]、[负载均衡器出站规则][azure-lb-outbound-rules]和 [Azure 中的出站连接][azure-lb-outbound-connections]，以充分了解你的更改造成的影响。

分配的出站端口及其空闲超时用于 [SNAT][azure-lb-outbound-connections]。 默认情况下，标准 SKU 负载均衡器[根据后端池大小自动分配出站端口数][azure-lb-outbound-preallocatedports]，并为每个端口设置 30 分钟的空闲超时。 若要查看这些值，请使用 [az network lb outbound-rule list][az-network-lb-outbound-rule-list] 显示负载均衡器的出站规则：

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

示例输出显示了 AllocatedOutboundPorts 和 IdleTimeoutInMinutes 的默认值。 如果 *AllocatedOutboundPorts* 的值为 0，则会根据后端池大小使用出站端口数自动分配机制来设置出站端口数。 例如，如果群集有 50 个或更少的节点，则会为每个节点分配 1024 个端口。

如果你预计使用上面的默认配置时会出现 SNAT 耗尽问题，请考虑更改 allocatedOutboundPorts 或 IdleTimeoutInMinutes 的设置。 每个额外的 IP 地址都会使 64,000 个额外端口可供分配，但 Azure 标准负载均衡器不会在添加更多 IP 地址时自动增加每个节点的端口数。 你可以通过设置 load-balancer-outbound-ports 和 load-balancer-idle-timeout 参数来更改这些值。 例如：

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ports 0 \
    --load-balancer-idle-timeout 30
```

> [!IMPORTANT]
> 在自定义 allocatedOutboundPorts 之前，必须[计算必需的配额][calculate-required-quota]以避免连接或缩放问题。 你为 allocatedOutboundPorts 指定的值还必须是 8 的倍数。

还可以在创建群集时使用 load-balancer-outbound-ports 和 load-balancer-idle-timeout 参数，但还必须指定 load-balancer-managed-outbound-ip-count、load-balancer-outbound-ips 或 load-balancer-outbound-ip-prefixes。  例如：

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --vm-set-type VirtualMachineScaleSets \
    --node-count 1 \
    --load-balancer-sku standard \
    --generate-ssh-keys \
    --load-balancer-managed-outbound-ip-count 2 \
    --load-balancer-outbound-ports 0 \
    --load-balancer-idle-timeout 30
```

将 load-balancer-outbound-ports 和 load-balancer-idle-timeout 参数从默认值更改为其他值会影响负载均衡器配置文件的行为，这将影响整个群集。

### <a name="required-quota-for-customizing-allocatedoutboundports"></a>自定义 allocatedOutboundPorts 时必需的配额
你必须有足够的出站 IP 容量，具体取决于节点 VM 和所需的已分配出站端口的数量。 若要验证是否有足够的出站 IP 容量，请使用以下公式： 
 
outboundIP 数 \* 64,000 \> nodeVM 数 \* desiredAllocatedOutboundPort 数。
 
例如，如果你有 3个 nodeVM 和 50,000 个 desiredAllocatedOutboundPort，则需要至少 3 个 outboundIP。 建议在所需容量的基础上增加额外的出站 IP 容量。 此外，在计算出站 IP 容量时，还必须考虑群集自动缩放程序，以及节点池升级的可能性。 对于群集自动缩放程序，请查看当前节点计数和最大节点计数，并使用较高的值。 对于升级，请考虑为每个节点池提供一个额外的允许升级的节点 VM。
 
将 IdleTimeoutInMinutes 设置为与默认值 30 分钟不同的值时，请考虑你的工作负荷需要使用出站连接多长时间。 还要考虑，对于在 AKS 外部使用的标准 SKU 负载均衡器，默认超时值为 4 分钟。 如果 IdleTimeoutInMinutes 值能够更准确地反映你的具体 AKS 工作负荷，则这有助于减少因不再使用的捆绑连接而导致的 SNAT 耗尽现象。

## <a name="restrict-access-to-specific-ip-ranges"></a>限制访问，仅允许特定 IP 范围进行访问

默认情况下，与负载均衡器的虚拟网络关联的网络安全组 (NSG) 有一个允许所有入站外部流量的规则。 你可以将此规则更新为仅允许特定 IP 范围的入站流量。 以下清单使用 loadBalancerSourceRanges 为入站外部流量指定新的 IP 范围：

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
  loadBalancerSourceRanges:
  - MY_EXTERNAL_IP_RANGE
```

上面的示例将该规则更新为仅允许来自 MY_EXTERNAL_IP_RANGE 范围的入站外部流量。 若要详细了解如何使用此方法限制对负载均衡器服务的访问，请参阅 [Kubernetes 文档][kubernetes-cloud-provider-firewall]。

## <a name="next-steps"></a>后续步骤

在 [Kubernetes 服务文档][kubernetes-services]中详细了解 Kubernetes 服务。

<!-- LINKS - External -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubernetes-cloud-provider-firewall]: https://kubernetes.io/docs/tasks/access-application-cluster/configure-cloud-provider-firewall/#restrict-access-for-loadbalancer-service
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
[azure-lb-comparison]: ../load-balancer/skus.md
[azure-lb-outbound-rules]: ../load-balancer/load-balancer-outbound-rules-overview.md#snatports
[azure-lb-outbound-connections]: ../load-balancer/load-balancer-outbound-connections.md#snat
[azure-lb-outbound-preallocatedports]: ../load-balancer/load-balancer-outbound-connections.md#preallocatedports
[azure-lb-outbound-rules-overview]: ../load-balancer/load-balancer-outbound-rules-overview.md
[install-azure-cli]: /cli/azure/install-azure-cli
[internal-lb-yaml]: internal-lb.md#create-an-internal-load-balancer
[kubernetes-concepts]: concepts-clusters-workloads.md
[use-kubenet]: configure-kubenet.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[calculate-required-quota]: #required-quota-for-customizing-allocatedoutboundports
