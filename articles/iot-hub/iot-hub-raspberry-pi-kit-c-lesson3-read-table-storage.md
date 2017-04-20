---
title: "将 Raspberry Pi (C) 连接到 Azure IoT - 第 3 课：表存储 | Microsoft Docs"
description: "在“设备到云”消息写入到 Azure 表存储时对这些消息进行监视。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "从云检索数据, iot 云服务"
ROBOTS: NOINDEX
redirect_url: /azure/iot-hub/iot-hub-raspberry-pi-kit-c-get-started
ms.assetid: 8c5558bb-3c31-4445-90e6-b1a978738545
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: 41e89f524bf9ae67489ab3a0eb09ceda6b211f15
ms.lasthandoff: 01/24/2017


---
# <a name="read-messages-persisted-in-azure-storage"></a>读取 Azure 存储中保存的消息
## <a name="what-you-will-do"></a>执行的操作
在从 Raspberry Pi 3 发送到 IoT 中心的“设备到云”消息写入到 Azure 表存储时对这些消息进行监视。 如果有任何问题，请在[故障排除页面](iot-hub-raspberry-pi-kit-c-troubleshooting.md)上查找解决方案。

## <a name="what-you-will-learn"></a>你要学习的知识
在本文中，你将学习如何使用 gulp 读取消息任务来读取 Azure 表存储中保存的消息。

## <a name="what-you-need"></a>所需条件
在开始此过程之前，必须已成功完成了[在 Raspberry Pi 3 上运行 Azure blink 示例应用程序](iot-hub-raspberry-pi-kit-c-lesson3-run-azure-blink.md)。

## <a name="read-new-messages-from-your-storage-account"></a>从存储帐户读取新消息
在前面的文章中，你在 Pi 上运行了一个示例应用程序。 该示例应用程序向 Azure IoT 中心发送了消息。 发送到 IoT 中心的消息通过 Azure Function App 存储到 Azure 表存储中。 若要从 Azure 表存储中读取消息，需要使用 Azure 表存储连接字符串。

若要读取 Azure 表存储中存储的消息，请执行以下步骤：

1. 通过运行以下命令获取连接字符串：

   ```bash
   az storage account list -g iot-sample --query [].name
   az storage account show-connection-string -g iot-sample -n {storage name}
   ```

   第一个命令检索第二个命令中使用的 `storage name` 来获取连接字符串。 使用 `iot-sample` 作为 `{resource group name}` 的值（如果未更改该值）。
2. 通过运行以下命令在 Visual Studio Code 中打开配置文件 `config-raspberrypi.json`：

   ```bash
   # For Windows command prompt
   code %USERPROFILE%\.iot-hub-getting-started\config-raspberrypi.json
   
   # For MacOS or Ubuntu
   code ~/.iot-hub-getting-started/config-raspberrypi.json
   ```
3. 将 `[Azure storage connection string]` 替换为在步骤 1 中获取的连接字符串。
4. 保存 `config-raspberrypi.json` 文件。
5. 再次发送消息并通过运行以下命令从 Azure 表存储中读取消息：
   
   ```bash
   gulp run --read-storage
   ```
   
   用于从 Azure 表存储中进行读取的逻辑位于 `azure-table.js` 文件中。
   
   ![gulp run --read-storage](media/iot-hub-raspberry-pi-lessons/lesson3/gulp_read_message_c.png)

## <a name="summary"></a>摘要
你已成功将 Pi 连接到云中的 IoT 中心，并使用 blink 示例应用程序发送了“设备到云”消息。 你还使用了 Azure Function App 将传入的 IoT 中心消息存储到 Azure 表存储。 现在可以从 IoT 中心向 Pi 发送“云到设备”消息了。

## <a name="next-steps"></a>后续步骤
[运行示例应用程序，接收云到设备消息](iot-hub-raspberry-pi-kit-c-lesson4-send-cloud-to-device-messages.md)


