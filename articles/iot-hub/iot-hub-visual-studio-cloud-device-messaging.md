---
title: 使用适用于 Visual Studio 的 Cloud Explorer 管理 Azure IoT 中心云设备消息传送 | Microsoft Docs
description: 了解如何在 Azure IoT 中心内使用适用于 Visual Studio 的 Cloud Explorer 监视设备到云的消息以及发送云到设备的消息。
author: shizn
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/07/2018
ms.author: xshi
ms.openlocfilehash: ab3c02d7207bca70a90df8aa08c73c1484cd635d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "61440579"
---
# <a name="use-cloud-explorer-for-visual-studio-to-send-and-receive-messages-between-your-device-and-iot-hub"></a>使用适用于 Visual Studio 的 Cloud Explorer 在设备与 IoT 中心之间发送和接收消息

![端到端关系图](./media/iot-hub-visual-studio-cloud-device-messaging/e-to-e-diagram.png)

[Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS) 是一种有用的 Visual Studio 扩展，可用于在 Visual Studio 中查看 Azure 资源，检查这些资源的属性，以及执行重要的开发人员操作。 本文将重点介绍如何使用 Cloud Explorer 在设备与 IoT 中心之间发送和接收消息。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="what-you-will-learn"></a>要学习的知识

你将了解如何使用适用于 Visual Studio 的 Cloud Explorer 监视设备到云的消息，以及发送云到设备的消息。 设备到云的消息可能是由设备收集然后发送到 IoT 中心的传感器数据。 云到设备的消息可能是由 IoT 中心发送到设备的命令。 例如，使连接到设备的 LED 闪烁。

## <a name="what-you-will-do"></a>执行的操作

- 使用适用于 Visual Studio 的 Cloud Explorer 监视设备到云的消息。
- 使用适用于 Visual Studio 的 Cloud Explorer 发送云到设备的消息。

## <a name="what-you-need"></a>所需条件

- 一个有效的 Azure 订阅。
- 你的订阅下有一个 Azure IoT 中心。
- Microsoft Visual Studio 2017 Update 8 或更高版本
- 来自 Visual Studio 安装程序的 Cloud Explorer 组件（默认情况下随 Azure 工作负荷一同选中）

## <a name="update-cloud-explorer-to-latest-version"></a>将 Cloud Explorer 更新到最新版本

来自 Visual Studio 安装程序的 Cloud Explorer 组件仅支持监视设备到云的消息以及云到设备的消息。 为了将消息发送到设备或云，需要下载并安装最新的 [Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS)。

## <a name="sign-in-to-access-your-iot-hub"></a>登录以访问 IoT 中心

1. 在 Visual Studio 的“Cloud Explorer”  窗口中，单击“帐户管理”图标。 Cloud Explorer 窗口可从“视图”   > “Cloud Explorer”  菜单中打开。

    ![单击“帐户管理”](media/iot-hub-visual-studio-cloud-device-messaging/click-account-management.png)


2. 在 Cloud Explorer 中单击“管理帐户”  。

3. 在新窗口中单击“添加帐户...”  ，以首次登录到 Azure。

4. 在你登录后，系统将显示 Azure 订阅列表。 选择要查看的 Azure 订阅，然后单击“应用”  。

5. 依次展开“你的订阅”   > “IoT 中心”   > “你的 IoT 中心”  ，设备列表将显示在你的 IoT 中心节点下。

    ![设备列表](media/iot-hub-visual-studio-cloud-device-messaging/device-list.png)

## <a name="monitor-device-to-cloud-messages"></a>监视设备到云的消息

若要监视从设备发送到 IoT 中心的消息，请按照以下步骤操作：

1. 右键单击 IoT 中心或设备，然后选择“开始监视 D2C 消息”  。

    ![开始监视 D2C 消息](media/iot-hub-visual-studio-cloud-device-messaging/start-monitoring-d2c-message.png)

2. 受监视的消息将显示在“IoT 中心”  输出窗格中。

    ![监视 D2C 消息结果](media/iot-hub-visual-studio-cloud-device-messaging/monitor-d2c-message-result.png)

3. 若要停止监视，请右键单击任何 IoT 中心或设备，然后选择“停止监视 D2C 消息”  。

## <a name="send-cloud-to-device-messages"></a>发送“云到设备”消息

若要将消息从 IoT 中心发送到设备，请按照以下步骤操作：

1. 右键单击设备，然后选择“发送 C2D 消息”  。

    ![发送 C2D 消息](media/iot-hub-visual-studio-cloud-device-messaging/send-c2d-message.png)

2. 在输入框中输入消息。

3. 结果将显示在“IoT 中心”  输出窗格中。

    ![发送 C2D 消息结果](media/iot-hub-visual-studio-cloud-device-messaging/send-c2d-message-result.png)

## <a name="next-steps"></a>后续步骤

现在，已了解如何监视设备到云的消息，以及在 IoT 设备与 Azure IoT 中心之间发送云到设备的消息。

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]