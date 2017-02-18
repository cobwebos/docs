---
title: "将 Arduino (C) 连接到 Azure IoT - 第 3 课：表存储 | Microsoft Docs"
description: "在“设备到云”消息写入到 Azure 表存储时对这些消息进行监视。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "云中的数据, 云数据收集, iot 云服务, iot 数据"
ms.assetid: 386083e0-0dbb-48c0-9ac2-4f8fb4590772
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/13/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: 7a49a15b00bf25c9416235f41da8cc86e54d0062


---
# <a name="read-messages-persisted-in-azure-storage"></a>读取 Azure 存储中保存的消息
## <a name="what-you-will-do"></a>执行的操作
对于从 Adafruit Feather M0 WiFi Arduino 开发板发送到 IoT 中心的设备到云消息，在将其写入 Azure 表存储时对其进行监视。

如果有任何问题，请在[故障排除页面][troubleshooting]上查找解决方案。

## <a name="what-you-will-learn"></a>你要学习的知识
在本文中，你将学习如何使用 gulp 读取消息任务来读取 Azure 表存储中保存的消息。

## <a name="what-you-need"></a>所需条件
在开始此过程之前，必须已成功完成了[在 Arduino 开发板上运行 Azure 闪烁示例应用程序][run-blink-application]操作。

## <a name="read-new-messages-from-your-storage-account"></a>从存储帐户读取新消息
在前面的文章中，你在 Arduino 开发板上运行了一个示例应用程序。 该示例应用程序向 Azure IoT 中心发送了消息。 发送到 IoT 中心的消息通过 Azure Function App 存储到 Azure 表存储中。 若要从 Azure 表存储中读取消息，需要使用 Azure 表存储连接字符串。

若要读取 Azure 表存储中存储的消息，请执行以下步骤：

1. 通过运行以下命令获取连接字符串：

   ```bash
   az storage account list -g iot-sample --query [].name
   az storage account show-connection-string -g iot-sample -n {storage name}
   ```

   第一个命令检索第二个命令中使用的 `storage name` 来获取连接字符串。 使用 `iot-sample` 作为 `{resource group name}` 的值（如果未更改该值）。
2. 通过运行以下命令在 Visual Studio Code 中打开配置文件 `config-arduino.json`：

   ```bash
   # For Windows command prompt
   code %USERPROFILE%\.iot-hub-getting-started\config-arduino.json

   # For MacOS or Ubuntu
   code ~/.iot-hub-getting-started/config-arduino.json
   ```
3. 将 `[Azure storage connection string]` 替换为在步骤 1 中获取的连接字符串。
4. 保存 `config-arduino.json` 文件。
5. 再次发送消息并通过运行以下命令从 Azure 表存储中读取消息：

   ```bash
   gulp run --read-storage

   # You can monitor the serial port by running listen task:
   gulp listen

   # Or you can combine above two gulp tasks into one:
   gulp run --read-storage --listen
   ```

   用于从 Azure 表存储中进行读取的逻辑位于 `azure-table.js` 文件中。

   ![gulp run --read-storage][gulp-run]

## <a name="summary"></a>摘要
你已成功将 Arduino 开发板连接到云中的 IoT 中心，并使用闪烁示例应用程序发送了“设备到云”消息。 你还使用了 Azure Function App 将传入的 IoT 中心消息存储到 Azure 表存储。 现在可以从 IoT 中心向 Arduino 开发板发送“云到设备”消息了。

## <a name="next-steps"></a>后续步骤
[发送云到设备的消息][send-cloud-to-device-messages]
<!-- Images and links -->

[troubleshooting]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-troubleshooting.md
[run-blink-application]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson3-run-azure-blink.md
[gulp-run]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson3/gulp_read_message_arduino.png
[send-cloud-to-device-messages]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson4-send-cloud-to-device-messages.md


<!--HONumber=Jan17_HO4-->


