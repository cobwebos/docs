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
ms.openlocfilehash: 92ec03e20fb6e681a0afd14048449ad004ebca0c
ms.sourcegitcommit: 007ee4ac1c64810632754d9db2277663a138f9c4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2019
ms.locfileid: "69991480"
---
# <a name="expressroute-monitoring-metrics-and-alerts"></a>ExpressRoute 监视、指标和警报

本文可帮助你使用 Azure Monitor 了解 ExpressRoute 监视、指标和警报。 Azure Monitor 是整个 Azure 中所有指标、警报和诊断日志的一站式商店。
 
>[!NOTE]
>建议不要使用**经典指标**。
>

## <a name="circuit-metrics"></a>线路指标

若要导航到“指标”，请单击要监视的线路的 ExpressRoute 页。 在“监视”下，可以查看**指标**。 从下面列出的指标中进行选择。 将应用默认聚合。 您也可以应用拆分, 这将显示具有不同尺寸的指标。

### <a name="metrics-available"></a>可用指标: 
* **可用性** 
    * Arp 可用性
      * 可用维度:
        * 对等机 (主/辅助 ExpressRoute 路由器)
        * 对等互连类型 (Private/Public/Microsoft)
    * Bgp 可用性
      * 可用维度:
        * 对等机 (主/辅助 ExpressRoute 路由器)
        * 对等互连类型 (Private/Public/Microsoft)
* **交易**
    * BitsInPerSecond
      * 可用维度:
        * 对等互连类型 (Private/Public/Microsoft)
    * BitsOutPerSecond
      * 可用维度:
        * 对等互连类型 (Private/Public/Microsoft)
    * GlobalReachBitsInPerSecond
      * 可用维度:
        * 对等互连线路 Skey (服务密钥)
    * GlobalReachBitsOutPerSecond
      * 可用维度:
        * 对等互连线路 Skey (服务密钥)

>[!NOTE]
>仅当至少建立了一个 Global Reach 连接时, 才会显示使用*GlobalGlobalReachBitsInPerSecond*和*GlobalGlobalReachBitsOutPerSecond* 。
>

## <a name="bits-in-and-out---metrics-across-all-peerings"></a>所有对等互连中的位和出量指标

可以在给定 ExpressRoute 线路上的所有对等互连中查看度量值。

![线路指标](./media/expressroute-monitoring-metrics-alerts/ermetricspeering.jpg)

## <a name="bits-in-and-out---metrics-per-peering"></a>每个对等互连中的位和出量指标

可以查看专用、公共和 Microsoft 对等互连的指标（以位/秒为单位）。

![每个对等互连的指标](./media/expressroute-monitoring-metrics-alerts/erpeeringmetrics.jpg) 

## <a name="bgp-availability---split-by-peer"></a>BGP 可用性-按对等方拆分  

可以查看跨对等互连和对等互连 (主要和辅助 ExpressRoute 路由器) 的 BGP 的近乎可用情况。 此仪表板显示专用对等互连的主 BGP 会话, 并为专用对等互连关闭第二个 BGP 会话。 

![每个对等 BGP 可用性](./media/expressroute-monitoring-metrics-alerts/erBgpAvailabilityMetrics.jpg) 

## <a name="arp-availability---split-by-peering"></a>ARP 可用性-按对等互连  

你可以查看跨对等互连和对等互连 (主和辅助 ExpressRoute 路由器) 的[ARP](https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-arp-resource-manager)的近乎可用情况。 此仪表板显示了在两个对等方上的专用对等 ARP 会话, 但在对等互连中针对 Microsoft 对等互连完成了。 在两个对等节点上使用了默认聚合 (平均值)。  

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
