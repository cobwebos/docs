---
title: "Azure 应用服务环境自述文件"
description: "列出了介绍 Azure 应用服务环境的文档"
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 77452413-5193-4762-8b3d-5fa8e4edf1ca
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.translationtype: Human Translation
ms.sourcegitcommit: cb4d075d283059d613e3e9d8f0a6f9448310d96b
ms.openlocfilehash: 8b401036d4fa8e6c4e6430433b1641f5cb4ae010
ms.contentlocale: zh-cn
ms.lasthandoff: 06/26/2017

---

# <a name="app-service-environment-documentation"></a>应用服务环境文档
应用服务环境是一项 Azure App Service 功能，可提供完全隔离和专用的环境，以便高度安全地运行 Azure App Service 应用。 此功能可托管 [Web 应用][webapps]、[移动应用][mobileapps]、[API 应用][APIApps]和 [Functions][Functions]。

应用服务环境适用于有以下要求的应用程序工作负荷：

* 极高的缩放性
* 隔离和安全的网络访问

客户可以在单个 Azure 区域和多个 Azure 区域创建多个应用服务环境。 因此，应用服务环境适用于水平缩放无状态应用层，支持高 RPS 工作负荷。

应用服务环境可在隔离后只运行单个客户的应用程序，并可始终部署到虚拟网络中。 客户可以使用[网络安全组][NSGs]对入站和出站应用程序网络流量进行精细控制。 应用程序也可以通过虚拟网络建立到本地公司资源的高速安全连接。

应用经常需要访问公司资源，例如内部数据库和 Web 服务。 在应用服务环境中运行的应用可以通过[站点到站点][SiteToSite] VPN 和 [Azure ExpressRoute][ExpressRoute] 连接访问那些允许访问的资源。

* [什么是应用服务环境？][Intro]
* [创建应用服务环境][MakeExternalASE]
* [创建内部负载均衡器应用服务环境][MakeILBASE]
* [使用应用服务环境][UsingASE]
* [网络注意事项和应用服务环境][ASENetwork]
* [通过模板创建应用服务环境][MakeASEfromTemplate]


## <a name="videos"></a>视频
借助 Azure App Service 掌握适用于企业的现代 PaaS
>[!VIDEO https://channel9.msdn.com/Events/Ignite/2016/BRK3205/player]

部署可高度缩放的安全应用
>[!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON325/player]

在 Azure App Service 上运行 Enterprise Web 和移动应用
>[!VIDEO https://channel9.msdn.com/Events/Ignite/2015/BRK3715/player]

## <a name="asev1"></a>ASEv1 ##
App Service 环境有两个版本：ASEv1 和 ASEv2。 有关 ASEv1 的信息，请参阅 [App Service 环境 v1 文档][ASEv1README]。


<!--Links-->
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[ASEReadme]: ./readme.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/virtual-networks-nsg.md
[ConfigureASEv1]: ../../app-service-web/app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: ../../app-service-web/app-service-app-service-environment-intro.md
[webapps]: ../../app-service-web/app-service-web-overview.md
[mobileapps]: ../../app-service-mobile/app-service-mobile-value-prop.md
[APIapps]: ../../app-service-api/app-service-api-apps-why-best-platform.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: http://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md
[ConfigureSSL]: ../../app-service-web/web-sites-purchase-ssl-web-site.md
[Kudu]: http://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[AppDeploy]: ../../app-service-web/web-sites-deploy.md
[ASEWAF]: ../../app-service-web/app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
[PremiumTier]: http://azure.microsoft.com/pricing/details/app-service/
[ASEv1README]: ../app-service-app-service-environments-readme.md
[SiteToSite]: ../../vpn-gateway/vpn-gateway-site-to-site-create.md
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/

