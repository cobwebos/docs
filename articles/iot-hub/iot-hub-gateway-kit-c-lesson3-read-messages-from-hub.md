---
title: "SensorTag 设备和 Azure IoT 网关 - 第 3 课：读取消息 | Microsoft Docs"
description: "在主计算机上运行示例代码，从 IoT 中心读取消息。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "云中的数据, 云数据收集, iot 云服务, iot 数据"
ms.assetid: cc88be24-b5c0-4ef2-ba21-4e8f77f3e167
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 61e9a9fc7876094c04238c61cfc38efdd97b05f7
ms.openlocfilehash: 0a5dd7e010ba46b724bb6a2526dc2a2730acc10f


---

# <a name="read-messages-from-your-iot-hub"></a>从 IoT 中心读取消息

## <a name="what-you-will-do"></a>执行的操作

- 在主计算机上运行示例代码，从 IoT 中心读取消息。

如果有任何问题，请在[故障排除页面](iot-hub-gateway-kit-c-troubleshooting.md)上查找解决方案。

## <a name="what-you-will-learn"></a>你要学习的知识

如何使用 gulp 工具从 IoT 中心读取消息。

## <a name="what-you-need"></a>所需条件

- 在第 3 课中成功运行的 BLE 示例应用程序。

## <a name="get-your-iot-hub-and-device-connection-strings"></a>获取 IoT 中心连接字符串和设备连接字符串

设备（TI SensorTag 或模拟设备）使用设备连接字符串连接到 IoT 中心。 IoT 中心连接字符串用于连接到 IoT 中心中的标识注册表，以便管理允许连接到 IoT 中心的设备。

- 运行以下命令，列出资源组中的所有 IoT 中心：

   ```bash
   az iot hub list -g iot-gateway --query [].name
   ```

   使用 `iot-gateway` 作为 `{resource group name}` 的值（如果未更改该值）。
- 运行以下命令，获取 IoT 中心连接字符串：

   ```bash
   az iot hub show-connection-string --name {my hub name} -g iot-gateway
   ```

   `{my hub name}` 是在第 2 课中指定的名称。

## <a name="configure-the-device-connection-for-the-sample-code"></a>为示例代码配置设备连接

更新设备配置文件 `config-azure.json`，以便可以在主机上从 IoT 中心读取消息。 为此，请执行以下步骤：

1. 通过在控制台窗口中运行以下命令在 Visual Studio Code 中打开 `config-azure.json`：

   ```bash
   # For Windows command prompt
   code %USERPROFILE%\.iot-hub-getting-started\config-azure.json
   # For MacOS or Ubuntu
   code ~/.iot-hub-getting-started/config-azure.json
   ```

2. 在 `config-azure.json` 文件中进行以下替换：

   ![配置 azure 的屏幕截图](media/iot-hub-gateway-kit-lessons/lesson3/config_azure.png)

   将 `[IoT hub connection string]` 替换为获取的 IoT 中心连接字符串。

## <a name="read-messages-from-your-iot-hub"></a>从 IoT 中心读取消息

如果有 TI SensorTag，请确保已经打开 SensorTag。 通过以下命令运行网关示例应用程序，并读取 IoT 中心消息：

```bash
gulp run --iot-hub
```

该命令运行 BLE 示例应用程序，该程序从 SensorTag 或模拟设备中读取温度数据并打包，每 2 秒将消息发送到 IoT 中心。 它还生成用于接收消息的子进程。

要发送和接收的消息均立即显示在主机的相同控制台窗口中。 示例应用程序实例将在 40 秒后自动终止。

![包含发送和接收的消息的 BLE 示例应用程序](media/iot-hub-gateway-kit-lessons/lesson3/gulp_run_read_hub.png)

## <a name="summary"></a>摘要

你已运行示例代码，从 IoT 中心读取消息。 你已准备好读取存储在 Azure 表存储中的消息。

## <a name="next-steps"></a>后续步骤
[创建 Azure Function App 和 Azure 存储帐户](iot-hub-gateway-kit-c-lesson4-deploy-resource-manager-template.md)





<!--HONumber=Jan17_HO4-->


