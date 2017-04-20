---
title: "将 Intel Edison (C) 连接到 Azure IoT - 第 4 课：接收消息 | Microsoft Docs"
description: "示例应用程序在 Edison 上运行，监视来自 IoT 中心的传入消息。 新的 gulp 任务会将消息从 IoT 中心发送到 Edison，使 LED 闪烁。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "arduino 从 web 控制 led, arduino 通过 web 控制 led"
ROBOTS: NOINDEX
redirect_url: /azure/iot-hub/iot-hub-intel-edison-kit-c-get-started
ms.assetid: 820d38f3-d3b8-4249-9e2b-f1b9b771e62f
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 475b25f02715a60493e79ecd2170854019dfc4ac
ms.openlocfilehash: b4d76eeb1bc0b406800c89c4952900350d8eafa8
ms.lasthandoff: 01/25/2017


---
# <a name="run-a-sample-application-to-receive-cloud-to-device-messages"></a>运行示例应用程序，接收云到设备消息
在本文中，你将在 Intel Edison 上部署示例应用程序。 示例应用程序监视来自 IoT 中心的传入消息。 还会在计算机上运行 gulp 任务，将消息从 IoT 中心发送到 Edison。 示例应用程序在收到这些消息后，就会让 LED 闪烁。 如果遇到任何问题，请在[故障排除页][troubleshooting]上查找解决方法。

## <a name="what-you-will-do"></a>执行的操作
* 将示例应用程序连接到 IoT 中心。
* 部署并运行示例应用程序。
* 将消息从 IoT 中心发送到 Edison，使 LED 闪烁。

## <a name="what-you-will-learn"></a>你要学习的知识
本文介绍：
* 如何监视来自 IoT 中心的传入消息。
* 如何将云到设备消息从 IoT 中心发送到 Edison。

## <a name="what-you-need"></a>需要什么
* Intel Edison，设置以供使用。 若要了解如何设置 Edison，请参阅[配置设备][configure-your-device]。
* 在 Azure 订阅中创建的一个 IoT 中心。 若要了解如何创建 IoT 中心，请参阅[创建 Azure IoT 中心][create-your-azure-iot-hub]。

## <a name="connect-the-sample-application-to-your-iot-hub"></a>将示例应用程序连接到 IoT 中心
1. 确保位于存储库文件夹 `iot-hub-c-edison-getting-started` 中。 通过运行以下命令在 Visual Studio Code 中打开示例应用程序：

   ```bash
   cd Lesson4
   code .
   ```

   `app` 子文件夹中的文件是重要的源文件，其中包含用于监视来自 IoT 中心的传入消息的代码。 `blinkLED` 函数可使 LED 闪烁。

   ![示例应用程序中的存储库结构][repo-structure]
2. 通过运行以下命令初始化配置文件：

   ```bash
   npm install
   gulp init
   ```

   如果已在此计算机上完成[创建 Azure Function App 和存储帐户][create-an-azure-function-app-and-storage-account]中的步骤，并继承了所有配置，则可跳过该步骤，转到部署并运行示例应用程序的任务。 如果在另一计算机上完成了[创建 Azure Function App 和存储帐户][create-an-azure-function-app-and-storage-account]中的步骤，需替换 `config-edison.json` 文件中的占位符。 `config-edison.json` 文件位于主文件夹的子文件夹中。

   ![config-edison.json 文件的内容](media/iot-hub-intel-edison-lessons/lesson4/config-edison.png)

   * 将 **[设备主机名或 IP 地址]** 替换为配置设备时标记的设备 IP 地址。
   * 将 **[IoT device connection string]** 替换为通过运行 `az iot device show-connection-string --hub-name {my hub name} --device-id {device id}` 命令获取的设备连接字符串。
   * 将 **[IoT hub connection string]** 替换为通过运行 `az iot hub show-connection-string --name {my hub name}` 命令获取的 IoT 中心连接字符串。

   > [!NOTE]
   > 如果在第 1 课中没有运行 **gulp install-tools**，则还需要运行此命令。

## <a name="deploy-and-run-the-sample-application"></a>部署并运行示例应用程序
运行以下命令，在 Edison 上部署并运行示例应用程序：

```bash
gulp deploy && gulp run
```

gulp 命令会将示例应用程序部署到 Edison。 然后，它会在 Edison 上运行该应用程序，并在主计算机上运行单独的任务，从 IoT 中心将 20 条闪烁消息发送到 Edison。

在示例应用程序运行以后，它会开始侦听 IoT 中心发出的消息。 同时，gulp 任务会将多条“闪烁”消息从 IoT 中心发送到 Edison。 Edison 每收到一条闪烁消息，示例应用程序就会调用一次 `blinkLED` 函数，使 LED 闪烁。

gulp 任务将 20 条消息从 IoT 中心发送到 Edison 时，应看到 LED 每隔两秒闪烁一次。 最后一条为“停止”消息，会停止示例应用程序的运行。

![包含 gulp 命令和闪烁消息的示例应用程序][gulp-command-and-blink-messages]

## <a name="summary"></a>摘要
已成功将消息从 IoT 中心发送到 Edison，使 LED 闪烁。 下一任务为可选任务：更改 LED 的开关行为。

## <a name="next-steps"></a>后续步骤
[更改 LED 的亮起和熄灭行为][change-the-on-and-off-behavior-of-the-led]

<!-- Images and links -->

[troubleshooting]: iot-hub-intel-edison-kit-c-troubleshooting.md
[configure-your-device]: iot-hub-intel-edison-kit-c-lesson1-configure-your-device.md
[create-your-azure-iot-hub]: iot-hub-intel-edison-kit-c-lesson2-prepare-azure-iot-hub.md
[repo-structure]: media/iot-hub-intel-edison-lessons/lesson4/repo_structure_c.png
[create-an-azure-function-app-and-storage-account]: iot-hub-intel-edison-kit-c-lesson3-deploy-resource-manager-template.md
[gulp-command-and-blink-messages]: media/iot-hub-intel-edison-lessons/lesson4/gulp_blink_c.png
[change-the-on-and-off-behavior-of-the-led]: iot-hub-intel-edison-kit-c-lesson4-change-led-behavior.md
