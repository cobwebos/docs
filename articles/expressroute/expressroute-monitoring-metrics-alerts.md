---
title: Azure ExpressRoute：监视、指标和警报
description: 此页提供有关 ExpressRoute 监视的信息
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: cherylmc
ms.openlocfilehash: 95083809f8fcfe42ae76faa0af507c7ce4acd709
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/14/2019
ms.locfileid: "74083436"
---
# <a name="expressroute-monitoring-metrics-and-alerts"></a>ExpressRoute 监视、指标和警报

本文可帮助你使用 Azure Monitor 了解 ExpressRoute 监视、指标和警报。 Azure Monitor 是整个 Azure 中所有指标、警报和诊断日志的一站式商店。
 
>[!NOTE]
>建议不要使用**经典指标**。
>

## <a name="expressroute-metrics"></a>ExpressRoute 指标

若要查看“指标”，请导航到“Azure Monitor”页，单击“指标”。 若要查看 **ExpressRoute** 指标，请按资源类型“ExpressRoute 线路”进行筛选。 若要查看 **Global Reach** 指标，请按资源类型“ExpressRoute 线路”进行筛选，然后选择一个已启用 Global Reach 的 ExpressRoute 线路资源。 若要查看 **ExpressRoute Direct** 指标，请按“ExpressRoute 端口”筛选资源类型。 

选择指标后，将应用默认聚合。 （可选）可以应用拆分，它将显示具有不同维度的指标。

### <a name="available-metrics"></a>可用指标
|**指标**|**类别**|**维度**|**功能**|
| --- | --- | --- | --- |
|ARP 可用性|可用性|<ui><li>对等机（主要/辅助 ExpressRoute 路由器）</ui></li><ui><li> 对等互连类型（专用/公共/Microsoft）</ui></li>|ExpressRoute|
|BGP 可用性|可用性|<ui><li> 对等机（主要/辅助 ExpressRoute 路由器）</ui></li><ui><li> 对等互连类型</ui></li>|ExpressRoute|
|BitsInPerSecond|交通|<ui><li> 对等互连类型 (ExpressRoute)</ui></li><ui><li>链路 (ExpressRoute Direct)</ui></li>| <li> ExpressRoute</li><li>ExpressRoute Direct|
|BitsOutPerSecond|交通| <ui><li>对等互连类型 (ExpressRoute)</ui></li><ui><li> 链路 (ExpressRoute Direct) | <ui><li>ExpressRoute<ui><li>ExpressRoute Direct</ui></li> |
|GlobalReachBitsInPerSecond|交通|<ui><li>对等互连线路密钥（服务密钥）</ui></li>|Global Reach|
|GlobalReachBitsOutPerSecond|交通|<ui><li>对等互连线路密钥（服务密钥）</ui></li>|Global Reach|
|AdminState|物理连接|链接|ExpressRoute Direct|
|LineProtocol|物理连接|链接|ExpressRoute Direct|
|RxLightLevel|物理连接|<ui><li>链路</ui></li><ui><li>通道</ui></li>|ExpressRoute Direct|
|TxLightLevel|物理连接|<ui><li>链路</ui></li><ui><li>通道</ui></li>|ExpressRoute Direct|
>[!NOTE]
>只有在至少建立了一个 Global Reach 连接的情况下，才能使用 *GlobalGlobalReachBitsInPerSecond* 和 *GlobalGlobalReachBitsOutPerSecond*。
>

## <a name="circuits-metrics"></a>线路指标

### <a name="bits-in-and-out---metrics-across-all-peerings"></a>进位和出位 - 所有对等互连的指标

可以查看给定 ExpressRoute 线路上所有对等互连的指标。

![线路指标](./media/expressroute-monitoring-metrics-alerts/ermetricspeering.jpg)

### <a name="bits-in-and-out---metrics-per-peering"></a>进位和出位 - 每个对等互连的指标

可以查看专用、公共和 Microsoft 对等互连的指标（以位/秒为单位）。

![每个对等互连的指标](./media/expressroute-monitoring-metrics-alerts/erpeeringmetrics.jpg) 

### <a name="bgp-availability---split-by-peer"></a>BGP 可用性 - 按对等机拆分  

可以查看跨对等互连和对等机（主要和辅助 ExpressRoute 路由器）的近实时 BGP 可用性。 此仪表板显示为专用对等互连启动的主要 BGP 会话以及为专用对等互连关闭的第二个 BGP 会话。 

![每个对等机的 BGP 可用性](./media/expressroute-monitoring-metrics-alerts/erBgpAvailabilityMetrics.jpg) 

### <a name="arp-availability---split-by-peering"></a>ARP 可用性 - 按对等互连拆分  

可以查看跨对等互连和对等机（主要和辅助 ExpressRoute 路由器）的近实时 [ARP](https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-arp-resource-manager) 可用性。 此仪表板显示跨两个对等机启动的专用对等互连 ARP 会话，该会话在跨 Microsoft 对等互连时会彻底关闭。 已跨两个对等机利用了默认聚合（平均）。  

![每个对等机的 ARP 可用性](./media/expressroute-monitoring-metrics-alerts/erArpAvailabilityMetrics.jpg) 

## <a name="expressroute-direct-metrics"></a>ExpressRoute Direct 指标

### <a name="admin-state---split-by-link"></a>管理状态 - 按链路拆分
可以查看 ExpressRoute Direct 端口对的每个链路的管理状态。

![er direct 管理状态](./media/expressroute-monitoring-metrics-alerts/adminstate-per-link.jpg)

### <a name="bits-in-per-second---split-by-link"></a>每秒传入位数 - 按链路拆分
可以查看 ExpressRoute Direct 端口对的跨两个链路的每秒传入位数。 

![er direct 每秒传入位数](./media/expressroute-monitoring-metrics-alerts/bits-in-per-second-per-link.jpg)

### <a name="bits-out-per-second---split-by-link"></a>每秒传出位数 - 按链路拆分
还可以查看 ExpressRoute Direct 端口对的跨两个链路的每秒传出位数。 

![er direct 每秒传出位数](./media/expressroute-monitoring-metrics-alerts/bits-out-per-second-per-link.jpg)

### <a name="line-protocol---split-by-link"></a>线路协议 - 按链路拆分
可以查看 ExpressRoute Direct 端口对的跨每个链路的线路协议。

![er direct 线路协议](./media/expressroute-monitoring-metrics-alerts/line-protocol-per-link.jpg)

### <a name="rx-light-level---split-by-link"></a>Rx 轻型级别 - 按链路拆分
可以查看每个端口的 Rx 轻型级别（ExpressRoute Direct 端口的轻型级别是**接收**）。 正常的 Rx 轻型级别通常在 -10 到 0 dBm 范围内

![er direct 线路 Rx 轻型级别](./media/expressroute-monitoring-metrics-alerts/rxlight-level-per-link.jpg)

### <a name="tx-light-level---split-by-link"></a>Tx 轻型级别 - 按链路拆分
可以查看每个端口的 Tx 轻型级别（ExpressRoute Direct 端口的轻型级别为**传输**）。 正常的 Tx 轻型级别通常在 -10 到 0 dBm 范围内

![er direct 线路 Rx 轻型级别](./media/expressroute-monitoring-metrics-alerts/txlight-level-per-link.jpg)

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
