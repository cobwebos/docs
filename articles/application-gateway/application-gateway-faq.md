---
title: 有关 Azure 应用程序网关的常见问题解答
description: 查找有关 Azure 应用程序网关常见问题的解答。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 05/26/2020
ms.author: victorh
ms.custom: references_regions
ms.openlocfilehash: b55ba6ab73758ed562aaabeef91cf08acf659758
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/10/2020
ms.locfileid: "89646549"
---
# <a name="frequently-asked-questions-about-application-gateway"></a>应用程序网关常见问题

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

以下是有关 Azure 应用程序网关的常见问题。

## <a name="general"></a>常规

### <a name="what-is-application-gateway"></a>什么是应用程序网关？

Azure 应用程序网关以服务形式提供应用程序传送控制器 (ADC)。 它为应用程序提供第 7 层的各种负载均衡功能。 此服务高度可用、可缩放，且完全由 Azure 管理。

### <a name="what-features-does-application-gateway-support"></a>应用程序网关支持哪些功能？

应用程序网关支持自动缩放、TLS 卸载、端到端 TLS、Web 应用程序防火墙 (WAF)、基于 Cookie 的会话相关性、基于 URL 路径的路由、多站点托管和其他功能。 有关支持的功能的完整列表，请参阅[应用程序网关简介](application-gateway-introduction.md)。

### <a name="how-do-application-gateway-and-azure-load-balancer-differ"></a>应用程序网关与 Azure 负载均衡器有何不同？

应用程序网关是第 7 层负载均衡器，这意味着，它只处理 Web 流量（HTTP、HTTPS、WebSocket 和 HTTP/2）。 它支持 TLS 终止、基于 Cookie 的会话相关性以及对流量进行负载均衡的轮循机制等功能。 负载均衡器在第 4 层对流量进行负载均衡（TCP 或 UDP）。

### <a name="what-protocols-does-application-gateway-support"></a>应用程序网关支持哪些协议？

应用程序网关支持 HTTP、HTTPS、HTTP/2 和 WebSocket。

### <a name="how-does-application-gateway-support-http2"></a>应用程序网关如何支持 HTTP/2？

请参阅 [HTTP/2 支持](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http2-support)。

### <a name="what-resources-are-supported-as-part-of-a-backend-pool"></a>支持在后端池中添加哪些资源？

请参阅[支持的后端资源](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#backend-pools)。

### <a name="in-what-regions-is-application-gateway-available"></a>应用程序网关已在哪些区域推出？

应用程序网关已在国际版 Azure 的所有区域推出。 在 [Azure 中国世纪互联](https://www.azure.cn/)和 [Azure 政府](https://azure.microsoft.com/overview/clouds/government/)中也已推出。

### <a name="is-this-deployment-dedicated-for-my-subscription-or-is-it-shared-across-customers"></a>此部署是专门于订阅，还是在所有客户之间共享？

应用程序网关是虚拟网络中的专用部署。

### <a name="does-application-gateway-support-http-to-https-redirection"></a>应用程序网关是否支持 HTTP 到 HTTPS 的重定向？

支持重定向。 请参阅[应用程序网关重定向概述](application-gateway-redirect-overview.md)。

### <a name="in-what-order-are-listeners-processed"></a>按什么顺序处理侦听器？

请参阅[侦听器处理顺序](https://docs.microsoft.com/azure/application-gateway/configuration-overview#order-of-processing-listeners)。

### <a name="where-do-i-find-the-application-gateway-ip-and-dns"></a>在何处可以找到应用程序网关的 IP 和 DNS？

如果使用公共 IP 地址作为终结点，可以在公共 IP 地址资源中找到 IP 和 DNS 信息。 或者可以在门户中应用程序网关的概述页上找到它。 如果使用内部 IP 地址，请在概述页面上查找信息。

对于 v2 SKU，请打开公共 IP 资源，然后选择“配置”。 “DNS 名称标签(可选)”字段可用于配置 DNS 名称。

### <a name="what-are-the-settings-for-keep-alive-timeout-and-tcp-idle-timeout"></a>保持活动状态超时和 TCP 空闲超时的设置有哪些？

Keep-Alive 超时控制应用程序网关在重新使用或关闭它之前将等待客户端在持久连接上发送另一个 HTTP 请求的时间。 TCP 空闲超时控制在无活动的情况下 TCP 连接保持打开状态的时间。 

应用程序网关 v1 SKU 中的 Keep-Alive 超时为 120 秒，而在 v2 SKU 中为 75 秒。 在应用程序网关的 v1 和 v2 SKU 的前端虚拟 IP (VIP) 上，TCP 空闲超时是默认的 4 分钟。 你可以将 v1 和 v2 应用程序网关上的 TCP 空闲超时值配置为 4 分钟到 30 分钟之间的任何时间值。 对于 v1 和 v2 应用程序网关，需要导航到应用程序网关的公共 IP，并更改门户上公共 IP 的“配置”边栏选项卡下的 TCP 空闲超时。 可以通过运行以下命令，通过 PowerShell 设置公共 IP 的 TCP 空闲超时值： 

```azurepowershell-interactive
$publicIP = Get-AzPublicIpAddress -Name MyPublicIP -ResourceGroupName MyResourceGroup
$publicIP.IdleTimeoutInMinutes = "15"
Set-AzPublicIpAddress -PublicIpAddress $publicIP
```

### <a name="does-the-ip-or-dns-name-change-over-the-lifetime-of-the-application-gateway"></a>在应用程序网关的生存期内，其 IP 或 DNS 名称是否会变化？

在应用程序网关 V1 SKU 中，如果停止再启动应用程序网关，则 VIP 可能会变化。 但是，与应用程序网关关联的 DNS 名称在网关的整个生命周期内不会更改。 由于 DNS 名称不会更改，建议使用 CNAME 别名并使其指向应用程序网关的 DNS 地址。 在应用程序网关 V2 SKU 中，可以将 IP 地址设置为静态，因此 IP 和 DNS 名称将在应用程序网关的生存期内不会更改。 

### <a name="does-application-gateway-support-static-ip"></a>应用程序网关是否支持静态 IP？

是，应用程序网关 v2 SKU 支持静态公共 IP 地址。 v1 SKU 支持静态内部 IP。

### <a name="does-application-gateway-support-multiple-public-ips-on-the-gateway"></a>应用程序网关是否支持在网关上使用多个公共 IP？

一个应用程序网关仅支持一个公共 IP 地址。

### <a name="how-large-should-i-make-my-subnet-for-application-gateway"></a>应该为应用程序网关创建多大的子网？

请参阅[应用程序网关子网大小注意事项](https://docs.microsoft.com/azure/application-gateway/configuration-overview#size-of-the-subnet)。

### <a name="can-i-deploy-more-than-one-application-gateway-resource-to-a-single-subnet"></a>是否可将多个应用程序网关资源部署到单个子网？

是的。 除了给定应用程序网关部署的多个实例以外，还可以在包含不同应用程序网关资源的现有子网中预配另一个唯一的应用程序网关资源。

单个子网不能同时支持 v2 和 v1 应用程序网关 SKU。

### <a name="does-application-gateway-v2-support-user-defined-routes-udr"></a>应用程序网关 v2 是否支持用户定义的路由 (UDR)？

是，但仅限特定方案。 有关详细信息，请参阅 [应用程序网关基础结构配置](configuration-infrastructure.md#supported-user-defined-routes)。

### <a name="does-application-gateway-support-x-forwarded-for-headers"></a>应用程序网关是否支持 x-forwarded-for 标头？

是的。 请参阅[对请求的修改](https://docs.microsoft.com/azure/application-gateway/how-application-gateway-works#modifications-to-the-request)。

### <a name="how-long-does-it-take-to-deploy-an-application-gateway-will-my-application-gateway-work-while-its-being-updated"></a>部署应用程序网关需要多长时间？ 更新时应用程序网关是否仍正常工作？

预配新的应用程序网关 v1 SKU 部署最多需 20 分钟。 更改实例大小或计数不会出现干扰，且在此期间网关处于活动状态。

大多数使用 v2 SKU 的部署大约需要 6 分钟进行预配。 但是，根据部署类型的不同，可能需要更长的时间。 例如，跨多个具有多个实例的可用性区域的部署可能需要 6 分钟以上的时间。 

### <a name="can-i-use-exchange-server-as-a-backend-with-application-gateway"></a>是否可以使用 Exchange Server 作为应用程序网关的后端？

否。 应用程序网关不支持电子邮件协议，如 SMTP、IMAP 和 POP3。

### <a name="is-there-guidance-available-to-migrate-from-the-v1-sku-to-the-v2-sku"></a>是否提供了从 v1 SKU 迁移到 v2 SKU 的指南？

是的。 有关详细信息，请参阅[将 Azure 应用程序网关和 Web 应用程序防火墙从 v1 迁移到 v2](migrate-v1-v2.md)。

### <a name="will-the-application-gateway-v1-sku-continue-to-be-supported"></a>是否会继续支持应用程序网关 v1 SKU？

是的。 我们会继续支持应用程序网关 v1 SKU。 不过，强烈建议你迁移到 v2 以利用该 SKU 中的功能更新。 有关详细信息，请参阅[自动缩放和区域冗余应用程序网关 v2](application-gateway-autoscaling-zone-redundant.md)。

### <a name="does-application-gateway-v2-support-proxying-requests-with-ntlm-authentication"></a>应用程序网关 V2 是否支持使用 NTLM 身份验证的代理请求？

否。 应用程序网关 V2 尚不支持使用 NTLM 身份验证的代理请求。

### <a name="does-application-gateway-affinity-cookie-support-samesite-attribute"></a>应用程序网关关联 Cookie 是否支持 SameSite 属性？
是的，[Chromium 浏览器](https://www.chromium.org/Home) [v80 更新](https://chromiumdash.appspot.com/schedule)引入了对 HTTP Cookie 的强制要求，不会将 SameSite 属性视为 SameSite = Lax。 这意味着，浏览器不会将应用程序网关关联 Cookie 发送到第三方上下文中。 

为了支持此方案，除了现有的“ApplicationGatewayAffinity”Cookie 外，应用程序网关还会注入另一个名为“ApplicationGatewayAffinityCORS”的 Cookie。  这两个 Cookie 类似，但 ApplicationGatewayAffinityCORS Cookie 中添加了两个附加属性：*SameSite=None; Secure*。 这些属性甚至可以为跨源请求维护粘性会话。 有关详细信息，请参阅[基于 Cookie 的关联部分](configuration-http-settings.md#cookie-based-affinity)。

## <a name="performance"></a>性能

### <a name="how-does-application-gateway-support-high-availability-and-scalability"></a>应用程序网关如何支持高可用性和可伸缩性？

如果已部署两个或更多个实例，则应用程序网关 v1 SKU 支持高可用性方案。 Azure 跨更新域和容错域分配这些实例，确保实例不会全部同时发生故障。 为了支持可伸缩性，v1 SKU 将添加同一网关的多个实例来分担负载。

v2 SKU 可以自动确保新实例分布到各个容错域和更新域中。 如果选择“区域冗余”，则最新实例还将分布到各个可用性区域中以提供区域性故障复原能力。

### <a name="how-do-i-achieve-a-dr-scenario-across-datacenters-by-using-application-gateway"></a>如何使用应用程序网关实现跨数据中心的灾难恢复方案？

使用流量管理器，可以跨不同数据中心内的多个应用程序网关分配流量。

### <a name="does-application-gateway-support-autoscaling"></a>应用程序网关是否支持自动缩放？

是，应用程序网关 v2 SKU 支持自动缩放。 有关详细信息，请参阅[自动缩放和区域冗余应用程序网关](application-gateway-autoscaling-zone-redundant.md)。

### <a name="does-manual-or-automatic-scale-up-or-scale-down-cause-downtime"></a>手动或自动纵向扩展或缩减是否会导致停机？

否。 实例将分布在升级域和容错域上。

### <a name="does-application-gateway-support-connection-draining"></a>应用程序网关是否支持连接排出？

是的。 可以设置连接排出，以在不中断操作的情况下更改后端池内的成员。 有关详细信息，请参阅[应用程序网关的“连接排出”部分](features.md#connection-draining)。

### <a name="can-i-change-instance-size-from-medium-to-large-without-disruption"></a>是否可以在不造成中断的情况下，将实例大小从中型更改为大型？

是的。

## <a name="configuration"></a>配置

### <a name="is-application-gateway-always-deployed-in-a-virtual-network"></a>是否始终要将应用程序网关部署在虚拟网络中？

是的。 应用程序网关始终部署在虚拟网络子网中。 此子网只能包含应用程序网关。 有关详细信息，请参阅[虚拟网络和子网要求](https://docs.microsoft.com/azure/application-gateway/configuration-overview#azure-virtual-network-and-dedicated-subnet)。

### <a name="can-application-gateway-communicate-with-instances-outside-of-its-virtual-network-or-outside-of-its-subscription"></a>应用程序网关是否能够与其所在虚拟网络外部或其所在订阅外部的实例通信？

只要建立 IP 连接，应用程序网关就能与其所在的虚拟网络外部的实例进行通信。 应用程序网关还能与其所在订阅外部的实例通信。 如果你打算使用内部 IP 作为后端池成员，请使用[虚拟网络对等互连](../virtual-network/virtual-network-peering-overview.md)或 [Azure VPN 网关](../vpn-gateway/vpn-gateway-about-vpngateways.md)。

### <a name="can-i-deploy-anything-else-in-the-application-gateway-subnet"></a>是否可以在应用程序网关子网中部署其他任何组件？

否。 但可以在子网中部署其他应用程序网关。

### <a name="are-network-security-groups-supported-on-the-application-gateway-subnet"></a>应用程序网关子网是否支持网络安全组？

请参阅[应用程序网关子网中的网络安全组](https://docs.microsoft.com/azure/application-gateway/configuration-overview#network-security-groups-on-the-application-gateway-subnet)。

### <a name="does-the-application-gateway-subnet-support-user-defined-routes"></a>应用程序网关子网是否支持用户定义的路由？

请参阅[应用程序网关子网中支持的用户定义的路由](https://docs.microsoft.com/azure/application-gateway/configuration-infrastructure#supported-user-defined-routes)。

### <a name="what-are-the-limits-on-application-gateway-can-i-increase-these-limits"></a>应用程序网关有哪些限制？ 是否可以提高这些限制？

请参阅[应用程序网关限制](../azure-resource-manager/management/azure-subscription-service-limits.md#application-gateway-limits)。

### <a name="can-i-simultaneously-use-application-gateway-for-both-external-and-internal-traffic"></a>是否可以同时对外部和内部流量使用应用程序网关？

是的。 每个应用程序网关支持一个内部 IP 和一个外部 IP。

### <a name="does-application-gateway-support-virtual-network-peering"></a>应用程序网关是否支持虚拟网络对等互连？

是的。 虚拟网络对等互连有助于对其他虚拟网络中的流量进行负载均衡。

### <a name="can-i-talk-to-on-premises-servers-when-theyre-connected-by-expressroute-or-vpn-tunnels"></a>如果通过 ExpressRoute 或 VPN 隧道连接本地服务器，是否可与这些服务器通信？

可以，只要允许这种流量。

### <a name="can-one-backend-pool-serve-many-applications-on-different-ports"></a>是否可以使用一个后端池来为不同端口上的许多应用程序提供服务？

支持微服务体系结构。 若要探测不同的端口，需要配置多个 HTTP 设置。

### <a name="do-custom-probes-support-wildcards-or-regex-on-response-data"></a>自定义探测是否支持对响应数据使用通配符或正则表达式？

否。 

### <a name="how-are-routing-rules-processed-in-application-gateway"></a>如何在应用程序网关中处理路由规则？

请参阅[规则的处理顺序](https://docs.microsoft.com/azure/application-gateway/configuration-overview#order-of-processing-rules)。

### <a name="for-custom-probes-what-does-the-host-field-signify"></a>对于自定义探测，Host 字段是什么意思？

在应用程序网关上配置了多站点的情况下，Host 字段指定要将探测发送到的名称。 否则使用“127.0.0.1”。 此值不同于虚拟机主机名。 其格式为 \<protocol\>://\<host\>:\<port\>\<path\>。

### <a name="can-i-allow-application-gateway-access-to-only-a-few-source-ip-addresses"></a>能否仅允许应用程序网关访问几个源 IP 地址？

是的。 请参阅[限制对特定源 IP 的访问](https://docs.microsoft.com/azure/application-gateway/configuration-overview#allow-application-gateway-access-to-a-few-source-ips)。

### <a name="can-i-use-the-same-port-for-both-public-facing-and-private-facing-listeners"></a>能否同时对公共和专用侦听器使用同一个端口？

否。

### <a name="does-application-gateway-support-ipv6"></a>应用程序网关是否支持 IPv6？

应用程序网关 v2 目前不支持 IPv6。 它只能使用 IPv4 在双堆栈 VNet 中运行，但网关子网仅限 IPv4。 应用程序网关 v1 不支持双堆栈 VNet。 

### <a name="how-do-i-use-application-gateway-v2-with-only-private-frontend-ip-address"></a>如何实现将应用程序网关 V2 仅用于专用前端 IP 地址？

应用程序网关 V2 目前不支持专用 IP 模式。 它支持以下组合
* 专用 IP 和公共 IP
* 仅公共 IP

但若要将应用程序网关 V2 仅用于专用 IP，则可按以下过程操作：
1. 使用公共和专用前端 IP 地址创建应用程序网关
2. 不要为公共前端 IP 地址创建任何侦听器。 应用程序网关不会侦听公共 IP 地址上的任何流量，但前提是没有为其创建侦听器。
3. 为应用程序网关子网创建并附加一个[网络安全组](https://docs.microsoft.com/azure/virtual-network/security-overview)，使用以下配置（按优先级顺序排列）：
    
    a. 允许的流量来自使用 **GatewayManager** 服务标记的“源”，其“目标”为“任意”，“目标端口”为 **65200-65535**。 此端口范围是进行 Azure 基础结构通信所必需的。 这些端口通过证书身份验证进行保护（锁定）。 如果没有适当的证书，外部实体（包括网关用户管理员）将无法对这些终结点做出任何更改
    
    b. 允许的流量来自使用 **AzureLoadBalancer** 服务标记的“源”，“目标”和“目标端口”为“任意”
    
    c. 拒绝的所有入站流量来自使用 **Internet** 服务标记的“源”，“目标”和“目标端口”为“任意”。 在入站规则中为此规则指定最低优先级
    
    d. 保留默认规则（例如允许入站 VirtualNetwork），这样就不会阻止在专用 IP 地址上进行的访问
    
    e. 不能阻止出站 Internet 连接。 否则会面临日志记录、指标等问题。

仅适用于专用 IP 访问的 NSG 配置示例：![仅适用于专用 IP 访问的应用程序网关 V2 NSG 配置](./media/application-gateway-faq/appgw-privip-nsg.png)

## <a name="configuration---tls"></a>配置 - TLS

### <a name="what-certificates-does-application-gateway-support"></a>应用程序网关支持哪些证书？

应用程序网关支持自签名证书、证书颁发机构 (CA) 证书、扩展验证 (EV) 证书、多域 (SAN) 证书和通配符证书。

### <a name="what-cipher-suites-does-application-gateway-support"></a>应用程序网关支持哪些加密套件？

应用程序网关支持以下密码套件。 

- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA
- TLS_DHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_DHE_RSA_WITH_AES_256_CBC_SHA
- TLS_DHE_RSA_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_AES_256_GCM_SHA384
- TLS_RSA_WITH_AES_128_GCM_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA256
- TLS_RSA_WITH_AES_128_CBC_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA
- TLS_RSA_WITH_AES_128_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_3DES_EDE_CBC_SHA
- TLS_DHE_DSS_WITH_3DES_EDE_CBC_SHA

有关如何自定义 TLS 选项的信息，请参阅[在应用程序网关上配置 TLS 策略版本和密码套件](application-gateway-configure-ssl-policy-powershell.md)。

### <a name="does-application-gateway-support-reencryption-of-traffic-to-the-backend"></a>应用程序网关是否支持重新加密发往后端的流量？

是的。 应用程序网关支持 TLS 卸载和端到端 TLS，因此支持重新加密发往后端的流量。

### <a name="can-i-configure-tls-policy-to-control-tls-protocol-versions"></a>能否通过配置 TLS 策略来控制 TLS 协议版本？

是的。 可将应用程序网关配置为拒绝 TLS1.0、TLS1.1 和 TLS1.2。 默认情况下，SSL 2.0 和 3.0 已禁用且不可配置。

### <a name="can-i-configure-cipher-suites-and-policy-order"></a>我是否可以配置密码套件和策略顺序？

是的。 在应用程序网关中，可以[配置加密套件](application-gateway-ssl-policy-overview.md)。 若要定义自定义策略，请至少启用下列其中一个加密套件。 

* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
* TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_256_CBC_SHA256
* TLS_RSA_WITH_AES_128_CBC_SHA256

应用程序网关使用 SHA256 进行后端管理。

### <a name="how-many-tlsssl-certificates-does-application-gateway-support"></a>应用程序网关支持多少个 TLS/SSL 证书？

应用程序网关最多支持 100 个 TLS/SSL 证书。

### <a name="how-many-authentication-certificates-for-backend-reencryption-does-application-gateway-support"></a>应用程序网关支持使用多少个身份验证证书进行后端重新加密？

应用程序网关最多支持 100 个身份验证证书。

### <a name="does-application-gateway-natively-integrate-with-azure-key-vault"></a>应用程序网关是否原生与 Azure 密钥保管库集成？

是，应用程序网关 v2 SKU 支持密钥保管库。 有关详细信息，请参阅[使用 Key Vault 证书实现 TLS 终止](key-vault-certs.md)。

### <a name="how-do-i-configure-https-listeners-for-com-and-net-sites"></a>如何为 .com 和 .net 站点配置 HTTPS 侦听器？ 

对于基于多域（基于主机）的路由，可以创建多站点侦听器，设置使用 HTTPS 作为协议的侦听器，然后将侦听器与路由规则相关联。 有关详细信息，请参阅[使用应用程序网关进行多站点托管](https://docs.microsoft.com/azure/application-gateway/multiple-site-overview)。

### <a name="can-i-use-special-characters-in-my-pfx-file-password"></a>能否在 .pfx 文件密码中使用特殊字符？

不可以，只能在 .pfx 文件密码中使用字母数字字符。

### <a name="my-ev-certificate-is-issued-by-digicert-and-my-intermediate-certificate-has-been-revoked-how-do-i-renew-my-certificate-on-application-gateway"></a>我的 EV 证书由 DigiCert 颁发，而我的中间证书已被吊销。 如何在应用程序网关上续订我的证书？

证书颁发机构 (CA) 浏览器成员最近发布的报告详细介绍了由 CA 供应商颁发的多个证书，而这些证书已被我们的客户、Microsoft 以及大型技术社区使用，且不符合公开受信任的 CA 行业标准。 有关非合规 CA 的报告，请参阅以下内容：  

* [Bug 1649951](https://bugzilla.mozilla.org/show_bug.cgi?id=1649951)
* [Bug 1650910](https://bugzilla.mozilla.org/show_bug.cgi?id=1650910)

根据行业的合规性要求，CA 供应商已开始吊销非合规 CA 并颁发合规 CA，这要求客户重新颁发其证书。 Microsoft 正在与这些供应商密切合作，以最大程度地降低对 Azure 服务的潜在影响，但在“自带证书”(BYOC)  方案中使用的自颁发证书或证书仍存在意外被吊销的风险。

若要检查应用程序使用的证书是否已被吊销，请参阅 [DigiCert 的公告](https://knowledge.digicert.com/alerts/DigiCert-ICA-Replacement)和[证书吊销跟踪程序](https://misissued.com/#revoked)。 如果你的证书已被吊销或将被吊销，你将需要从你的应用程序中使用的 CA 供应商请求新证书。 若要避免应用程序的可用性由于证书被意外吊销而被中断，或要更新已吊销的证书，请参阅我们的 Azure 更新贴文，获取支持 BYOC 的各种 Azure 服务的修正链接： https://azure.microsoft.com/updates/certificateauthorityrevocation/

有关应用程序网关的特定信息，请参阅下内容：

如果使用由某个已吊销的 ICA 颁发的证书，则可能会中断应用程序的可用性，并且你可能会收到各种错误消息（具体取决于应用程序），其中包括但不限于： 

1.  证书/已吊销的证书无效
2.  连接超时
3.  HTTP 502

若要避免应用程序由于此问题而出现任何中断，或者要重新颁发已吊销的 CA，你需要执行以下操作： 

1.  有关如何重新颁发证书的信息，请与证书提供商联系
2.  重新颁发后，请将 Azure 应用程序网关/WAF 上的证书更新为完全[信任链](https://docs.microsoft.com/windows/win32/seccrypto/certificate-chains)（叶证书、中间证书、根证书）。 根据证书的使用位置，在应用程序网关的侦听器或 HTTP 设置上，按照以下步骤更新证书，并查看所述的文档链接以获取详细信息。
3.  更新后端应用程序服务器，以使用重新颁发的证书。 根据所使用的后端服务器，证书更新步骤可能会有所不同。 请查看供应商提供的文档。

更新侦听器中的证书：

1.  在 [Azure 门户](https://portal.azure.com/)中，打开应用程序网关资源
2.  打开与证书关联的侦听器设置
3.  单击“续订或编辑所选证书”
4.  上传新的 PFX 证书，并加密，然后单击“保存”。
5.  访问网站并验证站点是否按预期方式工作。有关详细信息，请查看 [此处](https://docs.microsoft.com/azure/application-gateway/renew-certificates) 的文档。

如果要在应用程序网关侦听器中从 Azure KeyVault 引用证书，建议执行以下步骤来进行快速更改-

1.  在 [Azure 门户](https://portal.azure.com/)中，导航到已与应用程序网关关联的 Azure Key Vault 设置
2.  在存储区中添加/导入重新颁发的证书。 有关操作方法的详细信息，请参阅[此处](https://docs.microsoft.com/azure/key-vault/certificates/quick-create-portal)的文档。
3.  导入证书后，导航到应用程序网关侦听器设置，然后在“从 Key Vault 选择证书”下，单击“证书”下拉箭头，然后选择最近添加的证书
4.  单击“保存”。如需详细了解 TLS 终止和 Key Vault 证书的信息，请查看 [此处](https://docs.microsoft.com/azure/application-gateway/key-vault-certs) 的文档。


更新 HTTP 设置中的证书：

如果使用的是应用程序网关/WAF 服务的 V1 SKU，则必须将新证书上传为后端身份验证证书。
1.  在 [Azure 门户](https://portal.azure.com/)中，打开应用程序网关资源
2.  打开与证书关联的 HTTP设置
3.  单击“添加证书”，上传重新颁发的证书，然后单击“保存”
4.  可以稍后通过单击旧证书旁的“...” 选项按钮删除旧证书，再选择“删除”，然后单击“保存”。
有关详细信息，请参阅[此处](https://docs.microsoft.com/azure/application-gateway/end-to-end-ssl-portal#add-authenticationtrusted-root-certificates-of-back-end-servers)的文档。

如果使用的是应用程序网关/WAF 服务的 V2 SKU，则无需在 HTTP 设置中上传新证书，因为 V2 SKU 使用“受信任的根证书”，无需在此执行任何操作。

## <a name="configuration---ingress-controller-for-aks"></a>配置 - AKS 的入口控制器

### <a name="what-is-an-ingress-controller"></a>什么是入口控制器？

Kubernetes 允许创建 `deployment` 和 `service` 资源，以便在群集内部公开一组 pod。 为了向外公开同一服务，我们定义了一个 [`Ingress`](https://kubernetes.io/docs/concepts/services-networking/ingress/) 资源，它提供负载均衡、TLS 终止和基于名称的虚拟托管。
为了满足此 `Ingress` 资源，需要一个入口控制器来侦听对 `Ingress` 资源进行的任何更改并配置负载均衡器策略。

借助应用程序网关入口控制器 (AGIC)，可将 [Azure 应用程序网关](https://azure.microsoft.com/services/application-gateway/)用作 [Azure Kubernetes 服务](https://azure.microsoft.com/services/kubernetes-service/)（也称 AKS 群集）的入口。

### <a name="can-a-single-ingress-controller-instance-manage-multiple-application-gateways"></a>单个入口控制器实例是否可以管理多个应用程序网关？

目前，入口控制器的一个实例只能关联到一个应用程序网关。

### <a name="why-is-my-aks-cluster-with-kubenet-not-working-with-agic"></a>为什么我的带 kubenet 的 AKS 群集不能与 AGIC 一起使用？

AGIC 会尝试自动将路由表资源关联到应用程序网关子网，但 AGIC 可能会因缺少权限而失败。 如果 AGIC 无法将路由表关联到应用程序网关子网，则 AGIC 日志中会记录一个相应的错误来描述此问题。在这种情况下，必须手动将 AKS 群集创建的路由表关联到应用程序网关的子网。 有关详细信息，请参阅 [支持的用户定义路由](configuration-infrastructure.md#supported-user-defined-routes)。

### <a name="can-i-connect-my-aks-cluster-and-application-gateway-in-separate-virtual-networks"></a>是否可以将不同的虚拟网络中的 AKS 群集和应用程序网关连接在一起？ 

可以，前提是这些虚拟网络已对等互连且其地址空间不重叠。 如果运行带 kubenet 的 AKS，请确保将 AKS 生成的路由表关联到应用程序网关子网。 

### <a name="what-features-are-not-supported-on-the-agic-add-on"></a>AGIC 加载项不支持哪些功能？ 

若要了解通过 Helm 部署的 AGIC 与作为 AKS 加载项部署的 AGIC 之间的差异，请参阅[此处](ingress-controller-overview.md#difference-between-helm-deployment-and-aks-add-on)

### <a name="when-should-i-use-the-add-on-versus-the-helm-deployment"></a>何时应使用加载项部署？何时应使用 Helm 部署？ 

若要了解通过 Helm 部署的 AGIC 与作为 AKS 加载项部署的 AGIC 之间的差异，请参阅[此处](ingress-controller-overview.md#difference-between-helm-deployment-and-aks-add-on)，尤其要查看相关的表，了解通过 Helm 部署的 AGIC 支持哪些情况，作为 AKS 加载项部署的 AGIC 支持哪些情况。 一般情况下，通过 Helm 进行部署可以在正式版发布之前测试 Beta 功能和候选版本。 

### <a name="can-i-control-which-version-of-agic-will-be-deployed-with-the-add-on"></a>是否可以控制将通过加载项部署的 AGIC 版本？

否。AGIC 加载项是一项托管服务，这意味着 Microsoft 会自动将该加载项更新到最新的稳定版本。 

## <a name="diagnostics-and-logging"></a>诊断和日志记录

### <a name="what-types-of-logs-does-application-gateway-provide"></a>应用程序网关提供哪些类型的日志？

应用程序网关提供三种日志： 

* **ApplicationGatewayAccessLog**：访问日志包含提交到应用程序网关前端的每个请求。 数据包括调用方的 IP、请求的 URL、响应延迟、返回代码，以及传入和传出的字节数。它针对每个应用程序网关包含一条记录。
* **ApplicationGatewayPerformanceLog**：性能日志捕获每个应用程序网关的性能信息。 信息包括吞吐量（以字节为单位）、服务的请求总数、失败的请求计数，以及正常和不正常的后端实例计数。
* **ApplicationGatewayFirewallLog**：对于使用 WAF 配置的应用程序网关，防火墙日志包含通过检测模式或阻止模式记录的请求。

所有日志每 60 秒收集一次。 有关详细信息，请参阅[应用程序网关的后端运行状况、诊断日志和指标](application-gateway-diagnostics.md)。

### <a name="how-do-i-know-if-my-backend-pool-members-are-healthy"></a>如何知道后端池成员是否正常？

可以使用 PowerShell cmdlet `Get-AzApplicationGatewayBackendHealth` 或门户来确认运行状况。 有关详细信息，请参阅[应用程序网关诊断](application-gateway-diagnostics.md)。

### <a name="whats-the-retention-policy-for-the-diagnostic-logs"></a>诊断日志的保留策略是什么？

诊断日志将发往客户的存储帐户。 客户可以根据偏好设置保留策略。 此外，可将诊断日志发送到事件中心或 Azure Monitor 日志。 有关详细信息，请参阅[应用程序网关诊断](application-gateway-diagnostics.md)。

### <a name="how-do-i-get-audit-logs-for-application-gateway"></a>如何获取应用程序网关的审核日志？

在门户中应用程序网关的菜单边栏选项卡上，选择“活动日志”即可访问审核日志。 

### <a name="can-i-set-alerts-with-application-gateway"></a>是否可以使用应用程序网关设置警报？

是的。 在应用程序网关中，警报是针对指标配置的。 有关详细信息，请参阅[应用程序网关指标](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics)和[接收警报通知](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)。

### <a name="how-do-i-analyze-traffic-statistics-for-application-gateway"></a>如何分析应用程序网关的流量统计信息？

可通过多种方式查看和分析访问日志。 可以使用 Azure Monitor 日志、Excel、Power BI 等。

还可以使用一个资源管理器模板，针对应用程序网关访问日志安装和运行常用的 [GoAccess](https://goaccess.io/) 日志分析器。 GoAccess 提供宝贵的 HTTP 流量统计信息，例如唯一访问者、请求的文件、主机、操作系统、浏览器和 HTTP 状态代码。 有关详细信息，请参阅 GitHub 中的[资源管理器模板文件夹中的自述文件](https://aka.ms/appgwgoaccessreadme)。

### <a name="what-could-cause-backend-health-to-return-an-unknown-status"></a>有哪些原因可能会导致后端运行状况返回未知状态？

通常，如果对后端的访问被应用程序网关子网中的网络安全组 (NSG)、自定义 DNS 或用户定义的路由 (UDR) 阻止，则会看到未知状态。 有关详细信息，请参阅[应用程序网关的后端运行状况、诊断日志记录和指标](application-gateway-diagnostics.md)。

### <a name="is-there-any-case-where-nsg-flow-logs-wont-show-allowed-traffic"></a>是否有 NSG 流日志不显示允许的流量的情况？

是的。 如果配置与以下情况匹配，则 NSG 流日志中不会显示允许的流量：
- 已部署了应用程序网关 v2
- 应用程序网关子网上有 NSG
- 已在该 NSG 上启用 NSG 流日志

## <a name="next-steps"></a>后续步骤

若要详细了解应用程序网关，请参阅[什么是 Azure 应用程序网关？](overview.md)。
