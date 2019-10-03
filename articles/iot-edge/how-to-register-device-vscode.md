---
title: 通过 Visual Studio Code 注册新设备 - Azure IoT Edge | Microsoft Docs
description: 使用 Visual Studio Code 在 Azure IoT 中心中创建新的 IoT Edge 设备并检索连接字符串
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/03/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: c8fce104d48acc3a562599c65eb15cb0a66657b7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66495275"
---
# <a name="register-a-new-azure-iot-edge-device-from-visual-studio-code"></a>通过 Visual Studio Code 注册新 Azure IoT Edge 设备

在 Azure IoT Edge 中使用 IoT 设备之前，需要在 IoT 中心中注册这些设备。 后注册的设备，你将收到可用于设置设备的 IoT Edge 工作负荷的连接字符串。

本文介绍如何使用 Visual Studio Code (VS Code) 注册新 IoT Edge 设备。 有多种方法可以执行 VS Code 中的大部分操作。 本文使用资源管理器，但也可以使用命令面板中运行的步骤。

## <a name="prerequisites"></a>必备组件

* Azure 订阅中的 [IoT 中心](../iot-hub/iot-hub-create-through-portal.md)
* [Visual Studio Code](https://code.visualstudio.com/)
* 适用于 Visual Studio Code 的 [Azure IoT 工具](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)

## <a name="sign-in-to-access-your-iot-hub"></a>登录以访问 IoT 中心

可使用适用于 Visual Studio Code 的 Azure IoT 扩展来执行与 IoT 中心相关的操作。 若要运行这些操作，需要登录到 Azure 帐户并选择 IoT 中心。

1. 在 Visual Studio Code 中打开“资源管理器”视图  。

1. 在资源管理器的底部，依次展开**Azure IoT 中心**部分。

   ![展开“Azure IoT 中心设备”部分](./media/how-to-register-device-vscode/azure-iot-hub-devices.png)

1. 单击 **...** 中**Azure IoT 中心**部分标头。 如果没有看到省略号，请单击标题或将鼠标指针悬停在标题上。

1. 选择“选择 IoT 中心”  。

1. 如果您没有登录到 Azure 帐户，请按以下提示来执行此操作。

1. 选择 Azure 订阅。

1. 选择 IoT 中心。

## <a name="create-a-device"></a>创建设备

1. 在 VS Code 资源管理器中，展开“Azure IoT 中心设备”部分  。

1. 单击“Azure IoT 中心设备”部分标题中的“...”   。 如果没有看到省略号，请单击标题或将鼠标指针悬停在标题上。

1. 选择“创建 IoT Edge 设备”  。

1. 在打开的文本框中提供设备 ID。

在输出屏幕中，可以看到命令的结果。 其中显示有设备信息，包括所提供的“deviceId”以及可用于将物理设备连接到 IoT 中心的“connectionString”   。

## <a name="view-all-devices"></a>查看所有设备

连接到 IoT 中心的所有设备都所示**Azure IoT 中心**部分中的 Visual Studio Code 资源管理器。 IoT Edge 设备进行区分使用不同的图标和这一事实的非边缘设备的 **$edgeAgent**并 **$edgeHub**模块部署到每个 IoT Edge 设备。

   ![查看 IoT 中心中所有的 IoT Edge 设备](./media/how-to-register-device-vscode/view-devices.png)

## <a name="retrieve-the-connection-string"></a>检索连接字符串

如果已准备好设置设备，则需要连接字符串，该字符串使用物理设备在 IoT 中心内的标识链接该设备。

1. 右键单击你的设备中的 ID **Azure IoT 中心**部分。

1. 选择“复制设备连接字符串”  。

   连接字符串会复制到剪贴板。

还可从右键菜单中选择“获取设备信息”，在输出窗口中查看包括连接字符串在内的所有设备信息  。

## <a name="next-steps"></a>后续步骤

了解如何[使用 Visual Studio Code 将模块部署到设备](how-to-deploy-modules-vscode.md)。
