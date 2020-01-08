---
title: Azure ExpressRoute：监视、指标和警报
description: 此页提供有关 ExpressRoute 监视的信息
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: cherylmc
ms.openlocfilehash: 268a7e7c94285d3c4fdcb0c5fb91b685c09b58c8
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75436915"
---
# <a name="expressroute-monitoring-metrics-and-alerts"></a>ExpressRoute 监视、指标和警报

本文可帮助你使用 Azure Monitor 了解 ExpressRoute 监视、指标和警报。 Azure Monitor 是整个 Azure 中所有指标、警报和诊断日志的一站式商店。
 
>[!NOTE]
>建议不要使用**经典指标**。
>

## <a name="expressroute-metrics"></a>ExpressRoute 指标

若要查看**指标**，请导航到 " *Azure Monitor* " 页，然后单击 "*指标*"。 若要查看**expressroute**指标，请按资源类型*ExpressRoute 线路*进行筛选。 若要查看**Global Reach**度量值，请按资源类型*ExpressRoute 线路*进行筛选，然后选择已启用 Global Reach 的 expressroute 线路资源。 若要查看**Expressroute 直接**指标，请按*ExpressRoute 端口*筛选资源类型。 

选择度量值后，将应用默认聚合。 您也可以应用拆分，这将显示具有不同尺寸的指标。

### <a name="available-metrics"></a>可用度量值
|**指标**|**类别**|**维度**|**功能**|
| --- | --- | --- | --- |
|ARP 可用性|可用性|<ui><li>对等机（主/辅助 ExpressRoute 路由器）</ui></li><ui><li> 对等互连类型（Private/Public/Microsoft）</ui></li>|ExpressRoute|
|Bgp 可用性|可用性|<ui><li> 对等机（主/辅助 ExpressRoute 路由器）</ui></li><ui><li> 对等互连类型</ui></li>|ExpressRoute|
|BitsInPerSecond|交通|<ui><li> 对等互连类型（ExpressRoute）</ui></li><ui><li>链接（ExpressRoute 直接）</ui></li>| <li> ExpressRoute</li><li>ExpressRoute Direct|
|BitsOutPerSecond|交通| <ui><li>对等互连类型（ExpressRoute）</ui></li><ui><li> 链接（ExpressRoute 直接） | <ui><li>ExpressRoute<ui><li>ExpressRoute 直接</ui></li> |
|GlobalReachBitsInPerSecond|交通|<ui><li>对等互连线路 Skey （服务密钥）</ui></li>|全球覆盖|
|GlobalReachBitsOutPerSecond|交通|<ui><li>对等互连线路 Skey （服务密钥）</ui></li>|全球覆盖|
|AdminState|物理连接|链接|ExpressRoute Direct|
|LineProtocol|物理连接|链接|ExpressRoute Direct|
|RxLightLevel|物理连接|<ui><li>链接</ui></li><ui><li>通道</ui></li>|ExpressRoute Direct|
|TxLightLevel|物理连接|<ui><li>链接</ui></li><ui><li>通道</ui></li>|ExpressRoute Direct|
>[!NOTE]
>仅当至少建立了一个 Global Reach 连接时，才会显示使用*GlobalGlobalReachBitsInPerSecond*和*GlobalGlobalReachBitsOutPerSecond* 。
>

## <a name="circuits-metrics"></a>线路指标

### <a name="bits-in-and-out---metrics-across-all-peerings"></a>所有对等互连中的位和出量指标

可以在给定 ExpressRoute 线路上的所有对等互连中查看度量值。

![线路指标](./media/expressroute-monitoring-metrics-alerts/ermetricspeering.jpg)

### <a name="bits-in-and-out---metrics-per-peering"></a>每个对等互连中的位和出量指标

可以查看专用、公共和 Microsoft 对等互连的指标（以位/秒为单位）。

![每个对等互连的指标](./media/expressroute-monitoring-metrics-alerts/erpeeringmetrics.jpg) 

### <a name="bgp-availability---split-by-peer"></a>BGP 可用性-按对等方拆分  

可以查看跨对等互连和对等互连（主要和辅助 ExpressRoute 路由器）的 BGP 的近乎可用情况。 此仪表板显示专用对等互连的主 BGP 会话，并为专用对等互连关闭第二个 BGP 会话。 

![每个对等 BGP 可用性](./media/expressroute-monitoring-metrics-alerts/erBgpAvailabilityMetrics.jpg) 

### <a name="arp-availability---split-by-peering"></a>ARP 可用性-按对等互连  

你可以查看跨对等互连和对等互连（主和辅助 ExpressRoute 路由器）的[ARP](https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-arp-resource-manager)的近乎可用情况。 此仪表板显示了在两个对等方上的专用对等 ARP 会话，但在对等互连中针对 Microsoft 对等互连完成了。 在两个对等节点上使用了默认聚合（平均值）。  

![每个对等端的 ARP 可用性](./media/expressroute-monitoring-metrics-alerts/erArpAvailabilityMetrics.jpg) 

## <a name="expressroute-direct-metrics"></a>ExpressRoute 直接指标

### <a name="admin-state---split-by-link"></a>管理员状态-按链接拆分
可以查看 ExpressRoute 直接端口对的每个链接的管理状态。

![er 直接管理状态](./media/expressroute-monitoring-metrics-alerts/adminstate-per-link.jpg)

### <a name="bits-in-per-second---split-by-link"></a>每秒的位数-按链接拆分
可以跨 ExpressRoute 直接端口对的两个链接查看每秒的位数。 

![每秒的 er 直接位数](./media/expressroute-monitoring-metrics-alerts/bits-in-per-second-per-link.jpg)

### <a name="bits-out-per-second---split-by-link"></a>每秒的位数-按链接拆分
你还可以跨 ExpressRoute 直接端口对的两个链接查看每秒的位数。 

![每秒的 er 直接位数](./media/expressroute-monitoring-metrics-alerts/bits-out-per-second-per-link.jpg)

### <a name="line-protocol---split-by-link"></a>线路协议-按链接拆分
可以跨 ExpressRoute 直接端口对的每个链接查看线路协议。

![er 直接连线协议](./media/expressroute-monitoring-metrics-alerts/line-protocol-per-link.jpg)

### <a name="rx-light-level---split-by-link"></a>Rx 轻型级别-按链接拆分
可以查看每个端口的 Rx 轻型级别（ExpressRoute 直接端口**接收**的轻型级别）。 正常 Rx 轻型级别通常在-10 到 0 dBm 范围内

![er 直接行 Rx 浅色级别](./media/expressroute-monitoring-metrics-alerts/rxlight-level-per-link.jpg)

### <a name="tx-light-level---split-by-link"></a>Tx 轻型级别-按链接拆分
可以查看每个端口的 Tx 轻型级别（ExpressRoute 直接端口正在**传输**的轻型级别）。 正常的 Tx 轻型级别通常在-10 到 0 dBm 范围内

![er 直接行 Rx 浅色级别](./media/expressroute-monitoring-metrics-alerts/txlight-level-per-link.jpg)

## <a name="expressroute-gateway-connections-in-bitsseconds"></a>ExpressRoute 网关连接（以位/秒为单位）

![网关连接](./media/expressroute-monitoring-metrics-alerts/erconnections.jpg )

## <a name="alerts-for-expressroute-gateway-connections"></a>ExpressRoute 网关连接的警报

1. 若要配置警报，请导航到 **Azure Monitor**，然后单击“警报”。

   ![alerts](./media/expressroute-monitoring-metrics-alerts/eralertshowto.jpg)

2. 单击“+选择目标”，然后选择 ExpressRoute 网关连接资源。

   ![目标]( ./media/expressroute-monitoring-metrics-alerts/alerthowto2.jpg)
3. 定义警报详细信息。

   ![操作组](./media/expressroute-monitoring-metrics-alerts/alerthowto3.jpg)

4. 定义和添加操作组。

   ![添加操作组](./media/expressroute-monitoring-metrics-alerts/actiongroup.png)

## <a name="alerts-based-on-each-peering"></a>基于每个对等互连的警报

 ![内容](./media/expressroute-monitoring-metrics-alerts/basedpeering.jpg)

## <a name="configure-alerts-for-activity-logs-on-circuits"></a>为线路上的活动日志配置警报

在**警报条件**中，可以选择“活动日志”作为信号类型并选择“信号”。

  ![另一个](./media/expressroute-monitoring-metrics-alerts/alertshowto6activitylog.jpg)
  
## <a name="next-steps"></a>后续步骤

配置 ExpressRoute 连接。
  
  * [创建和修改线路](expressroute-howto-circuit-arm.md)
  * [创建和修改对等互连配置](expressroute-howto-routing-arm.md)
  * [将 VNet 链接到 ExpressRoute 线路](expressroute-howto-linkvnet-arm.md)
