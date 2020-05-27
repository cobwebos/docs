---
title: 注册新 Azure IoT Edge 设备 | Microsoft Docs
description: 使用适用于 Azure CLI 的 IoT 扩展注册新的 IoT Edge 设备并检索连接字符串
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/08/2020
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 2ac6a38d99f588c9dbab7a40bc092aa01b27649a
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2020
ms.locfileid: "83726003"
---
# <a name="register-an-azure-iot-edge-device"></a>注册 Azure IoT Edge 设备

在 Azure IoT Edge 中使用 IoT 设备之前，必须在 IoT 中心中注册这些设备。 在注册设备后，即可检索连接字符串，以便针对 IoT Edge 工作负荷设置设备。

使用以下工具之一即可选择注册：

* 如果你希望使用图形用户界面来创建、查看和管理 Azure 资源，请[在 Azure 门户中注册设备](#register-in-the-azure-portal)。
* 如果你希望在开发 IoT 解决方案的同一位置管理 Azure IoT 资源，请[使用 Visual Studio Code 注册设备](#register-with-visual-studio-code)。
* 如果你希望使用命令行工具来管理 Azure 资源或想要自动执行任务，请[使用 Azure CLI 注册设备](#register-with-the-azure-cli)。

## <a name="register-in-the-azure-portal"></a>在 Azure 门户中注册

可在 Azure 门户中执行所有注册任务。

### <a name="prerequisites-for-the-azure-portal"></a>Azure 门户的必备组件

Azure 订阅中的免费或标准 [IoT 中心](../iot-hub/iot-hub-create-through-portal.md)。

### <a name="create-an-iot-edge-device-in-the-azure-portal"></a>在 Azure 门户中创建 IoT Edge 设备

在 Azure 门户的 IoT 中心，IoT Edge 设备的创建和管理独立于不支持 Edge 的 IoT 设备。

1. 登录 [Azure 门户](https://portal.azure.com)，导航到 IoT 中心。
2. 在左窗格的菜单中，选择“IoT Edge”  。
3. 选择“添加 IoT Edge 设备”  。
4. 提供一个描述性的设备 ID。 使用默认设置自动生成身份验证密钥并将新设备连接到中心。
5. 选择“保存”。 

### <a name="view-iot-edge-devices-in-the-azure-portal"></a>在 Azure 门户中查看 IoT Edge 设备

所有连接到 IoT 中心并已启用 Edge 的设备都列在 **IoT Edge** 页上。

![查看 IoT 中心中所有的 IoT Edge 设备](./media/how-to-register-device/portal-view-devices.png)

### <a name="retrieve-the-connection-string-in-the-azure-portal"></a>在 Azure 门户中检索连接字符串

如果已准备好设置设备，则需要连接字符串，该字符串使用物理设备在 IoT 中心内的标识链接该设备。

1. 在门户的 **IoT Edge** 页中，单击 IoT Edge 设备列表中的设备 ID。
2. 复制“主连接字符串”或“辅助连接字符串”的值   。

## <a name="register-with-visual-studio-code"></a>注册到 Visual Studio Code

有多种方法可以执行 VS Code 中的大部分操作。 本文使用了资源管理器，但你也可使用命令面板来执行相关步骤。

### <a name="prerequisites-for-visual-studio-code"></a>Visual Studio Code 的必备组件

* Azure 订阅中的 [IoT 中心](../iot-hub/iot-hub-create-through-portal.md)
* [Visual Studio Code](https://code.visualstudio.com/)
* 适用于 Visual Studio Code 的 [Azure IoT 工具](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)

### <a name="sign-in-to-access-your-iot-hub"></a>登录以访问 IoT 中心

可使用适用于 Visual Studio Code 的 Azure IoT 扩展来执行与 IoT 中心相关的操作。 为了使这些操作顺利执行，你需要登录到 Azure 帐户并选择 IoT 中心。

1. 在 Visual Studio Code 中打开“资源管理器”视图  。
1. 在资源管理器底部，展开“Azure IoT 中心”部分  。

   ![展开“Azure IoT 中心设备”部分](./media/how-to-register-device/azure-iot-hub-devices.png)

1. 单击“Azure IoT 中心”部分标题中的“...”   。 如果没有看到省略号，请单击标题或将鼠标指针悬停在标题上。
1. 选择“选择 IoT 中心”  。
1. 如果尚未登录到 Azure 帐户，请按照相关提示执行此操作。
1. 选择 Azure 订阅。
1. 选择 IoT 中心。

### <a name="create-an-iot-edge-device-with-visual-studio-code"></a>使用 Visual Studio Code 创建 IoT Edge 设备

1. 在 VS Code 资源管理器中，展开“Azure IoT 中心设备”部分  。
1. 单击“Azure IoT 中心设备”部分标题中的“...”   。 如果没有看到省略号，请单击标题或将鼠标指针悬停在标题上。
1. 选择“创建 IoT Edge 设备”  。
1. 在打开的文本框中提供设备 ID。

在输出屏幕中，可以看到命令的结果。 其中显示有设备信息，包括所提供的“deviceId”以及可用于将物理设备连接到 IoT 中心的“connectionString”   。

在输出屏幕中，可以看到命令的结果。 其中显示有设备信息，包括所提供的“deviceId”以及可用于将物理设备连接到 IoT 中心的“connectionString”   。

### <a name="view-iot-edge-devices-with-visual-studio-code"></a>使用 Visual Studio Code 查看 IoT Edge 设备

Visual Studio Code 资源管理器的“Azure IoT 中心”部分中列出了连接到 IoT 中心的所有设备  。 IoT Edge 设备和非 Edge 设备可通过不同的图标加以区别。事实上， **$edgeAgent** 和 **$edgeHub** 模块会部署到每个 IoT Edge 设备。

![查看 IoT 中心中所有的 IoT Edge 设备](./media/how-to-register-device/view-devices.png)

### <a name="retrieve-the-connection-string-with-visual-studio-code"></a>通过 Visual Studio Code 检索连接字符串

如果已准备好设置设备，则需要连接字符串，该字符串使用物理设备在 IoT 中心内的标识链接该设备。

1. 右键单击“Azure IoT 中心”部分中的设备 ID  。
1. 选择“复制设备连接字符串”  。

   连接字符串会复制到剪贴板。

还可从右键菜单中选择“获取设备信息”，在输出窗口中查看包括连接字符串在内的所有设备信息  。

## <a name="register-with-the-azure-cli"></a>注册到 Azure CLI

[Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) 是一个开源跨平台命令行工具，用于管理 IoT Edge 等 Azure 资源。 使用 Azure CLI 2.0 可以管理 Azure IoT 中心资源、设备预配服务实例和现成的链接中心。 IoT 扩展丰富了 Azure CLI 的功能，例如设备管理和完整的 IoT Edge 功能。

### <a name="prerequisites-for-the-azure-cli"></a>Azure CLI 的必备组件

* Azure 订阅中的 [IoT 中心](../iot-hub/iot-hub-create-using-cli.md)。
* 环境中的 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)。 Azure CLI 版本必须至少是 2.0.70 或更高版本。 请使用 `az --version` 验证版本。 此版本支持 az 扩展命令，并引入了 Knack 命令框架。
* [适用于 Azure CLI 的 IoT 扩展](https://github.com/Azure/azure-iot-cli-extension)。

### <a name="create-an-iot-edge-device-with-the-azure-cli"></a>通过 Azure CLI 创建 IoT Edge 设备

使用 [az iot hub device-identity create](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/hub/device-identity?view=azure-cli-latest#ext-azure-iot-az-iot-hub-device-identity-create) 命令在 IoT 中心创建新设备标识。 例如：

   ```azurecli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled
   ```

此命令包括三个参数：

* **device-id**：提供对 IoT 中心唯一的描述性名称。
* **hub-name**：提供 IoT 中心的名称。
* **edge-enabled**：此参数声明该设备用于 IoT Edge。

   ![az iot hub device-identity create output](./media/how-to-register-device/Create-edge-device.png)

### <a name="view-iot-edge-devices-with-the-azure-cli"></a>通过 Azure CLI 查看 IoT Edge 设备

使用 [az iot hub device-identity list](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/hub/device-identity?view=azure-cli-latest#ext-azure-iot-az-iot-hub-device-identity-list) 命令在 IoT 中心查看所有设备。 例如：

   ```azurecli
   az iot hub device-identity list --hub-name [hub name]
   ```

注册为 IoT Edge 设备的任何设备的 **capabilities.iotEdge** 属性都会设置为 **true**。

### <a name="retrieve-the-connection-string-with-the-azure-cli"></a>通过 Azure CLI 检索连接字符串

如果已准备好设置设备，则需要连接字符串，该字符串使用物理设备在 IoT 中心内的标识链接该设备。 使用 [az iot hub device-identity show-connection-string](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/hub/device-identity?view=azure-cli-latest#ext-azure-iot-az-iot-hub-device-identity-show-connection-string) 命令返回单个设备的连接字符串：

   ```azurecli
   az iot hub device-identity show-connection-string --device-id [device id] --hub-name [hub name]
   ```

`device-id` 参数的值区分大小写。 不要复制连接字符串两端的引号。

## <a name="next-steps"></a>后续步骤

在 IoT 中心注册设备标识以后，即可在设备上安装 IoT Edge 运行时。 根据设备的操作系统安装运行时：

* [在 Windows 上安装 Azure IoT Edge](how-to-install-iot-edge-windows.md)
* [在 Linux 上安装 Azure IoT Edge 运行时](how-to-install-iot-edge-linux.md)
