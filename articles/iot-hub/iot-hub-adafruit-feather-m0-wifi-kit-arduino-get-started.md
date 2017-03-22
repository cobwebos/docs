---
title: "将 Arduino (C) 连接到 Azure IoT - 入门 | Microsoft Docs"
description: "开始使用 Adafruit Feather M0 WiFi、创建 Azure IoT 中心以及将 Adafruit Feather M0 WiFi 连接到 IoT 中心"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "azure iot 中心, 开始使用物联网, 物联网教程, adafruit 物联网, 开始使用 arduino"
ms.assetid: 51befcdb-332b-416f-a6a1-8aabdb67f283
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: 6789e6f1c094f8809163e29349c8ea54e1e97683
ms.lasthandoff: 01/24/2017


---
# <a name="get-started-with-your-arduino-board-adafruit-feather-m0-wifi"></a>Arduino 开发板入门：Adafruit Feather M0 WiFi

在本教程中，从学习如何使用 Arduino 开发板的基础知识开始。 然后将学习如何使用 [Azure IoT 中心](iot-hub-what-is-iot-hub.md)将设备无缝连接到云。

## <a name="lesson-1-configure-your-device"></a>第 1 课：配置设备
![第 1 课端到端关系图][Lesson-1-end-to-end-diagram]

在本课中，需为 Arduino 开发板配置操作系统、设置开发环境，以及将应用程序部署到 Arduino 开发板。

### <a name="configure-your-device"></a>配置设备
组装开发板、通电打开，配置首次使用的 Arduino 开发板。

*估计完成时间：5 分钟*

转到[配置设备][configure-your-device]。

### <a name="get-the-tools"></a>获取工具
下载相关工具和软件，以便为 Arduino 开发板生成和部署第一个应用程序。

*估计完成时间：20 分钟*

转到[获取工具][get-the-tools]

### <a name="create-and-deploy-the-blink-application"></a>创建并部署 blink 应用程序
克隆 GitHub 提供的示例 Arduino 闪烁应用程序，并使用 gulp 将此应用程序部署到 Arduino 开发板。 此示例应用程序让 GPIO #13 开发板 LED 每两秒闪烁一次。

*估计完成时间：5 分钟*

转到[创建并部署闪烁应用程序][create-and-deploy-the-blink-application]。

## <a name="lesson-2-create-your-iot-hub"></a>第 2 课：创建 IoT 中心
![第 2 课端到端关系图][lesson-2-end-to-end-diagram]

在本课中，用户需创建免费的 Azure 帐户、预配 Azure IoT 中心，以及在 IoT 中心创建第一个设备。

开始本课之前，请完成第 1 课。

### <a name="get-the-azure-tools"></a>获取 Azure 工具
安装 Azure 命令行界面 (Azure CLI)。

*估计完成时间：10 分钟*

转到[获取 Azure 工具][get-azure-tools]。

### <a name="create-your-iot-hub-and-register-your-arduino-board"></a>创建 IoT 中心并注册 Arduino 开发板
使用 Azure CLI 创建资源组、预配第一个 Azure IoT 中心，并将第一个设备添加到 IoT 中心。

*估计完成时间：10 分钟*

转到[创建 IoT 中心并注册 Arduino 开发板][create-your-iot-hub-and-register-your-arduino-board]。

## <a name="lesson-3-send-device-to-cloud-messages"></a>第 3 课：发送“设备到云”消息
![第 3 课端到端关系图][lesson-3-end-to-end-diagram]

在本课中，需要将消息从 Arduino 开发板发送到 IoT 中心。 此外还需创建一个 Azure Function App，以用于获取来自 IoT 中心的传入消息并将其写入到 Azure 表存储。

开始本课之前，请完成第 1 课和第 2 课。

### <a name="create-an-azure-function-app-and-azure-storage-account"></a>创建 Azure 函数应用和 Azure 存储帐户
使用 Azure Resource Manager 模板创建一个 Azure Function App 和一个 Azure 存储帐户。

*估计完成时间：10 分钟*

转到[创建 Azure Function App 和 Azure 存储帐户][create-an-azure-function-app-and-azure-storage-account]。

### <a name="run-a-sample-application-to-send-device-to-cloud-messages"></a>运行示例应用程序以发送“设备到云”消息
在 Arduino 开发板上部署并运行示例应用程序，将消息发送到 IoT 中心。

*估计完成时间：10 分钟*

转到[运行示例应用程序以发送“设备到云”消息][send-device-to-cloud-messages]。

### <a name="read-messages-persisted-in-azure-storage"></a>读取 Azure 存储中保存的消息
在“设备到云”消息写入到 Azure 存储时对这些消息进行监视。

*估计完成时间：5 分钟*

转到[读取 Azure 存储中保存的消息][read-messages-persisted-in-azure-storage]。

## <a name="lesson-4-send-cloud-to-device-messages"></a>第 4 课：发送“云到设备”消息
![第 4 课端到端关系图][lesson-4-end-to-end-diagram]

本课介绍如何从 Azure IoT 中心向 Arduino 开发板发送消息。 这些消息控制 GPIO #13 开发板 LED 的亮起和熄灭行为。 示例应用程序已准备就绪，你可以执行此任务了。

开始本课之前，请完成第 1 课、第 2 课和第 3 课。

### <a name="run-the-sample-application-to-receive-cloud-to-device-messages"></a>运行示例应用程序以接收“云到设备”消息
第 4 课中的示例应用程序在 Arduino 开发板上运行，用于监视来自 IoT 中心的传入消息。 新的 gulp 任务会从 IoT 中心向 Arduino 开发板发送消息，使 LED 闪烁。

*估计完成时间：10 分钟*

转到[运行示例应用程序以接收“云到设备”消息][receive-cloud-to-device-messages]。

### <a name="optional-section-change-the-on-and-off-behavior-of-the-led"></a>可选部分：更改 LED 的亮起和熄灭行为
对消息进行自定义以更改 LED 的亮起和熄灭行为。

*估计完成时间：10 分钟*

转到[可选部分：更改 LED 的亮起和熄灭行为][change-the-on-and-off-behavior-of-the-led]。

## <a name="troubleshooting"></a>故障排除
如果在课程中遇到任何问题，可在[故障排除][troubleshooting]一文中查找解决方案。

<!-- Images and links -->

[Lesson-1-end-to-end-diagram]: media/iot-hub-adafruit-feather-m0-wifi-lessons/e2e-lesson1.png
[configure-your-device]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson1-configure-your-device.md
[get-the-tools]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson1-get-the-tools-win32.md
[create-and-deploy-the-blink-application]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson1-deploy-blink-app.md
[lesson-2-end-to-end-diagram]: media/iot-hub-adafruit-feather-m0-wifi-lessons/e2e-lesson2.png
[get-azure-tools]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson2-get-azure-tools-win32.md
[create-your-iot-hub-and-register-your-arduino-board]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson2-prepare-azure-iot-hub.md
[lesson-3-end-to-end-diagram]: media/iot-hub-adafruit-feather-m0-wifi-lessons/e2e-lesson3.png
[create-an-azure-function-app-and-azure-storage-account]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson3-deploy-resource-manager-template.md
[send-device-to-cloud-messages]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson3-run-azure-blink.md
[read-messages-persisted-in-azure-storage]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson3-read-table-storage.md
[lesson-4-end-to-end-diagram]: media/iot-hub-adafruit-feather-m0-wifi-lessons/e2e-lesson4.png
[receive-cloud-to-device-messages]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson4-send-cloud-to-device-messages.md
[change-the-on-and-off-behavior-of-the-led]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson4-change-led-behavior.md
[troubleshooting]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-troubleshooting.md
