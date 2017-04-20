---
title: "将 Raspberry Pi（节点）连接到 Azure IoT - 第 3 课：运行示例 | Microsoft Docs"
description: "在 Raspberry Pi 3 上部署并运行一个示例应用程序，用以向 IoT 中心发送消息并使 LED 闪烁。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "闪烁 led 云 pi, 通过云使 led 闪烁"
ROBOTS: NOINDEX
redirect_url: /azure/iot-hub/iot-hub-raspberry-pi-kit-c-get-started
ms.assetid: e38df29f-f77f-435f-9add-46814297564f
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: 985ba895bfcad1611bebe80a6c2fc8ffed780da5
ms.lasthandoff: 01/24/2017


---
# <a name="run-a-sample-application-to-send-device-to-cloud-messages"></a>运行示例应用程序以发送“设备到云”消息
## <a name="what-you-will-do"></a>执行的操作
本文将说明如何在 Raspberry Pi 3 上部署并运行一个示例应用程序，用以向 IoT 中心发送消息。 如果有任何问题，请在[故障排除页面](iot-hub-raspberry-pi-kit-c-troubleshooting.md)上查找解决方案。

## <a name="what-you-will-learn"></a>你要学习的知识
你将学习如何使用 gulp 工具在 Pi 上部署并运行示例 Node.js 应用程序。

## <a name="what-you-need"></a>所需条件
* 在开始此任务之前，必须已成功完成了[创建 Azure Function App 和 Azure 存储帐户来处理和存储 IoT 中心消息](iot-hub-raspberry-pi-kit-c-lesson3-deploy-resource-manager-template.md)。

## <a name="get-your-iot-hub-and-device-connection-strings"></a>获取 IoT 中心连接字符串和设备连接字符串
Pi 使用设备连接字符串连接到 IoT 中心。 IoT 中心连接字符串用于连接到 IoT 中心中的标识注册表，以便管理允许连接到 IoT 中心的设备。 

* 运行以下 Azure CLI 命令，列出资源组中的所有 IoT 中心：

```bash
az iot hub list -g iot-sample --query [].name
```

使用 `iot-sample` 作为 `{resource group name}` 的值（如果尚未更改此值）。

* 运行以下 Azure CLI 命令，获取 IoT 中心连接字符串：

```bash
az iot hub show-connection-string --name {my hub name} -g iot-sample
```

`{my hub name}` 是用户在创建 IoT 中心和注册 Pi 时指定的名称。

* 运行以下命令，获取设备连接字符串：

```bash
az iot device show-connection-string --hub-name {my hub name} --device-id myraspberrypi -g iot-sample
```

使用 `myraspberrypi` 作为 `{device id}` 的值（如果尚未更改此值）。

## <a name="configure-the-device-connection"></a>配置设备连接
1. 通过运行以下命令初始化配置文件：
   
   ```bash
   npm install
   gulp init
   ```

> [!NOTE]
> 如果在第 1 课中没有运行 **gulp install-tools**，则还需要运行此命令。

2. 通过运行以下命令在 Visual Studio Code 中打开设备配置文件 `config-raspberrypi.json`：
   
   ```bash
   # For Windows command prompt
   code %USERPROFILE%\.iot-hub-getting-started\config-raspberrypi.json
   
   # For MacOS or Ubuntu
   code ~/.iot-hub-getting-started/config-raspberrypi.json
   ```
   
   ![config.json](media/iot-hub-raspberry-pi-lessons/lesson3/config.png)
3. 在 `config-raspberrypi.json` 文件中进行以下替换：
   
   * 将 **[device hostname or IP address]** 替换为你通过 `device-discovery-cli` 获取的设备 IP 地址或主机名，或者替换为你在配置设备时继承的值。
   * 将 **[IoT device connection string]** 替换为你获取的 `device connection string`。
   * 将 **[IoT hub connection string]** 替换为你获取的 `iot hub connection string`。

> [!NOTE]
> 本文中不需要 `azure_storage_connection_string`。 请保留该名称。

更新 `config-raspberrypi.json` 文件，以便部署计算机中的示例应用程序。

## <a name="deploy-and-run-the-sample-application"></a>部署并运行示例应用程序
通过运行以下命令在 Pi 上部署并运行示例应用程序：

```bash
gulp deploy && gulp run
```

## <a name="verify-that-the-sample-application-works"></a>验证示例应用程序是否正常工作
你应当会看到连接到 Pi 的 LED 每两秒闪烁一次。 LED 每次闪烁时，示例应用程序都会向 IoT 中心发送一条消息并验证该消息是否已成功发送到 IoT 中心。 此外，还会在控制台窗口中输出 IoT 中心收到的每条消息。 在发送 20 条消息后，示例应用程序将自动终止。

![包含发送和接收的消息的示例应用程序](media/iot-hub-raspberry-pi-lessons/lesson3/gulp_run_c.png)

## <a name="summary"></a>摘要
用户已在 Pi 上部署和运行新的 blink 示例应用程序，目的是将从设备到云的消息发送到 IoT 中心。 现可在将消息写入存储帐户时对其进行监视。

## <a name="next-steps"></a>后续步骤
[读取 Azure 存储中保存的消息](iot-hub-raspberry-pi-kit-c-lesson3-read-table-storage.md)


