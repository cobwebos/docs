---
title: 微软全球网络 - Azure
description: 描述微软如何建立快速可靠的全球网络
services: networking
documentationcenter: ''
author: KumudD
manager: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/13/2019
ms.author: kumud
ms.reviewer: ypitsch
ms.openlocfilehash: 10a061163447a60f1c25b386ef28028436284650
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75453072"
---
# <a name="microsoft-global-network"></a>Microsoft 全球网络

微软拥有并运营着世界上最大的骨干网络之一。 这种全球和复杂的架构跨越 100，000 英里，连接我们的数据中心和客户。 
 
每天，世界各地的客户都会将数万亿个请求连接到 Microsoft Azure、必应、Dynamics 365、Office 365、XBox 等。 无论类型如何，客户都期望我们的服务能够即时可靠性和响应能力。 
 
[微软全球网络](https://azure.microsoft.com/global-infrastructure/global-network/)（WAN） 是提供巨大云体验的核心部分。 我们的全球网络将 Microsoft[数据中心](https://azure.microsoft.com/global-infrastructure/)连接到全球 54 个 Azure 区域和大型边缘节点网格，提供可用性、容量和灵活性，以满足任何需求。

![Microsoft 全球网络](./media/microsoft-global-network/microsoft-global-wan.png)
 
## <a name="get-the-premium-cloud-network"></a>获取优质云网络
 
当您使用 Microsoft 云时，选择[最佳体验](https://www.sdxcentral.com/articles/news/azure-tops-aws-gcp-in-cloud-performance-says-thousandeyes/2018/11/)非常简单。 从客户流量通过我们战略性边缘节点进入我们的全球网络的那一刻起，您的数据就以接近光速的速度通过优化的路线传输。 这可确保最佳延迟，实现最佳性能。 这些边缘节点通过超过 145 个位置的数千个连接与 3500 多个独特的 Internet 合作伙伴（对等方）互连，为我们的互连战略奠定了基础。 
 
无论是从伦敦到东京，还是从华盛顿特区到洛杉矶，网络性能都会被量化，并受延迟、抖动、数据包丢失和吞吐量等因素的影响。  在 Microsoft，我们更喜欢并使用直接互连而不是传输链路，这样可以保持响应流量对称，并有助于尽可能短和简单地保持跃点、对等方和路径。 

例如，如果伦敦的用户尝试访问东京的服务，那么 Internet 流量就会进入我们在伦敦的一个边缘，通过法国、我们在欧洲和印度之间的跨阿拉伯路径，然后到达托管该服务的日本。 响应流量是对称的。 这有时称为[冷土豆路由](https://en.wikipedia.org/wiki/Hot-potato_and_cold-potato_routing)，这意味着流量在微软网络上停留的时间尽可能长，然后再将其传递。  
  
那么，在使用 Microsoft 服务时，这是否意味着任何和所有流量？ 是的，数据中心之间、Microsoft Azure 内或 Microsoft 服务（如虚拟机、Office 365、XBox、SQL DB、存储和虚拟网络）之间的任何流量都在我们的全球网络中路由，并且永远不会通过公共 Internet 路由，以确保最佳性能和完整性。  
 
在地铁、陆地和海底路径的光纤容量和多样性方面进行大量投资，对于我们保持一致和高水平的服务级别至关重要，同时推动我们的云和在线服务的极端增长。 我们全球网络最近增加了我们的[MAREA](https://www.submarinecablemap.com/#/submarine-cable/marea)海底电缆，这是业界首款穿越海底的开通线系统（OLS），位于西班牙毕尔巴鄂和美国弗吉尼亚州弗吉尼亚海滩之间，以及位于美国纽约和都柏林之间的[AEC，](https://www.submarinecablemap.com/#/submarine-cable/aeconnect-1)以及日本东京和美国俄勒冈州波特兰之间的[新太平洋（NCP）。](https://www.submarinecablemap.com/#/submarine-cable/new-cross-pacific-ncp-cable-system) 
 

## <a name="our-network-is-your-network"></a>我们的网络就是您的网络

我们投入了 20 年的经验，以及对网络的大量投资，以确保始终获得最佳性能。 企业可以充分利用我们的网络资产，并在顶部构建高级叠加架构。 
 
Microsoft Azure 提供了最丰富的服务和功能组合，使客户能够随时随地快速轻松地构建、扩展和满足网络要求。 我们的连接服务系列跨越区域、混合和云中点对点和站点到站点架构以及全球 IP 传输方案之间的虚拟网络对等互连。  对于希望将自己的数据中心或网络连接到 Azure 的企业，或具有海量数据引入或传输需求的客户[，ExpressRoute](../expressroute/expressroute-introduction.md)和[ExpressRoute Direct](../expressroute/expressroute-erdirect-about.md)提供高达 100 Gbps 的带宽选项，直接连接到 Microsoft 全球网络的全球网络，位于世界各地的对等位置。  
 
[ExpressRoute 全球覆盖](../expressroute/expressroute-global-reach.md)旨在补充服务提供商的 WAN 实现，并连接世界各地的本地站点。 如果运行全局操作，则可以使用 ExpressRoute 全球覆盖与您首选的本地服务提供商结合使用 Microsoft 全球网络连接所有全球站点。 通过 Azure 虚拟 WAN 扩展云中的新网络以涵盖大量分支站点，从而能够借助 SDWAN & VPN 设备（即客户场所设备或 CPE）将分支机构无缝连接到 Microsoft 全球网络，并内置易用性和自动化连接和配置管理。 
 
[全局 VNet 对等互连](../virtual-network/virtual-network-peering-overview.md)使客户能够跨区域无缝连接两个或多个 Azure 虚拟网络。 对等后，虚拟网络将显示为一个。 对等虚拟网络中虚拟机之间的流量通过 Microsoft 骨干基础结构路由，就像流量在同一虚拟网络中的虚拟机之间路由一样 - 只能通过专用 IP 地址。 
 

## <a name="well-managed-using-software-defined-innovation"></a>使用软件定义的创新进行良好管理

Microsoft 运行世界领先的云之一，在构建和管理高性能全球基础架构方面获得了许多洞察力和经验。  
 
我们坚持一套强有力的运营原则： 
 
- 跨网络的各个层使用同类最佳的交换硬件。  
- 部署对最终用户没有影响的新功能。  
- 尽可能快地跨车队安全可靠地推出更新。 小时而不是几周。  
- 利用云级深度遥测和全自动故障缓解。  
- 使用统一和软件定义的网络技术来控制网络中的所有硬件元素。  消除重复并减少故障。 
 
这些原则适用于网络的所有层：从主机网络接口、交换平台、数据中心的网络功能（如负载均衡器）一直使用我们的交通工程平台和光纤网络，一直连接到 WAN。  
 
Azure 及其网络的指数级增长已经达到我们最终意识到人类直觉不再依赖来管理全球网络操作的点。 为了满足验证网络上长期、中期和短期变化的需要，我们开发了一个平台来综合镜像和模拟我们的生产网络。 创建镜像环境和运行数百万次模拟的能力使我们能够测试软件和硬件更改及其影响，然后再将其提交到我们的生产平台和网络。 

## <a name="next-steps"></a>后续步骤
- [了解有关 Azure 中提供的网络服务](https://azure.microsoft.com/product-categories/networking/)
