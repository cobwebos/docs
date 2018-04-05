---
title: Azure 标准负载均衡器概述 | Microsoft Docs
description: Azure 标准负载均衡器功能概述
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: ''
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/21/2018
ms.author: kumud
ms.openlocfilehash: d7ee74a19f806faed0bcfcfa5f1c5de3937d9f31
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2018
---
# <a name="azure-load-balancer-standard-overview"></a>Azure 负载均衡器标准版概述

使用 Azure 负载均衡器可以缩放应用程序，并为服务提供高可用性。 负载均衡器可用于入站和出站方案、提供低延迟和高吞吐量，以及为所有 TCP 和 UDP 应用程序纵向扩展到数以百万计的流。 

本文着重介绍标准负载均衡器。  有关 Azure 负载均衡器的详细常规概述，还可以查看[负载均衡器概述](load-balancer-overview.md)。

## <a name="what-is-standard-load-balancer"></a>什么是标准负载均衡器？

标准负载均衡器是适用于所有 TCP 和 UDP 应用程序的新型负载均衡器产品，与基本负载均衡器相比拥有更广泛和精细的功能集。  尽管两者有许多相似之处，但请务必熟悉本文中所述的差异。

可将标准负载均衡器标准版用作公共或内部负载均衡器。 虚拟机可以连接到一个公共负载均衡器资源和一个内部负载均衡器资源。

负载均衡器资源的功能始终表示为前端、规则、运行状况探测和后端池定义。  资源可以包含多项规则。 可通过从虚拟机的 NIC 资源指定后端池，将虚拟机放入其中。  对于虚拟机规模集，该参数通过网络配置文件传递并进行扩展。

资源的虚拟网络范围是一个重要方面。  尽管基本负载均衡器存在于可用性集范围内部，但标准负载均衡器与虚拟网络范围完全集成，且所有虚拟网络概念均适用。

负载均衡器资源是一些对象，可在其中表述 Azure 应如何设定其多租户基础结构，以实现想要创建的场景。  负载均衡器资源与实际基础结构之间不存在直接的关系，创建负载均衡器不会创建实例，可始终使用容量，且无需考虑启动或缩放延迟。 

>[!NOTE]
> Azure 为方案提供了一套完全托管的负载均衡解决方案。  如果寻找 TLS 终止（“SSL 卸载”）或每个 HTTP/HTTPS 请求的应用层处理，请查看[应用程序网关](../application-gateway/application-gateway-introduction.md)。  如果寻找全局 DNS 负载均衡，请查看[流量管理器](../traffic-manager/traffic-manager-overview.md)。  端到端方案可从结合所需的解决方案中受益。

## <a name="why-use-standard-load-balancer"></a>为何使用标准负载均衡器？

从小规模部署到大型复杂的多区域体系结构的任何虚拟数据中心都可以使用标准负载均衡器。

查看下表，了解标准负载均衡器与基本负载均衡器之间的差异概述：

>[!NOTE]
> 应考虑对新设计使用标准负载均衡器。 

| | 标准 SKU | 基本 SKU |
| --- | --- | --- |
| 后端池大小 | 多达 1000 个实例 | 多达 100 个实例 |
| 后端池终结点 | 一个虚拟网络中的任何虚拟机，包括虚拟机、可用性集和虚拟机规模集的混合。 | 一个可用性集或虚拟机规模集中的虚拟机 |
| 可用性区域 | 入站和出站的区域冗余和区域前端，出站流映射在发生区域故障后仍保留，跨区域负载均衡 | / |
| 诊断 | Azure Monitor、多维度指标（包括字节和数据包计数器）、运行状况探测状态、连接尝试 (TCP SYN)、出站连接运行状况（SNAT 成功和失败流）、活动数据平面度量 | 仅用于公共负载均衡器的 Azure Log Analytics、SNAT 耗尽警报、后端池运行状况计数 |
| HA 端口 | 内部负载均衡器 | / |
| 默认保护 | 在默认情况下，对公共 IP 和负载均衡器终结点关闭，网络安全组必须用于显式允许列表，以便流量流动 | 默认打开，可选网络安全组 |
| 出站连接 | 多个前端，可根据每个规则选择禁用。必须显式创建出站方案，以便虚拟机能够使用出站连接。  [VNet 服务终结点](../virtual-network/virtual-network-service-endpoints-overview.md)无需出站连接便可访问，且不会计入处理的数据。  任何公共 IP 地址（包括不作为 VNet 服务终结点提供的 Azure PaaS 服务）必须通过出站连接才能访问，且计入处理的数据。 如果只有一个内部负载均衡器向虚拟机提供服务，通过默认 SNAT 的出站连接将不可用。 出站 SNAT 编程特定于传输协议，并以入站负载均衡规则的协议为基础。 | 单个前端，存在多个前端时随机选择。  如果仅内部负载均衡器向虚拟机提供服务，则使用默认 SNAT。 |
| 多个前端 | 入站和出站 | 仅限入站 |
| 管理操作 | 大多数操作都小于 30 秒 | 典型为 60 - 90 多秒 |
| SLA | 对拥有两个正常运行的虚拟机的数据路径为 99.99% | 在 VM SLA 中为隐式 | 
| 定价 | 基于规则数、与资源关联且经过入站或出站处理的数据量进行计费  | 免费 |

请查看[负载均衡器的服务限制](https://aka.ms/lblimits)、[定价](https://aka.ms/lbpricing)和 [SLA](https://aka.ms/lbsla)。


### <a name="backend"></a>后端池

标准负载均衡器的后端池在虚拟网络中扩展到任何虚拟机资源。  可包含多达 1000 个后端实例。  后端实例是 IP 配置（NIC 资源的属性）。

后端池可以包含独立的虚拟机、可用性集或虚拟机规模集。  可在后端池中混合资源，且后端池可包含这些资源的总计至多 150 种任意组合。

考虑后端池的设计方式时，可针对单个后端池资源的最小数字进行设计，从而进一步优化管理操作的持续时间。  在数据平面性能或规模中不存在任何差异。

## <a name="az"></a>可用性区域

>[!NOTE]
> 若要将[可用性区域预览版](https://aka.ms/availabilityzones)用于标准负载均衡器，需要[注册可用性区域](https://aka.ms/availabilityzones)。

标准负载均衡器在提供可用性区域的区域中支持其他功能。  这些功能可增量到所有标准负载均衡器提供的内容。  可用性区域配置可用于公共和内部标准负载均衡器。

使用可用性区域在区域中部署时，非区域性前端默认变为区域冗余前端。   区域冗余前端在发生区域故障后仍保留，并由所有区域中的专用基础结构同时提供。 

此外，可以保证特定区域的前端。 区域前端的运行状况取决于相应的区域，仅由一个区域中的专用基础结构提供。

跨区域负载均衡可用于后端池，且 VNet 中的任何虚拟机资源均可成为后端池的一部分。

请查看[有关可用性区域的功能的详细讨论](load-balancer-standard-availability-zones.md)。

### <a name="diagnostics"></a>诊断

标准负载均衡器通过 Azure Monitor 提供多维度指标。  可以筛选这些指标并进行分组，便于深入了解服务的当前及历史性能和运行状况。  还支持资源运行状况。  以下是支持的诊断的简要概述：

| 指标 | 说明 |
| --- | --- |
| VIP 可用性 | 负载均衡器标准版持续运用从区域内部到负载均衡器前端，直到支持 VM 的 SDN 堆栈的数据路径。 只要保留正常实例，这种度量就会遵循应用程序负载均衡的流量所用的相同路径。 此外，还会验证客户使用的数据路径。 度量对于应用程序不可见，且不会干扰其他操作。|
| DIP 可用性 | 负载均衡器标准版使用分布式运行状况探测服务，根据配置设置监视应用程序终结点的运行状况。 此指标提供负载均衡器池中每个实例终结点的聚合视图或按终结点筛选的视图。  可以查看负载均衡器如何根据运行状况探测配置的指示了解应用程序的运行状况。
| SYN 数据包 | 负载均衡器标准版不会终止 TCP 连接，也不会与 TCP 或 UDP 数据包流交互。 流及其握手始终位于源和 VM 实例之间。 若要更好地排查 TCP 协议方案的问题，可以使用 SYN 数据包计数器了解进行了多少次 TCP 连接尝试。 该指标将报告接收到的 TCP SYN 数据包数目。|
| SNAT 连接 | 负载均衡器标准版报告公共 IP 地址前端上伪装的出站流数。 SNAT 端口是可耗竭性资源。 此指标可以指出应用程序依赖于 SNAT 获取出站发起流的程度有多高。  将报告成功和失败的出站 SNAT 流的计数器，可使用这些计数器排查和了解出站流的运行状况。|
| 字节计数器 | 负载均衡器标准版按前端报告处理的数据。|
| 数据包计数器 | 负载均衡器标准版按前端报告处理的数据包。|

请查看[有关标准负载均衡器诊断的详细讨论](load-balancer-standard-diagnostics.md)。

### <a name="haports"></a>HA 端口

标准负载均衡器支持一种新型规则。  

可以配置负载均衡规则，让应用程序具有缩放性，并且变得高度可靠。 使用 HA 端口负载均衡规则时，在内部标准负载均衡器的前端 IP 地址的每个临时端口上，标准负载均衡器对每个流提供负载均衡。  该功能对无法或不需要指定单个端口的其他方案也很有用。

使用 HA 端口负载均衡规则可为网络虚拟设备以及需要大范围入站端口的任何应用程序创建主动-被动或主动-被动 n+1 方案。  运行状况探测可用于确定接收新流的后端。  可使用网络安全组模拟端口范围方案。

>[!IMPORTANT]
> 如果计划使用网络虚拟设备，请咨询供应商以获取指南，了解他们的产品是否测试了 HA 端口，然后按照他们提供的特定指南进行实现。 

请查看[有关 HA 端口的详细讨论](load-balancer-ha-ports-overview.md)。

### <a name="securebydefault"></a>默认保护

标准负载均衡器已完全载入虚拟网络。  虚拟网络是封闭的专用网络。  标准负载均衡器和标准公共 IP 地址旨在允许从虚拟网络外部访问该虚拟网络，因此，这些资源现在默认处于关闭状态，除非手动打开。 这意味着网络安全组 (NSG) 现在可用于显式允许并将允许的流量添加到允许列表。  可以创建整个虚拟数据中心，并通过 NSG 决定其提供的内容和可用的时间。  如果虚拟机资源的子网或 NIC 上没有 NSG，系统将不允许流量访问此资源。

若要详细了解 NSG 以及如何将其应用于自己的方案，请参阅[网络安全组](../virtual-network/virtual-networks-nsg.md)。

### <a name="outbound"></a>出站连接

负载均衡器支持入站和出站方案。  对于出站连接，标准负载均衡器与基本负载均衡器存在明显差异。

源网络地址转换 (SNAT) 用于将虚拟网络上的内部专用 IP 地址映射到负载均衡器前端的公共 IP 地址。

标准负载均衡器为实现[更可靠、可缩放且可预测的 SNAT 算法](load-balancer-outbound-connections.md#snat)引入了新算法，并启用新功能、去除多义性并强制实现显式配置，不会产生副作用。 若要允许新功能的出现，这些更改是必要的。 

使用标准负载均衡器时，请牢记以下关键原则：

- 驱动负载均衡器资源的是规则完成。  Azure 的所有编程均派生自其配置。
- 多个前端可用时，会使用所有前端，每个前端成倍增加可用的 SNAT 端口数。
- 如果不希望某特定前端用于出站连接，可进行选择和控制。
- 出站方案处于显式状态，指定出站连接后，该连接才会存在。
- 负载均衡规则推断 SNAT 的编程方式。 负载均衡规则特定于协议。 SNAT 特定于协议，配置应反映这一点，而不是产生副作用。

#### <a name="multiple-frontends"></a>多个前端
希望出现或已遇到出站连接的高需求时，如果想要更多 SNAT 端口，还可以通过配置其他前端、规则和后端池，将增量 SNAT 端口库存添加到相同的虚拟机资源。

#### <a name="control-which-frontend-is-used-for-outbound"></a>控制用于出站的前端
如果要将出站连接限制为仅来自于特定前端 IP 地址，可以按需在表示出站映射的规则上禁用出站 SNAT。

#### <a name="control-outbound-connectivity"></a>控制出站连接
标准负载均衡器存在于虚拟网络的上下文中。  虚拟网络是独立的专用网络。  除非存在与公共 IP 地址的关联，否则不允许公共连接。  可以访问 [VNet 服务终结点](../virtual-network/virtual-network-service-endpoints-overview.md)，因为它们在虚拟网络内部并位于本地。  若要对虚拟网络外部的目标建立出站连接，可执行以下两个选项：
- 将标准 SKU 公共 IP 地址作为实例层级公共 IP 地址分配到虚拟机资源；
- 或者，将虚拟机资源放入公共标准负载均衡器的后端池中。

上述两个选项均允许通过出站连接从虚拟网络访问虚拟网络的外部。 

如果只有一个内部标准负载均衡器与虚拟机资源所在的后端池关联，虚拟机仅可以访问虚拟网络资源和 [VNet 终结点](../virtual-network/virtual-network-service-endpoints-overview.md)。  可以按照上一段描述的步骤创建出站连接。

未与标准 SKU 关联的虚拟机资源的出站连接保持不变。

请查看[有关出站连接的详细讨论](load-balancer-outbound-connections.md)。

### <a name="multife"></a>多个前端
负载均衡器使用多个前端支持多项规则。  标准负载均衡器将其扩展到出站方案。  出站方案与入站负载均衡规则实质上存在逆反关系。  入站负载均衡规则还创建了出站连接的关联。 标准负载均衡器通过负载均衡规则使用与虚拟机资源关联的所有前端。  此外，使用负载均衡规则上的参数可以为了出站连接取消负载均衡规则，并允许选择特定前端（包括无前端）。

为进行比较，基本负载均衡器随机选择一个前端，且无法控制选择哪一个前端。

请查看[有关出站连接的详细讨论](load-balancer-outbound-connections.md)。

### <a name="operations"></a>管理操作

标准负载均衡器资源存在于全新的基础结构平台中。  这允许标准 SKU 大大提高管理操作的速度，对于每个标准 SKU 资源，完成时间通常少于 30 秒。  请注意，后端池增大时，其更改所需的持续时间也随之延长。

可以修改标准负载均衡器资源，显著提高在虚拟机之间移动标准公共 IP 地址的速度。

## <a name="migration-between-skus"></a>SKU 之间的迁移

SKU 不可变。 按照本部分中的步骤从一个资源 SKU 移动到另一个资源 SKU。

### <a name="migrate-from-basic-to-standard-sku"></a>从基本 SKU 迁移到标准 SKU

1. 根据需要创建新的标准版资源（负载均衡器和公共 IP）。 重新创建规则和探测定义。

2. 为 NIC 或子网创建新的 NSG 或更新现有 NSG，以便将负载均衡流量、探测以及你想要允许的任何其他流量加入允许列表。

3. 如果适用，从所有 VM 实例中删除基本 SKU 资源（负载均衡器和公共 IP）。 确保还会删除可用性集的所有 VM 实例。

4. 将所有 VM 实例附加到新的标准 SKU 资源。

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
>根据本文所述，基本和标准 SKU 存在一定差异。  请确保理解这些差异并做好相应准备。
>
>必须对负载均衡器和公共 IP 资源使用匹配的 SKU。 不能混合使用基本 SKU 资源和标准 SKU 资源。 无法将独立的虚拟机、可用性集资源中的虚拟机或虚拟机规模集资源同时附加到两个 SKU。

## <a name="region-availability"></a>上市区域

负载均衡器标准版目前已在所有公有云区域推出。

## <a name="sla"></a>SLA

可以使用标准负载均衡器，其 SLA 为 99.99%。  有关详细信息，请查看[标准负载均衡器 SLA](https://aka.ms/lbsla)。

## <a name="pricing"></a>定价

标准负载均衡器根据以下内容计费：配置的负载均衡规则数量以及处理的所有入站和出站数据量。 有关标准负载均衡器的定价信息，请访问[负载均衡器定价](https://aka.ms/lbpricing)页。

## <a name="limitations"></a>限制

- 负载均衡器后端实例目前不能位于对等互连的虚拟网络中。 所有后端实例必须位于同一区域中。
- SKU 不可变。 无法更改现有资源的 SKU。
- 独立的虚拟机资源、可用性集资源或虚拟机规模集资源可以引用一个 SKU，绝不能同时引用两个。
- 目前不支持 [Azure Monitor 警报](../monitoring-and-diagnostics/monitoring-overview-alerts.md)。
- 标准 SKU LB 和 PIP 资源不支持[移动订阅操作](../azure-resource-manager/resource-group-move-resources.md)。

## <a name="next-steps"></a>后续步骤

- 了解如何使用[标准负载均衡器和可用性区域](load-balancer-standard-availability-zones.md)
- 详细了解[可用性区域](../availability-zones/az-overview.md)。
- 了解有关[标准负载均衡器诊断](load-balancer-standard-diagnostics.md)的信息。
- 在 [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview.md) 中了解用于诊断的[支持的多维度指标](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftnetworkloadbalancers)。
- 了解如何使用[出站连接的负载均衡器](load-balancer-outbound-connections.md)
- 了解有关[具有 HA 端口负载均衡规则的标准负载均衡器](load-balancer-ha-ports-overview.md)的信息
- 了解如何使用[具有多个前端的负载均衡器](load-balancer-multivip-overview.md)
- 了解有关[虚拟网络](../virtual-network/virtual-networks-overview.md)的信息。
- 详细了解[网络安全组](../virtual-network/virtual-networks-nsg.md)。
- 了解有关 [VNet 服务终结点](../virtual-network/virtual-network-service-endpoints-overview.md)的信息
- 了解 Azure 的部分其他关键[网络功能](../networking/networking-overview.md)。
- 详细了解[负载均衡器](load-balancer-overview.md)。
