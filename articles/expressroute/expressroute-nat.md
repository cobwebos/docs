---
title: "为 ExpressRoute 线路的 NAT 要求 |Microsoft 文档"
description: "此页提供有关配置和管理 NAT 为 ExpressRoute 线路的详细的要求。"
documentationcenter: na
services: expressroute
author: cherylmc
manager: carmonm
editor: 
ms.assetid: 867bf936-c851-485f-84c8-d8d6e33fee9f
ms.service: expressroute
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/12/2017
ms.author: cherylmc
ms.openlocfilehash: d3de566ff2825ef0c41d88d4a86157dc23d9f46b
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="expressroute-nat-requirements"></a>ExpressRoute NAT 要求
若要连接到 Microsoft 云服务使用 ExpressRoute，你将需要设置和管理 Nat。 某些连接提供商提供设置和管理 NAT 作为托管服务。 咨询你的连接提供商，以查看它们是否提供此类服务。 如果没有，你必须遵守的要求如下所述。 

查看[ExpressRoute 线路和路由域](expressroute-circuit-peerings.md)页后，可以获取的各个路由域的概述。 为了满足 Azure 公共线路和 Microsoft 对等互连的公共 IP 地址要求，我们建议网络和 Microsoft 之间设置 NAT。 本部分提供你需要设置 NAT 基础结构的详细的说明。

## <a name="nat-requirements-for-azure-public-peering"></a>NAT 要求 Azure 公共对等互连
Azure 公共对等路径，可连接到 Azure 中对其公共 IP 地址上承载的所有服务。 其中包括中列出服务[ExpessRoute 常见问题](expressroute-faqs.md)和托管在 Microsoft Azure 上的 Isv 的任何服务。 

> [!IMPORTANT]
> 连接到 Microsoft Azure 公共对等互连上的服务始终从你的网络发起到 Microsoft 的网络。 因此，会话无法启动从 Microsoft Azure 服务到网络通过 ExpressRoute。 如果尝试，发送到这些播发 Ip 的数据包将使用 internet，而不是 ExpressRoute。
> 

要发送到 Microsoft Azure 公共对等互连上流量必须 SNATed 到有效的公用 IPv4 地址才能进入 Microsoft 网络。 下图提供了如何可能最多设置 NAT 的高级图片满足上述要求。

![](./media/expressroute-nat/expressroute-nat-azure-public.png) 

### <a name="nat-ip-pool-and-route-advertisements"></a>NAT IP 池和路由播发
你必须确保流量输入带有有效公共 IPv4 地址 Azure 公共对等路径。 Microsoft 必须能够验证对区域的路由 Internet 注册表 (RIR) 或 Internet 路由注册表 (IRR) IPv4 NAT 地址池的所有权。 将执行检查取决于正在使用对等的 AS 编号和用于 NAT 的 IP 地址 请参阅[ExpressRoute 路由要求](expressroute-routing.md)有关路由注册机构的信息的页。

没有通过此对等互连播发的 NAT IP 前缀的长度限制。 你必须监视 NAT 池，并确保你不缺少的 NAT 会话。

> [!IMPORTANT]
> 公布给 Microsoft 的 NAT IP 池必须不会将播发到 Internet。 这将中断连接到其他 Microsoft 服务。
> 
> 

## <a name="nat-requirements-for-microsoft-peering"></a>Microsoft 对等互连的 NAT 要求
Microsoft 对等路径使你能够连接到不支持通过 Azure 公共对等路径的 Microsoft 云服务。 服务列表中的包括 Office 365 服务，例如 Exchange Online、 SharePoint Online、 Skype for Business 和 Dynamics 365。 Microsoft 希望在 Microsoft 对等互连支持的双向连接。 要发送到 Microsoft 云服务的流量必须 SNATed 到有效的公用 IPv4 地址才能进入 Microsoft 网络。 从 Microsoft 云服务发送到你的网络流量必须是在你的 Internet 边缘，以防止 SNATed[非对称路由](expressroute-asymmetric-routing.md)。 下图提供了高级图片的 NAT 应为 Microsoft 对等互连设置。

![](./media/expressroute-nat/expressroute-nat-microsoft.png) 

### <a name="traffic-originating-from-your-network-destined-to-microsoft"></a>来自你要发送到 Microsoft 的网络流量
* 你必须确保流量输入带有有效的公共 IPv4 地址的 Microsoft 对等路径。 Microsoft 必须能够验证 IPv4 NAT 地址池针对区域路由 internet 注册表 (RIR) 或 internet 路由注册表 (IRR) 的所有者。 将执行检查取决于正在使用对等的 AS 编号和用于 NAT 的 IP 地址 请参阅[ExpressRoute 路由要求](expressroute-routing.md)有关路由注册机构的信息的页。
* 用于 Azure 公共对等互连-安装程序和其他 ExpressRoute 线路的 IP 地址必须不将播发到 Microsoft 通过 BGP 会话。 通过此对等互连播发的 NAT IP 前缀长度没有限制。
  
  > [!IMPORTANT]
  > 公布给 Microsoft 的 NAT IP 池必须不会将播发到 Internet。 这将中断连接到其他 Microsoft 服务。
  > 
  > 

### <a name="traffic-originating-from-microsoft-destined-to-your-network"></a>来自 Microsoft 要发送到你的网络流量
* 某些情况下需要 Microsoft 以启动连接到您的网络内承载的服务终结点。 方案的典型的示例是连接到在您的网络与 Office 365 中承载的 ADFS 服务器。 在这种情况下，必须从你的网络泄漏相应的前缀，到 Microsoft 对等互连。 
* 若要防止你网络中必须在服务终结点的 Internet 边缘 SNAT Microsoft 流量[非对称路由](expressroute-asymmetric-routing.md)。 请求**，回复**与目标与通过 ExpressRoute 接收路由匹配的 IP 将始终发送通过 ExpressRoute。 如果通过 Internet 使用通过 ExpressRoute 发送回复收到的请求，则非对称路由存在。 SNATing 在 Internet 边缘的传入 Microsoft 流量强制答复流量回 Internet 边缘，解决问题。

![非对称通过 ExpressRoute 进行路由](./media/expressroute-asymmetric-routing/AsymmetricRouting2.png)

## <a name="next-steps"></a>后续步骤
* 要求，请参阅[路由](expressroute-routing.md)和[QoS](expressroute-qos.md)。
* 工作流信息，请参阅[ExpressRoute 线路预配工作流和线路状态](expressroute-workflows.md)。
* 配置 ExpressRoute 连接。
  
  * [创建 ExpressRoute 线路](expressroute-howto-circuit-classic.md)
  * [配置路由](expressroute-howto-routing-classic.md)
  * [将 VNet 链接到 ExpressRoute 线路](expressroute-howto-linkvnet-classic.md)

