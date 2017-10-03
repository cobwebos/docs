---
title: "应用服务环境 v1 简介"
description: "了解有关可提供安全、加入 VNet 的专用缩放单位用于运行所有应用的应用服务环境 v1 功能。"
services: app-service
documentationcenter: 
author: stefsch
manager: erikre
editor: 
ms.assetid: 78e6d4f5-da46-4eb5-a632-b5fdc17d2394
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.translationtype: HT
ms.sourcegitcommit: 8f9234fe1f33625685b66e1d0e0024469f54f95c
ms.openlocfilehash: b13f726ada6deba67d5ed484eca769fed354e400
ms.contentlocale: zh-cn
ms.lasthandoff: 09/20/2017

---
# <a name="introduction-to-app-service-environment-v1"></a>应用服务环境 v1 简介

> [!NOTE]
> 本文介绍应用服务环境 v1。  应用服务环境有一个较新版本，此版本更易于使用并在更强大的基础结构上运行。 若要深入了解新版本，请先参阅[应用服务环境简介](intro.md)。
> 

## <a name="overview"></a>概述
应用服务环境是 [Azure 应用服务](../app-service-web-overview.md)的一项[高级][PremiumTier]服务计划选项，可提供完全隔离的专用环境来安全地大规模运行 Azure 应用服务应用，包括 Web 应用、移动应用和 API 应用。  

应用服务环境适用于有以下要求的应用程序工作负荷：

* 极高的缩放性
* 隔离和安全的网络访问

客户可以在单个 Azure 区域和多个 Azure 区域创建多个应用服务环境。  因此，应用服务环境适用于水平缩放无状态应用层，支持高 RPS 工作负荷。

应用服务环境可在隔离后只运行单个客户的应用程序，并可始终部署到虚拟网络中。  客户对于入站和出站的应用网络流量都有更细微的控制，且应用程序可以通过虚拟网络创建与本地公司资源的高速安全连接。

有关应用服务环境如何提供高缩放性和安全网络访问的概述，请参阅有关应用服务环境的 [AzureCon 深入探讨][AzureConDeepDive]！

有关使用多个应用服务环境横向调整规模的深入探讨，请参阅有关如何设置[按地理位置分布的应用的数量][GeodistributedAppFootprint]的文章。

若要查看“AzureCon 深入探讨”中所示的安全体系结构的配置方式，请参阅有关使用应用服务环境实现[分层安全体系结构](app-service-app-service-environment-layered-security.md)的文章。

在应用服务环境中运行的应用的访问权限可能受到 Web 应用程序防火墙 (WAF) 等上游设备的管制。  有关[配置应用服务环境的 WAF](app-service-app-service-environment-web-application-firewall.md) 的文章介绍了这种方案。 

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="dedicated-compute-resources"></a>专用计算资源
应用服务环境中的所有计算资源专属于单个订阅，对于一个应用服务环境，最多可以配置最多五十 (50) 个计算资源让单个应用程序独占使用。

应用服务环境由前端计算资源池，以及一到三个工作线程计算资源池组成。 

前端池包含负责处理 SSL 终止以及应用服务环境中应用请求的自动负载均衡的计算资源。 

每个工作线程池包含分配给[应用服务计划][AppServicePlan]的计算资源，而这些资源又包含一个或多个 Azure 应用服务应用。  由于应用服务环境中可能有多达三个不同的工作线程池，因此可以灵活地为每个工作线程池选择不同的计算资源。  

例如，可以针对主要用于开发或测试应用的应用服务方案创建一个计算资源较不强大的工作线程池。  第二个（甚至第三个）工作线程池可以使用比较强大的计算资源，以供应用服务计划运行生产应用。

有关前端和工作线程池可用计算资源数量的详细信息，请参阅[如何配置应用服务环境][HowToConfigureanAppServiceEnvironment]。  

有关应用服务环境中支持的可用计算资源大小的详细信息，请参阅[应用服务定价][AppServicePricing]页，并查看高级定价层中应用服务环境可用的选项。

## <a name="virtual-network-support"></a>虚拟网络支持
可以在 Azure Resource Manager 虚拟网络**或者**经典的部署模型虚拟网络中创建应用服务环境（[关于虚拟网络的更多信息][MoreInfoOnVirtualNetworks]）。  由于应用服务环境始终位于虚拟网络中（更准确地说，是位于虚拟网络的子网中），因此可以利用虚拟网络的安全功能来控制入站和出站网络通信。  

应用服务环境既可以是面向 Internet 的（使用公共 IP 地址），也可以是面向内部的（只使用 Azure 内部负载均衡器 (ILB) 地址）。

可以使用[网络安全组][NetworkSecurityGroups]将入站网络通信限制为应用服务环境所在的子网。  这样，便可以在上游设备和服务（例如 Ｗeb 应用程序防火墙和网络 SaaS 提供者）后面运行应用。

应用还经常需要访问公司资源，例如内部数据库和 Web 服务。  常见的做法是让这些终结点仅可用于在 Azure 虚拟网络中流动的内部网络流量。  一旦应用服务环境加入到与内部服务相同的虚拟网络，在此环境中运行的应用即可访问这些内部服务，包括可通过[站点到站点][SiteToSite]和 [Azure ExpressRoute][ExpressRoute] 连接访问的终结点。

有关应用服务环境如何在虚拟网络和本地网络中使用的详细信息，请参阅以下文章：[网络体系结构][NetworkArchitectureOverview]、[控制入站流量][ControllingInboundTraffic]和[安全连接到后端][SecurelyConnectingToBackends]。 

## <a name="getting-started"></a>入门
若要开始使用应用服务环境，请参阅[如何创建应用服务环境][HowToCreateAnAppServiceEnvironment]

有关应用服务环境网络体系结构的概述，请参阅[网络体系结构概述][NetworkArchitectureOverview]一文。

有关在应用服务环境中使用 ExpressRoute 的详细信息，请参阅以下文章：[Express Route 和应用服务环境][NetworkConfigDetailsForExpressRoute]。

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[PremiumTier]: http://azure.microsoft.com/pricing/details/app-service/
[MoreInfoOnVirtualNetworks]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[AppServicePlan]: ../azure-web-sites-web-hosting-plans-in-depth-overview.md
[HowToCreateAnAppServiceEnvironment]: app-service-web-how-to-create-an-app-service-environment.md
[LogicApps]: http://azure.microsoft.com/documentation/articles/app-service-logic-what-are-logic-apps/
[AzureConDeepDive]:  https://azure.microsoft.com/documentation/videos/azurecon-2015-deploying-highly-scalable-and-secure-web-and-mobile-apps/
[GeodistributedAppFootprint]:  app-service-app-service-environment-geo-distributed-scale.md
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[SiteToSite]: https://azure.microsoft.com/documentation/articles/vpn-gateway-site-to-site-create/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
[HowToConfigureanAppServiceEnvironment]:  app-service-web-configure-an-app-service-environment.md
[ControllingInboundTraffic]:  app-service-app-service-environment-control-inbound-traffic.md
[SecurelyConnectingToBackends]:  app-service-app-service-environment-securely-connecting-to-backend-resources.md
[NetworkArchitectureOverview]:  app-service-app-service-environment-network-architecture-overview.md
[NetworkConfigDetailsForExpressRoute]:  app-service-app-service-environment-network-configuration-expressroute.md
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/ 

<!-- IMAGES -->



