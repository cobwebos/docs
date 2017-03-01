---
title: "将 Raspberry Pi (C) 连接到 Azure IoT - 第 3 课：模板部署 |Microsoft Docs"
description: "Azure Function App 侦听 Azure IoT 中心事件，处理传入的消息并将其写入到 Azure 表存储中。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "将数据存储在云中, 存储在云中的数据, iot 云服务"
ms.assetid: 4bcfb071-b3ae-48cc-8ea5-7e7434732287
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: 402a5dac4e77f64576acb968b67df92167f54f27


---
# <a name="create-an-azure-function-app-and-azure-storage-account"></a>创建 Azure Function App 和 Azure 存储帐户
[Azure Functions](../../articles/azure-functions/functions-overview.md) 是一个用于轻松在云中运行*函数*（小段代码）的解决方案。 Azure Function App 主导函数在 Azure 中的执行。

## <a name="what-will-you-do"></a>执行的操作
使用 Azure Resource Manager 模板创建 Azure 函数应用和 Azure 存储帐户。 Azure Function App 侦听 Azure IoT 中心事件，处理传入的消息并将其写入到 Azure 表存储中。 如果有任何问题，请在[故障排除页面](iot-hub-raspberry-pi-kit-c-troubleshooting.md)上查找解决方案。

## <a name="what-will-you-learn"></a>学习的内容
在本文中，将学习以下内容：
* 如何使用 [Azure Resource Manager](../../articles/azure-resource-manager/resource-group-overview.md) 部署 Azure 资源。
* 如何使用 Azure Function App 处理 IoT 中心消息并将其写入到 Azure 表存储中的表中。

## <a name="what-do-you-need"></a>所需条件
* 必须已成功完成了以下任务：
- [Raspberry Pi 3 入门](iot-hub-raspberry-pi-kit-c-get-started.md)
- [创建 Azure IoT 中心](iot-hub-raspberry-pi-kit-c-get-started.md)

## <a name="open-the-sample-app"></a>打开示例应用
通过运行以下命令在 Visual Studio Code 中打开示例项目：

```bash
cd Lesson3
code .
```

![存储库结构](media/iot-hub-raspberry-pi-lessons/lesson3/repo_structure_c.png)

* `app` 子文件夹中的 `main.c` 文件是关键源文件。 此源文件中包含的代码用于将一条消息向 IoT 中心发送 20 次并针对它发送的每条消息使 LED 闪烁。
* `arm-template.json` 文件是 Azure Resource Manager 模板，其中包含一个 Azure Function App 和一个 Azure 存储帐户。
* `arm-template-param.json` 文件是 Azure Resource Manager 模板使用的配置文件。
* `ReceiveDeviceMessages` 子文件夹包含 Azure 函数的 Node.js 代码。

## <a name="configure-azure-resource-manager-templates-and-create-resources-in-azure"></a>在 Azure 中配置 Azure Resource Manager 模板并创建资源
在 Visual Studio Code 中更新 `arm-template-param.json` 文件。

![Azure Resource Manager 模板参数](media/iot-hub-raspberry-pi-lessons/lesson3/arm_para_c.png)

* 将 **[your IoT Hub name]** 替换为你在[创建 IoT 中心并注册 Raspberry Pi 3](iot-hub-raspberry-pi-kit-c-lesson2-prepare-azure-iot-hub.md) 时指定的 **{my hub name}**。
* 将 **[prefix string for new resources]** 替换为你需要的任何前缀。 前缀可以确保资源名称全局唯一以避免冲突。 不要使用短划线或数字作为前缀的开头。

更新 `arm-template-param.json` 文件后，通过运行以下命令将资源部署到 Azure：

```bash
az group deployment create --template-file arm-template.json --parameters @arm-template-param.json -g iot-sample
```

创建这些资源需要花费大约五分钟。 在资源创建期间，你可以前进到下一文章。

## <a name="summary"></a>摘要
你已创建了一个 Azure Function App 来处理 IoT 中心消息，并创建了一个 Azure 存储帐户来存储这些消息。 现在，你可以在 Pi 上部署并运行示例应用程序以发送“设备到云”消息了。

## <a name="next-steps"></a>后续步骤
[运行示例应用程序以发送“设备到云”消息](iot-hub-raspberry-pi-kit-c-lesson3-run-azure-blink.md)




<!--HONumber=Jan17_HO4-->


