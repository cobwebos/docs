---
title: 教程：通过 Azure 解决方案监视 IoT 设备 | Microsoft Docs
description: 本教程介绍如何使用远程监视解决方案加速器监视 IoT 设备。
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 07/19/2018
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: 1f9e5885e79e184b621ba2be7e2a8f329e31a6b1
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/27/2018
ms.locfileid: "39284483"
---
# <a name="tutorial-monitor-your-iot-devices"></a>教程：监视 IoT 设备

本教程介绍如何使用远程监视解决方案加速器来监视连接的 IoT 设备。 我们将使用解决方案仪表板来查看遥测数据、设备信息、警报和 KPI。

本教程使用两台模拟的卡车设备，它们发送位置、速度和货物温度遥测数据。 这些卡车由一家称作 Contoso 的组织进行管理，并已连接到远程监视解决方案加速器。 Contoso 操作员需要监视其中一辆卡车 (truck-02) 在现场的位置和行为。

本教程介绍以下操作：

>[!div class="checklist"]
> * 在仪表板中筛选设备
> * 查看实时遥测数据
> * 查看设备详细信息
> * 查看设备发出的警报
> * 查看系统 KPI

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [iot-accelerators-tutorial-prereqs](../../includes/iot-accelerators-tutorial-prereqs.md)]

## <a name="choose-the-devices-to-display"></a>选择要显示的设备

若要选择想要在“仪表板”页上显示的联网设备，请使用筛选器。 如果只想显示“卡车”设备，请在筛选器下拉列表中选择内置的“卡车”筛选器：

[![在仪表板上筛选卡车](./media/iot-accelerators-remote-monitoring-monitor/dashboardtruckfilter-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardtruckfilter-expanded.png#lightbox)

应用某个筛选器时，只有与筛选条件匹配的设备才会显示在“仪表板”页上的地图上和遥测数据面板中。 你可以看到有两辆卡车连接到了解决方案加速器，包括 truck-02：

[![地图上只显示卡车](./media/iot-accelerators-remote-monitoring-monitor/dashboardtruckmap-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardtruckmap-expanded.png#lightbox)

若要创建、编辑和删除筛选器，请单击“管理设备组”。

## <a name="view-real-time-telemetry"></a>查看实时遥测数据

解决方案加速器会在“仪表板”页上的图表中绘制实时遥测数据。 遥测数据图表的顶部显示当前筛选器选择的设备（包括 truck-02）的可用遥测类型。 默认情况下，图表显示卡车的纬度和卡车 02 似乎是固定的：

[![卡车遥测类型](./media/iot-accelerators-remote-monitoring-monitor/dashboardtelemetryview-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardtelemetryview-expanded.png#lightbox)

若要查看卡车的温度遥测数据，请单击“温度”。 可以查看 truck-02 的温度在过去一小时内是如何变化的。

[![卡车温度遥测绘图](./media/iot-accelerators-remote-monitoring-monitor/dashboardselecttelemetry-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardselecttelemetry-expanded.png#lightbox)

## <a name="view-the-map"></a>查看地图

地图显示当前筛选器选择的模拟卡车的相关信息。 可以缩放和平移地图，以更多或更少的细节显示位置。 地图上设备图标的颜色指示该设备是否有任何未处理的**警报**（深蓝色）或**警告**（红色）。 地图左侧显示**警报**和**警告**的汇总数目。

若要查看 truck-02 的详细信息，请通过平移和缩放地图找到该设备，并在地图上选择该卡车。 然后单击设备标签打开“设备详细信息”面板。 设备详细信息包括：

* 最近的遥测值
* 设备支持的方法
* 设备属性

[![在仪表板上查看设备详细信息](./media/iot-accelerators-remote-monitoring-monitor/dashboarddevicedetail-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboarddevicedetail-expanded.png#lightbox)

## <a name="view-alerts"></a>查看警报

“警报”面板显示来自设备的最新警报的详细信息。 来自 truck-02 的警报指示温度超过正常货物温度：

[![在仪表板上查看设备警报](./media/iot-accelerators-remote-monitoring-monitor/dashboardsystemalarms-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardsystemalarms-expanded.png#lightbox)

可以使用筛选器来调整最近警报的时间跨度。 默认情况下，该面板显示过去一个小时的警报。

## <a name="view-the-system-kpis"></a>查看系统 KPI

“仪表板”页在“分析”面板中显示解决方案加速器计算得出的系统 KPI：

[![仪表板 KPI](./media/iot-accelerators-remote-monitoring-monitor/dashboardkpis-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardkpis-expanded.png#lightbox)

仪表板显示当前设备和时间跨度筛选器选择的警报的三个 KPI：

* 触发大多数警报的规则的活动警报数。
* 按设备类型显示的警报比例。
* 严重警报的百分比。

对于 truck-02，所有警报都是关于超过正常货物温度的警告。

设置警报时间跨度和控制要显示的设备的相同筛选器确定了 KPI 的聚合方式。 默认情况下，该面板显示过去一小时聚合的 KPI。

[!INCLUDE [iot-accelerators-tutorial-cleanup](../../includes/iot-accelerators-tutorial-cleanup.md)]

## <a name="next-steps"></a>后续步骤

本教程介绍了如何使用远程监视解决方案加速器中的“仪表板”页来筛选和监视模拟卡车。 若要了解如何使用解决方案加速器来检测联网设备的问题，请继续学习下一篇教程。

> [!div class="nextstepaction"]
> [检测连接到监视解决方案的设备问题](iot-accelerators-remote-monitoring-automate.md)