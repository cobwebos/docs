---
title: 与连接到 Azure IoT 解决方案的 IoT 即插即用预览版设备交互 (Python) | Microsoft Docs
description: 使用 Python 连接到已与 Azure IoT 解决方案连接的 IoT 即插即用预览版设备并与之交互。
author: elhorton
ms.author: elhorton
ms.date: 7/13/2020
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: e3f00bb601cce17721c5247941588be1c2de788d
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/28/2020
ms.locfileid: "87352591"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-preview-device-thats-connected-to-your-solution-python"></a>快速入门：与已连接到解决方案的 IoT 即插即用预览版设备交互 (Python)

[!INCLUDE [iot-pnp-quickstarts-service-selector.md](../../includes/iot-pnp-quickstarts-service-selector.md)]

IoT 即插即用预览版简化了 IoT 的使用，无需了解底层设备实现，就能使用它来与某个设备的模型交互。 本快速入门介绍如何使用 Python 来连接和控制已与解决方案连接的 IoT 即插即用设备。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>先决条件

若要完成本快速入门，需要在开发计算机上使用 Python 3.7。 可以从 [python.org](https://www.python.org/) 下载适用于多个平台的最新推荐版本。可以使用以下命令检查 Python 版本：  

```cmd/sh
python --version
```

通过运行以下命令安装 [Python 服务 SDK 预览版包](https://pypi.org/project/azure-iot-hub/2.2.1rc0/)：

```cmd/sh
pip3 install azure-iot-hub==2.2.1rc0
```

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

运行以下命令，获取中心的 IoT 中心连接字符串。 请记下此连接字符串，稍后将在本快速入门中使用：

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

运行以下命令，获取已添加到中心的设备的设备连接字符串。 请记下此连接字符串，稍后将在本快速入门中使用：

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output
```

## <a name="run-the-sample-device"></a>运行示例设备

在本快速入门中，你将使用一个以 Python 编写的示例调温器作为 IoT 即插即用设备。 运行示例设备：

1. 在所选文件夹中打开一个终端窗口。 运行以下命令，将 [Azure IoT Python SDK](https://github.com/Azure/azure-iot-sdk-python) GitHub 存储库克隆到此位置：

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-python
    ```

1. 此终端窗口将用作设备终端。 前往克隆的存储库的文件夹，然后导航到 /azure-iot-sdk-python/azure-iot-device/samples/pnp 文件夹。

1. 配置设备连接字符串：

    ```cmd/sh
    set IOTHUB_DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

1. 使用以下命令运行示例调温器设备：

    ```cmd/sh
    python pnp_thermostat.py
    ```

1. 你会看到一些消息，指出设备已发送部分信息并报告其自身处于联机状态。 这些消息表明设备已开始向中心发送遥测数据，现在可以接收命令和属性更新。 请不要关闭此终端，需要在其中确认服务示例是否在正常运行。

## <a name="run-the-sample-solution"></a>运行示例解决方案

在本快速入门中，你将使用以 Python 编写的示例 IoT 解决方案与你刚刚设置的示例设备进行交互。

1. 打开另一个终端窗口用作服务终端。 服务 SDK 处于预览阶段，因此需要从 [Python SDK 的 预览分支](https://github.com/Azure/azure-iot-sdk-python/tree/pnp-preview-refresh)克隆示例：

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-python -b pnp-preview-refresh
    ```

1. 前往此克隆的存储库分支的文件夹，然后导航到 /azure-iot-sdk-python/azure-iot-hub/samples 文件夹。

1. 为设备 ID 和 IoT 中心连接字符串配置环境变量：

    ```cmd/sh
    set IOTHUB_CONNECTION_STRING=<YourIOTHubConnectionString>
    set IOTHUB_DEVICE_ID=<Your device ID>
    ```

1. 在 samples 文件夹中，有四个带有 `pnp` 前缀的示例文件。 这些示例演示如何使用各个 API 与 IoT 即插即用设备交互：

### <a name="get-digital-twin"></a>获取数字孪生

在服务终端中使用以下命令运行此示例：

```cmd/sh
python pnp_get_digital_twin_sample.py
```

其输出将显示设备的数字孪生，并打印其模型 ID：

```cmd/sh
{'$dtId': 'mySimpleThermostat', '$metadata': {'$model': 'dtmi:com:example:Thermostat;1'}}
Model Id: dtmi:com:example:Thermostat;1
```

以下代码片段显示了 pnp_get_digital_twin_sample.py 中的示例代码：

```python
    # Get digital twin and retrieve the modelId from it
    digital_twin = iothub_digital_twin_manager.get_digital_twin(device_id)
    if digital_twin:
        print(digital_twin)
        print("Model Id: " + digital_twin["$metadata"]["$model"])
    else:
        print("No digital_twin found")
```

### <a name="update-a-digital-twin"></a>更新数字孪生

此示例演示如何使用修补程序通过设备的数字孪生来更新属性。 pnp_update_digital_twin_sample.py 中的以下代码片段演示如何构建修补程序：

```python
# If you already have a component thermostat1:
# patch = [{"op": "replace", "path": "/thermostat1/targetTemperature", "value": 42}]
patch = [{"op": "add", "path": "/targetTemperature", "value": 42}]
iothub_digital_twin_manager.update_digital_twin(device_id, patch)
print("Patch has been succesfully applied")
```

在服务终端中使用以下命令运行此示例：

```cmd/sh
python pnp_update_digital_twin_sample.py
```

你可以在显示以下输出的设备终端中验证是否已应用更新：

```cmd/sh
the data in the desired properties patch was: {'targetTemperature': 42, '$version': 2}
previous values
42
```

服务终端确认修补程序已成功：

```cmd/sh
Patch has been successfully applied
```

### <a name="invoke-a-command"></a>调用命令

若要调用命令，请运行 pnp_invoke_command_sample.py 示例。 此示例演示如何在简单的调温器设备中调用命令。 在运行此示例之前，请在服务终端中设置 `IOTHUB_COMMAND_NAME` 和 `IOTHUB_COMMAND_PAYLOAD` 环境变量：

```cmd/sh
set IOTHUB_COMMAND_NAME="getMaxMinReport" # this is the relevant command for the thermostat sample
set IOTHUB_COMMAND_PAYLOAD="hello world" # this payload doesn't matter for this sample
```

在服务终端中使用以下命令运行此示例：
  
```cmd/sh
python pnp_invoke_command_sample.py
```

服务终端显示来自设备的确认消息：

```cmd/sh
{"tempReport": {"avgTemp": 34.5, "endTime": "13/07/2020 16:03:38", "maxTemp": 49, "minTemp": 11, "startTime": "13/07/2020 16:02:18"}}
```

在设备终端中，将显示设备收到以下命令：

```cmd/sh
Command request received with payload
hello world
Will return the max, min and average temperature from the specified time hello to the current time
Done generating
{"tempReport": {"avgTemp": 34.2, "endTime": "09/07/2020 09:58:11", "maxTemp": 49, "minTemp": 10, "startTime": "09/07/2020 09:56:51"}}
Sent message
```

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>后续步骤

本快速入门已介绍如何将 IoT 即插即用设备连接到 IoT 解决方案。 若要详细了解 IoT 即插即用设备模型，请参阅：

> [!div class="nextstepaction"]
> [IoT 即插即用预览版建模开发人员指南](concepts-developer-guide.md)
