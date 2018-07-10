---
title: 从 Azure 解决方案监视 IoT 设备 | Microsoft Docs
description: 本教程介绍如何使用远程监视解决方案加速器监视 IoT 设备。
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 06/08/2018
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: 5f42ed0fa5362959e5619f2d550ca1ae3711ed65
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2018
ms.locfileid: "37097455"
---
# <a name="tutorial-monitor-your-iot-devices"></a>教程：监视 IoT 设备

本教程介绍如何使用远程监视解决方案加速器来监视连接的 IoT 设备。 我们将使用解决方案仪表板来查看遥测数据、设备信息、警报和 KPI。

为了介绍这些监视功能，本教程使用了两个模拟卡车设备。 这些卡车由一家称作 Contoso 的组织进行管理，并已连接到远程监视解决方案加速器。 Contoso 操作员需要监视现场卡车的位置和行为。

本教程介绍以下操作：

>[!div class="checklist"]
> * 在仪表板中筛选设备
> * 查看实时遥测数据
> * 查看设备详细信息
> * 查看设备发出的警报
> * 查看系统 KPI

## <a name="prerequisites"></a>先决条件

若要遵循本教程，需在 Azure 订阅中部署远程监视解决方案加速器的实例。

如果尚未部署远程监视解决方案加速器，应完成[部署基于云的远程监视解决方案](quickstart-remote-monitoring-deploy.md)教程。

## <a name="choose-the-devices-to-display"></a>选择要显示的设备

若要选择想要在“仪表板”页上显示的联网设备，请使用筛选器。 如果只想显示“卡车”设备，请在筛选器下拉列表中选择内置的“卡车”筛选器：

[![在仪表板上筛选卡车](./media/iot-accelerators-remote-monitoring-monitor/dashboardtruckfilter-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardtruckfilter-expanded.png#lightbox)

应用某个筛选器时，只有与筛选条件匹配的设备才会显示在“仪表板”页上的地图中：

[![地图上只显示卡车](./media/iot-accelerators-remote-monitoring-monitor/dashboardtruckmap-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardtruckmap-expanded.png#lightbox)

筛选器还确定可在“遥测”图表中看到的设备：

[![卡车遥测数据显示在仪表板上](./media/iot-accelerators-remote-monitoring-monitor/dashboardtelemetry-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardtelemetry-expanded.png#lightbox)

若要创建、编辑和删除筛选器，请选择“管理设备组”。

## <a name="view-real-time-telemetry"></a>查看实时遥测数据

解决方案加速器会在“仪表板”页上的图表中绘制实时遥测数据。 遥测图表的顶部显示当前筛选器选择的设备的可用遥测类型：

[![卡车遥测类型](./media/iot-accelerators-remote-monitoring-monitor/dashboardtelemetryview-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardtelemetryview-expanded.png#lightbox)

若要查看温度遥测数据，请单击“温度”：

[![卡车温度遥测绘图](./media/iot-accelerators-remote-monitoring-monitor/dashboardselecttelemetry-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardselecttelemetry-expanded.png#lightbox)

## <a name="use-the-map"></a>使用地图

地图显示当前筛选器选择的模拟卡车的相关信息。 可以缩放和平移地图，以更多或更少的细节显示位置。 地图上的设备图标颜色指示该设备是否有任何未处理的**警报**或**警告**。 地图左侧显示**警报**和**警告**的汇总数目。

若要查看设备详细信息，请通过平移和缩放地图找到该设备，并在地图上选择该设备。 然后单击设备标签打开“设备详细信息”面板。 设备详细信息包括：

* 最近的遥测值
* 设备支持的方法
* 设备属性

[![在仪表板上查看设备详细信息](./media/iot-accelerators-remote-monitoring-monitor/dashboarddevicedetail-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboarddevicedetail-expanded.png#lightbox)

## <a name="view-alerts"></a>查看警报

“警报”面板显示有关设备最近发出的警报的详细信息：

[![在仪表板上查看设备警报](./media/iot-accelerators-remote-monitoring-monitor/dashboardsystemalarms-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardsystemalarms-expanded.png#lightbox)

可以使用筛选器来调整最近警报的时间跨度。 默认情况下，该面板显示过去一个小时的警报：

[![按时间筛选警报](./media/iot-accelerators-remote-monitoring-monitor/dashboardalarmsfilter-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardalarmsfilter-expanded.png#lightbox)

## <a name="view-the-system-kpis"></a>查看系统 KPI

“仪表板”页在“分析”面板中显示解决方案加速器计算得出的系统 KPI：

[![仪表板 KPI](./media/iot-accelerators-remote-monitoring-monitor/dashboardkpis-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardkpis-expanded.png#lightbox)

仪表板显示当前设备和时间跨度筛选器选择的警报的三个 KPI：

* 触发大多数警报的规则的活动警报数。
* 按设备类型显示的警报比例。
* 严重警报的百分比。

设置警报时间跨度和控制要显示的设备的相同筛选器确定了 KPI 的聚合方式。 默认情况下，该面板显示过去一小时聚合的 KPI。

## <a name="clean-up-resources"></a>清理资源

如果你打算继续学习下一篇教程，请将远程监视解决方案加速器保持为部署状态。 如果不再使用解决方案加速器，可在设置面板中停止模拟设备，以减少运行解决方案加速器所产生的费用：

[![暂停遥测](./media/iot-accelerators-remote-monitoring-monitor/togglesimulation-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/togglesimulation-expanded.png#lightbox)

准备好学习下一篇教程时，可以重启模拟设备。

如果不再需要该解决方案加速器，请从[预配的解决方案](https://www.azureiotsolutions.com/Accelerators#dashboard)页中将其删除：

![删除解决方案](media/iot-accelerators-remote-monitoring-monitor/deletesolution.png)

## <a name="next-steps"></a>后续步骤

本教程介绍了如何使用远程监视解决方案加速器中的“仪表板”页来筛选和监视模拟卡车。 若要了解如何使用解决方案加速器来检测联网设备的问题，请继续学习下一篇教程。

> [!div class="nextstepaction"]
> [检测连接到监视解决方案的设备问题](iot-accelerators-remote-monitoring-automate.md)