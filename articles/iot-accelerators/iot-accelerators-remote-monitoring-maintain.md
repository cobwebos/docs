---
title: 在远程监视解决方案中排查设备问题 - Azure | Microsoft Docs
description: 本教程介绍如何在远程监视解决方案中排查和修正设备问题。
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 05/01/2018
ms.topic: conceptual
ms.openlocfilehash: 9a620d91238393ba0bde89f521f790b58ab35baf
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34628066"
---
# <a name="troubleshoot-and-remediate-device-issues"></a>排查和修正设备问题

本教程介绍如何使用解决方案中的“维护”页来排查和修正设备问题。 为了介绍这些功能，本教程在 Contoso IoT 应用程序中使用了一个方案。

Contoso 正在现场测试某个新的**原型**设备。 在测试期间，Contoso 操作员注意到该**原型**设备在仪表板上意外触发了温度警报。 现在，我们必须调查这个有故障的**原型**设备的行为。

本教程介绍如何执行下列操作：

>[!div class="checklist"]
> * 使用“维护”页调查警报
> * 调用设备方法来修正问题

## <a name="prerequisites"></a>先决条件

若要遵循本教程，需在 Azure 订阅中部署远程监视解决方案的实例。

如果尚未部署远程监视解决方案，应完成[部署远程监视解决方案加速器](iot-accelerators-remote-monitoring-deploy.md)教程。

## <a name="use-the-maintenance-dashboard"></a>使用维护仪表板

在“仪表板”页上，我们注意到与**原型**设备关联的规则发出了意外的温度警报：

![仪表板上显示的警报](./media/iot-accelerators-remote-monitoring-maintain/dashboardalarm.png)

若要进一步调查此问题，请选择警报旁边的“浏览警报”选项：

![在仪表板中浏览警报](./media/iot-accelerators-remote-monitoring-maintain/dashboardexplorealarm.png)

警报的详细信息视图显示以下内容：

* 警报的触发时间
* 与警报关联的设备的状态信息
* 从与警报关联的设备获得的遥测数据

![警报详细信息](./media/iot-accelerators-remote-monitoring-maintain/maintenancealarmdetail.png)

若要确认警报，请依次选择“警报实例”、“确认”。 此操作可让其他操作员知道你已看到该警报并正在进行处理。

![确认警报](./media/iot-accelerators-remote-monitoring-maintain/maintenanceacknowledge.png)

确认警报时，实例的状态将更改为“已确认”。

在列表中，可以看到负责触发温度警报的**原型**设备：

![列出导致警报的设备](./media/iot-accelerators-remote-monitoring-maintain/maintenanceresponsibledevice.png)

## <a name="remediate-the-issue"></a>修正问题

若要修正**原型**设备的问题，需在该设备上调用 **DecreaseTemperature** 方法。

若要安排某个设备的操作，请在设备列表中将其选中，然后选择“作业”。 原型设备模型指定设备必须支持的六个方法：

![查看设备支持的方法](./media/iot-accelerators-remote-monitoring-maintain/maintenancemethods.png)

选择“DecreaseTemperature”，将作业名称设置为 **DecreaseTemperature**。 然后选择“应用”：

![创建用于降低温度的作业](./media/iot-accelerators-remote-monitoring-maintain/maintenancecreatejob.png)

若要在“维护”页上跟踪作业的状态，请选择“作业”。 使用“作业”视图跟踪解决方案中的所有作业和方法调用：

![监视用于降低温度的作业](./media/iot-accelerators-remote-monitoring-maintain/maintenancerunningjob.png)

若要查看特定作业或方法调用的详细信息，请在“作业”视图中的列表内将其选中：

![查看作业详细信息](./media/iot-accelerators-remote-monitoring-maintain/maintenancejobdetail.png)

## <a name="next-steps"></a>后续步骤

本教程介绍如何执行下列操作：

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * 使用“维护”页调查警报
> * 调用设备方法来修正问题

了解如何处理设备问题后，我们建议接下来了解如何[使用模拟设备测试解决方案](iot-accelerators-remote-monitoring-test.md)。

<!-- Next tutorials in the sequence -->