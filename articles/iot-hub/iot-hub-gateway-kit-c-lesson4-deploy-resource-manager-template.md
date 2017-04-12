---
title: "SensorTag 设备和 Azure IoT 网关 - 第 4 课：创建 Function App | Microsoft Docs"
description: "将消息从 Intel NUC 保存到 IoT 中心，将其写入到 Azure 表存储，然后从云中读取它们。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "将数据存储在云中, 存储在云中的数据, iot 云服务"
ROBOTS: NOINDEX
redirect_url: /azure/iot-hub/iot-hub-gateway-kit-c-lesson1-set-up-nuc
ms.assetid: f84f9a85-e2c4-4a92-8969-f65eb34c194e
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 61e9a9fc7876094c04238c61cfc38efdd97b05f7
ms.openlocfilehash: 3672804218dbbe3a545536b8c4baccb2b9171cdc
ms.lasthandoff: 01/25/2017


---
# <a name="create-an-azure-function-app-and-storage-account"></a>创建 Azure Function App 和存储帐户

Azure Functions 是一个用于轻松在云中运行_函数_（小段代码）的解决方案。 Azure Function App 主导函数在 Azure 中的执行。 

## <a name="what-you-will-do"></a>执行的操作

- 使用 Azure Resource Manager 模板创建一个 Azure Function App 和一个 Azure 存储帐户。 Azure Function App 侦听 Azure IoT 中心事件，处理传入的消息并将其写入到 Azure 表存储中。

如果有任何问题，请在[故障排除页面](iot-hub-gateway-kit-c-troubleshooting.md)上查找解决方案。


## <a name="what-you-will-learn"></a>你要学习的知识

在本课程中将学习：

- 如何使用 Azure Resource Manager 部署 Azure 资源。
- 如何使用 Azure Function App 处理 IoT 中心消息并将其写入到 Azure 表存储中的表中。

## <a name="what-you-need"></a>所需条件

必须已成功完成前面的课程：

- [第 1 课：将 Intel NUC 设置为 IoT 网关](iot-hub-gateway-kit-c-lesson1-set-up-nuc.md)
- [第 2 课：准备好主机和 Azure IoT 中心](iot-hub-gateway-kit-c-lesson2-get-the-tools-win32.md)
- [第 3 课：从 SensorTag 接收消息和从 IoT 中心读取消息](iot-hub-gateway-kit-c-lesson3-configure-ble-app.md)

## <a name="open-a-sample-app"></a>打开示例应用

转到 `iot-hub-c-intel-nuc-gateway-getting-started` 存储库文件夹，初始化配置文件，并通过运行以下命令，在 Visual Studio Code 中打开示例项目：

```bash
cd Lesson4
npm install
gulp init
code .
```

![存储库结构](media/iot-hub-gateway-kit-lessons/lesson4/arm_template.png)

- `arm-template.json` 文件是 Azure Resource Manager 模板，其中包含一个 Azure Function App 和一个 Azure 存储帐户。
- `arm-template-param.json` 文件是 Azure Resource Manager 模板使用的配置文件。
- `ReceiveDeviceMessages` 子文件夹包含 Azure 函数的 Node.js 代码。

## <a name="configure-azure-resource-manager-templates-and-create-resources-in-azure"></a>在 Azure 中配置 Azure Resource Manager 模板并创建资源

在 Visual Studio Code 中更新 `arm-template-param.json` 文件。

![arm 模板 json](media/iot-hub-gateway-kit-lessons/lesson4/arm_template_param.png)

- 将 `[your IoT Hub name]` 替换为在第 2 课中指定的 `{my hub name}`。

更新 `arm-template-param.json` 文件后，通过运行以下命令将资源部署到 Azure：

```bash
az group deployment create --template-file arm-template.json --parameters @arm-template-param.json -g iot-gateway
```

使用 `iot-gateway` 作为 `{resource group name}` 的值（如果未在第 2 课中更改此值）。

## <a name="summary"></a>摘要

你已创建了一个 Azure Function App 来处理 IoT 中心消息，并创建了一个 Azure 存储帐户来存储这些消息。 你现在可以读取由网关发送到 IoT 中心的消息。

## <a name="next-steps"></a>后续步骤
[读取 Azure 存储中保存的消息](iot-hub-gateway-kit-c-lesson4-read-table-storage.md)。

