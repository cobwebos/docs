---
title: "连接到云的模拟 Raspberry Pi (Node.js) - 将 Raspberry Pi Web 模拟器连接到 Azure IoT 中心 | Microsoft Docs"
description: "了解如何设置模拟 Raspberry Pi 并将其连接到 Azure IoT 中心，使其能够将数据发送到 Azure 云平台。 在本教程中，不需要使用物理板。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "Raspberry Pi 模拟器, Azure IoT Raspberry Pi, Raspberry Pi IoT 中心, Raspberry Pi 将数据发送到云, 连接到云的 Raspberry Pi"
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/7/2017
ms.author: xshi
ms.translationtype: HT
ms.sourcegitcommit: 49bc337dac9d3372da188afc3fa7dff8e907c905
ms.openlocfilehash: b76c64b677a1a57a032cb71d2922778e7cf700c8
ms.contentlocale: zh-cn
ms.lasthandoff: 07/14/2017

---

# <a name="connect-raspberry-pi-online-simulator-to-azure-iot-hub-nodejs"></a>将 Raspberry Pi 联机模拟器连接到 Azure IoT 中心 (Node.js)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

本教程会先介绍使用 Raspberry Pi 联机模拟器的基础知识。 然后介绍如何使用 [Azure IoT 中心](iot-hub-what-is-iot-hub.md)将 Pi 模拟器无缝连接到云。 

如果有物理设备，请访问[将 Raspberry Pi 连接到 Azure IoT 中心](iot-hub-raspberry-pi-kit-node-get-started.md)以开始进行操作。 

## <a name="what-you-do"></a>准备工作

* 了解 Raspberry Pi 联机模拟器的基础知识。
* 创建 IoT 中心。
* 在 IoT 中心内为 Pi 注册设备。
* 在 Pi 上运行示例应用程序，将模拟传感器数据发送到 IoT 中心。

将模拟 Raspberry Pi 连接到创建的 IoT 中心。 然后，使用模拟器运行示例应用程序，生成传感器数据。 最后，将传感器数据发送到 IoT 中心。

## <a name="what-you-learn"></a>学习内容

* 如何创建 Azure IoT 中心以及如何获取新的设备连接字符串。 若没有 Azure 帐户，只需几分钟即可[创建免费的 Azure 试用帐户](https://azure.microsoft.com/free/)。
* 如何使用 Raspberry Pi 联机模拟器。
* 如何将传感器数据发送到 IoT 中心。

## <a name="overview-of-raspberry-pi-web-simulator"></a>Raspberry Pi Web 模拟器概述

单击用于启动 Raspberry Pi 联机模拟器的按钮。

> [!div class="button"]
[启动 Raspberry Pi 模拟器](https://azure-samples.github.io/raspberry-pi-web-simulator/)

Web 模拟器中有三个区域。
* 程序集区域 - 默认电路是 Pi 连接到 BME280 传感器和 LED。 该区域在预览版本中为锁定状态，因此目前无法执行自定义操作。
* 编码区域 - 一个联机代码编辑器，可在其中使用 Raspberry Pi 进行编码。 默认示例应用程序可帮助从 BME280 传感器收集传感器数据，并将其发送到 Azure IoT 中心。 该应用程序与真实 Pi 设备完全兼容。 
* 集成控制台窗口 - 显示代码输出。 在此窗口的顶部，有三个按钮。
   * 运行 - 在编码区域运行应用程序。
   * 重置 - 将编码区域重置为默认示例应用程序。
   * 折叠/展开 - 可使用右侧的按钮折叠/展开控制台窗口。

> [!NOTE] 
目前 Raspberry Pi Web 模拟器在预览版中可用。 请通过 [Gitter Chatroom](https://gitter.im/Microsoft/raspberry-pi-web-simulator) 向我们提供意见和建议。 源代码在 [Github](https://github.com/Azure-Samples/raspberry-pi-web-simulator) 上是公开的。

![Pi 联机模拟器概述](media/iot-hub-raspberry-pi-web-simulator/0_overview.png)

[!INCLUDE [iot-hub-get-started-create-hub-and-device](../../includes/iot-hub-get-started-create-hub-and-device.md)]


## <a name="run-a-sample-application-on-pi-web-simulator"></a>在 Pi Web 模拟器上运行示例应用程序

1. 在编码区域，请确保使用的是默认示例应用程序。 将第 15 行的占位符替换为 Azure IoT 中心设备连接字符串。
   ![替换设备连接字符串](media/iot-hub-raspberry-pi-web-simulator/1_connectionstring.png)

2. 单击“运行”或键入 `npm start` 以运行应用程序。


应看到以下输出，该输出显示传感器数据和发送到 IoT 中心的消息![输出 - 从 Raspberry Pi 发送到 IoT 中心的传感器数据](media/iot-hub-raspberry-pi-web-simulator/2_run_application.png)


## <a name="next-steps"></a>后续步骤

此时已运行示例应用程序，以收集传感器数据并将其发送到 IoT 中心。

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]

