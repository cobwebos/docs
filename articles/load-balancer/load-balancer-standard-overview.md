---
title: "Azure 负载均衡器标准版概述 | Microsoft Docs"
description: "Azure 负载均衡器标准版功能概述"
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: 
ms.assetid: 
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2017
ms.author: kumud
ms.openlocfilehash: 0ed8d3432a988c468260589cfe12090529c403d7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="azure-load-balancer-standard-overview-preview"></a>Azure 负载均衡器标准版（预览版）概述

将 Azure 负载均衡器标准 SKU 和公共 IP 标准 SKU 结合使用，可以构建高度可缩放且可靠的体系结构。  使用负载均衡器标准版的应用程序可以利用新的功能，此外，还能降级延迟、提高吞吐量，以及针对所有 TCP 和 UDP 应用程序的数百万个流进行缩放。

>[!NOTE]
> 负载均衡器标准 SKU 目前以预览版提供。 在预览期，该功能的可用性和可靠性级别可能与正式版不同。 有关详细信息，请参阅 [Microsoft Azure 预览版 Microsoft Azure 补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 请将正式版[负载均衡器基本 SKU](load-balancer-overview.md) 用于生产服务。  与此预览版关联的功能[可用性区域](https://aka.ms/availabilityzones)和 [HA 端口](https://aka.ms/haports)目前需要单独注册。 除了负载均衡器[标准预览版](#preview-sign-up)外，请按照相应说明注册。

## <a name="why-use-load-balancer-standard"></a>为何使用负载均衡器标准版

从小规模部署到大型复杂的多区域体系结构的任何虚拟数据中心都可以充分负载均衡器标准版的以下功能：

- 可以使用负载均衡器标准版实现[企业级缩放](#enterprisescale)。  可对虚拟网络中的最多 1000 个任何虚拟机 (VM) 实例使用负载均衡器标准版。

- 提供[新的诊断见解](#diagnosticinsights)，让客户了解、管理虚拟数据中心的这个重要组件并对其进行故障排除。 使用 Azure Monitor（预览版）可以根据终结点运行状况探测、TCP 连接尝试和出站连接来显示、筛选和分组新的多维指标，以便持续度量从前端到 VM 的数据路径运行状况。

- [网络安全组](#nsg)对于与负载均衡器或公共 IP 的标准 SKU 关联的任何 VM 实例现在都是必需的，它们提供增强的安全性。

- [HA 端口提供较高的可靠性](#highreliability)，并可针对网络虚拟设备和其他应用程序方案进行缩放。 HA 端口可将内部负载均衡器前端中的所有端口负载均衡到 VM 实例池。

- [出站连接](#outboundconnections)现在使用新的 SNAT 端口分配模型，提供更高的复原能力和缩放性。

- 可以使用[包含可用性区域的负载均衡器标准版](#availabilityzones)来构建区域冗余的体系结构和区域体系结构，两者都具有跨区域负载均衡功能。 无需依赖于 DNS 记录即可实现区域冗余：默认情况下，单个 IP 地址就能实现区域冗余，可跨所有可用性区域访问区域 VNet 中的任何 VM。


可以在仍旧支持以下基本方案的公共或内部配置中使用负载均衡器标准版：

- 将入站流量负载均衡到正常的后端实例。
- 以端口转发的形式将入站流量传送到单个后端实例。
- 转换从 VNet 中的专用 IP 地址发往公共 IP 地址的出站流量。

### <a name = "enterprisescale"></a>企业级缩放

 设计高性能虚拟数据中心时，可以使用负载均衡器标准版。 可以在后端池中使用独立的 VM 实例或最多 1000 个实例虚拟机规模集，支持任何 TCP 或 UDP 应用程序。 使用负载均衡器标准版，应用程序仍可利用较低的转发延迟、较高的吞吐量性能，并可针对完全托管的 Azure 服务的数百万个流进行缩放。
 
负载均衡器标准版可将流量转发到区域 VNet 中的任何 VM 实例。 后端池大小最高可达 1000 个实例，其中可以采用以下任意组合：

- 无可用性集的独立 VM
- 有可用性集的独立 VM
- 最多包含 1000 个实例的虚拟机规模集（虚拟机规模集）
- 多个虚拟机规模集
- VM 与虚拟机规模集的混合形式

在可用性集方面不再有任何要求，但可以选择使用可用性集，以利用它们提供的其他优势。

### <a name = "diagnosticinsights"></a>诊断见解

负载均衡器标准版为公共和内部负载均衡器配置提供新的多维诊断功能。 这些新指标通过 Azure Monitor（预览版）提供，并利用所有相关的功能，包括与各种下游使用者集成的功能。

| 指标 | 说明 |
| --- | --- |
| VIP 可用性 | 负载均衡器标准版持续运用从区域内部到负载均衡器前端，最后再到支持 VM 的 SDN 堆栈的数据路径。 只要保留正常实例，这种度量就会遵循应用程序负载均衡的流量所用的相同路径，并验证客户可能正在使用的数据路径。 度量对于应用程序不可见，且不会造成干扰。|
| DIP 可用性 | 负载均衡器标准版使用分布式运行状况探测服务，根据配置的值监视应用程序终结点的运行状况。  此指标提供负载均衡器池中每个实例终结点的聚合视图或按终结点筛选的视图。  可以查看负载均衡器如何根据运行状况探测配置的指示了解应用程序的运行状况。
| SYN 数据包 | 负载均衡器标准版不会终止 TCP 连接或者与 TCP 或 UDP 数据包流交互；流及其握手始终在源与 VM 实例之间发生。 若要更好地排查所有 TCP 协议方案的问题，可以使用此指标了解做出了多少次 TCP 连接尝试。 此指标报告已收到的 TCP SYN 数据包数目，可以反映尝试与服务建立连接的客户端。|
| SNAT 连接 | 负载均衡器标准版报告公共 IP 地址前端上伪装的出站连接数。  SNAT 端口是可耗竭性资源，此指标可以指出应用程序依赖于 SNAT 进行出站发起连接的程度有多高。|
| 字节计数器 | 负载均衡器标准版按前端报告处理的数据。|
| 数据包计数器 | 负载均衡器标准版按前端报告处理的数据包。|

### <a name = "highreliability"></a>高可靠性

配置负载均衡规则可让应用程序具有缩放性，并且变得高度可靠。  可以针对单个端口配置规则，或者使用新的 HA 端口均衡所有流量，而不管 TCP 或 UDP 端口号是什么。  

使用新的 HA 端口功能可以解锁各种方案（包括高可用性）以及针对内部网络虚拟设备进行缩放，另外，还能为无法或不需要指定单个端口的方案提供支持。 HA 端口允许任意数目的实例，而不局限于主动/被动方案，因此可以提供冗余和缩放性。 运行状况探测配置通过将流量仅转发到正常实例来保护服务。

网络虚拟设备供应商可为客户消除单一故障点，并允许使用多个活动实例进行缩放，提供享有全面供应商支持的弹性方案。 可以扩展到两个或更多个实例（如果设备允许这种配置）。 对于这些方案，网络虚拟设备供应商需要提供更多的指导。

### <a name = "availabilityzones"></a>可用性区域

[!INCLUDE [availability-zones-preview-statement](../../includes/availability-zones-preview-statement.md)]

使用受支持区域中的可用性区域进一步提升应用程序的复原能力。 可用性区域目前在特定的区域以预览版提供，需要额外选用。

### <a name="automatic-zone-redundancy"></a>自动区域冗余

可以针对每个应用程序，选择负载均衡器是要提供区域冗余的前端还是区域前端。  使用负载均衡器标准版可以轻松创建区域冗余。  单个前端 IP 地址已自动实现区域冗余。  区域冗余的前端由区域中的所有可用性区域同时提供服务。 这样可为入站和出站连接创建区域冗余的数据路径。 Azure 中的区域冗余不需要多个 IP 地址和 DNS 记录。 

此区域冗余适用于公共或内部前端。 公共 IP 地址以及内部负载均衡器前端的专用 IP 可以实现区域冗余。

使用以下代码创建区域冗余的公共 IP 地址（请将“sku”添加到任何现有的资源管理器模板）：

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/publicIPAddresses",
            "name": "public_ip_standard",
            "location": "region",
            "sku":
            {
                "name": "Standard"
            },
```

使用以下代码创建内部负载均衡器区域冗余的前端 IP 配置（请将“sku”添加到任何现有的资源管理器模板）：

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/loadBalancers",
            "name": "load_balancer_standard",
            "location": "region",
            "sku":
            {
                "name": "Standard"
            },
            "properties": {
                "frontendIPConfigurations": [
                    {
                        "name": "zone_redundant_frontend",
                        "properties": {
                            "subnet": {
                                "Id": "[variables('subnetRef')]"
                            },
                            "privateIPAddress": "10.0.0.6",
                            "privateIPAllocationMethod": "Static"
                        }
                    },
                ],
```

如果公共 IP 前端是区域冗余的，则从 VM 实例发起的任何出站连接会自动实现区域冗余，在发生区域故障时可受到保护。  SNAT 端口分配在发生区域故障时也能幸存。

#### <a name="cross-zone-load-balancing"></a>跨区域负载均衡

可在区域中针对后端池使用跨区域负载均衡，为虚拟机实例提供最大的灵活性。  前端可将流量传送到 VNet 中的任何 VM，而不管 VM 实例的可用性区域是什么。

此外，还可以选择为前端和后端实例指定一个特定的区域，使数据路径和资源与特定的区域相符。

由于 VNet 和子网永远不受区域的约束，因此，只需使用所需的 VM 实例定义一个后端池即可完成配置。

#### <a name="zonal-deployments"></a>区域部署

（可选）还可以通过定义一个区域前端来使前端与特定的区域相符。  区域前端仅由指定的单个可用性区域提供服务，与区域 VM 实例相结合时，可使资源与特定的区域相符。

在特定区域中创建的公共 IP 地址将始终仅存在于该区域中。  不能更改公共 IP 地址的区域。  如果想要具有可附加到多个区域中的资源的公共 IP 地址，则应改为创建区域冗余的公共 IP。

使用以下代码在可用性区域 1 中创建区域公共 IP 地址（请将“zones”和“sku”添加到任何现有的资源管理器模板）：

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/publicIPAddresses",
            "name": "public_ip_standard",
            "location": "region",
            "zones": [ "1" ],
            "sku":
            {
                "name": "Standard"
            },
```

使用以下代码在可用性区域 1 中创建内部负载均衡器前端（请将“sku”添加到任何现有资源管理器模板，并将“zones”放入前端 IP 配置子资源）：

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/loadBalancers",
            "name": "load_balancer_standard",
            "location": "region",
            "sku":
            {
                "name": "Standard"
            },
            "properties": {
                "frontendIPConfigurations": [
                    {
                        "name": "zonal_frontend_in_az1",
                        "zones": [ "1" ],
                        "properties": {
                            "subnet": {
                                "Id": "[variables('subnetRef')]"
                            },
                            "privateIPAddress": "10.0.0.6",
                            "privateIPAllocationMethod": "Static"
                        }
                    },
                ],
```

此外，可以通过将 VNet 中的 VM 实例放入后端池，对后端使用跨区域负载均衡。

在支持可用性区域的位置，负载均衡器标准版资源本身始终是大区域 (region) 和局部区域 (zone) 冗余的。 公共 IP 地址或未分配有局部区域的内部负载均衡器前端可部署在任何大区域，不管是否支持可用性区域。 如果大区域后来获得了可用性区域，已部署的公共 IP 或内部负载均衡器前端会自动实现区域冗余。 区域冗余的数据路径并不意味着数据包完全不会丢失。

### <a name = "nsg"></a>网络安全组

负载均衡器标准版和公共 IP 标准版会完全进入 VNet，因此现在需要网络安全组 (NSG)。 NSG 允许将流加入白名单，并允许采用可让客户通过 NSG 完全控制何时让流量进入其部署，而不是在完成其他配置的情况下允许流量的配置。

可将 NSG 与子网关联，或者与后端池中 VM 实例的 NIC 关联。  这一点适用于负载均衡器标准版，以及用作实例级公共 IP 的公共 IP 标准版。 必须将想要通过 NSG 允许的流量显式加入白名单，这样才能传送该流量。

若要详细了解网络安全组以及如何将其应用于自己的方案，请参阅[网络安全组](../virtual-network/virtual-networks-nsg.md)。

### <a name ="outboundconnections"></a>出站连接

 使用端口伪装源网络地址转换 (SNAT) 将负载均衡器标准版与负载均衡关联后，负载均衡器标准版可以针对 VNet 中的 VM 提供出站连接。

将公共负载均衡器资源与 VM 实例关联后，会将每个出站连接的源从 VNet 的专用 IP 地址空间重新写入负载均衡器前端的公共 IP 地址。  负载均衡器标准版使用新的端口伪装源网络地址转换 (SNAT) 算法来提高可靠性和缩放性。  

此外，与区域冗余的前端结合使用时，出站连接也能实现区域冗余，发生区域故障时，SNAT 端口分配可以幸存。

将 SNAT 端口添加到池时，负载均衡器标准版的新算法会根据以下层，将这些端口预先分配到每个 VM 的网络接口：

| 后端池大小 | 预先分配的 SNAT 端口数 |
| --- | --- |
| 1-50 | 1024 |
| 51-100 | 512 |
| 101-200 | 256 |
| 201-400 | 128 |
| 401-800 | 64 |
| 801-1000 | 32 |

SNAT 端口数并不直接表示连接数。 可以针对多个唯一目标重用一个 SNAT 端口。  有关详细信息，请查看[出站连接](load-balancer-outbound-connections.md)一文。

如果后端池增大并从一个大小层过渡到下一个更大的大小，则会回收一半的分配端口。 与回收的端口关联的任何连接将会超时，需要重建。 任何新的连接尝试会立即成功。 如果后端池从一个大小层降低至下一个更小的大小，可用的 SNAT 端口数会增多，且现有连接不受影响。

负载均衡器标准版还有一个基于规则的附加配置选项，在有多个可用的前端时，可让客户控制要将哪个前端用于端口伪装 SNAT。

最后，如果只有负载均衡器标准版为 VM 实例提供服务，则 SNAT 出站连接不可用。 可以通过将 VM 实例同时分配到公共负载均衡器，或者将公共 IP 作为实例级公共 IP 直接分配到每个 VM 实例，来显式恢复此功能。 对于某些操作系统和应用程序方案，可能需要这样做。 

### <a name="port-forwarding"></a>端口转发

 基本和标准版负载均衡器提供配置入站 NAT 规则，以将前端端口映射到单个后端实例的功能。  此功能有很多用途，例如，公开远程桌面协议终结点、SSH 终结点，或实现其他各种应用程序方案。

负载均衡器标准版继续通过入站 NAT 规则提供端口转发功能。  与区域冗余的前端结合使用时，入站 NAT 规则可实现区域冗余，在发生区域故障时能够幸存。

### <a name="multiple-frontends"></a>多个前端

当应用程序需要公开多个独立 IP 地址时（例如 TLS 网站或 SQL AlwaysOn 可用性组终结点），可以配置多个前端来提高设计灵活性。  [此处](load-balancer-multivip-overview.md)提供了更多详细信息。

负载均衡器标准版继续提供多个前端，其中需要在唯一的 IP 地址上公开特定的应用程序终结点。

 [此处](load-balancer-multivip-overview.md)提供了更多详细信息。

## <a name = "sku"></a>关于 SKU

SKU 仅在 Azure 资源管理器部署模型中可用。  本预览版为负载均衡器和公共 IP 资源引入了两个 SKU（“基本”和“标准”）。  这些 SKU 的功能、性能特征、限制和某些内在行为有所不同。 可在任一 SKU 中使用虚拟机。 对于负载均衡器和公共 IP 资源，SKU 保留为可选属性，如果省略，则默认为“基本”。

>[!IMPORTANT]
>资源的 SKU 不可变。  无法更改现有资源的 SKU。  

### <a name="load-balancer"></a>负载均衡器

[现有的负载均衡器资源](load-balancer-overview.md)会变成基本 SKU 并保留正式版，且不可更改。

负载均衡器标准 SKU 是新产品，目前以预览版提供。 Microsoft.Network/loadBalancers 的 2017-08-01 API 版本为资源引入了 SKU。

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/loadBalancers",
            "name": "load_balancer_standard",
            "location": "region",
            "sku":
            {
                "name": "Standard"
            },
```
在也提供可用性区域的区域中使用时，负载均衡器标准版会自动获得区域弹性，除非已声明为区域性。

### <a name="public-ip"></a>公共 IP

[现有的公共 IP 资源](../virtual-network/virtual-network-ip-addresses-overview-arm.md)会变成基本 SKU，并保留正式版及其功能、性能特征和限制。

公共 IP 标准 SKU 是新产品，目前以预览版提供。 Microsoft.Network/publicIPAddresses 资源的 2017-08-01 API 版本引入了 SKU。

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/publicIPAddresses",
            "name": "public_ip_standard",
            "location": "region",
            "sku":
            {
                "name": "Standard"
            },
```

与提供多种分配方法的公共 IP 基本版不同，公共 IP 标准版始终为静态分配。

在也提供可用性区域的区域中使用时，公共 IP 标准版会自动获得区域弹性，除非已声明为区域性。  区域公共 IP 不能从一个区域更改为另一个区域。

## <a name="migration-between-skus"></a>SKU 之间的迁移

SKU 不可变。  若要从一个资源 SKU 迁移到另一个资源 SKU，请遵循以下步骤：

### <a name="migrating-from-basic-to-standard-sku"></a>将基本 SKU 迁移到标准 SKU

1. 根据需要创建新的标准版资源（负载均衡器和公共 IP），并重新创建规则和探测定义。
2. 从所有 VM 实例（包括可用性集的所有实例）中删除基本 SKU 资源（公共 IP 和 LB）。
3. 将所有 VM 实例附加到新的标准 SKU 资源。

### <a name="migrating-from-standard-to-basic-sku"></a>从标准 SKU 迁移到基本 SKU：

1. 根据需要创建新的基本版资源（负载均衡器和公共 IP），并重新创建规则和探测定义。 
2. 从所有 VM 实例（包括可用性集的所有实例）中删除标准 SKU 资源（公共 IP 和 LB）。
3. 将所有 VM 实例附加到新的基本 SKU 资源。

  >[!NOTE]
  >标准 SKU 的 HA 端口和诊断只能在标准 SKU 中使用。 无法从标准版迁移到基本版并保留此功能。

必须对负载均衡器和公共 IP 资源使用匹配的 SKU。  不能混用使用基本和标准 SKU 资源，或者将 VM、可用性集中的 VM 或虚拟机规模集同时附加到这两类资源。

## <a name="region-availability"></a>上市区域

负载均衡器标准版目前已在以下区域推出：
- 美国东部 2
- 美国中部
- 欧洲北部
- 美国中西部
- 欧洲西部
- 东南亚

## <a name="service-limits--abilities-comparison"></a>服务限制和功能比较

Azure 的[网络服务限制](https://docs.microsoft.com/en-us/azure/azure-subscription-service-limits#networking-limits)根据区域和订阅实施。 

下表提供了负载均衡器基本和标准 SKU 的限制与功能比较：

| 负载均衡器 | 基本 | 标准 |
| --- | --- | --- |
| 后端池大小 | 最大 100 | 最大 1000 |
| 后端池边界 | 可用性集 | VNet、区域 |
| 后端池设计 | 可用性集中的 VM，或可用性集中的虚拟机规模集 | VNet 中的任何 VM 实例 |
| HA 端口 | 不支持 | 可用 |
| 诊断 | 受限，仅限公共 IP | 可用 |
| VIP 可用性  | 不支持 | 可用 |
| 快速 IP 移动性 | 不支持 | 可用 |
|可用性区域方案 | 仅限区域 | 区域、区域冗余、跨区域负载均衡 |
| 出站 SNAT 算法 | 按需 | 预先分配 |
| 出站 SNAT 前端选择 | 不可配置，有多个候选项 | 可选配置，减少了候选项 |
| 网络安全组 | 在 NIC/子网上可选 | 必选 |

下表提供了公共 IP 基本和标准 SKU 的限制与功能比较：

| 公共 IP | 基本 | 标准 |
| --- | --- | --- |
| 可用性区域方案 | 仅限区域 | 区域冗余（默认）、区域（可选） | 
| 快速 IP 移动性 | 不支持 | 可用 |
| VIP 可用性 | 不支持 | 可用 |
| 计数器 | 不支持 | 可用 |
| 网络安全组 | 在 NIC 上可选 | 必选 |


## <a name="preview-sign-up"></a>预览版注册

若要参与负载均衡器标准 SKU 及其伴侣公共 IP 标准 SKU 的预览，请使用 PowerShell 或 Azure CLI 2.0 注册订阅以获取访问权限。

- 使用 PowerShell 注册

    ```powershell
    Register-AzureRmProviderFeature -FeatureName AllowLBPreview -ProviderNamespace Microsoft.Network
    ```

- 使用 Azure CLI 2.0 注册

    ```cli
    az feature register --name AllowLBPreview --namespace Microsoft.Network
    ```

>[!NOTE]
>注册负载均衡器标准版功能最长可能需要一小时。

>[!NOTE]
>如果要将负载均衡器标准版与[可用性区域](https://aka.ms/availabilityzones)和 [HA 端口](https://aka.ms/haports)配合使用，需要对这些预览功能进行单独注册。  请按照相应的说明操作。

## <a name="pricing"></a>定价

负载均衡器标准 SKU 根据配置的规则和处理的数据计费。  在预览期不会产生任何费用。  有关详细信息，请查看[负载均衡器](https://aka.ms/lbpreviewpricing)和[公共 IP](https://aka.ms/lbpreviewpippricing) 定价页。

客户可以继续免费享用负载均衡器基本 SKU。

## <a name="limitations"></a>限制

在预览期存在以下限制，这些限制随时可能发生变化：

- 负载均衡器后端实例目前不能位于对等互连的 VNet 中。 所有后端实例必须位于同一区域中。
- SKU 不可变。 无法更改现有资源的 SKU。
- 可以针对某个独立的 VM、可用性集中的所有 VM 实例或虚拟机规模集使用基本 SKU 或标准 SKU。 不可以同时在这两种 SKU 中使用某个独立的 VM 、可用性集中的所有 VM 实例或虚拟机规模集。 不允许混合使用 SKU。
- 对 VM 实例（或可用性集的任何组成部分）使用内部负载均衡器标准版会禁用[默认的 SNAT 出站连接](load-balancer-outbound-connections.md)。  可以在独立的 VM、VM 实例可用性集或虚拟机规模集上恢复此功能，并通过同时将公共负载均衡器标准版或公共 IP 标准版（作为实例级公共 IP）分配到同一个 VM 实例来建立出站连接。 完成后，会再次提供公共 IP 地址的端口伪装 SNAT。
- 可能需要将 VM 实例分组到可用性集中才能实现完全的后端池缩放。 在单个后端池中最多可以放置 150 个可用性集和独立 VM。
- 不支持 IPv6。
- 在可用性区域的上下文中，无法将前端从区域配置变成区域冗余配置，反之亦然。 以区域冗余配置创建前端后，它始终是区域冗余的。 以区域配置创建前端后，它始终是区域性的。
- 在可用性区域的上下文中，区域公共 IP 地址不能在从一个区域转移到另一个区域。


## <a name="next-steps"></a>后续步骤

- 详细了解[基本负载均衡器](load-balancer-overview.md)
- 详细了解[可用性区域](../availability-zones/az-overview.md)
- 了解 Azure 的部分其他关键[网络功能](../networking/networking-overview.md)

