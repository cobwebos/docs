---
title: "有关 Azure 堆栈的 VPN 网关 |Microsoft 文档"
description: "了解和配置 VPN 网关与 Azure 堆栈使用。"
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 0e30522f-20d6-4da7-87d3-28ca3567a890
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/01/2017
ms.author: brenduns
ms.openlocfilehash: ba9642d8c51f57623aded44b84d7127334806bc1
ms.sourcegitcommit: 80eb8523913fc7c5f876ab9afde506f39d17b5a1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2017
---
# <a name="about-vpn-gateway-for-azure-stack"></a>有关 Azure 堆栈的 VPN 网关
*适用范围： Azure 堆栈集成系统和 Azure 堆栈开发工具包*


Azure 虚拟网络和本地站点之间发送网络流量之前，你必须为你的虚拟网络创建虚拟网络网关。

VPN 网关是一种虚拟网络网关，可以通过公共连接发送加密的流量。 你可以使用 VPN 网关发送 Azure 堆栈中的虚拟网络和在 Azure 中，虚拟网络之间安全地或虚拟网络和连接到 VPN 设备的另一个网络之间的流量。

创建虚拟网络网关时，需指定要创建的网关类型。 Azure 堆栈支持一种类型的虚拟网络网关: Vpn 类型。

每个虚拟网络可以使用两个虚拟网络网关，但每种类型的网关只能有一个网关。 根据选择的设置，可与一个 VPN 网关建立多个连接。 此示例演示了一种多站点连接配置。

> [!NOTE]
> 在 Azure 中，必须在连接到它的所有连接之间划分你选择的 VPN 网关 SKU 的带宽吞吐量。  在 Azure 堆栈中的 VPN 网关 sku 的带宽值适用于连接到其每个连接资源。     

> 例如，在 Azure 中，基本的 VPN 网关 SKU 可以容纳大约 100 Mbps 的聚合的吞吐量。  如果创建两个连接到该 VPN 网关，并且一个连接正在使用 50 Mbps 的带宽，然后 50 Mbps 可供其他连接。   

> 在 Azure 堆栈*每个*到基本的 VPN 网关 SKU 的连接获取分配 100 Mbps 的吞吐量。

## <a name="configuring-a-vpn-gateway"></a>配置 VPN 网关
VPN 网关连接依赖于使用特定设置配置的多个资源。 大多数资源可以单独配置，虽然在某些情况下它们必须按特定顺序配置。

### <a name="settings"></a>设置
为每个资源选择的设置对于成功创建连接至关重要。 有关各个资源和 VPN 网关的设置的信息，请参阅[Azure 堆栈的关于 VPN 网关设置](azure-stack-vpn-gateway-settings.md)。 你可以找到信息来帮助你了解网关类型、 VPN 类型、 连接类型、 网关子网、 本地网络网关和你可能想要考虑的各种其他资源设置。

### <a name="deployment-tools"></a>部署工具
开始时可以使用一个配置工具（如 Azure 门户）创建和配置资源。 稍后可以切换到另一种工具（如 PowerShell）来配置其他资源，或者在适当的情况下修改现有资源。 目前，无法在 Azure 门户中配置每个资源和资源设置。 每个连接拓扑的文章中的说明指定了何时需要特定配置工具。

## <a name="connection-topology-diagrams"></a>连接拓扑图
必须知道，VPN 网关连接可以使用不同的配置。 必须确定哪种配置最适合自己的需要。 在下面的部分中，可以查看有关以下 VPN 网关连接的信息和拓扑示意图：以下部分包含一些表格，其中列出了：

- 可用的部署模型
- 可用的配置工具
- 直接转到某篇文章的链接（如果适用）

关系图和下列部分中的说明可以帮助您选择的连接拓扑以匹配你的要求。 示意图显示了主要的基准拓扑，但可以使用示意图作为准则生成更复杂的配置。

## <a name="site-to-site-and-multi-site-ipsecike-vpn-tunnel"></a>站点到站点和多站点（IPsec/IKE VPN 隧道）
### <a name="site-to-site"></a>站点到站点
站点到站点 (S2S) VPN 网关连接是通过 IPsec/IKE（IKEv1 或 IKEv2）VPN 隧道建立的连接。 此类型的连接要求位于本地的 VPN 设备分配有一个公共 IP 地址，并且不位于 NAT 后面。 S2S 连接可以用于跨界和混合配置。    
![站点到站点 VPN 连接配置示例](media/azure-stack-vpn-gateway-about-vpn-gateways/vpngateway-site-to-site-connection-diagram.png)

### <a name="multi-site"></a>多站点
这种类型的连接是站点到站点连接的另一种形式。 可从虚拟网络网关创建多个 VPN 连接（通常是连接到多个本地站点）。 使用多个连接时，必须使用 RouteBased VPN 类型（使用经典 VNet 时称为动态网关）。 由于每个虚拟网络只能有一个 VPN 网关，因此通过网关的所有连接都共享可用带宽。 这通常称为“多站点”连接。   
![Azure VPN 网关多站点连接示例](media/azure-stack-vpn-gateway-about-vpn-gateways/vpngateway-multisite-connection-diagram.png)



## <a name="gateway-skus"></a>网关 SKU
Azure 堆栈中创建虚拟网络网关时，你将指定网关你想要使用的 SKU。 支持以下 VPN 网关 Sku:
- 基本
- 标准
- HighPerformance

当通过标准或 Basic 选择更高版本网关 SKU，例如通过基本、 标准或高性能时，更多的 Cpu 和网络带宽被分配到网关。 因此，此网关可以支持更高的网络吞吐量，到虚拟网络。

Azure 堆栈不支持 UltraPerformance 网关 SKU，以独占方式与 Express Route 一起使用。

选择 SKU 时，请注意以下事项：
- Azure 堆栈不支持基于策略的网关。
- 基本 SKU 不支持边界网关协议 (BGP)。
- ExpressRoute VPN 网关共存 Azure 堆栈中不支持配置
- 只能在高性能 SKU 上配置主动-主动 S2S VPN 网关连接。

## <a name="estimated-aggregate-throughput-by-sku"></a>按 SKU 列出的估计聚合吞吐量
下表按网关 SKU 显示了网关类型和估计的聚合吞吐量。

|   | VPN 网关吞吐量*(1)* |VPN 网关最大 IPsec 隧道 |
|-------|-------|-------|
|**基本 SKU** ***(2)***    | 100 Mbps  | 10    |
|**标准 SKU**       | 100 Mbps  | 10    |
|**高性能 SKU** | 200 Mbps    | 30    |
***(1)*** VPN 吞吐量不是跨界连接有保证的吞吐量跨 Internet。 它是可能的最大吞吐量。  
***(2)*** BGP 不支持针对基本 SKU。

## <a name="next-steps"></a>后续步骤
了解有关[VPN 网关设置](azure-stack-vpn-gateway-settings.md)Azure 堆栈。
