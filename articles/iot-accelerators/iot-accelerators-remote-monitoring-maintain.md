---
title: 在远程监视解决方案中使用警报 - Azure | Microsoft Docs
description: 本教程介绍如何使用警报来识别和解决已连接到远程监视解决方案加速器的设备的问题。
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/08/2018
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: 853fa2b80e04dd8d9225d023db8030fed044ed7f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/24/2020
ms.locfileid: "73890922"
---
# <a name="tutorial-troubleshoot-and-fix-device-issues"></a>教程：排查并修复设备问题

在本教程中，我们将使用远程监视解决方案加速器来识别和解决联网 IoT 设备的问题。 我们将使用解决方案加速器仪表板中的警报来识别问题，然后运行远程作业来解决这些问题。

Contoso 正在现场测试某个新的**原型**设备。 在测试期间，Contoso 操作员注意到该**原型**设备在仪表板上意外触发了温度警报。 现在，我们必须调查这个有故障的**原型**设备的行为，并解决问题。

本教程介绍以下操作：

>[!div class="checklist"]
> * 调查设备发出的警报
> * 解决设备的问题

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [iot-accelerators-tutorial-prereqs](../../includes/iot-accelerators-tutorial-prereqs.md)]

## <a name="investigate-an-alert"></a>调查警报

在“仪表板”  页上，我们注意到与**原型**设备关联的规则发出了意外的温度警报：

[![仪表板上显示的警报](./media/iot-accelerators-remote-monitoring-maintain/dashboardalarm-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/dashboardalarm-expanded.png#lightbox)

若要进一步调查此问题，请选择警报旁边的“浏览警报”  选项：

[![在仪表板中浏览警报](./media/iot-accelerators-remote-monitoring-maintain/dashboardexplorealarm-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/dashboardexplorealarm-expanded.png#lightbox)

警报的详细信息视图显示以下内容：

* 警报的触发时间
* 与警报关联的设备的状态信息
* 从与警报关联的设备获得的遥测数据

[![警报详细信息](./media/iot-accelerators-remote-monitoring-maintain/maintenancealarmdetail-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/maintenancealarmdetail-expanded.png#lightbox)

若要确认警报，请依次选择所有“警报实例”、“确认”。   此操作可让其他操作员知道你已看到该警报并正在进行处理：

[![确认警报](./media/iot-accelerators-remote-monitoring-maintain/maintenanceacknowledge-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/maintenanceacknowledge-expanded.png#lightbox)

确认警报时，实例的状态将更改为“已确认”  。

在导致警报的设备列表中，可以看到导致触发温度警报的**原型**设备：

[![列出导致警报的设备](./media/iot-accelerators-remote-monitoring-maintain/maintenanceresponsibledevice-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/maintenanceresponsibledevice-expanded.png#lightbox)

## <a name="resolve-the-issue"></a>解决问题

若要解决**原型**设备的问题，需在该设备上调用 **DecreaseTemperature** 方法。

若要对某台设备执行操作，请在导致警报的设备列表中将其选中，然后选择“作业”。  **原型**设备模型支持六种方法：

[![查看设备支持的方法](./media/iot-accelerators-remote-monitoring-maintain/maintenancemethods-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/maintenancemethods-expanded.png#lightbox)

选择“DecreaseTemperature”，将作业名称设置为 **DecreaseTemperature**。 然后单击“应用”： 

[![创建用于降低温度的作业](./media/iot-accelerators-remote-monitoring-maintain/maintenancecreatejob-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/maintenancecreatejob-expanded.png#lightbox)

若要跟踪作业的状态，请单击“查看作业状态”。  使用“作业”视图跟踪解决方案中的所有作业和方法调用  ：

[![监视用于降低温度的作业](./media/iot-accelerators-remote-monitoring-maintain/maintenancerunningjob-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/maintenancerunningjob-expanded.png#lightbox)

可以在“仪表板”页上通过查看遥测数据来检查设备温度是否已下降： 

[![查看温度下降情况](./media/iot-accelerators-remote-monitoring-maintain/jobresult-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/jobresult-expanded.png#lightbox)

[!INCLUDE [iot-accelerators-tutorial-cleanup](../../includes/iot-accelerators-tutorial-cleanup.md)]

## <a name="next-steps"></a>后续步骤

本教程已介绍如何使用警报来识别设备问题，以及如何对这些设备采取措施以解决问题。 若要了解如何将实际设备连接到解决方案加速器，请继续阅读操作指南文章。

了解如何处理设备问题后，我们建议接下来了解如何[将设备连接到远程监视解决方案加速器](iot-accelerators-connecting-devices.md)。
