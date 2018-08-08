---
title: 使用用于 VS Code 的 Azure IoT 工具包创建 Azure IoT 中心| Microsoft Docs
description: 如何使用用于 VS Code 的 Azure IoT 工具包创建 IoT 中心。
author: formulahendry
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: junhan
ms.openlocfilehash: 71b188443ee48d742b14753cd5526edac493d9e3
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2018
ms.locfileid: "39368615"
---
# <a name="create-an-iot-hub-using-the-azure-iot-toolkit-for-visual-studio-code"></a>使用用于 Visual Studio Code 的 Azure IoT 工具包创建 IoT 中心

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

可使用[用于 Visual Studio Code 的 Azure IoT 工具包](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit)创建 Azure IoT 中心。 本文介绍如何使用 Azure IoT 工具包创建 IoT 中心。

要完成本文，需要以下各项：

* 有效的 Azure 帐户。
- [Visual Studio Code](https://code.visualstudio.com/)
- [Azure IoT Toolkit](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit)

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

现已使用用于 Visual Studio Code 的 Azure IoT 工具包部署 IoT 中心，可进行更深入的探索：

* [使用用于 Visual Studio Code 的 Azure IoT 工具包扩展在设备和 IoT 中心之间发送和接收消息](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md)。
* Azure IoT 工具包的 [Wiki 页面](https://github.com/microsoft/vscode-azure-iot-toolkit/wiki)。
