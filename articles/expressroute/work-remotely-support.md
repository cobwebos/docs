---
title: 使用 Azure ExpressRoute 支持远程用户
description: 本页介绍在新冠肺炎疫情期间如何利用 Azure ExpressRoute 来实现远程工作。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 03/22/2020
ms.author: duau
ms.openlocfilehash: d51f47b73fe572ce81d3e7b54f902f94fcd11b5b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89567667"
---
# <a name="using-azure-expressroute-to-create-hybrid-connectivity-to-support-remote-users"></a>使用 Azure ExpressRoute 创建混合连接以支持远程用户

本文介绍如何利用 ExpressRoute、Azure、Microsoft 网络和 Azure 合作伙伴生态系统进行远程工作。

## <a name="connecting-to-azure-services-with-expressroute"></a>通过 ExpressRoute 连接到 Azure 服务

>[!NOTE]
>本文介绍如何利用 ExpressRoute、Azure、Microsoft 网络和 Azure 合作伙伴生态系统进行远程工作，以及如何缓解因新冠肺炎危机而面临的网络问题。
>

[ExpressRoute](expressroute-introduction.md) 是一项 Azure 服务，可在 Microsoft 数据中心与本地环境或共同租用设施中的基础结构之间实现专用连接。 ExpressRoute 连接不通过公共 Internet 。 与通过 Internet 的典型连接相比，ExpressRoute 连接提供安全的连接、可靠性、速度和较低的一致延迟。

## <a name="useful-links"></a>有用链接

* [如何增加现有 ExpressRoute 线路的带宽](expressroute-howto-circuit-portal-resource-manager.md#modify)
* [ExpressRoute 监视、指标和警报](expressroute-monitoring-metrics-alerts.md#expressroute-gateway-connections-in-bitsseconds)
* [基于 ExpressRoute 的路由优化](expressroute-optimize-routing.md)
* [适用于 Microsoft 365 的 Azure ExpressRoute](/microsoft-365/enterprise/azure-expressroute)
* [非对称路由注意事项](expressroute-asymmetric-routing.md)
* [如何在 Azure 门户中打开支持请求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)

## <a name="troubleshoot"></a>故障排除

* [验证 ExpressRoute 连接](expressroute-troubleshooting-expressroute-overview.md)
* 在[资源管理器](expressroute-troubleshooting-arp-resource-manager.md)和[经典](expressroute-troubleshooting-arp-classic.md)中获取 ARP 表
* [重置故障线路](reset-circuit.md)
* [排查网络性能问题](expressroute-troubleshooting-network-performance.md)

## <a name="next-steps"></a>后续步骤

* [了解 ExpressRoute 连接模型](expressroute-connectivity-models.md)
* 了解 ExpressRoute 连接和路由域。 请参阅 [ExpressRoute 线路和路由域](expressroute-circuit-peerings.md)
* 查找服务提供商。 请参阅 [ExpressRoute 合作伙伴和对等位置](expressroute-locations.md)
* 确保符合所有先决条件。 请参阅 [ExpressRoute 先决条件](expressroute-prerequisites.md)。
* [创建和修改 ExpressRoute 线路](expressroute-howto-circuit-portal-resource-manager.md)
* [创建和修改 ExpressRoute 线路的对等互连](expressroute-howto-routing-portal-resource-manager.md)
* [将虚拟网络连接到 ExpressRoute 线路](expressroute-howto-linkvnet-portal-resource-manager.md)
