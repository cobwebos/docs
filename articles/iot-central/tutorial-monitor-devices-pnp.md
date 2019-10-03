---
title: 在 Azure IoT Central 中监视设备 | Microsoft Docs
description: 作为操作员，使用 Azure IoT Central 应用程序监视设备。
author: dominicbetts
ms.author: dobett
ms.date: 08/06/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: cf50fd80bdbce5895bd1da39700d1c6e4cdcc230
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/21/2019
ms.locfileid: "69878778"
---
# <a name="tutorial-use-azure-iot-central-to-monitor-your-devices-preview-features"></a>教程：使用 Azure IoT Central 监视设备（预览功能）

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

本教程介绍作为操作员如何使用 Microsoft Azure IoT Central 应用程序监视设备和更改设置。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 接收通知
> * 调查问题
> * 解决问题

## <a name="prerequisites"></a>先决条件

在开始之前，构建者应完成三个构建者教程以创建 Azure IoT Central 应用程序：

* [定义新设备类型](tutorial-define-device-type-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
* [添加设备](tutorial-add-device-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
* [为设备配置规则和操作](tutorial-configure-rules-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)

## <a name="receive-a-notification"></a>接收通知

Azure IoT Central 将有关设备的通知作为电子邮件发送。 构建者添加了一条规则，以便在连接的环境传感器设备中的温度超过阈值时发送通知。 检查发送到构建者选择接收通知的帐户的电子邮件。

打开你在[配置用于设备的规则和操作](tutorial-configure-rules-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)教程结束时收到的电子邮件。 在电子邮件中，选择指向设备的链接：

![警报通知电子邮件](media/tutorial-monitor-devices-pnp/email.png)

此时将在浏览器中打开前面教程中创建的环境传感器模拟设备的**仪表板**视图：

![触发通知电子邮件的设备](media/tutorial-monitor-devices-pnp/dashboard.png)

## <a name="investigate-an-issue"></a>调查问题

作为操作员，你可以在“仪表板”  、“环境传感器属性”  和“命令”  页上查看有关设备的信息。 构建者自定义了“仪表板”  和“环境传感器属性”  页，以显示有关连接的环境传感器设备的重要信息。

选择“仪表板”  视图可查看有关设备的信息。

仪表板上的图表显示了设备温度的曲线图。 还可以在“设备属性”  磁贴中查看设备的当前目标温度。 你确定目标温度过高。

## <a name="remediate-an-issue"></a>解决问题

若要对设备进行更改，请使用“环境传感器属性”  页：

1. 选择“环境传感器属性”。  将“亮度级别”  更改为 10。 选择“保存”  以更新设备。 当设备确认设置更改时，属性的状态将更改为“已同步”  ：

    ![更新设置](media/tutorial-monitor-devices-pnp/change-settings.png)

2. 选择“仪表板”  并验证新的属性值：

    ![“已更新设备”仪表板](media/tutorial-monitor-devices-pnp/new-settings.png)

## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

* 接收通知
* 调查问题
* 解决问题

现在已了解如何监视设备，建议执行的下一步骤是：

> [!div class="nextstepaction"]
> [配置设备的规则和操作](tutorial-configure-rules.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)。