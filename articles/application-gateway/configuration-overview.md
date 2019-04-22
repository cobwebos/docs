---
title: Azure 应用程序网关配置概述
description: 本文介绍如何配置 Azure 应用程序网关的组件
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 03/20/2019
ms.author: absha
ms.openlocfilehash: 40c5444a54f4e483a9dcacb958c18f66da45019a
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2019
ms.locfileid: "58906117"
---
# <a name="application-gateway-configuration-overview"></a>应用程序网关配置概述

Azure 应用程序网关包含多个组件，您可以配置以各种方式对不同的方案。 本文介绍如何配置每个组件。

![应用程序网关组件流图表](./media/configuration-overview/configuration-overview1.png)

此图阐释了具有三个侦听器的应用程序。 前两个多站点侦听器`http://acme.com/*`和`http://fabrikam.com/*`分别。 同时在端口 80 上侦听。 第三个是具有端到端安全套接字层 (SSL) 终止的基本侦听器。


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>必备组件

### <a name="azure-virtual-network-and-dedicated-subnet"></a>Azure 虚拟网络和专用子网

应用程序网关是专用虚拟网络中的部署。 在虚拟网络中的专用子网是必需的应用程序网关。 您可以指定应用程序网关部署的多个实例的子网中。 您还可以部署其他应用程序网关的子网中。 但不能部署应用程序网关子网中的任何其他资源。

> [!NOTE]
> 不能在同一个子网上混合 Standard_v2 和标准的 Azure 应用程序网关。

#### <a name="size-of-the-subnet"></a>子网的大小

如果专用前端 IP 配置，应用程序网关使用每个实例，1 个专用 IP 地址并使用另一个专用 IP 地址。

Azure 也会保留供内部使用的每个子网中的 5 个 IP 地址： 第一个 4 和最后一个 IP 地址。 例如，考虑使用没有专用前端 IP 的 15 个应用程序网关实例。 为此子网需要至少 20 个 IP 地址：用于内部使用和应用程序网关实例的 15 5。 因此，您需要/27 子网大小或更大。

请考虑具有 27 的应用程序网关实例和一个 IP 地址的专用前端 ip 子网。 在这种情况下，需要 33 的 IP 地址：对于应用程序网关实例，1 表示专用前端，供内部使用的 5 27。 因此，您需要 / 26 大小或更大子网。

我们建议使用至少/28 子网大小。 此大小提供 11 可用的 IP 地址。 如果应用程序负载要求超过 10 个 IP 地址，请考虑/27 或/26 子网大小。

#### <a name="network-security-groups-on-the-application-gateway-subnet"></a>应用程序网关子网的网络安全组

应用程序网关支持网络安全组 (Nsg)。 但有多个限制：

- 必须包括 v2 sku 上的应用程序网关 v1 sku 端口 65503-65534 和端口 65200 到 65535 之间的传入流量的异常。 此端口范围是进行 Azure 基础结构通信所必需的。 （已锁定） 受 Azure 证书保护这些端口。 外部实体，包括这些网关，客户不能启动这些终结点，而无需在位置中的相应证书上的更改。

- 不能阻止出站 Internet 连接。 在 NSG 中的默认出站规则允许 internet 连接。 建议：

  - 不删除默认出站规则。
  - 不创建其他拒绝出站 internet 连接的出站规则。

- 从流量**AzureLoadBalancer**必须允许标记。

##### <a name="whitelist-application-gateway-access-to-a-few-source-ips"></a>将应用程序网关列入允许列表以便能够访问一些源 IP

对于此方案，应用程序网关子网使用 Nsg。 按此优先顺序的子网上放置以下限制：

1. 允许来自源 IP/IP 范围的传入流量。
2. 允许所有来源的传入请求到达端口 65503-65534[后端运行状况通信](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)。 此端口范围是进行 Azure 基础结构通信所必需的。 （已锁定） 受 Azure 证书保护这些端口。 而无需在位置的适当证书，外部实体不能启动这些终结点上的更改。
3. 允许传入 Azure 负载均衡器探测 (*AzureLoadBalancer*标记) 和入站虚拟网络流量 (*VirtualNetwork*标记) 上[网络安全组](https://docs.microsoft.com/azure/virtual-network/security-overview)。
4. 通过使用全部拒绝规则来阻止其他所有传入流量。
5. 允许所有目的地的 Internet 出站流量。

#### <a name="user-defined-routes-supported-on-the-application-gateway-subnet"></a>应用程序网关子网支持用户定义的路由

对于 v1 SKU 中，用户定义的路由 (Udr) 支持应用程序网关子网，只要它们不会改变的端到端请求/响应通信。 例如，可以设置在应用程序网关子网 UDR 来指向数据包检查防火墙设备。 但是，您必须确保将数据包可以完成检查后进入其预期的目标。 如果不这样做可能会导致不正确的运行状况探测或流量路由行为。 这包括了解到的路由或通过虚拟网络中的 Azure ExpressRoute 或 VPN 网关传播的默认 0.0.0.0/0 路由。

V2 sku，不支持应用程序网关子网 Udr。 有关详细信息，请参阅[自动缩放和应用程序网关的区域冗余](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant#known-issues-and-limitations)。

> [!NOTE]
> 使用应用程序网关子网上的 Udr 将导致中的运行状况状态[后端运行状况视图](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#back-end-health)显示为"未知。 它还会导致生成的应用程序网关日志和度量值失败。 我们建议不要在应用程序网关子网都使用 Udr，以便可以查看后端运行状况、 日志和指标。

## <a name="front-end-ip"></a>前端 IP

你可以配置应用程序网关具有一个公共 IP 地址、 专用 IP 地址，或两者。 托管后端，该客户端必须通过面向 internet 的虚拟 IP (VIP) 通过 internet 访问时需要公共 IP。 

公共 IP 不需要不向 internet 公开的内部终结点。 这称作*内部的负载均衡器*(ILB) 终结点。 ILB 位于用于内部业务线应用程序不会公开到 internet 的应用程序网关。 它也是有用的服务和安全边界内的多层应用程序中的层，不向 internet 公开但需要执行轮循机制负载分散、 会话粘性或 SSL 终止。

支持一个公共 IP 地址或 1 个专用 IP 地址。 创建应用程序网关，请选择前端 IP。

- 针对公共 IP，可以创建新的公共 IP 地址，或在与应用程序网关位于同一位置中使用现有的公共 IP。 如果您创建新的公共 IP，你选择的 IP 地址类型 （静态或动态） 以后无法更改。 有关详细信息，请参阅[静态与动态公共 IP 地址](https://docs.microsoft.com/en-us/azure/application-gateway/application-gateway-components#static-vs-dynamic-public-ip-address)。

- 对于专用 IP，可以指定从在其中创建应用程序网关的子网的专用 IP 地址。 如果您不指定，会自动从子网中选择任意 IP 地址。 有关详细信息，请参阅[具有内部负载均衡器创建的应用程序网关](https://docs.microsoft.com/azure/application-gateway/application-gateway-ilb-arm)。

前端 IP 地址将关联到*侦听器*，它会检查上的前端 IP 的传入请求。

## <a name="listeners"></a>侦听器

侦听器是通过使用端口、 协议、 主机和 IP 地址检查传入的连接请求的逻辑实体。 在配置侦听器时，必须为这些网关的传入请求中的相应值匹配输入值。

当使用 Azure 门户创建应用程序网关时，你还通过侦听器选择协议和端口创建默认侦听器。 您可以选择是否要启用对侦听程序 HTTP2 支持。 创建应用程序网关后，可以编辑该默认侦听器的设置 (*appGatewayHttpListener*/*appGatewayHttpsListener*) 或创建新的侦听器。

### <a name="listener-type"></a>侦听器类型

当您创建一个新的侦听器时，请选择之间[*基本*并*多站点*](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#types-of-listeners)。

- 如果您正在托管单个站点应用程序网关后的，选择基本。 了解[如何创建基本侦听器的应用程序网关](https://docs.microsoft.com/azure/application-gateway/quick-create-portal)。

- 如果同一应用程序网关实例上配置多个 web 应用程序或同一父域的多个子域，请选择多站点侦听器。 多站点侦听器，你还必须输入主机名。 这是因为，应用程序网关需要使用 HTTP 1.1 主机标头才能在相同的公共 IP 地址和端口上托管多个网站。

#### <a name="order-of-processing-listeners"></a>侦听器的处理顺序

V1 sku 中，按所列的顺序处理侦听器。 如果基本侦听器与匹配的传入请求，侦听器将先处理该请求。 因此，将配置多站点侦听器之前基本侦听器以确保流量将路由到正确的后端。

V2 sku，基本侦听器之前处理多站点侦听器。

### <a name="front-end-ip"></a>前端 IP

选择你打算将与此侦听器相关联的前端 IP 地址。 侦听器将侦听此 IP 上的传入请求。

### <a name="front-end-port"></a>前端端口

选择前端的端口。 选择现有端口或创建一个新。 选择的任何值[允许的端口范围](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#ports)。 可以使用的已知端口，例如 80 和 443，不仅适用于任何允许自定义端口。 一个端口可用于面向公共的侦听器或面向专用的侦听器。

### <a name="protocol"></a>协议

选择 HTTP 或 HTTPS:

- 如果选择 HTTP，则客户端和应用程序网关之间的通信未加密。

- 如果您想选择 HTTPS [SSL 终止](https://docs.microsoft.com/azure/application-gateway/overview#secure-sockets-layer-ssl-terminationl)或[端到端 SSL 加密](https://docs.microsoft.com/azure/application-gateway/ssl-overview)。 客户端和应用程序网关之间的通信进行加密。 并在应用程序网关上终止 SSL 连接。 如果你想的端到端 SSL 加密，必须选择 HTTPS 并配置**后端 HTTP**设置。 这可确保从到的后端应用程序网关传输时重新加密流量。

若要配置 SSL 终止和端到端 SSL 加密，必须将证书添加到侦听器以启用应用程序网关，以派生一个对称密钥中。 这是由 SSL 协议规范中进行指定。 对称密钥用于加密和解密发送到网关的流量。 网关证书必须采用个人信息交换 (PFX) 格式。 此格式允许您导出网关用来加密和解密流量的专用密钥。

#### <a name="supported-certificates"></a>支持的证书

请参阅[支持用于 SSL 终止的证书](https://docs.microsoft.com/azure/application-gateway/ssl-overview#certificates-supported-for-ssl-termination)。

### <a name="additional-protocol-support"></a>其他协议支持

#### <a name="http2-support"></a>HTTP2 支持

HTTP/2 协议支持可供连接到应用程序网关侦听器的客户端。 通过 HTTP/1.1 到后端服务器池的通信。 默认情况下，HTTP/2 支持处于禁用状态。 以下 Azure PowerShell 代码片段演示如何启用此功能：

```azurepowershell
$gw = Get-AzApplicationGateway -Name test -ResourceGroupName hm

$gw.EnableHttp2 = $true

Set-AzApplicationGateway -ApplicationGateway $gw
```

#### <a name="websocket-support"></a>WebSocket 支持

默认情况下启用 WebSocket 支持。 没有用户可配置的设置来启用或禁用它。 可对 HTTP 和 HTTPS 侦听器使用 WebSocket。

### <a name="custom-error-pages"></a>自定义错误页

您可以定义在全局级别或侦听器级的自定义错误。 但从 Azure 门户中创建全局级别的自定义错误页当前不支持。 可以在侦听器级别配置 403 web 应用程序防火墙错误消息的自定义错误页或 502 维护页。 此外必须指定给定的错误状态代码的可公开访问的 blob URL。 有关详细信息，请参阅[创建应用程序网关自定义错误页](https://docs.microsoft.com/azure/application-gateway/custom-error)。

![应用程序网关错误代码](https://docs.microsoft.com/azure/application-gateway/media/custom-error/ag-error-codes.png)

若要配置的全局自定义错误页，请参阅[Azure PowerShell 配置](https://docs.microsoft.com/azure/application-gateway/custom-error#azure-powershell-configuration)。

### <a name="ssl-policy"></a>SSL 策略

您可以集中式 SSL 证书管理并降低开销的后端服务器场的加密解密。 集中式的 SSL 处理还允许你指定适合于您的安全要求的中央 SSL 策略。 你可以选择*默认*，*预定义*，或*自定义*SSL 策略。

配置 SSL 策略来控制 SSL 协议版本。 你可以配置为拒绝 TLS1.0、 TLS1.1 和 TLS1.2 的应用程序网关。 默认情况下，SSL 2.0 和 3.0 被禁用，无法配置。 有关详细信息，请参阅[应用程序网关 SSL 策略概述](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-policy-overview)。

创建侦听器后，您将其与关联的请求路由规则。 该规则确定如何将侦听器接收的请求路由到后端。

## <a name="request-routing-rules"></a>请求路由规则

使用 Azure 门户创建应用程序网关时创建默认规则 (*rule1*)。 此规则将绑定的默认侦听器 (*appGatewayHttpListener*) 与默认后端池 (*appGatewayBackendPool*) 和默认后端 HTTP 设置 (*appGatewayBackendHttpSettings*)。 创建网关后，可以编辑默认规则的设置或创建新规则。

### <a name="rule-type"></a>规则类型

创建规则，可以选择之间[*基本*并*路径基于*](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#request-routing-rule)。

- 如果你想要关联的侦听器上的所有请求都转发，请选择 basic (例如，*博客<i></i>.contoso.com/\*)* 到单个后端池。
- 选择基于路径的-如果你想要将请求路由从特定的 URL 路径到特定的后端池。 路径模式仅应用到 URL 的路径，而不应用到该 URL 的查询参数。

#### <a name="order-of-processing-rules"></a>规则的处理顺序

V1 sku，模式匹配的传入请求的处理路径基于路径的规则的 URL 路径映射中列出的顺序。 如果某个请求与匹配路径映射中的两个或多个路径中的模式，列出的路径首先将匹配。 并将请求转发到后端与该路径相关联。

对于 v2 SKU，完全匹配是优先级高于路径 URL 路径映射中的顺序。 如果某个请求与匹配两个或多个路径中的模式，该请求转发到后端使用与请求完全匹配的路径关联。 如果传入请求中的路径并不完全匹配在映射中的任何路径，该请求的模式匹配基于路径的规则路径映射顺序列表中进行处理。

### <a name="associated-listener"></a>关联的侦听器

将关联到规则的侦听器，以便*请求路由规则*关联侦听器的计算结果确定要路由到请求的后端池。

### <a name="associated-back-end-pool"></a>关联的后端池

将关联到该规则包含为该侦听器接收的请求提供服务的后端目标的后端池。

 - 对于基本规则，允许一个后端池。 关联的侦听器上的所有请求将都转发到该后端池。

 - 对于基于路径的规则，将添加到每个 URL 路径的相对应的多个后端池。 匹配输入的 URL 路径的请求将转发到相应的后端池。 此外，添加默认的后端池。 不匹配任何规则中的 URL 路径的请求转发到该池。

### <a name="associated-back-end-http-setting"></a>关联的后端 HTTP 设置

添加每个规则的后端 HTTP 设置。 请求路由到后端目标应用程序网关从使用的端口号、 协议和此设置中指定的其他信息。

对于基本规则，允许只有一个后端 HTTP 设置。 关联的侦听器上的所有请求使用此 HTTP 设置都转发到相应的后端目标。

添加每个规则的后端 HTTP 设置。 请求路由到后端目标应用程序网关从使用的端口号、 协议和此设置中指定其他信息。

对于基本规则，允许只有一个后端 HTTP 设置。 关联的侦听器上的所有请求使用此 HTTP 设置都转发到相应的后端目标。

对于基于路径的规则，将添加到每个 URL 路径的相对应的多个后端 HTTP 设置。 此设置中的 URL 路径匹配的请求使用对应于每个 URL 路径的 HTTP 设置转发到相应的后端目标。 此外，添加默认 HTTP 设置。 使用默认 HTTP 设置情况下，不匹配此规则中的任何 URL 路径的请求转发到的默认后端池。

### <a name="redirection-setting"></a>重定向设置

如果重定向配置为基本规则，关联的侦听器上的所有请求都重都定向到目标。 这是*全局*重定向。 如果重定向配置为基于路径的规则，只有在特定站点区域中的请求将重定向。 一个示例是由表示的购物车区域 */cart/\**。 这是*基于路径的*重定向。

有关重定向的详细信息，请参阅[应用程序网关重定向概述](https://docs.microsoft.com/azure/application-gateway/redirect-overview)。

#### <a name="redirection-type"></a>重定向类型

选择所需的重定向的类型：*Permanent(301)*， *Temporary(307)*， *Found(302)*，或*请参阅 other(303)*。

#### <a name="redirection-target"></a>重定向目标

选择作为重定向目标的另一个侦听器或外部站点。

##### <a name="listener"></a>侦听器

选择作为重定向目标为另一个网关从一个侦听器的流量重定向的侦听器。 当你想要启用 HTTP 到 HTTPS 重定向时，此设置是必需的。 它将从检查传入的 HTTP 请求到目标侦听器，用于检查传入 HTTPS 请求的源侦听器的流量重定向。 您还可以选择转发到重定向目标在请求中包含的查询字符串和原始请求中的路径。

![应用程序网关组件对话框](./media/configuration-overview/configure-redirection.png)

有关 HTTP 到 HTTPS 重定向的详细信息，请参阅：
- [使用 Azure 门户的 HTTP 到 HTTP 重定向](https://docs.microsoft.com/azure/application-gateway/redirect-http-to-https-portal)
- [使用 PowerShell 的 HTTP 到 HTTP 重定向](https://docs.microsoft.com/azure/application-gateway/redirect-http-to-https-powershell)
- [使用 Azure CLI 的 HTTP 到 HTTP 重定向](https://docs.microsoft.com/azure/application-gateway/redirect-http-to-https-cli)

##### <a name="external-site"></a>外部站点

如果你想要将与此规则应用于外部站点关联的侦听器上的流量重定向，请选择外部站点。 您可以选择转发到重定向目标在请求中包含来自原始请求的查询字符串。 不能转发到原始请求中是外部站点的路径。

有关重定向的详细信息，请参阅：
- [通过使用 PowerShell 重流量定向到外部站点](https://docs.microsoft.com/azure/application-gateway/redirect-external-site-powershell)
- [通过使用 CLI 重流量定向到外部站点](https://docs.microsoft.com/azure/application-gateway/redirect-external-site-cli)

#### <a name="rewrite-the-http-header-setting"></a>重写 HTTP 标头设置

此设置将添加、 删除或更新时请求的 HTTP 请求和响应头和响应数据包的客户端和后端池之间移动。 您只能配置此功能通过 PowerShell。 Azure 门户和 CLI 支持尚不可用。 有关详细信息，请参阅：

 - [重写 HTTP 标头概述](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers)
 - [配置 HTTP 标头重写](https://docs.microsoft.com/azure/application-gateway/add-http-header-rewrite-rule-powershell#specify-your-http-header-rewrite-rule-configuration)

## <a name="http-settings"></a>HTTP 设置

应用程序网关将流量路由到后端服务器使用此处指定的配置。 创建的 HTTP 设置后，你必须将其与一个或多个请求路由规则。

### <a name="cookie-based-affinity"></a>基于 Cookie 的相关性

需要在同一台服务器上保留用户会话时，此功能非常有用。 网关管理的 cookie 可让应用程序网关直接的后续流量来自用户会话到同一个服务器进行处理。 会话状态保存在本地服务器上为用户会话时，这很重要。 如果应用程序不能处理基于 cookie 的相关性，则无法使用此功能。 若要使用它，请确保客户端支持 cookie。

### <a name="connection-draining"></a>连接清空

连接排出可帮助你在计划内的服务更新过程正常删除后端池成员。 在规则创建期间，可以到后端池的所有成员应用此设置。 它可以确保所有取消注册实例的后端池未收到任何新请求。 同时，现有的请求将能够配置的时间限制内完成。 连接排出适用于后端实例的 API 调用从后端池显式删除。 它也适用于报告为后端实例*不正常*由运行状况探测。

### <a name="protocol"></a>协议

应用程序网关支持 HTTP 和 HTTPS 请求路由到后端服务器。 如果选择 HTTP，到后端服务器的通信未加密。 如果未加密的通信不可接受，请选择 HTTPS。

此设置侦听器支持结合使用 HTTPS[端到端 SSL](https://docs.microsoft.com/azure/application-gateway/ssl-overview)。 这允许你安全地传输到后端加密的敏感数据。 已启用端到端 SSL 的后端池中每个后端服务器必须配置证书，以便能够允许进行安全通信。

### <a name="port"></a>端口

此设置指定的后端服务器在其中侦听来自应用程序网关的流量的端口。 可以配置 1 到 65535 的端口号。

### <a name="request-timeout"></a>请求超时

此设置是应用程序网关从后端池收到响应，它将返回"连接超时"错误消息之前等待的秒数。

### <a name="override-back-end-path"></a>重写后端路径

此设置允许你配置将请求转发到后端时要使用的可选自定义转发路径。 传入路径相匹配的自定义路径中的任何一部分**重写后端路径**字段复制到转发路径。 下表显示了此功能的工作原理：

- 在 HTTP 设置附加到请求路由的基本规则：

  | 原始请求  | 重写后端路径 | 请求转发到后端 |
  | ----------------- | --------------------- | ---------------------------- |
  | /home/            | /override/            | /override/home/              |
  | /home/secondhome/ | /override/            | /override/home/secondhome/   |

- 当 HTTP 设置附加到基于路径的请求路由规则：

  | 原始请求           | 路径规则       | 重写后端路径 | 请求转发到后端 |
  | -------------------------- | --------------- | --------------------- | ---------------------------- |
  | /pathrule/home/            | /pathrule*      | /override/            | /override/home/              |
  | /pathrule/home/secondhome/ | /pathrule*      | /override/            | /override/home/secondhome/   |
  | /home/                     | /pathrule*      | /override/            | /override/home/              |
  | /home/secondhome/          | /pathrule*      | /override/            | /override/home/secondhome/   |
  | /pathrule/home/            | /pathrule/home* | /override/            | /override/                   |
  | /pathrule/home/secondhome/ | /pathrule/home* | /override/            | /override/secondhome/        |

### <a name="use-for-app-service"></a>对应用服务的使用

这是选择 Azure 应用服务后端所需的两个设置的用户界面快捷方式。 它使**选取主机名从后端地址**，而且它会创建新的自定义探测。 (有关详细信息，请参阅[选取主机名从后端地址](#pick)设置部分的这篇文章。)创建新的探测，并从后端成员的地址选取探测标头。

### <a name="use-custom-probe"></a>使用自定义探测

此设置将相关联[自定义探测](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#custom-health-probe)与 HTTP 设置。 只能将一个自定义探测关联到某个 HTTP 设置。 如果不显式相关联的自定义探测[默认探测](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#default-health-probe-settings)用于监视后端的运行状况。 我们建议您创建更好地控制应用后端运行状况监视的自定义探测。

> [!NOTE]
> 自定义探测不监视后端池的运行状况，除非相应的 HTTP 设置是显式与侦听器相关联。

### <a id="pick"/></a>选择从后端地址的主机名

此功能动态地设置*主机*中对后端池的主机名的请求标头。 它使用的 IP 地址或 FQDN。

此功能可帮助在后端的域名不同于应用程序网关的 DNS 名称后, 端依赖于特定的主机标头或服务器名称指示 (SNI) 扩展将解析为正确的终结点时。

示例用例是多租户服务作为后端。 应用服务是一个共享的空间使用单个 IP 地址的多租户服务。 因此，仅可以通过在自定义域设置中配置的主机名访问应用服务。

默认情况下，是自定义域名*example.azurewebsites。<i></i>net*。 若要使用的应用程序网关通过在应用服务中未显式注册的主机名或应用程序网关的 FQDN 访问你的应用服务，重写中对应用服务的主机名的原始请求的主机名。 若要执行此操作，启用**选取主机名从后端地址**设置。

其现有的自定义 DNS 名称映射到应用服务为自定义域，你无需启用此设置。

> [!NOTE]
> 此设置不需要为 powerapps，应用服务环境是专门的部署。

### <a name="host-name-override"></a>主机名替代

此功能取代了*主机*上指定的主机名的应用程序网关传入的请求标头中。

例如，如果*www.contoso<i></i>.com*中指定**主机名**设置，原始请求*https:/<i></i>/appgw.eastus.cloudapp.net/path1*更改为*https:/<i></i>/www.contoso.com/path1*时将请求转发到后端服务器。

## <a name="back-end-pool"></a>后端池

可以将后端池指向四种类型的后端成员： 特定的虚拟机、 虚拟机规模集、 IP 地址/FQDN 或应用服务。 每个后端池可以指向同一类型的多个成员。 不支持指向同一后端池中的不同类型的成员。

在创建后端池后，你必须将其与一个或多个请求路由规则。 此外必须在应用程序网关配置为每个后端池的运行状况探测。 当满足将请求路由规则条件时，应用程序网关将流量转发到相应的后端池中正常运行的服务器 （作为由运行状况探测确定）。

## <a name="health-probes"></a>运行状况探测

默认情况下，应用程序网关监视其后端中的所有资源的运行状况。 但我们强烈建议创建每个后端 HTTP 设置，以获得更好地控制运行状况监视的自定义探测。 若要了解如何配置自定义探测，请参阅[自定义运行状况探测设置](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#custom-health-probe-settings)。

> [!NOTE]
> 创建自定义运行状况探测后，您需要将其关联到后端 HTTP 设置。 自定义探测不会监视后端池的运行状况，除非相应的 HTTP 设置是显式与侦听器相关联。

## <a name="next-steps"></a>后续步骤

既然您已经了解应用程序网关组件，你可以：

- [在 Azure 门户中创建应用程序网关](quick-create-portal.md)
- [使用 PowerShell 创建应用程序网关](quick-create-powershell.md)
- [使用 Azure CLI 创建应用程序网关](quick-create-cli.md)
