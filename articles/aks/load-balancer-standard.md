---
title: 使用公共负载均衡器
titleSuffix: Azure Kubernetes Service
description: 了解如何使用带有标准 SKU 的公共负载均衡器通过 Azure Kubernetes Service (AKS) 公开服务。
services: container-service
ms.topic: article
ms.date: 06/14/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: 11f8442f188ea6ce7ee1de5a093362279da4594c
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2020
ms.locfileid: "86251157"
---
# <a name="use-a-public-standard-load-balancer-in-azure-kubernetes-service-aks"></a>使用 Azure Kubernetes 服务中的公共标准负载均衡器 (AKS) 

Azure 负载均衡器是支持入站和出站方案 (OSI) 型号的开放系统互连的 L4。 它将到达负载均衡器前端的入站流分发到后端池实例。

与 AKS 集成时的**公共**负载均衡器有两个用途：

1. 提供到 AKS 虚拟网络内群集节点的出站连接。 它通过将节点专用 IP 地址转换为其*出站池*所属的公共 ip 地址来实现此目标。
2. 通过类型的 Kubernetes 服务提供对应用程序的访问 `LoadBalancer` 。 利用它，可以轻松扩展应用程序，并创建高度可用的服务。

**内部 (或专用) **负载均衡器仅允许专用 ip 作为前端。 内部负载均衡器用于对虚拟网络内部的流量进行负载均衡。 在混合方案中，还可以从本地网络访问负载均衡器前端。

本文档介绍与公共负载均衡器的集成。 有关内部负载均衡器集成，请参阅[AKS 内部负载均衡器文档](internal-lb.md)。

## <a name="before-you-begin"></a>准备阶段

Azure 负载均衡器以两种 SKU 提供：“基本”和“标准”** **。 默认情况下，创建 AKS 群集时使用*标准*SKU。 使用*标准*SKU 可以访问附加功能，例如更大的后端池、[**多个节点池**](use-multiple-node-pools.md)和[**可用性区域**](availability-zones.md)。 这是建议用于 AKS 的负载均衡器 SKU。

有关基本与标准 SKU 的详细信息，请参阅 [Azure 负载均衡器 SKU 比较][azure-lb-comparison]。 

本文假设你有一个带有*标准*SKU Azure 负载平衡器的 AKS 群集，并演练如何使用和配置负载均衡器的某些功能和功能。 如果需要 AKS 群集，请参阅 AKS 快速入门[使用 Azure CLI][aks-quickstart-cli] 或[使用 Azure 门户][aks-quickstart-portal]。

> [!IMPORTANT]
> 如果你不想使用 Azure 负载均衡器来提供出站连接，而是使用自己的网关、防火墙或代理来实现这一目的，则可以通过使用[**出站类型作为 UserDefinedRouting (UDR) **](egress-outboundtype.md)，跳过创建负载均衡器出站池和相应的前端 IP。 出站类型定义群集的出口方法并且默认为类型：负载均衡器。

## <a name="use-the-public-standard-load-balancer"></a>使用公共标准负载均衡器

创建出站类型为 "AKS" 的群集后 (默认) ，该群集已准备好使用负载均衡器公开服务。

为此，可以创建类型的公共服务， `LoadBalancer` 如下面的示例中所示。 首先创建名为的服务清单 `public-svc.yaml` ：

```yaml
apiVersion: v1
kind: Service
metadata:
  name: public-svc
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: public-app
```

使用[kubectl 应用][kubectl-apply]程序部署公共服务清单，并指定 YAML 清单的名称：

```azurecli-interactive
kubectl apply -f public-svc.yaml
```

Azure 负载均衡器将配置为使用新的公共 IP，此服务将位于此新服务之前。 由于 Azure 负载均衡器可以有多个前端 ip，因此部署的每个新服务都将获取一个新的专用前端 IP 来进行唯一访问。

你可以通过运行以下示例确认是否已创建你的服务并配置负载均衡器：

```azurecli-interactive
kubectl get service public-svc
```

```console
NAMESPACE     NAME          TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)         AGE
default       public-svc    LoadBalancer   10.0.39.110    52.156.88.187   80:32068/TCP    52s
```

查看服务详细信息时，在负载均衡器上为此服务创建的公共 IP 地址将显示在 "*外部 ip* " 列中。 IP 地址可能需要一两分钟的时间才能从更改 *\<pending\>* 为实际的公共 IP 地址，如以上示例中所示。

## <a name="configure-the-public-standard-load-balancer"></a>配置公共标准负载均衡器

使用标准 SKU 公共负载均衡器时，可以在创建时自定义一组选项，或更新群集。 这些选项允许你自定义负载均衡器以满足工作负荷需求，并应相应地进行查看。 通过标准负载均衡器，你可以：

* 设置或缩放托管出站 Ip 的数目
* 自定义[出站 ip 或出站 IP 前缀](#provide-your-own-outbound-public-ips-or-prefixes)
* 自定义分配给群集的每个节点的出站端口数量
* 配置空闲连接的超时设置

### <a name="scale-the-number-of-managed-outbound-public-ips"></a>缩放托管出站公共 Ip 的数目

除了入站连接以外，Azure 负载均衡器还提供从虚拟网络的出站连接。 使用出站规则可以更方便地配置公共标准负载均衡器的出站网络地址转换。

与所有负载均衡器规则一样，出站规则遵循负载均衡和入站 NAT 规则的类似语法：

***前端 Ip + 参数 + 后端池***

出站规则为后端池识别的、要转换为前端的所有虚拟机配置出站 NAT。  参数针对出站 NAT 算法提供更精细的控制。

尽管出站规则只能配合单个公共 IP 地址使用，但出站规则减轻了缩放出站 NAT 的负担。 规划大规模方案时可以使用多个 IP 地址，并可以使用出站规则来缓解容易出现 SNAT 耗尽的模式。 前端提供的每个附加 IP 地址都为负载均衡器提供了64k 的暂时端口，用作 SNAT 端口。 

当使用*标准*SKU 负载平衡器以及默认情况下创建的托管出站公共 ip 时，可以使用参数来扩展托管出站公共 ip 的数目 **`load-balancer-managed-ip-count`** 。

若要更新现有群集，请运行以下命令。 还可以在创建群集时设置此参数，以获得多个托管出站公共 IP。

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-managed-outbound-ip-count 2
```

以上示例将 *myResourceGroup* 中 *myAKSCluster* 群集的托管出站公共 IP 数量设置为 *2*。 

创建群集时，还可以使用 **`load-balancer-managed-ip-count`** 参数设置初始托管的出站公共 ip 数，方法是在创建群集时追加 **`--load-balancer-managed-outbound-ip-count`** 参数，并将其设置为所需的值。 托管出站公共 IP 的默认数量为 1。

### <a name="provide-your-own-outbound-public-ips-or-prefixes"></a>提供自己的出站公共 Ip 或前缀

使用*标准*SKU 负载平衡器时，默认情况下，AKS 群集会自动在 AKS 管理的基础结构资源组中创建公共 IP，并将其分配给负载均衡器出站池。

AKS 创建的公共 IP 被视为 AKS 托管资源。 这意味着该公共 IP 的生命周期应由 AKS 管理，并且不需要直接在公共 IP 资源上执行任何用户操作。 或者，你可以在创建群集时分配你自己的自定义公共 IP 或公共 IP 前缀。 你的自定义 Ip 还可以更新现有群集的负载均衡器属性。

> [!NOTE]
> 自定义公共 IP 地址必须由用户创建和拥有。 由 AKS 创建的托管的公共 IP 地址不能用作自定义 IP，因为它可能会导致管理冲突。

在执行此操作之前，请确保满足配置出站 IP 或出站 IP 前缀所需的[先决条件和限制](../virtual-network/public-ip-address-prefix.md#constraints)。

#### <a name="update-the-cluster-with-your-own-outbound-public-ip"></a>用自己的出站公共 IP 更新群集

使用 [az network public-ip show][az-network-public-ip-show] 命令列出你的公共 IP 的 ID。

```azurecli-interactive
az network public-ip show --resource-group myResourceGroup --name myPublicIP --query id -o tsv
```

以上命令显示 *myResourceGroup* 资源组中 *myPublicIP* 公共 IP 的 ID。

使用 `az aks update` 带有参数的命令 **`load-balancer-outbound-ips`** ，通过公共 ip 更新群集。

下面的示例将 `load-balancer-outbound-ips` 参数与上一个命令的 id 结合使用。

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2>
```

#### <a name="update-the-cluster-with-your-own-outbound-public-ip-prefix"></a>用自己的出站公共 IP 前缀更新群集

你也可以将出口的公共 IP 前缀与标准 SKU 负载均衡器配合使用。 以下示例使用 [az network public-ip prefix show][az-network-public-ip-prefix-show] 命令列出公共 IP 前缀的 ID：

```azurecli-interactive
az network public-ip prefix show --resource-group myResourceGroup --name myPublicIPPrefix --query id -o tsv
```

以上命令显示 *myResourceGroup* 资源组中 *myPublicIPPrefix* 公共 IP 前缀的 ID。

以下示例将 load-balancer-outbound-ip-prefixes 参数与来自上一命令的 ID 配合使用。

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ip-prefixes <publicIpPrefixId1>,<publicIpPrefixId2>
```

#### <a name="create-the-cluster-with-your-own-public-ip-or-prefixes"></a>创建具有自己的公共 IP 或前缀的群集

在创建群集时，你可能想要使用自己的出口 IP 地址或 IP 前缀，以支持出口终结点允许列表等方案。 将上面所示的相同参数追加到群集创建步骤，可在群集生命周期的起始部分定义自己的公共 IP 和 IP 前缀。

结合 *load-balancer-outbound-ips* 参数使用 *az aks create* 命令可在启动时使用你的公共 IP 创建新的群集。

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2>
```

一开始就将 az aks create 命令与 load-balancer-outbound-ip-prefixes 参数配合使用，以便使用公共 IP 前缀创建新群集。

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --load-balancer-outbound-ip-prefixes <publicIpPrefixId1>,<publicIpPrefixId2>
```

### <a name="configure-the-allocated-outbound-ports"></a>配置分配的出站端口

> [!IMPORTANT]
> 如果你的群集上有应用程序，这些应用程序应与一组小型目标建立大量连接，例如 许多连接到 SQL DB 的前端实例都有很容易遇到 SNAT 端口耗尽 (用来从) 连接的端口。 在这些情况下，强烈建议在负载平衡器上增加分配的出站端口和出站前端 Ip。 增加应考虑到一个 (1) 额外的 IP 地址添加64k 个额外的端口，以便在所有群集节点上进行分配。


除非另外指定，否则 AKS 将使用标准负载均衡器定义的分配的出站端口的默认值。 此值在 AKS API 上为**null** ，在 SLB api 上为**0** ，如以下命令所示：

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

此输出并不意味着你有0个端口，而是[根据后端池大小使用自动出站端口分配][azure-lb-outbound-preallocatedports]，因此，如果群集具有50个或更少的节点，则会为每个节点分配1024个端口，因为从此处增加节点数量，则会为每个节点逐步获取更少的端口。


若要定义或增加分配的出站端口数，可以按照以下示例操作：


```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-managed-outbound-ip-count 7 \
    --load-balancer-outbound-ports 4000
```

此示例将为群集中的每个节点提供4000个分配的出站端口，使用7个 Ip，*每个节点有4000个端口 * 100 节点 = 400k total 端口数 < = 448k total 端口数 = 7 ip * 64k 个 ip 端口*。 这将允许你安全地缩放到100节点并具有默认的升级操作。 需要为升级和其他操作所需的其他节点分配足够的端口，这一点很重要。 AKS 默认为一个用于升级的缓冲区节点，在此示例中，在任何给定时间点都需要4000个可用端口。 如果使用[maxSurge 值](upgrade-cluster.md#customize-node-surge-upgrade-preview)，请将每个节点的出站端口乘以 maxSurge 值。

若要安全地跳到100节点，你必须添加更多的 Ip。


> [!IMPORTANT]
> 在自定义*allocatedOutboundPorts*之前，必须[计算所需的配额并检查要求，][requirements]以避免连接或缩放问题。

你还可以 **`load-balancer-outbound-ports`** 在创建群集时使用参数，但也必须指定 **`load-balancer-managed-outbound-ip-count`** 、 **`load-balancer-outbound-ips`** 或 **`load-balancer-outbound-ip-prefixes`** 。  例如：

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-sku standard \
    --load-balancer-managed-outbound-ip-count 2 \
    --load-balancer-outbound-ports 1024 
```

### <a name="configure-the-load-balancer-idle-timeout"></a>配置负载均衡器空闲超时

如果 SNAT 端口资源已经耗尽，那么在现有流释放 SNAT 端口之前出站流会失败。 负载均衡器会在流关闭时回收 SNAT 端口，而 AKS 配置的负载均衡器使用30分钟的空闲超时，从空闲流回收 SNAT 端口。
你还可以使用传输 (例如 **`TCP keepalives`**) 或 **`application-layer keepalives`** 刷新空闲流，并根据需要重置此空闲超时。 可以在以下示例中配置此超时值： 


```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-idle-timeout 4
```

如果你预期会有很多短暂的生存期连接，并且不存在长时间的连接并且可能具有很长的空闲时间，如利用 `kubectl proxy` 或 `kubectl port-forward` 考虑使用低超时值（如4分钟）。 而且，使用 TCP keepalive 时，足以在连接的一端启用它们。 例如，在服务器端启用它们只是为了重置流的空闲计时器，而这并不是双方启动 TCP keepalive 所必需的。 应用程序层（包括数据库客户端-服务器配置）也存在类似的概念。 检查服务器端对于特定于应用程序的 keepalive 存在哪些选项。

> [!IMPORTANT]
> 默认情况下，AKS 启用了 TCP 重置，并建议你将此配置保留在上，并利用它在你的方案中实现更可预测的应用程序行为。
> TCP RST 仅在 TCP 连接期间发送到已建立状态。 在[此处](../load-balancer/load-balancer-tcp-reset.md)阅读详细信息。

### <a name="requirements-for-customizing-allocated-outbound-ports-and-idle-timeout"></a>自定义分配的出站端口和空闲超时的要求

- 为 *allocatedOutboundPorts* 指定的值还必须是 8 的倍数。
- 你必须有足够的出站 IP 容量，取决于你的节点 Vm 的数量和所需的分配出站端口。 若要验证是否有足够的出站 IP 容量，请使用以下公式： 
 
outboundIP 数 \* 64,000 \> nodeVM 数 \* desiredAllocatedOutboundPort 数。
 
例如，如果你有 3个 nodeVM 和 50,000 个 desiredAllocatedOutboundPort，则需要至少 3 个 outboundIP。 建议在所需容量的基础上增加额外的出站 IP 容量。 此外，在计算出站 IP 容量时，必须考虑群集自动缩放程序和节点池升级的可能性。 对于群集自动缩放程序，请查看当前节点计数和最大节点计数，并使用较高的值。 对于升级，请考虑为允许升级的节点池添加一个额外的节点 VM。
 
- 将 *IdleTimeoutInMinutes* 设置为默认值 30 分钟之外的值时，请考虑你的工作负荷多长时间将需要出站连接。 还要考虑在 AKS 外部使用的“标准”SKU 负载平衡器的默认超时值是 4 分钟。 如果 *idletimeoutminutes* 值较准确地反映你的具体 AKS 工作负载，则有助于降低由于绑定不再使用的连接而导致的 SNAT 耗尽。

> [!WARNING]
> 更改*AllocatedOutboundPorts*和*IdleTimeoutInMinutes*的值可能会显著更改负载均衡器的出站规则的行为，但不应稍微进行更改，无需了解折衷情况和应用程序的连接模式，请[在更新][troubleshoot-snat]这些值之前查看 Azure 中的[负载均衡器出站规则][azure-lb-outbound-rules-overview]和[出站连接][azure-lb-outbound-connections]，以充分了解更改的影响。


## <a name="restrict-inbound-traffic-to-specific-ip-ranges"></a>限制到特定 IP 范围的入站流量

默认情况下，与负载均衡器的虚拟网络关联的网络安全组 (NSG) 包含一个允许所有入站外部流量的规则。 可以更新此规则，以便仅允许来自特定 IP 范围的入站流量。 以下清单使用 *loadBalancerSourceRanges* 来指定允许其发送入站外部流量的新 IP 范围：

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

## <a name="additional-customizations-via-kubernetes-annotations"></a>通过 Kubernetes 批注进行其他自定义

下面是类型为的 Kubernetes 服务支持的批注列表 `LoadBalancer` ，这些批注仅适用于**入站**流：

| Annotation | 值 | 描述
| ----------------------------------------------------------------- | ------------------------------------- | ------------------------------------------------------------ 
| `service.beta.kubernetes.io/azure-load-balancer-internal`         | `true` 或 `false`                     | 指定负载平衡器是否应为内部负载平衡器。 如果未设置，则默认为 public。
| `service.beta.kubernetes.io/azure-load-balancer-internal-subnet`  | 子网名称                    | 指定内部负载均衡器应绑定到的子网。 如果未设置，则默认为在云配置文件中配置的子网。
| `service.beta.kubernetes.io/azure-dns-label-name`                 | 公共 Ip 上 DNS 标签的名称   | 指定**公共**服务的 DNS 标签名称。 如果将其设置为空字符串，则不会使用公共 IP 中的 DNS 条目。
| `service.beta.kubernetes.io/azure-shared-securityrule`            | `true` 或 `false`                     | 指定应使用可与其他服务共享的 Azure 安全规则公开服务，并通过贸易规则来提高可公开的服务数量。 此批注依赖于 Azure 增强的网络安全组[安全规则](../virtual-network/security-overview.md#augmented-security-rules)功能。 
| `service.beta.kubernetes.io/azure-load-balancer-resource-group`   | 资源组的名称            | 指定负载平衡器公共 Ip 的资源组，该资源组与群集基础结构 (节点资源组) 不在同一资源组中。
| `service.beta.kubernetes.io/azure-allowed-service-tags`           | 允许的服务标记列表          | 指定以逗号分隔的允许[服务标记](../virtual-network/security-overview.md#service-tags)的列表。
| `service.beta.kubernetes.io/azure-load-balancer-tcp-idle-timeout` | TCP 空闲超时（分钟）          | 指定负载均衡器上的 TCP 连接空闲超时时间（以分钟为单位）。 默认值和最小值为4。 最大值为30。 必须是整数。
|`service.beta.kubernetes.io/azure-load-balancer-disable-tcp-reset` | `true`                                | 禁用 `enableTcpReset` SLB


## <a name="troubleshooting-snat"></a>SNAT 疑难解答

如果知道正在启动与同一目标 IP 地址和端口的多个出站 TCP 或 UDP 连接，并且观察到了出站连接失败，或者已建议你使用 PAT)  (预先分配的临时端口来耗尽 SNAT 端口，则有几个常规的缓解选项。 查看这些选项，确定可用且最适合自己的方案的选项。 一个或多个选项可能有助于管理此方案。 有关详细信息，请查看[出站连接故障排除指南](../load-balancer/troubleshoot-outbound-connection.md)。

通常，SNAT 耗尽的根本原因是建立和管理出站连接的方式出现了对立模式，或者可配置的计时器已更改，不再使用默认值。 请认真阅读本部分。

### <a name="steps"></a>步骤
1. 检查连接是否长时间处于空闲状态，并依赖于释放该端口的默认空闲超时时间。 如果是这样，则可能需要为方案减少30分钟的默认超时。
2. 调查应用程序如何建立出站连接（例如，通过代码评审或数据包捕获进行调查）。
3. 确定此活动是否为预期行为，或者应用程序是否行为异常。 使用 Azure Monitor 中的[指标](../load-balancer/load-balancer-standard-diagnostics.md)和[日志](../load-balancer/load-balancer-monitor-log.md)来证实你的发现。 例如，使用 SNAT 连接的 "失败" 类别。
4. 评估是否遵循适当的[模式](#design-patterns)。
5. 评估是否应通过[额外的出站 IP 地址和其他分配的出站端口](#configure-the-allocated-outbound-ports)来缓解 SNAT 端口耗尽。

### <a name="design-patterns"></a>设计模式
始终尽量利用连接重用和连接池。 这些模式可以避免资源耗尽问题，并使行为可预测。 在许多开发库和框架中，都可以找到这些模式的根源。

- 原子请求 (每个连接的请求) 通常不是良好的设计选择。 这种对立模式会限制缩放，降低性能并降低可靠性。 应该重复使用 HTTP/S 连接来减少连接数和关联的 SNAT 端口数。 应用程序规模会提高，性能提高，因为使用 TLS 时的握手、开销和加密操作成本降低。
- 如果使用的是群集/自定义 DNS，或 coreDNS 上的自定义上游服务器，请注意，当客户端未缓存 DNS 解析器结果时，DNS 可以在卷上引入多个单独的流。 请确保先自定义 coreDNS，而不是使用自定义 DNS 服务器，并定义良好的缓存值。
- UDP 流（例如 DNS 查找）根据空闲超时持续时间分配 SNAT 端口。 空闲超时越长，SNAT 端口上的压力越大。 使用较短的空闲超时（例如 4 分钟）。
使用连接池来调整连接卷。
- 切勿以静默方式丢弃 TCP 流，且不要依赖 TCP 计时器来清理流。 如果不允许 TCP 显式关闭连接，中间系统和终结点上将保持已分配状态，使 SNAT 端口不可用于其他连接。 此模式可能会触发应用程序故障和 SNAT 耗尽。
- 在对造成的影响了解不深的情况下，请不要更改与 OS 级别的 TCP 关闭相关的计时器值。 当 TCP 堆栈恢复时，如果连接的终结点的预期不匹配，则应用程序的性能可能会受到负面影响。 希望更改计时器通常是一种基本设计问题的迹象。 查看以下建议：


以上示例将更新规则，以便仅允许来自 *MY_EXTERNAL_IP_RANGE* 范围的入站外部流量。 如果将*MY_EXTERNAL_IP_RANGE*替换为内部子网 IP 地址，则流量仅限群集内部 ip。 这将不允许 Kubernetes 群集外部的客户端访问负载均衡器。

## <a name="moving-from-a-basic-sku-load-balancer-to-standard-sku"></a>从基本 SKU 负载均衡器移动到标准 SKU

如果现有的群集包含基本 SKU 负载均衡器，则在进行迁移以使用包含标准 SKU 负载均衡器的群集时，需要注意一些重要的行为差异。

例如，假设只能在创建群集时定义群集的 `load-balancer-sku` 类型，则通过蓝/绿部署迁移群集是常用的做法。 但是，*基本*Sku 负载均衡器使用*基本 sku* Ip 地址，这些地址与*标准 sku*负载平衡器不兼容，因为它们需要*标准 sku* ip 地址。 在迁移群集以升级负载均衡器 SKU 时，需要提供一个具有兼容 IP 地址 SKU 的新 IP 地址。

有关如何迁移群集的更多注意事项，请访问[有关迁移注意事项的文档](aks-migration.md)，以查看迁移时要考虑的重要主题列表。 以下限制也是在 AKS 中使用标准 SKU 负载均衡器时要注意的重要行为差异。

## <a name="limitations"></a>限制

创建和管理支持标准 SKU 负载均衡器的 AKS 群集时存在以下限制：

* 至少需要指定一个公共 IP 或 IP 前缀来允许 AKS 群集的出口流量。 还需要公共 IP 或 IP 前缀来维护控制平面和代理节点之间的连接，并保持与早期版本的 AKS 的兼容性。 可以使用以下选项指定标准 SKU 负载均衡器的公共 IP 或 IP 前缀：
    * 提供自己的公共 IP。
    * 提供自己的公共 IP 前缀。
    * 指定最大为 100 的数字，以允许 AKS 群集在其所在的同一个资源组（名称通常以 *MC_* 开头）中创建多个标准 SKU 公共 IP。 AKS 会将公共 IP 分配到标准 SKU 负载均衡器。 默认情况下，如果未指定公共 IP、公共 IP 前缀或 IP 数目，系统会在 AKS 群集所在的同一个资源组中自动创建一个公共 IP。 还必须允许公共地址，并避免创建任何禁止创建 IP 的 Azure Policy。
* 不能将 AKS 创建的公共 IP 作为自定义自带公共 IP 地址重复使用。 所有自定义 IP 地址都必须由用户创建和管理。
* 只有在创建 AKS 群集时，才能定义负载均衡器 SKU。 创建 AKS 群集后，无法更改负载平衡器 SKU。
* 在一个群集中只能使用一种类型的负载均衡器 SKU（基本或标准）。
* 标准 SKU 负载均衡器仅支持标准 SKU IP 地址。 


## <a name="next-steps"></a>后续步骤

在 [Kubernetes 服务文档][kubernetes-services]中详细了解 Kubernetes 服务。

若要详细了解如何使用内部负载均衡器进行入站流量，请[参阅 AKS 内部负载均衡器文档](internal-lb.md)。

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
[azure-lb-comparison]: ../load-balancer/skus.md
[azure-lb-outbound-rules]: ../load-balancer/load-balancer-outbound-connections.md#outboundrules
[azure-lb-outbound-connections]: ../load-balancer/load-balancer-outbound-connections.md
[azure-lb-outbound-preallocatedports]: ../load-balancer/load-balancer-outbound-connections.md#preallocatedports
[azure-lb-outbound-rules-overview]: ../load-balancer/load-balancer-outbound-connections.md#outboundrules
[install-azure-cli]: /cli/azure/install-azure-cli
[internal-lb-yaml]: internal-lb.md#create-an-internal-load-balancer
[kubernetes-concepts]: concepts-clusters-workloads.md
[use-kubenet]: configure-kubenet.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[requirements]: #requirements-for-customizing-allocated-outbound-ports-and-idle-timeout
[use-multiple-node-pools]: use-multiple-node-pools.md
[troubleshoot-snat]: #troubleshooting-snat
