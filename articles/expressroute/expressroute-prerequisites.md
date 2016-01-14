<properties
   pageTitle="采用 ExpressRoute 所要满足的先决条件 | Microsoft Azure"
   description="本页提供了在订购 Azure ExpressRoute 线路之前需要满足的要求列表。"
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carolz"
   editor=""/>
<tags
   ms.service="expressroute"
   ms.date="09/21/2015"
   wacn.date=""/>


# ExpressRoute 先决条件  

若要使用 ExpressRoute 连接到 Microsoft 云服务，你需要确认是否符合以下部分中所列的要求。

## 帐户要求

- 使用中的有效 Microsoft Azure 帐户。需有此帐户才能设置 ExpressRoute 线路。ExpressRoute 线路是 Azure 订阅中的资源。即使连接仅限于非 Azure Microsoft 云服务（例如 Office 365 服务和 CRM Online），也必须提供一个 Azure 订阅。
- 有效的 Office 365 订阅（如果使用 Office 365 服务）。有关详细信息，请参阅本文中的 [Office 365 的具体要求](#office-365-specific-requirements)部分。

## 与连接服务提供商的关系

- 与支持列表中的连接服务提供商建立关系，通过这些提供商便于建立连接。你必须与连接服务提供商存在现有的业务关系。你需要确保通过连接服务提供商获得的服务与 ExpressRoute 兼容。
- 如果你要使用未在支持列表中列出的连接服务提供商，仍可以通过 Exchange 来与 Microsoft 云服务建立连接。
	- 请咨询你的连接服务提供商，以确定他们是否处于支持列表所列的任何 Exchange 位置中。
	- 让你的连接提供商将你的网络扩展到选择的 Exchange 位置。
	- 从 Exchange 连接服务提供商处订购一条 ExpressRoute 线路。

## 在你的网络与连接服务提供商之间建立物理连接

有关连接模型的详细信息，请参阅“连接模型”部分。客户必须确保其本地基础结构通过所述的某个模型实际连接到服务提供商基础结构。

## 连接的冗余要求

客户基础结构与服务提供商基础结构之间的物理连接没有任何冗余要求。Microsoft 不要求在第 3 层配置冗余。但 Microsoft 确实要求在 Microsoft 边缘与客户网络之间，通过服务提供商针对要启用的每个对等互连设置冗余路由。如果未以冗余方式配置路由会话，服务的可用性 SLA 将会失效。

## IP 地址和路由注意事项

客户/连接服务提供商负责使用 Microsoft Edge 基础结构来设置冗余 BGP 会话。选择通过 IP VPN 提供商建立连接的客户通常依赖于连接服务提供商来管理路由配置。使用 Exchange 或通过点到点以太网提供商连接到 Microsoft 的客户必须为每个对等互连配置冗余 BGP 会话，以符合可用性 SLA 要求。连接服务提供商可能会提供此项增值服务。有关限制的详细信息，请参阅 [ExpressRoute 线路和路由域](/documentation/articles/expressroute-circuit-peerings)一文中的路由域表。

## 安全与防火墙

有关安全与防火墙信息，请参阅 [Microsoft 云服务和网络安全](/documentation/articles/best-practices-network-security)一文。

## 用于 Azure 公共与 Microsoft 对等互连的 NAT 配置

有关要求与配置的详细指引，请参阅 [ExpressRoute NAT 要求](/documentation/articles/expressroute-nat)。请咨询连接服务提供商，以确定他们是否代你管理 NAT 设置与管理工作。通常第 3 层连接服务提供商将代你管理 NAT。

## Office 365 的具体要求

请查看以下资源以获取有关 Office 365 要求的详细信息。

- [针对 Office 365 的网络规划和性能优化](http://aka.ms/tune)
- [Office 365 网络流量管理](https://support.office.com/article/Office-365-network-traffic-management-e1da26c6-2d39-4379-af6f-4da213218408)
- 有关 QoS 要求和配置的详细指导，请参阅 [ExpressRoute 服务质量 (QoS) 要求](/documentation/articles/expressroute-qos)一文。请咨询连接服务提供商，以确定他们是否为你的 VPN 提供多种级别的服务。 

## 后续步骤

- 有关 ExpressRoute 的详细信息，请参阅 [ExpressRoute 常见问题](/documentation/articles/expressroute-faqs)。
- 查找服务提供商。请参阅 [ExpressRoute 合作伙伴和对等位置](/documentation/articles/expressroute-locations)。
- 请参阅[路由](/documentation/articles/expressroute-routing)、[NAT](/documentation/articles/expressroute-nat) 和 [QoS](/documentation/articles/expressroute-qos) 的要求。
- 配置 ExpressRoute 连接。
	- [创建 ExpressRoute 线路](/documentation/articles/expressroute-howto-circuit-classic)
	- [配置路由](/documentation/articles/expressroute-howto-routing-classic)
	- [将 VNet 链接到 ExpressRoute 线路](/documentation/articles/expressroute-howto-linkvnet-classic
 

<!---HONumber=Mooncake_0104_2016-->