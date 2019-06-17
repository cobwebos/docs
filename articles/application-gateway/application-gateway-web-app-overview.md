---
title: 使用 Azure 应用程序网关的多租户后端（例如 Azure 应用服务）概述
description: 此页概述了应用程序网关对多租户后端的支持。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 03/18/2019
ms.author: victorh
ms.openlocfilehash: 256fb42be8fec056ed7d10cfc4197a1b5a33fac1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66807175"
---
# <a name="application-gateway-support-for-multi-tenant-back-ends-such-as-app-service"></a>应用程序网关对多租户后端（例如应用服务）的支持

在 Web 服务器的多租户体系结构设计中，多个网站在同一 Web 服务器实例上运行。 主机名用于区分托管的不同应用程序。 默认情况下，应用程序网关不更改从客户端传入的 HTTP 主机标头，而是将该标头原封不动地发送到后端。 这适用于后端池成员，例如 NIC、虚拟机规模集、公共 IP 地址、内部 IP 地址和 FQDN，因为这些资源无需依赖于特定的主机标头或 SNI 扩展即可解析为正确的终结点。 但是，有许多服务（例如 Azure 应用服务 Web 应用和 Azure API 管理）在性质上是多租户的，需要依赖于特定的主机标头或 SNI 扩展才能解析为正确的终结点。 通常，应用程序的 DNS 名称（也是与应用程序网关关联的 DNS 名称）不同于后端服务的域名。 因此，应用程序网关收到的原始请求中的主机标头不同于后端服务的主机名。 正因如此，除非从应用程序网关发往后端的请求中的主机标头已更改为后端服务的主机名，否则多租户后端无法将请求解析为正确的终结点。 

应用程序网关提供相应的功能，让用户根据后端的主机名替代请求中的 HTTP 主机标头。 此功能支持多租户后端，例如 Azure 应用服务 Web 应用和 API 管理。 此功能适用于 v1 和 v2 标准 SKU 和 WAF SKU。 

![主机替代](./media/application-gateway-web-app-overview/host-override.png)

> [!NOTE]
> 这不适用于 Azure 应用服务环境 (ASE)，因为 ASE 与 Azure 应用服务不同，前者是专用资源，而后者是多租户资源。

## <a name="override-host-header-in-the-request"></a>替代请求中的主机标头

指定主机替代的功能在 [HTTP 设置](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http-settings)中定义，可以在创建规则过程中应用到任何后端池。 多租户后端支持通过以下两种方式来替代主机标头和 SNI 扩展：

- 在 HTTP 设置中显式输入将主机名设置为固定值的功能。 此功能可确保将主机标头替代为该值，前提是在流量流向的后端池中应用了特定的 HTTP 设置。 使用端到端 SSL 时，会在 SNI 扩展中使用该替代的主机名。 有了此功能，后端池场收到的主机标头就可以不同于传入的客户主机标头。

- 从后端池成员的 IP 或 FQDN 派生主机名的功能。 HTTP 设置还提供了一个选项，用于从后端池成员的 FQDN 动态选取主机名，前提是配置了从单个后端池成员派生主机名的选项。 使用端到端 SSL 时，该主机名派生自 FQDN，用在 SNI 扩展中。 有了此功能，后端池就可以有两个或两个以上的多租户 PaaS 服务（例如 Azure Web 应用），而针对每个成员的请求的主机标头就可以包含从该成员的 FQDN 派生的主机名。 为了实现此方案，我们在 HTTP 设置中使用了名为[从后端地址中选取主机名](https://docs.microsoft.com/azure/application-gateway/configuration-overview#pick-host-name-from-back-end-address)的开关，此开关会将原始请求中的主机标头动态替代为后端池中指定的标头。  例如，如果后端池 FQDN 包含"contoso11.azurewebsites.net"和"contoso22.azurewebsites.net"，为 contoso.com 原始请求的主机标头将被重写到 contoso11.azurewebsites.net 或 contoso22.azurewebsites.net当请求是发送到相应的后端服务器。 

  ![Web 应用方案](./media/application-gateway-web-app-overview/scenario.png)

有了此功能，客户就可以在 HTTP 设置中指定选项，并根据相应的配置来自定义探测。 然后，可以通过规则将此设置绑定到侦听器和后端池。

## <a name="special-considerations"></a>特殊注意事项

### <a name="ssl-termination-and-end-to-end-ssl-with-multi-tenant-services"></a>多租户服务的 SSL 终止和端到端 SSL

多租户服务支持 SSL 终止和端到端 SSL 加密。 要在应用程序网关上实现 SSL 终止，仍然需要将 SSL 证书添加到应用程序网关侦听器。 但是，在实现端到端 SSL 时，受信任的 Azure 服务（例如 Azure 应用服务 Web 应用）不需要在应用程序网关中将后端加入白名单。 因此，无需添加任何身份验证证书。 

![端到端 SSL](./media/application-gateway-web-app-overview/end-to-end-ssl.png)

请注意，在上图中，将应用服务选作后端时，不必要添加身份验证证书。

### <a name="health-probe"></a>运行状况探测

替代 **HTTP 设置**中的主机标头只会影响请求及其路由， 而不影响运行状况探测行为。 若要使用端到端功能，必须修改探测和 HTTP 设置，使之反映正确的配置。 除了提供在探测配置中指定主机标头的功能以外，自定义探测还支持从当前配置的 HTTP 设置中派生主机标头的功能。 指定此配置时，可以在探测配置中使用 `PickHostNameFromBackendHttpSettings` 参数。

### <a name="redirection-to-app-services-url-scenario"></a>重定向到应用服务 URL 的情况

可以有来自应用服务的响应中的主机名可能会对最终用户浏览器将定向的方案 *。 azurewebsites.net 主机名而不是应用程序网关相关联的域。 在以下情况下可能会发生此问题：

- 在应用服务中配置了重定向。 只需在请求中添加一个尾随的斜杠即可配置重定向。
- Azure AD 身份验证导致重定向。

若要解决这种情况，请参阅[排查重定向到应用服务 URL 的问题](https://docs.microsoft.com/azure/application-gateway/troubleshoot-app-service-redirection-app-service-url)。

## <a name="next-steps"></a>后续步骤

访问[为应用服务 Web 应用配置应用程序网关](https://docs.microsoft.com/azure/application-gateway/create-web-app)，了解如何为用作后端池成员的多租户应用（例如 Azure 应用服务 Web 应用）设置应用程序网关
