---
title: "ExpressRoute 线路的 NAT 要求 | Microsoft Docs"
description: "本页提供有关为 ExpressRoute 线路配置和管理 NAT 的详细要求。"
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
ms.date: 10/12/2017
ms.author: cherylmc
ms.openlocfilehash: 2a9903b0e3c04a7098f7a8e529801483b10af142
ms.sourcegitcommit: 5d772f6c5fd066b38396a7eb179751132c22b681
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2017
---
# <a name="expressroute-nat-requirements"></a>ExpressRoute NAT 要求
要使用 ExpressRoute 连接到 Microsoft 云服务，需要设置并管理 NAT。 某些连接服务提供商以托管服务形式提供 NAT 的设置和管理。 请咨询连接服务提供商，以确定他们是否提供此类服务。 如果没有，则必须遵守相关要求，如下所述。 

查看 [ExpressRoute 线路和路由域](expressroute-circuit-peerings.md) 页，获得各种路由域概述。 为了符合 Azure 公共和 Microsoft 对等互连的公共 IP 地址要求，建议在网络与 Microsoft 之间设置 NAT。 本部分提供需要设置的 NAT 基础结构的详细描述。

## <a name="nat-requirements-for-microsoft-peering"></a>Microsoft 对等互连的 NAT 要求
Microsoft 对等互连路径用于连接到不支持通过 Azure 公共对等互连路径访问的 Microsoft 云服务。 服务列表包括 Office 365 服务，例如 Exchange Online、SharePoint Online、Skype for Business 和 Dynamics 365。 Microsoft 有望在 Microsoft 对等互连上支持双向连接。 定向到 Microsoft 云服务的流量必须由 SNAT 转换成有效的公共 IPv4 地址才能进入 Microsoft 网络。 从 Microsoft 云服务定向到网络的流量必须在 Internet 边缘进行 SNAT 转换，避免[非对称路由](expressroute-asymmetric-routing.md)。 下图提供了有关如何为 Microsoft 对等互连设置 NAT 的综合示意图。

![](./media/expressroute-nat/expressroute-nat-microsoft.png) 

### <a name="traffic-originating-from-your-network-destined-to-microsoft"></a>从网络发起的，目标为 Microsoft 的流量
* 必须确保流量进入公共 IPv4 地址有效的 Microsoft 对等互连路径。 Microsoft 必须能够根据区域路由 Internet 注册表 (RIR) 或 Internet 路由注册表 (IRR) 验证 IPv4 NAT 地址池的所有权。 会根据配对的 AS 编号和用于 NAT 的 IP 地址执行检查。 有关路由注册的信息，请参阅 [ExpressRoute 路由要求](expressroute-routing.md) 页。
* 用于 Azure 公共对等互连设置和其他 ExpressRoute 线路的 IP 地址不得通过 BGP 会话向 Microsoft 播发。 通过此对等互连播发的 NAT IP 前缀长度没有限制。
  
  > [!IMPORTANT]
  > 已播发到 Microsoft 的 NAT IP 池不得播发到 Internet。 这会中断其他 Microsoft 服务的连接。
  > 
  > 

### <a name="traffic-originating-from-microsoft-destined-to-your-network"></a>从 Microsoft 发起的，目标为网络的流量
* 某些方案需要 Microsoft 启动到网络中托管的服务终结点的连接。 一个典型的示例就是从 Office 365 连接到网络中托管的 ADFS 服务器。 在这种情况下，必须将网络中相应的前缀透露给 Microsoft 对等互连。 
* 必须在 Internet 边缘为网络中的服务终结点进行 Microsoft 流量的 SNAT 转换，避免[非对称路由](expressroute-asymmetric-routing.md)。 只要目标 IP 地址与通过 ExpressRoute 接收的路由匹配，将始终通过 ExpressRoute 发送请求和回复。 如果请求是通过 Internet 接收的，而回复是通过 ExpressRoute 发送的，则会存在非对称路由。 在 Internet 边缘对传入的 Microsoft 流量进行 SNAT 转换 可以强制回复流量回到 Internet 边缘，从而解决此问题。

![非对称路由与 ExpressRoute](./media/expressroute-asymmetric-routing/AsymmetricRouting2.png)

## <a name="nat-requirements-for-azure-public-peering"></a>Azure 公共对等互连的 NAT 要求
Azure 公共对等互连路径用于连接到托管于 Azure 中的所有服务的公共 IP 地址。 其中包括 [ExpessRoute 常见问题](expressroute-faqs.md) 中列出的服务以及由 Microsoft Azure 上的 ISV 托管的任何服务。 

> [!IMPORTANT]
> 始终从网络向 Microsoft 网络发起与公共对等互连中 Microsoft Azure 服务的连接。 因此，不能通过 ExpressRoute 启动从 Microsoft Azure 服务到网络的会话。 在尝试过后，发送到这些播发 IP 的数据包将使用 Internet 而不是 ExpressRoute。
> 

定向到公共对等互连中 Microsoft Azure 的流量必须由 SNAT 转换成有效的公共 IPv4 地址，才能进入 Microsoft 网络。 下图提供了有关如何设置 NAT 以符合上述要求的综合示意图。

![](./media/expressroute-nat/expressroute-nat-azure-public.png) 

### <a name="nat-ip-pool-and-route-advertisements"></a>NAT IP 池和路由播发
必须确保流量进入公共 IPv4 地址有效的 Azure 公共对等互连路径。 Microsoft 必须能够根据区域路由 Internet 注册表 (RIR) 或 Internet 路由注册表 (IRR) 验证 IPv4 NAT 地址池的所有权。 会根据配对的 AS 编号和用于 NAT 的 IP 地址执行检查。 有关路由注册的信息，请参阅 [ExpressRoute 路由要求](expressroute-routing.md) 页。

通过此对等互连播发的 NAT IP 前缀长度没有限制。 必须监视 NAT 池，并确保未耗尽 NAT 会话。

> [!IMPORTANT]
> 已播发到 Microsoft 的 NAT IP 池不得播发到 Internet。 这会中断其他 Microsoft 服务的连接。
> 
> 

## <a name="next-steps"></a>后续步骤
* 请参阅[路由](expressroute-routing.md)和 [QoS](expressroute-qos.md) 的要求。
* 有关工作流信息，请参阅 [ExpressRoute 线路预配工作流和线路状态](expressroute-workflows.md)。
* 配置 ExpressRoute 连接。
  
  * [创建 ExpressRoute 线路](expressroute-howto-circuit-portal-resource-manager.md)
  * [配置路由](expressroute-howto-routing-portal-resource-manager.md)
  * [将 VNet 链接到 ExpressRoute 线路](expressroute-howto-linkvnet-portal-resource-manager.md)

