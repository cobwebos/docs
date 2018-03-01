---
title: "远程监视解决方案中的高级监视 - Azure | Microsoft Docs"
description: "本教程介绍如何使用远程监视解决方案仪表板监视设备。"
services: 
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 02/22/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: fe0d936b4ee0d7703222c86c00959869b99f7851
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="perform-advanced-monitoring-using-the-remote-monitoring-solution"></a>使用远程监视解决方案执行高级监视

本教程介绍远程监视仪表板的功能。 为了介绍这些功能，本教程在 Contoso IoT 应用程序中使用了一个方案。

在本教程，我们将使用两个 Contoso 卡车模拟设备来了解如何从预配置解决方案仪表板监视设备。 Contoso 操作员需要监视现场卡车的位置和行为。

本教程介绍如何执行下列操作：

>[!div class="checklist"]
> * 在仪表板中筛选设备
> * 查看实时遥测数据
> * 查看设备详细信息
> * 查看设备发出的警报
> * 查看系统 KPI

## <a name="prerequisites"></a>先决条件

若要遵循本教程，需在 Azure 订阅中部署远程监视解决方案的实例。

如果尚未部署远程监视解决方案，应完成[部署远程监视预配置解决方案](iot-suite-remote-monitoring-deploy.md)教程。

## <a name="choose-the-devices-to-display"></a>选择要显示的设备

若要选择想要在“仪表板”页上显示的设备，请使用筛选器。 如果只想显示“卡车”设备，请在筛选器下拉列表中选择内置的“卡车”筛选器：

![在仪表板上筛选卡车](media/iot-suite-remote-monitoring-monitor/dashboardtruckfilter.png)

应用某个筛选器时，只有与筛选条件匹配的设备才会显示在“仪表板”页上的地图中：

![地图上的卡车显示信息](media/iot-suite-remote-monitoring-monitor/dashboardtruckmap.png)

筛选器还确定可在“遥测”图表中看到的设备：

![仪表板上的卡车遥测显示信息](media/iot-suite-remote-monitoring-monitor/dashboardtelemetry.png)

若要创建、编辑和删除筛选器，请选择“管理筛选器”。

## <a name="view-real-time-telemetry"></a>查看实时遥测数据

预配置解决方案会在“仪表板”页上的图表中绘制详细的实时遥测数据。 遥测图表显示当前筛选器选择的设备的遥测信息：

![卡车遥测绘图](media/iot-suite-remote-monitoring-monitor/dashboardtelemetryview.png)

若要选择想要查看的遥测值，请在图表顶部选择相应的遥测类型：

![卡车遥测绘图](media/iot-suite-remote-monitoring-monitor/dashboardselecttelemetry.png)

若要暂停实时遥测显示，请选择“流动”。 若要重新启用实时显示，请选择“暂停”：

![暂停和重启遥测显示](media/iot-suite-remote-monitoring-monitor/dashboardtelemetrypause.png)

## <a name="use-the-map"></a>使用地图

地图显示当前筛选器选择的模拟卡车的相关信息。 可以缩放和平移地图，以更多或更少的细节显示位置。 地图上的设备图标指示设备中尚未解决的任何**警报**或**警告**。 地图左侧显示了**警报**和**警告**的汇总数目。

若要查看设备详细信息，请通过平移和缩放地图找到该设备，然后在地图上单击该设备。 详细信息包括：

* 最近的遥测值
* 设备支持的方法
* 设备属性

![在仪表板上查看设备详细信息](media/iot-suite-remote-monitoring-monitor/dashboarddevicedetail.png)

## <a name="view-alarms-from-your-devices"></a>查看设备发出的警报

地图突出显示了当前筛选器中存在**警报**和**警告**的设备。 “系统警报”面板显示有关设备最近发出的警报的详细信息：

![在仪表板上查看系统警报](media/iot-suite-remote-monitoring-monitor/dashboardsystemalarms.png)

可以使用“系统警报”筛选器来调整最近警报的时间跨度。 默认情况下，该面板显示过去一个小时的警报：

![按时间筛选警报](media/iot-suite-remote-monitoring-monitor/dashboardalarmsfilter.png)

## <a name="view-the-system-kpis"></a>查看系统 KPI

“仪表板”页显示系统 KPI：

![按时间筛选警报](media/iot-suite-remote-monitoring-monitor/dashboardkpis.png)

可以使用“系统 KPI”筛选器来调整 KPI 聚合的时间跨度。 默认情况下，该面板显示过去一小时聚合的 KPI。

## <a name="next-steps"></a>后续步骤

本教程介绍了如何使用“仪表板”页来筛选和监视远程监视解决方案中预配的模拟卡车：

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * 在仪表板中筛选设备
> * 查看实时遥测数据
> * 查看设备详细信息
> * 查看设备发出的警报
> * 查看系统 KPI

了解如何监视设备后，我们建议接下来了解以下操作：

* [使用基于阈值的规则检测问题](./iot-suite-remote-monitoring-automate.md)。
* [管理和配置设备](./iot-suite-remote-monitoring-manage.md)。
* [排查和修正设备问题](./iot-suite-remote-monitoring-maintain.md)。
* [使用模拟设备测试解决方案](iot-suite-remote-monitoring-test.md)。

<!-- Next tutorials in the sequence -->