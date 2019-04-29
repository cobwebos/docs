---
title: 连接到云的模拟 Raspberry Pi (Node.js) - 将 Raspberry Pi Web 模拟器连接到 Azure IoT 中心 | Microsoft Docs
description: 将 Raspberry Pi Web 模拟器连接到 Azure IoT 中心，以供 Raspberry Pi 将数据发送到 Azure 云。
author: wesmc7777
manager: philmea
keywords: Raspberry Pi 模拟器, Azure IoT Raspberry Pi, Raspberry Pi IoT 中心, Raspberry Pi 将数据发送到云, 连接到云的 Raspberry Pi
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 04/11/2018
ms.author: wesmc
ms.openlocfilehash: 42c2c0d1a015baf4b846c86ed22e8383e21028b6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61442260"
---
# <a name="connect-raspberry-pi-online-simulator-to-azure-iot-hub-nodejs"></a>将 Raspberry Pi 联机模拟器连接到 Azure IoT 中心 (Node.js)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

本教程会先介绍使用 Raspberry Pi 联机模拟器的基础知识。 然后介绍如何使用 [Azure IoT 中心](about-iot-hub.md)将 Pi 模拟器无缝连接到云。

如果有物理设备，请访问[将 Raspberry Pi 连接到 Azure IoT 中心](iot-hub-raspberry-pi-kit-node-get-started.md)以开始进行操作。

<p>
<div id="diag" style="width:100%; text-align:center">
<a href="https://azure-samples.github.io/raspberry-pi-web-simulator/#getstarted" target="_blank">
<img src="media/iot-hub-raspberry-pi-web-simulator/3-banner.png" alt="Connect Raspberry Pi web simulator to Azure IoT Hub" width="400">
</div>
<p>
<div id="button" style="width:100%; text-align:center">
<a href="https://azure-samples.github.io/raspberry-pi-web-simulator/#Getstarted" target="_blank">
<img src="media/iot-hub-raspberry-pi-web-simulator/6-button-default.png" alt="Start Raspberry Pi simulator" width="400" onmouseover="this.src='media/iot-hub-raspberry-pi-web-simulator/5-button-click.png';" onmouseout="this.src='media/iot-hub-raspberry-pi-web-simulator/6-button-default.png';">
</div>

## <a name="what-you-do"></a>准备工作

* 了解 Raspberry Pi 联机模拟器的基础知识。

* 创建 IoT 中心。

* 在 IoT 中心内为 Pi 注册设备。

* 在 Pi 上运行示例应用程序，将模拟传感器数据发送到 IoT 中心。

将模拟 Raspberry Pi 连接到创建的 IoT 中心。 然后，使用模拟器运行示例应用程序，生成传感器数据。 最后，将传感器数据发送到 IoT 中心。

## <a name="what-you-learn"></a>学习内容

* 如何创建 Azure IoT 中心以及如何获取新的设备连接字符串。 如果没有 Azure 帐户，只需几分钟时间就能[创建一个免费的 Azure 试用帐户](https://azure.microsoft.com/free/)。

* 如何使用 Raspberry Pi 联机模拟器。

* 如何将传感器数据发送到 IoT 中心。

## <a name="overview-of-raspberry-pi-web-simulator"></a>Raspberry Pi Web 模拟器概述

单击用于启动 Raspberry Pi 联机模拟器的按钮。

> [!div class="button"]
> <a href="https://azure-samples.github.io/raspberry-pi-web-simulator/#GetStarted" target="_blank">启动 Raspberry Pi 模拟器</a>

Web 模拟器中有三个区域。

1. 程序集区域 - 默认电路是 Pi 连接到 BME280 传感器和 LED。 该区域在预览版本中为锁定状态，因此目前无法执行自定义操作。

2. 编码区域 - 一个联机代码编辑器，可在其中使用 Raspberry Pi 进行编码。 默认示例应用程序可帮助从 BME280 传感器收集传感器数据，并将其发送到 Azure IoT 中心。 该应用程序与真实 Pi 设备完全兼容。 

3. 集成控制台窗口 - 显示代码输出。 在此窗口的顶部，有三个按钮。

   * 运行 - 在编码区域运行应用程序。

   * 重置 - 将编码区域重置为默认示例应用程序。

   * 折叠/展开 - 可使用右侧的按钮折叠/展开控制台窗口。

> [!NOTE]
> 目前 Raspberry Pi Web 模拟器在预览版中可用。 请通过 [Gitter Chatroom](https://gitter.im/Microsoft/raspberry-pi-web-simulator) 向我们提供意见和建议。 源代码在 [GitHub](https://github.com/Azure-Samples/raspberry-pi-web-simulator) 上是公开的。

![Pi 联机模拟器概述](media/iot-hub-raspberry-pi-web-simulator/0-overview.png)

## <a name="create-an-iot-hub"></a>创建 IoT 中心

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="retrieve-connection-string-for-iot-hub"></a>检索 IoT 中心的连接字符串

[!INCLUDE [iot-hub-include-find-connection-string](../../includes/iot-hub-include-find-connection-string.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>在 IoT 中心内注册新设备

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="run-a-sample-application-on-pi-web-simulator"></a>在 Pi Web 模拟器上运行示例应用程序

1. 在编码区域，请确保使用的是默认示例应用程序。 将第 15 行的占位符替换为 Azure IoT 中心设备连接字符串。
1. 
   ![设备连接字符串替换为](media/iot-hub-raspberry-pi-web-simulator/1-connectionstring.png)

2. 选择**运行**或类型`npm start`运行该应用程序。

应看到以下输出，该输出显示传感器数据和发送到 IoT 中心的消息![输出 - 从 Raspberry Pi 发送到 IoT 中心的传感器数据](media/iot-hub-raspberry-pi-web-simulator/2-run-application.png)

## <a name="read-the-messages-received-by-your-hub"></a>读取中心收到的消息

若要监视从模拟设备在 IoT 中心接收的消息的一种方法是使用用于 Visual Studio Code 的 Azure IoT 工具。 若要了解详细信息，请参阅[使用针对 Visual Studio Code 进行发送和接收消息在设备与 IoT 中心之间的 Azure IoT 工具](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md)。

有关更多方法来处理设备发送的数据，继续学习下一节。

## <a name="next-steps"></a>后续步骤

此时已运行示例应用程序，收集传感器数据并将其发送到 IoT 中心。

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
