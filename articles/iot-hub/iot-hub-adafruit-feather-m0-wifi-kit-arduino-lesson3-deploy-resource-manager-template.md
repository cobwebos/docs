---
title: "将 Arduino (C) 连接到 Azure IoT - 第 3 课：模板部署 | Microsoft Docs"
description: "Azure Function App 侦听 Azure IoT 中心事件，处理传入的消息并将其写入到 Azure 表存储中。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "将数据存储在云中, 存储在云中的数据, iot 云服务"
ms.assetid: 9c8f4cd1-9511-4601-ad7e-51761a986753
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: cf666d17406a49036082a6a4dd15bad89c04aa8f
ms.lasthandoff: 01/24/2017


---

# <a name="create-an-azure-function-app-and-azure-storage-account"></a>创建 Azure Function App 和 Azure 存储帐户
[Azure Functions](../../articles/azure-functions/functions-overview.md) 是一个用于轻松在云中运行*函数*（小段代码）的解决方案。 Azure Function App 主导函数在 Azure 中的执行。

## <a name="what-will-you-do"></a>执行的操作
使用 Azure Resource Manager 模板创建 Azure 函数应用和 Azure 存储帐户。 Azure Function App 侦听 Azure IoT 中心事件，处理传入的消息并将其写入到 Azure 表存储中。

如果遇到任何问题，请在 [Adafruit Feather M0 WiFi Arduino 开发板的故障排除页](iot-hub-adafruit-feather-m0-wifi-kit-arduino-troubleshooting.md)中查找解决方法。

## <a name="what-will-you-learn"></a>学习的内容
在本文中，将学习以下内容：
* 如何使用 [Azure Resource Manager](../../articles/azure-resource-manager/resource-group-overview.md) 部署 Azure 资源。
* 如何使用 Azure Function App 处理 IoT 中心消息并将其写入到 Azure 表存储中的表中。

## <a name="what-do-you-need"></a>所需条件
必须已成功完成了以下任务：
- [Arduino 开发板入门][get-started]
- [创建 Azure IoT 中心][create-iot-hub]

## <a name="open-the-sample-app"></a>打开示例应用
通过运行以下命令在 Visual Studio Code 中打开示例项目：

```bash
cd Lesson3
code .
```

![存储库结构][repo-structure]

* `app` 子文件夹中的 `app.ino` 文件是关键源文件。 此源文件中包含的代码用于将一条消息向 IoT 中心发送 20 次并针对它发送的每条消息使 LED 闪烁。
* `config.json` 包含所需的配置设置。
* `arm-template.json` 文件是 Azure Resource Manager 模板，其中包含一个 Azure Function App 和一个 Azure 存储帐户。
* `arm-template-param.json` 文件是 Azure Resource Manager 模板使用的配置文件。
* `ReceiveDeviceMessages` 子文件夹包含 Azure 函数的 Node.js 代码。

## <a name="configure-azure-resource-manager-templates-and-create-resources-in-azure"></a>在 Azure 中配置 Azure Resource Manager 模板并创建资源
在 Visual Studio Code 中更新 `arm-template-param.json` 文件。

![Azure Resource Manager 模板参数][arm-template-params]

* 将 **[your IoT Hub name]** 替换为在[创建 IoT 中心并注册 Arduino 开发板][created-iot-hub-and-registered-arduino-board] 时指定的 **{my hub name}**。
* 将 **[prefix string for new resources]** 替换为你需要的任何前缀。 前缀可以确保资源名称全局唯一以避免冲突。 不要使用短划线或数字作为前缀的开头。

更新 `arm-template-param.json` 文件后，通过运行以下命令将资源部署到 Azure：

```bash
az group deployment create --template-file arm-template.json --parameters @arm-template-param.json -g iot-sample
```

创建这些资源需要花费大约五分钟。 在资源创建期间，你可以前进到下一文章。

## <a name="summary"></a>摘要
你已创建了一个 Azure Function App 来处理 IoT 中心消息，并创建了一个 Azure 存储帐户来存储这些消息。 现在，可在 Arduino 开发板上部署并运行示例，以发送设备到云的消息。

## <a name="next-steps"></a>后续步骤
[在 Arduino 开发板上运行示例应用程序以发送设备到云的消息][send-device-to-cloud-messages]

<!-- Images and links -->

[get-started]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-get-started.md
[create-iot-hub]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson2-prepare-azure-iot-hub.md
[repo-structure]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson3/repo_structure_c.png
[arm-template-params]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson3/arm_para_arduino.png
[created-iot-hub-and-registered-arduino-board]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson2-prepare-azure-iot-hub.md
[send-device-to-cloud-messages]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson3-run-azure-blink.md
