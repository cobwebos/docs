---
title: 应用程序网关组件
description: 本文提供有关应用程序网关中各个组件的信息
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 02/20/2019
ms.author: absha
ms.openlocfilehash: 49b6b49d908a7426e7cfd1bae5260ff399d9953b
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/20/2019
ms.locfileid: "67273229"
---
# <a name="application-gateway-components"></a>应用程序网关组件

 应用程序网关充当客户端的单一联络点。 它在包括 Azure VM、虚拟机规模集、Azure 应用服务和本地/外部服务器的多个后端池之间分配传入的应用程序流量。 为了分配流量，应用程序网关将使用本文所述的多个组件。

![应用程序网关中使用的组件](./media/application-gateway-components/application-gateway-components.png)

## <a name="frontend-ip-addresses"></a>前端 IP 地址

前端 IP 地址是与应用程序网关关联的 IP 地址。 可将应用程序网关配置为使用公共 IP 地址和/或专用 IP 地址。 应用程序网关支持一个公共 IP 地址或一个专用 IP 地址。 虚拟网络和公共 IP 地址必须位于应用程序网关所在的同一位置。 创建前端 IP 地址后，该地址将关联到某个侦听器。

### <a name="static-versus-dynamic-public-ip-address"></a>静态与动态公共 IP 地址

可以配置 Azure 应用程序网关 V2 SKU 以支持同时静态内部 IP 地址和静态公共 IP 地址或仅静态公共 IP 地址。 它不能配置为支持仅静态内部 IP 地址。

V1 SKU 可以配置为支持静态内部 IP 地址和动态公共 IP 地址，只有静态内部 IP 地址或仅动态公共 IP 地址。 应用程序网关的动态 IP 地址上正在运行的网关不会更改。 仅在停止或启动网关时，可以更改它。 它不会更改系统故障、 更新、 Azure 主机上更新等。 

与应用程序网关关联的 DNS 名称在网关的整个生命周期内不会变化。 出于此原因，应使用 CNAME 别名并使其指向应用程序网关的 DNS 地址。

## <a name="listeners"></a>侦听器

侦听器是检查传入连接请求的逻辑实体。 如果与请求关联的协议、端口、主机和 IP 地址匹配与侦听器配置关联的相同元素，侦听器将接受该请求。

在使用应用程序网关之前，必须至少添加一个侦听器。 可将多个侦听器附加到一个应用程序网关，这些侦听器可用于同一个协议。

侦听器检测到来自客户端的传入请求后，应用程序网关会将这些请求路由到后端池中的成员。 应用程序网关使用你为收到传入请求的侦听器定义的请求路由规则。

侦听器支持以下端口和协议。

### <a name="ports"></a>端口

侦听器在某个端口上侦听客户端请求。 您可以配置端口范围从 1 到 65502 v1 sku 和 1 到 65199 v2 sku。

### <a name="protocols"></a>协议

应用程序网关支持四种协议：HTTP、HTTPS、HTTP/2 和 WebSocket：

- 在侦听器配置中指定 HTTP 或 HTTPS 协议。
- 原生支持 [WebSocket 和 HTTP/2 协议](https://docs.microsoft.com/azure/application-gateway/overview#websocket-and-http2-traffic)，默认已启用 [WebSocket 支持](https://docs.microsoft.com/azure/application-gateway/application-gateway-websocket)。 用户无法通过配置设置来选择性地启用或禁用 WebSocket 支持。 对 HTTP 和 HTTPS 侦听器使用 WebSocket。
- 仅针对连接到应用程序网关侦听程序的客户端提供了 HTTP/2 协议支持。 与后端服务器池的通信是通过 HTTP/1.1 进行的。 默认情况下，HTTP/2 支持处于禁用状态。 可以选择启用该协议。

使用 HTTPS 侦听器进行 SSL 终止。 HTTPS 侦听器可将加密和解密工作卸载到应用程序网关，以避免加密和解密开销给 Web 服务器造成负担。 然后，应用可以专注于业务逻辑。

### <a name="custom-error-pages"></a>自定义错误页

应用程序网关可让你创建自定义错误页而非显示默认错误页。 你可以在自定义错误页上使用自己的品牌和布局。 当请求无法到达后端时，应用程序网关会显示自定义错误页。

有关详细信息，请参阅[应用程序网关的自定义错误页](https://docs.microsoft.com/azure/application-gateway/custom-error)。

### <a name="types-of-listeners"></a>侦听器类型

有两种类型的侦听器：

- **基本**。 此类侦听器侦听单个域站点，该站点中的单个 DNS 映射到应用程序网关的 IP 地址。 在应用程序网关后面托管单个站点时，需要使用此侦听器配置。

- **多站点**。 在同一个应用程序网关实例上配置多个 Web 应用程序时，需要使用此侦听器配置。 这样可以将最多 100 个网站添加到一个应用程序网关，为部署配置更有效的拓扑。 每个网站都可以定向到自己的后端池。 例如，有三个子域（abc.alpha.com、xyz.alpha.com 和 pqr.alpha.com）指向应用程序网关 IP 地址。 可以创建三个多站点侦听器，并为每个侦听器配置相应的端口和协议设置。

    有关详细信息，请参阅[多站点托管](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-app-overview)。

创建侦听器后，请将它关联到某个请求路由规则。 该规则确定如何将侦听器上收到的请求路由到后端。

应用程序网关按显示顺序处理侦听器。 如果基本侦听器与传入的请求相匹配，则会先处理该侦听器。 若要将流量路由到正确的后端，请将多站点侦听器配置在基本侦听器之前。

## <a name="request-routing-rules"></a>请求路由规则

请求路由规则是应用程序网关的关键组件，因为它确定如何在侦听器上路由流量。 该规则绑定侦听器、后端服务器池和后端 HTTP 设置。

当侦听器接受请求时，请求路由规则会将该请求转发到后端，或重定向到其他位置。 如果将请求转发到后端，则请求路由规则会定义要将其转发到哪个后端服务器池。 此外，请求路由规则还确定是否要重写请求中的标头。 一个侦听器可以附加到一个规则。

有两种类型的请求路由规则：

- **基本**。 使用关联的 HTTP 设置将关联的侦听器（例如 blog.contoso.com/*）上的所有请求转发到关联的后端池。

- **基于路径**。 此路由规则可让你根据请求中的 URL，将关联的侦听器上的请求路由到特定的后端池。 如果请求中的 URL 路径与基于路径的规则中的路径模式相匹配，规则将路由该请求。 路径模式仅应用到 URL 路径，而不应用到其查询参数。 如果侦听器请求中的 URL 路径与任何基于路径的规则都不匹配，则将请求路由到默认的后端池和 HTTP 设置。

有关详细信息，请参阅[基于 URL 的路由](https://docs.microsoft.com/azure/application-gateway/url-route-overview)。

### <a name="redirection-support"></a>重定向支持

请求路由规则还允许重定向应用程序网关上的流量。 这是一种通用重定向机制，因此可以针对使用规则定义的任何端口进行双向重定向。

可以选择另一个侦听器（有助于实现 HTTP 到 HTTPS 的自动重定向）或外部站点作为重定向目标。 也可以选择临时性或永久性重定向，或者将 URI 路径和查询字符串追加到重定向的 URL。

有关详细信息，请参阅[重定向应用程序网关上的流量](https://docs.microsoft.com/azure/application-gateway/redirect-overview)。

### <a name="rewrite-http-headers"></a>重写 HTTP 标头

通过使用请求路由规则，可以添加、 删除或更新 HTTP (S) 请求和响应标头以请求和响应数据包的形式移动客户端与后端池通过应用程序网关。

将标头可以设置为静态值或为其他标头和服务器变量。 这有助于与重要用例，如提取客户端 IP 地址，删除敏感信息的后端，添加更高的安全性，依次类推。

有关详细信息，请参阅[应用程序网关上重写 HTTP 标头](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers)。

## <a name="http-settings"></a>HTTP 设置

应用程序网关使用此组件中详细指定的端口号、协议和其他设置，将流量路由到后端服务器（在包含 HTTP 设置的请求路由规则中指定）。

HTTP 设置中使用的端口和协议确定应用程序网关与后端服务器之间的流量是要加密（提供端到端 SSL）还是不加密。

此组件还用于：

- 使用[基于 Cookie 的会话相关性](https://docs.microsoft.com/azure/application-gateway/overview#session-affinity)确定是否要在同一台服务器上保留用户会话。

- 使用[连接清空](https://docs.microsoft.com/azure/application-gateway/overview#connection-draining)正常删除后端池成员。

- 关联自定义探测以监视后端运行状况、设置请求超时间隔、替代请求中的主机名和路径，以及一键式指定应用服务后端的设置。

## <a name="backend-pools"></a>后端池

后端池将请求路由到为请求提供服务的后端服务器。 后端池可以包含：

- NIC
- 虚拟机规模集
- 公共 IP 地址
- 内部 IP 地址
- FQDN
- 多租户后端（例如应用服务）

应用程序网关后端池成员不会绑定到可用性集。 应用程序网关能够与其所在的虚拟网络外部的实例通信。 因此，后端池的成员可以跨群集、跨数据中心，或者位于 Azure 外部，前提是建立了 IP 连接。

如果你打算使用内部 IP 作为后端池成员，必须使用[虚拟网络对等互连](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)或 [VPN 网关](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)。 支持虚拟网络对等互连，这有助于对其他虚拟网络中的流量进行负载均衡。

此外，应用程序网关可与通过 Azure ExpressRoute 或 VPN 隧道连接的本地服务器通信（如果允许这种流量）。

可为不同类型的请求创建不同的后端池。 例如，为常规请求创建一个后端池，然后为发往应用程序微服务的请求创建另一个后端池。

## <a name="health-probes"></a>运行状况探测

默认情况下，应用程序网关会监视其后端池中所有资源的运行状况，并自动删除不正常的资源。 然后，它会监视不正常的实例，当这些实例恢复可用状态并能响应运行状况探测时，应用程序网关就会将它们添加回到正常的后端池中。

除了使用默认的运行状况探测监视以外，还可以根据应用程序的要求自定义运行状况探测。 使用自定义探测可以更精细地控制运行状况监视。 使用自定义探测时，可以配置探测间隔、要测试的 URL 和路径，以及在将后端池实例标记为不正常之前可接受的失败响应次数。 我们建议配置自定义探测来监视每个后端池的运行状况。

有关详细信息，请参阅[监视应用程序网关的运行状况](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview)。

## <a name="next-steps"></a>后续步骤

创建应用程序网关：

* [在 Azure 门户中](quick-create-portal.md)
* [使用 Azure PowerShell](quick-create-powershell.md)
* [使用 Azure CLI](quick-create-cli.md)
