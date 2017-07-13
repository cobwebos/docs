---
title: "将 Intel Edison (C) 连接到 Azure IoT - 第 3 课：监视消息 | Microsoft Docs"
description: "在“设备到云”消息写入到 Azure 表存储时对这些消息进行监视。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "云中的数据, 云数据收集, iot 云服务, iot 数据"
ROBOTS: NOINDEX
redirect_url: /azure/iot-hub/iot-hub-intel-edison-kit-c-get-started
ms.assetid: cad545c3-dd88-486c-a663-d587a924ccd4
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
ms.translationtype: Human Translation
ms.sourcegitcommit: 475b25f02715a60493e79ecd2170854019dfc4ac
ms.openlocfilehash: 1be4de1fef11da43c2940fdd890027d0e9728515
ms.contentlocale: zh-cn
ms.lasthandoff: 01/25/2017

---
<a id="read-messages-persisted-in-azure-storage" class="xliff"></a>

# 读取 Azure 存储中保存的消息
<a id="what-you-will-do" class="xliff"></a>

## 执行的操作
对于从 Intel Edison 发送到 IoT 中心的设备到云消息，在将其写入 Azure 表存储时对其进行监视。 如果有任何问题，请在[故障排除页面][troubleshooting]上查找解决方案。

<a id="what-you-will-learn" class="xliff"></a>

## 你要学习的知识
在本文中，你将学习如何使用 gulp 读取消息任务来读取 Azure 表存储中保存的消息。

<a id="what-you-need" class="xliff"></a>

## 所需条件
在开始此过程之前，必须已成功完成[在 Intel Edison 上运行 Azure 闪烁示例应用程序][run-the-azure-blink-sample-application-on-intel-edison]这一操作。

<a id="read-new-messages-from-your-storage-account" class="xliff"></a>

## 从存储帐户读取新消息
在以前的文章中，已在 Edison 上运行示例应用程序。 示例应用程序发送消息到 Azure IoT 中心。 发送到 IoT 中心的消息通过 Azure Function App 存储到 Azure 表存储中。 若要从 Azure 表存储中读取消息，需要使用 Azure 表存储连接字符串。

若要读取 Azure 表存储中存储的消息，请执行以下步骤：

1. 通过运行以下命令获取连接字符串：

   ```bash
   az storage account list -g iot-sample --query [].name
   az storage account show-connection-string -g iot-sample -n {storage name}
   ```

   第一个命令检索第二个命令中使用的 `storage name` 来获取连接字符串。 使用 `iot-sample` 作为 `{resource group name}` 的值（如果未更改该值）。
2. 通过运行以下命令在 Visual Studio Code 中打开配置文件 `config-edison.json`：

   ```bash
   # For Windows command prompt
   code %USERPROFILE%\.iot-hub-getting-started\config-edison.json

   # For MacOS or Ubuntu
   code ~/.iot-hub-getting-started/config-edison.json
   ```
3. 将 `[Azure storage connection string]` 替换为在步骤 1 中获取的连接字符串。
4. 保存 `config-edison.json` 文件。
5. 再次发送消息并通过运行以下命令从 Azure 表存储中读取消息：

   ```bash
   gulp run --read-storage
   ```

   用于从 Azure 表存储中进行读取的逻辑位于 `azure-table.js` 文件中。

   ![gulp run --read-storage][gulp run]

<a id="summary" class="xliff"></a>

## 摘要
已成功将 Edison 连接到云中的 IoT 中心，并已使用 blink 示例应用程序发送设备到云消息。 用户还使用 Azure 函数应用将传入的 IoT 中心消息存储到 Azure 表存储中。 现在可从 IoT 中心将云到设备消息发送到 Edison。

<a id="next-steps" class="xliff"></a>

## 后续步骤
[运行示例应用程序，接收云到设备消息][receive-cloud-to-device-messages]
<!-- Images and links -->

[troubleshooting]: iot-hub-intel-edison-kit-c-troubleshooting.md
[run-the-azure-blink-sample-application-on-intel-edison]: iot-hub-intel-edison-kit-c-lesson3-run-azure-blink.md
[gulp run]: media/iot-hub-intel-edison-lessons/lesson3/gulp_read_message_c.png
[receive-cloud-to-device-messages]: iot-hub-intel-edison-kit-c-lesson4-send-cloud-to-device-messages.md
