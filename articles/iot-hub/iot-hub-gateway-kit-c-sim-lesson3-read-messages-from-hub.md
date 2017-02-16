---
title: "从 Azure IoT 中心读取消息 | Microsoft Docs"
description: "在主计算机上运行示例代码，从 IoT 中心读取消息。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "云中的数据, 云数据收集, iot 云服务, iot 数据"
ms.assetid: 5a6ec9c1-d83c-41c1-beaf-7c0d3395d77f
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 65a25dd7a2f6a8d518217512f9e10fc9008ee728
ms.openlocfilehash: c92b9a86ceee306f713df5bcffca87071c905393


---

# <a name="read-messages-from-your-iot-hub"></a>从 IoT 中心读取消息

## <a name="what-you-will-do"></a>执行的操作

- 在主计算机上运行示例代码，从 IoT 中心读取消息。

如果有任何问题，请在[故障排除页面](iot-hub-gateway-kit-c-sim-troubleshooting.md)上查找解决方案。

## <a name="what-you-will-learn"></a>你要学习的知识

如何使用 gulp 工具从 IoT 中心读取消息。

## <a name="what-you-need"></a>所需条件

- [配置并运行模拟设备云上传示例应用程序](iot-hub-gateway-kit-c-sim-lesson3-configure-simulated-device-app.md)中的模拟设备示例。

## <a name="get-your-iot-hub-and-device-connection-strings"></a>获取 IoT 中心连接字符串和设备连接字符串

模拟设备使用设备连接字符串连接到 IoT 中心。 IoT 中心连接字符串用于连接到 IoT 中心中的标识注册表，以便管理允许连接到 IoT 中心的设备。

- 通过运行以下命令，列出资源组中的所有 IoT 中心：

   ```bash
   az iot hub list -g iot-gateway --query [].name
   ```

   使用 `iot-gateway` 作为 `{resource group name}` 的值（如果尚未更改）。
- 通过运行以下命令，获取 IoT 中心连接字符串：

   ```bash
   az iot hub show-connection-string --name {my hub name} -g iot-gateway
   ```

   `{my hub name}` 是在第 2 课中指定的名称。

## <a name="configure-the-device-connection-for-the-sample-code"></a>为示例代码配置设备连接

执行以下步骤，更新 `config-azure.json` 中的 IoT 中心和设备连接配置：

1. 通过在控制台窗口中运行以下命令，在 Visual Studio Code 中打开 `config-azure.json`：

   ```bash
   # For Windows command prompt
   code %USERPROFILE%\.iot-hub-getting-started\config-azure.json
   # For MacOS or Ubuntu
   code ~/.iot-hub-getting-started/config-azure.json
   ```

2. 在 `config-azure.json` 文件中进行以下替换：

   ![配置 azure 的屏幕截图](media/iot-hub-gateway-kit-lessons/lesson3/config_azure.png)

   将 `[IoT hub connection string]` 替换为 IoT 中心连接字符串。

## <a name="read-messages-from-your-iot-hub"></a>从 IoT 中心读取消息

通过以下命令运行模拟设备示例应用程序，并读取 IoT 中心消息：

```bash
gulp run --iot-hub
```

该命令运行每隔 2 秒将消息发送到 IoT 中心的应用程序。 它还生成用于接收消息的子进程。

要发送和接收的消息全部立即显示在主机的相同控制台窗口中。 该应用程序将在 40 秒后退出。

![包含发送和接收的消息的模拟示例应用程序](media/iot-hub-gateway-kit-lessons/lesson3/gulp_run_read_hub_simudev.png)

## <a name="summary"></a>摘要

已成功运行示例应用程序，可使用模拟设备将数据发送到 IoT 中心。 此外，也已读取发送到 IoT 中心的消息。

## <a name="next-steps"></a>后续步骤
[创建 Azure Function App 和 Azure 存储帐户](iot-hub-gateway-kit-c-sim-lesson4-deploy-resource-manager-template.md)





<!--HONumber=Dec16_HO3-->


