---
title: Azure 应用程序网关配置概述
description: 本文介绍如何在 Azure 应用程序网关配置的各个组件
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 03/04/2019
ms.author: absha
ms.openlocfilehash: 515243cb043bac8e9f28a3c63808e4fbd9b8f525
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/18/2019
ms.locfileid: "57905022"
---
# <a name="application-gateway-configuration-overview"></a>应用程序网关配置概述

应用程序网关包含多个组件可用于完成不同的方案不同的方式进行配置。 本文将引导您完成每个组件的配置方式。

![application-gateway-components](./media/configuration-overview/configuration-overview1.png)

上面的示例图像显示了具有 3 个侦听器的应用程序的配置。 前两个是多站点侦听器`http://acme.com/*`和`http://fabrikam.com/*`分别。 同时侦听端口 80。 第三个侦听器是一个基本侦听器与端到端 SSL 终止。 

## <a name="prerequisites"></a>必备组件

### <a name="azure-virtual-network-and-dedicated-subnet"></a>Azure 虚拟网络和专用子网

应用程序网关是专用虚拟网络中的部署。 在虚拟网络中的专用子网是必需的应用程序网关。 此子网中，可以具有给定应用程序网关部署多个的实例。 您还可以部署其他应用程序网关的子网中，但不能部署应用程序网关子网中的任何其他资源。  

> [!NOTE]   
> 不支持在同一子网上混合使用 Standard_v2 和标准应用程序网关。

#### <a name="size-of-the-subnet"></a>子网的大小

V1 SKU，如果应用程序网关使用一个专用的 IP 地址，每个实例，并使用另一个专用 IP 地址专用前端 IP 配置。 另外，Azure 会在每个子网中保留前四个 IP 地址和最后一个 IP 地址供内部使用。 例如，如果应用程序网关设置为三个实例并且没有专用前端 IP，则需要 /29 子网大小或更大。 在这种情况下，应用程序网关使用三个 IP 地址。 如果将三个实例和一个 IP 地址用于专用前端 IP 配置，则需要 /28 子网大小或更大，因为需要四个 IP 地址。

#### <a name="network-security-groups-supported-on-the-application-gateway-subnet"></a>应用程序网关子网支持网络安全组

有以下限制应用程序网关子网支持网络安全组 (Nsg): 

- 对于应用程序网关 v1 SKU，必须为端口 65503-65534 上的传入流量设置例外，对于 v2 SKU，必须为端口 65200 - 65535 上的传入流量设置例外。 此端口范围是进行 Azure 基础结构通信所必需的。 它们受 Azure 证书的保护（处于锁定状态）。 如果没有适当的证书，外部实体（包括这些网关的客户）无法对这些终结点做出任何更改。

- 不能阻止出站 Internet 连接。

- 必须允许来自 AzureLoadBalancer 标记的流量。

##### <a name="whitelist-application-gateway-access-to-a-few-source-ips"></a>将一些源 Ip 白名单应用程序网关访问

对应用程序网关子网使用 NSG 可以完成此方案。 应按列出的优先顺序对子网采取以下限制：

1. 允许来自源 IP/IP 范围的传入流量。
2. 允许来自所有源的请求传入端口 65503-65534，进行[后端运行状况通信](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)。 此端口范围是进行 Azure 基础结构通信所必需的。 它们受 Azure 证书的保护（处于锁定状态）。 如果没有适当的证书，外部实体（包括这些网关的客户）将无法对这些终结点做出任何更改。
3. 允许 [NSG](https://docs.microsoft.com/azure/virtual-network/security-overview) 上的传入 Azure 负载均衡器探测（AzureLoadBalancer 标记）和入站虚拟网络流量（VirtualNetwork 标记）。
4. 使用“拒绝所有”规则阻止其他所有传入流量。
5. 允许所有目的地的 Internet 出站流量。

#### <a name="user-defined-routes-supported-on-the-application-gateway-subnet"></a>用户定义的路由应用程序网关子网支持

V1 SKU，如果用户定义的路由 (Udr)，只要它们不会更改的端到端请求/响应通信应用程序网关子网，支持。

例如，可以在应用程序网关子网中设置 UDR 来指向用于数据包检查的防火墙设备，但必须确保数据包在检查后可以到达其预定目的地。 如果做不到这一点，可能会导致不正确的运行状况探测或流量路由行为。 这包括已了解的路由或通过 ExpressRoute 或 VPN 网关在虚拟网络中传播的默认 0.0.0.0/0 路由。

对于 v2 不支持 SKU、 应用程序网关子网上的 Udr。 有关详细信息，请参阅[自动缩放和区域冗余应用程序网关（公共预览版）](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant#known-issues-and-limitations)。

## <a name="frontend-ip"></a>前端 IP

你可以配置应用程序网关具有一个公共 IP 地址或专用 IP 地址。 承载所需客户端通过面向 Internet 的 VIP 通过 internet 访问的后端时需要公共 IP。 公共 IP 不需要不公开到 Internet，也称为内部负载均衡器 (ILB) 终结点的内部终结点。 配置使用 ILB 的网关适用于不向 Internet 公开的内部业务线应用程序。 对于位于不向 Internet 公开的安全边界内的多层应用程序中的服务和层也很有用，但仍需要执行循环负载分散、会话粘性或安全套接字层 (SSL) 终止。

支持只有一个公共 IP 地址或一个专用 IP 地址。 创建应用程序网关时选择前端 IP。 

- 如果公共 IP，你可以选择创建新的公共 IP，或在与应用程序网关位于同一位置中使用现有的公共 IP。 如果创建新的公共 IP 地址，则以后无法更改 IP 地址选择的类型 （static 或 dynamic）。 有关详细信息，请参阅[静态与动态公共 IP](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#static-vs-dynamic-public-ip) 

- 如果专用 IP，你可以选择指定从在其中创建应用程序网关的子网的专用 IP 地址。 如果未显式指定，将子网中自动选择的任意 IP 地址。 有关详细信息，请参阅[创建内部负载均衡器 (ILB) 终结点的应用程序网关。](https://docs.microsoft.com/azure/application-gateway/application-gateway-ilb-arm)

前端 IP 将关联到*侦听器*哪种检查上前端 IP 的传入请求。

## <a name="listeners"></a>侦听器

侦听器是一种逻辑实体的传入连接请求，将检查使用端口、 协议、 主机和 IP 地址。 因此，你需要输入的端口值的侦听器配置时，协议、 主机和 IP 地址是与网关的传入请求中的对应值相同。 创建使用 Azure 门户的应用程序网关时，你还通过侦听器选择协议和端口创建默认侦听器。 此外可以选择是否想要启用对侦听程序 HTTP2 支持。 应用程序网关创建后，可以编辑此默认侦听器的设置 (*appGatewayHttpListener*/*appGatewayHttpsListener*) 和/或创建新的侦听器。

### <a name="listener-type"></a>侦听器类型

您可以选择[基本或多站点侦听器](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#types-of-listeners)时创建一个新的侦听器。 

- 托管应用程序网关后面的单个站点时，如果选择基本侦听器。 了解[如何创建基本侦听器的应用程序网关](https://docs.microsoft.com/azure/application-gateway/quick-create-portal)。

- 如果同一应用程序网关实例上配置多个 web 应用程序或同一父域的多个子域，则可选择多站点侦听器。 为多站点侦听器，此外需要输入主机名。 这是因为应用程序网关依赖于 HTTP 1.1 主机标头来托管相同的公共 IP 地址和端口上的多个网站。![1551057450710](C:/Users/absha/AppData/Roaming/Typora/typora-user-images/1551057450710.png)


> [!NOTE]
> 发生 v1 Sku 时向他们显示的顺序处理侦听器。 因此，如果基本侦听器与传入请求匹配，它会先处理该请求。 因此，应在基本侦听器以确保将流量路由到正确的后端之前配置多站点侦听器。
>
> 对于 v2 Sku，多站点侦听器在基本侦听器之前进行处理。

### <a name="frontend-ip"></a>前端 IP

选择你想要将与此侦听器相关联的前端 IP。 侦听器会侦听此 IP 上的传入请求。

### <a name="frontend-port"></a>前端端口

选择前端端口。 可以选择从现有的端口，也可以创建一个新。 可以选择任何介于[允许的端口范围](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#ports)。 这样不仅可以使用的已知端口，例如 80 和 443，但任何允许自定义端口适用于你的使用。 一个端口既可用于公共面向公众的侦听器或专用面向公众的侦听器。

### <a name="protocol"></a>协议

您需要 HTTP 和 HTTPS 协议之间进行选择。 

- 如果选择 HTTP，将流的客户端和应用程序网关之间的通信未加密。

- 如果您感兴趣，请选择 HTTPS[安全套接字层 (SSL) 终止](https://docs.microsoft.com/azure/application-gateway/overview#secure-sockets-layer-ssl-terminationl)或[端到端 SSL 加密](https://docs.microsoft.com/azure/application-gateway/ssl-overview)。 如果您选择 HTTPS，客户端和应用程序网关之间的流量会被加密，将在应用程序网关上终止 SSL 连接。  如果需要端到端 SSL 加密，将另外需要配置时选择 HTTPS 协议*后端 HTTP 设置*。 这将确保传输到后端应用程序网关中时重新加密流量。

  若要配置安全套接字层 (SSL) 终止和端到端 SSL 加密，需要证书添加到以启用要派生一个根据 SSL 协议规范的对称密钥的应用程序网关的侦听器。 然后使用对称密钥进行加密和解密发送到网关的流量。 网关证书需要采用个人信息交换 (PFX) 格式。 此文件格式适用于导出私钥，后者是应用程序网关对流量进行加解密所必需的。 

#### <a name="supported-certs"></a>受支持的证书

支持自签名的证书、 CA 证书、 通配符证书和 EV 证书。

### <a name="additional-protocol-support"></a>其他协议支持

#### <a name="http2-support"></a>HTTP2 的支持

仅针对连接到应用程序网关侦听程序的客户端提供了 HTTP/2 协议支持。 与后端服务器池的通信是通过 HTTP/1.1 进行的。 默认情况下，HTTP/2 支持处于禁用状态。 以下 Azure PowerShell 代码片段示例展示了如何启用该支持：

```azurepowershell
$gw = Get-AzureRmApplicationGateway -Name test -ResourceGroupName hm

$gw.EnableHttp2 = $true

Set-AzureRmApplicationGateway -ApplicationGateway $gw
```

#### <a name="websocket-support"></a>Websocket 支持

默认已启用 WebSocket 支持。 用户无法通过配置设置来选择性地启用或禁用 WebSocket 支持。 可对 HTTP 和 HTTPS 侦听器使用 WebSocket。 

### <a name="custom-error-page"></a>自定义错误页

可以在全局级别，以及侦听器级定义自定义错误页，但是，从 Azure 门户中创建全局级别的自定义错误页当前不支持。 可以在侦听器级配置 WAF 403 错误的自定义错误页或 502 维护页。 此外需要指定给定的错误状态代码的可公开访问的 blob URL。 有关详细信息，请参阅[创建自定义错误页](https://docs.microsoft.com/azure/application-gateway/custom-error)。

![应用程序网关错误代码](https://docs.microsoft.com/azure/application-gateway/media/custom-error/ag-error-codes.png)

若要配置的全局自定义错误页，使用[配置 Azure PowerShell](https://docs.microsoft.com/azure/application-gateway/custom-error#azure-powershell-configuration) 

### <a name="ssl-policy"></a>SSL 策略

您可以集中式 SSL 证书管理并减少从后端服务器场的加密和解密开销。 这种集中式 SSL 处理还允许指定适合你组织安全要求的中央 SSL 策略。  您可以选择默认情况下，预定义的和自定义 SSL 策略。 

可以配置 SSL 策略来控制 SSL 协议版本。 你可以配置应用程序网关拒绝 TLS1.0、 TLS1.1 和 TLS1.2。 SSL 2.0 和 3.0 默认已禁用，并且不可配置。 有关详细信息，请参阅[应用程序网关 SSL 策略概述](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-policy-overview)。

创建侦听器后，将它与某个请求路由规则相关联。该规则确定如何将侦听器上收到的请求路由到后端。

## <a name="request-routing-rule"></a>请求路由规则

在创建应用程序网关使用 Azure 门户，同时创建默认规则 (*rule1*)，这会将绑定的默认侦听器 (*appGatewayHttpListener*) 与默认后端池 (*appGatewayBackendPool*) 和默认后端 HTTP 设置 (*appGatewayBackendHttpSettings*)。 应用程序网关创建后，你可以编辑此默认规则的设置和/或创建新的规则。

### <a name="rule-type"></a>规则类型

您可以选择[基本或基于路径的规则](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#request-routing-rule)时创建新的规则。 

- 如果你想要转发关联的侦听器上的所有请求 (例如： blog.contoso.com/*) 到单个后端池，请选择基本侦听器。 
- 选择基于路径的侦听器，如果你想要将路由请求带有特定后端池的特定 URL 路径。 路径模式仅应用到 URL 的路径，而不应用到该 URL 的查询参数。


> [!NOTE]
>
> 如果 v1 Sku，基于路径的规则的 URL 路径映射中列出各路径的顺序处理的传入请求的模式匹配。 出于此原因，如果某个请求与匹配 URL 路径映射中的两个或多个路径中的模式，然后路径列出的第一次将匹配，并请求将转发到后端与该路径相关联。
>
> 对于 v2 Sku 完全匹配对路径在 URL 路径映射中的列出的顺序保留更高的优先级。 为此，因此，如果请求与两个或多个路径中的模式相匹配，则请求将转发到后端与完全与请求匹配该路径相关联。 如果传入请求中的路径不完全匹配 URL 路径映射中的任何路径，然后匹配传入请求的模式的基于路径的规则的 URL 路径映射中列出各路径的顺序处理。

### <a name="associated-listener"></a>关联的侦听器

需要将关联到规则的侦听器，以便*请求路由规则*与关联*侦听器*评估以确定*后端池*到请求将被路由。

### <a name="associated-backend-pool"></a>关联后端池

将包含的后端目标，这将侦听器收到的请求提供服务的后端池相关联。 对于基本规则，允许仅一个后端池，因为关联的侦听器上的所有请求将都转发到此后端池。 对于基于路径的规则，添加与每个 URL 路径相对应的多个后端池。 匹配 URL 路径，在此处输入的请求将转发到相应的后端池。 此外，添加默认后端池，因为与此规则中输入任何 URL 路径不匹配的请求将转发给它。

### <a name="associated-backend-http-setting"></a>关联的后端 HTTP 设置

添加每个规则的后端 HTTP 设置。 将从到使用的端口号、 协议和其他设置，此设置中指定的后端目标应用程序网关路由请求。 对于基本规则，允许仅一个后端 HTTP 设置，因为关联的侦听器上的所有请求将都转发到相应的后端目标使用此 HTTP 设置。 对于基于路径的规则添加多个后端 HTTP 设置相对应的每个 URL 路径。 匹配 URL 路径，在此处输入的请求将转发到相应的后端目标使用对应于每个 URL 路径的 HTTP 设置。 此外，由于不匹配此规则中输入任何 URL 路径的请求将转发到使用默认 HTTP 设置的默认后端池添加默认 HTTP 设置。

### <a name="redirection-setting"></a>重定向设置

如果重定向配置为基本规则，关联的侦听器上的所有请求将重都定向到重定向目标，从而支持全局重定向。 如果重定向配置为基于路径的规则，请求仅在特定站点区域中，例如购物车区域为由/车 / *，将重定向到重定向目标，从而启用基于路径的重定向。 

有关重定向功能的信息，请参阅[重定向概述](https://docs.microsoft.com/azure/application-gateway/redirect-overview)。

- #### <a name="redirection-type"></a>重定向类型

  选择从所需的重定向的类型：永久临时的发现，或请参阅其他。

- #### <a name="redirection-target"></a>重定向目标

  您可以选择另一个侦听器或外部站点之间作为重定向目标。 

  - ##### <a name="listener"></a>侦听器

    选择作为重定向目标侦听器有助于将从一个侦听器重定向到在网关上的另一个侦听器。 当你想要启用 HTTP 到 HTTPS 重定向，即，来自源侦听器检查至目标侦听器的传入 HTTPS 请求检查传入的 HTTP 请求重定向流量时，此设置是必需的。 此外可以在转发到重定向目标的请求中包含的原始请求中选择的查询字符串和路径。![application-gateway-components](./media/configuration-overview/configure-redirection.png)

    对 HTTP 到 HTTPS 重定向的详细信息，请参阅[使用门户的 HTTP 到 HTTP 重定向](https://docs.microsoft.com/azure/application-gateway/redirect-http-to-https-portal)，[使用 PowerShell 的 HTTP 到 HTTP 重定向](https://docs.microsoft.com/azure/application-gateway/redirect-http-to-https-powershell)， [HTTP 到 HTTP 重定向使用 CLI](https://docs.microsoft.com/azure/application-gateway/redirect-http-to-https-cli)

  - ##### <a name="external-site"></a>外部站点

    如果你想要将与因此规则关联的侦听器，以重定向到外部站点上的流量重定向，请选择外部站点。 在转发到重定向目标的请求中包含的原始请求中，可以选择查询字符串。 不能转发到外部站点在原始请求中的路径。

    重定向到外部站点的详细信息，请参阅[将流量重定向到外部站点使用 PowerShell](https://docs.microsoft.com/azure/application-gateway/redirect-external-site-powershell)和 [https://docs.microsoft.com/azure/application-gateway/redirect-external-site-cli](https://docs.microsoft.com/azure/application-gateway/redirect-external-site-cli)

#### <a name="rewrite-http-header-setting"></a>重写 HTTP 标头设置

此功能，可添加、 删除或更新时请求的 HTTP 请求和响应头和响应数据包将客户端和后端池之间移动。    你可以配置此功能只能通过 PowerShell。 门户和 CLI 支持尚不可用。 有关详细信息，请参阅[重写 HTTP 标头](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers)概述和[配置 HTTP 标头重写](https://docs.microsoft.com/azure/application-gateway/add-http-header-rewrite-rule-powershell#specify-your-http-header-rewrite-rule-configuration)。

## <a name="http-settings"></a>HTTP 设置

应用程序网关将流量路由到后端服务器使用此组件中指定的配置。 后创建的 HTTP 设置，您需要将其与一个关联或更多请求路由规则。

### <a name="cookie-based-affinity"></a>基于 Cookie 的相关性

需要在同一台服务器上保留用户会话时，此功能非常有用。 借助网关托管的 Cookie，应用程序网关可以将来自用户会话的后续流量定向到同一服务器进行处理。 在用户会话的会话状态在服务器上进行本地保存的情况下，此功能十分重要。 如果应用程序不能处理基于 cookie 的相关性，你将无法使用此功能。 若要使用基于 cookie 的会话相关性，应确保客户端必须支持 cookie。 

### <a name="connection-draining"></a>连接清空

连接清空可帮助你在计划内服务更新期间正常删除后端池成员。 在创建规则期间，可将此设置应用到后端池的所有成员。 启用后，可确保应用程序网关的后端池的所有取消注册实例不会同时允许现有已配置的时间限制内完成的请求中收到任何新请求。 这适用于通过 API 调用显式从后端池中删除的后端实例，以及所报告的由运行状况探测确定为不正常的后端实例。

### <a name="protocol"></a>协议

应用程序网关支持 HTTP 和 HTTPS 协议的请求路由到后端服务器。 如果选择 HTTP 协议，然后流量流到后端服务器以未加密状态。 在这些情况下，与后端服务器的未加密的通信不是可接受的选项，则应选择 HTTPS 协议。 此设置与在侦听器中选择 HTTPS 协议，可启用[端到端 SSL](https://docs.microsoft.com/azure/application-gateway/ssl-overview)。 这允许你安全地传输到后端加密的敏感数据。 后端池中每个已启用端到端 SSL 的后端服务器都必须配置证书，以便能够进行安全的通信。

### <a name="port"></a>端口

这是后端服务器侦听来自应用程序网关的流量的端口。 您可以配置 1 到 65535 范围内的端口。

### <a name="request-timeout"></a>请求超时

应用程序网关等待返回"连接超时"错误之前从后端池接收响应的秒数。

### <a name="override-backend-path"></a>替代后端路径

此设置，可以配置该请求转发到后端时要使用的可选自定义转发路径。 这会将复制到自定义中指定的路径匹配传入路径中的任何一部分**重写后端路径**转发路径字段。 请参阅下表以了解功能的工作原理。

- 在 HTTP 设置附加到的基本请求路由规则：

  | 原始请求  | 替代后端路径 | 请求转发到后端 |
  | ----------------- | --------------------- | ---------------------------- |
  | /home/            | /override/            | 重写/home / /              |
  | / home/secondhome / | /override/            | 重写/home/secondhome /   |

- 在 HTTP 设置附加到基于路径的请求路由规则：

  | 原始请求           | 路径规则       | 替代后端路径 | 请求转发到后端 |
  | -------------------------- | --------------- | --------------------- | ---------------------------- |
  | /pathrule/home /            | /pathrule*      | /override/            | 重写/home / /              |
  | /pathrule/home/secondhome/ | /pathrule*      | /override/            | 重写/home/secondhome /   |
  | /home/                     | /pathrule*      | /override/            | 重写/home / /              |
  | / home/secondhome /          | /pathrule*      | /override/            | 重写/home/secondhome /   |
  | /pathrule/home /            | pathrule/主页 * | /override/            | /override/                   |
  | /pathrule/home/secondhome/ | pathrule/主页 * | /override/            | 重写/secondhome / /        |

### <a name="use-for-app-service"></a>用于应用服务

这是一个 UI 快捷方式，后者将选择应用服务后端所需的两个设置-允许选择从后端地址的主机名，并创建新的自定义探测。 为什么完成前的原因中的部分所述**选取主机名从后端地址**设置。 探测标头从后端成员的地址的还选取其中创建新的探测。

### <a name="use-custom-probe"></a>使用自定义探测

此设置用于将相关联[自定义探测](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#custom-health-probe)与此 HTTP 设置。 可以将只有一个自定义探测的 HTTP 设置与相关联。 如果您不显式将相关联的自定义探测，然后[默认探测](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#default-health-probe-settings)将用于监视后端的运行状况。 建议您创建能够更精细地控制你的后端运行状况监视的自定义探测。

> [!NOTE]   
> 自定义探测将会开始监视后端池的运行状况，除非相应的 HTTP 设置是显式与侦听器相关联。

### <a name="pick-host-name-from-backend-address"></a>从后端地址中选取主机名

此功能动态地设置*主机*中对后端池使用的 IP 地址或完全限定的域名 (FQDN) 的主机名的请求标头。 此功能的后端域名称不同于应用程序网关的 DNS 名称后, 端依赖于特定的主机标头或 SNI 扩展，以解析为正确的终结点，例如，对于多租户服务，如在方案中非常有用后端。 由于应用服务是一个共享的空间使用单个 IP 地址的多租户服务，可以仅使用自定义域设置中配置的主机名访问应用服务。 默认情况下，是自定义域名*example.azurewebsites.net*。 因此，如果你想要访问应用服务使用应用程序网关不显式注册在应用服务中的任一主机名或使用应用程序网关的 FQDN，您必须通过重写中对应用服务的主机名的原始请求的主机名启用**选取主机名从后端地址**设置。

如果你拥有自定义域，并将现有的自定义 DNS 名称映射到了应用服务，然后不需要启用此设置。

> [!NOTE]   
> 由于 ASE 是专门的部署，则不需要应用服务环境 (ASE) 此设置。 

### <a name="host-name-override"></a>主机名称替代

此功能取代了*主机*此处指定的主机名到应用程序网关上传入的请求标头中。 例如，如果 www\.contoso.com 指定为**主机名**设置，原始请求 https://appgw.eastus.cloudapp.net/path1将变为 https://www.contoso.com/path1时将请求转发到后端服务器。 

## <a name="backend-pool"></a>后端池

后端池可以指向四种类型的后端成员： 特定的虚拟机、 虚拟机规模集、 IP 地址/FQDN 或应用服务。 每个后端池可以指向同一类型的多个成员。 不支持指向同一后端池中的不同类型的成员。 

在创建后端池后，您需要将它与一个或多个请求路由规则相关联。 此外需要在应用程序网关配置为每个后端池的运行状况探测。 当满足请求路由规则条件时，应用程序网关将流量转发到相应的后端池中正常运行的服务器 （作为由运行状况探测确定）。

## <a name="health-probes"></a>运行状况探测

即使应用程序网关监视其后端中的所有资源的运行状况，默认情况下，强烈建议创建以便具有更精细地控制运行状况监视每个后端 HTTP 设置的自定义探测。 若要了解如何配置自定义运行状况探测，请参阅[自定义运行状况探测设置](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#custom-health-probe-settings)。

> [!NOTE]   
> 一旦创建自定义运行状况探测，需要将其关联到后端 HTTP 设置。 自定义探测将会开始监视后端池的运行状况，除非相应的 HTTP 设置是显式与侦听器相关联。

## <a name="next-steps"></a>后续步骤

了解后应用程序网关组件，你可以：

- [在 Azure 门户中创建应用程序网关](quick-create-portal.md)
- [创建使用 PowerShell 的应用程序网关](quick-create-powershell.md)
- [创建使用 Azure CLI 的应用程序网关](quick-create-cli.md)