---
title: 使用公共负载均衡器
titleSuffix: Azure Kubernetes Service
description: 了解如何在 Azure Kubernetes 服务 (AKS) 中使用标准 SKU 公共负载均衡器来公开服务。
services: container-service
ms.topic: article
ms.date: 06/14/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: a58b00018f6ac89f024661d8d3f50ea5249e620b
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2020
ms.locfileid: "89182116"
---
# <a name="use-a-public-standard-load-balancer-in-azure-kubernetes-service-aks"></a>在 Azure Kubernetes 服务 (AKS) 中使用公共标准负载均衡器

Azure 负载均衡器是开放式系统互连 (OSI) 模型的 L4，支持入站和出站场景。 负载均衡器将抵达负载均衡器前端的入站流量分配到后端池实例。

公共负载均衡器与 AKS 集成时有两个用途：

1. 提供到 AKS 虚拟网络内群集节点的出站连接。 它通过将节点专用 IP 地址转换为作为其“出站池”一部分的公共 IP 地址来实现此目标。
2. 通过类型为 `LoadBalancer` 的 Kubernetes 服务提供对应用程序的访问。 使用负载均衡器你可轻松缩放应用程序，并创建高度可用的服务。

内部（或专用）负载平衡器用于仅在前端允许专用 IP 的情况。 内部负载均衡器用于对虚拟网络内部的流量进行负载均衡。 负载均衡器前端还可在混合场景中从本地网络进行访问。

本文档介绍与公共负载均衡器的集成。 如需了解内部负载均衡器集成，请参阅 [AKS 内部负载均衡器文档](internal-lb.md)。

## <a name="before-you-begin"></a>准备阶段

Azure 负载均衡器以两种 SKU 提供：“基本”和“标准” 。 默认情况下，创建 AKS 群集时将使用标准 SKU。 使用 *标准* SKU 可以访问附加功能，例如更大的后端池、 [**多个节点池**](use-multiple-node-pools.md)和 [**可用性区域**](availability-zones.md)。 这是推荐的 AKS 的负载均衡器 SKU。

有关基本和标准 SKU 的详细信息，请参阅 [Azure 负载均衡器 SKU 的比较][azure-lb-comparison]。 

本文假定你拥有具有标准 SKU Azure 负载均衡器的 AKS 群集，并介绍如何使用和配置负载均衡器的一些功能和特性。 如果需要 AKS 群集，请参阅 AKS 快速入门[使用 Azure CLI][aks-quickstart-cli] 或[使用 Azure 门户][aks-quickstart-portal]。

> [!IMPORTANT]
> 如果不想利用 Azure 负载均衡器提供出站连接，而希望为此拥有自己的网关、防火墙或代理，则可以使用[出站类型作为 UserDefinedRouting (UDR)](egress-outboundtype.md)，跳过创建负载均衡器出站池和相应的前端 IP 的操作。 出站类型定义群集的出口方法，它默认为负载均衡器类型。

## <a name="use-the-public-standard-load-balancer"></a>使用公共标准负载均衡器

创建默认出站类型为负载均衡器的 AKS 群集后，群集也可使用负载均衡器公开服务。

为此，你可创建类型为 `LoadBalancer` 的公共服务，如以下示例所示。 首先创建名为 `public-svc.yaml` 的服务清单：

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

使用 [kubectl apply][kubectl-apply] 部署公共服务清单，并指定 YAML 清单的名称：

```azurecli-interactive
kubectl apply -f public-svc.yaml
```

将为 Azure 负载均衡器配置一个支持此新服务的新公共 IP。 由于 Azure 负载均衡器可以有多个前端 IP，因此部署的每个新服务都将获得唯一的专用前端 IP。

你可以确认你的服务已创建，并且通过运行来配置负载均衡器，例如：

```azurecli-interactive
kubectl get service public-svc
```

```console
NAMESPACE     NAME          TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)         AGE
default       public-svc    LoadBalancer   10.0.39.110    52.156.88.187   80:32068/TCP    52s
```

查看服务详细信息时，为负载均衡器上的该服务创建的公共 IP 地址显示在“EXTERNAL-IP”列中。 可能需要一两分钟，IP 地址才会从 \<pending\> 更改为实际的公共 IP 地址，如以下示例所示。

## <a name="configure-the-public-standard-load-balancer"></a>配置公共标准负载均衡器

使用标准 SKU 公共负载均衡器时，可以在创建时或通过更新群集自定义一组选项。 通过这些选项，你可自定义负载均衡器以满足工作负载需求，并应相应地进行审查。 使用标准负载均衡器，你可以执行以下操作：

* 设置或缩放受管理出站 IP 的数量
* 自带自定义[出站 IP 或出站 IP 前缀](#provide-your-own-outbound-public-ips-or-prefixes)
* 自定义分配给群集的每个节点的出站端口数
* 为空闲连接配置超时设置

### <a name="scale-the-number-of-managed-outbound-public-ips"></a>缩放受管理出站公共 IP 的数量

除了入站连接以外，Azure 负载均衡器还提供从虚拟网络的出站连接。 使用出站规则可以更方便地配置公共标准负载均衡器的出站网络地址转换。

与所有负载均衡器规则一样，出站规则遵循负载均衡和入站 NAT 规则的类似语法：

***前端 IP + 参数 + 后端池***

出站规则为后端池识别的、要转换为前端的所有虚拟机配置出站 NAT。 参数针对出站 NAT 算法提供更精细的控制。

尽管出站规则只能配合单个公共 IP 地址使用，但出站规则减轻了缩放出站 NAT 的负担。 规划大规模部署场景时可以使用多个 IP 地址，并可以使用出站规则来缓解容易出现 SNAT 耗尽的模式。 前端提供的每个附加 IP 地址可提供 64,000 个临时端口，供负载均衡器用作 SNAT 端口。 

结合默认创建的受管理出站公共 IP 使用标准 SKU 负载均衡器时，可以使用 `load-balancer-managed-ip-count` 参数来调整受管理出站公共 IP 的数量。

若要更新现有群集，请运行以下命令。 还可以在创建群集时设置此参数，以获得多个托管出站公共 IP。

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-managed-outbound-ip-count 2
```

以上示例将 *myResourceGroup* 中 *myAKSCluster* 群集的托管出站公共 IP 数量设置为 *2*。 

还可以在创建群集时，通过追加 `--load-balancer-managed-outbound-ip-count` 参数并将其设置为所需的值，使用 `load-balancer-managed-ip-count` 参数来设置托管出站公共 IP 的初始数量 。 托管出站公共 IP 的默认数量为 1。

### <a name="provide-your-own-outbound-public-ips-or-prefixes"></a>提供自己的出站公共 IP 或前缀

使用标准 SKU 负载均衡器时，默认情况下，AKS 群集会自动在 AKS 管理的基础结构资源组中创建公共 IP 并将其分配给负载均衡器出站池。

AKS 创建的公共 IP 被视为受 AKS 管理的资源。 这意味着该公共 IP 的生命周期由 AKS 管理，并且用户不需要直接对公共 IP 资源执行操作。 或者，你可以在群集创建时分配自己的自定义公共 IP 或公共 IP 前缀。 还可以在现有群集的负载均衡器属性上更新自定义 IP。

> [!NOTE]
> 自定义公共 IP 地址必须由用户创建和拥有。 由 AKS 创建的托管公共 IP 地址不能重新用于自带的自定义 IP，因为它可能会导致管理冲突。

在执行此操作前，请确保满足配置出站 IP 或出站 IP 前缀所需的[先决条件和限制](../virtual-network/public-ip-address-prefix.md#constraints)。

#### <a name="update-the-cluster-with-your-own-outbound-public-ip"></a>使用自己的出站公共 IP 更新群集

使用 [az network public-ip show][az-network-public-ip-show] 命令列出公共 IP 的 ID。

```azurecli-interactive
az network public-ip show --resource-group myResourceGroup --name myPublicIP --query id -o tsv
```

以上命令显示 *myResourceGroup* 资源组中 *myPublicIP* 公共 IP 的 ID。

将 `az aks update` 命令与 `load-balancer-outbound-ips` 参数一起使用，以使用公共 IP 更新群集。

以下示例结合前一命令返回的 ID 使用 `load-balancer-outbound-ips` 参数。

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2>
```

#### <a name="update-the-cluster-with-your-own-outbound-public-ip-prefix"></a>使用自己的出站公共 IP 前缀更新群集

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

#### <a name="create-the-cluster-with-your-own-public-ip-or-prefixes"></a>使用自己的公共 IP 或前缀创建群集

你可能希望在创建群集时为出口提供自己的 IP 地址或 IP 前缀，以支持将出口终结点添加到允许列表这样的方案。 将上面所示的相同参数追加到群集创建步骤，可在群集生命周期的起始部分定义自己的公共 IP 和 IP 前缀。

结合 *load-balancer-outbound-ips* 参数使用 *az aks create* 命令可在启动时使用你的公共 IP 创建新的群集。

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2>
```

结合 *load-balancer-outbound-ip-prefixes* 参数使用 *az aks create* 命令可在启动时使用你的公共 IP 前缀创建新的群集。

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --load-balancer-outbound-ip-prefixes <publicIpPrefixId1>,<publicIpPrefixId2>
```

### <a name="configure-the-allocated-outbound-ports"></a>配置分配的出站端口

> [!IMPORTANT]
> 如果你的群集上有一些应用程序，它们预期会与一小组目标集（例如， 许多连接到 SQL DB 的前端实例）建立大量连接，你的情况是很容易遇到 SNAT 端口耗尽（要连接的端口用完）。 对于这些场景，强烈建议增加负载均衡器上分配的出站端口和出站前端 IP。 此增加操作应考虑一 (1) 个附加 IP 地址添加 64,000 个附加端口，以便分布在所有群集节点上。


除非另有说明，否则 AKS 将使用标准负载均衡器在配置时所定义的分配出站端口的默认值。 在 AKS API 上此值为 null，或在 SLB API 上为 0，如以下命令所示 ：

```azurecli-interactive
NODE_RG=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
az network lb outbound-rule list --resource-group $NODE_RG --lb-name kubernetes -o table
```

上述命令将列出负载均衡器的出站规则，例如：

```console
AllocatedOutboundPorts    EnableTcpReset    IdleTimeoutInMinutes    Name             Protocol    ProvisioningState    ResourceGroup
------------------------  ----------------  ----------------------  ---------------  ----------  -------------------  -------------
0                         True              30                      aksOutboundRule  All         Succeeded            MC_myResourceGroup_myAKSCluster_eastus  
```

此输出并不意味着你有 0 个端口，而是你利用[基于后端池大小的自动出站端口分配][azure-lb-outbound-preallocatedports]，因此，举例来说，如果群集有 50 个或更少的节点，则为每个节点分配 1024 个端口，当你增加节点数时，每个节点的端口数将逐渐减少。


若要定义或增加分配的出站端口的数量，可以按照以下示例操作：


```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-managed-outbound-ip-count 7 \
    --load-balancer-outbound-ports 4000
```

此示例假设为群集中的每个节点提供 4000 个分配的出站端口以及 7 个 IP，则你会得到以下结果：每个节点 4000 个端口 * 100 个节点 = 400,000 个总端口 < = 448,000 个总端口 = 7 个 IP * 每个 IP 64,000 端口。 这样你便可安全地缩放到 100 个节点，并执行默认升级操作。 为升级和其他操作所需的其他节点分配足够的端口至关重要。 AKS 默认为一个缓冲区节点用于升级，在此示例中，这要求在任何给定时间点有 4000 个可用端口。 如果使用 [maxSurge 值](upgrade-cluster.md#customize-node-surge-upgrade-preview)，请将每个节点的出站端口乘以 maxSurge 值。

要安全地超过 100 个节点，必须添加更多 IP。


> [!IMPORTANT]
> 在自定义 allocatedOutboundPort 之前，必须[计算所需的配额并检查要求][requirements]以避免连接或缩放问题。

创建群集时，还可以使用 `load-balancer-outbound-ports` 参数，但必须同时指定 `load-balancer-managed-outbound-ip-count`、`load-balancer-outbound-ips` 或 `load-balancer-outbound-ip-prefixes`   。  例如：

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-sku standard \
    --load-balancer-managed-outbound-ip-count 2 \
    --load-balancer-outbound-ports 1024 
```

### <a name="configure-the-load-balancer-idle-timeout"></a>配置负载均衡器的空闲超时

如果 SNAT 端口资源已经耗尽，那么在现有流释放 SNAT 端口之前出站流会失败。 当流关闭时，负载均衡器将回收 SNAT 端口，AKS 配置的负载均衡器将使用 30 分钟空闲超时回收空闲流中的 SNAT 端口。
还可以使用传输（例如 `TCP keepalives`）或 `application-layer keepalives` 刷新空闲流，并在必要时重置此空闲超时 。 可以按照以下示例配置此超时： 


```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-idle-timeout 4
```

如果你希望具有许多短生存期的连接，而不想有长生存期且可能有长时间空闲的连接（例如利用 `kubectl proxy` 或 `kubectl port-forward`），请考虑使用低超时值（如 4 分钟）。 另外，使用 TCP keepalive 时，在连接的一端启用它们就足够了。 例如，若要重置流的空闲计时器，在服务器端启用它们就足够了，没有必要在两端都启动 TCP keepalive。 应用程序层（包括数据库客户端-服务器配置）也存在类似的概念。 检查服务器端对于特定于应用程序的 keepalive 存在哪些选项。

> [!IMPORTANT]
> AKS 默认启用“空闲时执行 TCP 重置”，建议保持此配置，并利用它在你的场景中实现可预测性更高的应用程序行为。
> 只有在 TCP 连接的状态为“已建立”时才会发送 TCP RST。 在[此处](../load-balancer/load-balancer-tcp-reset.md)阅读详细信息。

### <a name="requirements-for-customizing-allocated-outbound-ports-and-idle-timeout"></a>自定义分配的出站端口和空闲超时的要求

- 为 *allocatedOutboundPorts* 指定的值还必须是 8 的倍数。
- 你必须有足够的出站 IP 容量，具体取决于节点 VM 和所需的已分配出站端口的数量。 若要验证是否有足够的出站 IP 容量，请使用以下公式： 
 
*outboundIPs* \* 64,000 \> *nodeVMs* \* *desiredAllocatedOutboundPorts*。
 
例如，如果你有 3 个 *nodeVM* 和 50,000 个 *desiredAllocatedOutboundPort*，则至少需要有 3 个 *outboundIP*。 建议你在所需容量的基础上增加额外的出站 IP 容量。 此外，在计算出站 IP 容量时，必须考虑群集自动缩放程序和节点池升级的可能性。 对于群集自动缩放程序，请查看当前节点计数和最大节点计数，并使用较高的值。 对于升级，请考虑为允许升级的节点池添加一个额外的节点 VM。

- 将 *IdleTimeoutInMinutes* 设置为默认值 30 分钟之外的值时，请考虑你的工作负荷多长时间将需要出站连接。 还要考虑在 AKS 外部使用的“标准”SKU 负载平衡器的默认超时值是 4 分钟。 如果 *idletimeoutminutes* 值较准确地反映你的具体 AKS 工作负载，则有助于降低由于绑定不再使用的连接而导致的 SNAT 耗尽。

> [!WARNING]
> 更改 AllocatedOutboundPort 和 IdleTimeoutInMinute 的值可能会显著更改负载均衡器的出站规则的行为，因此在不了解弊端和应用程序连接模式的情况下，不应轻易去做，请查看[下面的 SNAT 故障排除部分][troubleshoot-snat]，并在更新这些值之前查看[负载均衡器出站规则][azure-lb-outbound-rules-overview]和 [Azure 中的出站连接][azure-lb-outbound-connections]，以充分了解更改的影响 。

## <a name="restrict-inbound-traffic-to-specific-ip-ranges"></a>将入站流量限制为特定 IP 范围

以下清单使用 *loadBalancerSourceRanges* 来指定允许其发送入站外部流量的新 IP 范围：

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

> [!NOTE]
> 接收入站流量从负载均衡器流向 AKS 群集的虚拟网络。 虚拟网络具有一个 (NSG) 的网络安全组，该安全组允许来自负载均衡器的所有入站流量。 此 NSG 使用类型为*LoadBalancer*的[服务标记][service-tags]，以允许来自负载均衡器的流量。

## <a name="maintain-the-clients-ip-on-inbound-connections"></a>维护入站连接上的客户端 IP

默认情况下，在 [Kubernetes](https://kubernetes.io/docs/tutorials/services/source-ip/#source-ip-for-services-with-type-loadbalancer) 和 AKS 中，`LoadBalancer` 类型的服务在连接到 Pod 时不会持久保留客户端的 IP 地址。 传递到 Pod 的数据包上的源 IP 将是节点的专用 IP。 若要维护客户端的 IP 地址，必须 `service.spec.externalTrafficPolicy` `local` 在服务定义中将设置为。 以下清单显示了一个示例：

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
spec:
  type: LoadBalancer
  externalTrafficPolicy: Local
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

## <a name="additional-customizations-via-kubernetes-annotations"></a>通过 Kubernetes 注释进行其他自定义

下面是类型 `LoadBalancer` 的 Kubernetes 服务支持的注释列表，这些注释仅适用于入站流：

| Annotation | Value | 描述
| ----------------------------------------------------------------- | ------------------------------------- | ------------------------------------------------------------ 
| `service.beta.kubernetes.io/azure-load-balancer-internal`         | `true` 或 `false`                     | 指定负载均衡器是否应为“内部”。 如果未设置，则默认为 public。
| `service.beta.kubernetes.io/azure-load-balancer-internal-subnet`  | 子网的名称                    | 指定内部负载均衡器应绑定到的子网。 如果未设置，则默认为在云配置文件中配置的子网。
| `service.beta.kubernetes.io/azure-dns-label-name`                 | 公共 IP 上的 DNS 标签的名称   | 指定公共服务的 DNS 标签的名称。 如果设置为空字符串，则不会使用公共 IP 中的 DNS 条目。
| `service.beta.kubernetes.io/azure-shared-securityrule`            | `true` 或 `false`                     | 指定应使用可能与其他服务共享的 Azure 安全规则公开服务，交易规则的特定性，以增加可公开的服务数量。 此注释依赖于网络安全组的 Azure [扩充式安全规则](../virtual-network/security-overview.md#augmented-security-rules)功能。 
| `service.beta.kubernetes.io/azure-load-balancer-resource-group`   | 资源组的名称            | 指定与群集基础结构（节点资源组）不在同一资源组中的负载均衡器公共 IP 的资源组。
| `service.beta.kubernetes.io/azure-allowed-service-tags`           | 允许的服务标记列表          | 指定以逗号隔开的允许[服务标记][service-tags]的列表。
| `service.beta.kubernetes.io/azure-load-balancer-tcp-idle-timeout` | TCP 空闲超时（以分钟为单位）          | 指定 TCP 连接空闲超时在负载均衡器上发生的时间（以分钟为单位）。 默认值和最小值为 4。 最大值为 30。 必须为整数。
|`service.beta.kubernetes.io/azure-load-balancer-disable-tcp-reset` | `true`                                | 为 SLB 禁用 `enableTcpReset`


## <a name="troubleshooting-snat"></a>SNAT 疑难解答

如果知道正在启动与同一目标 IP 地址和端口的多个出站 TCP 或 UDP 连接，观察失败的出站连接，或者支持人员通知已耗尽 SNAT 端口（PAT 使用的预先分配的临时端口），则有几个常见缓解选项可供选择。 查看这些选项，确定可用且最适合自己的方案的选项。 一个或多个选项可能有助于管理此方案。 有关详细信息，请查看[出站连接故障排除指南](../load-balancer/troubleshoot-outbound-connection.md)。

通常，SNAT 耗尽的根本原因是建立和管理出站连接的方式出现了对立模式，或者可配置的计时器已更改，不再使用默认值。 请认真阅读本部分。

### <a name="steps"></a>步骤
1. 检查连接是否长时间处于空闲状态，以及是否依靠默认的空闲超时释放该端口。 如果是，可能需要为你的场景减少 30 分钟的默认超时。
2. 调查应用程序如何建立出站连接（例如，通过代码评审或数据包捕获进行调查）。
3. 确定此活动是否为预期行为，或者应用程序是否行为异常。 使用 Azure Monitor 中的[指标](../load-balancer/load-balancer-standard-diagnostics.md)和[日志](../load-balancer/load-balancer-monitor-log.md)来证实发现结果。 例如，对“SNAT 连接”指标使用“失败”类别。
4. 评估是否遵循适当的[模式](#design-patterns)。
5. 评估[更多出站 IP 地址 + 更多分配的出站端口](#configure-the-allocated-outbound-ports)是否应可以缓解 SNAT 端口耗尽的问题。

### <a name="design-patterns"></a>设计模式
始终尽量利用连接重用和连接池。 这些模式可以避免资源耗尽问题，并使行为可预测。 在许多开发库和框架中，都可以找到这些模式的根源。

- 原子请求（每个连接一个请求）是一个通常并不良好的设计选项。 这种对立模式会限制缩放，降低性能并降低可靠性。 应该重复使用 HTTP/S 连接来减少连接数和关联的 SNAT 端口数。 由于使用 TLS 时可以减少握手次数、系统开销以及加密操作的开销，因此应用规模与性能都会提高。
- 如果你使用的是群集/自定义 DNS，或者 coreDNS 上的自定义上游服务器，请记住，当客户端不缓存 DNS 解析器结果时，DNS 可以在卷上引入许多单独的流。 请确保首先自定义 coreDNS 而不是使用自定义 DNS 服务器，并定义合适的缓存值。
- UDP 流（例如 DNS 查找）根据空闲超时持续时间分配 SNAT 端口。 空闲超时越长，SNAT 端口上的压力越大。 使用较短的空闲超时（例如 4 分钟）。
使用连接池来调整连接卷。
- 切勿以静默方式丢弃 TCP 流，且不要依赖 TCP 计时器来清理流。 如果不允许 TCP 显式关闭连接，中间系统和终结点上将保持已分配状态，使 SNAT 端口不可用于其他连接。 此模式可能会触发应用程序故障和 SNAT 耗尽。
- 在对造成的影响了解不深的情况下，请不要更改与 OS 级别的 TCP 关闭相关的计时器值。 当某个连接的终结点不符合预期时，尽管 TCP 堆栈会恢复，但应用程序的性能可能会受负面影响。 希望更改计时器往往意味着底层设计出现了问题。 查看以下建议：


以上示例将更新规则，以便仅允许来自 *MY_EXTERNAL_IP_RANGE* 范围的入站外部流量。 如果将 MY_EXTERNAL_IP_RANGE 替换为内部子网 IP 地址，则流量仅限于群集内部 IP。 这样，来自 Kubernetes 群集外部的客户端不能访问负载均衡器。

## <a name="moving-from-a-basic-sku-load-balancer-to-standard-sku"></a>从基本 SKU 负载均衡器转移到标准 SKU

如果现有的群集包含基本 SKU 负载均衡器，则在进行迁移以使用包含标准 SKU 负载均衡器的群集时，需要注意一些重要的行为差异。

例如，假设只能在创建群集时定义群集的 `load-balancer-sku` 类型，则通过蓝/绿部署迁移群集是常用的做法。 但是，基本 SKU 负载均衡器使用基本 SKU IP 地址，而这些地址与标准 SKU 负载均衡器不兼容，因为这些负载均衡器需要标准 SKU IP 地址   。 在迁移群集以升级负载均衡器 SKU 时，需要提供一个具有兼容 IP 地址 SKU 的新 IP 地址。

有关如何迁移群集的更多注意事项，请访问[有关迁移注意事项的文档](aks-migration.md)，以查看迁移时要考虑的重要主题列表。 以下限制也是在 AKS 中使用标准 SKU 负载均衡器时要注意的重要行为差异。

## <a name="limitations"></a>限制

创建和管理支持标准 SKU 负载均衡器的 AKS 群集时存在以下限制：

* 至少需要指定一个公共 IP 或 IP 前缀来允许 AKS 群集的出口流量。 此外，需要使用公共 IP 或 IP 前缀来保持控制平面与代理节点之间的连接，以及保持与旧版 AKS 的兼容性。 可以使用以下选项指定标准 SKU 负载均衡器的公共 IP 或 IP 前缀：
    * 提供自己的公共 IP。
    * 提供自己的公共 IP 前缀。
    * 指定最大为 100 的数字，以允许 AKS 群集在其所在的同一个资源组（名称通常以 *MC_* 开头）中创建多个标准 SKU 公共 IP。 AKS 会将公共 IP 分配到标准 SKU 负载均衡器。 默认情况下，如果未指定公共 IP、公共 IP 前缀或 IP 数目，系统会在 AKS 群集所在的同一个资源组中自动创建一个公共 IP。 此外，必须允许公共地址，并避免创建任何会阻止创建 IP 的 Azure Policy。
* 由 AKS 创建的公共 IP 不能重复使用为自定义自带的公共 IP 地址。 所有自定义 IP 地址必须由用户创建和管理。
* 只能在创建 AKS 群集时定义负载均衡器 SKU。 创建 AKS 群集后，无法更改负载均衡器 SKU。
* 在一个群集中只能使用一种类型的负载均衡器 SKU（基本或标准）。
* 标准 SKU 负载均衡器仅支持标准 SKU IP 地址。 


## <a name="next-steps"></a>后续步骤

在 [Kubernetes 服务文档][kubernetes-services]中详细了解 Kubernetes 服务。

如需详细了解使用入站流量的内部负载均衡器，请参阅 [AKS 内部负载均衡器文档](internal-lb.md)。

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
[service-tags]: ../virtual-network/security-overview.md#service-tags
