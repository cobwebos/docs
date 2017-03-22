---
title: "将 Arduino (C) 连接到 Azure IoT - 第 3 课：运行示例 | Microsoft Docs"
description: "在 Adafruit Feather M0 WiFi 上部署并运行示例应用程序，以便将消息发送到 IoT 中心并使 LED 闪烁。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "iot 云服务, arduino 向云发送数据"
ms.assetid: 92cce319-2b17-4c9b-889d-deac959e3e7c
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: 631a6677e4801b1c3475b9e8f2726219dd101132
ms.lasthandoff: 01/24/2017


---
# <a name="run-a-sample-application-to-send-device-to-cloud-messages"></a>运行示例应用程序以发送“设备到云”消息
## <a name="what-you-will-do"></a>执行的操作
本文说明如何在 Adafruit Feather M0 WiFi Arduino 开发板上部署和运行示例应用程序，以便将消息发送到 IoT 中心。

如果有任何问题，请在[故障排除页面][troubleshooting]上查找解决方案。

## <a name="what-you-will-learn"></a>你要学习的知识
你将学习如何使用 gulp 工具在 Arduino 开发板上部署并运行示例 Arduino 应用程序。

## <a name="what-you-need"></a>所需条件
* 在开始此任务之前，必须已成功完成了[创建 Azure Function App 和 Azure 存储帐户来处理和存储 IoT 中心消息][process-and-store-iot-hub-messages]。

## <a name="get-your-iot-hub-and-device-connection-strings"></a>获取 IoT 中心连接字符串和设备连接字符串
设备连接字符串用来将 Arduino 开发板连接到 IoT 中心。 IoT 中心连接字符串用来将 IoT 中心连接到 IoT 中心内代表 Arduino 开发板的设备标识。

* 运行以下 Azure CLI 命令，列出资源组中的所有 IoT 中心：

```bash
az iot hub list -g iot-sample --query [].name
```

使用 `iot-sample` 作为 `{resource group name}` 的值（如果尚未更改此值）。

* 通过运行以下 Azure CLI 命令获取 IoT 中心连接字符串：

```bash
az iot hub show-connection-string --name {my hub name}
```

`{my hub name}` 是用户在创建 IoT 中心和注册 Arduino 开发板时指定的名称。

* 通过运行以下命令获取设备连接字符串：

```bash
az iot device show-connection-string --hub-name {my hub name} --device-id mym0wifi
```

使用 `mym0wifi` 作为 `{device id}` 的值（如果尚未更改此值）。
## <a name="configure-the-device-connection"></a>配置设备连接
若要配置设备连接，请执行以下步骤：

1. 使用设备发现 CLI 获取设备的串行端口：

   ```bash
   devdisco list --usb
   ```

   应会看到类似于下面的输出，接下来请查找 Arduino 开发板的 USB COM 端口：

   ![设备发现][device-discovery]

2. 打开课程文件夹中的文件 `config.json`，添加找到的 COM 端口号的值：

   ```json
   {
       "device_port" : "COM1"
   }
   ```

   ![config.json][config-json]

   > [!NOTE]
   > 在 Windows 平台上 COM 端口的格式为 `COM1, COM2, ...`。 在 macOS 或 Ubuntu 上，此端口以 `/dev/` 开头。

3. 通过运行以下命令初始化配置文件：

   ```bash
   npm install
   gulp init
   gulp install-tools
   ```
4. 通过运行以下命令在 Visual Studio Code 中打开设备配置文件 `config-arduino.json`：

   ```bash
   # For Windows command prompt
   code %USERPROFILE%\.iot-hub-getting-started\config-arduino.json

   # For MacOS or Ubuntu
   code ~/.iot-hub-getting-started/config-arduino.json
   ```

   ![config-arduino.json][config-arduino-json]

5. 在 `config-arduino.json` 文件中进行以下替换：

   * 将 **[Wi-fi SSID]** 替换为连接到 Internet 的 Wi-Fi SSID。
   * 将 **[Wi-Fi password]** 替换为 Wi-Fi 密码。 如果你的 Wi-Fi 不需要密码，请删除此字符串。
   * 将 **[IoT device connection string]** 替换为你获取的 `device connection string`。
   * 将 **[IoT hub connection string]** 替换为你获取的 `iot hub connection string`。

   > [!NOTE]
   > 本文中不需要 `azure_storage_connection_string`。 请保留该名称。

## <a name="deploy-and-run-the-sample-application"></a>部署并运行示例应用程序
通过运行以下命令在 Arduino 开发板上部署并运行示例应用程序：

```bash
gulp run
# You can monitor the serial port by running listen task:
gulp listen

# Or you can combine above two gulp tasks into one:
gulp run --listen
```

> [!NOTE]
> 默认 gulp 任务按顺序运行 `install-tools` 和 `run` 任务。 [部署闪烁应用][deployed-the-blink-app]时，是分别运行这些任务的。

## <a name="verify-that-the-sample-application-works"></a>验证示例应用程序是否正常工作
应看到 GPIO #0 开发板 LED 每两秒闪烁一次。 LED 每次闪烁时，示例应用程序都会向 IoT 中心发送一条消息并验证该消息是否已成功发送到 IoT 中心。 此外，还会在控制台窗口中输出 IoT 中心收到的每条消息。 在发送 20 条消息后，示例应用程序将自动终止。

![包含发送和接收的消息的示例应用程序][sample-application-with-sent-and-received-messages]

## <a name="summary"></a>摘要
用户已在 Arduino 开发板上部署和运行新的闪烁示例应用程序，目的是将从设备到云的消息发送到 IoT 中心。 现可在将消息写入存储帐户时对其进行监视。

## <a name="next-steps"></a>后续步骤
[读取 Azure 存储中保存的消息][read-messages-persisted-in-azure-storage]
<!-- Images and links -->

[troubleshooting]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-troubleshooting.md
[process-and-store-iot-hub-messages]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson3-deploy-resource-manager-template.md
[device-discovery]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson1/device_discovery.png
[config-json]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson1/vscode-config-mac.png
[config-arduino-json]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson3/config-arduino.png
[deployed-the-blink-app]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson1-deploy-blink-app.md
[sample-application-with-sent-and-received-messages]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson3/gulp_run_arduino.png
[read-messages-persisted-in-azure-storage]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson3-read-table-storage.md
