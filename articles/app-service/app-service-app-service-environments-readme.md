---
title: "应用服务环境 | Microsoft Docs"
description: "什么是 Azure 应用服务环境？ 应用服务环境简介。"
keywords: "azure 应用服务环境, 虚拟网络, 安全网络"
services: app-service
documentationcenter: 
author: stefsch
manager: erikre
editor: 
ms.assetid: 1db5c057-3c56-4537-b580-cdd21fe3f3a7
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/01/2016
ms.author: stefsch
translationtype: Human Translation
ms.sourcegitcommit: df2fa1d0790f8942a0350f9734232cc4b25cadc1
ms.openlocfilehash: a54f559801e1a4f4b752bc2268ea8d6cb20b1267
ms.lasthandoff: 12/01/2016


---
# <a name="app-service-environment-documentation"></a>应用服务环境文档
应用服务环境是 Azure 应用服务的一项[高级][PremiumTier]服务计划选项，可提供完全隔离的专用环境来安全地大规模运行 Azure 应用服务应用，包括 [Web 应用][WebApps]、[移动应用][MobileApps]和 [API 应用][APIApps]。  

应用服务环境适用于有以下要求的应用程序工作负荷：

* 极高的缩放性
* 隔离和安全的网络访问

客户可以在单个 Azure 区域和多个 Azure 区域创建多个应用服务环境。  因此，应用服务环境适用于水平缩放无状态应用层，支持高 RPS 工作负荷。

应用服务环境可在隔离后只运行单个客户的应用程序，并可始终部署到虚拟网络中。  客户可以使用[网络安全组][NetworkSecurityGroups]对入站和出站应用程序网络流量进行精细控制。  应用程序也可以通过虚拟网络建立到本地公司资源的高速安全连接。

应用经常需要访问公司资源，例如内部数据库和 Web 服务。  在应用服务环境中运行的应用可以通过[站点到站点][SiteToSite] VPN 和 [Azure ExpressRoute][ExpressRoute] 连接访问那些允许访问的资源。

* [什么是应用服务环境？](../app-service-web/app-service-app-service-environment-intro.md)
* [创建应用服务环境](../app-service-web/app-service-web-how-to-create-an-app-service-environment.md)
* [在应用服务环境中创建应用](../app-service-web/app-service-web-how-to-create-a-web-app-in-an-ase.md)
* [在应用服务环境中创建和使用内部负载均衡器](../app-service-web/app-service-environment-with-internal-load-balancer.md)
* [配置应用服务环境](../app-service-web/app-service-web-configure-an-app-service-environment.md) 
* [在应用服务环境中调整应用规模](../app-service-web/app-service-web-scale-a-web-app-in-an-app-service-environment.md)
* [网络安全和体系结构](../app-service-web/app-service-app-service-environment-network-architecture-overview.md)

## <a name="how-tos"></a>操作说明
[!INCLUDE [app-service-blueprint-app-service-environment](../../includes/app-service-blueprint-app-service-environment.md)]

## <a name="videos"></a>视频
>[!VIDEO https://channel9.msdn.com/Events/Ignite/2016/BRK3205/player]

>[!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON325/player]

>[!VIDEO https://channel9.msdn.com/Events/Ignite/2015/BRK3715/player]



<!-- LINKS -->
[PremiumTier]: http://azure.microsoft.com/pricing/details/app-service/
[WebApps]: http://azure.microsoft.com/documentation/articles/app-service-web-overview/
[MobileApps]: http://azure.microsoft.com/documentation/articles/app-service-mobile-value-prop-preview/
[APIApps]: http://azure.microsoft.com/documentation/articles/app-service-api-apps-why-best-platform/
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[SiteToSite]: https://azure.microsoft.com/documentation/articles/vpn-gateway-site-to-site-create/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/

