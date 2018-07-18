---
title: 关于 Azure Stack 的 VPN 网关 | Microsoft Docs
description: 了解和配置用于 Azure Stack 的 VPN 网关。
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 0e30522f-20d6-4da7-87d3-28ca3567a890
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/02/2018
ms.author: brenduns
ms.openlocfilehash: 0ff3402115ae9f4c736bf9058fc09de16eaefb1e
ms.sourcegitcommit: 756f866be058a8223332d91c86139eb7edea80cc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2018
ms.locfileid: "37347178"
---
# <a name="about-vpn-gateway-for-azure-stack"></a>关于 Azure Stack 的 VPN 网关

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

必须先为虚拟网络创建虚拟网络网关，然后才能发送 Azure 虚拟网络和本地站点之间的网络流量。

VPN 网关是一种虚拟网络网关，可以通过公共连接发送加密的流量。 可以使用 VPN 网关在 Azure Stack 中的虚拟网络和 Azure 中的虚拟网络之间安全地发送流量。 还可以在虚拟网络和连接到 VPN 设备的另一个网络之间安全地发送流量。

创建虚拟网络网关时，需指定要创建的网关类型。 Azure Stack 支持一种类型的虚拟网络网关：**Vpn** 类型。

每个虚拟网络可以使用两个虚拟网络网关，但每种类型的网关只能有一个网关。 根据选择的设置，可与一个 VPN 网关建立多个连接。 一个示例是多站点连接配置。

创建和配置 Azure Stack 的 VPN 网关之前，请查看[Azure Stack 网络注意事项](/azure/azure-stack/user/azure-stack-network-differences)若要了解与 Azure 的 Azure Stack 配置有何区别。

>[!NOTE]
>在 Azure 中，所选 VPN 网关 SKU 的带宽吞吐量必须分配给连接到网关的所有连接。 但在 Azure Stack 中，VPN 网关 SKU 的带宽值会应用于连接到网关的每个连接资源。
>
> 例如：
> * 在 Azure 中，基本 VPN 网关 SKU 可以容纳大约 100 Mbps 的聚合吞吐量。 如果对该 VPN 网关创建两个连接，而且其中一个连接使用 50 Mbps 的带宽，则 50 Mbps 可供另一个连接使用。
> * 在 Azure Stack 中，VPN 网关 SKU 的*每个*连接可分配到 100 Mbps 的吞吐量。

## <a name="configuring-a-vpn-gateway"></a>配置 VPN 网关

VPN 网关连接需依赖于多个具有特定设置的资源。 大多数资源可单独进行配置，但在某些情况下，必须按特定的顺序配置这些资源。

### <a name="settings"></a>设置

为每个资源选择的设置对于创建成功的连接至关重要。

有关 VPN 网关的各个资源和设置的信息，请参阅[关于 Azure Stack 的 VPN 网关设置](azure-stack-vpn-gateway-settings.md)。 本文将会介绍：

* 网关类型、VPN 类型和连接类型。
* 网关子网、本地网络网关和可能需要考虑的其他资源设置。

### <a name="deployment-tools"></a>部署工具

可以使用一个配置工具（如 Azure 门户）创建和配置资源。 稍后，可以切换到 PowerShell 等其他工具来配置其他资源或修改现有资源（如果适用）。 目前，无法在 Azure 门户中配置每个资源和资源设置。 每个连接拓扑的文章中的说明指定了何时需要特定配置工具。

## <a name="connection-topology-diagrams"></a>连接拓扑图

必须知道，VPN 网关连接可以使用不同的配置。 确定哪种配置最适合自己的需要。 在以下部分，可以查看有关以下 VPN 网关连接的信息和拓扑图示：

* 可用的部署模型
* 可用的配置工具
* 直接转到某篇文章的链接（如果适用）

以下部分中的图示和说明可帮助你选择符合要求的连接拓扑。 这些图示显示主要基准拓扑，但也可以使用这些图示作为指导来构建更复杂的配置。

## <a name="site-to-site-and-multi-site-ipsecike-vpn-tunnel"></a>站点到站点和多站点（IPsec/IKE VPN 隧道）

### <a name="site-to-site"></a>站点到站点

站点到站点 (S2S) VPN 网关连接是通过 IPsec/IKE（IKEv1 或 IKEv2）VPN 隧道建立的连接。 此类连接需要一个位于本地的 VPN 设备，并需要为此类连接分配公共 IP 地址。 此设备不能位于 NAT 的后面。 S2S 连接可以用于跨界和混合配置。

![站点到站点 VPN 连接配置示例](media/azure-stack-vpn-gateway-about-vpn-gateways/vpngateway-site-to-site-connection-diagram.png)

### <a name="multi-site"></a>多站点

这种类型的连接是站点到站点连接的另一种形式。 可从虚拟网络网关创建多个 VPN 连接（通常是连接到多个本地站点）。 使用多个连接时，必须使用 RouteBased VPN 类型（使用经典 VNet 时称为动态网关）。由于每个虚拟网络只能有一个 VPN 网关，因此通过网关的所有连接都共享可用带宽。 这通常称为“多站点”连接。

![Azure VPN 网关多站点连接示例](media/azure-stack-vpn-gateway-about-vpn-gateways/vpngateway-multisite-connection-diagram.png)

## <a name="gateway-skus"></a>网关 SKU

为 Azure Stack 创建虚拟网络网关时，需要指定要使用的网关 SKU。 支持以下 VPN 网关 SKU：

* 基本
* 标准
* HighPerformance

当选择较高的网关 SKU 时（诸如标准高于基本，高性能高于标准或基本），会将更多 CPU 和网络带宽分配给网关。 如此一来，网关可以对虚拟网络支持更高的网络吞吐量。

Azure Stack 不支持专门搭配 Express Route 使用的超性能网关 SKU。

选择 SKU 时请考虑以下事项：

* Azure Stack 不支持基于策略的网关。
* 基本 SKU 不支持边界网关协议 (BGP)。
* Azure Stack 不支持 ExpressRoute-VPN 网关共存配置。
* 只能在高性能 SKU 上配置主动-主动 S2S VPN 网关连接。

## <a name="estimated-aggregate-throughput-by-sku"></a>按 SKU 列出的估计聚合吞吐量

下表按网关 SKU 显示了网关类型和估计的聚合吞吐量。

|   | VPN 网关吞吐量 *(1)* | VPN 网关最大 IPsec 隧道数 *(2)* |
|-------|-------|-------|
|**基本 SKU** ***(3)***    | 100 Mbps  | 10    |
|**标准 SKU**       | 100 Mbps  | 10    |
|**高性能 SKU** | 200 Mbps    | 5 |

**表格注释：**

*注释 (1)* - VPN 吞吐量不是 Internet 上跨界连接的保证吞吐量。 这是可能的最大吞吐量度量。  
*注释 (2)* - 最大隧道数是所有订阅的每个 Azure Stack 部署总数。  
*注释 (3)* - 基本 SKU 不支持 BGP 路由。

## <a name="next-steps"></a>后续步骤

[Azure Stack 的 VPN 网关配置设置](azure-stack-vpn-gateway-settings.md)
