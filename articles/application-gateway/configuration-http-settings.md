---
title: Azure 应用程序网关 HTTP 设置配置
description: 本文介绍如何配置 Azure 应用程序网关 HTTP 设置。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 09/09/2020
ms.author: surmb
ms.openlocfilehash: c0c939a6a8323dfdfafddb46ccdb7d7ef3dd2f2c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89652776"
---
# <a name="application-gateway-http-settings-configuration"></a>应用程序网关 HTTP 设置配置

应用程序网关使用此处指定的配置将流量路由到后端服务器。 创建 HTTP 设置后，必须将其关联到一个或多个请求路由规则。

## <a name="cookie-based-affinity"></a>基于 Cookie 的相关性

Azure 应用程序网关使用网关托管 Cookie 来维护用户会话。 当用户将第一个请求发送到应用程序网关时，它会在响应中使用包含会话详细信息的哈希值来设置关联 Cookie，将具有关联 Cookie 的后续请求路由到同一后端服务器，以便保持粘性。 

当要在同一台服务器上保存用户会话时，以及在服务器上以本地方式为用户会话保存会话状态时，可以使用此功能。 如果应用程序无法处理基于 Cookie 的相关性，则你无法使用此功能。 若要使用此功能，请确保客户端支持 Cookie。

[Chromium 浏览器](https://www.chromium.org/Home) [v80 更新](https://chromiumdash.appspot.com/schedule)提出了一个要求：必须将不包含 [SameSite](https://tools.ietf.org/id/draft-ietf-httpbis-rfc6265bis-03.html#rfc.section.5.3.7) 属性的 HTTP Cookie 视为 SameSite=Lax。 对于 CORS（跨源资源共享）请求，如果必须在第三方上下文中发送 Cookie，它必须使用 *SameSite=None; Secure* 属性，并且只应通过 HTTPS 发送它。 否则，在仅限 HTTP 的方案中，浏览器不会在第三方上下文中发送 Cookie。 Chrome 的此更新的目标是增强安全性，避免跨站点请求伪造 (CSRF) 攻击。 

为了支持此更改，从 2020 年 2 月 17 日开始，除了现有的 *ApplicationGatewayAffinity* Cookie 外，应用程序网关（所有 SKU 类型）还会注入另一个名为 ApplicationGatewayAffinityCORS 的 Cookie。 *ApplicationGatewayAffinityCORS* Cookie 又添加了两个属性 ( *"SameSite=None; Secure"* )，这样即使对于跨域请求也可以保持粘性会话。

请注意，默认关联 Cookie 名称是 *ApplicationGatewayAffinity*，可以对其进行更改。 如果使用自定义相关性 Cookie 名称，则会添加一个以 CORS 为后缀的附加 Cookie。 例如，*CustomCookieNameCORS*。

> [!NOTE]
> 如果设置了属性 *SameSite = None*，则 Cookie 还必须包含 *Secure* 标志，并且必须通过 HTTPS 发送。  如果需要基于 CORS 的会话相关性，则必须将工作负载迁移到 HTTPS。 请参阅此处的应用程序网关的 TLS 卸载和端到端 TLS 文档– [概述](ssl-overview.md)： [使用 AZURE 门户配置具有 TLS 终止的应用程序网关](create-ssl-portal.md)， [并通过门户使用应用程序网关配置端到端 tls](end-to-end-ssl-portal.md)。

## <a name="connection-draining"></a>连接清空

连接清空可帮助你在计划内服务更新期间正常删除后端池成员。 可以通过在 HTTP 设置上启用连接排出来将此设置应用于后端池的所有成员。 它确保后端池的所有注销实例继续维护现有连接，并在可配置的超时时间内处理正在进行的请求，并且不会接收任何新请求或连接。 此情况的唯一例外是由于网关托管会话相关性而绑定到注销实例的请求，这些请求将继续被转发到注销实例。 连接清空将应用到已从后端池中显式删除的后端实例。

## <a name="protocol"></a>协议

应用程序网关支持使用 HTTP 和 HTTPS 将请求路由到后端服务器。 如果选择了 HTTP 协议，则流量将以未加密的形式传送到后端服务器。 如果不能接受未加密的通信，请选择 HTTPS。

在侦听器中结合 HTTPS 使用此设置将有助于实现[端到端的 TLS](ssl-overview.md)。 这样，就可以安全地将敏感数据以加密的形式传输到后端。 后端池中每个已启用端到端 TLS 的后端服务器都必须配置证书，以便能够进行安全的通信。

## <a name="port"></a>端口

此设置指定后端服务器要在哪个端口上侦听来自应用程序网关的流量。 可以配置 1 到 65535 的端口号。

## <a name="request-timeout"></a>请求超时

此设置表示应用程序网关在接收后端服务器的响应时会等待多少秒。

## <a name="override-back-end-path"></a>替代后端路径

使用此设置可以配置可选的自定义转发路径，以便在将请求转发到后端时使用。 与“替代后端路径”字段中的自定义路径匹配的任意传入路径部分将复制到转发的路径。 下表描述了此功能的工作原理：

- 将 HTTP 设置附加到基本请求路由规则时：

  | 原始请求  | 替代后端路径 | 转发到后端的请求 |
  | ----------------- | --------------------- | ---------------------------- |
  | /home/            | /override/            | /override/home/              |
  | /home/secondhome/ | /override/            | /override/home/secondhome/   |

- 将 HTTP 设置附加到基于路径的请求路由规则时：

  | 原始请求           | 路径规则       | 替代后端路径 | 转发到后端的请求 |
  | -------------------------- | --------------- | --------------------- | ---------------------------- |
  | /pathrule/home/            | /pathrule*      | /override/            | /override/home/              |
  | /pathrule/home/secondhome/ | /pathrule*      | /override/            | /override/home/secondhome/   |
  | /home/                     | /pathrule*      | /override/            | /override/home/              |
  | /home/secondhome/          | /pathrule*      | /override/            | /override/home/secondhome/   |
  | /pathrule/home/            | /pathrule/home* | /override/            | /override/                   |
  | /pathrule/home/secondhome/ | /pathrule/home* | /override/            | /override/secondhome/        |
  | /pathrule/                 | /pathrule/      | /override/            | /override/                   |

## <a name="use-for-app-service"></a>用于应用服务

这是一个仅限 UI 的快捷方式，用于选择 Azure 应用服务后端的两个所需设置。 它会启用“从后端地址中选取主机名”，并创建新的自定义探测（如果你还没有该探测）。 （有关详细信息，请参阅本文的[从后端地址中选取主机名](#pick-host-name-from-back-end-address)设置部分。）将创建新的探测，并从后端成员的地址中选取探测标头。

## <a name="use-custom-probe"></a>使用自定义探测

此设置用于将[自定义探测](application-gateway-probe-overview.md#custom-health-probe)与某个 HTTP 设置相关联。 只能将一个自定义探测关联到某个 HTTP 设置。 如果未显式关联自定义探测，则会使用[默认探测](application-gateway-probe-overview.md#default-health-probe-settings)来监视后端的运行状况。 我们建议创建自定义探测，以便更好地控制后端的运行状况监视。

> [!NOTE]
> 只有在将相应的 HTTP 设置显式关联到某个侦听器之后，自定义探测才会监视后端池的运行状况。

## <a name="pick-host-name-from-back-end-address"></a>从后端地址中选取主机名

此功能将请求中的 *host* 标头动态设置为后端池的主机名。 主机名使用 IP 地址或 FQDN。

如果后端的域名不同于应用程序网关的 DNS 名称，并且后端必须使用特定的 host 标头才能解析为正确的终结点，则此功能会很有帮助。

例如，使用多租户服务作为后端时。 应用服务是使用共享空间和单个 IP 地址的多租户服务。 因此，只能通过自定义域设置中配置的主机名访问应用服务。

默认情况下，自定义域名为 *example.azurewebsites.net*。 若要通过未显式注册到应用服务中的主机名或者通过应用程序网关的 FQDN 使用应用程序网关访问应用服务，请将原始请求中的主机名替代为应用服务的主机名。 为此，请启用“从后端地址中选取主机名”设置。

对于其现有自定义 DNS 名称已映射到应用服务的自定义域，不需要启用此设置。

> [!NOTE]
> 应用服务环境不需要此设置，因为它属于专用部署。

## <a name="host-name-override"></a>主机名替代

此功能可将应用程序网关上的传入请求中的 *host* 标头替换为指定的主机名。

例如，如果将 *www.contoso.com* 指定为“主机名”设置，则将请求转发到后端服务器时，原始请求 *`https://appgw.eastus.cloudapp.azure.com/path1` 会更改为 *`https://www.contoso.com/path1`。

## <a name="next-steps"></a>后续步骤

- [了解后端池](configuration-overview.md#back-end-pool)