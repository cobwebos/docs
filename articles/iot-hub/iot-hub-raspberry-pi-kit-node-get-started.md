---
title: "将 Raspberry Pi（节点）连接到 Azure IoT - 入门 | Microsoft Docs"
description: "开始使用 Raspberry Pi 3 设备、创建 Azure IoT 中心，并将 Pi 连接到 IoT 中心。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "azure iot 中心, 物联网入门, iot 工具包"
experimental: true
experiment_id: xshi-happypathemu-20161202
ms.assetid: b0e14bfa-8e64-440a-a6ec-e507ca0f76ba
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 7adde91586f5fbbffd0aeaf0efb0810cc891ac0b
ms.openlocfilehash: 8d283531fc7905e9691feccca25fffd27499e2bb
ms.lasthandoff: 03/02/2017


---
# <a name="connect-your-raspberry-pi-3-device-to-your-iot-hub-using-nodejs"></a>使用 Node.js 将 Raspberry Pi 3 设备连接到 IoT 中心
> [!div class="op_single_selector"]
> * [Node.JS](iot-hub-raspberry-pi-kit-node-get-started.md)
> * [C](iot-hub-raspberry-pi-kit-c-get-started.md)

在本教程中，你将开始学习基础知识，了解如何使用运行 Raspbian 的 Raspberry Pi 3， 然后将学习如何使用 [Azure IoT 中心](iot-hub-what-is-iot-hub.md)将设备无缝连接到云。 有关 Windows 10 IoT Core 的示例，请访问 [Windows 开发人员中心](http://www.windowsondevices.com/)。

还没有工具包？ 从[此处](https://azure.microsoft.com/develop/iot/starter-kits)开始。

## <a name="lesson-1-configure-your-device"></a>第 1 课：配置设备
![第 1 课端到端关系图](media/iot-hub-raspberry-pi-lessons/e2e-lesson1.png)

在本课中，你将为 Raspberry Pi 3 设备配置操作系统、设置开发环境，并将应用程序部署到 Pi。

### <a name="configure-your-device"></a>配置设备
为首次使用配置 Raspberry Pi 3 并安装 Raspbian。 Raspbian 是一个针对 Raspberry Pi 硬件进行了优化的免费操作系统。

*估计完成时间：30 分钟*

转到[配置设备](iot-hub-raspberry-pi-kit-node-lesson1-configure-your-device.md)。

### <a name="get-the-tools"></a>获取工具
下载相关工具和软件，以便为 Raspberry Pi 3 生成和部署第一个应用程序。

*估计完成时间：20 分钟*

转到[获取工具](iot-hub-raspberry-pi-kit-node-lesson1-get-the-tools-win32.md)。

### <a name="create-and-deploy-the-blink-application"></a>创建并部署 blink 应用程序
克隆 GitHub 提供的示例 Node.js blink 应用程序，并使用 gulp 将此应用程序部署到 Raspberry Pi 3 电路板。 此示例应用程序让连接到电路板的 LED 每隔两秒闪烁一次。

*估计完成时间：5 分钟*

转到[创建并部署 blink 应用程序](iot-hub-raspberry-pi-kit-node-lesson1-deploy-blink-app.md)。

## <a name="lesson-2-create-your-iot-hub"></a>第 2 课：创建 IoT 中心
![第 2 课端到端关系图](media/iot-hub-raspberry-pi-lessons/e2e-lesson2.png)

在本课中，你将创建免费的 Azure 帐户、预配 Azure IoT 中心，并在 IoT 中心内创建第一个设备。

开始本课之前，请完成第 1 课。

### <a name="get-the-azure-tools"></a>获取 Azure 工具
安装 Azure 命令行界面 (Azure CLI)。

*估计完成时间：10 分钟*

转到[获取 Azure 工具](iot-hub-raspberry-pi-kit-node-lesson2-get-azure-tools-win32.md)。

### <a name="create-your-iot-hub-and-register-raspberry-pi-3"></a>创建 IoT 中心并注册 Raspberry Pi 3
使用 Azure CLI 创建资源组、预配第一个 Azure IoT 中心，并将第一个设备添加到 IoT 中心。

*估计完成时间：10 分钟*

转到[创建 IoT 中心并注册 Raspberry Pi 3](iot-hub-raspberry-pi-kit-node-lesson2-prepare-azure-iot-hub.md)。

## <a name="lesson-3-send-device-to-cloud-messages"></a>第 3 课：发送“设备到云”消息
![第 3 课端到端关系图](media/iot-hub-raspberry-pi-lessons/e2e-lesson3.png)

在本课中，你需要将消息从 Pi 发送到 IoT 中心。 此外还需创建一个 Azure Function App，以用于获取来自 IoT 中心的传入消息并将其写入到 Azure 表存储。

开始本课之前，请完成第 1 课和第 2 课。

### <a name="create-an-azure-function-app-and-azure-storage-account"></a>创建 Azure Function App 和 Azure 存储帐户
使用 Azure Resource Manager 模板创建一个 Azure Function App 和一个 Azure 存储帐户。

*估计完成时间：10 分钟*

转到[创建 Azure Function App 和 Azure 存储帐户](iot-hub-raspberry-pi-kit-node-lesson3-deploy-resource-manager-template.md)。

### <a name="run-a-sample-application-to-send-device-to-cloud-messages"></a>运行示例应用程序以发送“设备到云”消息
在 Raspberry Pi 3 设备上部署并运行示例应用程序，以便向 IoT 中心发送消息。

*估计完成时间：10 分钟*

转到[运行示例应用程序以发送“设备到云”消息](iot-hub-raspberry-pi-kit-node-lesson3-run-azure-blink.md)。

### <a name="read-messages-persisted-in-azure-storage"></a>读取 Azure 存储中保存的消息
在“设备到云”消息写入到 Azure 存储时对这些消息进行监视。

*估计完成时间：5 分钟*

转到[读取 Azure 存储中保存的消息](iot-hub-raspberry-pi-kit-node-lesson3-read-table-storage.md)。

## <a name="lesson-4-send-cloud-to-device-messages"></a>第 4 课：发送“云到设备”消息
![第 4 课端到端关系图](media/iot-hub-raspberry-pi-lessons/e2e-lesson4.png)

本课介绍如何从 Azure IoT 中心向 Raspberry Pi 3 发送消息。 这些消息控制连接到 Pi 的 LED 的亮起和熄灭行为。 示例应用程序已准备就绪，你可以执行此任务了。

开始本课之前，请完成第 1 课、第 2 课和第 3 课。

### <a name="run-the-sample-application-to-receive-cloud-to-device-messages"></a>运行示例应用程序以接收“云到设备”消息
第 4 课中的示例应用程序在 Pi 上运行，用于监视来自 IoT 中心的传入消息。 新的 gulp 任务会从 IoT 中心向 Pi 发送消息，使 LED 闪烁。

*估计完成时间：10 分钟*

转到[运行示例应用程序以接收“云到设备”消息](iot-hub-raspberry-pi-kit-node-lesson4-send-cloud-to-device-messages.md)。

### <a name="optional-section-change-the-on-and-off-behavior-of-the-led"></a>可选部分：更改 LED 的亮起和熄灭行为
对消息进行自定义以更改 LED 的亮起和熄灭行为。

*估计完成时间：10 分钟*

转到[可选部分：更改 LED 的亮起和熄灭行为](iot-hub-raspberry-pi-kit-node-lesson4-change-led-behavior.md)。

## <a name="troubleshooting"></a>故障排除
如果在课程学习过程中遇到任何问题，可参阅[故障排除](iot-hub-raspberry-pi-kit-node-troubleshooting.md)一文以获取解决方案。


