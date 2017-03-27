---
featureFlags:
- usabilla
title: "将 Raspberry Pi（节点）连接到 Azure IoT - 第 4 课：云到设备 | Microsoft Docs"
description: "示例应用程序在 Pi 上运行，用于监视来自 IoT 中心的传入消息。 新的 gulp 任务会从 IoT 中心向 Pi 发送消息，使 LED 闪烁。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "云到设备, 来自云的消息"
ms.assetid: 6ae6539e-1163-4490-8d72-fdf7803e3054
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: 92ee9d6faae9f539c663395e47714609a146f2df
ms.lasthandoff: 01/24/2017


---
# <a name="run-the-sample-application-to-receive-cloud-to-device-messages"></a>运行示例应用程序以接收“云到设备”消息
在本文中，需要在 Raspberry Pi 3 上部署示例应用程序。 示例应用程序监视来自 IoT 中心的传入消息。 此外，还需在计算机上运行一项 gulp 任务来从 IoT 中心向 Pi 发送消息。 示例应用程序在收到这些消息后，就会让 LED 闪烁。 如果有任何问题，请在[故障排除页面](iot-hub-raspberry-pi-kit-node-troubleshooting.md)上查找解决方案。

## <a name="what-you-will-do"></a>执行的操作
* 将示例应用程序连接到 IoT 中心。
* 部署并运行示例应用程序。
* 从 IoT 中心向 Pi 发送消息来使 LED 闪烁。

## <a name="what-you-will-learn"></a>你要学习的知识
在本文中，将学习以下内容：
* 如何监视来自 IoT 中心的传入消息
* 如何从 IoT 中心向 Pi 发送“云到设备”消息。

## <a name="what-you-need"></a>所需条件
* 已完成设置可供使用的 Raspberry Pi 3。 若要了解如何设置 Pi，请参阅[配置设备](iot-hub-raspberry-pi-kit-node-lesson1-configure-your-device.md)。
* 在 Azure 订阅中创建的一个 IoT 中心。 若要了解如何创建 IoT 中心，请参阅[创建 IoT 中心并注册 Raspberry Pi 3](iot-hub-raspberry-pi-kit-node-lesson2-prepare-azure-iot-hub.md)。

## <a name="connect-the-sample-application-to-your-iot-hub"></a>将示例应用程序连接到 IoT 中心
1. 确保位于存储库文件夹 `iot-hub-node-raspberrypi-getting-started` 中。 通过运行以下命令在 Visual Studio Code 中打开示例应用程序：
   
   ```bash
   cd Lesson4
   code .
   ```
   
   请注意 `app` 子文件夹中的 `app.js` 文件。 `app.js` 文件是关键源文件，其中包含的代码用于监视来自 IoT 中心的传入消息。 `blinkLED` 函数可使 LED 闪烁。
   
   ![示例应用程序中的存储库结构](media/iot-hub-raspberry-pi-lessons/lesson4/repo_structure.png)
2. 使用以下命令初始化配置文件：
   
   ```bash
   npm install
   gulp init
   ```
   
   如果你在此计算机上完成了[创建 Azure Function App 和存储帐户](iot-hub-raspberry-pi-kit-node-lesson3-deploy-resource-manager-template.md)中的步骤，继承了所有配置，则可跳到用于部署并运行示例应用程序的任务。 如果你在另一计算机上完成了[创建 Azure Function App 和存储帐户](iot-hub-raspberry-pi-kit-node-lesson3-deploy-resource-manager-template.md)中的步骤，则需替换 `config-raspberrypi.json` 文件中的占位符。 `config-raspberrypi.json` 文件位于主文件夹的子文件夹中。
   
   ![config-raspberrypi.json 文件的内容](media/iot-hub-raspberry-pi-lessons/lesson4/config_raspberrypi.png)

* 将 **[device hostname or IP address]** 替换为通过运行 `devdisco list --eth` 命令获取的 Pi 的 IP 地址或主机名。
* 将 **[IoT device connection string]** 替换为通过运行 `az iot device show-connection-string --hub-name {my hub name} --device-id {device id} -g iot-sample {resource group name}` 命令获取的设备连接字符串。
* 将 **[IoT hub connection string]** 替换为通过运行 `az iot hub show-connection-string --name {my hub name} -g iot-sample {resource group name}` 命令获取的 IoT 中心连接字符串。

> [!NOTE]
> 如果在第 1 课中没有运行 **gulp install-tools**，则还需要运行此命令。

## <a name="deploy-and-run-the-sample-application"></a>部署并运行示例应用程序
通过运行以下命令在 Pi 上部署并运行示例应用程序：

```bash
gulp deploy && gulp run
```

此命令将示例应用程序部署到 Pi。 然后，它会在 Pi 上运行该应用程序，并在主机上运行单独的任务，以便从 IoT 中心将 20 条闪烁消息发送到 Pi。

在示例应用程序运行以后，它会开始侦听 IoT 中心发出的消息。 同时，gulp 任务会将多个“闪烁”消息从 IoT 中心发送到 Pi。 Pi 每收到一条闪烁消息，示例应用程序都会调用 `blinkLED` 函数，使 LED 闪烁。

当 gulp 任务将 20 条消息从 IoT 中心发送到 Pi 时，用户会看到 LED 每隔两秒闪烁一次。 最后一条为“停止”消息，告知应用程序停止运行。

![包含 gulp 命令和闪烁消息的示例应用程序](media/iot-hub-raspberry-pi-lessons/lesson4/gulp_blink.png)

## <a name="summary"></a>摘要
你已成功地将消息从 IoT 中心发送到 Pi，使 LED 闪烁。 下一任务为可选任务：更改 LED 的亮起和熄灭行为。

## <a name="next-steps"></a>后续步骤
[更改 LED 的亮起和熄灭行为](iot-hub-raspberry-pi-kit-node-lesson4-change-led-behavior.md)


