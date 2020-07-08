---
title: Azure 对等互连服务概述
description: 了解 Azure 对等互连服务概述
services: peering-service
author: derekolo
ms.service: peering-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Infrastructure-services
ms.date: 05/18/2020
ms.author: derekol
ms.openlocfilehash: 548d4f90bd9632e4807547120ac1be589668e8fa
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84871337"
---
# <a name="azure-peering-service-overview"></a>Azure 对等互连服务概述

Azure 对等互连服务是一种网络服务，可增强客户与 Microsoft 云服务（例如 Office 365、Dynamics 365、软件即服务 (SaaS)、Azure 或可通过公共 Internet 访问的任何 Microsoft 服务）建立的连接。 Microsoft 在全球范围内与 internet 服务提供商（Isp）、internet exchange 合作伙伴（IXPs）和软件定义的云互连（SDCI）提供商合作，通过从客户到 Microsoft 网络的最佳路由提供可靠且高性能的公共连接。

使用对等互连服务时，客户可以在给定区域中选择连接良好的合作伙伴服务提供商。 公用连接经过优化，可实现高可靠性和从云服务到最终用户位置的最小延迟。

![与 Microsoft 云的分布式连接](./media/peering-service-about/peering-service-what.png)

客户还可以通过在 Azure 门户中注册对等互连服务连接，为 Microsoft 网络、BGP 路由监视和警报（通过在中注册对等互连服务连接）选择使用对等互连服务遥测数据（如用户延迟度量值）。 

若要使用对等互连服务，客户无需注册到 Microsoft。 唯一的要求是联系对[等服务合作伙伴](location-partners.md)以获取服务。 若要选择启用对等互连服务遥测，客户必须在 Azure 门户中注册它。

有关如何注册对等互连服务的说明，请参阅[使用 Azure 门户注册对等互连服务](azure-portal.md)。 

> [!NOTE]
> 本文适用于网络架构师，负责与云和 internet 建立企业连接。


## <a name="what-is-peering-service"></a>什么是对等互连服务？

对等互连服务为：

- 使用公共 internet 的 IP 服务。 
- 一个包含服务提供商的协作平台和一个增值服务，旨在通过服务提供商合作伙伴通过公共网络向 Microsoft 云提供最佳且可靠的路由。

对等互连服务不是 Azure ExpressRoute 或 VPN 产品等专用连接产品。

> [!NOTE]
> 有关 ExpressRoute 的详细信息，请参阅[expressroute 文档](https://docs.microsoft.com/azure/expressroute/)。
>

## <a name="background"></a>背景

Office 365、Dynamics 365 和任何其他 Microsoft SaaS 服务托管在多个 Microsoft 数据中心中，并且可从任何地理位置进行访问。 Microsoft 全球网络在世界各地具有 Microsoft Edge 状态点（PoP）位置，它可以通过其服务提供商连接到最终用户。 

Microsoft 和合作伙伴服务提供商确保注册到对等服务连接的前缀的流量进入并退出 Microsoft 全球网络上最近的 Microsoft Edge PoP 位置。 Microsoft 确保从注册到对等互连服务连接的前缀传出的网络流量在 Microsoft 全球网络上采用最近的 Microsoft Edge PoP 位置。

![Microsoft 网络和公共连接](./media/peering-service-about/peering-service-background-final.png)

> [!NOTE]
> 有关 Microsoft 全球网络的详细信息，请参阅[microsoft 全球网络](https://docs.microsoft.com/azure/networking/microsoft-global-network)。
>

## <a name="why-use-peering-service"></a>为什么使用对等互连服务？

寻找 internet 首次访问云或考虑 SD WAN 体系结构或使用 Microsoft SaaS 服务高利用率的企业需要可靠且高性能的 internet 连接。 客户可以通过使用对等互连服务来实现这种转换。 Microsoft 和服务提供商已经建立了合作，以提供与 Microsoft 云的可靠、以性能为中心的公开连接。 此处列出了一些主要的客户功能：

- 通过 internet 进行的最佳公共路由 Microsoft Azure 云服务，以获得最佳性能和可靠性。
- 能够选择首选的服务提供商以连接到 Microsoft 云。
- 流量见解，如延迟报告和前缀监视。
- Microsoft 云中的最佳网络跃点（作为跃点）。
- 路由分析和统计信息：（[BGP](https://en.wikipedia.org/wiki/Border_Gateway_Protocol)）路由异常事件（泄漏或劫持检测）和不理想路由。

### <a name="robust-reliable-peering"></a>可靠、可靠的对等互连

对等互连服务使用两种类型的冗余：

- **本地冗余**

   Microsoft 和服务提供商相互连接跨多个 Microsoft Edge PoP 位置，以提供对等互连服务。 在每个位置，互连必须支持跨两个路由器的故障转移。

   每个对等互连位置都预配了冗余的和不同的对等互连链接。

- **异地冗余**

   Microsoft 已与多个大都市位置的服务提供商互连，因此，如果其中一个边缘节点的性能下降，流量将通过备用站点与 Microsoft 进行路由。 Microsoft 通过使用基于 SDN 的路由策略来路由其全球网络中的流量，以获得最佳性能。

    这种类型的冗余使用最短路由路径，方法是始终选择最靠近最终用户的 Microsoft Edge PoP，并确保客户是远离 Microsoft 的一个网络跃点（作为跃点）。

   ![异地冗余](./media/peering-service-about/peering-service-geo-shortest.png)

### <a name="optimal-routing"></a>最佳路由

以下路由方法是首选方法：

-  **冷薯片路由**

   软件定义的冷刷路由方法可控制源自 Microsoft 云的网络流量。 它确保流量始终位于高容量、低延迟和高可靠性的 Microsoft 全球网络上，直到达到目标。
   
   不使用冷插技术的路由称为热登录路由。 通过热登录路由，来自 Microsoft 云的流量将通过 internet。

   ![冷薯片路由](./media/peering-service-about/peering-service-cold-potato.png)

### <a name="monitoring-platform"></a>监视平台

   提供服务监视来分析客户流量和路由，并提供以下功能： 

-  **Internet BGP 路由异常检测**
          
   此服务用于检测路由异常事件（如路由劫持到客户前缀）并发出警报。

-  **客户延迟**

   此服务监视客户位置与 Microsoft 之间的路由性能。 
   
   路由性能是通过验证从客户端获取的往返时间来衡量的，以到达 Microsoft 边缘 PoP。 客户可以查看不同地理位置的滞后时间报表。

   监视将捕获事件，以防任何服务性能下降。

   ![对等互连服务的监视平台](media/peering-service-about/peering-service-latency-report.png)

### <a name="traffic-protection"></a>流量保护

仅通过在向对等互连服务注册客户时定义的首选路径进行路由。

Microsoft 保证通过首选路径路由流量，即使检测到恶意活动也是如此。

在 Azure 门户中报告 BGP 路由异常（如果有）。

## <a name="next-steps"></a>后续步骤

- 若要了解对等互连服务连接的详细信息，请参阅对[等互连服务连接](connection.md)。
- 若要了解对等互连服务连接遥测数据，请参阅[对等互连服务连接遥测数据](connection-telemetry.md)。
- 若要查找服务提供商合作伙伴，请参阅对[等互连服务合作伙伴和位置](location-partners.md)。
- 若要加入对等互连服务连接，请参阅[载入对等互连服务模型](onboarding-model.md)。
- 若要使用 Azure 门户注册连接，请参阅[使用 Azure 门户注册对等连接服务连接](azure-portal.md)。
- 若要度量遥测，请参阅[度量连接遥测](measure-connection-telemetry.md)。
