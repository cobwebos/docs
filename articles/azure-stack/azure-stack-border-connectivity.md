---
title: Azure Stack 集成系统的边界连接网络集成注意事项 | Microsoft Docs
description: 了解可以执行哪些操作来为多节点 Azure Stack 规划数据中心边界网络连接。
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2018
ms.author: jeffgilb
ms.reviewer: wamota
ms.openlocfilehash: 260c58ad9099a4532c8a6558cfcf5c13f0fc8d52
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/27/2018
ms.locfileid: "39282002"
---
# <a name="border-connectivity"></a>边界连接 
网络集成规划是成功进行 Azure Stack 集成系统部署、操作和管理的重要先决条件。 边界连接规划首先是要选择是否将动态路由与边界网关协议 (BGP) 配合使用。 这需要分配 16 位的 BGP 自治系统编号（公共或专用），或者使用静态路由（在这种情况下会将静态默认路由分配给边界设备）。

> [!IMPORTANT]
> 架顶式 (TOR) 交换机需要在物理接口上配置具有点到点 IP（/30 网络）的第 3 层上行链路。 不支持将第 2 层上行链路用于支持 Azure Stack 操作的 TOR 交换机。 

## <a name="bgp-routing"></a>BGP 路由
使用 BGP 等动态路由协议可以保证系统始终会注意到网络更改和便于管理。 

如下图所示，将使用前缀列表限制播发 TOR 交换机上的专用 IP 空间。 前缀列表将拒绝专用 IP 子网并将其作为路由映射应用于 TOR 与边界之间的连接。

Azure Stack 解决方案内运行的软件负载均衡器 (SLB) 将对等互连到 TOR 设备，以便它可以动态播发 VIP 地址。

若要确保用户流量立即以透明方式从故障中恢复，TOR 设备之间配置的 VPC 或 MLAG 允许对主机和 HSRP 或 VRRP 使用多底盘链接聚合以便为 IP 网络提供网络冗余。

![BGP 路由](media/azure-stack-border-connectivity/bgp-routing.png)

## <a name="static-routing"></a>静态路由
静态路由需要额外配置才能边界设备。 它需要更多的手动干预，并且管理，以及之前的任何更改和配置错误导致的问题进行全面分析可能需要更多的时间，具体取决于所做的更改回滚到。 它不是建议的路由方法，但受支持。

若要将 Azure Stack 集成到你使用静态路由的网络环境，必须连接的边框和 TOR 设备之间的所有四个物理链路和静态路由的工作原理由于不能保证高可用性。

必须使用指向要发送到外部网络或公共 Vip 和基础结构网络流量的 TOR 设备 P2P 的静态路由配置为边界设备。 需要部署到 BMC 网络的静态路由。 客户可以选择将保留在边框来访问驻留在 BMC 网络的某些资源的静态路由。  添加到的静态路由*交换机基础结构*并*切换管理*网络是可选的。

将所有流量都发送到边界设备的静态默认路由附带配置 TOR 设备。 默认规则的一个流量例外是被阻止使用应用于 TOR 到边界连接的访问控制列表的私有空间。

静态路由仅适用于 TOR 和边框交换机之间上行。 BGP 动态路由是机架内使用，因为它是 SLB 和其他组件的必备工具，无法禁用或删除。

![静态路由](media/azure-stack-border-connectivity/static-routing.png)

## <a name="transparent-proxy"></a>透明代理
如果数据中心要求所有流量都使用代理，则必须配置“透明代理”以便根据策略处理来自机架的所有流量，并分离网络上不同区域之间的流量。

> [!IMPORTANT]
> Azure Stack 解决方案不支持普通 Web 代理。  

透明代理（也称为截获、内联或强制代理）将截获网络层的正常通信，而无需任何特殊的客户端配置。 客户端不需要注意代理是否存在。

![透明代理](media/azure-stack-border-connectivity/transparent-proxy.png)

## <a name="next-steps"></a>后续步骤
[DNS 集成](azure-stack-integrate-dns.md)