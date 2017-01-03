---
title: "运行示例应用程序以发送“设备到云”消息 | Microsoft 文档"
description: "在 Raspberry Pi 3 上部署并运行一个示例应用程序，用以向 IoT 中心发送消息并使 LED 闪烁。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "闪烁 led 云 pi, 通过云使 led 闪烁"
ms.assetid: 427d8e5e-8af8-4249-8607-44edc90a4972
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: ffcb9214b8fa645a8a2378c5e7054b9f984addbb
ms.openlocfilehash: 0c60200f87bf2c1df0a32b1887b1f9412ba69b39


---
# <a name="run-a-sample-application-to-send-device-to-cloud-messages"></a>运行示例应用程序以发送“设备到云”消息
## <a name="what-you-will-do"></a>执行的操作
本文将说明如何在 Raspberry Pi 3 上部署并运行一个示例应用程序，用以向 IoT 中心发送消息。 如果有任何问题，请在[故障排除页面](iot-hub-raspberry-pi-kit-node-troubleshooting.md)上查找解决方案。

## <a name="what-you-will-learn"></a>你要学习的知识
你将学习如何使用 gulp 工具在 Pi 上部署并运行示例 Node.js 应用程序。

## <a name="what-you-need"></a>所需条件
在开始此任务之前，必须已成功完成了[创建 Azure Function App 和 Azure 存储帐户来处理和存储 IoT 中心消息](iot-hub-raspberry-pi-kit-node-lesson3-deploy-resource-manager-template.md)。

## <a name="get-your-iot-hub-and-device-connection-strings"></a>获取 IoT 中心连接字符串和设备连接字符串
设备连接字符串用来将 Pi 连接到 IoT 中心。 IoT 中心连接字符串用来将 IoT 中心连接到 IoT 中心内代表 Pi 的设备标识。

* 通过运行以下 Azure CLI 命令获取 IoT 中心连接字符串：

```bash
az iot hub show-connection-string --name {my hub name} --resource-group iot-sample
```

`{my hub name}` 是你在创建 IoT 中心并注册 Pi 时指定的名称。 使用 `iot-sample` 作为 `{resource group name}` 的值（如果未更改该值）。

* 通过运行以下命令获取设备连接字符串：

```bash
az iot device show-connection-string --hub {my hub name} --device-id myraspberrypi --resource-group iot-sample
```

`{my hub name}` 采用与前一命令所用值相同的值。 使用 `iot-sample` 作为 `{resource group name}` 的值，使用 `myraspberrypi` 作为 `{device id}` 的值（如果未更改该值）。

## <a name="configure-the-device-connection"></a>配置设备连接
1. 通过运行以下命令初始化配置文件：
   
    ```bash
    npm install
    gulp init
    ```
2. 通过运行以下命令在 Visual Studio Code 中打开设备配置文件 `config-raspberrypi.json`：
   
    ```bash
    # For Windows command prompt
    code %USERPROFILE%\.iot-hub-getting-started\config-raspberrypi.json
   
    # For macOS or Ubuntu
    code ~/.iot-hub-getting-started/config-raspberrypi.json
    ```
   
    ![config.json](media/iot-hub-raspberry-pi-lessons/lesson3/config.png)
3. 在 `config-raspberrypi.json` 文件中进行以下替换：
   
   * 将 **[device hostname or IP address]** 替换为你通过 `device-discovery-cli` 获取的设备 IP 地址或主机名，或者替换为你在配置设备时继承的值。
   * 将 **[IoT device connection string]** 替换为你获取的 `device connection string`。
   * 将 **[IoT hub connection string]** 替换为你获取的 `iot hub connection string`。

更新 `config-raspberrypi.json` 文件，以便你可以从计算机部署示例应用程序。

## <a name="deploy-and-run-the-sample-application"></a>部署并运行示例应用程序
通过运行以下命令在 Pi 上部署并运行示例应用程序：

```bash
gulp deploy && gulp run
```

## <a name="verify-that-the-sample-application-works"></a>验证示例应用程序是否正常工作
你应当会看到连接到 Pi 的 LED 每两秒闪烁一次。 LED 每次闪烁时，示例应用程序都会向 IoT 中心发送一条消息并验证该消息是否已成功发送到 IoT 中心。 此外，还会在控制台窗口中输出 IoT 中心收到的每条消息。 在发送 20 条消息后，示例应用程序将自动终止。

![包含发送和接收的消息的示例应用程序](media/iot-hub-raspberry-pi-lessons/lesson3/gulp_run.png)

## <a name="summary"></a>摘要
你已在 Pi 上部署并运行了新的 blink 示例应用程序来向 IoT 中心发送“设备到云”消息。 现在，你可以在消息写入到存储帐户时监视这些消息。

## <a name="next-steps"></a>后续步骤
[读取 Azure 存储中保存的消息](iot-hub-raspberry-pi-kit-node-lesson3-read-table-storage.md)




<!--HONumber=Nov16_HO5-->


