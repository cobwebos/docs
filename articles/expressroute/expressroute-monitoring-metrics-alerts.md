---
title: 监视、指标和警报 - Azure ExpressRoute | Microsoft Docs
description: 此页提供有关 ExpressRoute 监视的信息
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: dbe03ef29bd28d465fa671abc915d63d4b038cb2
ms.sourcegitcommit: 116bc6a75e501b7bba85e750b336f2af4ad29f5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/20/2019
ms.locfileid: "71154767"
---
# <a name="expressroute-monitoring-metrics-and-alerts"></a>ExpressRoute 监视、指标和警报

本文可帮助你使用 Azure Monitor 了解 ExpressRoute 监视、指标和警报。 Azure Monitor 是整个 Azure 中所有指标、警报和诊断日志的一站式商店。
 
>[!NOTE]
>建议不要使用**经典指标**。
>

## <a name="expressroute-metrics"></a>ExpressRoute 指标

若要查看**指标**，请导航到 " *Azure Monitor* " 页，然后单击 "*指标*"。 若要查看**expressroute**指标，请按资源类型*ExpressRoute 线路*查看文件服务器。 若要查看**Global Reach**度量值，请按资源类型*ExpressRoute 线路*进行筛选，然后选择已启用 Global Reach 的 expressroute 线路资源。 若要查看**Expressroute 直接**指标，请按*ExpressRoute 端口*筛选资源类型。 

选择度量值后，将应用默认聚合。 您也可以应用拆分，这将显示具有不同尺寸的指标。

### <a name="available-metrics"></a>可用度量值
|**指标**|**类别**|**维度**|**功能**|
| --- | --- | --- | --- |
|ARP 可用性|可用性|<ui><li>对等机（主/辅助 ExpressRoute 路由器）</ui></li><ui><li> 对等互连类型（Private/Public/Microsoft）</ui></li>|ExpressRoute|
|Bgp 可用性|可用性|<ui><li> 对等机（主/辅助 ExpressRoute 路由器）</ui></li><ui><li> 对等类型</ui></li>|ExpressRoute|
|BitsInPerSecond|流量|<ui><li> 对等互连类型（ExpressRoute）</ui></li><ui><li>链接（ExpressRoute 直接）</ui></li>| <li> ExpressRoute</li><li>ExpressRoute Direct|
|BitsOutPerSecond|流量| <ui><li>对等互连类型（ExpressRoute）</ui></li><ui><li> 链接（ExpressRoute 直接） | <ui><li>ExpressRoute<ui><li>ExpressRoute 直接</ui></li> |
|GlobalReachBitsInPerSecond|流量|<ui><li>对等互连线路 Skey （服务密钥）</ui></li>|Global Reach|
|GlobalReachBitsOutPerSecond|流量|<ui><li>对等互连线路 Skey （服务密钥）</ui></li>|Global Reach|
|AdminState|物理连接|链接|ExpressRoute Direct|
|LineProtocol|物理连接|链接|ExpressRoute Direct|
|RxLightLevel|物理连接|<ui><li>连接</ui></li><ui><li>通道</ui></li>|ExpressRoute Direct|
|TxLightLevel|物理连接|<ui><li>连接</ui></li><ui><li>通道</ui></li>|ExpressRoute Direct|
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

## <a name="expressroute-gateway-connections-in-bitsseconds"></a>ExpressRoute 网关连接（以位/秒为单位）

![网关连接](./media/expressroute-monitoring-metrics-alerts/erconnections.jpg ) 

## <a name="alerts-for-expressroute-gateway-connections"></a>ExpressRoute 网关连接的警报

1. 若要配置警报，请导航到 **Azure Monitor**，然后单击“警报”。

   ![警报](./media/expressroute-monitoring-metrics-alerts/eralertshowto.jpg)

2. 单击“+选择目标”，然后选择 ExpressRoute 网关连接资源。

   ![target]( ./media/expressroute-monitoring-metrics-alerts/alerthowto2.jpg)
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
