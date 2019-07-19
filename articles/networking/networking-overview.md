---
title: Azure 网络 | Microsoft Docs
description: 了解 Azure 中的网络服务及其功能。
services: networking
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/17/2019
ms.author: kumud
ms.openlocfilehash: 759b61e5fb444643bf83e1cca47b6f7152a96590
ms.sourcegitcommit: 770b060438122f090ab90d81e3ff2f023455213b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/17/2019
ms.locfileid: "68305653"
---
# <a name="azure-networking"></a>Azure 网络

Azure 中的网络服务提供各种网络功能, 这些功能可一起使用, 也可单独使用。 请单击以下任一重要功能了解更多相关信息：
- [**连接服务**](#connect):使用 Azure 虚拟网络 (VNet)、虚拟 WAN、ExpressRoute、VPN 网关、Azure DNS 或 Azure 堡垒中的任何或一系列网络服务来连接 Azure 资源和本地资源。
- [**应用程序保护服务**](#protect)使用 Azure 中的这些网络服务的任意或组合保护应用程序-DDoS 保护、防火墙、网络安全组、Web 应用程序防火墙或虚拟网络终结点。
- [**应用程序交付服务**](#deliver)使用 Azure-内容交付网络 (CDN)、Azure 前门服务、流量管理器、应用程序网关或负载均衡器中的任何或一系列网络服务来提供 Azure 网络中的应用程序。
- [**网络监视**](#monitor)–使用 Azure 中的这些网络服务的任意或组合监视网络资源-网络观察程序、ExpressRoute 监视器、Azure Monitor 或 VNet 终端访问点 (点击)。

## <a name="connect"></a>连接服务
 
本部分介绍在 Azure 资源之间提供连接、从本地网络连接到 Azure 资源的服务, 以及在 Azure 虚拟网络、ExpressRoute、VPN 网关、虚拟 WAN、DNS 和 Azure 中分支到分支连接的服务起.

|服务|为什么要使用？|方案|
|---|---|---|
|[虚拟网络](#vnet)|使 Azure 资源能够安全地与其他网络、internet 和本地网络通信。| <p>[筛选网络流量](../virtual-network/tutorial-filter-network-traffic.md)</p> <p>[路由网络流量](../virtual-network/tutorial-create-route-table-portal.md)</p> <p>[限制对资源的网络访问](../virtual-network/tutorial-restrict-network-access-to-resources.md)</p> <p>[连接虚拟网络](../virtual-network/tutorial-connect-virtual-networks-portal.md)</p>|
|[ExpressRoute](#expressroute)|通过连接服务提供商所提供的专用连接, 将本地网络扩展到 Microsoft 云。|<p>[创建和修改 ExpressRoute 线路](../expressroute/expressroute-howto-circuit-portal-resource-manager.md)</p> <p>[创建和修改 ExpressRoute 线路的对等互连](../expressroute/expressroute-howto-routing-portal-resource-manager.md)</p> <p>[将 VNet 链接到 ExpressRoute 线路](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)</p> <p>[配置和管理 ExpressRoute 线路的路由筛选器](../expressroute/how-to-routefilter-portal.md)</p>|
|[VPN 网关](#vpngateway)|通过公共 Internet 在 Azure 虚拟网络与本地位置之间发送加密流量。|<p>[站点到站点连接](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)</p> <p>[VNet 到 VNet 连接](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)</p> <p>[点到站点连接](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md)</p>|
|[虚拟 WAN](#virtualwan)|优化与 Azure 的分支连接并实现其自动化。 Azure 区域充当可以选择将分支连接到的中心。|<p>[站点到站点连接](../virtual-wan/virtual-wan-site-to-site-portal.md), [ExpressRoute 连接](../virtual-wan/virtual-wan-expressroute-portal.md)</p> <p>[点到站点连接](../virtual-wan/virtual-wan-point-to-site-portal.md)</p> |
|[Azure DNS](#dns)|承载通过使用 Microsoft Azure 基础结构提供名称解析的 DNS 域。|<p>[在 Azure DNS 中托管域](../dns/dns-delegate-domain-azure-dns.md)</p><p>[为 web 应用创建 DNS 记录](../dns/dns-web-sites-custom-domain.md)</p> <p>[为流量管理器创建别名记录](../dns/tutorial-alias-tm.md)</p> <p>[为公共 IP 地址创建别名记录](../dns/tutorial-alias-pip.md)</p> <p>[为区域资源记录创建别名记录](../dns/tutorial-alias-rr.md)</p>|
|[Azure 堡垒 (预览版)](#bastion)|直接在 Azure 门户中配置与虚拟机之间通过 SSL 的安全、无缝 RDP/SSH 连接。 通过 Azure 堡垒连接时, 虚拟机不需要公共 IP 地址|<p>[创建 Azure 堡垒主机](../bastion/bastion-create-host-portal.md)</p><p>[使用 SSH 连接到 Linux VM](../bastion/bastion-connect-vm-ssh.md)</p><p>[使用 RDP 连接到 Windows VM](/bastion/bastion-connect-vm-rdp.md)</p>|
||||


### <a name="vnet"></a>虚拟网络

Azure 虚拟网络 (VNet) 是 Azure 中专用网络的基本构建块。 你可以使用 Vnet 来执行以下操作:
- **Azure 资源之间的通信**:可以将 VM 和多个其他类型的 Azure 资源部署到虚拟网络，如 Azure 应用服务环境、Azure Kubernetes 服务 (AKS) 和 Azure 虚拟机规模集。 若要查看可部署到虚拟网络的 Azure 资源的完整列表，请参阅[虚拟网络服务集成](../virtual-network/virtual-network-for-azure-services.md)。
- **相互通信**:可以互相连接虚拟网络，使虚拟网络中的资源能够通过虚拟网络对等互连相互进行通信。 连接的虚拟网络可以在相同或不同的 Azure 区域中。 有关详细信息，请参阅[虚拟网络对等互连](../virtual-network/virtual-network-peering-overview.md)。
- **与 Internet 通信**:默认情况下，VNet 中的所有资源都可以与 Internet 进行出站通信。 可以通过分配公共 IP 地址或公共负载均衡器来与资源进行入站通信。 你还可以使用[公共 IP 地址](../virtual-network/virtual-network-public-ip-address.md)或公共[负载均衡器](../load-balancer/load-balancer-overview.md)来管理你的出站连接。
- **与本地网络通信**:可以使用[VPN 网关](../vpn-gateway/vpn-gateway-about-vpngateways.md)或[ExpressRoute](../expressroute/expressroute-introduction.md)将本地计算机和网络连接到虚拟网络。

有关详细信息, 请参阅[什么是 Azure 虚拟网络？](../virtual-network/virtual-networks-overview.md)。

### <a name="expressroute"></a>ExpressRoute
ExpressRoute 使你可以通过连接服务提供商所提供的专用连接将本地网络扩展到 Microsoft 云。 此连接是专用连接。 流量不经过 Internet。 使用 ExpressRoute 可与 Microsoft Azure、Office 365 和 Dynamics 365 等 Microsoft 云服务建立连接。  有关详细信息, 请参阅[什么是 ExpressRoute？](../expressroute/expressroute-introduction.md)。

![Azure ExpressRoute](./media/networking-overview/expressroute-connection-overview.png)

### <a name="vpngateway"></a>VPN 网关
VPN 网关可帮助你从本地位置创建到虚拟网络的加密跨界连接, 或在 Vnet 之间创建加密连接。 VPN 网关连接可以使用不同的配置, 例如站点到站点连接、点到站点连接或 VNet 到 VNet。
下图演示了到同一虚拟网络的多个站点到站点 VPN 连接。

![站点到站点 Azure VPN 网关连接](./media/networking-overview/vpngateway-multisite-connection-diagram.png)

有关不同类型的 VPN 连接的详细信息, 请参阅[Vpn 网关](../vpn-gateway/vpn-gateway-about-vpngateways.md)。

### <a name="virtualwan"></a>虚拟 WAN
Azure Virtual WAN 是一种网络服务，提供到 Azure 并穿过该服务的经优化的自动分支连接。 Azure 区域充当可以选择将分支连接到的中心。 还可以利用 Azure 主干连接分支，享用分支到 VNet 的连接。 Azure 虚拟 WAN 汇集了许多 Azure 云连接服务, 例如站点到站点 VPN、ExpressRoute、点到站点用户 VPN, 并将其转换为单个操作接口。 通过使用虚拟网络连接建立与 Azure VNet 的连接。 有关详细信息, 请参阅[什么是 Azure 虚拟 WAN？](../virtual-wan/virtual-wan-about.md)。

![虚拟 WAN 示意图](./media/networking-overview/virtualwan1.png)

### <a name="dns"></a>Azure DNS
Azure DNS 是 DNS 域的托管服务，它使用 Microsoft Azure 基础结构提供名称解析。 通过在 Azure 中托管域，可以使用与其他 Azure 服务相同的凭据、API、工具和计费来管理 DNS 记录。 有关详细信息, 请参阅[什么是 Azure DNS？](../dns/dns-overview.md)。

### <a name="bastion"></a>Azure 堡垒 (预览版)
Azure Bastion 服务是的一种新型的完全托管平台 PaaS 服务，可在虚拟网络中进行预配。 可通过 SSL 直接在 Azure 门户中实现与虚拟机之间的安全、无缝 RDP/SSH 连接。 在你通过 Azure Bastion 连接时，你的虚拟机无需公共 IP 地址。 有关详细信息, 请参阅[什么是 Azure 堡垒？](/bastion/bastion-overview.md)。

![Azure 堡垒体系结构](./media/networking-overview/architecture.png)


## <a name="protect"></a>应用程序保护服务

本部分介绍 Azure 中的网络服务, 这些服务可帮助保护网络资源-DDoS 保护、Web 应用程序防火墙、Azure 防火墙、网络安全组和服务终结点。

|服务|为什么要使用？|应用场景|
|---|---|---|
|[DDoS 保护](#ddosprotection) |针对额外的 IP 流量收费提供保护的应用程序的高可用性|[管理 Azure DDoS 保护](../virtual-network/manage-ddos-protection.md)|
|[Web 应用程序防火墙](#waf)|<p>[具有应用程序网关的 AZURE WAF](../application-gateway/waf-overview.md)为公用和专用地址空间中的实体提供区域保护</p><p>[带有前门的 AZURE WAF](../frontdoor/waf-overview.md)提供网络边缘到公共终结点的保护。</p>|<p>[配置机器人保护规则](../frontdoor/waf-front-door-policy-configure-bot-protection.md)</p> <p>[配置自定义响应代码](../frontdoor/waf-front-door-configure-custom-response-code.md)</p> <p>[配置 IP 限制规则](../frontdoor/waf-front-door-configure-ip-restriction.md)</p> <p>[配置速率限制规则](../frontdoor/waf-front-door-rate-limit-powershell.md)</p> |
|[Azure 防火墙](#firewall)|Azure 防火墙是托管的基于云的网络安全服务，可保护 Azure 虚拟网络资源。 它是一个服务形式的完全有状态防火墙，具有内置的高可用性和不受限制的云可伸缩性。|<p>[在 Vnet 中部署 Azure 防火墙](../firewall/tutorial-firewall-deploy-portal.md)</p> <p>[-在混合网络中部署 Azure 防火墙](../firewall/tutorial-hybrid-ps.md)</p> <p>[用 Azure Firewall DNAT 筛选入站流量](../firewall/tutorial-firewall-dnat.md)</p>|
|[网络安全组](#nsg)|所有网络通信流的 VM/子网上的完全精细分布式结束节点控件|[使用网络安全组筛选网络流量](../virtual-network/tutorial-filter-network-traffic.md)|
|[虚拟网络服务终结点](#serviceendpoints)|使你能够将对某些 Azure 服务资源的网络访问限制为虚拟网络子网|[限制 PaaS 资源的网络访问](../virtual-network/tutorial-restrict-network-access-to-resources-powershell.md)|
|||
### <a name="ddosprotection"></a>DDoS 保护 
[Azure DDoS 防护](../virtual-network/manage-ddos-protection.md)针对最复杂的 DDoS 威胁提供对策。 此服务为你的虚拟网络中部署的应用程序和资源提供增强的 DDoS 缓解功能。 此外, 使用 Azure DDoS 防护的客户有权访问 DDoS 快速响应支持, 以在主动攻击期间与 DDoS 专家联系。

![DDoS 保护](./media/networking-overview/ddos-protection.png)

### <a name="waf"></a>Web 应用程序防火墙

Azure Web 应用程序防火墙 (WAF) 为 web 应用程序提供保护, 使其免受常见 web 攻击和漏洞 (如 SQL 注入和跨站点脚本编写) 的攻击。 Azure WAF 提供了通过托管规则 OWASP 前10个漏洞的现成保护。 此外, 客户还可以配置自定义规则, 这些规则是客户托管规则, 用于根据源 IP 范围提供额外的保护, 以及请求属性 (如标头、cookie、表单数据字段或查询字符串参数)。

客户可以选择部署[AZURE WAF 应用程序网关](../application-gateway/waf-overview.md), 为公用和专用地址空间中的实体提供区域保护。 客户还可以选择部署[具有前门的 AZURE WAF](../frontdoor/waf-overview.md) , 它提供网络边缘到公共终结点的保护。


### <a name="firewall"></a>Azure 防火墙
Azure 防火墙是托管的基于云的网络安全服务，可保护 Azure 虚拟网络资源。 使用 Azure 防火墙, 可以跨订阅和虚拟网络集中创建、强制和记录应用程序和网络连接策略。 Azure 防火墙对虚拟网络资源使用静态公共 IP 地址，使外部防火墙能够识别来自你的虚拟网络的流量。 

有关 Azure 防火墙的详细信息, 请参阅[Azure 防火墙文档](../firewall/overview.md)。

![防火墙概述](./media/networking-overview/firewall-threat.png)

### <a name="nsg"></a>网络安全组
可以使用网络安全组筛选进出 Azure 虚拟网络中的 Azure 资源的网络流量。 有关详细信息, 请参阅[安全性概述](../virtual-network/security-overview.md)。

### <a name="serviceendpoints"></a>服务终结点
虚拟网络 (VNet) 服务终结点可通过直接连接将 VNet 的虚拟网络专用地址空间和标识扩展到 Azure 服务。 使用终结点可以保护关键的 Azure 服务资源，只允许在客户自己的虚拟网络中对其进行访问。 从 VNet 发往 Azure 服务的流量始终保留在 Microsoft Azure 主干网络中。 有关详细信息，请参阅[虚拟网络服务终结点](../virtual-network/virtual-network-service-endpoints-overview.md)。

![虚拟网络服务终结点](./media/networking-overview/vnet-service-endpoints-overview.png)

## <a name="deliver"></a>应用程序交付服务

本部分介绍 Azure 中的网络服务, 该服务可帮助提供应用程序-内容交付网络 (CDN)、Azure 前门服务、流量管理器、应用程序网关和负载均衡器。

|服务|为什么要使用？|应用场景|
|---|---|---|
|[内容交付网络](#cdn)|向用户提供高带宽内容。 Cdn 将缓存的内容存储在靠近最终用户的存在点 (POP) 位置中的边缘服务器上, 以最大程度地减少延迟|<p>[向 web 应用添加 CDN](../cdn/cdn-add-to-web-app.md)</p> <p>[-通过 HTTPS 使用 Azure CDN 自定义域访问存储 blob](..//cdn/cdn-storage-custom-domain-https.md)</p> <p>[将自定义域添加到 Azure CDN 终结点](../cdn/cdn-map-content-to-custom-domain.md)</p> <p>[在 Azure CDN 自定义域上配置 HTTPS](../cdn/cdn-custom-ssl.md?tabs=option-1-default-enable-https-with-a-cdn-managed-certificate)</p>|
|[Azure 前门服务](#frontdoor)|使你能够通过优化以实现最佳性能和用于实现高可用性的即时全局故障转移来定义、管理和监视你的 web 流量的全局路由。|<p>[将自定义域添加到 Azure 前门服务](../frontdoor/front-door-custom-domain.md)</p> <p>[在前门自定义域上配置 HTTPS](../frontdoor/front-door-custom-domain-https.md)</p><p>[设置异地筛选 Web 应用程序防火墙策略](../frontdoor/front-door-tutorial-geo-filtering.md)|
|[流量管理器](#trafficmanager)|跨全球 Azure 区域将基于 DNS 的流量分配给服务, 同时提供高可用性和响应能力|<p> [路由流量以降低延迟](../traffic-manager/tutorial-traffic-manager-improve-website-response.md)</p><p>[将流量路由到优先终结点](../traffic-manager/traffic-manager-configure-priority-routing-method.md)</p><p> [使用加权的终结点控制流量](../traffic-manager/tutorial-traffic-manager-weighted-endpoint-routing.md)</p><p>[根据终结点的地理位置路由流量](../traffic-manager/traffic-manager-configure-geographic-routing-method.md)</p> <p> [根据用户的子网路由流量](../traffic-manager/tutorial-traffic-manager-subnet-routing.md)</p>|
|[负载均衡器](#loadbalancer)|通过将流量路由到可用性区域和 Vnet 来提供区域负载平衡。 通过在资源之间路由流量并构建区域应用程序, 提供内部负载平衡。|<p> [对传入 VM 的 Internet 流量进行负载均衡](../load-balancer/tutorial-load-balancer-standard-manage-portal.md)</p> <p>[对虚拟网络中 Vm 之间的流量进行负载均衡](../load-balancer/tutorial-load-balancer-basic-internal-portal.md)<p>[端口将流量转发到特定 Vm 上的特定端口](../load-balancer/tutorial-load-balancer-port-forwarding-portal.md)</p><p> [配置负载平衡和出站规则](../load-balancer/configure-load-balancer-outbound-cli.md)</p>|
|[应用程序网关](#applicationgateway)|Azure 应用程序网关是一种 Web 流量负载均衡器，可用于管理 Web 应用程序的流量。|<p>[Azure 应用程序网关的直接 web 流量](../application-gateway/quick-create-portal.md)</p><p>[配置带有 SSL 终端的应用程序网关](../application-gateway/create-ssl-portal.md)</p><p>[创建支持基于 URL 路径进行重定向的应用程序网关](../application-gateway/create-url-route-portal.md) </p>|
|

### <a name="cdn"></a>内容交付网络
Azure 内容分发网络 (CDN) 为开发人员提供了一个全局解决方案，通过在世界各地按特定策略放置的物理节点缓存内容来快速分发高带宽内容。 有关 Azure CDN 的详细信息, 请参阅[Azure 内容分发网络](../cdn/cdn-overview.md)

![Azure CDN](./media/networking-overview/cdn-overview.png)

### <a name="frontdoor"></a>Azure 前门服务
在 Azure Front Door 服务中可以进行优化以实现最佳性能以及进行即时全球故障转移以实现高可用性，并以此定义、管理和监视 Web 流量的全局路由。 使用 Front Door，可将全球（多区域）消费型和企业应用程序转化成可靠、高性能、个性化的现代应用程序、API 和内容，供 Azure 全球受众访问。 有关详细信息, 请参阅[Azure 前门](../frontdoor/front-door-overview.md)。


### <a name="trafficmanager"></a>流量管理器

Azure 流量管理器是一种基于 DNS 的流量负载均衡器，可以在全球 Azure 区域内以最佳方式向服务分发流量，同时提供高可用性和响应性。 流量管理器提供了一系列流量路由方法, 用于分配优先级、加权、性能、地理、多值或子网等流量。 有关流量路由方法的详细信息, 请参阅[流量管理器路由方法](../traffic-manager/traffic-manager-routing-methods.md)。

下图显示了具有流量管理器的基于终结点优先级的路由:

![Azure 流量管理器的“优先级”流量路由方法](./media/networking-overview/priority.png)

有关流量管理器的详细信息, 请参阅[什么是 Azure 流量管理器？](../traffic-manager/traffic-manager-overview.md)

### <a name="loadbalancer"></a>负载均衡器
Azure 负载均衡器为所有 UDP 和 TCP 协议提供高性能、低延迟的第 4 层负载均衡。 它管理入站和出站连接。 可以配置公共和内部负载均衡终结点。 可以定义规则，以便将入站连接映射到后端池目标，并在其中包含 TCP 和 HTTP 运行状况探测选项来管理服务的可用性。 若要了解有关负载均衡器的详细信息，请参阅[负载均衡器概述](../load-balancer/load-balancer-overview.md)一文。

下图显示了利用外部和内部负载均衡器的面向 Internet 的多层应用程序：

![Azure 负载均衡器示例](./media/networking-overview/IC744147.png)


### <a name="applicationgateway"></a>应用程序网关
Azure 应用程序网关是一种 Web 流量负载均衡器，可用于管理 Web 应用程序的流量。 它是应用程序传送控制器 (ADC) 作为服务, 为应用程序提供各种第7层负载均衡功能。 有关详细信息, 请参阅[什么是 Azure 应用程序的网关？](../application-gateway/overview.md)。

下图显示了具有应用程序网关的基于 url 路径的路由。

![应用程序网关示例](./media/networking-overview/figure1-720.png)

## <a name="monitor"></a>网络监视服务
本部分介绍 Azure 中的网络服务, 它们可帮助监视网络资源-网络观察程序、ExpressRoute 监视器、Azure Monitor 和虚拟网络分流。

|服务|为什么要使用？|应用场景|
|---|---|---|
|[网络观察程序](#networkwatcher)|帮助监视和解决连接问题, 帮助诊断 VPN、NSG 和路由问题, 捕获 VM 上的数据包, 使用 Azure Functions 和逻辑应用自动触发诊断工具|<p>[诊断 VM 流量筛选器问题](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md)</p><p>[诊断 VM 路由问题](../network-watcher/diagnose-vm-network-routing-problem.md)</p><p>[监视 Vm 之间的通信](../network-watcher/connection-monitor.md)</p><p>[诊断网络之间的通信问题](../network-watcher/diagnose-communication-problem-between-networks.md)</p><p>[记录传入和传出 VM 的网络流量](../network-watcher/network-watcher-nsg-flow-logging-portal.md)</p>|
|[ExpressRoute 监视器](#expressroutemonitor)|提供对网络性能、可用性和利用率的实时监视, 有助于自动发现网络拓扑, 提供更快的故障隔离, 检测暂时性的网络问题, 帮助分析历史网络性能特征, 支持多订阅|<p>[为 ExpressRoute 配置网络性能监视器](../expressroute/how-to-npm.md)</p><p>[ExpressRoute 监视、指标和警报](../expressroute/expressroute-monitoring-metrics-alerts.md)</p>|
|[Azure Monitor](#azuremonitor)|帮助你了解应用程序的执行方式, 并主动识别影响它们的问题及其依赖的资源。|<p>[流量管理器指标和警报](../traffic-manager/traffic-manager-metrics-alerts.md)</p><p>[适用于标准负载均衡器的 Azure monitor 诊断](../load-balancer/load-balancer-standard-diagnostics.md)</p><p>[监视 Azure 防火墙日志和指标](../firewall/tutorial-diagnostics.md)</p><p>[Azure web application firewall monitoring and logging](../frontdoor/waf-front-door-monitor.md)（Azure Web 应用程序防火墙监视和日志记录）</p>|
|[虚拟网络点击](#vnettap)|提供对数据包收集器的虚拟机网络流量的连续流式处理, 启用网络和应用程序性能管理解决方案和安全分析工具|[创建 VNet 点击资源](../virtual-network/tutorial-tap-virtual-network-cli.md)|
|

### <a name="networkwatcher"></a>网络观察程序
Azure 网络观察程序提供所需的工具用于监视、诊断 Azure 虚拟网络中的资源、查看其指标，以及为其启用或禁用日志。 有关详细信息, 请参阅[什么是网络观察程序？](../network-watcher/network-watcher-monitoring-overview.md?toc=%2fazure%2fnetworking%2ftoc.json)。
### <a name="expressroutemonitor"></a>ExpressRoute 监视器
若要了解如何查看 ExpressRoute 线路指标、诊断日志和警报, 请参阅[expressroute 监视、指标和警报](../expressroute/expressroute-monitoring-metrics-alerts.md?toc=%2fazure%2fnetworking%2ftoc.json)。
### <a name="azuremonitor"></a>Azure Monitor
Azure Monitor 提供用于收集、分析和处理来自云与本地环境的遥测数据的综合解决方案，可将应用程序的可用性和性能最大化。 它可以帮助你了解应用程序的性能，并主动识别影响应用程序及其所依赖资源的问题。 有关详细信息, 请参阅[Azure Monitor 概述](../azure-monitor/overview.md?toc=%2fazure%2fnetworking%2ftoc.json)。
### <a name="vnettap"></a>虚拟网络点击
通过 Azure 虚拟网络 TAP（终端接入点），可让你持续将虚拟机网络流量流式传输到网络数据包收集器或分析工具。 收集器或分析工具由[网络虚拟设备](https://azure.microsoft.com/solutions/network-appliances/)合作伙伴提供。 

下图显示虚拟网络 TAP 的工作原理。 

![虚拟网络 TAP 的工作原理](./media/networking-overview/virtual-network-tap-architecture.png)

有关详细信息, 请参阅[什么是虚拟网络点击](../virtual-network/virtual-network-tap-overview.md)。

## <a name="next-steps"></a>后续步骤

- 完成[创建首个虚拟网络](../virtual-network/quick-create-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)一文中的步骤，创建自己的首个虚拟网络，并将几个 VM 连接到此网络。
- 完成[配置点到站点连接一文](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)中的步骤, 将计算机连接到 VNet。
- 完成[创建面向 Internet 的负载均衡器](../load-balancer/load-balancer-get-started-internet-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)一文中的步骤，对发往公共服务器的 Internet 流量进行负载均衡。
 
 
   
