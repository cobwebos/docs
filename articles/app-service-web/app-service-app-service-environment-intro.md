<!-- not suitable for Mooncake -->


<properties 
	pageTitle="Azure 环境简介" 
	description="了解有关可提供安全、加入 VNet 的专用缩放单位用于运行所有应用的 Azure 环境功能。" 
	services="app-service" 
	documentationCenter="" 
	authors="ccompy" 
	manager="wpickett" 
	editor=""/>

<tags
	ms.service="app-service"
	ms.date="07/11/2016"
	wacn.date=""/>  


# Azure 环境简介

## 概述 ##
Azure 环境是 Azure 的一项[高级][PremiumTier]服务方案选项，可提供完全隔离的专用环境，以便你安全地大规模运行 Azure Web 应用，包括 [Web Apps][WebApps]、[Mobile Apps][MobileApps] 和 [API Apps][APIApps]。

Azure 环境非常适合有以下要求的应用程序工作负荷：

- 极高的缩放性
- 隔离和安全的网络访问

客户可以在单个 Azure 区域，以及跨多个 Azure 区域中创建多个 Azure 环境。这使得 Azure 环境很适合用于水平缩放无状态应用层，以支持高 RPS 工作负荷。

Azure 环境已经过隔离，可运行只有单个客户的应用程序，且始终部署到虚拟网络。客户对于入站和出站的应用网络流量都有更细微的控制，且应用程序可以通过虚拟网络创建与本地公司资源的高速安全连接。

[应用程序服务环境自述文件](/documentation/articles/app-service-app-service-environments-readme/)中提供了有关 Azure 环境的所有文章和操作说明。

有关 Azure 环境如何提供高缩放性和安全网络访问的概述，请参阅有关 Azure 环境的 [AzureCon 深入探讨][AzureConDeepDive]！

有关使用多个 Azure 环境水平缩放的深入探讨，请参阅有关如何设置[地理分散的应用占用空间][GeodistributedAppFootprint]的文章。

若要查看“AzureCon 深入探讨”中所示的安全体系结构的配置方式，请参阅有关使用 Azure 环境实现[分层安全体系结构](/documentation/articles/app-service-app-service-environment-layered-security/)的文章。

在 Azure 环境中运行的应用的访问权限可能受到 Web 应用程序防火墙 (WAF) 等上游设备的管制。有关[配置 Azure 环境的 WAF](/documentation/articles/app-service-app-service-environment-web-application-firewall/) 的文章介绍了这种方案。

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## 专用计算资源 ##
Azure 环境中的所有计算资源专属于单个订阅，对于一个 Azure 环境，最多可以配置最多五十 (50) 个计算资源让单个应用程序独占使用。

Azure 环境由一个前端计算资源池，以及一到三个辅助角色计算资源池组成。

前端池包含负责处理 SSL 终止以及 Azure 环境中应用请求的自动负载平衡的计算资源。

每个辅助角色池都包含分配给 [App Service 计划][AppServicePlan]的计算资源，而这些资源又包含一个或多个 Azure Web Apps。由于 Azure 环境中可能有多达三个不同的辅助角色池，因此你可以灵活地为每个辅助角色池选择不同的计算资源。

例如，你可以针对主要用于开发或测试应用的 App Service 方案创建一个计算资源较不强大的工作线程池。第二个（甚至第三个）工作线程池可以使用比较强大的计算资源，以供 App Service 计划运行生产应用。

有关前端池和辅助角色池可用计算资源数量的更多详细信息，请参阅[如何配置 Azure 环境][HowToConfigureanAppServiceEnvironment]。

有关 Azure 环境中支持的可用计算资源大小的详细信息，请参阅 [Azure 定价][AppServicePricing]页，并查看高级定价层中 Azure 环境可用的选项。

## 虚拟网络支持 ##
可以在 Azure Resource Manager 虚拟网络**或者**经典的部署模型虚拟网络中创建 Azure 环境（[关系虚拟网络的更多信息][MoreInfoOnVirtualNetworks]）。由于 Azure 环境始终位于虚拟网络中（更准确地说，是位于虚拟网络的子网中），因此你可以利用虚拟网络的安全功能来控制入站和出站网络通信。

可以使用[网络安全组][NetworkSecurityGroups]将入站网络通信限制为 Azure 环境所在的子网。这样，你便可以在上游设备和服务（例如 Ｗeb 应用程序防火墙和网络 SaaS 提供者）后面运行应用。

应用还经常需要访问公司资源，例如内部数据库和 Web 服务。常见的做法是让这些终结点仅可用于在 Azure 虚拟网络中流动的内部网络流量。一旦 Azure 环境加入到与内部服务相同的虚拟网络，在此环境中运行的应用即可访问这些内部服务，包括可通过[站点到站点][SiteToSite]和 [Azure ExpressRoute][ExpressRoute] 连接访问的终结点。

有关 Azure 环境如何配合虚拟网络和本地网络使用的更多详细信息，请参阅以下文章：[网络体系结构][NetworkArchitectureOverview]、[控制入站流量][ControllingInboundTraffic]和[安全连接到后端][SecurelyConnectingToBackends]。

## 入门

若要开始使用 Azure 环境，请参阅[如何创建 Azure 环境][HowToCreateAnAppServiceEnvironment]

[应用程序服务环境自述文件](/documentation/articles/app-service-app-service-environments-readme/)中提供了有关 Azure 环境的所有文章和操作说明。

有关 Azure 平台的详细信息，请参阅 [Azure Web 应用][AzureAppService]。

有关 Azure 环境网络体系结构的概述，请参阅[网络体系结构概述][NetworkArchitectureOverview]一文。

有关在 Azure 环境中使用 ExpressRoute 的详细信息，请参阅以下文章：[Express Route 和 Azure 环境][NetworkConfigDetailsForExpressRoute]。

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[PremiumTier]: /pricing/details/web-site/
[MoreInfoOnVirtualNetworks]: /documentation/articles/virtual-networks-faq/
[AppServicePlan]: /documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/
[HowToCreateAnAppServiceEnvironment]: /documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[AzureAppService]: /documentation/services/web-sites/
[WebApps]: /home/features/web-site/
[MobileApps]: /documentation/articles/app-service-mobile-value-prop-preview/
[APIApps]: /documentation/articles/app-service-api-apps-why-best-platform/
[LogicApps]: /documentation/articles/app-service-logic-what-are-logic-apps/
[AzureConDeepDive]: https://azure.microsoft.com/documentation/videos/azurecon-2015-deploying-highly-scalable-and-secure-web-and-mobile-apps/
[GeodistributedAppFootprint]: /documentation/articles/app-service-app-service-environment-geo-distributed-scale/
[NetworkSecurityGroups]: /documentation/articles/virtual-networks-nsg/
[SiteToSite]: /documentation/articles/vpn-gateway-site-to-site-create/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
[HowToConfigureanAppServiceEnvironment]: /documentation/articles/app-service-web-configure-an-app-service-environment/
[ControllingInboundTraffic]: /documentation/articles/app-service-app-service-environment-control-inbound-traffic/
[SecurelyConnectingToBackends]: /documentation/articles/app-service-app-service-environment-securely-connecting-to-backend-resources/
[NetworkArchitectureOverview]: /documentation/articles/app-service-app-service-environment-network-architecture-overview/
[NetworkConfigDetailsForExpressRoute]: /documentation/articles/app-service-app-service-environment-network-configuration-expressroute/
[AppServicePricing]: /pricing/details/web-site/

<!-- IMAGES -->

 

<!---HONumber=Mooncake_0815_2016-->