---
title: 使用适用于 VS Code 的 Azure IoT 工具创建 Azure IoT 中心| Microsoft Docs
description: 如何使用适用于 VS Code 的 Azure IoT 工具创建 IoT 中心。
author: formulahendry
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: junhan
ms.openlocfilehash: 9138a709cf8a166bbb572e04b082c5b8e6c82949
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2019
ms.locfileid: "54050228"
---
# <a name="create-an-iot-hub-using-the-azure-iot-tools-for-visual-studio-code"></a>使用适用于 Visual Studio Code 的 Azure IoT 工具创建 IoT 中心

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

本文介绍了如何使用[适用于 Visual Studio Code 的 Azure IoT 工具](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)创建 Azure IoT 中心。 

若要完成本文，需要以下各项：

- Azure 订阅。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

- [Visual Studio Code](https://code.visualstudio.com/)

- 适用于 Visual Studio Code 的 [Azure IoT 工具](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)。

## <a name="create-an-iot-hub"></a>创建 IoT 中心

1. 在 Visual Studio Code 中打开“资源管理器”视图。

2. 在资源管理器底部，展开“Azure IoT 中心设备”部分。 

   ![展开 Azure IoT 中心设备](./media/iot-hub-create-use-iot-toolkit/azure-iot-hub-devices.png)

3. 单击“Azure IoT 中心设备”部分标题中的“...”。 如果没有看到省略号，请将鼠标悬停在标题处。 

4. 选择“创建 IoT 中心”。

5. 弹出窗口将显示在右下角，以便让你首次登录 Azure。

6. 选择 Azure 订阅。 

7. 选择资源组。

8. 选择位置。

9. 选择定价层。

10. 为 IoT 中心输入一个全局唯一名称。

11. 稍等几分钟，待 IoT 中心创建完毕。

## <a name="next-steps"></a>后续步骤

现在，你已使用适用于 Visual Studio Code 的 Azure IoT 工具部署了 IoT 中心。 若要进一步探索，请查看以下文章：

* [使用适用于 Visual Studio Code 的 Azure IoT 工具在设备和 IoT 中心之间发送和接收消息](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md)。

* [使用适用于 Visual Studio Code 的 Azure IoT 工具进行 Azure IoT 中心设备管理](iot-hub-device-management-iot-toolkit.md)

* [请参阅 Azure IoT 中心工具包 Wiki 页](https://github.com/microsoft/vscode-azure-iot-toolkit/wiki)。
