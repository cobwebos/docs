---
title: Azure ExpressRoute 监视、指标和警报 | Microsoft Docs
description: 此页提供有关 ExpressRoute 监视的信息
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: cherylmc
ms.openlocfilehash: fcdaf10ad30cee0e7b79304ff21d67560522875c
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/17/2018
ms.locfileid: "49377791"
---
# <a name="expressroute-monitoring-metrics-and-alerts"></a>ExpressRoute 监视、指标和警报

 本文可帮助你了解 ExpressRoute 监视、指标和警报。 Azure Monitor 是整个 Azure 中所有指标、警报和诊断日志的一站式商店。

## <a name="circuit-metrics"></a>线路指标

若要导航到“指标”，请单击要监视的线路的 ExpressRoute 页。 在“监视”下，可以查看**指标**。 选择 BitsInPerSecond 或 BitsOutPerSecond 以及聚合。 可以选择性地应用拆分，这将显示每个对等互连类型的指标。

![线路指标](./media/expressroute-monitoring-metrics-alerts/ermetricspeering.jpg)

## <a name="metrics-per-peering"></a>每个对等互连的指标

可以查看专用、公共和 Microsoft 对等互连的指标（以位/秒为单位）。

![每个对等互连的指标](./media/expressroute-monitoring-metrics-alerts/erpeeringmetrics.jpg) 

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
* 配置 ExpressRoute 连接。
  
  * [创建和修改线路](expressroute-howto-circuit-arm.md)
  * [创建和修改对等互连配置](expressroute-howto-routing-arm.md)
  * [将 VNet 链接到 ExpressRoute 线路](expressroute-howto-linkvnet-arm.md)
