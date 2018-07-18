---
title: 注册新 Azure IoT Edge 设备 (VS Code) | Microsoft Docs
description: 使用 Visual Studio Code 在 Azure IoT 中心中创建新的 IoT Edge 设备
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/14/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 7902461f58df1b4fe0c3ed3b577f668fe8be4cc2
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2018
ms.locfileid: "37034381"
---
# <a name="register-a-new-azure-iot-edge-device-from-visual-studio-code"></a>通过 Visual Studio Code 注册新 Azure IoT Edge 设备

若要在 IoT 设备上使用 Azure IoT Edge，首先需要在 IoT 中心注册这些设备。 注册设备后会收到一个连接字符串，该字符串可用于设置设备的 Edge 工作负载。 

本文介绍如何使用 Visual Studio Code (VS Code) 注册新 IoT Edge 设备。 有多种方法可以执行 VS Code 中的大部分操作。 本文采用的是资源管理器，但也可使用命令面板来执行大部分步骤。 

## <a name="prerequisites"></a>先决条件

* Azure 订阅中的 [IoT 中心](../iot-hub/iot-hub-create-through-portal.md)
* [Visual Studio Code](https://code.visualstudio.com/) 
* 适用于 Visual Studio Code 的 [Azure IoT Edge 扩展](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)

## <a name="sign-in-to-access-your-iot-hub"></a>登录以访问 IoT 中心

可使用适用于 Visual Studio Code 的 Azure IoT 扩展来执行与 IoT 中心相关的操作。 为让操作顺利进行，需登录 Azure 帐户并选择要使用的 IoT 中心。

1. 在 Visual Studio Code 中打开“资源管理器”视图。

2. 在资源管理器底部，展开“Azure IoT 中心设备”部分。 

   ![展开 Azure IoT 中心设备](./media/how-to-register-device-vscode/azure-iot-hub-devices.png)

3. 单击“Azure IoT 中心设备”部分标题中的“...”。 如果没有看到省略号，请将鼠标悬停在标题处。 

4. 选择“选择 IoT 中心”。

5. 如果尚未登录 Azure 帐户，请按照提示登录。 

6. 选择 Azure 订阅。 

7. 选择 IoT 中心。 

## <a name="create-a-device"></a>创建设备

1. 在 VS Code 资源管理器中，展开“Azure IoT 中心设备”部分。 

2. 单击“Azure IoT 中心设备”部分标题中的“...”。 如果没有看到省略号，请将鼠标悬停在标题处。 

3. 选择“创建 IoT Edge 设备”。 

4. 在打开的文本框中提供设备 ID。 

在输出屏幕中，可以看到命令的结果。 其中显示有设备信息，包括所提供的“deviceId”以及可用于将物理设备连接到 IoT 中心的“connectionString”。 

## <a name="view-all-devices"></a>查看所有设备

Visual Studio Code 资源管理器的“Azure IoT 中心设备”部分列出了连接到 IoT 中心的所有设备。 可通过不同的图标区分 IoT Edge 设备和非 Edge 设备，并且可展开设备以显示部署至各设备的模块。 

   ![在 VS Code 中查看设备](./media/how-to-register-device-vscode/view-devices.png)

## <a name="retrieve-the-connection-string"></a>检索连接字符串

如果已准备好设置设备，则需连接字符串，该字符串使用 IoT 中心内物理设备的标识链接设备。

1. 右键单击“Azure IoT 中心设备”部分中的设备 ID。 
2. 选择“复制设备连接字符串”。

   连接字符串会复制到剪贴板。 

还可从右键菜单中选择“获取设备信息”，在输出窗口中查看包括连接字符串在内的所有设备信息。 


## <a name="next-steps"></a>后续步骤

了解如何[使用 Visual Studio Code 将模块部署到设备](how-to-deploy-modules-vscode.md)。
