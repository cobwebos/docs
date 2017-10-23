---
title: "关于 ExpressRoute 虚拟网络网关 | Microsoft Docs"
description: "了解 ExpressRoute 的虚拟网络网关。"
services: expressroute
documentationcenter: na
author: cherylmc
manager: carmonm
editor: 
tags: azure-resource-manager, azure-service-management
ms.assetid: 7e0d9658-bc00-45b0-848f-f7a6da648635
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/05/2017
ms.author: cherylmc
ms.openlocfilehash: a6363fa380d0bab05d7500141cc6019d1d3f68b8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="about-virtual-network-gateways-for-expressroute"></a>关于 ExpressRoute 的虚拟网络网关
虚拟网络网关用于在 Azure 虚拟网络和本地位置之间发送网络流量。 配置 ExpressRoute 连接时，必须创建并配置虚拟网络网关和虚拟网络网关连接。

创建虚拟网络网关时，需要指定几项设置。 其中一个必要设置为，指定是否将网关用于 ExpressRoute 或站点到站点 VPN 流量。 在 Resource Manager 部署模型中，该设置为“-GatewayType”。

如果是通过专用连接发送网络流量，可以使用网关类型“ExpressRoute”。 也称为 ExpressRoute 网关。 如果网络流量通过公共 Internet 以加密形式发送，可以使用网关类型“Vpn”。 称为 VPN 网关。 站点到站点连接、点到站点连接和 VNet 到 VNet 连接都使用 VPN 网关。

对于每种网关类型，每个虚拟网络只能有一个虚拟网络网关。 例如，一个虚拟网络网关使用 -GatewayType Vpn，另一个使用 -GatewayType ExpressRoute。 本文重点介绍 ExpressRoute 虚拟网络网关。

## <a name="gwsku"></a>网关 SKU
[!INCLUDE [expressroute-gwsku-include](../../includes/expressroute-gwsku-include.md)]

如果想要将网关升级为功能更强大的网关 SKU，在大多数情况下，可以使用“Resize-AzureRmVirtualNetworkGateway”PowerShell cmdlet。 此方法适用于升级到 Standard 和 HighPerformance SKU。 但是，若要升级到 UltraPerformance SKU，需要重新创建网关。

### <a name="aggthroughput"></a>按网关 SKU 列出的估计聚合吞吐量
下表显示网关类型和估计的聚合吞吐量。 此表适用于 Resource Manager 与经典部署模型。

[!INCLUDE [expressroute-table-aggthroughput](../../includes/expressroute-table-aggtput-include.md)]

> [!IMPORTANT]
> 应用程序吞吐量取决于多种因素，例如端到端延迟和应用程序打开的流量数。 表中的数字表示应用程序在理想环境下理论上可达到的上限。 
> 
>

## <a name="resources"></a>REST API 和 PowerShell cmdlet
有关将 REST API 和 PowerShell cmdlet 用于虚拟网络网关配置的其他技术资源和特定语法要求，请参阅以下页面：

| **经典** | **Resource Manager** |
| --- | --- |
| [PowerShell](https://msdn.microsoft.com/library/mt270335.aspx) |[PowerShell](https://msdn.microsoft.com/library/mt163510.aspx) |
| [REST API](https://msdn.microsoft.com/library/jj154113.aspx) |[REST API](https://msdn.microsoft.com/library/mt163859.aspx) |

## <a name="next-steps"></a>后续步骤
有关可用连接配置的详细信息，请参阅 [ExpressRoute 概述](expressroute-introduction.md)。 

