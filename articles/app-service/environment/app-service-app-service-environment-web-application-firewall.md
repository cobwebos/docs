---
title: 配置 WAF
description: 了解如何使用 Azure 应用程序网关或第三方 WAF 在应用服务环境的前面配置 Web 应用程序防火墙 (WAF)。
author: ccompy
ms.assetid: a2101291-83ba-4169-98a2-2c0ed9a65e8d
ms.topic: tutorial
ms.date: 03/03/2018
ms.author: stefsch
ms.custom: mvc, seodec18
ms.openlocfilehash: 33fd0b6a3a07fa4fbc5448a97ca93c75a3e239d5
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2020
ms.locfileid: "83684223"
---
# <a name="configuring-a-web-application-firewall-waf-for-app-service-environment"></a>为应用服务环境配置 Web 应用程序防火墙 (WAF)
## <a name="overview"></a>概述

Web 应用程序防火墙 (WAF) 会检查入站 Web 流量，并阻止 SQL 注入、跨站点脚本、恶意软件上传和应用程序 DDoS 及其他攻击，有助于保护 Web 应用程序的安全。 为了进行数据丢失防护 (DLP)，该防火墙还会检查后端 Web 服务器的响应。 与隔离功能以及应用服务环境提供的附加缩放相结合，它可以提供一个理想的环境，用于托管需要承受恶意请求和大量流量的业务关键型 Web 应用程序。 Azure 通过[应用程序网关](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction)提供 WAF 功能。  若要了解如何将应用服务环境与应用程序网关集成，请阅读[将 ILB ASE 与应用程序网关集成](https://docs.microsoft.com/azure/app-service/environment/integrate-with-application-gateway)文档。

除了 Azure 应用程序网关，还有多个市场选项，例如 [Barracuda WAF for Azure](https://www.barracuda.com/programs/azure)，在 [Azure 市场](https://azuremarketplace.microsoft.com/marketplace/apps/barracudanetworks.waf?tab=PlansAndPrice)中提供。 本文档其余部分重点介绍如何将应用服务环境与 Barracuda WAF 设备集成。

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)] 

## <a name="setup"></a>设置
在本文中，我们配置受多个 Barracuda WAF 负载均衡实例保护的应用服务环境，只让来自 WAF 的流量到达该应用服务环境，而且无法从 DMZ 访问该环境。 在 Barracuda WAF 实例的前面，我们还部署了 Azure 流量管理器，用于在 Azure 数据中心和区域实现负载均衡。 高级设置示意图如下所示：

![体系结构][Architecture] 

> [!NOTE]
> 通过引入[对应用服务环境的 ILB支持](app-service-environment-with-internal-load-balancer.md)，可以将 ASE 配置为不可从 DMZ 访问，而仅可供专用网络访问。 
> 
> 

## <a name="configuring-your-app-service-environment"></a>配置应用服务环境
要配置应用服务环境，请参阅有关该主题的[文档](app-service-web-how-to-create-an-app-service-environment.md)。 创建应用服务环境后，可在此环境中创建 Web 应用、API 应用和[移动应用](../../app-service-mobile/app-service-mobile-value-prop.md)，下一部分中配置的 WAF 可保护所有这些应用。

## <a name="configuring-your-barracuda-waf-cloud-service"></a>配置 Barracuda WAF 云服务
Barracuda 提供了有关在 Azure 中的虚拟机上部署其 WAF 的 [详细文章](https://campus.barracuda.com/product/webapplicationfirewall/article/WAF/DeployWAFInAzure) 。 但是，由于我们想要冗余，但不想要造成单一故障点，因此可以在遵循这些说明时，将至少两个 WAF 实例 VM 部署到相同的云服务中。

### <a name="adding-endpoints-to-cloud-service"></a>将终结点添加云服务
云服务中有两个以上的 WAF VM 实例之后，即可使用 [Azure 门户](https://portal.azure.com/)添加应用程序使用的 HTTP 和 HTTPS 终结点，如下图所示：

![配置终结点][ConfigureEndpoint]

如果应用程序使用其他终结点，则还要确保将其添加到此列表中。 

### <a name="configuring-barracuda-waf-through-its-management-portal"></a>通过管理门户配置 Barracuda WAF
Barracuda WAF 使用 TCP 端口 8000 通过其管理门户进行配置。 如果有多个 WAF VM 实例，则需要针对每个 VM 实例重复这些步骤。 

> [!NOTE]
> 完成 WAF 配置后，从所有 WAF VM 中删除 TCP/8000 终结点，以保护 WAF 的安全。
> 
> 

请按下图所示添加管理终结点，以配置 Barracuda WAF。

![添加管理终结点][AddManagementEndpoint]

使用浏览器浏览到云服务上的管理终结点。 如果云服务名称为 test.cloudapp.net，则浏览到 `http://test.cloudapp.net:8000` 即可访问此终结点。 应会看到与下图类似的登录页，在此页上，可以使用在 WAF VM 设置阶段指定的凭据登录。

![管理登录页][ManagementLoginPage]

登录后，应会看到与下图类似的仪表板，其中显示了有关 WAF 保护的基本统计信息。

![管理仪表板][ManagementDashboard]

单击“服务”选项卡可以根据 WAF 保护的服务配置 WAF  。 有关配置 Barracuda WAF 的详细信息，请参阅[相关文档](https://techlib.barracuda.com/waf/getstarted1)。 在以下示例中，已配置处理 HTTP 和 HTTPS 流量的应用服务应用。

![管理添加服务][ManagementAddServices]

> [!NOTE]
> 根据应用程序的配置方式与应用服务环境中正在使用的功能，需要转发非 80 和 443 TCP 端口的流量（例如，如果为应用服务应用设置了 IP TLS）。 有关应用服务环境中使用的网络端口的列表，请参阅[控制入站流量文档](app-service-app-service-environment-control-inbound-traffic.md)中的“网络端口”部分。
> 
> 

## <a name="configuring-microsoft-azure-traffic-manager-optional"></a>配置 Microsoft Azure 流量管理器（可选）
如果多个区域中都有应用程序，则可以使用 [Azure 流量管理器](../../traffic-manager/traffic-manager-overview.md)对这些区域进行负载均衡。 为此，可以使用流量管理器配置文件中 WAF 的云服务名称在 [Azure 门户](https://portal.azure.com)中添加终结点，如下图所示。 

![流量管理器终结点][TrafficManagerEndpoint]

如果应用程序需要身份验证，请确保有某个资源不需要任何身份验证，使流量管理器能够 ping 出应用程序的可用性。 可以在 [Azure 门户](https://portal.azure.com)的“配置”  页上配置 URL，如下图所示：

![配置流量管理器][ConfigureTrafficManager]

要将流量管理器 ping 从 WAF 转发给应用程序，需要在 Barracuda WAF 上设置网站转换，以将流量转发给应用程序，如以下示例所示：

![网站转换][WebsiteTranslations]

## <a name="securing-traffic-to-app-service-environment-using-network-security-groups-nsg"></a>使用网络安全组 (NSG) 保护发往应用服务环境的流量
有关使用云服务的 VIP 地址只限制从 WAF 流入应用服务环境的流量的详细信息，请遵循[控制入站流量文档](app-service-app-service-environment-control-inbound-traffic.md)。 以下是针对 TCP 端口 80 运行此任务的示例 Powershell 命令。

    Get-AzureNetworkSecurityGroup -Name "RestrictWestUSAppAccess" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP Barracuda" -Type Inbound -Priority 201 -Action Allow -SourceAddressPrefix '191.0.0.1'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP

将 SourceAddressPrefix 替换为 WAF 云服务的虚拟 IP 地址 (VIP)。

> [!NOTE]
> 删除并重新创建云服务后，云服务的 VIP 会发生更改。 在这样做之后，请务必更新网络资源组中的 IP 地址。 
> 
> 

<!-- IMAGES -->
[Architecture]: ./media/app-service-app-service-environment-web-application-firewall/Architecture.png
[ConfigureEndpoint]: ./media/app-service-app-service-environment-web-application-firewall/ConfigureEndpoint.png
[AddManagementEndpoint]: ./media/app-service-app-service-environment-web-application-firewall/AddManagementEndpoint.png
[ManagementAddServices]: ./media/app-service-app-service-environment-web-application-firewall/ManagementAddServices.png
[ManagementDashboard]: ./media/app-service-app-service-environment-web-application-firewall/ManagementDashboard.png
[ManagementLoginPage]: ./media/app-service-app-service-environment-web-application-firewall/ManagementLoginPage.png
[TrafficManagerEndpoint]: ./media/app-service-app-service-environment-web-application-firewall/TrafficManagerEndpoint.png
[ConfigureTrafficManager]: ./media/app-service-app-service-environment-web-application-firewall/ConfigureTrafficManager.png
[WebsiteTranslations]: ./media/app-service-app-service-environment-web-application-firewall/WebsiteTranslations.png
