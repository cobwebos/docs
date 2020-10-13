---
title: 将 IoT 即插即用示例 Python 组件设备代码连接到 IoT 中心 | Microsoft Docs
description: 生成和运行使用多个组件并连接到 IoT 中心的 IoT 即插即用示例 Python 设备代码。 使用 Azure IoT 资源管理器工具查看由设备发送到中心的信息。
author: ericmitt
ms.author: ericmitt
ms.date: 7/14/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 084ba93baa35790da58e7765750bb79de27ed69c
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91578013"
---
# <a name="tutorial-connect-a-sample-iot-plug-and-play-multiple-component-device-application-to-iot-hub-python"></a>教程：将示例 IoT 即插即用多组件设备应用程序连接到 IoT 中心 (Python)

[!INCLUDE [iot-pnp-tutorials-device-selector.md](../../includes/iot-pnp-tutorials-device-selector.md)]

本教程介绍如何使用组件生成示例 IoT 即插即用设备应用程序，将其连接到 IoT 中心，并使用 Azure IoT 资源管理器工具来查看它发送到中心的信息。 该示例应用程序以 Python 编写，包含在用于 Python 的 Azure IoT 设备 SDK 中。 解决方案构建者可以使用 Azure IoT 资源管理器工具来了解 IoT 即插即用设备的功能，而无需查看任何设备代码。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

若要完成本教程，需要在开发计算机上使用 Python 3.7。 可以从 [python.org](https://www.python.org/) 下载适用于多个平台的最新推荐版本。可以使用以下命令检查 Python 版本：  

```cmd/sh
python --version
```

可以从 [python.org](https://www.python.org/) 下载适用于多个平台的最新推荐版本。

## <a name="download-the-code"></a>下载代码

azure-iot-device 包以 PIP 的形式发布。

在本地 Python 环境中安装该包，如下所示：

```cmd/sh
pip install azure-iot-device
```

如果已完成[快速入门：将 Windows 上运行的示例 IoT 即插即用设备应用程序连接到 IoT 中心 (Python)](quickstart-connect-device-python.md)，则已克隆了存储库。

克隆 Python SDK IoT 存储库：

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-python
```

## <a name="review-the-code"></a>查看代码

此示例实现 IoT 即插即用温度控制器设备。 此示例实现的模型使用[多个组件](concepts-components.md)。 [温度设备的数字孪生定义语言 (DTDL) 模型文件](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json)定义了设备实现的遥测、属性和命令。

azure-iot-sdk-python\azure-iot-device\samples\pnp 文件夹包含 IoT 即插即用设备的示例代码。 温度控制器示例的文件包括：

- temp_controller_with_thermostats.py
- pnp_helper_preview_refresh.py

温度控制器基于温度控制器 DTDL 模型，具有多个组件和一个默认组件。

在所选的编辑器中打开 temp_controller_with_thermostats.py 文件。 此文件中的代码：

1. 导入 `pnp_helper_preview_refresh.py` 以获取对帮助器方法的访问权限。

1. 定义两个数字孪生模型标识符 (DTMI)，它们唯一表示 DTDL 模型中定义的两个不同接口。 实际温度控制器中的组件应实现这两个接口。 这两个接口已在中央存储库中发布。 这些 DTMI 必须为用户所知，并且根据设备实现方案而有所不同。 对于当前示例，这两个接口表示：

    - 调温器
    - Azure 开发的设备信息。

1. 为要实现的设备定义 DTMI `model_id`。 DTMI 是用户定义的，必须与 DTDL 模型文件中的 DTMI 匹配。

1. 定义为 DTDL 文件中的组件提供的名称。 DTDL 中有两个调温器和一个设备信息组件。 名为 `serial_number` 的常量也是在默认组件中定义。 设备的 `serial_number` 无法更改。

1. 定义命令处理程序实现。 它们定义设备接收命令请求时执行哪些操作。

1. 定义创建命令响应的函数。 它们定义设备如何响应命令请求。 如果命令需要将自定义响应发送回 IoT 中心，你可以创建命令响应函数。 如果未提供命令的响应函数，则发送通用响应。 在此示例中，只有 getMaxMinReport 命令具有自定义响应。

1. 定义一个函数，用于从此设备发送遥测数据。 恒温器和默认组件都发送遥测数据。 此函数采用可选的组件名称参数，这让它能够识别哪个组件发送了遥测数据。

1. 定义命令请求的侦听器。

1. 定义所需属性更新的侦听器。

1. 有一个 `main` 函数：

    - 使用设备 SDK 创建设备客户端并连接到 IoT 中心。 设备发送 `model_id`，以便 IoT 中心可以将设备标识为 IoT 即插即用设备。

    - 使用帮助器文件中的 `create_reported_properties` 函数创建属性。 将组件名称和属性作为键值对传递给此函数。

    - 通过调用 `patch_twin_reported_properties` 为其组件更新可读属性。

    - 使用 `execute_command_listener` 函数开始侦听命令请求。 函数为来自服务的命令请求设置侦听器。 设置侦听器时，提供 `method_name`、`user_command_handler` 和可选的 `create_user_response_handler` 作为参数。
        - `method_name` 定义命令请求。 在此示例中，模型定义命令“reboot”和“getMaxMinReport” 。
        - `user_command_handler` 函数定义设备在收到命令时应执行的操作。
        - 命令成功执行时，`create_user_response_handler` 函数将创建要发送到 IoT 中心的响应。 可以在门户中查看此响应。 如果未提供此函数，则向服务发送通用响应。

    - 使用 `execute_property_listener` 侦听属性更新。

    - 使用 `send_telemetry` 开始发送遥测数据。 示例代码使用循环来调用三个发送遥测数据的函数。 每八秒调用一次每个函数

    - 禁用所有侦听器和任务，并在按 Q 或 q 时退出循环 。

[!INCLUDE [iot-pnp-environment](../../includes/iot-pnp-environment.md)]

若要详细了解示例配置，请参阅[示例自述文件](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/pnp/README.md)。

使用以下命令运行示例：

```cmd/sh
python temp_controller_with_thermostats.py
```

示例设备每隔几秒钟向 IoT 中心发送遥测消息。

你会看到以下输出，这表明设备正在向中心发送遥测数据，现在可以接收命令和属性更新。

![设备确认消息](media/tutorial-multiple-components-python/multiple-component.png)

在执行后续步骤时，保持示例处于运行状态。

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>使用 Azure IoT 资源管理器验证代码

设备客户端示例启动后，使用 Azure IoT 资源管理器工具验证它是否正常工作。

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

## <a name="next-steps"></a>后续步骤

本教程已介绍如何将 IoT 即插即用设备和组件连接到 IoT 中心。 若要详细了解 IoT 即插即用设备模型，请参阅：

> [!div class="nextstepaction"]
> [IoT 即插即用建模开发人员指南](concepts-developer-guide-device-csharp.md)
