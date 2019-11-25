---
title: Register a new Azure IoT Edge device | Microsoft Docs
description: 使用适用于 Azure CLI 的 IoT 扩展注册新的 IoT Edge 设备并检索连接字符串
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/21/2019
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 793ddcb9f218248c396e10f23201dfe905545ceb
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456852"
---
# <a name="register-an-azure-iot-edge-device"></a>Register an Azure IoT Edge device

Before you can use your IoT devices with Azure IoT Edge, you must register them with your IoT hub. Once a device is registered, you can retrieve a connection string to set up your device for IoT Edge workloads.

You have the choice of registering by using one of the following tools:

* [Azure portal](https://portal.azure.com) provides a graphical user interface to create, view, and manage Azure resources.
* [Visual Studio Code](https://code.visualstudio.com/) is a source-code editor. Azure IoT extensions make it easy to manage IoT resources from the same tool where you're developing IoT solutions.
* [Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) is a command-line tool for managing Azure resources. Its reusable commands are helpful for automating tasks.

## <a name="register-in-the-azure-portal"></a>Register in the Azure portal

You can perform all registration tasks in the Azure portal.

### <a name="prerequisites-for-the-azure-portal"></a>Prerequisites for the Azure portal

A free or standard [IoT hub](../iot-hub/iot-hub-create-through-portal.md) in your Azure subscription.

### <a name="create-an-iot-edge-device-in-the-azure-portal"></a>Create an IoT Edge device in the Azure portal

In your IoT Hub in the Azure portal, IoT Edge devices are created and managed separately from IOT devices that are not edge enabled.

1. 登录 [Azure 门户](https://portal.azure.com)，导航到 IoT 中心。
2. In the left pane, select **IoT Edge** from the menu.
3. 选择“添加 IoT Edge 设备”。
4. 提供一个描述性的设备 ID。 Use the default settings to auto-generate authentication keys and connect the new device to your hub.
5. 选择“保存”。

### <a name="view-iot-edge-devices-in-the-azure-portal"></a>View IoT Edge devices in the Azure portal

所有连接到 IoT 中心并已启用 Edge 的设备都列在 **IoT Edge** 页上。

![查看 IoT 中心中所有的 IoT Edge 设备](./media/how-to-register-device/portal-view-devices.png)

### <a name="retrieve-the-connection-string-in-the-azure-portal"></a>Retrieve the connection string in the Azure portal

如果已准备好设置设备，则需连接字符串，该字符串使用 IoT 中心内物理设备的标识链接设备。

1. From the **IoT Edge** page in the portal, click on the device ID from the list of IoT Edge devices.
2. 复制**连接字符串(主密钥)** 或**连接字符串(辅助密钥)** 的值。

## <a name="register-with-visual-studio-code"></a>Register with Visual Studio Code

有多种方法可以执行 VS Code 中的大部分操作。 This article uses the Explorer, but you can also use the Command Palette to run the steps.

### <a name="prerequisites-for-visual-studio-code"></a>Prerequisites for Visual Studio Code

* Azure 订阅中的 [IoT 中心](../iot-hub/iot-hub-create-through-portal.md)
* [Visual Studio Code](https://code.visualstudio.com/)
* 适用于 Visual Studio Code 的 [Azure IoT 工具](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)

### <a name="sign-in-to-access-your-iot-hub"></a>登录以访问 IoT 中心

You can use the Azure IoT extensions for Visual Studio Code to perform operations with your IoT Hub. For these operations to work, you need to sign in to your Azure account and select your IoT Hub.

1. 在 Visual Studio Code 中打开“资源管理器”视图。
1. At the bottom of the Explorer, expand the **Azure IoT Hub** section.

   ![展开“Azure IoT 中心设备”部分](./media/how-to-register-device/azure-iot-hub-devices.png)

1. Click on the **...** in the **Azure IoT Hub** section header. 如果没有看到省略号，请单击标题或将鼠标指针悬停在标题上。
1. 选择“选择 IoT 中心”。
1. If you aren't signed in to your Azure account, follow the prompts to do so.
1. 选择 Azure 订阅。
1. 选择 IoT 中心。

### <a name="create-an-iot-edge-device-with-visual-studio-code"></a>Create an IoT Edge device with Visual Studio Code

1. 在 VS Code 资源管理器中，展开“Azure IoT 中心设备”部分。
1. 单击“Azure IoT 中心设备”部分标题中的“...”。 如果没有看到省略号，请单击标题或将鼠标指针悬停在标题上。
1. 选择“创建 IoT Edge 设备”。
1. 在打开的文本框中提供设备 ID。

在输出屏幕中，可以看到命令的结果。 其中显示有设备信息，包括所提供的“deviceId”以及可用于将物理设备连接到 IoT 中心的“connectionString”。

在输出屏幕中，可以看到命令的结果。 其中显示有设备信息，包括所提供的“deviceId”以及可用于将物理设备连接到 IoT 中心的“connectionString”。

### <a name="view-iot-edge-devices-with-visual-studio-code"></a>View IoT Edge devices with Visual Studio Code

All the devices that connect to your IoT hub are listed in the **Azure IoT Hub** section of the Visual Studio Code Explorer. IoT Edge devices are distinguishable from non-Edge devices with a different icon, and the fact that the **$edgeAgent** and **$edgeHub** modules are deployed to each IoT Edge device.

![查看 IoT 中心中所有的 IoT Edge 设备](./media/how-to-register-device/view-devices.png)

### <a name="retrieve-the-connection-string-with-visual-studio-code"></a>Retrieve the connection string with Visual Studio Code

如果已准备好设置设备，则需连接字符串，该字符串使用 IoT 中心内物理设备的标识链接设备。

1. Right-click on the ID of your device in the **Azure IoT Hub** section.
1. 选择“复制设备连接字符串”。

   连接字符串会复制到剪贴板。

还可从右键菜单中选择“获取设备信息”，在输出窗口中查看包括连接字符串在内的所有设备信息。

## <a name="register-with-the-azure-cli"></a>Register with the Azure CLI

The [Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) is an open-source cross platform command-line tool for managing Azure resources such as IoT Edge. 使用 Azure CLI 2.0 可以管理 Azure IoT 中心资源、设备预配服务实例和现成的链接中心。 新的 IoT 扩展丰富了 Azure CLI 的功能，例如设备管理和完整的 IoT Edge 功能。

### <a name="prerequisites-for-the-azure-cli"></a>Prerequisites for the Azure CLI

* Azure 订阅中的 [IoT 中心](../iot-hub/iot-hub-create-using-cli.md)。
* 环境中的 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)。 Azure CLI 版本必须至少是 2.0.24 或更高版本。 请使用 `az --version` 验证版本。 此版本支持 az 扩展命令，并引入了 Knack 命令框架。
* [适用于 Azure CLI 的 IoT 扩展](https://github.com/Azure/azure-iot-cli-extension)。

### <a name="create-an-iot-edge-device-with-the-azure-cli"></a>Create an IoT Edge device with the Azure CLI

Use the [az iot hub device-identity create](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-create) command to create a new device identity in your IoT hub. 例如：

   ```cli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled
   ```

此命令包括三个参数：

* **device-id**：提供对 IoT 中心唯一的描述性名称。
* **hub-name**：提供 IoT 中心的名称。
* **edge-enabled**：此参数声明该设备用于 IoT Edge。

   ![az iot hub device-identity create output](./media/how-to-register-device/Create-edge-device.png)

### <a name="view-iot-edge-devices-with-the-azure-cli"></a>View IoT Edge devices with the Azure CLI

Use the [az iot hub device-identity list](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-list) command to view all devices in your IoT hub. 例如：

   ```cli
   az iot hub device-identity list --hub-name [hub name]
   ```

注册为 IoT Edge 设备的任何设备的 **capabilities.iotEdge** 属性都会设置为 **true**。

### <a name="retrieve-the-connection-string-with-the-azure-cli"></a>Retrieve the connection string with the Azure CLI

如果已准备好设置设备，则需连接字符串，该字符串使用 IoT 中心内物理设备的标识链接设备。 Use the [az iot hub device-identity show-connection-string](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-show-connection-string) command to return the connection string for a single device:

   ```cli
   az iot hub device-identity show-connection-string --device-id [device id] --hub-name [hub name]
   ```

`device-id` 参数的值区分大小写。 不要复制连接字符串两端的引号。

## <a name="next-steps"></a>后续步骤

Now that you have a device identity registered in your IoT hub, you're ready to install the IoT Edge runtime on your devices. Install the runtime according to the device's operating system:

* [Install Azure IoT Edge on Windows](how-to-install-iot-edge-windows.md)
* [Install the Azure IoT Edge runtime on Linux](how-to-install-iot-edge-linux.md)
