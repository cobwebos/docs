---
title: Azure 应用程序网关配置概述
description: 本文介绍如何配置 Azure 应用程序网关的组件
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 03/24/2020
ms.author: absha
ms.openlocfilehash: 046946bb9d3ce1ae86d49409d024c862d2edb982
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82856058"
---
# <a name="application-gateway-configuration-overview"></a>应用程序网关配置概述

Azure 应用程序网关由多个组件构成，可根据不同的方案以不同的方式配置这些组件。 本文将会介绍如何配置每个组件。

![应用程序网关组件流程图](./media/configuration-overview/configuration-overview1.png)

此图演示了包含三个侦听器的应用程序。 前两个侦听器是分别用于 `http://acme.com/*` 和 `http://fabrikam.com/*` 的多站点侦听器， 两者在端口 80 上侦听。 第三个侦听器是支持端到端传输层安全性 (TLS) 终止（前称为“安全套接字层 (SSL) 终止”）的基本侦听器。


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>先决条件

### <a name="azure-virtual-network-and-dedicated-subnet"></a>Azure 虚拟网络和专用子网

应用程序网关是虚拟网络中的专用部署。 需要在虚拟网络中为应用程序网关配置一个专用子网。 在子网中，可以创建给定应用程序网关部署的多个实例。 还可以在该子网中部署其他应用程序网关。 但不能在应用程序网关子网中部署其他任何资源。

> [!NOTE]
> 不能在同一子网中混合使用 Standard_v2 和 Standard Azure 应用程序网关。

#### <a name="size-of-the-subnet"></a>子网的大小

如果配置了专用前端 IP，则应用程序网关将使用每个实例的 1 个专用 IP 地址，以及另一个专用 IP 地址。

另外，Azure 会在每个子网中保留 5 个 IP 地址供内部使用：前 4 个 IP 地址和最后一个 IP 地址。 例如，假设有 15 个应用程序网关实例没有专用前端 IP。 至少需要为此子网提供 20 个 IP 地址：5 个 IP 地址供内部使用，15 个 IP 地址供应用程序网关实例使用。 因此，需要 /27 或更大的子网大小。

假设某个子网包含 27 个应用程序网关实例，并且包含一个用作专用前端 IP 的 IP 地址。 在这种情况下，需要 33 个 IP 地址：27 个 IP 地址用于应用程序网关实例，1 个 IP 地址用于专用前端，5 个 IP 地址供内部使用。 因此，需要 /26 或更大的子网大小。

我们建议至少使用 /28 子网大小。 这种大小可以提供 11 个可用的 IP 地址。 如果应用程序负载需要 10 个以上的应用程序网关实例，请考虑 /27 或 /26 子网大小。

#### <a name="network-security-groups-on-the-application-gateway-subnet"></a>应用程序网关子网中的网络安全组

应用程序网关支持网络安全组 (NSG)。 但是，存在一些限制：

- 对于应用程序网关 v1 SKU，必须允许 TCP 端口 65503-65534 上的传入 Internet 流量，对于目标子网为 **Any** 且源为 **GatewayManager** 服务标记的 v2 SKU，必须允许 TCP 端口 65200-65535 上的传入 Internet 流量。 此端口范围是进行 Azure 基础结构通信所必需的。 这些端口受 Azure 证书的保护（处于锁定状态）。 外部实体（包括这些网关的客户）无法在这些终结点上通信。

- 不能阻止出站 Internet 连接。 NSG 中的默认出站规则允许 Internet 连接。 建议：

  - 不要删除默认出站规则。
  - 不要创建拒绝任何出站连接的其他出站规则。

- 必须允许来自 **AzureLoadBalancer** 标记的流量。

#### <a name="allow-application-gateway-access-to-a-few-source-ips"></a>允许应用程序网关访问一些源 IP

对于此方案，请在应用程序网关子网中使用 NSG。 按以下优先顺序对子网施加以下限制：

1. 允许来自源 IP 或 IP 范围的传入流量，其目标为整个应用程序网关子网地址范围，目标端口为入站访问端口，例如，使用端口 80 进行 HTTP 访问。
2. 允许特定的传入请求，这些请求来自采用 **GatewayManager** 服务标记的源，其目标为“任意”  ，目标端口为 65503-65534（适用于应用程序网关 v1 SKU）或 65200-65535（适用于 v2 SKU），可以进行[后端运行状况通信](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)。 此端口范围是进行 Azure 基础结构通信所必需的。 这些端口受 Azure 证书的保护（处于锁定状态）。 如果没有适当的证书，外部实体将无法对这些终结点做出任何更改。
3. 允许[网络安全组](https://docs.microsoft.com/azure/virtual-network/security-overview)中的传入 Azure 负载均衡器探测（*AzureLoadBalancer* 标记）和入站虚拟网络流量（*VirtualNetwork* 标记）。
4. 使用“全部拒绝”规则阻止其他所有传入流量。
5. 允许发往 Internet 的所有目标的出站流量。

#### <a name="user-defined-routes-supported-on-the-application-gateway-subnet"></a>应用程序网关子网支持用户定义的路由

> [!IMPORTANT]
> 在应用程序网关子网中使用 UDR 可能会导致[后端运行状况视图](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#back-end-health)中的运行状态显示为“未知”。  此外，可能还会导致应用程序网关日志和指标生成失败。 建议不要在应用程序网关子网中使用 UDR，以便能够查看后端运行状况、日志和指标。

- **v1**

   使用 v1 SKU 时，只要用户定义的路由 (UDR) 未更改端到端请求/响应通信，则应用程序网关子网就会支持这些 UDR。 例如，可以在应用程序网关子网中设置一个指向防火墙设备的、用于检查数据包的 UDR。 但是，必须确保数据包在检查后可以访问其预期目标。 否则，可能会导致不正确的运行状况探测或流量路由行为。 这包括已探测到的路由，或者通过 Azure ExpressRoute 或 VPN 网关在虚拟网络中传播的默认 0.0.0.0/0 路由。

- **v2**

   v2 SKU 存在支持和不支持的方案：

   **v2 支持的方案**
   > [!WARNING]
   > 错误配置路由表可能会导致应用程序网关 v2 中出现非对称路由。 确保所有管理平面/控制平面流量直接发送到 Internet，且不通过虚拟设备发送。 日志和指标也可能会受影响。


  **场景 1**：使用 UDR 禁用向应用程序网关子网进行边界网关协议 (BGP) 路由传播

   有时，默认网关路由 (0.0.0.0/0) 会通过与应用程序网关虚拟网络关联的 ExpressRoute 或 VPN 网关进行播发。 这会中断管理平面流量，因此需要 Internet 的直接路径。 在这种情况下，可以使用 UDR 来禁用 BGP 路由传播。 

   若要禁用 BGP 路由传播，请使用以下步骤：

   1. 在 Azure 中创建一个“路由表”资源。
   2. 禁用“虚拟网络网关路由传播”参数。  
   3. 将路由表关联到相应的子网。 

   为此方案启用 UDR 不应会破坏任何现有设置。

  **场景 2**：使用 UDR 将 0.0.0.0/0 定向到 Internet

   可以创建一个 UDR，用于将 0.0.0.0/0 流量直接发送到 Internet。 

  **方案 3**： UDR For Azure Kubernetes Service with kubenet

  如果将 kubenet 与 Azure Kubernetes 服务（AKS）和应用程序网关入口控制器（AGIC）结合使用，则需要一个路由表，以允许从应用程序网关发送到 pod 的流量路由到正确的节点。 如果使用 Azure CNI，则不需要这样做。 

  若要使用路由表来允许 kubenet 工作，请执行以下步骤：

  1. 中转到由 AKS 创建的资源组（资源组的名称应以 "MC_" 开头）
  2. 在该资源组中查找由 AKS 创建的路由表。 应填写路由表，其中包含以下信息：
     - 地址前缀应是要在 AKS 中访问的 pod 的 IP 范围。 
     - 下一跃点类型应为虚拟设备。 
     - 下一个跃点地址应是托管 pod 的节点的 IP 地址。
  3. 将此路由表关联到应用程序网关子网。 
    
  **v2 不支持的方案**

  **方案 1**：虚拟设备的 UDR

  任何需要通过任何虚拟设备重定向 0.0.0.0/0、中心/辐射虚拟网络或本地（强制隧道）的情况下，V2 都不支持此方案。

## <a name="front-end-ip"></a>前端 IP

可将应用程序网关配置为使用公共 IP 地址和/或专用 IP 地址。 托管需要由客户端在 Internet 中通过面向 Internet 的虚拟 IP (VIP) 访问的后端时，必须使用公共 IP。 

不向 Internet 公开的内部终结点不需要公共 IP。 该终结点称为内部负载均衡器 (ILB) 终结点或专用前端 IP。** 应用程序网关 ILB 适合用于不向 Internet 公开的内部业务线应用程序。 这对于不向 internet 公开但需要轮循负载分发、会话粘性或 TLS 终止的安全边界内的多层应用程序中的服务和层也很有用。

仅支持1个公共 IP 地址或一个专用 IP 地址。 在创建应用程序网关时选择前端 IP。

- 对于公共 IP，可以在应用程序网关所在的同一位置创建新的公共 IP 地址或使用现有的公共 IP。 有关详细信息，请参阅[静态与动态公共 IP 地址](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#static-versus-dynamic-public-ip-address)。

- 对于专用 IP，可以在创建应用程序网关的子网中指定一个专用 IP 地址。 如果不显式指定专用 IP 地址，则系统会在子网中自动选择一个任意 IP 地址。 以后无法更改选定的 IP 地址类型（静态或动态）。 有关详细信息，请参阅[创建包含内部负载均衡器的应用程序网关](https://docs.microsoft.com/azure/application-gateway/application-gateway-ilb-arm)。

某个前端 IP 地址将关联到检查前端 IP 上的传入请求的侦听器。**

## <a name="listeners"></a>侦听器

侦听器是一个逻辑实体，它可以使用端口、协议、主机和 IP 地址检查传入的连接请求。 配置侦听器时，必须输入与网关上传入请求中的对应值相匹配的值。

使用 Azure 门户创建应用程序网关时，还可以通过选择侦听器的协议和端口来创建默认的侦听器。 可以选择是否要在侦听器上启用 HTTP2 支持。 创建应用程序网关后，可以编辑该默认侦听器的设置 (*appGatewayHttpListener*) 或创建新的侦听器。

### <a name="listener-type"></a>侦听器类型

创建新侦听器时，可以选择[“基本”或“多站点”](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#types-of-listeners)。****

- 如果你希望自己的所有请求（针对任何域）都能够被接受并转发到后端池，请选择“基本”。 了解[如何创建包含基本侦听器的应用程序网关](https://docs.microsoft.com/azure/application-gateway/quick-create-portal)。

- 如果希望根据 *host* 标头或主机名将请求转发到不同的后端池，请选择多站点侦听器，并且必须在其中指定与传入请求匹配的主机名。 这是因为，应用程序网关需要使用 HTTP 1.1 主机标头才能在相同的公共 IP 地址和端口上托管多个网站。

#### <a name="order-of-processing-listeners"></a>侦听器的处理顺序

对于 v1 SKU，请求根据规则顺序和侦听器类型进行匹配。 如果使用基本侦听器的规则最先出现在订单中，则会先处理该规则，并接受该端口和 IP 组合的任何请求。 为了避免这种情况，请先使用多站点侦听器配置规则，然后将包含基本侦听器的规则推送到列表中的最后。

对于 v2 SKU，在基本侦听器之前处理多站点侦听器。

### <a name="front-end-ip"></a>前端 IP

选择要与此侦听器关联的前端 IP 地址。 侦听器将在此 IP 上侦听传入的请求。

### <a name="front-end-port"></a>前端端口

选择前端端口。 选择现有端口或新建一个端口。 选择[允许的端口范围](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#ports)内的任意值。 不仅可以使用已知的端口（例如 80 和 443），而且还能使用任何适用的且允许的自定义端口。 一个端口可用于公共侦听器或专用侦听器。

### <a name="protocol"></a>协议

选择 HTTP 或 HTTPS：

- 如果选择 HTTP，则客户端与应用程序网关之间的流量将不会加密。

- 如果需要[tls 终止](features.md#secure-sockets-layer-ssltls-termination)或[端到端 tls 加密](https://docs.microsoft.com/azure/application-gateway/ssl-overview)，请选择 "HTTPS"。 客户端与应用程序网关之间的流量将会加密。 TLS 连接在应用程序网关上终止。 如果需要端到端 TLS 加密，则必须选择 "HTTPS" 并配置**后端 HTTP**设置。 这可以确保流量在从应用程序网关传输到后端时重新得到加密。


若要配置 TLS 终止和端到端 TLS 加密，你必须将证书添加到侦听器，以使应用程序网关能够派生对称密钥。 这由 TLS 协议规范决定。 使用该对称密钥可以加密和解密发送到网关的流量。 网关证书必须采用个人信息交换 (PFX) 格式。 使用此格式可以导出私钥，供网关用来加密和解密流量。

#### <a name="supported-certificates"></a>支持的证书

请参阅[TLS 终止支持的证书](https://docs.microsoft.com/azure/application-gateway/ssl-overview#certificates-supported-for-ssl-termination)。

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

### <a name="tls-policy"></a>TLS 策略

可以集中进行 TLS/SSL 证书管理，并减少后端服务器场的加密解密开销。 通过集中式 TLS 处理，还可以指定适用于安全要求的中心 TLS 策略。 你可以选择 "*默认*"、"*预定义*" 或 "*自定义*TLS 策略"。

配置 TLS 策略来控制 TLS 协议版本。 可将应用程序网关配置为使用 TLS1.0、TLS1.1 和 TLS1.2 中适用于 TLS 握手的最低协议版本。 默认情况下，SSL 2.0 和 3.0 已禁用且不可配置。 有关详细信息，请参阅[应用程序网关 TLS 策略概述](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-policy-overview)。

创建侦听器后，请将它关联到某个请求路由规则。 该规则确定如何将侦听器上收到的请求路由到后端。

## <a name="request-routing-rules"></a>请求路由规则

使用 Azure 门户创建应用程序网关时，可创建一个默认规则 (*rule1*)。 此规则会将默认侦听器 (*appGatewayHttpListener*) 绑定到默认后端池 (*appGatewayBackendPool*) 和默认后端 HTTP 设置 (*appGatewayBackendHttpSettings*)。 创建网关后，可以编辑该默认规则的设置，或创建新的规则。

### <a name="rule-type"></a>规则类型

创建规则时，可以选择[“基本”或“基于路径”](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#request-routing-rules)。****

- 若要将关联的侦听器（例如 *blog<i></i>.contoso.com/\**）上的所有请求转发到单个后端池，请选择“基本”。
- 若要将来自特定 URL 路径的请求路由到特定的后端池，请选择“基于路径”。 路径模式仅应用到 URL 的路径，而不应用到该 URL 的查询参数。

#### <a name="order-of-processing-rules"></a>规则的处理顺序

使用 v1 SKU 时，将按照路径在基于路径的规则的 URL 路径映射中的列出顺序处理传入请求的模式匹配。 如果某个请求与 URL 路径映射中的两个或更多个路径的模式相匹配，则会匹配最先列出的路径。 请求将转发到与该路径关联的后端。

对于 v2 SKU，完全匹配的优先级高于 URL 路径映射中的路径顺序。 如果请求与两个或更多路径中的模式匹配，则会将请求转发到与完全匹配请求的路径关联的后端。 如果传入请求中的路径与映射中的任何路径都不完全匹配，则将在基于路径的规则的路径映射顺序列表中处理请求的模式匹配。

### <a name="associated-listener"></a>关联的侦听器

将一个侦听器关联到该规则，以评估与该侦听器关联的请求路由规则，从而确定请求要路由到的后端池。**

### <a name="associated-back-end-pool"></a>关联的后端池

将规则关联到包含后端目标的后端池，该池为侦听器收到的请求提供服务。

 - 如果使用基本规则，则只允许一个后端池。 关联的侦听器上的所有请求将转发到该后端池。

 - 如果使用基于路径的规则，请添加对应于每个 URL 路径的多个后端池。 与输入的 URL 路径匹配的请求将转发到相应的后端池。 另请添加默认后端池。 与规则中的任何 URL 路径都不匹配的请求将转发到该池。

### <a name="associated-back-end-http-setting"></a>关联的后端 HTTP 设置

为每个规则添加后端 HTTP 设置。 系统使用此设置中指定的端口号、协议和其他信息，将请求从应用程序网关路由到后端目标。

如果使用基本规则，则只允许一个后端 HTTP 设置。 系统会使用此 HTTP 设置将关联的侦听器上的所有请求转发到相应的后端目标。

如果使用基于路径的规则，请添加对应于每个 URL 路径的多个后端 HTTP 设置。 系统使用对应于每个 URL 路径的 HTTP 设置，将与此设置中的 URL 路径匹配的请求转发到相应的后端目标。 另请添加默认 HTTP 设置。 系统会使用默认 HTTP 设置，将与此规则中的任何 URL 路径都不匹配的请求转发到默认后端池。

### <a name="redirection-setting"></a>重定向设置

如果为基本规则配置了重定向，则关联的侦听器上的所有请求将重定向到目标。 此过程称为全局重定向。** 如果为基于路径的规则配置了重定向，则只会重定向特定站点区域中的请求。 区域的示例包括 */cart/\** 表示的购物车区域。 此过程称为基于路径的重定向。**

有关重定向的详细信息，请参阅[应用程序网关重定向概述](redirect-overview.md)。

#### <a name="redirection-type"></a>重定向类型

选择所需的重定向类型：*永久性（301）*、*临时（307）*、*找到（302）* 或*查看其他（303）*。

#### <a name="redirection-target"></a>重定向目标

选择另一个侦听器或外部站点作为重定向目标。

##### <a name="listener"></a>侦听器

选择侦听器作为重定向目标可将来自网关上的一个侦听器的流量重定向到另一个侦听器。 想要启用 HTTP 到 HTTPS 的重定向时，必须指定此设置。 此设置将来自源侦听器（用于检查 HTTP 请求）的流量重定向到目标侦听器（用于检查传入的 HTTPS 请求）。 还可以选择在转发到重定向目标的请求中包含来自原始请求的查询字符串和路径。

![应用程序网关组件对话框](./media/configuration-overview/configure-redirection.png)

有关 HTTP 到 HTTPS 的重定向的详细信息，请参阅：
- [使用 Azure 门户配置 HTTP 到 HTTPS 的重定向](redirect-http-to-https-portal.md)
- [使用 PowerShell 配置 HTTP 到 HTTPS 的重定向](redirect-http-to-https-powershell.md)
- [使用 Azure CLI 配置 HTTP 到 HTTPS 的重定向](redirect-http-to-https-cli.md)

##### <a name="external-site"></a>外部站点

若要将与此类规则关联的侦听器上的流量重定向到外部站点，请选择外部站点。 可以选择在转发到重定向目标的请求中包含来自原始请求的查询字符串。 无法将原始请求中的路径转发到外部站点。

有关重定向的详细信息，请参阅：
- [使用 PowerShell 将流量重定向到外部站点](redirect-external-site-powershell.md)
- [使用 CLI 将流量重定向到外部站点](redirect-external-site-cli.md)

#### <a name="rewrite-the-http-header-setting"></a>重写 HTTP 标头设置

当请求和响应数据包在客户端和后端池之间移动时，此设置将添加、删除或更新 HTTP 请求和响应标头。 有关详细信息，请参见:

 - [重写 HTTP 标头概述](rewrite-http-headers.md)
 - [配置 HTTP 标头重写](rewrite-http-headers-portal.md)

## <a name="http-settings"></a>HTTP 设置

应用程序网关使用此处指定的配置将流量路由到后端服务器。 创建 HTTP 设置后，必须将其关联到一个或多个请求路由规则。

### <a name="cookie-based-affinity"></a>基于 Cookie 的相关性

Azure 应用程序网关使用网关管理的 cookie 来维护用户会话。 当用户将第一个请求发送到应用程序网关时，它会在响应中使用包含会话详细信息的哈希值来设置关联 cookie，这样，携带相关性 cookie 的后续请求将路由到相同的后端服务器以保持粘性。 

如果要在同一台服务器上保存用户会话，并在服务器上为用户会话保存会话状态时，此功能很有用。 如果应用程序无法处理基于 Cookie 的相关性，则你无法使用此功能。 若要使用此功能，请确保客户端支持 Cookie。

[Chromium 浏览器](https://www.chromium.org/Home) [v80 更新](https://chromiumdash.appspot.com/schedule)带来了一个强制要求，其中不含[SameSite](https://tools.ietf.org/id/draft-ietf-httpbis-rfc6265bis-03.html#rfc.section.5.3.7)特性的 HTTP Cookie 必须被视为 SameSite = 宽松。 对于 CORS （跨源资源共享）请求，如果必须在第三方上下文中发送 cookie，则必须使用*SameSite = None;安全*特性仅应通过 HTTPS 发送。 否则，在仅限 HTTP 的方案中，浏览器不会在第三方上下文中发送 cookie。 此更新从 Chrome 的目标是增强安全性并避免跨站点请求伪造（CSRF）攻击。 

若要支持此项更改，从 17 2020 年2月起，应用程序网关（所有 SKU 类型）将插入另一个名为*ApplicationGatewayAffinityCORS*的 cookie 以及现有的*ApplicationGatewayAffinity* cookie。 *ApplicationGatewayAffinityCORS* cookie 添加了两个以上的属性（*"SameSite = None;安全 "*），以便即使对于跨源请求，也会保留粘滞会话。

请注意，默认关联 cookie 名称为*ApplicationGatewayAffinity* ，你可以对其进行更改。 如果使用的是自定义相关性 cookie 名称，则会添加一个具有 CORS 作为后缀的附加 cookie。 例如， *CustomCookieNameCORS*。

> [!NOTE]
> 如果设置了属性*SameSite = None* ，则 cookie 还必须包含*安全*标志，并且必须通过 HTTPS 发送。  如果通过 CORS 需要会话相关性，则必须将工作负荷迁移到 HTTPS。 请参阅此处的应用程序网关的 TLS 卸载和端到端 TLS 文档–[概述](ssl-overview.md)：[使用 AZURE 门户配置具有 TLS 终止的应用程序网关](create-ssl-portal.md)，[并通过门户使用应用程序网关配置端到端 tls](end-to-end-ssl-portal.md)。

### <a name="connection-draining"></a>连接清空

连接清空可帮助你在计划内服务更新期间正常删除后端池成员。 在创建规则期间，可将此设置应用到后端池的所有成员。 它确保后端池的所有注销实例继续维护现有连接，并在可配置的超时时间内处理正在进行的请求，并且不会接收任何新请求或连接。 此情况的唯一例外是由于网关托管会话相关性而绑定到注销实例的请求，这些请求将继续被转发到注销实例。 连接清空将应用到已从后端池中显式删除的后端实例。

### <a name="protocol"></a>协议

应用程序网关支持使用 HTTP 和 HTTPS 将请求路由到后端服务器。 如果选择了 HTTP 协议，则流量将以未加密的形式传送到后端服务器。 如果不能接受未加密的通信，请选择 HTTPS。

此设置与侦听器中的 HTTPS 组合支持[端到端 TLS](ssl-overview.md)。 这样，就可以安全地将敏感数据以加密的形式传输到后端。 后端池中启用了端到端 TLS 的每个后端服务器都必须配置证书以允许安全通信。

### <a name="port"></a>Port

此设置指定后端服务器要在哪个端口上侦听来自应用程序网关的流量。 可以配置 1 到 65535 的端口号。

### <a name="request-timeout"></a>请求超时

此设置表示应用程序网关在接收后端服务器的响应时会等待多少秒。

### <a name="override-back-end-path"></a>替代后端路径

使用此设置可以配置可选的自定义转发路径，以便在将请求转发到后端时使用。 与“替代后端路径”字段中的自定义路径匹配的任意传入路径部分将复制到转发的路径。**** 下表描述了此功能的工作原理：

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

### <a name="use-for-app-service"></a>用于应用服务

这是一个仅限 UI 的快捷方式，用于选择 Azure 应用服务后端的两个所需设置。 它会启用“从后端地址中选取主机名”，并创建新的自定义探测（如果你还没有该探测）。**** （有关详细信息，请参阅本文的[从后端地址中选取主机名](#pick)部分。）将创建新的探测，并从后端成员的地址中选取探测标头。

### <a name="use-custom-probe"></a>使用自定义探测

此设置用于将[自定义探测](application-gateway-probe-overview.md#custom-health-probe)与某个 HTTP 设置相关联。 只能将一个自定义探测关联到某个 HTTP 设置。 如果未显式关联自定义探测，则会使用[默认探测](application-gateway-probe-overview.md#default-health-probe-settings)来监视后端的运行状况。 我们建议创建自定义探测，以便更好地控制后端的运行状况监视。

> [!NOTE]
> 只有在将相应的 HTTP 设置显式关联到某个侦听器之后，自定义探测才会监视后端池的运行状况。

### <a name="pick-host-name-from-back-end-address"></a><a id="pick"/></a>从后端地址中选取主机名

此功能将请求中的 *host* 标头动态设置为后端池的主机名。 主机名使用 IP 地址或 FQDN。

如果后端的域名不同于应用程序网关的 DNS 名称，并且后端必须使用特定的 host 标头才能解析为正确的终结点，则此功能会很有帮助。

例如，使用多租户服务作为后端时。 应用服务是使用共享空间和单个 IP 地址的多租户服务。 因此，只能通过自定义域设置中配置的主机名访问应用服务。

默认情况下，自定义域名为*example.azurewebsites.net*。 若要使用应用程序网关访问应用服务，通过未在应用服务中显式注册的主机名或通过应用程序网关的 FQDN，你可以将原始请求中的主机名重写到应用服务的主机名。 为此，请启用“从后端地址中选取主机名”设置。****

对于其现有自定义 DNS 名称已映射到应用服务的自定义域，不需要启用此设置。

> [!NOTE]
> 应用服务环境不需要此设置，因为它属于专用部署。

### <a name="host-name-override"></a>主机名替代

此功能可将应用程序网关上的传入请求中的 *host* 标头替换为指定的主机名。

例如，如果将 *www.contoso.com* 指定为“主机名”设置，则将请求转发到后端服务器时，原始请求 *`https://appgw.eastus.cloudapp.azure.com/path1` 会更改为 *`https://www.contoso.com/path1`。****

## <a name="back-end-pool"></a>后端池

可将后端池指向四种类型的后端成员：特定的虚拟机、虚拟机规模集、IP 地址/FQDN 或应用服务。 

创建后端池后，必须将其关联到一个或多个请求路由规则。 此外，必须为应用程序网关上的每个后端池配置运行状况探测。 满足请求路由规则条件时，应用程序网关会将流量转发到相应后端池中正常运行的服务器（是否正常由运行状况探测决定）。

## <a name="health-probes"></a>运行状况探测

应用程序网关默认会监视其后端中所有资源的运行状况。 但是，我们强烈建议为每个后端 HTTP 设置创建一个自定义探测，以便更好地控制运行状况监视。 若要了解如何配置自定义探测，请参阅[自定义运行状况探测设置](application-gateway-probe-overview.md#custom-health-probe-settings)。

> [!NOTE]
> 创建自定义运行状况探测后，需将其关联到后端 HTTP 设置。 只有在将相应的 HTTP 设置通过规则显式关联到某个侦听器之后，自定义探测才会监视后端池的运行状况。

## <a name="next-steps"></a>后续步骤

了解应用程序网关组件后，可以：

- [在 Azure 门户中创建应用程序网关](quick-create-portal.md)
- [使用 PowerShell 创建应用程序网关](quick-create-powershell.md)
- [使用 Azure CLI 创建应用程序网关](quick-create-cli.md)
