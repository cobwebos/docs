---
title: "使用 iothub-explorer 管理 Azure IoT 中心云设备消息传送 | Microsoft Docs"
description: "了解如何在 Azure IoT 中心内使用 iothub-explorer CLI 工具监视设备到云 (D2C) 的消息以及发送到云到设备 (C2D) 的消息。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "iothub explorer, 云设备消息传送, iot 中心云到设备, 云到设备的消息"
ms.assetid: 04521658-35d3-4503-ae48-51d6ad3c62cc
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: 8c25b8f558aae638a95d6e7186e19e77d02edbb2
ms.lasthandoff: 03/30/2017


---
# <a name="use-iothub-explorer-to-send-and-receive-messages-between-your-device-and-iot-hub"></a>使用 iothub-explorer 在设备与 IoT 中心之间发送和接收消息

> [!NOTE]
> 在开始本教程之前，请确保已完成[将 ESP8266 连接到 Azure IoT 中心](/iot-hub-arduino-huzzah-esp8266-get-started.md)。 在[将 ESP8266 连接到 Azure IoT 中心](/iot-hub-arduino-huzzah-esp8266-get-started.md)教程中，你已设置 IoT 设备和 IoT 中心，并部署了一个在设备上运行的示例应用程序。 该应用程序会将收集的传感器数据发送到 IoT 中心。

[iothub-explorer](https://github.com/azure/iothub-explorer) 提供一些命令用于简化 IoT 中心的管理。 本教程重点介绍如何使用 iothub-explorer 在设备与 IoT 中心之间发送和接收消息。

## <a name="what-you-will-learn"></a>你要学习的知识

了解如何使用 iothub-explorer 监视设备到云的消息以及发送云到设备的消息。 设备到云的消息可能是设备收集的，随后要发送到 IoT 中心的传感器数据。 云到设备的消息可能是 IoT 中心发送到设备的，用于闪烁连接到设备的 LED 的命令。

## <a name="what-you-will-do"></a>执行的操作

- 使用 iothub-explorer 监视设备到云的消息。
- 使用 iothub-explorer 发送云到设备的消息。

## <a name="what-you-need"></a>所需条件

- 满足已完成的教程[将 ESP8266 连接到 Azure IoT 中心](/iot-hub-arduino-huzzah-esp8266-get-started.md)所述的以下要求：
  - 一个有效的 Azure 订阅。
  - 已订阅中创建一个 Azure IoT 中心。
  - 一个可向 Azure IoT 中心发送消息的客户端应用程序。
- iothub-explorer。 （[安装 iothub-explorer](https://github.com/azure/iothub-explorer)）

## <a name="monitor-device-to-cloud-messages"></a>监视设备到云的消息

若要监视设备发送到 IoT 中心的消息，请执行以下步骤：

1. 打开控制台窗口。
1. 运行以下命令：

   ```bash
   iothub-explorer monitor-events <device-id> --login <IoTHubConnectionString>
   ```

   > [!Note]
   > 从 IoT 中心获取 `<device-id>` 和 `<IoTHubConnectionString>`。 确保已完成以前的教程。

## <a name="send-cloud-to-device-messages"></a>发送“云到设备”消息

若要将消息从 IoT 中心发送到设备，请执行以下步骤：

1. 打开控制台窗口。
1. 运行以下命令，在 IoT 中心内启动一个会话：

   ```bash
   iothub-explorer login <IoTHubConnectionString>
   ```

1. 运行以下命令，将消息发送到设备：

   ```bash
   iothub-explorer send <device-id> <message>
   ```

该命令将闪烁连接到设备的 LED，然后将消息发送到设备。

> [!Note]
> 设备收到消息后，不需要向 IoT 中心发送单独的确认命令。

## <a name="next-steps"></a>后续步骤

现在，你已了解如何监视设备到云的消息，以及在 IoT 设备与 Azure IoT 中心之间发送云到设备的消息。

若要继续了解 IoT 中心入门知识并浏览其他 IoT 方案，请参阅：

- [将 IoT 中心消息保存到 Azure 数据存储](iot-hub-store-data-in-azure-table-storage.md)
