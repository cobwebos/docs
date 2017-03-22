---
title: "模拟设备和 Azure IoT 网关 - 第 4 课：表存储 | Microsoft Docs"
description: "将消息从 Intel NUC 保存到 IoT 中心，将其写入到 Azure 表存储，然后从云中读取它们。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "从云检索数据, iot 云服务"
ms.assetid: 78e4b6ea-968d-401e-a7dc-8f9acdb3ec1a
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 61e9a9fc7876094c04238c61cfc38efdd97b05f7
ms.openlocfilehash: b12e16a5a532448cf2e939cfcad322225b9ee811
ms.lasthandoff: 01/25/2017


---

# <a name="read-messages-persisted-in-azure-table-storage"></a>读取 Azure 表存储中保存的消息

## <a name="what-you-will-do"></a>执行的操作

- 在将消息发送到 IoT 中心的网关上运行网关示例应用程序。
- 在主机计算机上运行示例代码，以便读取 Azure 表存储中的消息。

如果有任何问题，请在[故障排除页面](iot-hub-gateway-kit-c-sim-troubleshooting.md)上查找解决方案。

## <a name="what-you-will-learn"></a>你要学习的知识

如何使用 gulp 工具运行示例代码，以读取 Azure 表存储中的消息。

## <a name="what-you-need"></a>所需条件

已成功完成以下任务：

- [创建了 Azure Function App 和 Azure 存储帐户](iot-hub-gateway-kit-c-sim-lesson4-deploy-resource-manager-template.md)。
- [运行网关示例应用程序](iot-hub-gateway-kit-c-sim-lesson3-configure-simulated-device-app.md)。
- [从 IoT 中心读取消息](iot-hub-gateway-kit-c-sim-lesson3-read-messages-from-hub.md)。

## <a name="get-your-azure-storage-connection-strings"></a>获取 Azure 存储连接字符串

在本课程的前面部分，已成功创建了一个 Azure 存储帐户。 若要获取 Azure 存储帐户的连接字符串，请运行以下命令：

* 列出所有存储帐户。

```bash
az storage account list -g iot-gateway --query [].name
```

* 获取 Azure 存储连接字符串。

```bash
az storage account show-connection-string -g iot-gateway -n {storage name}
```

使用 `iot-gateway` 作为 `{resource group name}` 的值（如果未在第 2 课中更改此值）。

## <a name="configure-the-device-connection"></a>配置设备连接

更新 `config-azure.json` 文件，以便在主计算机上运行的示例代码可以读取 Azure 表存储中的消息。 若要配置设备连接，请执行以下步骤：

1. 通过运行以下命令，打开设备配置文件 `config-azure.json`：

   ```bash
   # For Windows command prompt
   code %USERPROFILE%\.iot-hub-getting-started\config-azure.json
   # For MacOS or Ubuntu
   code ~/.iot-hub-getting-started/config-azure.json
   ```

   ![配置](media/iot-hub-gateway-kit-lessons/lesson4/config_azure.png)

2. 将 `[Azure storage connection string]` 替换为获取的 Azure 存储连接字符串。

   `[IoT hub connection string]` 应已在第&3; 课的[从 Azure IoT 中心读取消息](iot-hub-gateway-kit-c-sim-lesson3-read-messages-from-hub.md)部分中进行了替换。

## <a name="read-messages-in-your-azure-table-storage"></a>读取 Azure 表存储中的消息

通过以下命令运行网关示例应用程序，并读取 Azure 表存储消息：

```bash
gulp run --table-storage
```

IoT 中心会在新消息到达时触发 Azure 函数应用程序，以将消息保存到 Azure 表存储中。
`gulp run` 命令运行将消息发送到 IoT 中心的网关示例应用程序。 借助 `table-storage` 参数，它还生成子进程以接收 Azure 表存储中的已保存消息。

要发送和接收的消息全部立即显示在主机的相同控制台窗口中。 示例应用程序实例将在 40 秒后自动终止。

   ![gulp 读取](media/iot-hub-gateway-kit-lessons/lesson4/gulp_run_read_table_simudev.png)


## <a name="summary"></a>摘要

已运行示例代码，可读取由 Azure 函数应用程序保存在 Azure 表存储中的消息。

