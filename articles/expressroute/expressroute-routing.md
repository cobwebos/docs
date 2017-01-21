---
title: "ExpressRoute 路由要求 | Microsoft Docs"
description: "本页提供有关为 ExpressRoute 线路配置和管理路由的详细要求。"
documentationcenter: na
services: expressroute
author: osamazia
manager: ganesr
editor: 
ms.assetid: 5b382e79-fa3f-495a-a764-c5ff86af66a2
ms.service: expressroute
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/03/2017
ms.author: osamam
translationtype: Human Translation
ms.sourcegitcommit: 4f67c67639eaf33bb1f2aa236164e98030f5b555
ms.openlocfilehash: c76471f4c0f4e1b751d623f691578d354008d863


---
# <a name="expressroute-routing-requirements"></a>ExpressRoute 路由要求
若要使用 ExpressRoute 连接到 Microsoft 云服务，需要设置并管理路由。 某些连接服务提供商以托管服务形式提供路由的设置和管理。 请咨询连接服务提供商，以确定他们是否提供此类服务。 如果不提供，则必须遵守以下要求。 

有关需要在设置后才能建立连接的路由会话的说明，请参阅[线路和路由域](expressroute-circuit-peerings.md)一文。

> [!NOTE]
> Microsoft 不支持任何高可用性配置的路由器冗余协议（如 HSRP 和 VRRP）。 我们依赖每个对等互连的一组冗余 BGP 会话来获得高可用性。
> 
> 

## <a name="ip-addresses-used-for-peerings"></a>用于对等互连的 IP 地址
用户需要保留一些 IP 地址，用于配置网络与 Microsoft Enterprise Edge (MSEE) 路由器之间的路由。 本部分提供了要求列表，并介绍有关如何获取和使用这些 IP 地址的规则。

### <a name="ip-addresses-used-for-azure-private-peering"></a>用于 Azure 专用对等互连的 IP 地址
你可以使用专用 IP 地址或公共 IP 地址来配置对等互连。 用于配置路由的地址范围不得与用于在 Azure 中创建虚拟网络的地址范围重叠。 

* 必须为路由接口保留一个 /29 子网或两个 /30 子网。
* 用于路由的子网可以是专用 IP 地址或公共 IP 地址。
* 子网不得与客户保留用于 Microsoft 云的范围冲突。
* 如果使用 /29 子网，它将拆分成两个 /30 子网。 
  * 第一个 /30 子网用于主链路，第二个 /30 子网用于辅助链路。
  * 对于每个 /30 子网，必须在路由器上使用 /30 子网的第一个 IP 地址。 Microsoft 使用 /30 子网的第二个 IP 地址设置 BGP 会话。
  * 只有设置两个 BGP 会话，我们的 [可用性 SLA](https://azure.microsoft.com/support/legal/sla/) 才有效。  

#### <a name="example-for-private-peering"></a>专用对等互连示例
如果选择使用 a.b.c.d/29 设置对等互连，它将拆分成两个 /30 子网。 在以下示例中，我们可以了解 a.b.c.d/29 子网的用法。 

a.b.c.d/29 拆分成 a.b.c.d/30 和 a.b.c.d+4/30 并通过预配 API 一路传递到 Microsoft。 你将使用 a.b.c.d+1 作为主要 PE 的 VRF IP，而 Microsoft 将使用 a.b.c.d+2 作为主要 MSEE 的 VRF IP。 你将使用 b.c.d+5 作为辅助 PE 的 VRF IP，而 Microsoft 将使用 a.b.c.d+6 作为辅助 MSEE 的 VRF IP。

假设选择 192.168.100.128/29 设置专用对等互连。 192.168.100.128/29 包括从 192.168.100.128 到 192.168.100.135 的地址，其中：

* 192.168.100.128/30 将分配给 link1（提供商使用 192.168.100.129，而 Microsoft 使用 192.168.100.130）。
* 192.168.100.132/30 将分配给 link2（提供商使用 192.168.100.133，而 Microsoft 使用 192.168.100.134）。

### <a name="ip-addresses-used-for-azure-public-and-microsoft-peering"></a>用于 Azure 公共网络与 Microsoft 对等互连的 IP 地址
你必须使用自己的公共 IP 地址来设置 BGP 会话。 Microsoft 必须能够通过路由 Internet 注册表和 Internet 路由注册表来验证 IP 地址的所有权。 

* 必须使用一个唯一的 /29 子网或两个 /30 子网为每条 ExpressRoute 线路（如果有多个）的每个对等互连设置 BGP 对等互连。 
* 如果使用 /29 子网，它将拆分成两个 /30 子网。 
  * 第一个 /30 子网用于主链路，第二个 /30 子网用于辅助链路。
  * 对于每个 /30 子网，必须在路由器上使用 /30 子网的第一个 IP 地址。 Microsoft 使用 /30 子网的第二个 IP 地址设置 BGP 会话。
  * 只有设置两个 BGP 会话，我们的 [可用性 SLA](https://azure.microsoft.com/support/legal/sla/) 才有效。

## <a name="public-ip-address-requirement"></a>公共 IP 地址要求
### <a name="private-peering"></a>专用对等互连
进行专用对等互连时，可以选择使用公共的或专用的 IPv4 地址。 我们会对用户的流量进行端到端隔离，因此在进行专用对等互连时，不可能出现与其他客户的地址发生重叠的情况。 这些地址不会播发到 Internet。 

### <a name="public-peering"></a>公共对等互连
Azure 公共对等互连路径可让你连接到托管于 Azure 中的所有服务的公共 IP 地址。 其中包括 [ExpessRoute 常见问题](expressroute-faqs.md) 中列出的服务以及由 Microsoft Azure 上的 ISV 托管的任何服务。 始终从你的网络向 Microsoft 网络发起与公共对等互连中 Microsoft Azure 服务的连接。 必须使用公共 IP 地址才能将流量发往 Microsoft 网络。

### <a name="microsoft-peering"></a>Microsoft 对等互连
Microsoft 对等互连路径可让你连接到不支持通过 Azure 公共对等互连路径访问的 Microsoft 云服务。 服务列表包括 Office 365 服务，例如 Exchange Online、SharePoint Online、Skype for Business 和 CRM Online。 Microsoft 支持在 Microsoft 对等互连时进行双向连接。 定向到 Microsoft 云服务的流量必须使用有效的公共 IPv4 地址才能进入 Microsoft 网络。

确保你的 IP 地址和 AS 号码已在下列其中一个注册表中注册。

* [ARIN](https://www.arin.net/)
* [APNIC](https://www.apnic.net/)
* [AFRINIC](https://www.afrinic.net/)
* [LACNIC](http://www.lacnic.net/)
* [RIPENCC](https://www.ripe.net/)
* [RADB](http://www.radb.net/)
* [ALTDB](http://altdb.net/)

> [!IMPORTANT]
> 通过 ExpressRoute 播发到 Microsoft 的公共 IP 地址不得播发到 Internet。 这会中断其他 Microsoft 服务的连接。 但是，在用户的网络中由服务器使用的公共 IP 地址可以在 Microsoft 内部与 O365 终结点通信，可以通过 ExpressRoute 进行播发。 
> 
> 

## <a name="dynamic-route-exchange"></a>动态路由交换
路由交换将通过 eBGP 协议进行。 EBGP 会话在 MSEE 与路由器之间建立。 不要求对 BGP 会话进行身份验证。 如果需要，可以配置 MD5 哈希。 有关配置 BGP 会话的信息，请参阅[配置路由](expressroute-howto-routing-classic.md)及[线路预配工作流和线路状态](expressroute-workflows.md)。

## <a name="autonomous-system-numbers"></a>自治系统编号
Microsoft 使用 AS 12076 进行 Azure 公共、Azure 专用和 Microsoft 对等互连。 我们保留了 ASN 65515-65520 供内部使用。 支持 16 和 32 位 AS 编号。

数据传输对称没有相关要求。 转发与返回路径可以遍历不同的路由器对。 相同的路由必须在你拥有的多个线路对上，从任何一端播发。 路由指标不需要完全相同。

## <a name="route-aggregation-and-prefix-limits"></a>路由聚合与前缀限制
我们支持通过 Azure 专用对等互连向我们播发最多 4000 个前缀。 如果已启用 ExpressRoute 高级版附加组件，则可增加到 10,000 个前缀。 我们接受为每个 BGP 会话最多使用 200 个前缀建立 Azure 公共和 Microsoft 对等互连。 

如果前缀数目超过此限制，将丢弃 BGP 会话。 我们只接受专用对等互连链路上的默认路由。 提供商必须从 Azure 公共和 Microsoft 对等互连路径中筛选出默认路由和专用 IP 地址 (RFC 1918)。 

## <a name="transit-routing-and-cross-region-routing"></a>传输路由和跨区域路由
ExpressRoute 不能配置为传输路由器。 你必须依赖连接服务提供商的传输路由服务。

## <a name="advertising-default-routes"></a>播发默认路由
只有 Azure 专用对等互连会话允许默认路由。 在这种情况下，我们将所有流量从关联的虚拟网络路由到你的网络。 在专用对等互连中播发默认路由会导致来自 Azure 的 Internet 路径遭到阻止。 你必须依赖企业网络边缘，为 Azure 中托管的服务往返路由 Internet 的流量。 

 若要连接其他 Azure 服务和基础结构服务，你必须确保已准备好下列其中一项：

* 已启用 Azure 公共对等互连，以将流量路由到公共终结点
* 使用用户定义的路由，为需要 Internet 连接的每个子网建立 Internet 连接。

> [!NOTE]
> 播发默认路由会中断 Windows 和其他 VM 许可证激活。 请按照 [此处](http://blogs.msdn.com/b/mast/archive/2015/05/20/use-azure-custom-routes-to-enable-kms-activation-with-forced-tunneling.aspx) 的说明来解决此问题。
> 
> 

## <a name="support-for-bgp-communities"></a>BGP 社区支持
本部分概述如何配合 ExpressRoute 使用 BGP 社区。 Microsoft 将播发公共和 Microsoft 对等互连路径中的路由并为路由标记适当的社区值。 下面将会介绍这种方案的理由以及有关社区值的详细信息。 但是，Microsoft 不遵循向 Microsoft 播发的路由的任何标记社区值。

如果你要在某个地缘政治区域内的任何一个对等互连位置通过 ExpressRoute 连接到 Microsoft，就必须能够访问该地缘政治边界内所有区域中的所有 Microsoft 云服务。 

例如，如果你在阿姆斯特丹通过 ExpressRoute 连接到 Microsoft，则就能够访问在欧洲北部和欧洲西部托管的所有 Microsoft 云服务。 

有关地缘政治地区、关联的 Azure 区域和对应的 ExpressRoute 对等互连位置的详细列表，请参阅 [ExpressRoute 合作伙伴和对等位置](expressroute-locations.md) 。

可以针对每个地缘政治区域购买多个 ExpressRoute 线路。 如果拥有多个连接，则可以从异地冗余中获得明显的高可用性优势。 如果你多条 ExpressRoute 线路，将从 Microsoft 收到同一组公共对等互连和 Microsoft 对等互连路径的前缀。 这意味着你可以使用多个路径从你的网络接入 Microsoft。 这可能会导致在网络中做出欠佳的路由决策。 因此，你可能会在不同的服务上遇到欠佳的连接体验。 可以依赖社区值做出适当的路由决策，[向用户提供最佳路由](expressroute-optimize-routing.md)。

| **Microsoft Azure 区域** | **BGP 社区值** |
| --- | --- |
| **北美** | |
| 美国东部 |12076:51004 |
| 美国东部 2 |12076:51005 |
| 美国西部 |12076:51006 |
| 美国西部 2 |12076:51026 |
| 美国中西部 |12076:51027 |
| 美国中北部 |12076:51007 |
| 美国中南部 |12076:51008 |
| 美国中部 |12076:51009 |
| 加拿大中部 |12076:51020 |
| 加拿大东部 |12076:51021 |
| **南美洲** | |
| 巴西南部 |12076:51014 |
| **欧洲** | |
| 欧洲北部 |12076:51003 |
| 欧洲西部 |12076:51002 |
| 英国南部 | 12076:51024 |
| 英国西部 | 12076:51025 |
| **亚太区** | |
| 东亚 |12076:51010 |
| 亚洲东南部 |12076:51011 |
| **日本** | |
| 日本东部 |12076:51012 |
| 日本西部 |12076:51013 |
| **澳大利亚** | |
| 澳大利亚东部 |12076:51015 |
| 澳大利亚东南部 |12076:51016 |
| **印度** | |
| 印度南部 |12076:51019 |
| 印度西部 |12076:51018 |
| 印度中部 |12076:51017 |

所有 Microsoft 播发的路由都标有适当的社区值。 

> [!IMPORTANT]
> 全局前缀将使用相应的社区值进行标记，并且仅当已启用 ExpressRoute 高级版附加组件时才会播发。
> 
> 

除了上述各项，Microsoft 还将根据其所属的服务加上标记及前缀。 这只适用于 Microsoft 对等互连。 下表提供了服务与 BGP 社区值之间的映射。

| **服务** | **BGP 社区值** |
| --- | --- |
| Exchange Online |12076:5010 |
| SharePoint Online |12076:5020 |
| Skype For Business Online |12076:5030 |
| CRM Online |12076:5040 |
| 其他 Office 365 Online 服务 |12076:5100 |

> [!NOTE]
> Microsoft 不遵循你在播发到 Microsoft 的路由上设置的任何 BGP 社区值。
> 
> 

### <a name="bgp-community-support-in-national-clouds-preview"></a>区域云中的 BGP 社区支持（预览版）

| **国家云 Azure 区域**| **BGP 社区值** |
| --- | --- |
| **美国政府** |  |
| 美国政府爱荷华州 | 12076:51109 |
| 美国政府弗吉尼亚州 | 12076:51105 |


| **国家云中的服务** | **BGP 社区值** |
| --- | --- |
| **美国政府** |  |
| Exchange Online |12076:5110 |
| SharePoint Online |12076:5120 |
| Skype For Business Online |12076:5130 |
| CRM Online |12076:5140 |
| 其他 Office 365 Online 服务 |12076:5200 |

## <a name="next-steps"></a>后续步骤
* 配置 ExpressRoute 连接。
  
  * [创建经典部署模型的 ExpressRoute 线路](expressroute-howto-circuit-classic.md)或[使用 Azure Resource Manager 创建和修改 ExpressRoute 线路](expressroute-howto-circuit-arm.md)
  * [为经典部署模型配置路由](expressroute-howto-routing-classic.md)或[为 Resource Manager 部署模型配置路由](expressroute-howto-routing-arm.md)
  * [将经典 VNet 链接到 ExpressRoute 线路](expressroute-howto-linkvnet-classic.md)或[将 Resource Manager VNet 链接到 ExpressRoute 线路](expressroute-howto-linkvnet-arm.md)




<!--HONumber=Jan17_HO1-->


