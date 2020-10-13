---
title: Azure 网络服务概述
description: 了解 Azure 中的网络服务，包括连接、应用程序保护、应用程序交付和网络监视服务。
services: networking
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 09/29/2020
ms.author: kumud
ms.openlocfilehash: 791a248c8fb10bc4db483451971d452a359c8a07
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91569245"
---
# <a name="azure-networking-services-overview"></a>Azure 网络服务概述

Azure 中的网络服务提供可以搭配使用或单独使用的各种网络功能。 请单击以下任一重要功能了解更多相关信息：
- [**连接服务**](#connect)：使用 azure 中的这些网络服务的任意或组合（ (VNet) 、虚拟 WAN、EXPRESSROUTE、VPN 网关、虚拟网络 NAT 网关、Azure DNS、对等互连服务和 Azure 堡垒）连接 azure 资源和本地资源。
- [**应用程序保护服务**](#protect)：使用 Azure 中的这些网络服务的任意或组合保护应用程序-私有链接、DDoS 保护、防火墙、网络安全组、Web 应用程序防火墙和虚拟网络终结点。
- [**应用程序传递服务**](#deliver)：使用 azure 中的这些网络服务的任意或组合（ (CDN) 、Azure 前门服务、流量管理器、应用程序网关、Internet 分析器和负载均衡器）在 azure 网络中提供应用程序。
- [**网络监视**](#monitor)：使用 Azure 中的这些网络服务的任意或组合监视网络资源-网络观察程序、ExpressRoute 监视器、Azure Monitor 或 VNet 终端访问点 (点击 ") "。

## <a name="connectivity-services"></a><a name="connect"></a>连接服务
 
本部分介绍了在 Azure 资源之间提供连接、从本地网络连接到 Azure 资源，以及在 Azure 虚拟网络中分支到分支连接的服务 (VNet) 、虚拟 WAN、ExpressRoute、VPN 网关、虚拟网络 NAT 网关、Azure DNS、Azure 对等互连服务和 Azure 堡垒。

|服务|为何使用此类服务？|方案|
|---|---|---|
|[虚拟网络](#vnet)|可让 Azure 资源以安全方式彼此通信、与 Internet 通信，以及与本地网络通信。| <p>[筛选网络流量](../virtual-network/tutorial-filter-network-traffic.md)</p> <p>[路由网络流量](../virtual-network/tutorial-create-route-table-portal.md)</p> <p>[限制对资源的网络访问](../virtual-network/tutorial-restrict-network-access-to-resources.md)</p> <p>[连接虚拟网络](../virtual-network/tutorial-connect-virtual-networks-portal.md)</p>|
|[ExpressRoute](#expressroute)|通过连接服务提供商所提供的专用连接，将本地网络扩展到 Microsoft 云。|<p>[创建和修改 ExpressRoute 线路](../expressroute/expressroute-howto-circuit-portal-resource-manager.md)</p> <p>[创建和修改 ExpressRoute 线路的对等互连](../expressroute/expressroute-howto-routing-portal-resource-manager.md)</p> <p>[将 VNet 链接到 ExpressRoute 线路](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)</p> <p>[配置和管理 ExpressRoute 线路的路由筛选器](../expressroute/how-to-routefilter-portal.md)</p>|
|[VPN 网关](#vpngateway)|通过公共 Internet 在 Azure 虚拟网络与本地位置之间发送加密流量。|<p>[站点到站点连接](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)</p> <p>[VNet 到 VNet 连接](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)</p> <p>[点到站点连接](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md)</p>|
|[虚拟 WAN](#virtualwan)|优化并自动化到 Azure 和通过 Azure 的分支连接。 Azure 区域充当可以选择将分支连接到的中心。|<p>[站点到站点连接](../virtual-wan/virtual-wan-site-to-site-portal.md)， [ExpressRoute 连接](../virtual-wan/virtual-wan-expressroute-portal.md)</p>|
|[Azure DNS](#dns)|托管使用 Microsoft Azure 基础结构提供名称解析的 DNS 域。|<p>[在 Azure DNS 中托管域](../dns/dns-delegate-domain-azure-dns.md)</p><p>[为 Web 应用创建 DNS 记录](../dns/dns-web-sites-custom-domain.md)</p> <p>[为流量管理器创建别名记录](../dns/tutorial-alias-tm.md)</p> <p>[为公共 IP 地址创建别名记录](../dns/tutorial-alias-pip.md)</p> <p>[为区域资源记录创建别名记录](../dns/tutorial-alias-rr.md)</p>|
|[Azure Bastion](#bastion)|直接在 Azure 门户 over TLS 中，配置与虚拟机的安全且无缝的 RDP/SSH 连接。 通过 Azure 堡垒连接时，虚拟机不需要公共 IP 地址|<p>[创建 Azure Bastion 主机](../bastion/bastion-create-host-portal.md)</p><p>[使用 SSH 连接到 Linux VM](../bastion/bastion-connect-vm-ssh.md)</p><p>[使用 RDP 连接到 Windows VM](../bastion/bastion-connect-vm-rdp.md)</p>|
|[虚拟网络 NAT 网关](#nat)|创建 NAT 网关，为虚拟机提供出站连接。|<p>[创建 NAT 网关](../virtual-network/quickstart-create-nat-gateway-portal.md)</p>|
|[Azure 对等互连服务](#azurepeeringservice)|通过公共网络与服务提供商协作，以获得最佳和可靠的路由。|<p>[注册 Azure 对等互连服务](../peering-service/azure-portal.md)</p>|
| [Azure Edge Zone](#edge-zones)| 将 Vm、容器和其他服务部署到边缘区域，以解决低延迟和高吞吐量要求。| <p>[带有电信公司的边缘区域](edge-zones-overview.md#carrier)<p> <p>[专用边缘区域](edge-zones-overview.md#private-edge-zones)</p>|
| [Azure Orbital](#orbital)| 与内门或附属星座、下行和上行数据通信，在云中处理数据，在独特的方案中将服务与 Azure 服务链在一起，并为客户生成产品。  | <p>[全局通信](azure-orbital-overview.md#global-communication)</p>  <p>[合作伙伴地面](azure-orbital-overview.md#digital-transformation)</p>|
||||

### <a name="virtual-network"></a><a name="vnet"></a>虚拟网络

Azure 虚拟网络 (VNet) 是 Azure 中专用网络的基本构建块。 使用 VNet 可以：
- **在 Azure 资源之间通信**：可以将 VM 和多个其他类型的 Azure 资源部署到虚拟网络，如 Azure 应用服务环境、Azure Kubernetes 服务 (AKS) 和 Azure 虚拟机规模集。 若要查看可部署到虚拟网络的 Azure 资源的完整列表，请参阅[虚拟网络服务集成](../virtual-network/virtual-network-for-azure-services.md)。
- **相互通信**：可以互相连接虚拟网络，使虚拟网络中的资源能够通过虚拟网络对等互连相互进行通信。 连接的虚拟网络可以在相同或不同的 Azure 区域中。 有关详细信息，请参阅[虚拟网络对等互连](../virtual-network/virtual-network-peering-overview.md)。
- **与 Internet 通信**：默认情况下，VNet 中的所有资源都可以与 Internet 进行出站通信。 可以通过分配公共 IP 地址或公共负载均衡器来与资源进行入站通信。 还可以使用[公共 IP 地址](../virtual-network/virtual-network-public-ip-address.md)或公共[负载均衡器](../load-balancer/load-balancer-overview.md)来管理出站连接。
- **与本地网络通信**：可以使用 [VPN 网关](../vpn-gateway/vpn-gateway-about-vpngateways.md)或 [ExpressRoute](../expressroute/expressroute-introduction.md) 将本地计算机和网络连接到虚拟网络。

有关详细信息，请参阅[什么是 Azure 虚拟网络？](../virtual-network/virtual-networks-overview.md)

### <a name="expressroute"></a><a name="expressroute"></a>ExpressRoute
使用 ExpressRoute 可通过连接服务提供商所提供的专用连接，将本地网络扩展到 Microsoft 云。 此连接是专用连接。 流量不经过 Internet。 使用 ExpressRoute 可与 Microsoft Azure、Microsoft 365 和 Dynamics 365 等 Microsoft 云服务建立连接。  有关详细信息，请参阅[什么是 ExpressRoute？](../expressroute/expressroute-introduction.md)

:::image type="content" source="./media/networking-overview/expressroute-connection-overview.png" alt-text="Azure ExpressRoute" border="false":::

### <a name="vpn-gateway"></a><a name="vpngateway"></a>VPN 网关
VPN 网关可帮助你创建从本地位置到虚拟网络的加密跨界连接，或者在 VNet 之间创建加密连接。 VPN 网关连接可以使用不同的配置，例如站点到站点连接、点到站点连接或 VNet 到 VNet。
下图演示了与同一虚拟网络建立的多个站点到站点 VPN 连接。

:::image type="content" source="./media/networking-overview/vpngateway-multisite-connection-diagram.png" alt-text="Azure ExpressRoute":::

有关不同类型的 VPN 连接的详细信息，请参阅 [VPN 网关](../vpn-gateway/vpn-gateway-about-vpngateways.md)。

### <a name="virtual-wan"></a><a name="virtualwan"></a>虚拟 WAN
Azure Virtual WAN 是一种网络服务，提供到 Azure 并穿过该服务的经优化的自动分支连接。 Azure 区域充当可以选择将分支连接到的中心。 利用 Azure 主干网还可以连接分支并享用分支到 VNet 的连接。 Azure 虚拟 WAN 将许多 Azure 云连接服务（例如，站点到站点 VPN、ExpressRoute、点到站点用户 VPN）汇集到一个操作界面中。 通过使用虚拟网络连接建立与 Azure VNet 的连接。 有关详细信息，请参阅[什么是 Azure 虚拟 WAN？](../virtual-wan/virtual-wan-about.md)。

:::image type="content" source="./media/networking-overview/virtualwan1.png" alt-text="Azure ExpressRoute":::

### <a name="azure-dns"></a><a name="dns"></a>Azure DNS
Azure DNS 是 DNS 域的托管服务，它使用 Microsoft Azure 基础结构提供名称解析。 通过在 Azure 中托管域，可以使用与其他 Azure 服务相同的凭据、API、工具和计费来管理 DNS 记录。 有关详细信息，请参阅[什么是 Azure DNS？](../dns/dns-overview.md)

### <a name="azure-bastion"></a><a name="bastion"></a>Azure Bastion
Azure Bastion 服务是的一种新型的完全托管平台 PaaS 服务，可在虚拟网络中进行预配。 可通过 TLS 直接在 Azure 门户中实现与虚拟机之间的安全无缝的 RDP/SSH 连接。 在你通过 Azure Bastion 连接时，你的虚拟机无需公共 IP 地址。 有关详细信息，请参阅[什么是 Azure Bastion？](../bastion/bastion-overview.md)。

:::image type="content" source="./media/networking-overview/architecture.png" alt-text="Azure ExpressRoute":::

### <a name="virtual-network-nat-gateway"></a><a name="nat"></a>虚拟网络 NAT 网关
虚拟网络 NAT（网络地址转换）简化了虚拟网络的仅限出站 Internet 连接。 在子网中配置后，所有出站连接将使用指定的静态公共 IP 地址。 无需使用负载均衡器或将公共 IP 地址直接附加到虚拟机，即可建立出站连接。 有关详细信息，请参阅 [什么是虚拟网络 NAT 网关？](../virtual-network/nat-overview.md)。

:::image type="content" source="./media/networking-overview/flow-map.png" alt-text="Azure ExpressRoute":::

### <a name="azure-peering-service"></a><a name="azurepeeringservice"></a> Azure 对等互连服务
Azure 对等互连服务增强了客户与 Microsoft 云服务的连接，例如 Microsoft 365、Dynamics 365、软件即服务 (SaaS) 服务、Azure 或可通过公共 internet 访问的任何 Microsoft 服务。 有关详细信息，请参阅 [什么是 Azure 对等互连服务？](../peering-service/about.md)。

### <a name="azure-edge-zones"></a><a name="edge-zones"></a>Azure Edge Zone

Azure 边缘区域是 Microsoft Azure 中的一系列产品，可使数据处理靠近用户。 你可以将 Vm、容器和其他所选 Azure 服务部署到边缘区域，以解决应用程序的低延迟和高吞吐量要求。 有关详细信息，请参阅 [什么是 Azure 边缘区域？](edge-zones-overview.md)。

### <a name="azure-orbital"></a><a name="orbital"></a>Azure Orbital

Azure Orbital 是一种完全托管的基于云的地面站即服务，用于与航天器或卫星星座通信、进行下行和上行数据传输、在云中处理数据、在独特的方案中将服务与 Azure 服务进行链接，以及为客户生成产品。 此系统是在 Azure 全球基础设施和低延迟全球光纤网络的基础上构建的。 有关详细信息，请参阅 [什么是 Azure Orbital？](azure-orbital-overview.md)。

:::image type="content" source="./media/azure-orbital-overview/orbital-communications-use-flow.png" alt-text="Azure ExpressRoute":::

## <a name="application-protection-services"></a><a name="protect"></a>应用程序保护服务

本部分介绍 Azure 中的网络服务，该服务可帮助保护你的网络资源-使用 Azure 专用链接、DDoS 防护、防火墙、网络安全组、Web 应用程序防火墙和虚拟网络终结点中的任何或一组网络服务来保护你的应用程序。

|服务|为何使用此类服务？|方案|
|---|---|---|
|[DDoS 保护](#ddosprotection) |针对额外的 IP 流量收费提供保护的应用程序的高可用性|[管理 Azure DDoS 保护](../virtual-network/manage-ddos-protection.md)|
|[Web 应用程序防火墙](#waf)|<p>[具有应用程序网关的 Azure WAF](../web-application-firewall/ag/ag-overview.md) 为公共和专用地址空间中的实体提供区域性保护</p><p>[带有前门的 AZURE WAF](../web-application-firewall/afds/afds-overview.md) 提供网络边缘到公共终结点的保护。</p>|<p>[配置机器人保护规则](../frontdoor/waf-front-door-policy-configure-bot-protection.md)</p> <p>[配置自定义响应代码](../frontdoor/waf-front-door-configure-custom-response-code.md)</p> <p>[配置 IP 限制规则](../frontdoor/waf-front-door-configure-ip-restriction.md)</p> <p>[配置速率限制规则](../frontdoor/waf-front-door-rate-limit-powershell.md)</p> |
|[Azure 防火墙](#firewall)|Azure 防火墙是托管的基于云的网络安全服务，可保护 Azure 虚拟网络资源。 它是一个服务形式的完全有状态防火墙，具有内置的高可用性和不受限制的云可伸缩性。|<p>[在 Vnet 中部署 Azure 防火墙](../firewall/tutorial-firewall-deploy-portal.md)</p> <p>[- 在混合网络中部署 Azure 防火墙](../firewall/tutorial-hybrid-ps.md)</p> <p>[使用 Azure 防火墙 DNAT 筛选入站流量](../firewall/tutorial-firewall-dnat.md)</p>|
|[网络安全组](#nsg)|在 VM/子网中对所有网络流量进行完全粒度的分布式终端节点控制|[使用网络安全组筛选网络流量](../virtual-network/tutorial-filter-network-traffic.md)|
|[虚拟网络服务终结点](#serviceendpoints)|使你可以将对某些 Azure 服务资源的网络访问限制到虚拟网络子网|[限制 PaaS 资源的网络访问](../virtual-network/tutorial-restrict-network-access-to-resources-powershell.md)|
[专用链接](#privatelink)|使你能够通过虚拟网络中的专用终结点访问 Azure PaaS 服务 (例如，Azure 存储和 SQL 数据库) 和 Azure 托管的客户拥有/合作伙伴服务。|<p>[创建专用终结点](../private-link/create-private-endpoint-portal.md)</p><p>[创建专用链接服务](../private-link/create-private-link-service-portal.md)</p>|
|||

### <a name="ddos-protection"></a><a name="ddosprotection"></a>DDoS 保护 
[Azure DDoS 防护](../virtual-network/manage-ddos-protection.md) 针对最复杂的 DDoS 威胁提供对策。 此服务为你的虚拟网络中部署的应用程序和资源提供增强的 DDoS 缓解功能。 此外，使用 Azure DDoS 防护的客户有权访问 DDoS 快速响应支持，以在主动攻击期间与 DDoS 专家联系。

:::image type="content" source="./media/networking-overview/ddos-protection.png" alt-text="Azure ExpressRoute":::

### <a name="web-application-firewall"></a><a name="waf"></a>Web 应用程序防火墙

[Azure Web 应用程序防火墙](../web-application-firewall/overview.md) (WAF) 为 Web 应用程序提供保护，使其免受 SQL 注入、跨站点脚本等常见 Web 攻击和漏洞的影响。 Azure WAF 通过托管的规则针对 OWASP 识别出的 10 大漏洞提供现成保护。 此外，客户还可以配置自定义规则（客户管理的规则），用于根据源 IP 范围，以及标头、Cookie、表单数据字段或查询字符串参数等请求属性提供额外的保护。

客户可以选择部署[具有应用程序网关的 Azure WAF](../application-gateway/waf-overview.md)，以便为公共和专用地址空间中的实体提供区域性保护。 客户还可以选择部署 [具有前门的 AZURE WAF](../frontdoor/waf-overview.md) ，它提供网络边缘到公共终结点的保护。

:::image type="content" source="./media/networking-overview/waf-overview.png" alt-text="Azure ExpressRoute":::

### <a name="azure-firewall"></a><a name="firewall"></a>Azure 防火墙
Azure 防火墙是托管的基于云的网络安全服务，可保护 Azure 虚拟网络资源。 使用 Azure 防火墙可以跨订阅和虚拟网络集中创建、实施和记录应用程序与网络连接策略。 Azure 防火墙对虚拟网络资源使用静态公共 IP 地址，使外部防火墙能够识别来自你的虚拟网络的流量。 

有关 Azure 防火墙的详细信息，请参阅 [Azure 防火墙文档](../firewall/overview.md)。

:::image type="content" source="./media/networking-overview/firewall-threat.png" alt-text="Azure ExpressRoute":::

### <a name="network-security-groups"></a><a name="nsg"></a>网络安全组
可以使用网络安全组来筛选 Azure 虚拟网络中出入 Azure 资源的网络流量。 有关详细信息，请参阅[安全性概述](../virtual-network/security-overview.md)。

### <a name="service-endpoints"></a><a name="serviceendpoints"></a>服务终结点
虚拟网络 (VNet) 服务终结点可通过直接连接将 VNet 的虚拟网络专用地址空间和标识扩展到 Azure 服务。 使用终结点可以保护关键的 Azure 服务资源，只允许在客户自己的虚拟网络中对其进行访问。 从 VNet 发往 Azure 服务的流量始终保留在 Microsoft Azure 主干网络中。 有关详细信息，请参阅[虚拟网络服务终结点](../virtual-network/virtual-network-service-endpoints-overview.md)。

:::image type="content" source="./media/networking-overview/vnet-service-endpoints-overview.png" alt-text="Azure ExpressRoute":::

### <a name="azure-private-link"></a><a name="privatelink"></a>Azure 专用链接
使用[Azure Private Link](../private-link/private-link-overview.md) ，可以通过虚拟网络中的专用终结点访问 Azure PaaS 服务 (例如，azure 存储和 SQL 数据库) 以及 azure 托管的客户拥有/合作伙伴服务。
虚拟网络与服务之间的流量将遍历 Microsoft 主干网络。 不再需要向公共 Internet 公开服务。 可以在虚拟网络中创建自己的专用链接服务，并将其交付给客户。

:::image type="content" source="./media/networking-overview/private-endpoint.png" alt-text="Azure ExpressRoute":::

## <a name="application-delivery-services"></a><a name="deliver"></a>应用程序分发服务

本部分介绍 Azure 中的网络服务，该服务可帮助提供应用程序-内容交付网络、Azure 前门服务、流量管理器、负载均衡器和应用程序网关。

|服务|为何使用此类服务？|方案|
|---|---|---|
|[内容分发网络](#cdn)|向用户传送高带宽内容。 CDN 将缓存的内容存储在接入点 (POP) 位置（靠近最终用户的边缘服务器），以最大程度地降低延迟。|<p>[向 Web 应用添加 CDN](../cdn/cdn-add-to-web-app.md)</p> <p>[- 通过 HTTPS 使用 Azure CDN 自定义域访问存储 Blob](..//cdn/cdn-storage-custom-domain-https.md)</p> <p>[将自定义域添加到 Azure CDN 终结点](../cdn/cdn-map-content-to-custom-domain.md)</p> <p>[在 Azure CDN 自定义域中配置 HTTPS](../cdn/cdn-custom-ssl.md?tabs=option-1-default-enable-https-with-a-cdn-managed-certificate)</p>|
|[Azure Front Door 服务](#frontdoor)|使你能够通过优化以实现最佳性能和用于实现高可用性的即时全局故障转移来定义、管理和监视你的 web 流量的全局路由。|<p>[将自定义域添加到 Azure Front Door 服务](../frontdoor/front-door-custom-domain.md)</p> <p>[在 Front Door 自定义域中配置 HTTPS](../frontdoor/front-door-custom-domain-https.md)</p><p>[设置异地筛选 Web 应用程序防火墙策略](../frontdoor/front-door-tutorial-geo-filtering.md)|
|[流量管理器](#trafficmanager)|基于 DNS 将流量分发到全球 Azure 区域中的服务，同时提供高可用性和响应度。|<p> [路由流量以降低延迟](../traffic-manager/tutorial-traffic-manager-improve-website-response.md)</p><p>[将流量路由到优先终结点](../traffic-manager/traffic-manager-configure-priority-routing-method.md)</p><p> [使用加权的终结点控制流量](../traffic-manager/tutorial-traffic-manager-weighted-endpoint-routing.md)</p><p>[基于终结点的地理位置路由流量](../traffic-manager/traffic-manager-configure-geographic-routing-method.md)</p> <p> [根据用户的子网路由流量](../traffic-manager/tutorial-traffic-manager-subnet-routing.md)</p>|
|[负载均衡器](#loadbalancer)|通过将流量路由到不同的可用性区域和你的 VNet 来提供区域负载均衡。 通过在资源中和资源之间路由流量来提供内部负载均衡，以构建区域性应用程序。|<p> [对传入 VM 的 Internet 流量进行负载均衡](../load-balancer/tutorial-load-balancer-standard-manage-portal.md)</p> <p>[对虚拟网络中 VM 之间的流量进行负载均衡](../load-balancer/tutorial-load-balancer-standard-internal-portal.md)<p>[通过端口转发将流量发送到特定 VM 上的特定端口](../load-balancer/tutorial-load-balancer-port-forwarding-portal.md)</p><p> [配置负载均衡和出站规则](../load-balancer/configure-load-balancer-outbound-cli.md)</p>|
|[应用程序网关](#applicationgateway)|Azure 应用程序网关是一种 Web 流量负载均衡器，可用于管理 Web 应用程序的流量。|<p>[使用 Azure 应用程序网关定向 Web 流量](../application-gateway/quick-create-portal.md)</p><p>[教程：通过 Azure 门户使用 TLS 终端配置应用程序网关](../application-gateway/create-ssl-portal.md)</p><p>[创建支持基于 URL 路径进行重定向的应用程序网关](../application-gateway/create-url-route-portal.md) </p>|
|

### <a name="content-delivery-network"></a><a name="cdn"></a>内容分发网络
Azure 内容分发网络 (CDN) 为开发人员提供了一个全局解决方案，通过在世界各地按特定策略放置的物理节点缓存内容来快速分发高带宽内容。 有关 Azure CDN 的详细信息，请参阅 [Azure 内容分发网络](../cdn/cdn-overview.md)。

:::image type="content" source="./media/networking-overview/cdn-overview.png" alt-text="Azure ExpressRoute":::

### <a name="azure-front-door-service"></a><a name="frontdoor"></a>Azure 前门服务
在 Azure Front Door 服务中可以进行优化以实现最佳性能以及进行即时全球故障转移以实现高可用性，并以此定义、管理和监视 Web 流量的全局路由。 使用 Front Door，可将全球（多区域）消费型和企业应用程序转化成可靠、高性能、个性化的现代应用程序、API 和内容，供 Azure 全球受众访问。 有关详细信息，请参阅 [Azure 前门](../frontdoor/front-door-overview.md)。

### <a name="traffic-manager"></a><a name="trafficmanager"></a>流量管理器

Azure 流量管理器是一种基于 DNS 的流量负载均衡器，可以在全球 Azure 区域内以最佳方式向服务分发流量，同时提供高可用性和响应性。 流量管理器提供一系列流量路由方法来分发流量，例如优先级、加权、性能、地理位置、多值或子网路由方法。 有关流量路由方法的详细信息，请参阅[流量管理器路由方法](../traffic-manager/traffic-manager-routing-methods.md)。

下图演示了流量管理器的基于终结点优先级的路由方法：

:::image type="content" source="./media/networking-overview/priority.png" alt-text="Azure ExpressRoute":::

有关流量管理器的详细信息，请参阅[什么是 Azure 流量管理器？](../traffic-manager/traffic-manager-overview.md)

### <a name="load-balancer"></a><a name="loadbalancer"></a>负载均衡器
Azure 负载均衡器为所有 UDP 和 TCP 协议提供高性能、低延迟的第 4 层负载均衡。 它管理入站和出站连接。 可以配置公共和内部负载均衡终结点。 可以定义规则，以便将入站连接映射到后端池目标，并在其中包含 TCP 和 HTTP 运行状况探测选项来管理服务的可用性。 若要了解有关负载均衡器的详细信息，请参阅[负载均衡器概述](../load-balancer/load-balancer-overview.md)一文。

下图显示了利用外部和内部负载均衡器的面向 Internet 的多层应用程序：

:::image type="content" source="./media/networking-overview/load-balancer.png" alt-text="Azure ExpressRoute":::

### <a name="application-gateway"></a><a name="applicationgateway"></a>应用程序网关
Azure 应用程序网关是一种 Web 流量负载均衡器，可用于管理 Web 应用程序的流量。 它是服务形式的应用程序传送控制器 (ADC)，借此为应用程序提供各种第 7 层负载均衡功能。 有关详细信息，请参阅[什么是 Azure 应用程序网关？](../application-gateway/overview.md)

下图演示了应用程序网关的基于 URL 路径的路由方法。

:::image type="content" source="./media/networking-overview/figure1-720.png" alt-text="Azure ExpressRoute":::

## <a name="network-monitoring-services"></a><a name="monitor"></a>网络监视服务
本部分介绍 Azure 中可帮助监视网络资源的网络服务 - 网络观察程序、ExpressRoute Monitor、Azure Monitor 和虚拟网络 TAP。

|服务|为何使用此类服务？|方案|
|---|---|---|
|[网络观察程序](#networkwatcher)|帮助监视和排查连接问题，帮助诊断 VPN、NSG 和路由问题，捕获 VM 上的数据包，使用 Azure Functions 和逻辑应用自动触发诊断工具|<p>[诊断 VM 流量筛选器问题](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md)</p><p>[诊断 VM 路由问题](../network-watcher/diagnose-vm-network-routing-problem.md)</p><p>[监视 VM 之间的通信](../network-watcher/connection-monitor.md)</p><p>[诊断网络之间的通信问题](../network-watcher/diagnose-communication-problem-between-networks.md)</p><p>[记录传入和传出 VM 的网络流量](../network-watcher/network-watcher-nsg-flow-logging-portal.md)</p>|
|[ExpressRoute 监视器](#expressroutemonitor)|提供网络性能、可用性和利用率的实时监视，帮助自动发现网络拓扑，提供更快的故障隔离，检测暂时性网络问题，帮助分析历史网络性能特征，支持多订阅|<p>[为 ExpressRoute 配置网络性能监视器](../expressroute/how-to-npm.md)</p><p>[ExpressRoute 监视、指标和警报](../expressroute/expressroute-monitoring-metrics-alerts.md)</p>|
|[Azure Monitor](#azuremonitor)|帮助你了解应用程序的性能，并主动识别影响应用程序及其所依赖资源的问题。|<p>[流量管理器指标和警报](../traffic-manager/traffic-manager-metrics-alerts.md)</p><p>[针对标准负载均衡器的 Azure Monitor 诊断](../load-balancer/load-balancer-standard-diagnostics.md)</p><p>[监视 Azure 防火墙日志和指标](../firewall/tutorial-diagnostics.md)</p><p>[Azure web application firewall monitoring and logging](../frontdoor/waf-front-door-monitor.md)（Azure Web 应用程序防火墙监视和日志记录）</p>|
|[虚拟网络点击](#vnettap)|提供对数据包收集器的虚拟机网络流量的连续流式处理，启用网络和应用程序性能管理解决方案和安全分析工具|[创建 VNet 点击资源](../virtual-network/tutorial-tap-virtual-network-cli.md)|
|

### <a name="network-watcher"></a><a name="networkwatcher"></a>网络观察程序
Azure 网络观察程序提供所需的工具用于监视、诊断 Azure 虚拟网络中的资源、查看其指标，以及为其启用或禁用日志。 有关详细信息，请参阅[什么是网络观察程序？](../network-watcher/network-watcher-monitoring-overview.md?toc=%2fazure%2fnetworking%2ftoc.json)
### <a name="expressroute-monitor"></a><a name="expressroutemonitor"></a>ExpressRoute Monitor
若要了解如何查看 ExpressRoute 线路指标、资源日志和警报，请参阅 [ExpressRoute 监视、指标和警报](../expressroute/expressroute-monitoring-metrics-alerts.md?toc=%2fazure%2fnetworking%2ftoc.json)。
### <a name="azure-monitor"></a><a name="azuremonitor"></a>Azure Monitor
Azure Monitor 提供用于收集、分析和处理来自云与本地环境的遥测数据的综合解决方案，可将应用程序的可用性和性能最大化。 它可以帮助你了解应用程序的性能，并主动识别影响应用程序及其所依赖资源的问题。 有关详细信息，请参阅 [Azure Monitor 概述](../azure-monitor/overview.md?toc=%2fazure%2fnetworking%2ftoc.json)。
### <a name="virtual-network-tap"></a><a name="vnettap"></a>虚拟网络点击
通过 Azure 虚拟网络 TAP（终端接入点），可让你持续将虚拟机网络流量流式传输到网络数据包收集器或分析工具。 收集器或分析工具由[网络虚拟设备](https://azure.microsoft.com/solutions/network-appliances/)合作伙伴提供。 

下图显示虚拟网络 TAP 的工作原理。 

:::image type="content" source="./media/networking-overview/virtual-network-tap-architecture.png" alt-text="Azure ExpressRoute":::

有关详细信息，请参阅 [什么是虚拟网络点击](../virtual-network/virtual-network-tap-overview.md)。

## <a name="next-steps"></a>后续步骤

- 完成[创建首个虚拟网络](../virtual-network/quick-create-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)一文中的步骤，创建自己的首个 VNet，并将几个 VM 连接到此网络。
- 完成[配置点到站点连接](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)一文中的步骤，将计算机连接到 VNet。
- 完成[创建面向 Internet 的负载均衡器](../load-balancer/load-balancer-get-started-internet-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)一文中的步骤，对发往公共服务器的 Internet 流量进行负载均衡。
