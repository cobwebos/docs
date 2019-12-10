---
title: 应用程序网关组件
description: 本文提供了有关应用程序网关上各个组件的信息
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 02/20/2019
ms.author: absha
ms.openlocfilehash: 49f3759d7c5ba178cd0f1d0164a45c09df464571
ms.sourcegitcommit: b5ff5abd7a82eaf3a1df883c4247e11cdfe38c19
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/09/2019
ms.locfileid: "74942216"
---
# <a name="application-gateway-components"></a>应用程序网关组件

 应用程序网关充当客户端的单一联系点。 它将传入应用程序流量分布到多个后端池，其中包括 Azure Vm、虚拟机规模集、Azure App Service 以及本地/外部服务器。 若要分发流量，应用程序网关应使用本文中所述的多个组件。

![应用程序网关上使用的组件](./media/application-gateway-components/application-gateway-components.png)

## <a name="frontend-ip-addresses"></a>前端 IP 地址

前端 IP 地址是与应用程序网关关联的 IP 地址。 你可以将应用程序网关配置为具有公共 IP 地址和/或专用 IP 地址。 应用程序网关支持一个公共或一个专用 IP 地址。 你的虚拟网络和公共 IP 地址必须与你的应用程序网关位于同一位置。 创建后，前端 IP 地址与侦听器相关联。

### <a name="static-versus-dynamic-public-ip-address"></a>静态与动态公共 IP 地址

Azure 应用程序网关 V2 SKU 可配置为支持静态内部 IP 地址和静态公共 IP 地址，或仅支持静态公共 IP 地址。 不能将它配置为仅支持静态内部 IP 地址。

V1 SKU 可配置为支持静态或动态内部 IP 地址和动态公共 IP 地址。 应用程序网关的动态 IP 地址在运行的网关上不会更改。 它仅在停止或启动网关时才可以更改。 它不会因系统故障、更新、Azure 主机更新等而发生更改。 

与应用程序网关关联的 DNS 名称不会随网关的生命周期而更改。 因此，应使用 CNAME 别名，并将其指向应用程序网关的 DNS 地址。

## <a name="listeners"></a>侦听器

侦听器是检查传入连接请求的逻辑实体。 如果与请求关联的协议、端口、主机名和 IP 地址与侦听器配置关联的元素匹配，则侦听器会接受请求。

使用应用程序网关之前，必须至少添加一个侦听器。 可能有多个侦听器附加到应用程序网关，它们可用于同一协议。

侦听器检测到来自客户端的传入请求后，应用程序网关会将这些请求路由到规则中配置的后端池中的成员。

侦听器支持以下端口和协议。

### <a name="ports"></a>端口

侦听器用于侦听客户端请求的端口。 对于 v2 sku，你可以配置范围从1到65502的端口，为 v2 sku 配置端口1到65199。

### <a name="protocols"></a>协议

应用程序网关支持四种协议： HTTP、HTTPS、HTTP/2 和 WebSocket：
>[!NOTE]
>仅针对连接到应用程序网关侦听程序的客户端提供了 HTTP/2 协议支持。 与后端服务器池的通信始终通过 HTTP/1.1 进行。 默认情况下，HTTP/2 支持处于禁用状态。 你可以选择启用它。

- 在侦听器配置中指定 HTTP 和 HTTPS 协议。
- 支持[websocket 和 HTTP/2 协议](https://docs.microsoft.com/azure/application-gateway/overview#websocket-and-http2-traffic)，并在默认情况下启用[websocket 支持](https://docs.microsoft.com/azure/application-gateway/application-gateway-websocket)。 用户无法通过配置设置来选择性地启用或禁用 WebSocket 支持。 将 Websocket 用于 HTTP 和 HTTPS 侦听器。

使用 HTTPS 侦听器进行 SSL 终止。 HTTPS 侦听器会将加密和解密工作卸载到应用程序网关，因此，web 服务器不会承受开销。

### <a name="custom-error-pages"></a>自定义错误页

应用程序网关允许你创建自定义错误页，而不是显示默认错误页。 你可以在自定义错误页上使用自己的品牌和布局。 当请求无法访问后端时，应用程序网关会显示自定义错误页。

有关详细信息，请参阅[应用程序网关的自定义错误页](https://docs.microsoft.com/azure/application-gateway/custom-error)。

### <a name="types-of-listeners"></a>侦听器的类型

有两种类型的侦听器：

- **基本**。 这种类型的侦听器侦听单一域站点，其中包含指向应用程序网关的 IP 地址的单个 DNS 映射。 在应用程序网关后托管单一站点时，需要此侦听器配置。

- **多站点**。 当你在同一应用程序网关实例上配置多个 web 应用程序时，此侦听器配置是必需的。 它允许您通过将最多100网站添加到一个应用程序网关，为部署配置更有效的拓扑。 每个网站都可以定向到自己的后端池。 例如，三个子域、abc.contoso.com、xyz.contoso.com 和 pqr.contoso.com，指向应用程序网关的 IP 地址。 你将创建三个多站点侦听器，并为各自的端口和协议设置配置每个侦听器。

    有关详细信息，请参阅[多站点托管](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-app-overview)。

创建侦听器后，将其与请求路由规则相关联。 此规则确定如何将侦听器上收到的请求路由到后端。

应用程序网关按[显示的顺序](https://docs.microsoft.com/en-us/azure/application-gateway/configuration-overview#order-of-processing-listeners)处理侦听器。

## <a name="request-routing-rules"></a>请求路由规则

请求路由规则是应用程序网关的关键组件，因为它确定如何在侦听器上路由流量。 规则将绑定侦听器、后端服务器池和后端 HTTP 设置。

当侦听器接受请求时，请求路由规则将请求转发到后端或将其重定向到其他位置。 如果将请求转发到后端，请求路由规则将定义要将其转发到的后端服务器池。 请求路由规则还确定是否要重写请求中的标头。 一个侦听器可以附加到一个规则。

请求路由规则有两种类型：

- **基本**。 关联侦听器上的所有请求（例如，blog.contoso.com/*）都将通过使用关联的 HTTP 设置转发到关联的后端池。

- **基于路径**。 此路由规则使你可以基于请求中的 URL 将关联侦听器上的请求路由到特定的后端池。 如果请求中 URL 的路径与基于路径的规则中的路径模式匹配，则请求的规则路由。 它仅将路径模式应用于 URL 路径，而不是其查询参数。 如果侦听器请求上的 URL 路径与任何基于路径的规则都不匹配，则会将请求路由到默认的后端池和 HTTP 设置。

有关详细信息，请参阅[基于 URL 的路由](https://docs.microsoft.com/azure/application-gateway/url-route-overview)。

### <a name="redirection-support"></a>重定向支持

请求路由规则还允许你在应用程序网关上重定向流量。 这是一种通用的重定向机制，因此你可以使用规则重定向到你定义的任何端口。

你可以选择将重定向目标作为另一个侦听器（可帮助启用自动 HTTP 到 HTTPS 重定向）或外部站点。 还可以选择将重定向设置为临时或永久，或将 URI 路径和查询字符串附加到重定向的 URL。

有关详细信息，请参阅[在应用程序网关上重定向流量](https://docs.microsoft.com/azure/application-gateway/redirect-overview)。

### <a name="rewrite-http-headers"></a>重写 HTTP 标头

通过使用请求路由规则，你可以添加、删除或更新 HTTP （S）请求和响应标头，因为请求和响应数据包通过应用程序网关在客户端和后端池之间移动。

标头可以设置为静态值，也可以设置为其他标头和服务器变量。 这有助于处理重要的用例，如提取客户端 IP 地址、删除有关后端的敏感信息、添加更多安全性等。

有关详细信息，请参阅[在应用程序网关上重写 HTTP 标头](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers)。

## <a name="http-settings"></a>HTTP 设置

应用程序网关通过使用此组件中详细说明的端口号、协议和其他设置，将流量路由到后端服务器（在包含 HTTP 设置的请求路由规则中指定）。

HTTP 设置中使用的端口和协议确定是否对应用程序网关和后端服务器之间的流量进行加密（提供端到端 SSL）或未加密。

此组件还用于：

- 使用[基于 cookie 的会话相关性](https://docs.microsoft.com/azure/application-gateway/overview#session-affinity)确定是否要在同一台服务器上保存用户会话。

- 使用[连接排出](https://docs.microsoft.com/azure/application-gateway/overview#connection-draining)正常删除后端池成员。

- 关联自定义探测以监视后端运行状况，设置请求超时间隔，替代请求中的主机名和路径，并提供一次单击缓动来指定应用服务后端的设置。

## <a name="backend-pools"></a>后端池

后端池将请求路由到为请求提供服务的后端服务器。 后端池可以包含：

- NIC
- 虚拟机规模集
- 公共 IP 地址
- 内部 IP 地址
- FQDN
- 多租户后端（如应用服务）

应用程序网关后端池成员不会绑定到可用性集。 应用程序网关可与其所在的虚拟网络外部的实例通信。 因此，只要有 IP 连接，后端池的成员可以跨多个数据中心或 Azure 外部的群集。

如果使用内部 Ip 作为后端池成员，则必须使用[虚拟网络对等互连](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)或[VPN 网关](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)。 支持虚拟网络对等互连，并对其他虚拟网络中的负载均衡流量有利。

当应用程序网关通过 Azure ExpressRoute 或 VPN 隧道连接时，还可以与本地服务器通信，前提是允许流量。

可以为不同类型的请求创建不同的后端池。 例如，为常规请求创建一个后端池，然后为应用程序的微服务创建一个后端池。

## <a name="health-probes"></a>运行状况探测

默认情况下，应用程序网关监视其后端池中所有资源的运行状况，并自动删除不正常的资源。 然后，它会监视不正常的实例，并在其可用时将其添加回正常的后端池，并响应运行状况探测。

除了使用默认的运行状况探测监视以外，还可以根据应用程序的要求自定义运行状况探测。 自定义探测器允许更精细地控制运行状况监视。 使用自定义探测时，可以配置探测间隔、要测试的 URL 和路径，以及在将后端池实例标记为不正常之前可接受的失败响应数。 建议你将自定义探测配置为监视每个后端池的运行状况。

有关详细信息，请参阅[监视应用程序网关的运行状况](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview)。

## <a name="next-steps"></a>后续步骤

创建应用程序网关：

* [在 Azure 门户中](quick-create-portal.md)
* [使用 Azure PowerShell](quick-create-powershell.md)
* [使用 Azure CLI](quick-create-cli.md)
