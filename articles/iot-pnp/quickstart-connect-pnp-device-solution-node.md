---
title: 与连接到 Azure IoT 解决方案的 IoT 即插即用预览版设备交互 | Microsoft Docs
description: 使用 Node.js 连接到已与 Azure IoT 解决方案连接的 IoT 即插即用预览版设备并与之交互。
author: miagdp
ms.author: miag
ms.date: 12/27/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 9962763e647faddc5a2179f304aeb3fa8ca256e8
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/31/2019
ms.locfileid: "75550666"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-preview-device-thats-connected-to-your-solution-nodejs"></a>快速入门：与连接到解决方案的 IoT 即插即用预览版设备交互 (Node.js)

[!INCLUDE [iot-pnp-quickstarts-3-selector.md](../../includes/iot-pnp-quickstarts-3-selector.md)]

IoT 即插即用预览版简化了 IoT 的使用，无需了解底层设备实现，就能使用它来与某个设备的功能交互。 本快速入门介绍如何使用 Node.js 来连接和控制已与解决方案连接的 IoT 即插即用设备。

## <a name="prerequisites"></a>必备条件

若要完成本快速入门，需要在开发计算机上使用 Node.js。 可以从 [nodejs.org](https://nodejs.org) 下载适用于多个平台的最新推荐版本。

可以使用以下命令验证开发计算机上 Node.js 当前的版本：

```cmd/sh
node --version
```

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

运行以下命令，获取中心的 _IoT 中心连接字符串_（记录以供稍后使用）：

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="run-the-sample-device"></a>运行示例设备

在本快速入门中，你将使用一个以 Node.js 编写的示例环境传感器作为 IoT 即插即用设备。 下面说明了如何安装和运行该设备：

1. 在所选目录中打开一个终端窗口。 执行以下命令，将[适用于 Node.js 的 Azure IoT 示例](https://github.com/azure-samples/azure-iot-samples-node) GitHub 存储库克隆到此位置：

    ```cmd/sh
    git clone https://github.com/azure-samples/azure-iot-samples-node
    ```

1. 此终端窗口现在将用作_设备_终端。 前往克隆的存储库的文件夹，然后导航到 /azure-iot-samples-node/digital-twins/Quickstarts/Device 文件夹  。 运行以下命令安装所有依赖项：

    ```cmd/sh
    npm install
    ```

1. 配置设备连接字符串： 

    ```cmd/sh
    set DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

1. 使用以下命令运行示例：

    ```cmd/sh
    node sample_device.js
    ```

1. 你会看到一些消息，指出设备已发送部分信息并报告其自身处于联机状态。 这表明设备已开始向中心发送遥测数据，现在可以接收命令和属性更新。 请不要关闭此终端，稍后需要在其中确认服务示例是否也能正常运行。

## <a name="run-the-sample-solution"></a>运行示例解决方案

在本快速入门中，你将使用以 Node.js 编写的示例 IoT 解决方案来与示例设备交互。

1. 打开另一个终端窗口（这将是_服务_终端）。 转到克隆的存储库所在的文件夹，然后导航到 **/azure-iot-samples-node/digital-twins/Quickstarts/Service** 文件夹。 运行以下命令安装所有依赖项：

    ```cmd/sh
    npm install
    ```

1. 配置 _IoT 中心连接字符串_，以允许服务连接到它：

    ```cmd/sh
    set IOTHUB_CONNECTION_STRING=<YourIoTHubConnectionString>
    ```

### <a name="read-a-property"></a>读取属性

1. 在设备终端连接_设备_时，你会看到以下消息，指示其已联机：

    ```cmd/sh
    reported state property as online
    ```

1. 在 /azure-iot-samples-node/digital-twins/Quickstarts/Service 文件夹中，打开文件 get_digital_twin.js   。 将 `<DEVICE_ID_GOES_HERE>` 占位符替换为你的设备 ID，然后保存该文件。

1. 转到_服务_终端，并使用以下命令来运行用于读取设备信息的示例：

    ```cmd/sh
    node get_digital_twin.js
    ```

1. 在_服务_终端输出中，滚动到 `environmentalSensor` 组件。 你会看到 `state` 属性已报告为 _online_：

    ```JSON
    "environmentalSensor": {
      "name": "environmentalSensor",
      "properties": {
        "state": {
          "reported": {
            "value": "online"
          }
        }
      }
    }
    ```

### <a name="update-a-writable-property"></a>更新可写属性

1. 打开文件 **update_digital_twin_property.js**。

1. 在该文件的开头，有一组使用大写占位符定义的常量。 将 `<DEVICE_ID_GOES_HERE>` 占位符替换为实际设备 ID，使用以下值更新其余常量，然后保存该文件：

    ```javascript
    const interfaceInstanceName = 'environmentalSensor';
    const propertyName = 'brightness';
    const propertyValue = 42;
    ```

1. 转到_服务_终端，并使用以下命令来运行用于更新属性的示例：

    ```cmd/sh
    node update_digital_twin_property.js
    ```

1. _服务_终端输出显示更新的设备信息。 滚动到 `environmentalSensor` 组件，可以看到新的亮度值 42。

    ```json
    "environmentalSensor": {
      "name": "environmentalSensor",
      "properties": {
        "brightness": {
          "desired": {
            "value": "42"
          }
        },
        "state": {
          "reported": {
            "value": "online"
          }
        }
      }
    }
    ```

1. 转到设备终端，将看到设备已接收更新： 

    ```cmd/sh
    Received an update for brightness: 42
    updated the property
    ```
2. 返回到_服务_终端，并运行以下命令再次获取设备信息，以确认属性已更新。
    
    ```cmd/sh
    node get_digital_twin.js
    ```

3. 在_服务_终端输出中的 `environmentalSensor` 组件下，你会看到报告了更新的亮度值。 注意：设备可能需要花费一段时间来完成更新。 可以不断重复此步骤，直到设备已实际处理属性更新为止。
    
    ```json
    "environmentalSensor": {
      "name": "environmentalSensor",
      "properties": {
        "brightness": {
          "reported": {
            "value": "42",
            "desiredState": {
              "code": 200,
              "version": 2,
              "description": "helpful descriptive text"
            }
          },
          "desired": {
            "value": "42"
          }
        },
        "state": {
          "reported": {
            "value": "online"
          }
        }
      }
    }
    ```

### <a name="invoke-a-command"></a>调用命令

1. 打开文件 **invoke_command.js**。

1. 在该文件的开头，将 `<DEVICE_ID_GOES_HERE>` 占位符替换为实际设备 ID。 使用以下值更新其余常量，然后保存该文件：

    ```javascript
    const interfaceInstanceName = 'environmentalSensor';
    const commandName = 'blink';
    const commandArgument = '<For the environmental sensor, this value does not matter. Any string will do.>'; 
    ```

1. 转到_服务_终端。 使用以下命令来运行用于调用该命令的示例：

    ```cmd/sh
    node invoke_command.js
    ```

1. _服务_终端中的输出应显示以下确认消息：

    ```cmd/sh
    invoking command blink on interface instanceenvironmentalSensor for device <device ID>...
    {
      "result": "helpful response text",
      "statusCode": 200,
      "requestId": "<some ID value>",
      "_response": "helpful response text"
    }
    ```

1. 转到设备终端，将看到命令已确认： 

    ```cmd/sh
    received command: blink for interfaceInstance: environmentalSensor
    acknowledgement succeeded.
    ```

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>后续步骤

本快速入门已介绍如何将 IoT 即插即用设备连接到 IoT 解决方案。 若要详细了解如何生成可与 IoT 即插即用设备交互的解决方案，请参阅：

> [!div class="nextstepaction"]
> [操作说明：连接到设备并与之交互](howto-develop-solution.md)
