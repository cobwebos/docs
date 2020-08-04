---
title: 将 IoT 即插即用预览版示例 Python 组件设备代码连接到 IoT 中心 | Microsoft Docs
description: 生成和运行使用多个组件并连接到 IoT 中心的 IoT 即插即用预览版示例 Python 设备代码。 使用 Azure IoT 资源管理器工具查看由设备发送到中心的信息。
author: ericmitt
ms.author: ericmitt
ms.date: 7/14/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 0cde9caa2f2b68b1e75eac635a81865cc4b6b33c
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/28/2020
ms.locfileid: "87351719"
---
# <a name="tutorial-connect-a-sample-iot-plug-and-play-preview-multiple-component-device-application-to-iot-hub-python"></a>教程：将示例 IoT 即插即用预览版多个组件设备应用程序连接到 IoT 中心 (Python)

[!INCLUDE [iot-pnp-tutorials-device-selector.md](../../includes/iot-pnp-tutorials-device-selector.md)]

本教程介绍如何使用组件和根接口生成示例 IoT 即插即用设备应用程序，将其连接到 IoT 中心，并使用 Azure IoT 资源管理器工具来查看它发送到中心的信息。 该示例应用程序以 Python 编写，包含在用于 Python 的 Azure IoT 设备 SDK 中。 解决方案构建者可以使用 Azure IoT 资源管理器工具来了解 IoT 即插即用设备的功能，而无需查看任何设备代码。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>先决条件

若要完成本教程，需要在开发计算机上使用 Python 3.7。 可以从 [python.org](https://www.python.org/) 下载适用于多个平台的最新推荐版本。可以使用以下命令检查 Python 版本：  

```cmd/sh
python --version
```

可以从 [python.org](https://www.python.org/) 下载适用于多个平台的最新推荐版本。

### <a name="azure-iot-explorer"></a>Azure IoT 资源管理器

要在本教程的第二部分中与示例设备进行交互，请使用“Azure IoT 资源管理器”工具。 为操作系统[下载并安装最新版本的 Azure IoT 资源管理器](./howto-use-iot-explorer.md)。

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

运行以下命令，获取中心的 IoT 中心连接字符串。 记下此连接字符串，稍后将在本教程用到它：

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

> [!TIP]
> 还可以使用 Azure IoT 资源管理器工具查找 IoT 中心连接字符串。

运行以下命令，获取已添加到中心的设备的设备连接字符串。 记下此连接字符串，稍后将在本教程用到它：

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

克隆 Python SDK IoT 存储库并查看“pnp-preview-refresh”：

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-python
```

## <a name="review-the-code"></a>查看代码

此示例实现 IoT 即插即用温度控制器设备。 此示例实现的模型使用[多个组件](concepts-components.md)。 [温度设备的数字孪生定义语言 (DTDL) 模型文件](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json)定义了设备实现的遥测、属性和命令。

azure-iot-sdk-python\azure-iot-device\samples\pnp 文件夹包含 IoT 即插即用设备的示例代码。 温度控制器示例的文件包括：

- pnp_temp_controller_with_thermostats.py
- pnp_helper_preview_refresh.py

温度控制器基于温度控制器 DTDL 模型，具有多个组件和一个根接口。

在所选的编辑器中打开 pnp_temp_controller_with_thermostats.py 文件。 此文件中的代码：

1. 导入 `pnp_helper_preview_refresh.py` 以获取对帮助器方法的访问权限。

1. 定义两个数字孪生模型标识符 (DTMI)，它们唯一表示 DTDL 模型中定义的两个不同接口。 实际温度控制器中的组件应实现这两个接口。 这两个接口已在中央存储库中发布。 这些 DTMI 必须为用户所知，并且根据设备实现方案而有所不同。 对于当前示例，这两个接口表示：

  - 调温器
  - Azure 开发的设备信息。

. 为要实现的设备定义 DTMI `model_id`。 DTMI 是用户定义的，必须与 DTDL 模型文件中的 DTMI 匹配。

1. 定义为 DTDL 文件中的组件提供的名称。 DTDL 中有两个调温器和一个设备信息组件。 名为 `serial_number` 的常量也是在根接口中定义的。 设备的 `serial_number` 无法更改。

1. 定义命令处理程序实现。 它们定义设备接收命令请求时执行哪些操作。

1. 定义创建命令响应的函数。 它们定义设备如何响应命令请求。 如果命令需要将自定义响应发送回 IoT 中心，你可以创建命令响应函数。 如果未提供命令的响应函数，则发送通用响应。 在此示例中，只有 getMaxMinReport 命令具有自定义响应。

1. 定义一个函数，用于从此设备发送遥测数据。 调温器和根接口都会发送遥测数据。 此函数采用可选的组件名称参数，这让它能够识别哪个组件发送了遥测数据。

1. 定义命令请求的侦听器。

1. 定义所需属性更新的侦听器。

1. 有一个 `main` 函数：

    1. 使用设备 SDK 创建设备客户端并连接到 IoT 中心。 设备发送 `model_id`，以便 IoT 中心可以将设备标识为 IoT 即插即用设备。

    1. 使用帮助器文件中的 `create_reported_properties` 函数创建属性。 将组件名称和属性作为键值对传递给此函数。

    1. 通过调用 `patch_twin_reported_properties` 为其组件更新可读属性。

    1. 使用 `execute_command_listener` 函数开始侦听命令请求。 函数为来自服务的命令请求设置侦听器。 设置侦听器时，提供 `method_name`、`user_command_handler` 和可选的 `create_user_response_handler` 作为参数。
        - `method_name` 定义命令请求。 在此示例中，模型定义命令“reboot”和“getMaxMinReport” 。
        - `user_command_handler` 函数定义设备在收到命令时应执行的操作。
        - 命令成功执行时，`create_user_response_handler` 函数将创建要发送到 IoT 中心的响应。 可以在门户中查看此响应。 如果未提供此函数，则向服务发送通用响应。

    1. 使用 `execute_property_listener` 侦听属性更新。

    1. 使用 `send_telemetry` 开始发送遥测数据。 示例代码使用循环来调用三个发送遥测数据的函数。 每八秒调用一次每个函数

    1. 禁用所有侦听器和任务，并在按 Q 或 q 时退出循环 。

现在你已经查看了代码，请创建一个名为 IOTHUB_DEVICE_CONNECTION_STRING 的环境变量来存储你之前记下的设备连接字符串。使用以下命令运行该示例：

```cmd/sh
python pnp_temp_controller_with_thermostats.py
```

示例设备每隔几秒钟向 IoT 中心发送遥测消息。

你会看到以下输出，这表明设备正在向中心发送遥测数据，现在可以接收命令和属性更新。

![设备确认消息](media/tutorial-multiple-components-python/multiple-component.png)

在执行后续步骤时，保持示例处于运行状态。

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>使用 Azure IoT 资源管理器验证代码

设备客户端示例启动后，使用 Azure IoT 资源管理器工具验证它是否正常工作。

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>后续步骤

本教程介绍了如何将 IoT 即插即用设备和组件连接到 IoT 中心。 若要详细了解 IoT 即插即用设备模型，请参阅：

> [!div class="nextstepaction"]
> [IoT 即插即用预览版建模开发人员指南](concepts-developer-guide.md)
