<properties
   pageTitle="采用 ExpressRoute 所要满足的先决条件 | Microsoft Azure"
   description="本页提供了在订购 Azure ExpressRoute 线路之前需要满足的要求列表。"
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carmonm"
   editor=""/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="06/13/2016"
   ms.author="cherylmc"/>


# ExpressRoute 先决条件和清单  

若要使用 ExpressRoute 连接到 Microsoft 云服务，你需要确认是否符合以下部分中所列的要求。

[AZURE.INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

## Azure 帐户

- 使用中的有效 Microsoft Azure 帐户。需有此帐户才能设置 ExpressRoute 线路。ExpressRoute 线路是 Azure 订阅中的资源。即使连接限于非 Azure Microsoft 云服务（例如 Office 365 服务和 CRM Online），Azure 订阅也是一个必要条件。
- 激活 Office 365 订阅（如果要使用 Office 365 服务）。有关详细信息，请参阅本文中的 [Office 365 的具体要求](#office-365-specific-requirements)部分。

## 连接服务提供商
- 可使用 [ExpressRoute 连接合作伙伴](expressroute-locations.md#partners)连接到 Microsoft 云。有[三种方法](expressroute-introduction.md#howtoconnect)可以在本地网络与 Microsoft 之间建立连接。
- 即使你的提供商不是 ExpressRoute 连接合作伙伴，你也可以通过[云交换提供商](expressroute-locations.md#nonpartners)连接到 Microsoft 云。

## 网络要求
- **冗余连接**：对于你和提供商之间的物理连接，没有冗余方面的要求。即使你只有[一个到云交换的物理连接](expressroute-faqs.md#onep2plink)，Microsoft 也会要求在 Microsoft 的路由器和对等互连路由器之间建立冗余性的 BGP 会话。
- **路由**：你或你的提供商需设置和管理针对[路由域](expressroute-circuit-peerings.md)的 BGP 会话，具体取决于连接到 Microsoft 云的方式。某些以太网连接服务提供商或云交换服务提供商可能会以增值服务的形式提供 BGP 管理。
- **NAT**：Microsoft 只能通过 Microsoft 对等互连方式接受公共 IP 地址。如果使用的是本地网络中的专用 IP 地址，你或你的提供商需[使用 NAT](expressroute-nat.md) 将专用 IP 地址转换为公共 IP 地址。
- **QoS**：Skype for Business 的各种服务（例如语音、视频、文本）所要求的 QoS 处理方式各有差异。你和你的提供商应遵循 [QoS 要求](expressroute-qos.md)。
- **网络安全**：通过 ExpressRoute 连接到 Microsoft 云时，应考虑[网络安全](../best-practices-network-security.md)。
 
## Office 365

如果你打算在 ExpressRoute 上启用 Office 365，请参阅以下文档以详细了解 Office 365 方面的要求。


- [Overview of ExpressRoute for Office 365（适用于 Office 365 的 ExpressRoute 概述）](https://support.office.com/zh-CN/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd)
- [Routing with ExpressRoute for Office 365（通过适用于 Office 365 的 ExpressRoute 进行路由）](https://support.office.com/zh-CN/article/Routing-with-ExpressRoute-for-Office-365-e1da26c6-2d39-4379-af6f-4da213218408)
- [Office 365 URL 和 IP 地址范围](https://support.office.com/zh-CN/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)
- [针对 Office 365 的网络规划和性能优化](https://support.office.com/zh-CN/article/Network-planning-and-performance-tuning-for-Office-365-e5f1228c-da3c-4654-bf16-d163daee8848)
- [Network bandwidth calculators and tools（网络带宽计算器和工具）](https://support.office.com/zh-CN/article/Network-and-migration-planning-for-Office-365-f5ee6c33-bcd7-4b0b-b0f8-dc1d9fb8d132)
- [Office 365 与本地环境的集成](https://support.office.com/zh-CN/article/Office-365-integration-with-on-premises-environments-263faf8d-aa21-428b-aed3-2021837a4b65)

## CRM Online 
如果你打算在 ExpressRoute 上启用 CRM Online，请参阅以下文档以详细了解 CRM Online

- [CRM Online URLs](https://support.microsoft.com/kb/2655102)（CRM Online URL）和 [IP address ranges](https://support.microsoft.com/kb/2728473)（IP 地址范围）

## 后续步骤

- 有关 ExpressRoute 的详细信息，请参阅 [ExpressRoute 常见问题](expressroute-faqs.md)。
- 查找 ExpressRoute 连接服务提供商。请参阅 [ExpressRoute 合作伙伴和对等位置](expressroute-locations.md)。
- 请参阅[路由](expressroute-routing.md)、[NAT](expressroute-nat.md) 和 [QoS](expressroute-qos.md) 的要求。
- 配置 ExpressRoute 连接。
	- [创建 ExpressRoute 线路](expressroute-howto-circuit-classic.md)
	- [配置路由](expressroute-howto-routing-classic.md)
	- [将 VNet 链接到 ExpressRoute 线路](expressroute-howto-linkvnet-classic.md)

<!---HONumber=AcomDC_0921_2016-->