---
title: "将 Intel Edison（节点）连接到 Azure IoT - 第 3 课：发送消息 | Microsoft Docs"
description: "在 Intel Edison 上部署并运行示例应用程序，将消息发送到 IoT 中心并使 LED 闪烁。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "iot 云服务, arduino 向云发送数据"
ROBOTS: NOINDEX
redirect_url: /azure/iot-hub/iot-hub-intel-edison-kit-node-get-started
ms.assetid: 1b3b1074-f4d4-42ac-b32c-55f18b304b44
ms.service: iot-hub
ms.devlang: nodejs
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
ms.translationtype: Human Translation
ms.sourcegitcommit: adf5b10721a28432e6b37ef73c6a7e7ec9f93cdd
ms.openlocfilehash: ddc40f2e3467cb406a7336924f9577d52061c42e
ms.contentlocale: zh-cn
ms.lasthandoff: 01/25/2017

---
<a id="run-a-sample-application-to-send-device-to-cloud-messages" class="xliff"></a>

# 运行示例应用程序以发送“设备到云”消息
<a id="what-you-will-do" class="xliff"></a>

## 执行的操作
本文说明如何在 Intel Edison 上部署和运行示例应用程序，以便将消息发送到 IoT 中心。 如果有任何问题，请在[故障排除页面][troubleshooting]上查找解决方案。

<a id="what-you-will-learn" class="xliff"></a>

## 你要学习的知识
可学习如何使用 gulp 工具在 Edison 上部署和运行示例 C 应用程序。

<a id="what-you-need" class="xliff"></a>

## 所需条件
* 在开始此任务之前，必须已成功完成了[创建 Azure Function App 和 Azure 存储帐户来处理和存储 IoT 中心消息][process-and-store-iot-hub-messages]。

<a id="get-your-iot-hub-and-device-connection-strings" class="xliff"></a>

## 获取 IoT 中心连接字符串和设备连接字符串
设备连接字符串用于将 Edison 连接到 IoT 中心。 IoT 中心连接字符串用于将 IoT 中心连接到在 IoT 中心代表 Edison 的设备标识。

* 运行以下 Azure CLI 命令，列出资源组中的所有 IoT 中心：

```bash
az iot hub list -g iot-sample --query [].name
```

使用 `iot-sample` 作为 `{resource group name}` 的值（如果尚未更改此值）。

* 运行以下 Azure CLI 命令，获取 IoT 中心连接字符串：

```bash
az iot hub show-connection-string --name {my hub name}
```

`{my hub name}` 是创建 IoT 中心和注册 Edison 时指定的名称。

* 运行以下命令，获取设备连接字符串：

```bash
az iot device show-connection-string --hub-name {my hub name} --device-id myinteledison
```

使用 `myinteledison` 作为 `{device id}` 的值（如果尚未更改此值）。

<a id="configure-the-device-connection" class="xliff"></a>

## 配置设备连接
1. 通过运行以下命令初始化配置文件：

   ```bash
   npm install
   gulp init
   ```

2. 通过运行以下命令在 Visual Studio Code 中打开设备配置文件 `config-edison.json`：

   ```bash
   # For Windows command prompt
   code %USERPROFILE%\.iot-hub-getting-started\config-edison.json

   # For MacOS or Ubuntu
   code ~/.iot-hub-getting-started/config-edison.json
   ```

   ![config.json](media/iot-hub-intel-edison-lessons/lesson3/config.png)
3. 在 `config-edison.json` 文件中进行以下替换：

   * 将 **[设备主机名或 IP 地址]** 替换为配置设备时标记的设备 IP 地址。
   * 将 **[IoT device connection string]** 替换为你获取的 `device connection string`。
   * 将 **[IoT hub connection string]** 替换为你获取的 `iot hub connection string`。

   > [!NOTE]
   > 本文中不需要 `azure_storage_connection_string`。 请保留该名称。

<a id="deploy-and-run-the-sample-application" class="xliff"></a>

## 部署并运行示例应用程序
运行以下命令，在 Edison 上部署并运行示例应用程序：

```bash
gulp deploy && gulp run
```

<a id="verify-that-the-sample-application-works" class="xliff"></a>

## 验证示例应用程序是否正常运行
应看到连接到 Edison 的 LED 每隔两秒闪烁一次。 每次 LED 闪烁时，示例应用程序都会将消息发送到 IoT 中心，并验证该消息是否已成功发送到 IoT 中心。 此外，还会在控制台窗口中输出 IoT 中心收到的每条消息。 在发送 20 条消息后，示例应用程序将自动终止。

![包含发送和接收的消息的示例应用程序][sample-application-with-sent-and-received-messages]

<a id="summary" class="xliff"></a>

## 摘要
已在 Edison 上部署和运行新的 blink 示例应用程序，目的是将设备到云消息发送到 IoT 中心。 现可在将消息写入存储帐户时对其进行监视。

<a id="next-steps" class="xliff"></a>

## 后续步骤
[读取 Azure 存储中保存的消息][read-messages-persisted-in-azure-storage]
<!-- Images and links -->

[troubleshooting]: iot-hub-intel-edison-kit-node-troubleshooting.md
[process-and-store-iot-hub-messages]: iot-hub-intel-edison-kit-node-lesson3-deploy-resource-manager-template.md
[sample-application-with-sent-and-received-messages]: media/iot-hub-intel-edison-lessons/lesson3/gulp_run.png
[read-messages-persisted-in-azure-storage]: iot-hub-intel-edison-kit-node-lesson3-read-table-storage.md
