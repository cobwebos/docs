---
title: 安装和使用 Azure IoT 资源管理器 |微软文档
description: 安装 Azure IoT 资源管理器工具，并使用它与连接到 IoT 中心的 IoT 即插即用预览设备进行交互。
author: miagdp
ms.author: miag
ms.date: 12/27/2019
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 963421fa1ef06599448c9a4197f0d7a6ad2e142d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159177"
---
# <a name="install-and-use-azure-iot-explorer"></a>安装和使用 Azure IoT 资源管理器

Azure IoT 资源管理器是一种图形工具，用于与 IoT 即插即用预览设备进行交互和测试。 在本地计算机上安装该工具后，可以使用该工具连接到设备。 您可以使用该工具查看设备发送的遥测数据、使用设备属性和调用命令。

本文介绍如何：

- 安装和配置 Azure IoT 资源管理器工具。
- 使用该工具与设备交互和测试设备。

## <a name="prerequisites"></a>先决条件

要使用 Azure IoT 资源管理器工具，您需要：

- Azure IoT 中心。 有许多方法可以将 IoT 中心添加到 Azure 订阅中，例如[使用 Azure CLI 创建 IoT 中心](../iot-hub/iot-hub-create-using-cli.md)。 您需要 IoT 中心连接字符串来运行 Azure IoT 资源管理器工具。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 在 IoT 中心注册的设备。 可以使用以下 Azure CLI 命令注册设备。 请务必用值替换`{YourIoTHubName}`和`{YourDeviceID}`占位符：

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id {YourDeviceID}
    ```

## <a name="install-azure-iot-explorer"></a>安装 Azure IoT 资源管理器

转到[Azure IoT 资源管理器版本](https://github.com/Azure/azure-iot-explorer/releases)并展开最新版本的资产列表。 下载并安装应用程序的最新版本。

## <a name="use-azure-iot-explorer"></a>使用 Azure IoT 资源管理器

对于设备，您可以连接自己的设备，或使用我们的模拟示例设备之一。 按照[这些说明](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/iothub_client/samples)运行模拟设备示例。

### <a name="connect-to-your-hub"></a>连接到您的中心

首次运行 Azure IoT 资源管理器时，系统会提示您使用 IoT 中心的连接字符串。 添加连接字符串后，选择 **"连接**"。 您可以使用该工具的设置通过更新连接字符串切换到另一个 IoT 中心。

IoT 即插即用设备的模型定义存储在公共存储库、公司存储库或连接的设备中。 默认情况下，该工具在公共模型存储库和连接的设备中查找模型定义。 您可以在 **"设置"** 中添加和删除源，或配置源的优先级：

要添加源：

1. 转到 **“设置”**。
1. 选择 **"新建"** 并选择源。
1. 如果要添加公司模型存储库，请提供连接字符串。

要删除源：

1. 转到 **“设置”**。
1. 查找要删除的源。
1. 选择**X**将其删除。 无法删除公共模型存储库，因为公共接口定义来自此存储库。

更改源优先级：

您可以将其中一个模型定义源拖放到列表中的不同排名。 如果存在冲突，排名较高的定义源将覆盖排名较低的源。

### <a name="view-devices"></a>查看设备

该工具连接到 IoT 中心后，将显示列出在 IoT 中心注册的设备标识**的设备**列表页。 您可以展开列表中的任何条目以查看详细信息。

在 **"设备"** 列表页上，您可以：

- 选择 **"添加"** 可向集线器注册新设备。 然后输入设备 ID。 使用默认设置自动生成身份验证密钥并启用到集线器的连接。
- 选择设备，然后选择 **"删除**"以删除设备标识。 在完成此操作之前，请查看设备详细信息，以确保删除正确的设备标识。
- 查询者`capabilityID``interfaceID`和 。 添加或`capabilityID``interfaceID`作为参数来查询设备。

## <a name="interact-with-a-device"></a>与设备交互

在"**设备"** 列表页上，在 **"设备 ID"** 列中选择一个值以查看已注册设备的详细信息页。 对于每个设备有两个部分：**设备和****数字孪生**。

### <a name="device"></a>设备

本节包括**设备标识**、**设备孪生**、**遥测**、**直接方法和****云到设备消息**选项卡。

- 您可以在 **"设备标识**"选项卡上查看和更新[设备标识](../iot-hub/iot-hub-devguide-identity-registry.md)信息。
- 您可以在 **"设备孪生"** 选项卡上访问[设备孪生](../iot-hub/iot-hub-devguide-device-twins.md)信息。
- 如果设备已连接并主动发送数据，则可以在 **"遥测"** 选项卡上查看[遥测](../iot-hub/iot-hub-devguide-messages-read-builtin.md)数据。
- 您可以在**Direct 方法**选项卡上调用设备上[的直接方法](../iot-hub/iot-hub-devguide-direct-methods.md)。
- 您可以在"**云到设备消息**"选项卡上发送[云到设备消息](../iot-hub/iot-hub-devguide-messages-c2d.md)。

### <a name="digital-twin"></a>数字孪生

您可以使用该工具查看设备的数字孪生实例。 对于 IoT 即插即用设备，与设备功能模型关联的所有接口都显示在工具的此部分中。 选择一个接口来扩展其相应的[IoT 即插即用基元](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL)。

### <a name="interface"></a>接口

在 **"接口"** 页上，您可以查看接口的 JSON 定义。

#### <a name="properties"></a>属性

您可以查看在 **"不可写入属性**"页上的界面中定义的只读属性。 您可以更新"**可写入属性**"页上的接口中定义的可写入属性：

1. 转到 **"可写属性"** 页。
1. 单击要更新的属性。
1. 输入属性的新值。
1. 预览要发送到设备的有效负载。
1. 提交更改。

提交更改后，可以跟踪更新状态：**同步**、**成功**或**错误**。 合成完成后，您将在 **"报告属性"** 列中看到属性的新值。 如果在合成完成之前导航到其他页面，该工具仍会在更新完成后通知您。 您还可以使用工具的通知中心查看通知历史记录。

#### <a name="commands"></a>命令

要向设备发送命令，请转到 **"命令"** 页：

1. 在命令列表中，展开要触发的命令。
1. 输入命令的任何必需值。
1. 预览要发送到设备的有效负载。
1. 提交命令。

#### <a name="telemetry"></a>遥测

要查看所选接口的遥测数据，请访问其**遥测**页面。

## <a name="next-steps"></a>后续步骤

在本"执行"一文中，您学习了如何安装和使用 Azure IoT 资源管理器与 IoT 即插即用设备进行交互。 建议的下一步是了解如何[安装和使用 Azure CLI 扩展](./howto-install-pnp-cli.md)。
