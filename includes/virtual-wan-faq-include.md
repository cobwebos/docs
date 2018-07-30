---
title: include 文件
description: include 文件
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 07/18/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: d059cab5668eef8d4dafc1442ca9749a7dcf8c9d
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/19/2018
ms.locfileid: "39162508"
---
### <a name="what-is-the-difference-between-an-azure-virtual-network-gateway-vpn-gateway-and-an-azure-virtual-wan-vpngateway"></a>Azure 虚拟网络网关（VPN 网关）与 Azure 虚拟 WAN vpngateway 之间有什么差别？

虚拟 WAN 提供大规模站点到站点连接，在设计上考虑到了吞吐量、可伸缩性和易用性。 CPE 分支设备自动预配并连接到 Azure 虚拟 WAN。 这些设备由一个不断扩张的 SD-WAN 和 VPN 合作伙伴生态系统提供。

### <a name="which-device-providers-virtual-wan-partners-are-supported-at-launch-time"></a>推出时支持哪些设备提供商（虚拟 WAN 合作伙伴）？ 

目前，Citrix 和 Riverbed 支持完全自动化的虚拟 WAN 体验。 有关详细信息，请参阅[虚拟 WAN 合作伙伴](https://aka.ms/virtualwan)。

### <a name="am-i-required-to-use-a-preferred-partner-device"></a>是否需要使用首选的合作伙伴设备？

不是。 可以使用任何支持 VPN 的且符合 IKEv2 IPsec 预览版支持要求的设备。

### <a name="how-do-virtual-wan-partners-automate-connectivity-with-azure-virtual-wan"></a>虚拟 WAN 合作伙伴如何自动与 Azure 虚拟 WAN 建立连接？

软件定义的连接解决方案通常使用控制器或设备预配中心来管理其分支设备。 控制器可以使用 Azure API 自动与 Azure 虚拟 WAN 建立连接。 有关详细信息，请参阅[虚拟 WAN 合作伙伴自动化](../articles/virtual-wan/virtual-wan-configure-automation-providers.md)。

### <a name="does-virtual-wan-change-any-existing-connectivity-features"></a>虚拟 WAN 是否会更改任何现有的连接功能？   

不会对现有的 Azure 连接功能进行任何更改。

### <a name="are-there-new-resource-manager-resources-available-for-virtual-wan"></a>虚拟 WAN 是否有新的可用资源管理器资源？
  
是的，虚拟 WAN 引入了新的资源管理器资源。 有关详细信息，请参阅[概述](https://go.microsoft.com/fwlink/p/?LinkId=2004389)。

### <a name="are-there-any-special-requirements-to-join-the-preview"></a>是否需要满足任何特殊要求才能参与预览版？ 

在配置 Azure 虚拟 WAN 之前，必须先在预览版中注册订阅。 若要注册，请向 <azurevirtualwan@microsoft.com> 发送一封包含订阅 ID 的电子邮件。 注册订阅后，你会收到电子邮件。 在收到已注册订阅的确认之前，无法在门户中使用虚拟 WAN。

注意事项：

* 预览版仅限在 Azure 公共区域中使用。
* 每个虚拟中心最多支持 100 个连接。 每个连接包括采用主动-主动配置的两个隧道。 隧道在 Azure 虚拟中心 vpngateway 中终止。
* 在以下情况下，请考虑使用此预览版：
  * 要为每个虚拟中心部署的聚合带宽不超过 1 Gbps。
  * VPN 设备支持基于路由的配置和 IKEv2 IPsec 连接。
  * 想要了解如何使用 SD-WAN，以及如何操作推出方合作伙伴（Riverbed 和 Citrix）提供的分支设备。<br>或<br>想要设置分支到分支以及分支到 Azure 的连接，其中包括本地设备的配置管理。 （自我配置）

### <a name="is-global-vnet-peering-supported-with-azure-virtual-wan"></a>Azure 虚拟 WAN 是否支持全局 VNet 对等互连？ 

 不是。

### <a name="can-spoke-vnets-connected-to-a-virtual-hub-communicate-with-each-other"></a>连接到虚拟中心的分支 VNet 是否能够相互通信？

是的。 可以直接在连接到虚拟中心的分支之间建立 VNet 对等互连。 有关详细信息，请参阅[虚拟网络对等互连](../articles/virtual-network/virtual-network-peering-overview.md)。

### <a name="can-i-deploy-and-use-my-favorite-network-virtual-appliance-in-an-nva-vnet-with-azure-virtual-wan"></a>是否可以在 Azure 虚拟 WAN 中部署和使用我偏爱的网络虚拟设备（在 NVA VNet 中）？

是的，可以将你偏爱的网络虚拟设备 (NVA) VNet 连接到 Azure 虚拟 WAN，但这需要在中心内实现路由功能，该功能在我们的路线图上。 连接到 NVA VNet 的所有分支必须另外连接到虚拟中心。 

### <a name="can-an-nva-vnet-have-a-virtual-network-gateway"></a>NVA VNet 是否可以包含虚拟网络网关？

不是。 如果 NVA VNet 已连接到虚拟中心，则不能包含虚拟网络网关。 

### <a name="is-there-support-for-bgp"></a>是否支持 BGP？

是的，支持 BGP。 为了确保来自 NVA VNet 的路由适当播发，如果分支已连接到 NVA VNet，而后者又连接到虚拟中心，则分支必须禁用 BGP。 此外，需将分支 VNet 连接到虚拟中心。

### <a name="can-i-direct-traffic-using-udr-in-the-virtual-hub"></a>是否可以在虚拟中心使用 UDR 定向流量？

此功能在我们的路线图上。 敬请期待！

### <a name="is-there-any-licensing-or-pricing-information-for-virtual-wan"></a>虚拟 WAN 是否有任何许可或定价信息？
 
在预览期，虚拟 WAN 不会产生额外的费用。 在预览期，当前的 [Azure VPN 和传出流量定价](https://azure.microsoft.com/pricing/details/vpn-gateway/)保持有效。

### <a name="how-do-new-partners-that-are-not-listed-in-your-launch-partner-list-get-onboarded"></a>没有在启动合作伙伴列表中列出的新合作伙伴如何加入？

向 azurevirtualwan@microsoft.com 发送电子邮件。 理想的合作伙伴具有可以预配 IKEv2 IPSec 连接的设备。

### <a name="is-it-possible-to-construct-azure-virtual-wan-with-a-resource-manager-template"></a>是否可以使用资源管理器模板构造 Azure 虚拟 WAN？

我们正在开发此功能。 目前，该服务是由 REST 和门户驱动的。

### <a name="is-branch-to-branch-connectivity-allowed-in-virtual-wan"></a>虚拟 WAN 中是否允许分支到分支连接？

是的，分支到分支连接在虚拟 WAN 中可用。

### <a name="does-branch-to-branch-traffic-traverse-through-the-azure-virtual-wan"></a>分支到分支流量是否可以穿过 Azure 虚拟 WAN？

是的。

### <a name="how-is-virtual-wan-different-from-the-existing-azure-virtual-network-gateway"></a>虚拟 WAN 与现有 Azure 虚拟网络网关有何不同？

虚拟网络网关 VPN 限制为 30 个隧道。 对于连接，应当为大型 VPN 使用虚拟 WAN。 在公共预览版中，每个中心的分支连接数限制为 100 个，总带宽为 1 Gbps。

### <a name="does-this-virtual-wan-require-expressroute-from-each-site"></a>此虚拟 WAN 是否要求每个站点中都有 ExpressRoute？

否，虚拟 WAN 不要求每个站点中都有 ExpressRoute。 它通过 Internet 链接使用从设备到 Azure 虚拟 WAN 中心的标准 IPsec 站点到站点连接。

### <a name="is-there-a-network-throughput-limit-when-using-azure-virtual-wan"></a>使用 Azure 虚拟 WAN 时是否存在网络吞吐量限制？

在公共预览版中，分支数限制为每个中心/区域 100 个连接，中心内总带宽为 1 G。

### <a name="does-virtual-wan-allow-the-on-premises-device-to-utilize-multiple-isps-in-parallel-or-is-it-always-a-single-vpn-tunnel"></a>虚拟 WAN 是否允许本地设备并行利用多个 ISP？亦或它始终为单个 VPN 隧道？

是的，可以有来自单个分支的主动-主动隧道（2 个隧道 = 1 个 Azure 虚拟 WAN 连接），具体取决于分支设备。

### <a name="how-is-traffic-is-routed-on-the-azure-backbone"></a>流量在 Azure 主干网上是如何路由的？

流量遵循以下模式：分支设备-> ISP-> Microsoft Edge-> Microsoft DC-> Microsoft Edge-> ISP-> 分支设备。

### <a name="in-this-model-what-do-you-need-at-each-site-just-an-internet-connection"></a>在此模型中，需要在每个站点执行什么操作？ 只需要创建 Internet 连接？

是的。 一个 Internet 连接和物理设备，最好是来自我们的集成合作伙伴。 除非你希望手动管理配置以及从你的首选设备到 Azure 的连接。