---
title: 使用适用于 Visual Studio 的 Cloud Explorer 管理 Azure IoT 中心云设备消息传送 | Microsoft Docs
description: 了解如何在 Azure IoT 中心内使用适用于 Visual Studio 的 Cloud Explorer 监视设备到云的消息以及发送云到设备的消息。
author: shizn
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: xshi
ms.openlocfilehash: b8ea705b55c1485cab2e1478d2d455f1d4a427d3
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2019
ms.locfileid: "70050253"
---
# <a name="use-cloud-explorer-for-visual-studio-to-send-and-receive-messages-between-your-device-and-iot-hub"></a>使用适用于 Visual Studio 的 Cloud Explorer 在设备与 IoT 中心之间发送和接收消息

![端到端关系图](./media/iot-hub-visual-studio-cloud-device-messaging/e-to-e-diagram.png)

[Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS) 是一种有用的 Visual Studio 扩展，可用于在 Visual Studio 中查看 Azure 资源，检查这些资源的属性，以及执行重要的开发人员操作。 本文重点介绍如何使用 Cloud Explorer 在设备和中心之间发送和接收消息。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="what-you-learn"></a>学习内容

本文介绍如何使用 Visual Studio Cloud Explorer 来监视设备到云的消息以及发送云到设备的消息。 设备到云的消息可能是由设备收集然后发送到 IoT 中心的传感器数据。 云到设备的消息可能是由 IoT 中心发送到设备的命令。 例如，使连接到设备的 LED 闪烁。

## <a name="what-you-do"></a>准备工作

本文将执行以下任务:

- 使用适用于 Visual Studio 的 Cloud Explorer 监视设备到云的消息。

- 使用适用于 Visual Studio 的 Cloud Explorer 发送云到设备的消息。

## <a name="what-you-need"></a>所需条件

需要以下先决条件:

- 一个有效的 Azure 订阅。

- 你的订阅下有一个 Azure IoT 中心。

- Microsoft Visual Studio 2017 Update 9 或更高版本。 本文使用[Visual Studio 2019](https://www.visualstudio.com/vs/)。

- Visual Studio 安装程序中的 Cloud Explorer 组件, 默认情况下, 它是使用 Azure 工作负荷选中的。

## <a name="update-cloud-explorer-to-latest-version"></a>将 Cloud Explorer 更新到最新版本

Visual Studio 2017 Visual Studio 安装程序中的 Cloud Explorer 组件仅支持监视设备到云和云到设备的消息。 若要使用 Visual Studio 2017, 请下载并安装最新的[Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS)。

## <a name="sign-in-to-access-your-hub"></a>登录以访问中心

若要访问中心, 请执行以下步骤:

1. 在 Visual Studio 中, 选择 "**查看** > **Cloud Explorer** " 打开 Cloud Explorer。

1. 选择 "帐户管理" 图标以显示你的订阅。

    !["帐户管理" 图标](media/iot-hub-visual-studio-cloud-device-messaging/account-management-icon.png)

1. 如果已登录到 Azure, 则会显示帐户。 若要首次登录到 Azure, 请选择 "**添加帐户**"。

1. 选择要使用的 Azure 订阅, 并选择 "**应用**"。

1. 展开你的订阅, 然后展开**IoT 中心**。  在每个中心下, 你都可以看到该中心的设备。

    ![设备列表](media/iot-hub-visual-studio-cloud-device-messaging/hub-device-list.png)

## <a name="monitor-device-to-cloud-messages"></a>监视设备到云的消息

若要监视从设备发送到 IoT 中心的消息，请按照以下步骤操作：

1. 右键单击 IoT 中心或设备，然后选择“开始监视 D2C 消息”。

    ![开始监视 D2C 消息](media/iot-hub-visual-studio-cloud-device-messaging/start-monitoring-d2c-message-vs2019.png)

1. 监视的消息显示在 "**输出**" 下。

    ![监视 D2C 消息结果](media/iot-hub-visual-studio-cloud-device-messaging/monitor-d2c-message-result-vs2019.png)

1. 若要停止监视，请右键单击任何 IoT 中心或设备，然后选择“停止监视 D2C 消息”。

## <a name="send-cloud-to-device-messages"></a>发送“云到设备”消息

若要将消息从 IoT 中心发送到设备，请按照以下步骤操作：

1. 右键单击设备，然后选择“发送 C2D 消息”。

1. 在输入框中输入消息。

    ![发送 C2D 消息](media/iot-hub-visual-studio-cloud-device-messaging/send-c2d-message-test.png)

    结果显示在 "**输出**" 下。

    ![发送 C2D 消息结果](media/iot-hub-visual-studio-cloud-device-messaging/send-c2d-message-result-vs2019.png)

## <a name="next-steps"></a>后续步骤

现在，已了解如何监视设备到云的消息，以及在 IoT 设备与 Azure IoT 中心之间发送云到设备的消息。

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]