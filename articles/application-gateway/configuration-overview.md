---
title: Azure 应用程序网关配置概述
description: 本文介绍如何配置 Azure 应用程序网关的组件
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 6/1/2019
ms.author: absha
ms.openlocfilehash: 55c7670821ee6c6f5b924bf18b5f7ad01d4b6d51
ms.sourcegitcommit: 087ee51483b7180f9e897431e83f37b08ec890ae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/31/2019
ms.locfileid: "66431299"
---
# <a name="application-gateway-configuration-overview"></a>应用程序网关配置概述

Azure 应用程序网关由多个组件构成，可根据不同的方案以不同的方式配置这些组件。 本文将会介绍如何配置每个组件。

![应用程序网关组件流程图](./media/configuration-overview/configuration-overview1.png)

此图演示了包含三个侦听器的应用程序。 前两个侦听器是分别用于 `http://acme.com/*` 和 `http://fabrikam.com/*` 的多站点侦听器， 两者在端口 80 上侦听。 第三个侦听器是支持端到端安全套接字层 (SSL) 终止的基本侦听器。


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>必备组件

### <a name="azure-virtual-network-and-dedicated-subnet"></a>Azure 虚拟网络和专用子网

应用程序网关是虚拟网络中的专用部署。 需要在虚拟网络中为应用程序网关配置一个专用子网。 在子网中，可以创建给定应用程序网关部署的多个实例。 还可以在该子网中部署其他应用程序网关。 但不能在应用程序网关子网中部署其他任何资源。

> [!NOTE]
> 不能在同一子网中混合使用 Standard_v2 和 Standard Azure 应用程序网关。

#### <a name="size-of-the-subnet"></a>子网的大小

如果配置了专用前端 IP，则应用程序网关将使用每个实例的 1 个专用 IP 地址，以及另一个专用 IP 地址。

另外，Azure 会在每个子网中保留 5 个 IP 地址供内部使用：前 4 个 IP 地址和最后一个 IP 地址。 例如，假设有 15 个应用程序网关实例没有专用前端 IP。 至少需要为此子网提供 20 个 IP 地址：5个 IP 地址供内部使用，15 个 IP 地址用于应用程序网关实例。 因此，需要 /27 或更大的子网大小。

假设某个子网包含 27 个应用程序网关实例，并且包含一个用作专用前端 IP 的 IP 地址。 在这种情况下，需要 33 个 IP 地址：27 个 IP 地址用于应用程序网关实例，1 个 IP 地址用于专用前端，5 个 IP 地址供内部使用。 因此，需要 /26 或更大的子网大小。

我们建议至少使用 /28 子网大小。 这种大小可以提供 11 个可用的 IP 地址。 如果应用程序负载需要 10 个以上的 IP 地址，请考虑 /27 或 /26 子网大小。

#### <a name="network-security-groups-on-the-application-gateway-subnet"></a>应用程序网关子网中的网络安全组

应用程序网关支持网络安全组 (NSG)。 但同时存在多种限制：

- 必须包括 v2 sku 上的应用程序网关 v1 sku 端口 65503-65534 和端口 65200 到 65535 之间的传入流量的异常。 此端口范围是进行 Azure 基础结构通信所必需的。 这些端口受 Azure 证书的保护（处于锁定状态）。 如果没有适当的证书，外部实体（包括这些网关的客户）将无法对这些终结点做出任何更改。

- 不能阻止出站 Internet 连接。 NSG 中的默认出站规则允许 Internet 连接。 建议：

  - 不要删除默认出站规则。
  - 不要创建拒绝出站 Internet 连接的其他出站规则。

- 必须允许来自 **AzureLoadBalancer** 标记的流量。

##### <a name="allow-application-gateway-access-to-a-few-source-ips"></a>允许应用程序网关访问权限将一些源 Ip

对于此方案，请在应用程序网关子网中使用 NSG。 按以下优先顺序对子网施加以下限制：

1. 允许来自源 IP/IP 范围的传入流量。
2. 允许来自所有源的请求传入端口 65503-65534，以实现[后端运行状况通信](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)。 此端口范围是进行 Azure 基础结构通信所必需的。 这些端口受 Azure 证书的保护（处于锁定状态）。 如果没有适当的证书，外部实体将无法对这些终结点做出任何更改。
3. 允许[网络安全组](https://docs.microsoft.com/azure/virtual-network/security-overview)中的传入 Azure 负载均衡器探测（*AzureLoadBalancer* 标记）和入站虚拟网络流量（*VirtualNetwork* 标记）。
4. 使用“全部拒绝”规则阻止其他所有传入流量。
5. 允许所有目的地的 Internet 出站流量。

#### <a name="user-defined-routes-supported-on-the-application-gateway-subnet"></a>应用程序网关子网支持用户定义的路由

使用 v1 SKU 时，只要用户定义的路由 (UDR) 未更改端到端请求/响应通信，则应用程序网关子网就会支持这些 UDR。 例如，可以在应用程序网关子网中设置一个指向防火墙设备的、用于检查数据包的 UDR。 但是，必须确保数据包在检查后可以访问其预期目标。 否则，可能会导致不正确的运行状况探测或流量路由行为。 这包括已探测到的路由，或者通过 Azure ExpressRoute 或 VPN 网关在虚拟网络中传播的默认 0.0.0.0/0 路由。

V2 sku，不支持应用程序网关子网 Udr。 有关详细信息，请参阅[Azure 应用程序网关 v2 SKU](application-gateway-autoscaling-zone-redundant.md#differences-with-v1-sku)。

> [!NOTE]
> 在应用程序网关子网中使用 UDR 会导致[后端运行状况视图](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#back-end-health)中的运行状态显示为“未知”。 此外，还会导致应用程序网关日志和指标生成失败。 建议不要在应用程序网关子网中使用 UDR，以便能够查看后端运行状况、日志和指标。

## <a name="front-end-ip"></a>前端 IP

可将应用程序网关配置为使用公共 IP 地址和/或专用 IP 地址。 托管需要由客户端在 Internet 中通过面向 Internet 的虚拟 IP (VIP) 访问的后端时，必须使用公共 IP。 

不向 Internet 公开的内部终结点不需要公共 IP。 该终结点称为内部负载均衡器 (ILB) 终结点。  应用程序网关 ILB 适合用于不向 Internet 公开的内部业务线应用程序。 对于位于不向 Internet 公开的安全边界内的多层级应用程序中的服务和层级，ILB 也很有用，但需要启用轮循机制负载分配、会话粘性或 SSL 终止。

仅支持 1 个公共 IP 地址或 1 个专用 IP 地址。 在创建应用程序网关时选择前端 IP。

- 对于公共 IP，可以在应用程序网关所在的同一位置创建新的公共 IP 地址或使用现有的公共 IP。 如果创建新的公共 IP，则以后无法更改选定的 IP 地址类型（静态或动态）。 有关详细信息，请参阅[静态与动态公共 IP 地址](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#static-vs-dynamic-public-ip-address)。

- 对于专用 IP，可以在创建应用程序网关的子网中指定一个专用 IP 地址。 如果不显式指定专用 IP 地址，则系统会在子网中自动选择一个任意 IP 地址。 有关详细信息，请参阅[创建包含内部负载均衡器的应用程序网关](https://docs.microsoft.com/azure/application-gateway/application-gateway-ilb-arm)。

某个前端 IP 地址将关联到检查前端 IP 上的传入请求的侦听器。 

## <a name="listeners"></a>侦听器

侦听器是一个逻辑实体，它可以使用端口、协议、主机和 IP 地址检查传入的连接请求。 配置侦听器时，必须输入与网关上传入请求中的对应值相匹配的值。

使用 Azure 门户创建应用程序网关时，还可以通过选择侦听器的协议和端口来创建默认的侦听器。 可以选择是否要在侦听器上启用 HTTP2 支持。 创建应用程序网关后，可以编辑该默认侦听器的设置 (*appGatewayHttpListener*/*appGatewayHttpsListener*) 或创建新的侦听器。

### <a name="listener-type"></a>侦听器类型

创建新侦听器时，可以选择[“基本”或“多站点”](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#types-of-listeners)。  

- 如果在应用程序网关后面托管单个站点，请选择“基本”。 了解[如何创建包含基本侦听器的应用程序网关](https://docs.microsoft.com/azure/application-gateway/quick-create-portal)。

- 如果在同一个应用程序网关实例上配置具有相同父域的多个 Web 应用程序或多个子域，请选择多站点侦听器。 对于多站点侦听器，还需要输入主机名。 这是因为，应用程序网关需要使用 HTTP 1.1 主机标头才能在相同的公共 IP 地址和端口上托管多个网站。

#### <a name="order-of-processing-listeners"></a>侦听器的处理顺序

使用 v1 SKU 时，侦听器将按其列出顺序进行处理。 如果基本侦听器与传入请求匹配，该侦听器会先处理该请求。 因此，请先配置多站点侦听器，再配置基本侦听器，以确保将流量路由到正确的后端。

V2 sku，基本侦听器之前处理多站点侦听器。

### <a name="front-end-ip"></a>前端 IP

选择要与此侦听器关联的前端 IP 地址。 侦听器将在此 IP 上侦听传入的请求。

### <a name="front-end-port"></a>前端端口

选择前端端口。 选择现有端口或新建一个端口。 选择[允许的端口范围](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#ports)内的任意值。 不仅可以使用已知的端口（例如 80 和 443），而且还能使用任何适用的且允许的自定义端口。 一个端口可用于公共侦听器或专用侦听器。

### <a name="protocol"></a>Protocol

选择 HTTP 或 HTTPS：

- 如果选择 HTTP，则客户端与应用程序网关之间的流量将不会加密。

- 如果想要实现 [SSL 终止](https://docs.microsoft.com/azure/application-gateway/overview#secure-sockets-layer-ssl-terminationl)或[端到端 SSL 加密](https://docs.microsoft.com/azure/application-gateway/ssl-overview)，请选择 HTTPS。 客户端与应用程序网关之间的流量将会加密。 SSL 连接将在应用程序网关上终止。 若要实现端到端的 SSL 加密，必须选择 HTTPS，并配置**后端 HTTP** 设置。 这可以确保流量在从应用程序网关传输到后端时重新得到加密。

若要配置 SSL 终止和端到端 SSL 加密，必须将一个证书添加到侦听器，使应用程序网关能够派生对称密钥。 派生过程中根据 SSL 协议规范进行的。 使用该对称密钥可以加密和解密发送到网关的流量。 网关证书必须采用个人信息交换 (PFX) 格式。 使用此格式可以导出私钥，供网关用来加密和解密流量。

#### <a name="supported-certificates"></a>支持的证书

请参阅[支持用于 SSL 终止的证书](https://docs.microsoft.com/azure/application-gateway/ssl-overview#certificates-supported-for-ssl-termination)。

### <a name="additional-protocol-support"></a>其他协议支持

#### <a name="http2-support"></a>HTTP2 支持

仅针对连接到应用程序网关侦听器的客户端提供 HTTP/2 协议支持。 与后端服务器池的通信是通过 HTTP/1.1 进行的。 默认情况下，HTTP/2 支持处于禁用状态。 以下 Azure PowerShell 代码片段演示如何启用此支持：

```azurepowershell
$gw = Get-AzApplicationGateway -Name test -ResourceGroupName hm

$gw.EnableHttp2 = $true

Set-AzApplicationGateway -ApplicationGateway $gw
```

#### <a name="websocket-support"></a>WebSocket 支持

默认已启用 WebSocket 支持。 没有任何用户可配置的设置可以启用或禁用此支持。 可对 HTTP 和 HTTPS 侦听器使用 WebSocket。

### <a name="custom-error-pages"></a>自定义错误页

可以在全局级别以及侦听器级别定义自定义错误。 但是，目前不支持在 Azure 门户中创建全局级别的自定义错误页。 可以在侦听器级别为 403 Web 应用程序防火墙错误或 502 维护页配置自定义错误页。 此外，必须为给定的错误状态代码指定一个可公开访问的 Blob URL。 有关详细信息，请参阅[创建应用程序网关自定义错误页](https://docs.microsoft.com/azure/application-gateway/custom-error)。

![应用程序网关错误代码](https://docs.microsoft.com/azure/application-gateway/media/custom-error/ag-error-codes.png)

若要配置全局自定义错误页，请参阅 [Azure PowerShell 配置](https://docs.microsoft.com/azure/application-gateway/custom-error#azure-powershell-configuration)。

### <a name="ssl-policy"></a>SSL 策略

可以集中管理 SSL 证书，以及减小后端服务器场的加密-解密开销。 采用集中式 SSL 处理还能指定符合安全要求的集中 SSL 策略。 可以选择默认、预定义或自定义的 SSL 策略。   

可以配置 SSL 策略来控制 SSL 协议版本。 可将应用程序网关配置为拒绝 TLS1.0、TLS1.1 和 TLS1.2。 默认情况下，SSL 2.0 和 3.0 已禁用且不可配置。 有关详细信息，请参阅[应用程序网关 SSL 策略概述](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-policy-overview)。

创建侦听器后，请将它关联到某个请求路由规则。 该规则确定如何将侦听器上收到的请求路由到后端。

## <a name="request-routing-rules"></a>请求路由规则

使用 Azure 门户创建应用程序网关时，可创建一个默认规则 (*rule1*)。 此规则会将默认侦听器 (*appGatewayHttpListener*) 绑定到默认后端池 (*appGatewayBackendPool*) 和默认后端 HTTP 设置 (*appGatewayBackendHttpSettings*)。 创建网关后，可以编辑该默认规则的设置，或创建新的规则。

### <a name="rule-type"></a>规则类型

创建规则时，可以选择[“基本”或“基于路径”](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#request-routing-rule)。  

- 若要将关联的侦听器（例如 *blog<i></i>.contoso.com/\** ）上的所有请求转发到单个后端池，请选择“基本”。
- 若要将来自特定 URL 路径的请求路由到特定的后端池，请选择“基于路径”。 路径模式仅应用到 URL 的路径，而不应用到该 URL 的查询参数。

#### <a name="order-of-processing-rules"></a>规则的处理顺序

使用 v1 SKU 时，将按照路径在基于路径的规则的 URL 路径映射中的列出顺序处理传入请求的模式匹配。 如果某个请求与 URL 路径映射中的两个或更多个路径的模式相匹配，则会匹配最先列出的路径。 请求将转发到与该路径关联的后端。

对于 v2 SKU，完全匹配是优先级高于路径 URL 路径映射中的顺序。 如果某个请求与匹配两个或多个路径中的模式，该请求转发到后端使用与请求完全匹配的路径关联。 如果传入请求中的路径并不完全匹配在映射中的任何路径，该请求的模式匹配基于路径的规则路径映射顺序列表中进行处理。

### <a name="associated-listener"></a>关联的侦听器

将一个侦听器关联到该规则，以评估与该侦听器关联的请求路由规则，从而确定请求要路由到的后端池。 

### <a name="associated-back-end-pool"></a>关联的后端池

将规则关联到包含后端目标的后端池，该池为侦听器收到的请求提供服务。

 - 如果使用基本规则，则只允许一个后端池。 关联的侦听器上的所有请求将转发到该后端池。

 - 如果使用基于路径的规则，请添加对应于每个 URL 路径的多个后端池。 与输入的 URL 路径匹配的请求将转发到相应的后端池。 另请添加默认后端池。 与规则中的任何 URL 路径都不匹配的请求将转发到该池。

### <a name="associated-back-end-http-setting"></a>关联的后端 HTTP 设置

为每个规则添加后端 HTTP 设置。 系统使用此设置中指定的端口号、协议和其他信息，将请求从应用程序网关路由到后端目标。

如果使用基本规则，则只允许一个后端 HTTP 设置。 系统会使用此 HTTP 设置将关联的侦听器上的所有请求转发到相应的后端目标。

为每个规则添加后端 HTTP 设置。 系统使用此设置中指定的端口号、协议和其他信息，将请求从应用程序网关路由到后端目标。

如果使用基本规则，则只允许一个后端 HTTP 设置。 系统会使用此 HTTP 设置将关联的侦听器上的所有请求转发到相应的后端目标。

如果使用基于路径的规则，请添加对应于每个 URL 路径的多个后端 HTTP 设置。 系统使用对应于每个 URL 路径的 HTTP 设置，将与此设置中的 URL 路径匹配的请求转发到相应的后端目标。 另请添加默认 HTTP 设置。 系统会使用默认 HTTP 设置，将与此规则中的任何 URL 路径都不匹配的请求转发到默认后端池。

### <a name="redirection-setting"></a>重定向设置

如果为基本规则配置了重定向，则关联的侦听器上的所有请求将重定向到目标。 此过程称为全局重定向。  如果为基于路径的规则配置了重定向，则只会重定向特定站点区域中的请求。 区域的示例包括 */cart/\** 表示的购物车区域。 此过程称为基于路径的重定向。 

有关重定向的详细信息，请参阅[应用程序网关重定向概述](https://docs.microsoft.com/azure/application-gateway/redirect-overview)。

#### <a name="redirection-type"></a>重定向类型

选择所需的重定向类型：*Permanent(301)* 、*Temporary(307)* 、*Found(302)* 或 *See other(303)* 。

#### <a name="redirection-target"></a>重定向目标

选择另一个侦听器或外部站点作为重定向目标。

##### <a name="listener"></a>侦听器

选择侦听器作为重定向目标可将来自网关上的一个侦听器的流量重定向到另一个侦听器。 想要启用 HTTP 到 HTTPS 的重定向时，必须指定此设置。 此设置将来自源侦听器（用于检查 HTTP 请求）的流量重定向到目标侦听器（用于检查传入的 HTTPS 请求）。 还可以选择在转发到重定向目标的请求中包含来自原始请求的查询字符串和路径。

![应用程序网关组件对话框](./media/configuration-overview/configure-redirection.png)

有关 HTTP 到 HTTPS 的重定向的详细信息，请参阅：
- [使用 Azure 门户配置 HTTP 到 HTTPS 的重定向](https://docs.microsoft.com/azure/application-gateway/redirect-http-to-https-portal)
- [使用 PowerShell 配置 HTTP 到 HTTPS 的重定向](https://docs.microsoft.com/azure/application-gateway/redirect-http-to-https-powershell)
- [使用 Azure CLI 配置 HTTP 到 HTTPS 的重定向](https://docs.microsoft.com/azure/application-gateway/redirect-http-to-https-cli)

##### <a name="external-site"></a>外部站点

若要将与此类规则关联的侦听器上的流量重定向到外部站点，请选择外部站点。 可以选择在转发到重定向目标的请求中包含来自原始请求的查询字符串。 无法将原始请求中的路径转发到外部站点。

有关重定向的详细信息，请参阅：
- [使用 PowerShell 将流量重定向到外部站点](https://docs.microsoft.com/azure/application-gateway/redirect-external-site-powershell)
- [使用 CLI 将流量重定向到外部站点](https://docs.microsoft.com/azure/application-gateway/redirect-external-site-cli)

#### <a name="rewrite-the-http-header-setting"></a>重写 HTTP 标头设置

此设置将添加、 删除或更新时请求的 HTTP 请求和响应头和响应数据包的客户端和后端池之间移动。 您只能配置此功能通过 PowerShell。 Azure 门户和 CLI 支持尚不可用。 有关详细信息，请参阅：

 - [重写 HTTP 标头概述](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers)
 - [配置 HTTP 标头重写](https://docs.microsoft.com/azure/application-gateway/add-http-header-rewrite-rule-powershell#specify-your-http-header-rewrite-rule-configuration)

## <a name="http-settings"></a>HTTP 设置

应用程序网关使用此处指定的配置将流量路由到后端服务器。 创建 HTTP 设置后，必须将其关联到一个或多个请求路由规则。

### <a name="cookie-based-affinity"></a>基于 Cookie 的相关性

需要在同一台服务器上保留用户会话时，此功能非常有用。 使用网关托管的 Cookie，应用程序网关可将来自用户会话的后续流量定向到同一服务器进行处理。 如果用户会话的会话状态保存在服务器本地，则此功能十分重要。 如果应用程序无法处理基于 Cookie 的相关性，则你无法使用此功能。 若要使用此功能，请确保客户端支持 Cookie。

### <a name="connection-draining"></a>连接清空

连接清空可帮助你在计划内服务更新期间正常删除后端池成员。 在创建规则期间，可将此设置应用到后端池的所有成员。 此设置可确保后端池的所有已取消注册实例不再收到任何新请求。 同时，允许现有请求在所配置的时间限制内完成。 连接清空将应用到已通过 API 调用从后端池中显式删除的后端实例。 它还会应用到被运行状况探测报告为“不正常”的后端实例。 

### <a name="protocol"></a>Protocol

应用程序网关支持使用 HTTP 和 HTTPS 将请求路由到后端服务器。 如果选择了 HTTP 协议，则流量将以未加密的形式传送到后端服务器。 如果不能接受未加密的通信，请选择 HTTPS。

在侦听器中结合 HTTPS 使用此设置将有助于实现[端到端的 SSL](https://docs.microsoft.com/azure/application-gateway/ssl-overview)。 这样，就可以安全地将敏感数据以加密的形式传输到后端。 后端池中每个已启用端到端 SSL 的后端服务器都必须配置证书，以便能够进行安全的通信。

### <a name="port"></a>Port

此设置指定后端服务器要在哪个端口上侦听来自应用程序网关的流量。 可以配置 1 到 65535 的端口号。

### <a name="request-timeout"></a>请求超时

此设置表示应用程序网关等待后端池做出响应的秒数，如此超过此秒数，则会返回“连接超时”错误消息。

### <a name="override-back-end-path"></a>替代后端路径

使用此设置可以配置可选的自定义转发路径，以便在将请求转发到后端时使用。 与“替代后端路径”字段中的自定义路径匹配的任意传入路径部分将复制到转发的路径。  下表描述了此功能的工作原理：

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

### <a name="use-for-app-service"></a>用于应用服务

这是一个 UI 快捷方式，用于选择 Azure 应用服务后端的两个所需设置。 它会启用“从后端地址中选取主机名”，并创建新的自定义探测。  （有关详细信息，请参阅本文的[从后端地址中选取主机名](#pick)设置部分。）将创建新的探测，并从后端成员的地址中选取探测标头。

### <a name="use-custom-probe"></a>使用自定义探测

此设置用于将[自定义探测](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#custom-health-probe)与某个 HTTP 设置相关联。 只能将一个自定义探测关联到某个 HTTP 设置。 如果未显式关联自定义探测，则会使用[默认探测](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#default-health-probe-settings)来监视后端的运行状况。 我们建议创建自定义探测，以便更好地控制后端的运行状况监视。

> [!NOTE]
> 只有在将相应的 HTTP 设置显式关联到某个侦听器之后，自定义探测才会监视后端池的运行状况。

### <a id="pick"/></a>从后端地址中选取主机名

此功能将请求中的 *host* 标头动态设置为后端池的主机名。 主机名使用 IP 地址或 FQDN。

如果后端的域名不同于应用程序网关的 DNS 名称，并且后端必须使用特定的 host 标头或服务器名称指示 (SNI) 扩展才能解析为正确的终结点，则此功能会很有帮助。

例如，使用多租户服务作为后端时。 应用服务是使用共享空间和单个 IP 地址的多租户服务。 因此，只能通过自定义域设置中配置的主机名访问应用服务。

自定义域名默认为 *example.azurewebsites.<i></i>net*。 若要通过未显式注册到应用服务中的主机名或者通过应用程序网关的 FQDN 使用应用程序网关访问应用服务，请将原始请求中的主机名替代为应用服务的主机名。 为此，请启用“从后端地址中选取主机名”设置。 

对于其现有自定义 DNS 名称已映射到应用服务的自定义域，不需要启用此设置。

> [!NOTE]
> 适用于 PowerApps 的应用服务环境不需要此设置，因为它属于专用部署。

### <a name="host-name-override"></a>主机名替代

此功能可将应用程序网关上的传入请求中的 *host* 标头替换为指定的主机名。

例如，如果*www.contoso<i></i>.com*中指定**主机名**设置，原始请求*https:/<i></i>/appgw.eastus.cloudapp.net/path1*更改为*https:/<i></i>/www.contoso.com/path1*时将请求转发到后端服务器。

## <a name="back-end-pool"></a>后端池

可将后端池指向四种类型的后端成员：特定的虚拟机、虚拟机规模集、IP 地址/FQDN 或应用服务。 每个后端池可以指向同一类型的多个成员。 不支持指向同一后端池中不同类型的成员。

创建后端池后，必须将其关联到一个或多个请求路由规则。 此外，必须为应用程序网关上的每个后端池配置运行状况探测。 满足请求路由规则条件时，应用程序网关会将流量转发到相应后端池中正常运行的服务器（是否正常由运行状况探测决定）。

## <a name="health-probes"></a>运行状况探测

应用程序网关默认会监视其后端中所有资源的运行状况。 但是，我们强烈建议为每个后端 HTTP 设置创建一个自定义探测，以便更好地控制运行状况监视。 若要了解如何配置自定义探测，请参阅[自定义运行状况探测设置](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#custom-health-probe-settings)。

> [!NOTE]
> 创建自定义运行状况探测后，需将其关联到后端 HTTP 设置。 只有在将相应的 HTTP 设置显式关联到某个侦听器之后，自定义探测才会监视后端池的运行状况。

## <a name="next-steps"></a>后续步骤

了解应用程序网关组件后，可以：

- [在 Azure 门户中创建应用程序网关](quick-create-portal.md)
- [使用 PowerShell 创建应用程序网关](quick-create-powershell.md)
- [使用 Azure CLI 创建应用程序网关](quick-create-cli.md)
