---
title: 使用适用于 Visual Studio 的 Cloud Explorer 管理 Azure IoT 设备 | Microsoft Docs
description: 使用适用于 Visual Studio 的 Cloud Explorer 进行 Azure IoT 中心设备管理，其中包含直接方法和孪生所需的属性管理选项。
author: shizn
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: xshi
ms.openlocfilehash: 87a0847f5d42e014f3b2691c96446892176b481b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60399506"
---
# <a name="use-cloud-explorer-for-visual-studio-for-azure-iot-hub-device-management"></a>使用适用于 Visual Studio 的 Cloud Explorer 管理 Azure IoT 中心设备

![端到端关系图](media/iot-hub-device-management-visual-studio/iot-e2e-simple.png)

[Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS) 是一种有用的 Visual Studio 扩展，可用于在 Visual Studio 中查看 Azure 资源，检查这些资源的属性，以及执行重要的开发人员操作。 它附带了可用于执行各种任务的管理选项。

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

了解如何使用用于 Visual Studio 的云资源管理器中，使用在开发计算机上的各种管理选项。

## <a name="what-you-do"></a>准备工作

通过各种管理选项运行适用于 Visual Studio 的 Cloud Explorer。

## <a name="what-you-need"></a>所需条件

- 一个有效的 Azure 订阅
- 你的订阅在一个 Azure IoT 中心
- Microsoft Visual Studio 2017 Update 8 或更高版本
- 来自 Visual Studio 安装程序的 Cloud Explorer 组件（默认情况下随 Azure 工作负荷一同选中）

## <a name="update-cloud-explorer-to-latest-version"></a>将 Cloud Explorer 更新到最新版本

来自 Visual Studio 安装程序的 Cloud Explorer 组件仅支持监视设备到云的消息以及云到设备的消息。 需要下载并安装最新的 [Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS) 才能访问管理选项。

## <a name="sign-in-to-access-your-iot-hub"></a>登录以访问 IoT 中心

1. 在 Visual Studio 的“Cloud Explorer”窗口中，单击“帐户管理”图标。 Cloud Explorer 窗口可从“视图” > “Cloud Explorer”菜单中打开。

    ![单击“帐户管理”](media/iot-hub-visual-studio-cloud-device-messaging/click-account-management.png)

1. 在 Cloud Explorer 中单击“管理帐户”。
1. 在新窗口中单击“添加帐户...”，以首次登录到 Azure。
1. 在你登录后，系统将显示 Azure 订阅列表。 选择要查看的 Azure 订阅，然后单击“应用”。
1. 依次展开“你的订阅” > “IoT 中心” > “你的 IoT 中心”，设备列表将显示在你的 IoT 中心节点下。 右键单击一个设备以访问管理选项。

    ![管理选项](media/iot-hub-device-management-visual-studio/management-options.png)

## <a name="direct-methods"></a>直接方法

1. 右键单击设备并选择“调用设备直接方法”。
1. 在输入框中输入方法名称和有效负载。
1. 结果将显示在“IoT 中心”输出窗格中。

## <a name="read-device-twin"></a>读取设备孪生

1. 右键单击设备并选择“编辑设备孪生”。
1. 将打开一个 azure-iot-device-twin.json 文件，其中包含设备孪生的内容。

## <a name="update-device-twin"></a>更新设备孪生

1. 对 **azure-iot-device-twin.json** 文件中的**标记**或 **properties.desired** 字段进行一些编辑。
1. 按 **Ctrl + S** 来更新设备孪生。
1. 结果将显示在“IoT 中心”输出窗格中。

## <a name="send-cloud-to-device-messages"></a>发送“云到设备”消息

若要将消息从 IoT 中心发送到设备，请按照以下步骤操作：

1. 右键单击设备，然后选择“发送 C2D 消息”。
1. 在输入框中输入消息。
1. 结果将显示在“IoT 中心”输出窗格中。

## <a name="next-steps"></a>后续步骤

你已学习了如何通过各种管理选项运行适用于 Visual Studio 的 Cloud Explorer。

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]