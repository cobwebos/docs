---
title: include 文件
description: include 文件
services: iot-suite
author: dominicbetts
ms.service: iot-suite
ms.topic: include
ms.date: 04/24/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 9b9e28f18208674609d0842b0e3a54e3fc661c9f
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/23/2019
ms.locfileid: "66147742"
---
## <a name="view-device-telemetry"></a>查看设备遥测数据

您可以查看从你的设备发送的遥测数据**Device Explorer**解决方案中的页。

1. 选择的设备的设备列表中预配**Device Explorer**页。 一个面板将显示有关设备的信息，其中包括设备遥测绘图：

    ![查看设备详细信息](media/iot-suite-visualize-connecting/devicesdetail.png)

1. 选择“压力”可更改遥测显示：

    ![查看压力遥测](media/iot-suite-visualize-connecting/devicespressure.png)

1. 若要查看有关设备的诊断信息，请向下滚动到“诊断”：

    ![查看设备诊断](media/iot-suite-visualize-connecting/devicesdiagnostics.png)

## <a name="act-on-your-device"></a>对设备执行操作

若要调用设备上的方法，请使用**Device Explorer**远程监视解决方案中的页。 例如，在远程监视解决方案**冷却器**设备实现**重新启动**方法。

1. 选择**设备**以导航到**Device Explorer**解决方案中的页。

1. 选择的设备的设备列表中预配**Device Explorer**页：

    ![选择真实设备](media/iot-suite-visualize-connecting/devicesselect.png)

1. 若要显示在设备可以调用的方法的列表，请选择**作业**，然后**方法**。 若要计划在多个设备上运行的作业，可以在列表中选择多个设备。 “作业”面板会显示普遍适用于所有选定设备的方法类型。

1. 选择**重新启动**，将作业名称设置为**RebootPhysicalChiller** ，然后选择**应用**:

    ![计划固件更新](media/iot-suite-visualize-connecting/deviceschedule.png)

1. 模拟设备处理该方法时，一系列消息将显示在运行设备代码的控制台中。

> [!NOTE]
> 若要跟踪解决方案中作业的状态，请选择**查看作业状态**。

## <a name="next-steps"></a>后续步骤

[自定义远程监视解决方案加速器](../articles/iot-accelerators/iot-accelerators-remote-monitoring-customize.md)一文中介绍了自定义解决方案加速器的一些方法。