---
title: "Azure 政府版网络 |Microsoft Docs"
description: "这提供了 e Government 专业连接的功能和指南比较"
services: Azure-Government
cloud: gov
documentationcenter: 
author: ryansoc
manager: zakramer
editor: 
ms.assetid: 3da70579-ecda-421a-8ebf-d52906334e9b
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 09/28/2016
ms.author: ryansoc
translationtype: Human Translation
ms.sourcegitcommit: 14aa9126d3426c560c1f9497ed0d7a92448137b4
ms.openlocfilehash: 39e67d449c0fa37b76354dc522216f9473df7b97


---
# <a name="azure-government-networking"></a>Azure Government 网络
## <a name="expressroute-private-connectivity"></a>ExpressRoute（专用连接）
ExpressRoute 已在 Azure Government 中正式发布。 有关详细信息（包括合作伙伴和对等位置），请参阅 [ ExpressRoute 公共文档 ](../expressroute/index.md)。

### <a name="variations"></a>变体
ExpressRoute 已在 Azure Government 中正式发布 (GA)。 

* Government 客户通过专用的 Azure Government (Gov) ExpressRoute (ER) 连接连接到实际独立的容量
* Azure Gov 通过利用多个位于最近 500 英里之外的区域对提高了可用性和持久性 
* 默认情况下，所有 Azure Gov ER 连接通过支持爆发配置主动-主动冗余，并提供高达 10G 的线路容量（最小为 50MB）
* Azure Gov ER 位置为客户和 Azure Gov 地域冗余区域提供优化的路径（最短跃点、低延迟、高性能等）
* Azure Gov ER 专用连接不使用、遍历或依赖 Internet
* Azure Gov 物理和逻辑基础结构实际上是专用且分隔的，并且仅限美国客户访问
* Microsoft 拥有并操作 Azure Gov 区域和 Azure Gov ER Meet-Me 位置之间的所有光纤基础结构
* Azure Gov ER 提供与 Microsoft Azure、O365 和 CRM 云服务的连接

### <a name="considerations"></a>注意事项
有两个基本服务提供到 Azure Government 的专用网络连接：VPN（典型组织的站点到站点）和 ExpressRoute。

Azure ExpressRoute 用于在 Azure Government 数据中心和你的本地基础结构或共置环境之间创建专有连接。 ExpressRoute 连接并不绕过公共 Internet—与典型的 Internet 连接相比，它的可靠性更高、速度更快且延迟时间更短。 在某些情况下，使用 ExpressRoute 连接在本地系统和 Azure 之间传输数据可以带来显著的成本效益。   

使用 ExpressRoute，你可以在 ExpressRoute 位置（如 Exchange 提供商设施）建立与 Azure 的连接，或从现有 WAN 网络（例如多协议标签交换 (MPLS) VPN）直接连接到 Azure。

![替换文字](./media/azure-government-capability-private-connectivity-options.PNG)  ![替换文字](./media/government-capability-expressroute.PNG)  

对于支持 Azure Government 客户应用程序和解决方案的网络服务，强烈建议实现 ExpressRoute（专用连接）以连接到 Azure Government。 如果使用 VPN 连接，则要考虑以下事项：

* 客户应联系其授权官方/机构以确定是否需要专用连接或其他安全连接机制，并确定要考虑的任意其他限制。
* 客户应决定是否要强制将站点到站点 VPN 通过专用连接区域路由。
* 客户应使用授权的专用连接访问提供程序来获取 MPLS 线路或 VPN。

所有使用专用连接体系结构的客户都应验证已建立相应的实施，并为到 Azure Government 的网关网络 /Internet (GN/I) 边缘路由器分界点的客户连接进行维护。 同样，你的组织必须在 Azure Government 的本地环境和网关网络/客户 (GN/C) 边缘路由器分界点之间建立网络连接。

## <a name="next-steps"></a>后续步骤
有关补充信息和更新，请订阅 <a href="https://blogs.msdn.microsoft.com/azuregov/">Microsoft Azure 政府博客</a>。




<!--HONumber=Nov16_HO3-->


