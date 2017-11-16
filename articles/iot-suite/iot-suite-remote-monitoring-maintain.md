---
title: "在远程监视解决方案中排查设备问题 - Azure | Microsoft Docs"
description: "本教程介绍如何在远程监视解决方案中排查和修正设备问题。"
services: 
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 11/10/2017
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 46b0269b3f5b98e1bcecdbeabd5544e97bb5c06c
ms.sourcegitcommit: bc8d39fa83b3c4a66457fba007d215bccd8be985
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/10/2017
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

如果尚未部署远程监视解决方案，应完成[部署远程监视预配置解决方案](iot-suite-remote-monitoring-deploy.md)教程。

## <a name="use-the-maintenance-dashboard"></a>使用维护仪表板

在“仪表板”页上，我们注意到与**原型**设备关联的规则发出了意外的温度警报：

![仪表板上显示的警报](media/iot-suite-remote-monitoring-maintain/dashboardalarm.png)

若要进一步调查此问题，请选择警报旁边的“浏览警报”选项：

![在仪表板中浏览警报](media/iot-suite-remote-monitoring-maintain/dashboardexplorealarm.png)

现在，可以在“维护”页上看到警报列表：

![维护页上的警报列表](media/iot-suite-remote-monitoring-maintain/maintenancealarms.png)

若要显示警报的详细信息，请“警报”列表中选择该警报。 详细信息视图显示：

* 警报的触发时间
* 与警报关联的设备的状态信息
* 与警报关联的设备发出的遥测数据

![警报详细信息](media/iot-suite-remote-monitoring-maintain/maintenancealarmdetail.png)

若要确认警报，请依次选择“警报出现”、“确认”。 此操作可让其他操作员知道你已看到该警报并正在进行处理。

![确认警报](media/iot-suite-remote-monitoring-maintain/maintenanceacknowledge.png)

在列表中，可以看到负责触发温度警报的**原型**设备：

![导致生成警报的设备列表](media/iot-suite-remote-monitoring-maintain/maintenanceresponsibledevice.png)

## <a name="remediate-the-issue"></a>修正问题

若要修正**原型**设备的问题，需在该设备上调用 **DecreaseTemperature** 方法。

若要安排某个设备的操作，请在设备列表中将其选中，然后选择“计划”。 **引擎**设备模型指定设备必须支持的三个方法：

![查看设备支持的方法](media/iot-suite-remote-monitoring-maintain/maintenancemethods.png)

选择“DecreaseTemperature”，将作业名称设置为 **DecreaseTemperature**。 然后选择“应用”：

![创建用于降低温度的作业](media/iot-suite-remote-monitoring-maintain/maintenancecreatejob.png)

若要在“维护”页上跟踪作业的状态，请选择“系统状态”。 使用“系统状态”视图跟踪解决方案中的所有作业和方法调用：

![监视用于降低温度的作业](media/iot-suite-remote-monitoring-maintain/maintenancerunningjob.png)

若要查看特定作业或方法调用的详细信息，请在“系统状态”视图中的列表内将其选中：

![查看作业详细信息](media/iot-suite-remote-monitoring-maintain/maintenancejobdetail.png)

## <a name="next-steps"></a>后续步骤

本教程已介绍以下操作：

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * 使用“维护”页调查警报
> * 调用设备方法来修正问题

了解如何处理设备问题后，我们建议接下来了解如何[使用模拟设备测试解决方案](iot-suite-remote-monitoring-test.md)。

<!-- Next tutorials in the sequence -->