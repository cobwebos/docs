<properties 
	pageTitle="应用服务环境 | Azure" 
	description="什么是 Azure 应用服务环境？ 应用服务环境简介。" 
	keywords="azure 应用服务环境, 虚拟网络, 安全网络"
	services="app-service" 
	documentationCenter="" 
	authors="yochay" 
	manager="wpickett" 
	editor=""/>

<tags
	ms.service="app-service"
	ms.date="07/15/2016"
	wacn.date=""/>

# 应用服务环境文档

应用服务环境是 Azure 应用服务的一项[高级][PremiumTier]服务计划选项，可提供完全隔离的专用环境来安全地大规模运行 Azure 应用服务应用，包括 [Web 应用][WebApps]、[移动应用][MobileApps]和 [API 应用][APIApps]。

应用服务环境适用于有以下要求的应用程序工作负荷：

- 极高的缩放性
- 隔离和安全的网络访问

客户可以在单个 Azure 区域和多个 Azure 区域创建多个应用服务环境。因此，应用服务环境适用于水平缩放无状态应用层，支持高 RPS 工作负荷。

应用服务环境可在隔离后只运行单个客户的应用程序，并可始终部署到虚拟网络中。客户可以通过[网络安全组][NetworkSecurityGroups]对入站和出站应用程序网络流量进行精细控制。应用程序也可以通过虚拟网络建立到本地公司资源的高速安全连接。

应用经常需要访问公司资源，例如内部数据库和 Web 服务。在应用服务环境中运行的应用可以通过[站点到站点][SiteToSite] VPN 和 [Azure ExpressRoute][ExpressRoute] 连接访问那些允许访问的资源。

* [什么是 App Service 环境？](/documentation/articles/app-service-app-service-environment-intro/)
* [创建 App Service 环境？](/documentation/articles/app-service-web-how-to-create-an-app-service-environment/)
* [在 App Service 环境中创建应用](/documentation/articles/app-service-web-how-to-create-a-web-app-in-an-ase/)
* [在应用服务环境中创建和使用内部负载均衡器](/documentation/articles/app-service-environment-with-internal-load-balancer/)
* [配置 App Service 环境](/documentation/articles/app-service-web-configure-an-app-service-environment/)
* [在 App Service 环境中缩放应用](/documentation/articles/app-service-web-scale-a-web-app-in-an-app-service-environment/)
* [网络安全和体系结构](/documentation/articles/app-service-app-service-environment-network-architecture-overview/)

## 操作说明

[AZURE.INCLUDE [app-service-blueprint-app-service-environment](../../includes/app-service-blueprint-app-service-environment.md)]


<!-- LINKS -->
[PremiumTier]: /pricing/details/app-service/
[WebApps]: /documentation/articles/app-service-web-overview/
[MobileApps]: /documentation/articles/app-service-mobile-value-prop-preview/
[APIApps]: /documentation/articles/app-service-api-apps-why-best-platform/
[NetworkSecurityGroups]: /documentation/articles/virtual-networks-nsg/
[SiteToSite]: /documentation/articles/vpn-gateway-site-to-site-create/
[ExpressRoute]: /home/features/expressroute/

<!---HONumber=Mooncake_0919_2016-->