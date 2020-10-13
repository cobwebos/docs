---
title: 使用 Azure MQTT 客户端库向 MQTT 服务器发送消息
description: 了解如何使用 MQTT 客户端库将消息发送到 MQTT broker。 还将了解如何将 mXChip IoT DevKit 配置为 MQTT 客户端。
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/02/2018
ms.author: liydu
ms.custom: mqtt
ms.openlocfilehash: 09ea3ceb15c9b175b8366bf3ac57a61e468ab72a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91356020"
---
# <a name="send-messages-to-an-mqtt-server"></a>向 MQTT 服务器发送消息

物联网 (IoT) 系统经常要处理断断续续的、质量不佳的或低速的 Internet 连接。 MQTT 是一种机器到机器 (M2M) 连接协议，它是为了应对此类难题而开发的。 

此处使用的 MQTT 客户端库是 [Eclipse Paho](https://www.eclipse.org/paho/) 项目的一部分，该项目提供了用于通过多种传输方式使用 MQTT 的 API。

## <a name="what-you-learn"></a>学习内容

此项目介绍：
- 如何使用 MQTT 客户端库向 MQTT 代理发送消息。
- 如何将 MXChip Iot DevKit 配置为 MQTT 客户端。

## <a name="what-you-need"></a>需要什么

完成[入门指南](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started)来实现以下目的：

* 将 DevKit 连接到 Wi-Fi
* 准备开发环境

## <a name="open-the-project-folder"></a>打开项目文件夹

1. 如果 DevKit 已连接到计算机，请断开连接。

2. 启动 VS Code。

3. 将 DevKit 连接到计算机。

## <a name="open-the-mqttclient-sample"></a>打开 MQTTClient 示例

展开左侧的“ARDUINO 示例”部分，浏览到“MXCHIP AZ3166 的示例 > MQTT”，然后选择“MQTTClient”。 此时会打开一个新的 VS Code 窗口，其中包含一个项目文件夹。

> [!NOTE]
> 也可以从命令面板打开示例。 使用 `Ctrl+Shift+P`（macOS: `Cmd+Shift+P`）打开命令面板，键入“Arduino”，然后找到并选择“Arduino: Examples”。

## <a name="build-and-upload-the-arduino-sketch-to-the-devkit"></a>生成 Arduino 草图并将其上传到 DevKit

键入 `Ctrl+P`（macOS：`Cmd+P`）以运行 `task device-upload`。 上传完成后，DevKit 将重启并运行草图。

![屏幕截图显示了一个命令提示符窗口，该窗口将上传和运行 Arduino 草绘。](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/device-upload.jpg)

> [!NOTE]
> 可能会收到“错误:AZ3166:未知程序包”错误消息。 如果未正确刷新板包索引，则会出现此错误。 若要解决此错误，请参阅 [IoT DevKit 常见问题解答的开发部分](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development)。

## <a name="test-the-project"></a>测试项目

在 VS Code 中，按照以下过程打开并设置串行监视器：

1. 单击 `COM[X]` 状态栏上的单词，以将正确的 COM 端口设置为 `STMicroelectronics` ： ![ 屏幕截图显示已选择 COM8 S T 微电子设备 Visual Studio Code。](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/set-com-port.jpg)

2. 单击状态栏上的电源插头图标，打开串行监视器： ![ 屏幕截图显示状态栏中的 "发行摘要" 和电源插头图标。](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/serial-monitor.jpg)
  
3. 在状态栏上，单击代表波特率的数字，并将其设置为 `115200` ： ![ 屏幕截图显示 Visual Studio Code 中设置波特率。](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/set-baud-rate.jpg)

串行监视器显示由示例草图发送的所有消息。 该草图将 DevKit 连接到 Wi-Fi。 在 Wi-Fi 连接成功后，该草图会向 MQTT 代理发送一条消息。 之后，示例分别使用 QoS 0 和 QoS 1 重复发送两条“iot.eclipse.org”消息。

![屏幕截图显示了显示草图发送的消息的串行监视器。](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/serial-output.jpg)

## <a name="problems-and-feedback"></a>问题和反馈

如果遇到问题，请参阅 [IoT DevKit 常见问题解答](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/)或通过以下渠道进行联系：

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="see-also"></a>另请参阅

* [将 IoT DevKit AZ3166 连接到云中的 Azure IoT 中心](iot-hub-arduino-iot-devkit-az3166-get-started.md)
* [摇一摇，摇一摇搜推文](iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message.md)

## <a name="next-steps"></a>后续步骤

现在，你已了解如何将 MXChip Iot DevKit 配置为 MQTT 客户端，并使用 MQTT 客户端库将消息发送到 MQTT broker，以下是建议的下一步： [Azure Iot 远程监视解决方案加速器概述](https://docs.microsoft.com/azure/iot-suite/)
