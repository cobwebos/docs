---
title: 使用适用于 Visual Studio Code 的 Azure IoT 工具管理 Azure IoT 中心云设备消息传送 | Microsoft Docs
description: 了解如何使用适用于 Visual Studio Code 的 Azure IoT 工具来监视设备到云消息，并在 Azure IoT 中心中发送云到设备消息。
author: formulahendry
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 01/18/2019
ms.author: junhan
ms.openlocfilehash: 0455dafaa39f9f6bc19399fab31663d6b2e06e95
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/23/2019
ms.locfileid: "56726340"
---
# <a name="use-azure-iot-tools-for-visual-studio-code-to-send-and-receive-messages-between-your-device-and-iot-hub"></a>使用适用于 Visual Studio Code 的 Azure IoT 工具在设备和 IoT 中心之间发送和接收消息

![端到端关系图](media/iot-hub-get-started-e2e-diagram/2.png)

[Azure IoT 工具](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit)是一个有用的 Visual Studio Code 扩展，可以使 IoT 中心管理和 IoT 应用程序开发更加容易。 本文重点介绍了如何使用适用于 Visual Studio Code 的 Azure IoT 工具在设备和 IoT 中心之间发送和接收消息。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="what-you-will-learn"></a>要学习的知识

了解如何使用适用于 Visual Studio Code 的 Azure IoT 工具来监视设备到云消息，并发送云到设备消息。 设备到云的消息可能是设备收集的，随后要发送到 IoT 中心的传感器数据。 云到设备的消息可能是 IoT 中心发送到设备的，用于闪烁连接到设备的 LED 的命令。

## <a name="what-you-will-do"></a>执行的操作

- 使用适用于 Visual Studio Code 的 Azure IoT 工具来监视设备到云消息。
- 使用适用于 Visual Studio Code 的 Azure IoT 工具来发送云到设备消息。

## <a name="what-you-need"></a>所需条件

- 一个有效的 Azure 订阅。
- 已在订阅中创建一个 Azure IoT 中心。
- [Visual Studio Code](https://code.visualstudio.com/)
- [适用于 VS Code 的 Azure IoT 工具](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)

## <a name="sign-in-to-access-your-iot-hub"></a>登录以访问 IoT 中心

1. 在 VS Code 的“资源管理器”视图中，展开左下角的“Azure IoT 中心设备”部分。
1. 在上下文菜单中单击“选择 IoT 中心”。
1. 弹出窗口将显示在右下角，以便让你首次登录 Azure。
1. 登录后，将显示 Azure 订阅列表，然后选择“Azure 订阅和 IoT 中心”。
1. 几秒钟后，设备列表将显示在“Azure IoT 中心设备”选项卡中。

   > [!Note]
   > 也可以选择“设置 IoT 中心连接字符串”来完成设置。 在弹出窗口中输入 IoT 设备连接到的 IoT 中心的连接字符串。
   
## <a name="monitor-device-to-cloud-messages"></a>监视设备到云的消息

若要监视设备发送到 IoT 中心的消息，请执行以下步骤：

1. 右键单击设备，然后选择“开始监视 D2C 消息”。
1. 受监视的消息将显示在“输出” > “Azure IoT 中心工具包”视图中。
1. 若要停止监视，请右键单击“输出”视图，然后选择“停止监视 D2C 消息”。

## <a name="send-cloud-to-device-messages"></a>发送“云到设备”消息

要将消息从 IoT 中心发送到设备，请执行以下步骤：

1. 右键单击设备，然后选择“将 C2D 消息发送到设备”。 
1. 在输入框中输入消息。
1. 结果将显示在“输出” > “Azure IoT 中心工具包”视图中。

## <a name="next-steps"></a>后续步骤

现在，已了解如何监视设备到云的消息，以及在 IoT 设备与 Azure IoT 中心之间发送云到设备的消息。

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
