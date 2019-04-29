---
title: 应用程序网关组件
description: 本文提供了有关在应用程序网关的各个组件的信息
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 02/20/2019
ms.author: absha
ms.openlocfilehash: f5dfa34760bcef23bf54d65b35e3ad8f48cc2ee5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60831822"
---
# <a name="application-gateway-components"></a>应用程序网关组件

 应用程序网关充当客户端的单一联络点。 它将应用程序的传入流量分布跨多个后端池，其中包括 Azure Vm、 虚拟机规模集、 Azure 应用服务和上-内部/外部的服务器。 若要分发流量，应用程序网关，请使用本文中所述的多个组件。

![在应用程序网关使用的组件](./media/application-gateway-components/application-gateway-components.png)

## <a name="frontend-ip-addresses"></a>前端 IP 地址

前端 IP 地址是与应用程序网关相关联的 IP 地址。 你可以配置应用程序网关具有一个公共 IP 地址、 专用 IP 地址，或两者。 应用程序网关支持一个公共或专用 IP 地址。 虚拟网络和公共 IP 地址必须位于应用程序网关所在的同一位置。 创建前端 IP 地址后，与侦听器相关联。

### <a name="static-versus-dynamic-public-ip-address"></a>静态与动态公共 IP 地址

Azure 应用程序网关 v2 SKU 支持这两个静态内部和静态公共 IP 地址，但 v1 SKU 支持仅静态内部 IP 地址。 如果停止和启动应用程序网关，可以更改虚拟 IP (VIP) 地址。

与应用程序网关关联的 DNS 名称不会更改对网关的生命周期。 因此，应使用 CNAME 别名并使其指向应用程序网关的 DNS 地址。

## <a name="listeners"></a>侦听器

侦听器是检查传入的连接请求的逻辑实体。 如果协议、 端口、 主机和与请求关联的 IP 地址匹配的侦听器配置与关联的相同元素，侦听器将接受请求。

在使用应用程序网关之前，必须添加至少一个侦听器。 可以有多个侦听器连接到应用程序网关，并且它们可以用于相同的协议。

侦听器检测到来自客户端的传入请求后，应用程序网关会将这些请求路由到后端池中的成员。 应用程序网关使用定义为侦听器接收传入请求的请求路由规则。

侦听器支持的以下端口和协议。

### <a name="ports"></a>端口

端口是其中一个侦听器侦听的客户端请求。 您可以配置端口范围从 1 到 65502 v1 sku 和 1 到 65199 v2 sku。

### <a name="protocols"></a>协议

应用程序网关支持四个协议：HTTP、 HTTPS、 HTTP/2 和 WebSocket:

- 之间的 HTTP 和 HTTPS 协议中指定的侦听器配置。
- 为支持[Websocket 和 HTTP/2 协议](https://docs.microsoft.com/azure/application-gateway/overview#websocket-and-http2-traffic)本机，提供并[WebSocket 支持](https://docs.microsoft.com/azure/application-gateway/application-gateway-websocket)默认情况下启用。 用户无法通过配置设置来选择性地启用或禁用 WebSocket 支持。 与 HTTP 和 HTTPS 侦听器使用 Websocket。
- 仅针对连接到应用程序网关侦听程序的客户端提供了 HTTP/2 协议支持。 与后端服务器池的通信是通过 HTTP/1.1 进行的。 默认情况下，HTTP/2 支持处于禁用状态。 可以选择启用该协议。

使用 SSL 终端的 HTTPS 侦听器。 HTTPS 侦听器将卸载到应用程序网关，加密和解密工作，以便你的 web 服务器不负责的开销。 您的应用程序然后可以将重心放在业务逻辑。

### <a name="custom-error-pages"></a>自定义错误页

应用程序网关，可以创建自定义错误页，而不是显示默认错误页。 你可以在自定义错误页上使用自己的品牌和布局。 当请求无法访问后端时，应用程序网关将显示自定义错误页。

有关详细信息，请参阅[应用程序网关的自定义错误页](https://docs.microsoft.com/azure/application-gateway/custom-error)。

### <a name="types-of-listeners"></a>侦听器类型

有两种类型的侦听器：

- **基本**。 这种类型的侦听器侦听单个域站点时，有一个 DNS 映射到应用程序网关的 IP 地址。 托管应用程序网关后面的单个站点时需要此侦听器配置。

- **多站点**。 在同一个应用程序网关实例上配置多个 Web 应用程序时，需要使用此侦听器配置。 它允许您通过将最多 100 个网站添加到一个应用程序网关配置为部署更有效的拓扑。 每个网站都可以定向到自己的后端池。 例如，三个子域、 abc.contoso.com、 xyz.contoso.com 和 pqr.contoso.com，点到应用程序网关的 IP 地址。 您可以创建三个多站点侦听器，并配置相应的端口和协议设置的每个侦听器。

    有关详细信息，请参阅[多站点托管](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-app-overview)。

创建侦听器后，您将其与关联的请求路由规则。 此规则确定如何将接收到对侦听程序的请求路由到后端。

应用程序网关处理侦听器中所示的顺序。 如果基本侦听器与传入请求匹配，它是第一次处理。 若要将流量路由到正确的后端，配置多站点侦听器之前基本侦听器。

## <a name="request-routing-rules"></a>请求路由规则

请求路由规则是应用程序网关的关键组件，因为它可确定如何路由流量的侦听器。 该规则绑定侦听器、后端服务器池和后端 HTTP 设置。

当侦听器接受的请求时，请求路由规则将请求转发到后端，或将其他位置重定向。 如果该请求被转发到后端，请求路由规则定义要转发到的后端服务器池。 此外，请求路由规则还确定是否要重写中请求的标头。 一个侦听器可以附加到一条规则。

有两种类型的请求路由规则：

- **基本**。 关联的侦听器 (例如，blog.contoso.com/*) 上的所有请求使用关联的 HTTP 设置都转发到关联的后端池。

- **基于路径的**。 此路由规则可将请求路由到特定的后端池，根据在请求中的 URL 关联的侦听器。 如果在请求中 URL 的路径与匹配的路径模式中基于路径的规则，规则将路由该请求。 仅对 URL 路径上，而不是其查询参数，它应用的路径模式。 如果对侦听程序请求的 URL 路径不匹配任何基于路径的规则，它将请求路由到的默认后端池和 HTTP 设置。

有关详细信息，请参阅[基于 URL 的路由](https://docs.microsoft.com/azure/application-gateway/url-route-overview)。

### <a name="redirection-support"></a>重定向支持

请求路由规则还允许重定向应用程序网关上的流量。 这是一种泛型重定向机制，因此可重定向到和来自任何端口使用定义的规则。

可以选择要为另一个侦听器 （这可以帮助您实现自动 HTTP 到 HTTPS 重定向） 或外部站点的重定向目标。 此外可以选择让重定向是临时或永久性的或将 URI 路径和查询字符串追加到重定向的 URL。

有关详细信息，请参阅[应用程序网关上的流量重定向](https://docs.microsoft.com/azure/application-gateway/redirect-overview)。

### <a name="rewrite-http-headers"></a>重写 HTTP 标头

通过使用请求路由规则，可以添加、 删除或更新 HTTP (S) 请求和响应标头以请求和响应数据包的形式移动客户端与后端池通过应用程序网关。

将标头可以设置为静态值或为其他标头和服务器变量。 这有助于与重要用例，如提取客户端 IP 地址，删除敏感信息的后端，添加更高的安全性，依次类推。

有关详细信息，请参阅[应用程序网关上重写 HTTP 标头](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers)。

## <a name="http-settings"></a>HTTP 设置

应用程序网关使用的端口号、 协议和详细的组件中的其他设置将路由到后端服务器 （包括 HTTP 设置的请求路由规则中指定） 的流量。

端口和 HTTP 设置中使用的协议确定是否在应用程序网关和后端服务器之间的流量进行加密 （提供端到端 SSL） 或以未加密状态。

此组件还用于：

- 确定是否要保留在同一台服务器上使用的用户会话[基于 cookie 的会话相关性](https://docs.microsoft.com/azure/application-gateway/overview#session-affinity)。

- 使用正常删除后端池成员[连接排出](https://docs.microsoft.com/azure/application-gateway/overview#connection-draining)。

- 将一个自定义探测来监视后端运行状况、 设置请求超时间隔、 重写主机名和在请求中的路径和提供一键式轻松指定应用服务后端的设置相关联。

## <a name="backend-pools"></a>后端池

后端池将请求路由到后端服务器，为请求提供服务。 后端池可以包含：

- NIC
- 虚拟机规模集
- 公共 IP 地址
- 内部 IP 地址
- FQDN
- 多租户后端 （如应用服务）

应用程序网关后端池成员不被绑定到一个可用性集。 应用程序网关可以与在虚拟网络外部的实例通信。 结果是后, 端池的成员可以跨群集，跨数据中心或 Azure 外部，只要 IP 连接。

如果你使用内部 Ip 作为后端池成员，则必须使用[虚拟网络对等互连](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)或[VPN 网关](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)。 虚拟网络对等互连是受支持，并且非常有用的负载平衡的其他虚拟网络中通信。

应用程序网关还可以与通信的本地服务器时它们如果允许流量通过 Azure ExpressRoute 或 VPN 隧道连接。

可为不同类型的请求创建不同的后端池。 例如，创建一个后端池的常规请求和对你的应用程序的微服务发出的请求的另一个后端池。

## <a name="health-probes"></a>运行状况探测

默认情况下，应用程序网关监视后端池中的所有资源的运行状况，并会自动删除不正常的。 然后，再监视不正常的实例，并将它们添加回正常的后端池时可用，并且响应运行状况探测。

除了使用默认的运行状况探测监视以外，还可以根据应用程序的要求自定义运行状况探测。 自定义探测允许更精细地控制运行状况监视。 使用自定义探测时，可以配置探测间隔、 URL 和路径，若要测试，以及可接受的后端池实例标记为不正常之前的失败的响应次数。 我们建议配置自定义探测来监视每个后端池的运行状况。

有关详细信息，请参阅[监视应用程序网关的运行状况](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview)。

## <a name="next-steps"></a>后续步骤

创建应用程序网关：

* [在 Azure 门户中](quick-create-portal.md)
* [使用 Azure PowerShell](quick-create-powershell.md)
* [使用 Azure CLI](quick-create-cli.md)
