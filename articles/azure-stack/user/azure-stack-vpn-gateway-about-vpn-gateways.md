---
title: 关于 Azure Stack 的 VPN 网关 | Microsoft Docs
description: 了解和配置用于 Azure Stack 的 VPN 网关。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 0e30522f-20d6-4da7-87d3-28ca3567a890
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/04/2019
ms.author: sethm
ms.openlocfilehash: aa2754d2de8771468f59225273d88731b4f2f545
ms.sourcegitcommit: 82cdc26615829df3c57ee230d99eecfa1c4ba459
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/19/2019
ms.locfileid: "54414836"
---
# <a name="about-vpn-gateway-for-azure-stack"></a>关于 Azure Stack 的 VPN 网关

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

必须先为虚拟网络创建虚拟网络网关，然后才能发送 Azure 虚拟网络和本地站点之间的网络流量。

VPN 网关是一种虚拟网络网关，可以通过公共连接发送加密的流量。 可以使用 VPN 网关在 Azure Stack 中的虚拟网络和 Azure 中的虚拟网络之间安全地发送流量。 还可以在虚拟网络和连接到 VPN 设备的另一个网络之间安全地发送流量。

创建虚拟网络网关时，需指定要创建的网关类型。 Azure Stack 支持一种类型的虚拟网络网关： **Vpn**类型。

每个虚拟网络可以使用两个虚拟网络网关，但每种类型的网关只能有一个网关。 根据选择的设置，可与一个 VPN 网关建立多个连接。 例如，多站点连接配置。

在为 Azure Stack 创建和配置 VPN 网关之前，请查看 [Azure Stack 网络的注意事项](azure-stack-network-differences.md)，以了解 Azure Stack 的配置与 Azure 的不同之处。

>[!NOTE]
>在 Azure 中，VPN 网关 SKU 选择的带宽吞吐量必须被划分在连接到网关的所有站点。 但是，在 Azure Stack 中 VPN 网关 SKU 的带宽值应用于连接到网关的每个连接资源。
>
> 例如：
> * 在 Azure 中，基本 VPN 网关 SKU 可以容纳大约 100 Mbps 的聚合吞吐量。 如果创建两个连接到该 VPN 网关，并且一个连接使用 50 Mbps 的带宽，则 50 Mbps 可供另一个连接。
> * 在 Azure Stack*每个*基本 VPN 网关 SKU 连接获取分配 100 Mbps 的吞吐量。

## <a name="configuring-a-vpn-gateway"></a>配置 VPN 网关

VPN 网关连接需依赖于多个具有特定设置的资源。 大多数资源可单独进行配置，但在某些情况下，必须按特定的顺序配置这些资源。

### <a name="settings"></a>设置

为每个资源选择的设置对于成功创建连接至关重要。

有关 VPN 网关的各个资源和设置的信息，请参阅[关于 Azure Stack 的 VPN 网关设置](azure-stack-vpn-gateway-settings.md)。 本文将会介绍：

* 网关类型、VPN 类型和连接类型。
* 网关子网、本地网络网关和可能需要考虑的其他资源设置。

### <a name="deployment-tools"></a>部署工具

可以使用一个配置工具（如 Azure 门户）创建和配置资源。 稍后您可能会切换到另一个工具，如 PowerShell 来配置其他资源或修改现有资源时适用。 目前，不能在 Azure 门户中配置的每个资源和资源设置。 每个连接拓扑的文章中的说明指定了何时需要特定配置工具。

## <a name="connection-topology-diagrams"></a>连接拓扑图

有不同的配置可用于 VPN 网关连接。 确定哪种配置最适合自己的需要。 在以下部分，可以查看有关以下 VPN 网关连接的信息和拓扑图示：

* 可用的部署模型
* 可用的配置工具
* 直接转到某篇文章的链接（如果适用）

以下部分中的图示和说明可帮助你选择符合要求的连接拓扑。 这些图示显示主要基准拓扑，但也可以使用这些图示作为指导来构建更复杂的配置。

## <a name="site-to-site-and-multi-site-ipsecike-vpn-tunnel"></a>站点到站点和多站点 （IPsec/IKE VPN 隧道）

### <a name="site-to-site"></a>站点到站点

一个*站点到站点*(S2S) VPN 网关连接是通过 IPsec/IKE （IKEv1 或 IKEv2） VPN 隧道连接。 此类型的连接要求的 VPN 设备，是位于的本地，并且分配的公共 IP 地址。 此设备不能位于 nat 之后。 S2S 连接可以用于跨界和混合配置。

![站点到站点 VPN 连接配置示例](media/azure-stack-vpn-gateway-about-vpn-gateways/vpngateway-site-to-site-connection-diagram.png)

### <a name="multi-site"></a>多站点

一个*多站点*连接是站点到站点连接的变体。 可从虚拟网络网关创建多个 VPN 连接（通常是连接到多个本地站点）。 当使用多个连接，必须使用基于路由的 VPN 类型 （称为动态网关使用经典 Vnet 时）。 由于每个虚拟网络只能有一个 VPN 网关，因此通过网关的所有连接都共享可用带宽。

![Azure VPN 网关多站点连接示例](media/azure-stack-vpn-gateway-about-vpn-gateways/vpngateway-multisite-connection-diagram.png)

## <a name="gateway-skus"></a>网关 SKU

为 Azure Stack 创建虚拟网络网关时，需要指定要使用的网关 SKU。 支持以下 VPN 网关 SKU：

* 基本
* 标准
* 高性能

当您选择更高版本网关 SKU，如优于基本、 标准或高性能高于标准或基本时，到网关分配更多的 Cpu 和网络带宽。 如此一来，网关可以对虚拟网络支持更高的网络吞吐量。

Azure Stack 不支持超高性能网关 SKU，专门搭配 Express Route 使用。

当你选择的 SKU，请考虑以下：

* Azure Stack 不支持基于策略的网关。
* 基本 SKU 不支持边界网关协议 (BGP)。
* 在 Azure Stack 中不支持 ExpressRoute VPN 网关共存配置。
* 可以在高性能 SKU 上配置主动-主动 S2S VPN 网关连接。

## <a name="estimated-aggregate-throughput-by-sku"></a>按 SKU 列出的估计聚合吞吐量

下表显示网关 SKU 网关类型和估计的聚合吞吐量：

|   | VPN 网关吞吐量 *(1)* | VPN 网关最大 IPsec 隧道数 *(2)* |
|-------|-------|-------|
|**基本 SKU** ***(3)***    | 100 Mbps  | 20    |
|**标准 SKU**       | 100 Mbps  | 20    |
|**高性能 SKU** | 200 Mbps    | 10    |

**表格注释：**

*注释 (1)* -VPN 吞吐量是不能保证该吞吐量跨界连接在 Internet 上。 它是可能的最大吞吐量。  
*注释 (2)* -最大隧道是每个 Azure Stack 部署的所有订阅总数。  
*注释 (3)* -基本 sku 不支持 BGP 路由。

>[!NOTE]
>两个 Azure Stack 部署之间，可以创建只有一个站点到站点 VPN 连接。 这是由于只允许一个到相同的 IP 地址的 VPN 连接的平台中的限制。 由于 Azure Stack 利用多租户网关，它在 Azure Stack 系统的所有 VPN 网关使用单个公共 IP，可以有两个 Azure Stack 系统之间只能有一个 VPN 连接。 此限制也适用于连接到任何使用单个 IP 地址的 VPN 网关的多个站点到站点 VPN 连接。 Azure Stack 不允许多个本地网络网关资源，若要创建使用相同的 IP 地址。

## <a name="next-steps"></a>后续步骤

[Azure Stack 的 VPN 网关配置设置](azure-stack-vpn-gateway-settings.md)
