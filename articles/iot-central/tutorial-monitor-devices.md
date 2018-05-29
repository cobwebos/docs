---
title: 在 Azure IoT Central 中监视设备 | Microsoft Docs
description: 作为操作员，使用 Azure IoT Central 应用程序监视设备。
services: iot-central
author: tanmaybhagwat
ms.author: tanmayb
ms.date: 04/16/2018
ms.topic: tutorial
ms.prod: microsoft-iot-central
manager: timlt
ms.openlocfilehash: a07c9e3c28fadaead8bfaaebe4d1ee06ac66a99e
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2018
ms.locfileid: "34201381"
---
# <a name="use-azure-iot-central-to-monitor-your-devices"></a>使用 Azure IoT Central 监视设备

本教程介绍作为操作员如何使用 Microsoft Azure IoT Central 应用程序监视设备和更改设置。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 接收通知
> * 调查问题
> * 解决问题

## <a name="prerequisites"></a>先决条件

在开始之前，构建者应完成三个构建者教程以创建 Azure IoT Central 应用程序：

* [定义新设备类型](tutorial-define-device-type.md)
* [配置用于设备的规则和操作](tutorial-configure-rules.md)
* [自定义操作员的视图](tutorial-customize-operator.md)

## <a name="receive-a-notification"></a>接收通知

Azure IoT Central 将有关设备的通知作为电子邮件发送。 构建者添加了一条规则，用于在连接的空调设备中的温度超过阈值时发送通知。 检查发送到构建者选择接收通知的帐户的电子邮件。

打开你在[配置用于设备的规则和操作](tutorial-configure-rules.md)教程结束时收到的电子邮件。 在此电子邮件中，选择“单击此处打开设备”：

![应用程序构建者规则](media/tutorial-monitor-devices/email.png)

此时将在浏览器中打开前面教程中创建的“已连接空调 - 1” 模拟设备的“设备”页：

![触发通知电子邮件的设备](media/tutorial-monitor-devices/sourcedevice.png)

## <a name="investigate-an-issue"></a>调查问题

作为操作员，你可以在“度量”、“设置”、“属性”、“规则”和“仪表板”页中查看有关设备的信息。 构建者自定义了**仪表板**以显示有关已连接空调设备的重要信息。

* 选择“仪表板”视图可查看有关设备的信息：

    ![设备仪表板](media/tutorial-monitor-devices/initial_screen.png)

    仪表板上的图表显示了设备温度的曲线图。 还可以在“设置目标温度”磁贴中查看设备的当前目标温度。 你确定目标温度过高。

## <a name="remediate-an-issue"></a>解决问题

若要更改设备的目标温度，请使用“设置”页：

1. 选择“设置”。 将“设置温度”更改为 100。 选择“更新”，将新的目标温度发送到设备。 当设备确认设置更改时，设置值的状态将变为“已同步”：

    ![更新设置](media/tutorial-monitor-devices/change_settings.png)

1. 选择“仪表板”并验证新的设置值：

    ![“已更新设备”仪表板](media/tutorial-monitor-devices/new_settings.png)

## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="nextstepaction"]
> * 接收通知
> * 调查问题
> * 解决问题

现在已监视了设备，建议的下一步是[添加设备](tutorial-add-device.md)。
