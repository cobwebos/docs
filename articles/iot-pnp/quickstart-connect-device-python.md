---
title: 将 IoT 即插即用预览版示例 Python 设备代码连接到 Azure IoT 中心 | Microsoft Docs
description: 使用 Python 生成并运行可连接到 IoT 中心的 IoT 即插即用预览版示例设备代码。 使用 Azure IoT 资源管理器工具查看由设备发送到中心的信息。
author: ericmitt
ms.author: ericmitt
ms.date: 7/14/2020
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 84ef7ff18c294097da20640c1de237b41900cb40
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/28/2020
ms.locfileid: "87352556"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-to-iot-hub-python"></a>快速入门：将示例 IoT 即插即用预览版设备应用程序连接到 IoT 中心 (Python)

[!INCLUDE [iot-pnp-quickstarts-device-selector.md](../../includes/iot-pnp-quickstarts-device-selector.md)]

本快速入门介绍如何生成示例 IoT 即插即用设备应用程序，将其连接到 IoT 中心，并使用 Azure IoT 资源管理器工具来查看它发送的遥测数据。 该示例应用程序针对 Python 编写，包含在用于 Python 的 Azure IoT 中心设备 SDK 中。 解决方案构建者可以使用 Azure IoT 资源管理器工具来了解 IoT 即插即用设备的功能，而无需查看任何设备代码。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>先决条件

若要完成本快速入门，需要在开发计算机上使用 Python 3.7。 可以从 [python.org](https://www.python.org/) 下载适用于多个平台的最新推荐版本。可以使用以下命令检查 Python 版本：  

```cmd/sh
python --version
```

### <a name="azure-iot-explorer"></a>Azure IoT 资源管理器

要在本快速入门的第二部分中与示例设备进行交互，请使用 Azure IoT 资源管理器工具。 为操作系统[下载并安装最新版本的 Azure IoT 资源管理器](./howto-use-iot-explorer.md)。

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

运行以下命令，获取中心的 IoT 中心连接字符串。 请记下此连接字符串，稍后将在本快速入门中使用：

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

> [!TIP]
> 还可以使用 Azure IoT 资源管理器工具查找 IoT 中心连接字符串。

运行以下命令，获取已添加到中心的设备的设备连接字符串。 请记下此连接字符串，稍后将在本快速入门中使用：

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output table
```

[!INCLUDE [iot-pnp-download-models.md](../../includes/iot-pnp-download-models.md)]

## <a name="set-up-your-environment"></a>设置你的环境

此包作为公共预览版刷新的 PIP 发布。 包版本应为最新版本或 `2.1.4`

在本地 python 环境中安装该文件，如下所示：

```cmd/sh
pip install azure-iot-device
```

克隆 Python SDK IoT 存储库，并查看 master：

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-python
```

## <a name="run-the-sample-device"></a>运行示例设备

azure-iot-sdk-python\azure-iot-device\samples\pnp 文件夹包含 IoT 即插即用设备的示例代码。 此快速入门使用 pnp_thermostat.py 文件。 此示例代码实现 IoT 即插即用兼容设备，并使用 Azure IoT Python 设备客户端库。

创建一个名为 IOTHUB_DEVICE_CONNECTION_STRING 的环境变量，以存储你之前记下的设备连接字符串。

在文本编辑器中打开 pnp_thermostat.py 文件。 请注意操作方式：

1. 定义唯一表示[调温器](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json)的单一设备孪生模型标识符 (DTMI)。 DTMI 必须为用户所熟知，并且根据设备实现方案而异。 对于当前示例，该模型表示调温器，其具有与监视温度关联的遥测、属性和命令。

1. 具有用于定义命令处理程序实现的函数。 你可编写这些处理程序以定义设备对命令请求的响应方式。

1. 具有用于定义命令响应的函数。 你可创建命令响应函数以将响应发送回 IoT 中心。

1. 定义输入键盘侦听器函数，以便退出应用程序。

1. 具有 main 函数。 main 函数：

    1. 使用设备 SDK 创建设备客户端并连接到 IoT 中心。

    1. 更新属性。 使用的模型（调温器）将 `targetTemperature` 和 `maxTempSinceLastReboot` 定义为调温器的两个属性，这就是我们将使用的内容。 使用 `device_client` 上定义的 `patch_twin_reported_properties` 方法更新属性。

    1. 使用 execute_command_listener 函数开始侦听命令请求。 该函数设置侦听器以侦听来自服务的命令。 设置侦听器时，提供 `method_name`、`user_command_handler` 和 `create_user_response_handler`。 
        - `user_command_handler` 函数定义设备在收到命令时应执行的操作。 例如，如果闹钟响了，接收此命令的效果是你该醒了。 将此视为所调用命令的“效果”。
        - 命令成功执行时，`create_user_response_handler` 函数将创建要发送到 IoT 中心的响应。 例如，如果你的闹钟响了，则按下推迟以作为响应，这是对服务的反馈。 将此视为你给服务的答复。 可以在门户中查看此响应。

    1. 开始发送遥测。 pnp_methods.py 文件中定义了 pnp_send_telemetry。 示例代码使用循环每隔 8 秒调用此函数。

    1. 禁用所有侦听器和任务，并在按 Q 或 q 时退出循环 。

看到代码后，请使用以下命令运行该示例：

```cmd/sh
python pnp_thermostat.py
```

将显示以下输出，这表明设备正在向中心发送遥测数据，现在可以接收命令和属性更新：

```cmd/sh
Connecting using Connection String HostName=<your hub name>.azure-devices.net;DeviceId=<your device id>;SharedAccessKey=<your device shared access key>
Listening for command requests and property updates
Press Q to quit
Sending telemetry for temperature
Sent message
```

在执行后续步骤时，保持示例处于运行状态。

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>使用 Azure IoT 资源管理器验证代码

设备客户端示例启动后，使用 Azure IoT 资源管理器工具验证它是否正常工作。

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>后续步骤

本快速入门已介绍如何将 IoT 即插即用设备连接到 IoT 中心。 若要详细了解如何生成可与 IoT 即插即用设备交互的解决方案，请参阅：

> [!div class="nextstepaction"]
> [与已连接到解决方案的 IoT 即插即用预览版设备交互](quickstart-service-python.md)
