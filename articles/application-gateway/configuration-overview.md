---
title: Azure 应用程序网关配置概述
description: 本文介绍如何配置 Azure 应用程序网关的组件
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/15/2019
ms.author: absha
ms.openlocfilehash: 79867bd048be882414e247af11c133ed481788a0
ms.sourcegitcommit: d614a9fc1cc044ff8ba898297aad638858504efa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2019
ms.locfileid: "74996623"
---
# <a name="application-gateway-configuration-overview"></a>应用程序网关配置概述

Azure 应用程序网关包含多个组件，你可以在不同方案中以多种方式对其进行配置。 本文介绍如何配置每个组件。

![应用程序网关组件流程图](./media/configuration-overview/configuration-overview1.png)

此图说明了包含三个侦听器的应用程序。 前两个分别是 `http://acme.com/*` 和 `http://fabrikam.com/*`的多站点侦听器。 两者都在端口80上侦听。 第三种是具有端对端安全套接字层（SSL）终止的基本侦听器。


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>必备组件

### <a name="azure-virtual-network-and-dedicated-subnet"></a>Azure 虚拟网络和专用子网

应用程序网关是虚拟网络中的专用部署。 在虚拟网络中，应用程序网关需要专用子网。 一个子网中可以有多个给定应用程序网关部署的实例。 你还可以在子网中部署其他应用程序网关。 但不能在应用程序网关子网中部署任何其他资源。

> [!NOTE]
> 不能将 Standard_v2 和标准 Azure 应用程序网关混合到同一子网中。

#### <a name="size-of-the-subnet"></a>子网的大小

应用程序网关每个实例使用1个专用 IP 地址，此外，如果配置了专用前端 IP，还会使用另一个专用 IP 地址。

Azure 还在每个子网中保留5个 IP 地址供内部使用：前4个和最后一个 IP 地址。 例如，考虑15个没有专用前端 IP 的应用程序网关实例。 对于此子网，需要至少20个 IP 地址：5供内部使用，15表示应用程序网关实例。 因此，你需要/27 子网大小或更大的子网大小。

请考虑一个具有27个应用程序网关实例的子网，以及一个专用前端 IP 的 IP 地址。 在这种情况下，你需要33个 IP 地址：27个用于应用程序网关实例，1个用于专用前端，5个用于内部使用。 因此，你需要/26 子网大小或更大。

建议使用至少为28的子网大小。 此大小提供了11个可用的 IP 地址。 如果应用程序加载需要超过10个应用程序网关实例，请考虑使用/27 或/26 子网大小。

#### <a name="network-security-groups-on-the-application-gateway-subnet"></a>应用程序网关子网中的网络安全组

应用程序网关上支持网络安全组（Nsg）。 但有几个限制：

- 对于应用程序网关 v1 SKU，你必须允许 TCP 端口65503-65534 上的传入 Internet 流量，并允许 v2 SKU 的 TCP 端口65200-65535 （目标子网为*Any*）。 此端口范围是进行 Azure 基础结构通信所必需的。 这些端口受 Azure 证书保护（锁定）。 外部实体（包括这些网关的客户）无法在没有适当证书的情况下在这些终结点上启动更改。

- 不能阻止出站 Internet 连接。 NSG 中的默认出站规则允许 internet 连接。 建议：

  - 请勿删除默认的出站规则。
  - 请勿创建其他拒绝出站 internet 连接的出站规则。

- 必须允许来自**AzureLoadBalancer**标记的流量。

##### <a name="allow-application-gateway-access-to-a-few-source-ips"></a>允许应用程序网关访问一些源 Ip

对于此方案，请在应用程序网关子网中使用 Nsg。 按照此优先级顺序在子网上施加以下限制：

1. 允许来自源 IP 或 IP 范围的传入流量和目标作为整个应用程序网关子网，或作为特定的已配置专用前端 IP。 NSG 不适用于公共 IP。
2. 对于应用程序网关 v1 SKU，允许所有源的传入请求传入端口65503-65534，为 v2 SKU 允许端口65200-65535 进行[后端运行状况通信](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)。 此端口范围是进行 Azure 基础结构通信所必需的。 这些端口受 Azure 证书保护（锁定）。 如果没有适当的证书，外部实体将无法在这些终结点上启动更改。
3. 允许[网络安全组](https://docs.microsoft.com/azure/virtual-network/security-overview)上的传入 Azure 负载均衡器探测（*AzureLoadBalancer*标记）和入站虚拟网络流量（*VirtualNetwork*标记）。
4. 使用 "全部拒绝" 规则阻止其他所有传入流量。
5. 允许所有目的地的 Internet 出站流量。

#### <a name="user-defined-routes-supported-on-the-application-gateway-subnet"></a>应用程序网关子网支持的用户定义的路由

对于 v1 SKU，应用程序网关子网支持用户定义的路由（Udr），前提是它们不更改端到端的请求/响应通信。 例如，可以在应用程序网关子网中设置 UDR，以指向用于数据包检查的防火墙设备。 但必须确保在检查后，数据包可以到达其预期目标。 否则，可能会导致不正确的运行状况探测或流量路由行为。 这包括通过 Azure ExpressRoute 或虚拟网络中的 VPN 网关传播的已知路由或默认 0.0.0.0/0 路由。

对于 v2 SKU，应用程序网关子网不支持 Udr。 有关详细信息，请参阅[Azure 应用程序 Gateway V2 SKU](application-gateway-autoscaling-zone-redundant.md#differences-with-v1-sku)。

> [!NOTE]
> V2 SKU 不支持 Udr。  如果需要 Udr，应继续部署 v1 SKU。

> [!NOTE]
> 在应用程序网关子网上使用 Udr 会导致[后端运行状况视图](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#back-end-health)中的运行状况状态显示为 "未知"。 它还会导致生成应用程序网关日志和指标失败。 建议你不要在应用程序网关子网中使用 Udr，以便可以查看后端运行状况、日志和指标。

## <a name="front-end-ip"></a>前端 IP

你可以将应用程序网关配置为具有公共 IP 地址和/或专用 IP 地址。 当你托管客户端必须通过面向 internet 的虚拟 IP （VIP）通过 internet 访问的后端时，需要公共 IP。 

不会向 internet 公开的内部终结点不需要公共 IP。 这称为*内部负载均衡器*（ILB）终结点或专用前端 IP。 对于不向 internet 公开的内部业务线应用程序，应用程序网关 ILB 非常有用。 这对于不向 internet 公开但需要轮循负载分发、会话粘性或 SSL 终止的安全边界内的多层应用程序中的服务和层也很有用。

仅支持1个公共 IP 地址或1个专用 IP 地址。 创建应用程序网关时，请选择前端 IP。

- 对于公共 IP，你可以在应用程序网关所在的同一位置创建新的公共 IP 地址或使用现有的公共 IP 地址。 有关详细信息，请参阅[静态和动态公共 IP 地址](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#static-versus-dynamic-public-ip-address)。

- 对于专用 IP，可以指定在其中创建应用程序网关的子网中的专用 IP 地址。 如果未指定此项，则会自动从子网中选择任意 IP 地址。 以后无法更改所选的 IP 地址类型（静态或动态）。 有关详细信息，请参阅[创建具有内部负载均衡器的应用程序网关](https://docs.microsoft.com/azure/application-gateway/application-gateway-ilb-arm)。

前端 IP 地址与*侦听器*相关联，后者检查前端 ip 的传入请求。

## <a name="listeners"></a>侦听器

侦听器是一个逻辑实体，通过使用端口、协议、主机和 IP 地址来检查传入的连接请求。 配置侦听器时，必须输入与网关上传入请求中的相应值相匹配的值。

使用 Azure 门户创建应用程序网关时，还可以通过为侦听器选择协议和端口来创建默认侦听器。 你可以选择是否在侦听器上启用 HTTP2 支持。 创建应用程序网关后，可以编辑该默认侦听器（*appGatewayHttpListener*）的设置或创建新的侦听器。

### <a name="listener-type"></a>侦听器类型

当你创建新的侦听器时，你可以在[*基本*和*多站点*之间进行](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#types-of-listeners)选择。

- 如果希望接受所有请求（对于任何域）并将其转发到后端池，请选择 "基本"。 了解[如何创建具有基本侦听器的应用程序网关](https://docs.microsoft.com/azure/application-gateway/quick-create-portal)。

- 如果希望根据*主机*标头或主机名将请求转发到不同的后端池，请选择 "多站点侦听器"，其中你还必须指定与传入请求匹配的主机名。 这是因为，应用程序网关依赖于 HTTP 1.1 主机标头在同一公共 IP 地址和端口上托管多个网站。

#### <a name="order-of-processing-listeners"></a>处理侦听器的顺序

对于 v1 SKU，请求会根据规则顺序和侦听器类型进行匹配。 如果具有基本侦听器的规则首先出现在订单中，则先处理该规则，并接受该端口和 IP 组合的任何请求。 若要避免这种情况，请先配置多站点侦听器的规则，并将规则与基本侦听器一起推送到列表中的最后一个。

对于 v2 SKU，多站点侦听器在基本侦听器之前进行处理。

### <a name="front-end-ip"></a>前端 IP

选择计划与此侦听器关联的前端 IP 地址。 侦听器将侦听此 IP 上的传入请求。

### <a name="front-end-port"></a>前端端口

选择前端端口。 选择一个现有端口或创建一个新端口。 从[允许的端口范围](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#ports)中选择任何值。 你不仅可以使用众所周知的端口，例如80和443，还可以使用合适的任何允许的自定义端口。 端口可用于面向公众的侦听器或面向专用的侦听器。

### <a name="protocol"></a>协议

选择 HTTP 或 HTTPS：

- 如果选择 HTTP，则不会加密客户端和应用程序网关之间的流量。

- 如果需要[ssl 终止](https://docs.microsoft.com/azure/application-gateway/overview#secure-sockets-layer-ssltls-termination)或[端到端 ssl 加密](https://docs.microsoft.com/azure/application-gateway/ssl-overview)，请选择 "HTTPS"。 客户端和应用程序网关之间的流量会被加密。 SSL 连接将在应用程序网关上终止。 如果需要端到端 SSL 加密，则必须选择 "HTTPS" 并配置**后端 HTTP**设置。 这可确保当流量从应用程序网关传输到后端时，会重新对其进行加密。

若要配置 SSL 终止和端到端 SSL 加密，必须将证书添加到侦听器，以使应用程序网关能够派生对称密钥。 这由 SSL 协议规范决定。 对称密钥用于加密和解密发送到网关的流量。 网关证书必须为个人信息交换（PFX）格式。 此格式允许您导出网关用于加密和解密流量的私钥。

#### <a name="supported-certificates"></a>支持的证书

请参阅[SSL 终止支持的证书](https://docs.microsoft.com/azure/application-gateway/ssl-overview#certificates-supported-for-ssl-termination)。

### <a name="additional-protocol-support"></a>其他协议支持

#### <a name="http2-support"></a>HTTP2 支持

HTTP/2 协议支持仅适用于连接到应用程序网关侦听器的客户端。 与后端服务器池的通信通过 HTTP/1.1 进行。 默认情况下，HTTP/2 支持处于禁用状态。 以下 Azure PowerShell 代码片段演示了如何启用此操作：

```azurepowershell
$gw = Get-AzApplicationGateway -Name test -ResourceGroupName hm

$gw.EnableHttp2 = $true

Set-AzApplicationGateway -ApplicationGateway $gw
```

#### <a name="websocket-support"></a>WebSocket 支持

默认情况下启用 WebSocket 支持。 无用户可配置的设置来启用或禁用该设置。 可以将 Websocket 与 HTTP 和 HTTPS 侦听器一起使用。

### <a name="custom-error-pages"></a>自定义错误页

可以在全局级别或侦听器级别定义自定义错误。 但当前不支持从 Azure 门户创建全局级别的自定义错误页。 可以在侦听器级别为 403 web 应用程序防火墙错误或502维护页配置自定义错误页。 还必须指定给定错误状态代码的可公开访问的 blob URL。 有关详细信息，请参阅[创建应用程序网关自定义错误页](https://docs.microsoft.com/azure/application-gateway/custom-error)。

![应用程序网关错误代码](https://docs.microsoft.com/azure/application-gateway/media/custom-error/ag-error-codes.png)

若要配置全局自定义错误页，请参阅[Azure PowerShell 配置](https://docs.microsoft.com/azure/application-gateway/custom-error#azure-powershell-configuration)。

### <a name="ssl-policy"></a>SSL 策略

可以集中 SSL 证书管理，并减少后端服务器场的加密解密开销。 集中式 SSL 处理还允许指定适合于安全要求的中央 SSL 策略。 你可以选择*默认*、*预定义*或*自定义*SSL 策略。

你可以配置 SSL 策略来控制 SSL 协议版本。 你可以将应用程序网关配置为使用 TLS 1.0、TLS 1.1 和 TLS 1.2 中 TLS 握手的最小协议版本。 默认情况下，SSL 2.0 和3.0 处于禁用状态且不可配置。 有关详细信息，请参阅[应用程序网关 SSL 策略概述](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-policy-overview)。

创建侦听器后，将其与请求路由规则相关联。 该规则确定如何将侦听器上收到的请求路由到后端。

## <a name="request-routing-rules"></a>请求路由规则

使用 Azure 门户创建应用程序网关时，将创建默认规则（*rule1*）。 此规则将默认侦听器（*appGatewayHttpListener*）与默认的后端池（*appGatewayBackendPool*）和默认的后端 HTTP 设置（*appGatewayBackendHttpSettings*）进行绑定。 创建网关后，可以编辑默认规则的设置或创建新规则。

### <a name="rule-type"></a>规则类型

创建规则时，可以在[*基本*和*基于路径的*之间进行](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#request-routing-rules)选择。

- 如果要将关联侦听器上的所有请求（例如， *<i></i>contoso.com/\*）* 转发到一个后端池，请选择 "基本"。
- 如果要将来自特定 URL 路径的请求路由到特定的后端池，请选择 "基于路径"。 路径模式仅应用于 URL 路径，而不应用于其查询参数。

#### <a name="order-of-processing-rules"></a>处理规则的顺序

对于 v1 SKU，按路径在基于路径的规则的 URL 路径映射中列出的顺序处理传入请求的模式匹配。 如果请求与路径映射中两个或多个路径中的模式匹配，则将匹配首先列出的路径。 请求将转发到与该路径关联的后端。

对于 v2 SKU，完全匹配的优先级高于 URL 路径映射中的路径顺序。 如果请求与两个或更多路径中的模式匹配，则会将请求转发到与与请求完全匹配的路径关联的后端。 如果传入请求中的路径与映射中的任何路径都不完全匹配，则将在基于路径的规则的路径映射顺序列表中处理请求的模式匹配。

### <a name="associated-listener"></a>关联侦听器

将侦听器关联到规则，以便对与侦听器关联的*请求路由规则*进行评估，以确定要将请求路由到的后端池。

### <a name="associated-back-end-pool"></a>关联的后端池

与规则关联的后端池，其中包含为侦听器接收的请求提供服务的后端目标。

 - 对于基本规则，只允许使用一个后端池。 关联侦听器上的所有请求都将转发到该后端池。

 - 对于基于路径的规则，请添加对应于每个 URL 路径的多个后端池。 与输入的 URL 路径匹配的请求将转发到相应的后端池。 另外，添加默认的后端池。 与规则中的任何 URL 路径都不匹配的请求将转发到该池。

### <a name="associated-back-end-http-setting"></a>关联的后端 HTTP 设置

为每个规则添加一个后端 HTTP 设置。 请求使用此设置中指定的端口号、协议和其他信息从应用程序网关路由到后端目标。

对于基本规则，只允许使用一个后端 HTTP 设置。 关联侦听器上的所有请求都通过使用此 HTTP 设置转发到相应的后端目标。

对于基于路径的规则，请添加多个对应于每个 URL 路径的后端 HTTP 设置。 与此设置中的 URL 路径匹配的请求将使用对应于每个 URL 路径的 HTTP 设置转发到相应的后端目标。 另外，添加默认的 HTTP 设置。 与此规则中的任何 URL 路径都不匹配的请求将通过使用默认 HTTP 设置转发到默认后端池。

### <a name="redirection-setting"></a>重定向设置

如果为基本规则配置了重定向，则关联侦听器上的所有请求都将重定向到目标。 这是*全局*重定向。 如果为基于路径的规则配置了重定向，则仅重定向特定站点区域中的请求。 例如， */cart/\** 表示购物车区域。 这是*基于路径的*重定向。

有关重定向的详细信息，请参阅[应用程序网关重定向概述](https://docs.microsoft.com/azure/application-gateway/redirect-overview)。

#### <a name="redirection-type"></a>重定向类型

选择所需的重定向类型：*永久性（301）* 、*临时（307）* 、*找到（302）* 或*查看其他（303）* 。

#### <a name="redirection-target"></a>重定向目标

选择另一个侦听器或外部站点作为重定向目标。

##### <a name="listener"></a>侦听器

选择 "侦听器" 作为重定向目标，以在网关上将流量从一个侦听器重定向到另一个侦听器。 如果要启用 HTTP 到 HTTPS 重定向，则需要此设置。 它将检查传入 HTTP 请求的源侦听器的流量重定向到检查传入 HTTPS 请求的目标侦听器。 你还可以选择在转发到重定向目标的请求中包含来自原始请求的查询字符串和路径。

!["应用程序网关组件" 对话框](./media/configuration-overview/configure-redirection.png)

有关 HTTP 到 HTTPS 重定向的详细信息，请参阅：
- [使用 Azure 门户进行 HTTP 到 HTTPS 的重定向](https://docs.microsoft.com/azure/application-gateway/redirect-http-to-https-portal)
- [使用 PowerShell 进行 HTTP 到 HTTPS 的重定向](https://docs.microsoft.com/azure/application-gateway/redirect-http-to-https-powershell)
- [使用 Azure CLI 进行 HTTP 到 HTTPS 的重定向](https://docs.microsoft.com/azure/application-gateway/redirect-http-to-https-cli)

##### <a name="external-site"></a>外部站点

如果要将与此规则关联的侦听器上的流量重定向到外部站点，请选择 "外部站点"。 您可以选择在转发到重定向目标的请求中包含原始请求中的查询字符串。 不能将路径转发到原始请求中的外部站点。

有关重定向的详细信息，请参阅：
- [使用 PowerShell 将流量重定向到外部站点](https://docs.microsoft.com/azure/application-gateway/redirect-external-site-powershell)
- [使用 CLI 将流量重定向到外部站点](https://docs.microsoft.com/azure/application-gateway/redirect-external-site-cli)

#### <a name="rewrite-the-http-header-setting"></a>重写 HTTP 标头设置

此设置在客户端和后端池之间移动请求和响应数据包时添加、删除或更新 HTTP 请求和响应标头。 有关详细信息，请参阅：

 - [重写 HTTP 标头概述](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers)
 - [配置 HTTP 标头重写](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers-portal)

## <a name="http-settings"></a>HTTP 设置

应用程序网关使用你在此处指定的配置将流量路由到后端服务器。 创建 HTTP 设置之后，必须将其与一个或多个请求路由规则相关联。

### <a name="cookie-based-affinity"></a>基于 Cookie 的相关性

如果要在同一台服务器上保留用户会话，此功能很有用。 通过网关管理的 cookie，应用程序网关可以将来自用户会话的后续流量定向到相同的服务器进行处理。 当在服务器上为用户会话保存会话状态时，这一点非常重要。 如果应用程序无法处理基于 cookie 的关联，则无法使用此功能。 若要使用它，请确保客户端支持 cookie。

### <a name="connection-draining"></a>连接清空

连接排出有助于在计划内服务更新期间正常删除后端池成员。 你可以在创建规则期间将此设置应用到后端池的所有成员。 它确保后端池的所有注销实例都继续维持现有连接，并为可配置的超时提供正在进行的请求，而不会收到任何新的请求或连接。 这种情况的唯一例外是，由于网关托管会话相关性，将请求绑定到 deregistring 实例，并将继续代理到 deregistring 实例。 连接排出适用于从后端池中显式删除的后端实例。

### <a name="protocol"></a>协议

应用程序网关支持将请求路由到后端服务器的 HTTP 和 HTTPS。 如果选择 HTTP，则不会加密到后端服务器的流量。 如果无法接受未加密的通信，请选择 HTTPS。

此设置与侦听器中的 HTTPS 组合支持[端到端 SSL](https://docs.microsoft.com/azure/application-gateway/ssl-overview)。 这样，你就可以安全地将加密的敏感数据传输到后端。 后端池中每个已启用端到端 SSL 的后端服务器都必须配置证书以允许安全通信。

### <a name="port"></a>Port

此设置指定后端服务器侦听来自应用程序网关的流量的端口。 可以配置范围从1到65535的端口。

### <a name="request-timeout"></a>请求超时

此设置是应用程序网关等待接收来自后端服务器的响应的秒数。

### <a name="override-back-end-path"></a>重写后端路径

此设置允许你配置在将请求转发到后端时要使用的可选自定义转发路径。 传入路径中与 "**替代后端路径**" 字段中的自定义路径匹配的任何部分都将复制到转发的路径。 下表显示了此功能的工作原理：

- 当 HTTP 设置附加到基本请求路由规则时：

  | 原始请求  | 重写后端路径 | 请求转发到后端 |
  | ----------------- | --------------------- | ---------------------------- |
  | /home/            | /override            | /override/home/              |
  | /home/secondhome/ | /override            | /override/home/secondhome/   |

- 当 HTTP 设置附加到基于路径的请求路由规则时：

  | 原始请求           | 路径规则       | 重写后端路径 | 请求转发到后端 |
  | -------------------------- | --------------- | --------------------- | ---------------------------- |
  | /pathrule/home/            | pathrule      | /override            | /override/home/              |
  | /pathrule/home/secondhome/ | pathrule      | /override            | /override/home/secondhome/   |
  | /home/                     | pathrule      | /override            | /override/home/              |
  | /home/secondhome/          | pathrule      | /override            | /override/home/secondhome/   |
  | /pathrule/home/            | /pathrule/home* | /override            | /override                   |
  | /pathrule/home/secondhome/ | /pathrule/home* | /override            | /override/secondhome/        |
  | pathrule                 | pathrule      | /override            | /override                   |

### <a name="use-for-app-service"></a>用于应用服务

这是一个仅限 UI 的快捷方式，可选择 Azure App Service 后端所需的两个设置。 它可**从后端地址中选取主机名**，并会创建新的自定义探测（如果尚未这样做）。 （有关详细信息，请参阅本文的[从后端地址中选取主机名](#pick)部分。）将创建新的探测，并从后端成员的地址中选取探测标头。

### <a name="use-custom-probe"></a>使用自定义探测

此设置将[自定义探测](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#custom-health-probe)与 HTTP 设置关联。 只能将一个自定义探测与一个 HTTP 设置关联。 如果未显式关联自定义探测，则使用[默认探测器](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#default-health-probe-settings)来监视后端的运行状况。 建议创建自定义探测，以便更好地控制后端的运行状况监视。

> [!NOTE]
> 自定义探测不会监视后端池的运行状况，除非相应的 HTTP 设置与侦听器显式关联。

### <a id="pick"/></a>从后端地址中选取主机名

此功能会将请求中的*主机*标头动态地设置为后端池的主机名。 它使用 IP 地址或 FQDN。

当后端的域名不同于应用程序网关的 DNS 名称，并且后端依赖特定的主机标头解析为正确的终结点时，此功能可帮助。

例如，多租户服务作为后端。 应用服务是一种多租户服务，它使用带有单个 IP 地址的共享空间。 因此，只能通过在自定义域设置中配置的主机名来访问应用服务。

默认情况下，自定义域名为*example.azurewebsites.net*。 若要使用应用程序网关访问应用服务，通过未在应用服务中显式注册的主机名或通过应用程序网关的 FQDN，你可以将原始请求中的主机名重写到应用服务的主机名。 为此，请启用 "**从后端地址中选取主机名称**" 设置。

对于其现有自定义 DNS 名称映射到应用服务的自定义域，无需启用此设置。

> [!NOTE]
> 此设置不是应用服务环境专用部署所必需的。

### <a name="host-name-override"></a>主机名替代

此功能将应用程序网关上传入请求中的*主机*标头替换为指定的主机名。

例如，如果在 "**主机名**" 设置中指定了*www.contoso.com* ，则在将请求转发到后端服务器时，原始请求 * https://appgw.eastus.cloudapp.azure.com/path1 将更改为 * https://www.contoso.com/path1 。

## <a name="back-end-pool"></a>后端池

可以将后端池指向四种类型的后端成员：特定虚拟机、虚拟机规模集、IP 地址/FQDN 或应用服务。 每个后端池可以指向同一类型的多个成员。 不支持指向同一后端池中不同类型的成员。

创建后端池后，必须将其与一个或多个请求路由规则相关联。 还必须为应用程序网关上的每个后端池配置运行状况探测。 满足请求路由规则条件时，应用程序网关会将流量转发到相应后端池中的正常服务器（由运行状况探测决定）。

## <a name="health-probes"></a>运行状况探测

默认情况下，应用程序网关监视其后端中所有资源的运行状况。 但我们强烈建议为每个后端 HTTP 设置创建自定义探测，以便更好地控制运行状况监视。 若要了解如何配置自定义探测，请参阅[自定义运行状况探测设置](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#custom-health-probe-settings)。

> [!NOTE]
> 创建自定义运行状况探测后，需要将其关联到后端 HTTP 设置。 自定义探测不会监视后端池的运行状况，除非相应的 HTTP 设置与使用规则的侦听器显式关联。

## <a name="next-steps"></a>后续步骤

现在，你已了解应用程序网关组件，你可以：

- [在 Azure 门户中创建应用程序网关](quick-create-portal.md)
- [使用 PowerShell 创建应用程序网关](quick-create-powershell.md)
- [使用 Azure CLI 创建应用程序网关](quick-create-cli.md)
