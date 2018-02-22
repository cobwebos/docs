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
ms.date: 02/04/2018
ms.author: kumud
ms.openlocfilehash: cf7be370ab0d79be9068534f0c43b88f454bc024
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/09/2018
---
# <a name="azure-load-balancer-standard-overview-preview"></a>Azure 负载均衡器标准版概述（预览版）

将 Azure 负载均衡器标准 SKU 和公共 IP 标准 SKU 结合使用，可以构建高度可缩放且可靠的体系结构。 使用负载均衡器标准版的应用程序可以利用新的功能。 针对所有 TCP 和 UDP 应用程序的数百万个流提供低延迟、高吞吐量和缩放功能。

>[!NOTE]
> 负载均衡器标准版 SKU 目前以预览版提供。 在预览期，该功能的可用性和可靠性级别可能与正式版不同。 有关详细信息，请参阅 [Microsoft Azure 预览版 Microsoft Azure 补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 请将正式版[负载均衡器基本 SKU](load-balancer-overview.md) 用于生产服务。 若要将[可用性区域预览版](https://aka.ms/availabilityzones)与此预览版配合使用，需要进行[单独注册](https://aka.ms/availabilityzones)，并且还需要注册负载均衡器[标准预览版](#preview-sign-up)。

## <a name="why-use-load-balancer-standard"></a>为何使用负载均衡器标准版？

可对各种规模的数据中心使用负载均衡器标准版。 从小规模部署到大型复杂的多区域体系结构，都可以使用负载均衡器标准版来充分利用以下功能：

- 可以使用负载均衡器标准版实现[企业级缩放](#enterprisescale)。 可对虚拟网络中的最多 1000 个任何虚拟机 (VM) 实例使用此功能。

- 提供[新的诊断见解](#diagnosticinsights)，帮助你了解、管理虚拟数据中心的这个重要组件并对其进行故障排除。 使用 Azure Monitor（预览版）显示、筛选新的多维指标并对其进行分组，以便持续度量数据路径运行状况。 监视从前端到 VM、终结点运行状况探测、TCP 连接尝试再到出站连接的数据。

- [网络安全组](#nsg)对于与负载均衡器标准版 SKU 或公共 IP 标准版 SKU 关联的任何 VM 实例现在都是必需的。 网络安全组 (NSG) 可为方案提供增强的安全性。

- [高可用性 (HA) 端口提供较高的可靠性](#highreliability)，并可针对网络虚拟设备 (NVA) 和其他应用程序方案进行缩放。 HA 端口可将 Azure 内部负载均衡器 (ILB) 前端中的所有端口负载均衡到 VM 实例池。

- [出站连接](#outboundconnections)现在使用新的源网络地址转换 (SNAT) 端口分配模型，提供更高的复原能力和缩放性。

- [包含可用性区域的负载均衡器标准版](#availabilityzones)可用于构建区域冗余的体系结构和区域体系结构。 这两种体系结构都具有跨区域负载均衡功能。 可以在不依赖于 DNS 记录的情况下实现区域冗余。 默认情况下，单个 IP 地址是区域冗余的。  单个 IP 地址可在一个跨所有可用性区域的区域中访问虚拟网络中的任何 VM。


可以在支持以下基本方案的公共或内部配置中使用负载均衡器标准版：

- 将入站流量负载均衡到正常的后端实例。
- 以端口转发的形式将入站流量传送到单个后端实例。
- 转换从虚拟网络中的专用 IP 地址发往公共 IP 地址的出站流量。

### <a name = "enterprisescale"></a>企业级缩放

 使用负载均衡器标准版，设计高性能的虚拟数据中心和支持任何 TCP 或 UDP 应用程序。 在后端池中使用独立的 VM 实例或最多 1,000 个虚拟机规模集的实例。 仍可使用较低的转发延迟、较高的吞吐量性能，并可针对完全托管的 Azure 服务的数百万个流进行缩放。
 
负载均衡器标准版可将流量转发到区域虚拟网络中的任何 VM 实例。 后端池大小最高可达 1,000 个实例，其中可以采用以下 VM 方案的任意组合：

- 无可用性集的独立 VM
- 有可用性集的独立 VM
- 最多包含 1,000 个实例的虚拟机规模集
- 多个虚拟机规模集
- VM 与虚拟机规模集的混合

可用性集不再有任何要求。 可以选择使用可用性集，以利用它们提供的其他好处。

### <a name = "diagnosticinsights"></a>诊断见解

负载均衡器标准版为公共和内部负载均衡器配置提供新的多维诊断功能。 这些新指标通过 Azure Monitor（预览版）提供，并利用所有相关功能，包括与下游使用者集成的功能。

| 指标 | 说明 |
| --- | --- |
| VIP 可用性 | 负载均衡器标准版持续运用从区域内部到负载均衡器前端，直到支持 VM 的 SDN 堆栈的数据路径。 只要保留正常实例，这种度量就会遵循应用程序负载均衡的流量所用的相同路径。 此外，还会验证客户使用的数据路径。 度量对于应用程序不可见，且不会干扰其他操作。|
| DIP 可用性 | 负载均衡器标准版使用分布式运行状况探测服务，根据配置设置监视应用程序终结点的运行状况。 此指标提供负载均衡器池中每个实例终结点的聚合视图或按终结点筛选的视图。  可以查看负载均衡器如何根据运行状况探测配置的指示了解应用程序的运行状况。
| SYN 数据包 | 负载均衡器标准版不会终止 TCP 连接，也不会与 TCP 或 UDP 数据包流交互。 流及其握手始终位于源和 VM 实例之间。 若要更好地排查 TCP 协议方案的问题，可以使用 SYN 数据包计数器了解进行了多少次 TCP 连接尝试。 该指标将报告接收到的 TCP SYN 数据包数目。|
| SNAT 连接 | 负载均衡器标准版报告公共 IP 地址前端上伪装的出站流数。 SNAT 端口是可耗竭性资源。 此指标可以指出应用程序依赖于 SNAT 获取出站发起流的程度有多高。  将报告成功和失败的出站 SNAT 流的计数器，可使用这些计数器排查和了解出站流的运行状况。|
| 字节计数器 | 负载均衡器标准版按前端报告处理的数据。|
| 数据包计数器 | 负载均衡器标准版按前端报告处理的数据包。|

### <a name = "highreliability"></a>高可靠性

配置负载均衡规则可让应用程序具有缩放性，并且变得高度可靠。 可以针对单个端口配置规则，或者使用 HA 端口均衡所有流量，而不管 TCP 或 UDP 端口号是什么。  

可以使用新的 HA 端口功能来解锁各种方案（包括高可用性）以及针对内部 NVA 进行缩放。 该功能对无法或不需要指定单个端口的其他方案也很有用。 HA 端口允许任意数目的实例，因此可以提供冗余和缩放性。 配置不再局限于主动/被动方案。 运行状况探测配置通过将流量仅转发到正常实例来保护服务。

NVA 供应商可为客户提供享有全面供应商支持的弹性方案。 已删除单一故障点，并支持多个活动实例进行缩放。 可缩放到两个或多个实例，具体取决于设备的功能。 有关这些方案的其他指导，请联系 NVA 供应商。

### <a name = "availabilityzones"></a>可用性区域

[!INCLUDE [availability-zones-preview-statement](../../includes/availability-zones-preview-statement.md)]

使用受支持区域中的可用性区域进一步提升应用程序的复原能力。 可用性区域目前在特定的区域以预览版提供，需要额外选用。

### <a name="automatic-zone-redundancy"></a>自动区域冗余

可以针对每个应用程序，选择负载均衡器是要提供区域冗余的前端还是区域前端。 使用负载均衡器标准版可以轻松创建区域冗余。 单个前端 IP 地址已自动实现区域冗余。 区域冗余的前端由区域中的所有可用性区域同时提供服务。 可为入站和出站连接创建区域冗余的数据路径。 Azure 中的区域冗余不需要多个 IP 地址和 DNS 记录。 

区域冗余适用于公共或内部前端。 公共 IP 地址和内部负载均衡器的前端专用 IP 可以实现区域冗余。

使用以下脚本为内部负载均衡器创建区域冗余的公共 IP 地址。 如果在配置中使用现有的资源管理器模板，请将 sku 部分添加到这些模板中。

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

使用以下脚本为内部负载均衡器创建区域冗余的前端 IP。 如果在配置中使用现有的资源管理器模板，请将 sku 部分添加到这些模板中。

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

如果公共 IP 前端是区域冗余的，则从 VM 实例发起的出站连接会自动实现区域冗余。 前端在发生区域故障时可受到保护。 SNAT 端口分配在发生区域故障时也能幸存。

#### <a name="cross-zone-load-balancing"></a>跨区域负载均衡

可在区域中针对后端池使用跨区域负载均衡，为 VM 实例提供最大的灵活性。 前端将流量传送到虚拟网络中的任何 VM，而不管 VM 实例的可用性区域是什么。

也可为前端和后端实例指定一个特定的区域，使数据路径和资源与特定的区域相符。

虚拟网络和子网从不受区域限制。 只需使用所需的 VM 实例定义后端池，即可完成配置。

#### <a name="zonal-deployments"></a>区域部署

可以选择通过定义区域前端，将负载均衡器前端与特定区域对齐。 区域前端仅由指定的单个可用性区域提供服务。 前端与区域 VM 实例相结合时，可将资源与特定区域对齐。

在特定区域中创建的公共 IP 地址始终仅存在于该区域中。 不能更改公共 IP 地址的区域。 对于可附加到多个区域中的资源的公共 IP 地址，请改为创建区域冗余的公共 IP。

使用以下脚本在可用性区域 1 中创建区域性公共 IP 地址。 如果在配置中使用现有的资源管理器模板，请将 sku 部分添加到这些模板中。

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

使用以下脚本在可用性区域 1 中创建内部负载均衡器前端。

如果在配置中使用现有的资源管理器模板，请将 sku 部分添加到这些模板中。 此外，在子资源的前端 IP 配置中定义 zones 属性。

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

通过将虚拟网络中的 VM 实例放入后端池，为该后端池添加跨区域负载均衡。

在支持可用性区域的位置，负载均衡器标准版资源始终是大区域 (region) 和局部区域 (zone) 冗余的。 可将公共 IP 地址或未分配有局部区域的内部负载均衡器前端部署在任何大区域中。 对可用性区域的支持不会影响部署功能。 如果大区域后来获得了可用性区域，之前部署的公共 IP 或内部负载均衡器前端会自动实现区域冗余。 区域冗余的数据路径并不意味着数据包完全不会丢失。

### <a name = "nsg"></a>网络安全组

负载均衡器标准版和公共 IP 标准版会完全进入虚拟网络，这需要使用网络安全组 (NSG)。 NSG 可将通信流加入允许列表。 可以使用 NSG 获得对部署流量的完全控制。 无需再等待其他通信流完成。

将 NSG 与子网或后端池中 VM 实例的网络接口 (NIC) 关联。 此配置适用于负载均衡器标准版，以及用作实例层级公共 IP 的公共 IP 标准版。 NSG 必须将要允许的流量显式加入允许列表，才能传送该流量。

若要详细了解 NSG 以及如何将其应用于自己的方案，请参阅[网络安全组](../virtual-network/virtual-networks-nsg.md)。

### <a name ="outboundconnections"></a>出站连接

负载均衡器使用端口伪装的 SNAT 时，负载均衡器标准版可为该虚拟网络内的 VM 提供出站连接。 端口伪装 SNAT 算法提供更高的可靠性和缩放性。

公共负载均衡器资源与 VM 实例相关联时，将重写每个出站连接源。 出站连接源从虚拟网络专用 IP 地址空间重新写入负载均衡器的前端公共 IP 地址。

与区域冗余的前端结合使用时，出站连接也能实现区域冗余，发生区域故障时，SNAT 端口分配可以幸存。

负载均衡器标准版中的新算法可向每个 VM 的 NIC 预先分配 SNAT 端口。 将 NIC 添加到该池后，将根据池大小预先分配 SNAT 端口。 下表显示了针对六层后端池大小的端口预分配：

| 池大小（VM 实例） | 预分配的 SNAT 端口数 |
| --- | --- |
| 1 - 50 | 1024 |
| 51 - 100 | 512 |
| 101 - 200 | 256 |
| 201 - 400 | 128 |
| 401 - 800 | 64 |
| 801 - 1,000 | 32 |

SNAT 端口数并不直接表示出站连接数。 可以针对多个唯一目标重用一个 SNAT 端口。 有关详细信息，请查看[出站连接](load-balancer-outbound-connections.md)一文。

如果后端池增大并转换到更高层级，则会回收一半的分配端口。 与回收端口关联的连接会超时，必须重新建立连接。 新的连接尝试会立即成功。 如果后端池减小并转换到更低层级，可用的 SNAT 端口数会增多。 在这种情况下，现有连接不受影响。

负载均衡器标准版还有一个可基于规则的附加配置选项。 如果多个前端可用，则可控制哪个前端用于端口伪装 SNAT。

如果只有负载均衡器标准版为 VM 实例提供服务，则 SNAT 出站连接不可用。 还可通过向公共负载均衡器分配 VM 实例来显式还原此功能。 也可向每个 VM 实例直接分配作为实例层级公共 IP 的公共 IP。 对于某些操作系统和应用程序方案，可能需要此配置选项。 

### <a name="port-forwarding"></a>端口转发

基本版和标准版负载均衡器提供配置入站 NAT 规则，以将前端端口映射到单个后端实例的功能。 通过配置这些规则，可以公开远程桌面协议终结点和 SSH 终结点，或执行其他应用程序方案。

负载均衡器标准版继续通过入站 NAT 规则提供端口转发功能。 与区域冗余的前端结合使用时，入站 NAT 规则可实现区域冗余，在发生区域故障时能够幸存。

### <a name="multiple-front-ends"></a>多个前端

应用程序需要公开多个独立 IP 地址时（例如 TLS 网站或 SQL AlwaysOn 可用性组终结点），可以配置多个前端来提高设计灵活性。 

负载均衡器标准版继续提供多个前端，其中需要在唯一的 IP 地址上公开特定的应用程序终结点。

若要深入了解如何配置多个前端 IP，请参阅[多 IP 配置](load-balancer-multivip-overview.md)。

## <a name = "sku"></a>关于 SKU

SKU 仅在 Azure 资源管理器部署模型中可用。 本预览版为负载均衡器和公共 IP 资源引入了两个 SKU：“基本”和“标准”。 这些 SKU 的功能、性能特征、限制和某些内在行为有所不同。 可在任一 SKU 中使用虚拟机。 对于负载均衡器和公共 IP 资源，SKU 保留为可选属性。 如果在方案定义中省略 SKU，配置将默认为使用基本 SKU。

>[!IMPORTANT]
>资源的 SKU 不可变。 无法更改现有资源的 SKU。  

### <a name="load-balancer"></a>负载均衡器

[现有的负载均衡器资源](load-balancer-overview.md)会变成基本 SKU 并保留正式版，且不可更改。

负载均衡器标准 SKU 是新产品，目前以预览版提供。 2017 年 8 月 1 日的 Microsoft.Network/loadBalancers API 版本向资源定义添加了 sku 属性：

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
负载均衡器标准版会在提供可用性区域的区域中自动获得区域弹性。 如果负载均衡器已声明为区域性，则不会自动获得区域弹性。

### <a name="public-ip"></a>公共 IP

[现有的公共 IP 资源](../virtual-network/virtual-network-ip-addresses-overview-arm.md)会变成基本 SKU，并保留正式版及其功能、性能特征和限制。

公共 IP 标准 SKU 是新产品，目前以预览版提供。 2017 年 8 月 1 日的 Microsoft.Network/publicIPAddresses API 版本向资源定义添加了 sku 属性：

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

与提供多种分配方法的公共 IP 基本版不同，公共 IP 标准版始终使用静态分配。

公共 IP 标准版会在提供可用性区域的区域中自动获得区域弹性。 如果公共 IP 已声明为区域性，则将不会自动获得区域弹性。 区域公共 IP 不能从一个区域更改为另一个区域。

## <a name="migration-between-skus"></a>SKU 之间的迁移

SKU 不可变。 按照本部分中的步骤从一个资源 SKU 移动到另一个资源 SKU。

### <a name="migrate-from-basic-to-standard-sku"></a>从基本 SKU 迁移到标准 SKU

1. 根据需要创建新的标准版资源（负载均衡器和公共 IP）。 重新创建规则和探测定义。

2. 如果适用，从所有 VM 实例中删除基本 SKU 资源（负载均衡器和公共 IP）。 确保还会删除可用性集的所有 VM 实例。

3. 将所有 VM 实例附加到新的标准 SKU 资源。

### <a name="migrate-from-standard-to-basic-sku"></a>从标准 SKU 迁移到基本 SKU

1. 根据需要创建新的基本版资源（负载均衡器和公共 IP）。 重新创建规则和探测定义。 

2. 如果适用，从所有 VM 实例中删除标准 SKU 资源（负载均衡器和公共 IP）。 确保还会删除可用性集的所有 VM 实例。

3. 将所有 VM 实例附加到新的基本 SKU 资源。

>[!IMPORTANT]
>
>使用基本 SKU 和标准 SKU 具有以下限制。
>
>标准 SKU 的 HA 端口和诊断只能在标准 SKU 中使用。 无法从标准 SKU 迁移到基本 SKU，并同时保留这些功能。
>
>必须对负载均衡器和公共 IP 资源使用匹配的 SKU。 不能混合使用基本 SKU 资源和标准 SKU 资源。 不能将 VM、可用性集中的 VM 或虚拟机规模集同时附加到这两类 SKU。
>

## <a name="region-availability"></a>上市区域

负载均衡器标准版目前已在所有公有云区域推出。

>[!IMPORTANT]
> 在短时间内，访问初始启动区域（美国东部 2、美国中部、北欧、美国中西部、西欧、东南亚）之外的区域需要注册其他订阅功能（AllowLBPreviewWave2 和AllowLBPreviewWave3）。  [请执行以下步骤](#additionalpreviewregions)。 请执行所有这些步骤，即使以前已注册了 AllowLBPreview 也是如此。
> 未来几周中将删除此要求。

## <a name="sku-service-limits-and-abilities"></a>SKU 服务限制和功能

Azure 的[网络服务限制](https://docs.microsoft.com/azure/azure-subscription-service-limits#networking-limits)根据区域和订阅实施。 

下表比较了负载均衡器基本和标准 SKU 的限制和功能：

| 负载均衡器 | 基本 | 标准 |
| --- | --- | --- |
| 后端池大小 | 最大 100 | 最大 1,000 |
| 后端池边界 | 可用性集 | 虚拟网络、区域 |
| 后端池设计 | 可用性集中的 VM、可用性集中的虚拟机规模集 | 虚拟网络中的任何 VM 实例 |
| HA 端口 | 不支持 | 可用 |
| 诊断 | 受限，仅限公共 IP | 可用 |
| VIP 可用性  | 不支持 | 可用 |
| 快速 IP 移动性 | 不支持 | 可用 |
|可用性区域方案 | 仅限区域 | 区域、区域冗余、跨区域负载均衡 |
| 出站 SNAT 算法 | 按需 | 预先分配 |
| 出站 SNAT 前端选择 | 不可配置，有多个候选项 | 可选配置，减少了候选项 |
| 网络安全组 | 在 NIC/子网上可选 | 必选 |

下表比较了公共 IP 基本和标准 SKU 的限制和功能：

| 公共 IP | 基本 | 标准 |
| --- | --- | --- |
| 可用性区域方案 | 仅限区域 | 区域冗余（默认）、区域（可选） | 
| 快速 IP 移动性 | 不支持 | 可用 |
| VIP 可用性 | 不支持 | 可用 |
| 计数器 | 不支持 | 可用 |
| 网络安全组 | 在 NIC 上可选 | 必选 |


## <a name="preview-sign-up"></a>预览版注册

若要参与负载均衡器标准 SKU 及其配套公共 IP 标准 SKU 的预览，请注册订阅。  通过注册订阅，即可使用 PowerShell 或 Azure CLI 2.0 进行访问。 若要进行注册，请执行以下步骤：

>[!NOTE]
>注册负载均衡器标准版功能可能需要长达一小时才能在全局范围内生效。 如果要将负载均衡器标准版与[可用性区域](https://aka.ms/availabilityzones)配合使用，需要对 AZ 预览版进行[单独注册](https://aka.ms/availabilityzones)。

<a name="additionalpreviewregions"></a>
>[!IMPORTANT]
> 在短时间内，访问初始启动区域（美国东部 2、美国中部、北欧、美国中西部、西欧、东南亚）之外的区域需要注册其他订阅功能（AllowLBPreviewWave2 和AllowLBPreviewWave3）。  下面的步骤已被修改以启用其他订阅功能。 请执行所有这些步骤，即使以前已注册了 AllowLBPreview 也是如此。 未来几周中将删除此要求。


### <a name="sign-up-by-using-azure-cli-20"></a>使用 Azure CLI 2.0 注册

1. 在提供程序中注册此功能：

    ```cli
    az feature register --name AllowLBPreview --namespace Microsoft.Network
    az feature register --name AllowLBPreviewWave2 --namespace Microsoft.Network
    az feature register --name AllowLBPreviewWave3 --namespace Microsoft.Network
    ```
    
2. 此操作可能最多需要 10 分钟才能完成。 可使用以下命令检查操作状态：

    ```cli
    az feature list --query "[?name=='Microsoft.Network/AllowLBPreview']" --output json
    az feature list --query "[?name=='Microsoft.Network/AllowLBPreviewWave2']" --output json
    az feature list --query "[?name=='Microsoft.Network/AllowLBPreviewWave3']" --output json
    ```
    
    对于上述每种注册功能，当功能注册状态返回“已注册”时，继续执行下一步。 示例：
   
    ```json
    {
       "id": "/subscriptions/foo/providers/Microsoft.Features/providers/Microsoft.Network/features/AllowLBPreview",
       "name": "Microsoft.Network/AllowLBPreview",
       "properties": {
          "state": "Registered"
       },
       "type": "Microsoft.Features/providers/features"
    }
    ```
    
4. 在资源提供程序中重新注册订阅，完成预览版注册：

    ```cli
    az provider register --namespace Microsoft.Network
    ```
    

### <a name="sign-up-by-using-powershell"></a>使用 PowerShell 注册

1. 在提供程序中注册此功能：

    ```powershell
    Register-AzureRmProviderFeature -FeatureName AllowLBPreview -ProviderNamespace Microsoft.Network
    Register-AzureRmProviderFeature -FeatureName AllowLBPreviewWave2 -ProviderNamespace Microsoft.Network
    Register-AzureRmProviderFeature -FeatureName AllowLBPreviewWave3 -ProviderNamespace Microsoft.Network
    ```
    
2. 此操作可能最多需要 10 分钟才能完成。 可使用以下命令检查操作状态：

    ```powershell
    Get-AzureRmProviderFeature -FeatureName AllowLBPreview -ProviderNamespace Microsoft.Network
    Get-AzureRmProviderFeature -FeatureName AllowLBPreviewWave2 -ProviderNamespace Microsoft.Network
    Get-AzureRmProviderFeature -FeatureName AllowLBPreviewWave3 -ProviderNamespace Microsoft.Network
    ```

  对于上述每种注册功能，当功能注册状态返回“已注册”时，继续执行下一步。 示例：

    ```
    FeatureName      ProviderName        RegistrationState
    -----------      ------------        -----------------
    AllowLBPreview   Microsoft.Network   Registered
    ```
    
3. 在资源提供程序中重新注册订阅，完成预览版注册：

    ```powershell
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
    ```
 
## <a name="pricing"></a>定价

负载均衡器标准 SKU 根据配置的规则和处理的数据计费。 在预览期不会产生任何费用。 有关详细信息，请查看[负载均衡器](https://aka.ms/lbpreviewpricing)和[公共 IP](https://aka.ms/lbpreviewpippricing) 定价页。

客户可以继续免费享用负载均衡器基本 SKU。

## <a name="limitations"></a>限制

在预览期存在以下限制，这些限制随时可能发生变化：

- 负载均衡器后端实例目前不能位于对等互连的虚拟网络中。 所有后端实例必须位于同一区域中。
- SKU 不可变。 无法更改现有资源的 SKU。
- 可在这两种 SKU 中使用某个独立的 VM、可用性集中的 VM 实例或虚拟机规模集。 不可以同时在这两种 SKU 中使用 VM 组合。 不允许使用混合了这两种 SKU 的配置。
- 对 VM 实例（或可用性集的任何组成部分）使用内部负载均衡器标准版会禁用[默认的 SNAT 出站连接](load-balancer-outbound-connections.md)。 可对独立的 VM、可用性集中的 VM 实例或虚拟机规模集还原此功能。 还可还原此功能来建立出站连接。 若要还原这些功能，请将公共负载均衡器标准版或公共 IP 标准版（作为实例层级公共 IP）同时分配到同一个 VM 实例。 分配完成后，会再次提供公共 IP 地址的端口伪装 SNAT。
- 可能需要将 VM 实例分组到可用性集中才能实现完全的后端池缩放。 在单个后端池中最多可以放置 150 个可用性集和独立 VM。
- 不支持 IPv6。
- 在可用性区域的上下文中，无法将前端从区域配置变成区域冗余配置，反之亦然。 将前端创建为区域冗余配置后，它将一直是区域冗余配置。 将前端创建为区域配置后，它将一直是区域配置。
- 在可用性区域的上下文中，区域公共 IP 地址不能从一个区域转移到另一个区域。
- 目前不支持 [Azure Monitor 警报](../monitoring-and-diagnostics/monitoring-overview-alerts.md)。
- 门户尚不支持扩展的预览区域。  请使用模板、Azure CLI 2.0 或 PowerShell 等客户端工具作为解决方法。


## <a name="next-steps"></a>后续步骤

- 详细了解[负载均衡器基本版](load-balancer-overview.md)。
- 详细了解[可用性区域](../availability-zones/az-overview.md)。
- 详细了解[网络安全组](../virtual-network/virtual-networks-nsg.md)。
- 了解 Azure 的部分其他关键[网络功能](../networking/networking-overview.md)。
- 了解 [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview.md) [中公开的指标](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftnetworkloadbalancers)。
