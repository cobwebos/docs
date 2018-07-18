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
ms.openlocfilehash: 5702c6e9c9d75c6cccb82f1c57684ef7b9898c34
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34665987"
---
## <a name="view-device-telemetry"></a>查看设备遥测数据

可以在解决方案中的“设备”页上查看从设备发送的遥测数据。

1. 在“设备”页上的设备列表中选择已预配的设备。 一个面板将显示有关设备的信息，其中包括设备遥测绘图：

    ![查看设备详细信息](media/iot-suite-visualize-connecting/devicesdetail.png)

1. 选择“压力”可更改遥测显示：

    ![查看压力遥测](media/iot-suite-visualize-connecting/devicespressure.png)

1. 若要查看有关设备的诊断信息，请向下滚动到“诊断”：

    ![查看设备诊断](media/iot-suite-visualize-connecting/devicesdiagnostics.png)

## <a name="act-on-your-device"></a>对设备执行操作

若要对设备调用方法，请使用远程监视解决方案中的“设备”页。 例如，在远程监视解决方案中，冷却器设备实现了 FirmwareUpdate 方法。

1. 选择“设备”，导航到解决方案中的“设备”页面。

1. 在“设备”页上的设备列表中选择已预配的设备：

    ![选择物理设备](media/iot-suite-visualize-connecting/devicesselect.png)

1. 若要显示可在设备上调用的方法列表，请选择“作业”，然后选择“运行方法”。 若要计划在多个设备上运行的作业，可以在列表中选择多个设备。 “作业”面板会显示普遍适用于所有选定设备的方法类型。

1. 选择 FirmwareUpdate，将作业名称设置为 UpdatePhysicalChiller。 将“固件版本”设置为 **2.0.0**，将“固件 URI”设置为 **http://contoso.com/updates/firmware.bin**，然后选择“应用”：

    ![计划固件更新](media/iot-suite-visualize-connecting/deviceschedule.png)

1. 模拟设备处理该方法时，一系列消息将显示在运行设备代码的控制台中。

1. 更新完成时，“设备”页上将显示新的固件版本：

    ![更新已完成](media/iot-suite-visualize-connecting/complete.png)

> [!NOTE]
> 若要跟踪解决方案中作业的状态，请选择“查看”。

## <a name="next-steps"></a>后续步骤

[自定义远程监视解决方案加速器](../articles/iot-accelerators/iot-accelerators-remote-monitoring-customize.md)一文中介绍了自定义解决方案加速器的一些方法。