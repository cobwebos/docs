---
title: 带有 Visual Studio Cloud Explorer 的 Azure IoT 设备管理
description: 使用适用于 Visual Studio 的 Cloud Explorer 进行 Azure IoT 中心设备管理，其中包含直接方法和孪生所需的属性管理选项。
author: shizn
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: xshi
ms.openlocfilehash: 6fe5a45dda6632c56b3c6714827950e25e7d26af
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2019
ms.locfileid: "73953185"
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

设备孪生是存储设备状态信息（包括元数据、配置和条件）的 JSON 文档。 IoT 中心为连接到它的每台设备保留一个设备孪生。 有关设备孪生的详细信息，请参阅[设备孪生入门](iot-hub-node-node-twin-getstarted.md)。

## <a name="what-you-learn"></a>学习内容

本文介绍如何在开发计算机上通过各种管理选项使用适用于 Visual Studio 的 Cloud Explorer。

## <a name="what-you-do"></a>准备工作

在本文中，将通过各种管理选项运行适用于 Visual Studio 的 Cloud Explorer。

## <a name="what-you-need"></a>需要什么

需要具备以下先决条件：

- 一个有效的 Azure 订阅。

- 你的订阅下有一个 Azure IoT 中心。

- Microsoft Visual Studio 2017 Update 9 或更高版本。 本文使用 [Visual Studio 2017 或 Visual Studio 2019](https://www.visualstudio.com/vs/)。

- 来自 Visual Studio 安装程序的 Cloud Explorer 组件（默认情况下与 Azure 工作负荷一同选中）。

## <a name="update-cloud-explorer-to-latest-version"></a>将 Cloud Explorer 更新到最新版本

来自 Visual Studio 2017 安装程序的 Cloud Explorer 组件仅支持监视设备到云的消息以及云到设备的消息。 若要使用 Visual Studio 2017，请下载并安装最新的 [Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS)。

## <a name="sign-in-to-access-your-hub"></a>登录以访问中心

1. 在 Visual Studio 中，选择“查看” > “Cloud Explorer”以打开 Cloud Explorer。

1. 选择“帐户管理”图标以显示你的订阅。

    ![“帐户管理”图标](media/iot-hub-visual-studio-cloud-device-messaging/account-management-icon.png)

1. 如果已登录到 Azure，则会显示帐户。 若要首次登录 Azure，请选择“添加帐户”。

1. 选择要使用的 Azure 订阅并选择“应用”。

1. 展开订阅，然后展开“IoT 中心”。  在每个中心下，都可以看到该中心的设备。 右键单击一个设备以访问管理选项。

    ![管理选项](media/iot-hub-device-management-visual-studio/management-options-vs2019.png)

## <a name="direct-methods"></a>直接方法

若要使用直接方法，请执行以下步骤：

1. 右键单击设备并选择“调用设备直接方法”。

1. 在**调用直接方法**中输入方法名称和有效负载，然后选择“确定”。

    结果将显示在**输出**中。

## <a name="update-device-twin"></a>更新设备孪生

若要编辑设备孪生，请执行以下步骤：

1. 右键单击设备并选择“编辑设备孪生”。

   将打开一个 **azure-iot-device-twin.json** 文件，其中包含设备孪生的内容。

1. 对 **azure-iot-device-twin.json** 文件中的**标记**或 **properties.desired** 字段进行一些编辑。

1. 按 **Ctrl + S** 来更新设备孪生。

   结果将显示在**输出**中。

## <a name="send-cloud-to-device-messages"></a>发送“云到设备”消息

若要将消息从 IoT 中心发送到设备，请按照以下步骤操作：

1. 右键单击设备，然后选择“发送 C2D 消息”。

1. 在**发送 C2D 消息**中输入消息，然后选择“确定”。

   结果将显示在**输出**中。

## <a name="next-steps"></a>后续步骤

你已学习了如何通过各种管理选项运行适用于 Visual Studio 的 Cloud Explorer。

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
