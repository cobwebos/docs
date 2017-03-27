---
title: "将 Intel Edison（节点）连接到 Azure IoT - 入门 | Microsoft Docs"
description: "开始使用 Intel Edison，创建 Azure IoT 中心，并将 Edison 连接到 IoT 中心"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "intel edison 开发, azure iot 中心, 开始使用物联网, 物联网教程, adafruit 物联网, intel edison arduino, 开始使用 arduino"
ms.assetid: a7c9cf2d-c102-41b0-aa45-41285c6877eb
ms.service: iot-hub
ms.devlang: nodejs
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 7adde91586f5fbbffd0aeaf0efb0810cc891ac0b
ms.openlocfilehash: 43b6efb64c24405ae3638f6d04da5d21e30a55e1
ms.lasthandoff: 03/02/2017


---
# <a name="connect-your-intel-edison-device-to-your-iot-hub-using-nodejs"></a>使用 Node.js 将 Intel Edison 设备连接到 IoT 中心
> [!div class="op_single_selector"]
> * [Node.JS](iot-hub-intel-edison-kit-node-get-started.md)
> * [C](iot-hub-intel-edison-kit-c-get-started.md)

在本教程中，从学习如何使用 Intel Edison 的基础知识开始。 然后将学习如何使用 [Azure IoT 中心](iot-hub-what-is-iot-hub.md)将设备无缝连接到云。

还没有工具包？ 从[此处](https://azure.microsoft.com/develop/iot/starter-kits)开始

## <a name="lesson-1-configure-your-device"></a>第 1 课：配置设备
![第 1 课端到端关系图](media/iot-hub-intel-edison-lessons/e2e-lesson1.png)

在本课中，会为 Intel Edison 配置操作系统、设置开发环境，以及将应用程序部署到 Edison。

### <a name="configure-your-device"></a>配置设备
首次配置 Intel Edison 进行使用的操作步骤如下：组装开发板，接通电源，在台式机操作系统中安装配置工具以刷写 Edison 固件、设置密码并将其连接到 Wi-Fi。  

*估计完成时间：30 分钟*

转到[配置设备][configure-your-device]。

### <a name="get-the-tools"></a>获取工具
下载相关工具和软件，为 Intel Edison 生成和部署第一个应用程序。

*估计完成时间：20 分钟*

转到[获取工具][get-the-tools]。

### <a name="create-and-deploy-the-blink-application"></a>创建并部署 blink 应用程序
克隆 GitHub 提供的示例 blink 应用程序，并使用 gulp 将此应用程序部署到 Intel Edison 板。 此示例应用程序让连接到电路板的 LED 每隔两秒闪烁一次。

*估计完成时间：5 分钟*

转到[创建并部署闪烁应用程序][create-and-deploy-the-blink-application]。

## <a name="lesson-2-create-your-iot-hub"></a>第 2 课：创建 IoT 中心
![第 2 课端到端关系图](media/iot-hub-intel-edison-lessons/e2e-lesson2.png)

在本课中，用户需创建免费的 Azure 帐户、预配 Azure IoT 中心，以及在 IoT 中心创建第一个设备。

开始本课之前，请完成第 1 课。

### <a name="get-the-azure-tools"></a>获取 Azure 工具
安装 Azure 命令行界面 (Azure CLI)。

*估计完成时间：10 分钟*

转到[获取 Azure 工具][get-azure-tools]。

### <a name="create-your-iot-hub-and-register-intel-edison"></a>创建 IoT 中心并注册 Intel Edison
使用 Azure CLI 创建资源组、预配第一个 Azure IoT 中心，并将第一个设备添加到 IoT 中心。

*估计完成时间：10 分钟*

转到[创建 IoT 中心并注册 Intel Edison](iot-hub-intel-edison-kit-node-lesson2-prepare-azure-iot-hub.md)。

## <a name="lesson-3-send-device-to-cloud-messages"></a>第 3 课：发送“设备到云”消息
![第 3 课端到端关系图](media/iot-hub-intel-edison-lessons/e2e-lesson3.png)

在本课中，会将消息从 Edison 发送到 IoT 中心。 此外还需创建一个 Azure 函数应用，以便获取 IoT 中心发出的传入消息并将其写入到 Azure 表存储。

开始本课之前，请完成第 1 课和第 2 课。

### <a name="create-an-azure-function-app-and-azure-storage-account"></a>创建 Azure 函数应用和 Azure 存储帐户
使用 Azure Resource Manager 模板创建一个 Azure Function App 和一个 Azure 存储帐户。

*估计完成时间：10 分钟*

转到[创建 Azure Function App 和 Azure 存储帐户][create-an-azure-function-app-and-azure-storage-account]。

### <a name="run-a-sample-application-to-send-device-to-cloud-messages"></a>运行示例应用程序以发送“设备到云”消息
在 Intel Edison 设备上部署并运行示例应用程序，将消息发送到 IoT 中心。

*估计完成时间：10 分钟*

转到[运行示例应用程序以发送“设备到云”消息][send-device-to-cloud-messages]。

### <a name="read-messages-persisted-in-azure-storage"></a>读取 Azure 存储中保存的消息
在“设备到云”消息写入到 Azure 存储时对这些消息进行监视。

*估计完成时间：5 分钟*

转到[读取 Azure 存储中保存的消息][read-messages-persisted-in-azure-storage]。

## <a name="lesson-4-send-cloud-to-device-messages"></a>第 4 课：发送“云到设备”消息
![第 4 课端到端关系图](media/iot-hub-intel-edison-lessons/e2e-lesson4.png)

本课说明如何将消息从 Azure IoT 中心发送到 Intel Edison。 这些消息控制连接到 Edison 的 LED 的开关行为。 示例应用程序已准备就绪，你可以执行此任务了。

开始本课之前，请完成第 1 课、第 2 课和第 3 课。

### <a name="run-the-sample-application-to-receive-cloud-to-device-messages"></a>运行示例应用程序，接收从云到设备的消息
第 4 课中的示例应用程序在 Edison 上运行，并监视来自 IoT 中心的传入消息。 新的 gulp 任务会将消息从 IoT 中心发送到 Edison，使 LED 闪烁。

*估计完成时间：10 分钟*

转到[运行示例应用程序以接收“云到设备”消息][receive-cloud-to-device-messages]。

### <a name="optional-section-change-the-on-and-off-behavior-of-the-led"></a>可选部分：更改 LED 的亮起和熄灭行为
对消息进行自定义以更改 LED 的亮起和熄灭行为。

*估计完成时间：10 分钟*

转到[可选部分：更改 LED 的亮起和熄灭行为][change-the-on-and-off-behavior-of-the-led]。

## <a name="troubleshooting"></a>故障排除
如果在课程中遇到任何问题，可在[故障排除][troubleshooting]一文中查找解决方案。
<!-- Images and links -->

[configure-your-device]: iot-hub-intel-edison-kit-node-lesson1-configure-your-device.md
[get-the-tools]: iot-hub-intel-edison-kit-node-lesson1-get-the-tools-win32.md
[create-and-deploy-the-blink-application]: iot-hub-intel-edison-kit-node-lesson1-deploy-blink-app.md
[get-azure-tools]: iot-hub-intel-edison-kit-node-lesson2-get-azure-tools-win32.md
[create-an-azure-function-app-and-azure-storage-account]: iot-hub-intel-edison-kit-node-lesson3-deploy-resource-manager-template.md
[send-device-to-cloud-messages]: iot-hub-intel-edison-kit-node-lesson3-run-azure-blink.md
[read-messages-persisted-in-azure-storage]: iot-hub-intel-edison-kit-node-lesson3-read-table-storage.md
[receive-cloud-to-device-messages]: iot-hub-intel-edison-kit-node-lesson4-send-cloud-to-device-messages.md
[change-the-on-and-off-behavior-of-the-led]: iot-hub-intel-edison-kit-node-lesson4-change-led-behavior.md
[troubleshooting]: iot-hub-intel-edison-kit-node-troubleshooting.md
