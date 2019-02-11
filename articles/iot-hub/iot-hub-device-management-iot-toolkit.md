---
title: 使用适用于 Visual Studio Code 的 Azure IoT 工具进行 Azure IoT 设备管理 | Microsoft Docs
description: 使用适用于 Visual Studio Code 的 Azure IoT 工具进行 Azure IoT 中心设备管理，特点是使用直接方法并提供孪生所需的属性管理选项。
author: formulahendry
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: junhan
ms.openlocfilehash: ec19045892b975fed7611c7747fa7b2bb4b1515c
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2019
ms.locfileid: "54051130"
---
# <a name="use-azure-iot-tools-for-visual-studio-code-for-azure-iot-hub-device-management"></a>使用适用于 Visual Studio Code 的 Azure IoT 工具进行 Azure IoT 中心设备管理

![端到端关系图](media/iot-hub-get-started-e2e-diagram/2.png)

[Azure IoT 工具](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)是一个有用的 Visual Studio Code 扩展，可以使 IoT 中心管理和 IoT 应用程序开发更加容易。 它附带了可用于执行各种任务的管理选项。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

| 管理选项          | 任务                    |
|----------------------------|--------------------------------|
| 直接方法             | 让设备执行操作，如开始或停止发送消息或重新启动设备。                                        |
| 读取设备孪生           | 获取报告的设备状态。 例如，设备报告 LED 现在正在闪烁。                                    |
| 更新设备孪生         | 让设备进入特定状态，例如将 LED 设置为绿色，或将遥测发送间隔设置为 30 分钟。         |
| 云到设备的消息   | 向设备发送通知。 例如，“今天很可能会下雨。 不要忘记带雨伞。”              |

有关这些选项的差异和使用指导的更详细说明，请参阅[设备到云通信指南](iot-hub-devguide-d2c-guidance.md)和[云到设备通信指南](iot-hub-devguide-c2d-guidance.md)。

设备孪生是存储设备状态信息（元数据、配置和条件）的 JSON 文档。 IoT 中心为连接到它的每台设备保留一个设备孪生。 有关设备孪生的详细信息，请参阅[设备孪生入门](iot-hub-node-node-twin-getstarted.md)。

## <a name="what-you-learn"></a>学习内容

了解在开发计算机上通过各种管理选项使用适用于 Visual Studio Code 的 Azure IoT 工具。

## <a name="what-you-do"></a>准备工作

使用各种管理选项运行适用于 Visual Studio Code 的 Azure IoT 工具。

## <a name="what-you-need"></a>所需条件

* 一个有效的 Azure 订阅。
* 已在订阅中创建一个 Azure IoT 中心。
* [Visual Studio Code](https://code.visualstudio.com/)
* [适用于 VS Code 的 Azure IoT 工具](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)

## <a name="sign-in-to-access-your-iot-hub"></a>登录以访问 IoT 中心

1. 在 VS Code 的“资源管理器”视图中，展开左下角的“Azure IoT 中心设备”部分。

2. 在上下文菜单中单击“选择 IoT 中心”。

3. 弹出窗口将显示在右下角，以便让你首次登录 Azure。

4. 登录后，将显示 Azure 订阅列表，然后选择“Azure 订阅和 IoT 中心”。

5. 几秒钟后，设备列表将显示在“Azure IoT 中心设备”选项卡中。

   > [!Note]
   > 也可以选择“设置 IoT 中心连接字符串”来完成设置。 在弹出窗口中输入 IoT 设备连接到的 IoT 中心的连接字符串。

## <a name="direct-methods"></a>直接方法

1. 右键单击设备并选择“调用直接方法”。 

2. 在输入框中输入方法名称和有效负载。

3. 结果将显示在“输出” > “Azure IoT 中心工具包”视图中。

## <a name="read-device-twin"></a>读取设备孪生

1. 右键单击设备并选择“编辑设备孪生”。 

2. 将打开一个 azure-iot-device-twin.json 文件，其中包含设备孪生的内容。

## <a name="update-device-twin"></a>更新设备孪生

1. 编辑“标记”或 properties.desired 字段。

2. 右键单击 azure-iot-device-twin.json 文件。

3. 选择“更新设备孪生”以更新设备孪生。

## <a name="send-cloud-to-device-messages"></a>发送“云到设备”消息

要将消息从 IoT 中心发送到设备，请执行以下步骤：
 
1. 右键单击设备，然后选择“将 C2D 消息发送到设备”。 

2. 在输入框中输入消息。

3. 结果将显示在“输出” > “Azure IoT 中心工具包”视图中。

## <a name="next-steps"></a>后续步骤

你已学习了如何通过各种管理选项使用适用于 Visual Studio Code 的 Azure IoT 工具扩展。

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
