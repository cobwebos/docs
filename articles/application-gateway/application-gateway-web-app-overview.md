---
title: 多租户后端，如 Azure 应用服务中，通过 Azure 应用程序网关的概述
description: 此页概述了应用程序网关对多租户后端的支持。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 03/18/2019
ms.author: victorh
ms.openlocfilehash: 8434340bb7ed95cc36115c05048b2b67682b5796
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2019
ms.locfileid: "58224492"
---
# <a name="application-gateway-support-for-multi-tenant-back-ends-such-as-app-service"></a>例如，应用服务应用程序网关支持对多租户后结束

在 web 服务器中的多租户体系结构设计，在同一 web 服务器实例上运行的多个网站。 主机名用于区分不同的应用程序托管。 默认情况下，应用程序网关不更改从客户端传入的 HTTP 主机标头，而是将该标头原封不动地发送到后端。 此适用于后端池成员，例如 Nic，虚拟机规模集，公共 IP 地址、 内部 IP 地址和 FQDN 为这些不依赖特定的主机标头或 SNI 扩展，以解析为正确的终结点。 但是，有很多的服务等 Azure 应用服务 web 应用和 Azure API 管理是在本质上的多租户，依赖于特定的主机标头或 SNI 扩展，以解析为正确的终结点。 通常情况下，应用程序，又是与应用程序网关相关联的 DNS 名称，DNS 名称与后端服务的域名不同。 因此，应用程序网关接收到的原始请求中的主机标头不是与后端服务的主机名相同。 正因为如此，除非在请求中向后端应用程序网关的主机标头更改为后端服务的主机名多租户后端不能解析为正确的终结点的请求。 

应用程序网关提供了一项功能，允许用户重写中根据后端的主机名的请求的 HTTP 主机标头。 此功能，例如 Azure 应用服务 web 应用和 API 管理的多租户后端的支持。 此功能仅供的 v1 和 v2 标准和 WAF Sku。 

![主机重写](./media/application-gateway-web-app-overview/host-override.png)

> [!NOTE]
> 由于 ASE 是不同于 Azure 应用服务是多租户资源的专用的资源，这是不适用于 Azure 应用服务环境 (ASE)。

## <a name="override-host-header-in-the-request"></a>重写在请求中的主机标头

指定主机替代这一功能中定义[HTTP 设置](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http-settings)，可以应用于任何后端池在规则创建过程。 支持重写主机标头和 SNI 扩展为多租户后端的以下两种方法：

- 主机名设置为固定值显式输入 HTTP 设置中的功能。 此功能可确保将主机标头中重写到在其中应用特定的 HTTP 设置的后端池的所有流量为此值。 使用端到端 SSL 时，会在 SNI 扩展中使用该替代的主机名。 有了此功能，后端池场收到的主机标头就可以不同于传入的客户主机标头。

- 从后端池成员的 IP 或 FQDN 派生主机名的功能。 HTTP 设置还提供一个选项来动态地从后端池成员的 FQDN 选取主机名，如果配置了选项，从单个后端池成员派生主机名。 使用端到端 SSL 时，该主机名派生自 FQDN，用在 SNI 扩展中。 有了此功能，后端池就可以有两个或两个以上的多租户 PaaS 服务（例如 Azure Web 应用），而针对每个成员的请求的主机标头就可以包含从该成员的 FQDN 派生的主机名。 为了实现这种情况下，我们使用名为 HTTP 设置中的交换机[选取主机名从后端地址](https://docs.microsoft.com/azure/application-gateway/configuration-overview#pick-host-name-from-backend-address)会动态将覆盖中对后端池中所述的原始请求的主机标头。  例如，如果后端池 FQDN 包含"contoso11.azurewebsites.net"和"contoso22.azurewebsites.net"，为 contoso.com 原始请求的主机标头将被重写到 contoso11.azurewebsites.net 或 contoso22.azurewebsites.net当请求是发送到相应的后端服务器。 

  ![Web 应用方案](./media/application-gateway-web-app-overview/scenario.png)

有了此功能，客户就可以在 HTTP 设置中指定选项，并根据相应的配置来自定义探测。 然后，可以通过规则将此设置绑定到侦听器和后端池。

## <a name="special-considerations"></a>特殊注意事项

### <a name="ssl-termination-and-end-to-end-ssl-with-multi-tenant-services"></a>SSL 终止和与多租户服务的端到端 SSL

多租户服务支持 SSL 终止和端到端 SSL 加密。 在应用程序网关上的 SSL 终端，仍是需要添加到应用程序网关侦听器 SSL 证书。 但是，在端到端 SSL，受信任的 Azure 服务等 Azure 应用服务 web 应用不需要列入允许列表中的应用程序网关后端。 因此，没有无需添加任何身份验证证书。 

![端到端 SSL](./media/application-gateway-web-app-overview/end-to-end-ssl.png)

请注意，在上面的图像，则不需要时选择应用服务作为后端添加身份验证证书。

### <a name="health-probe"></a>运行状况探测

重写中的主机标头**HTTP 设置**仅影响请求和它的路由。 它不会影响运行状况探测行为。 若要使用端到端功能，必须修改探测和 HTTP 设置，使之反映正确的配置。 除了提供能够在探测配置中指定主机标头，自定义探测还支持从当前配置的 HTTP 设置派生主机标头的功能。 指定此配置时，可以在探测配置中使用 `PickHostNameFromBackendHttpSettings` 参数。

### <a name="redirection-to-app-services-url-scenario"></a>重定向到应用服务的 URL 的应用程序

可以有来自应用服务的响应中的主机名可能会对最终用户浏览器将定向的方案 *。 azurewebsites.net 主机名而不是应用程序网关相关联的域。 可能会发生此问题时：

- 在应用服务中配置了重定向。 只需在请求中添加一个尾随的斜杠即可配置重定向。
- Azure AD 身份验证导致重定向。

若要解决这种情况下，请参阅[对重定向到应用服务的 URL 问题进行故障排除](https://docs.microsoft.com/azure/application-gateway/troubleshoot-app-service-redirection-app-service-url)。

## <a name="next-steps"></a>后续步骤

了解如何将具有一个多租户应用，如 Azure 应用服务 web 应用的应用程序网关设置为后端池成员，通过访问[配置应用服务 web 应用与应用程序网关](https://docs.microsoft.com/azure/application-gateway/create-web-app)
