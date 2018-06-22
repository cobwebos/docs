---
title: 远程监视解决方案中的设备管理 - Azure | Microsoft Docs
description: 本教程介绍如何管理与远程监视解决方案连接的设备。
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 05/01/2018
ms.topic: conceptual
ms.openlocfilehash: 0f177c3a8746f801e52cdac6cb2189e9cc28e1e8
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34627273"
---
# <a name="manage-and-configure-your-devices"></a>管理和配置设备

本教程介绍远程监视解决方案的设备管理功能。 为了介绍这些功能，本教程在 Contoso IoT 应用程序中使用了一个方案。

Contoso 订购了新的机器，以扩建其某处生产场地来提高产量。 在等待交付新机器期间，你想要运行一次模拟，以验证解决方案的行为。 操作员希望能够在远程监视解决方案中管理和配置设备。

为了提供一种可扩展的方式来管理和配置设备，远程监视解决方案使用了[作业](../iot-hub/iot-hub-devguide-jobs.md)和[直接方法](../iot-hub/iot-hub-devguide-direct-methods.md)等 IoT 中心功能。 若要了解设备开发人员如何在物理设备上实现方法，请参阅[自定义远程监视解决方案加速器](iot-accelerators-remote-monitoring-customize.md)。

本教程介绍如何执行下列操作：

>[!div class="checklist"]
> * 预配模拟设备。
> * 测试模拟设备。
> * 从解决方案调用设备方法。
> * 重新配置设备。

## <a name="prerequisites"></a>先决条件

若要遵循本教程，需在 Azure 订阅中部署远程监视解决方案的实例。

如果尚未部署远程监视解决方案，应完成[部署远程监视解决方案加速器](iot-accelerators-remote-monitoring-deploy.md)教程。

## <a name="add-a-simulated-device"></a>添加模拟设备

在解决方案中导航到“设备”页，然后选择“+ 新建设备”。 在“新建设备”面板中，选择“模拟”：

![预配模拟设备](./media/iot-accelerators-remote-monitoring-manage/devicesprovision.png)

将要预配的设备数保留设置为 **1**。 选择“故障引擎”设备模型，并选择“应用”以创建模拟设备：

![预配模拟引擎设备](./media/iot-accelerators-remote-monitoring-manage/devicesprovisionengine.png)

若要了解如何预配物理设备，请参阅[将设备连接到远程监视解决方案加速器](iot-accelerators-connecting-devices-node.md)。

## <a name="test-the-simulated-device"></a>测试模拟设备

若要查看新模拟设备的详细信息，请在“设备”页上的设备列表中选择该设备。 有关设备的信息显示在“设备详细信息”面板中：

![查看新模拟引擎设备](./media/iot-accelerators-remote-monitoring-manage/devicesviewnew.png)

在“设备详细信息”中，验证新设备是否发送遥测数据。 若要查看来自设备的不同遥测数据流，请选择遥测名称，例如“振动”：

![选择要查看的遥测数据流](./media/iot-accelerators-remote-monitoring-manage/devicesvibration.png)

“设备详细信息”面板显示有关设备的其他信息，例如标记值、设备支持的方法，以及设备报告的属性。

若要查看详细诊断信息，请向下滚动到“诊断”视图。

## <a name="act-on-a-device"></a>安排设备操作

若要对一个或多个设备执行操作，请在设备列表中将其选中，然后选择“作业”。 **引擎**设备模型指定设备必须支持的三个方法：

![引擎方法](./media/iot-accelerators-remote-monitoring-manage/devicesmethods.png)

选择 **FillTank**，将作业名称设置为 **FillEngineTank**，然后选择“应用”：

![计划重启方法](./media/iot-accelerators-remote-monitoring-manage/devicesrestartengine.png)

若要在“维护”页上跟踪作业的状态，请选择“作业”：

![监视计划作业](./media/iot-accelerators-remote-monitoring-manage/maintenancerestart.png)

### <a name="methods-in-other-devices"></a>其他设备中的方法

浏览不同的模拟设备类型时，可以看到其他设备类型支持不同的方法。 在包含物理设备的部署中，设备模型指定设备应该支持的方法。 通常，设备开发人员需负责开发可让设备对方法调用做出响应的代码。

若要计划要在多个设备上运行的方法，可以在“设备”页上的列表中选择多个设备。 “作业”面板显示普遍适用于所有选定设备的方法类型。

## <a name="reconfigure-a-device"></a>重新配置设备

若要更改某个设备的配置，请在“设备”页上的设备列表将其选中，选择“作业”，然后选择“重新配置”。 “作业”面板显示选定设备的可更改属性值：

![重新配置设备](./media/iot-accelerators-remote-monitoring-manage/devicesreconfigure.png)

若要进行更改，请添加作业的名称，更新属性值，并选择“应用”：

![更新设备属性值](./media/iot-accelerators-remote-monitoring-manage/devicesreconfigurephysical.png)

若要在“维护”页上跟踪作业的状态，请选择“作业”。

## <a name="next-steps"></a>后续步骤

本教程已介绍以下操作：

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * 预配模拟设备。
> * 测试模拟设备。
> * 从解决方案调用设备方法。
> * 重新配置设备。

了解如何管理设备后，我们建议接下来了解以下操作：

* [排查和修正设备问题](iot-accelerators-remote-monitoring-maintain.md)。
* [使用模拟设备测试解决方案](iot-accelerators-remote-monitoring-test.md)。
* [将设备连接到远程监视解决方案加速器](iot-accelerators-connecting-devices-node.md)。

<!-- Next tutorials in the sequence -->