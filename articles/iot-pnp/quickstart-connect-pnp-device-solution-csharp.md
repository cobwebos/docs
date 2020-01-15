---
title: 与连接到 Azure IoT 解决方案的 IoT 即插即用预览版设备交互 | Microsoft Docs
description: 使用 C# (.NET) 连接到已与 Azure IoT 解决方案连接的 IoT 即插即用预览版设备并与之交互。
author: baanders
ms.author: baanders
ms.date: 12/30/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 9e8bc6c4ad7ed852ddaae2e193b91887fcd92e47
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/31/2019
ms.locfileid: "75550768"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-preview-device-thats-connected-to-your-solution-c"></a>快速入门：与已连接到解决方案的 IoT 即插即用预览版设备交互 (C#)

[!INCLUDE [iot-pnp-quickstarts-3-selector.md](../../includes/iot-pnp-quickstarts-3-selector.md)]

IoT 即插即用预览版简化了 IoT 的使用，无需了解底层设备实现，就能使用它来与某个设备的功能交互。 本快速入门介绍如何使用 C#（结合使用 .NET）来连接和控制已与解决方案连接的 IoT 即插即用设备。

## <a name="prerequisites"></a>必备条件

若要完成本快速入门，需要在开发计算机上安装 .NET Core（2.x.x 或 3.x.x）。 可以从[下载 .NET Core](https://dotnet.microsoft.com/download/dotnet-core/) 下载适用于多平台的 .NET Core SDK 首选版本。

可以通过在本地终端窗口中运行以下命令来验证开发计算机上的 .NET 版本： 

```cmd/sh
dotnet --version
```

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

运行以下命令，获取中心的 _IoT 中心连接字符串_（记录以供稍后使用）：

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="run-the-sample-device"></a>运行示例设备

在本快速入门中，你将使用一个以 C# 编写的示例环境传感器作为 IoT 即插即用设备。 下面说明了如何安装和运行该设备：

1. 在所选目录中打开一个终端窗口。 执行以下命令，将[适用于 C# (.NET) 的 Azure IoT 示例](https://github.com/Azure-Samples/azure-iot-samples-csharp) GitHub 存储库克隆到此位置：

    ```cmd/sh
    git clone https://github.com/Azure-Samples/azure-iot-samples-csharp
    ```

1. 此终端窗口现在将用作_设备_终端。 前往克隆的存储库的文件夹，并导航到 azure-iot-samples-csharp/digitaltwin/Samples/device/EnvironmentalSensorSample 文件夹  。

1. 配置设备连接字符串： 

    ```cmd/sh
    set DIGITAL_TWIN_DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

1. 生成所需的包，并使用如下命令运行示例：

    ```cmd\sh
        dotnet run
    ```

1. 你会看到有消息显示设备已成功注册并正在等待云中的更新。 这表明设备现在可以接收命令和属性更新，并且已开始向中心发送遥测数据。 请不要关闭此终端，稍后需要在其中确认服务示例是否也能正常运行。

## <a name="run-the-sample-solution"></a>运行示例解决方案

在本快速入门中，你将使用以 C# 编写的示例 IoT 解决方案来与示例设备交互。

1. 打开另一个终端窗口（这将是_服务_终端）。 转到克隆的存储库所在的文件夹，然后导航到 /azure-iot-samples-csharp/digitaltwin/Samples/service 文件夹  。

1. 配置 IoT 中心连接字符串和设备 ID，以允许服务可以连接到这两者   ：

    ```cmd/sh
    set IOTHUB_CONNECTION_STRING=<YourIoTHubConnectionString>
    set DEVICE_ID=<YourDeviceID>
    ```

### <a name="read-a-property"></a>读取属性

1. 在设备终端连接_设备_时，你会看到以下消息，指示其已联机：

    ```cmd/sh
    Waiting to receive updates from cloud...
    ```

1. 转到服务终端，并使用以下命令来运行用于读取设备信息的示例  ：

    ```cmd/sh
    cd GetDigitalTwin
    dotnet run
    ```

1. 在_服务_终端输出中，滚动到 `environmentalSensor` 组件。 可以看到用于指示设备是否在线的 `state` 属性被报告为 true  ：

    ```JSON
    "environmentalSensor": {
      "name": "environmentalSensor",
      "properties": {
        "state": {
          "reported": {
            "value": true
          }
        }
      }
    }
    ```

### <a name="update-a-writable-property"></a>更新可写属性

1. 转到服务终端，并设置以下变量来定义要更新哪个属性  ：
    ```cmd/sh
    set INTERFACE_INSTANCE_NAME=environmentalSensor
    set PROPERTY_NAME=brightness
    set PROPERTY_VALUE=42
    ```

1. 使用以下命令运行此示例以更新此属性：

    ```cmd/sh
    cd ..\UpdateProperty
    dotnet run
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
            "value": true
          }
        }
      }
    }
    ```

1. 转到设备终端，将看到设备已接收更新： 

    ```cmd/sh
    Received updates for property 'brightness'
    Desired brightness = '"42"'.
    Reported brightness = ''.
    Version is '2'.
    Sent pending status for brightness property.
    Sent completed status for brightness property.
    ```
2. 返回到服务终端，并运行以下命令再次获取设备信息，以确认属性已更新  。
    
    ```cmd/sh
    cd ..\GetDigitalTwin
    dotnet run
    ```
3. 在_服务_终端输出中的 `environmentalSensor` 组件下，你会看到报告了更新的亮度值。 注意：设备可能需要花费一段时间来完成更新。 可以不断重复此步骤，直到设备已实际处理属性更新为止。
    
    ```json
    "environmentalSensor": {
      "name": "environmentalSensor",
      "properties": {
        "brightness": {
          "desired": {
            "value": "42"
          },
          "reported": {
            "value": "42",
            "desiredState": {
              "code": 200,
              "description": "Request completed",
              "version": 2
            }
          }
        },
        "state": {
          "reported": {
            "value": true
          }
        }
      }
    },
    ```

### <a name="invoke-a-command"></a>调用命令

1. 转到服务终端，并设置以下变量来定义要调用哪个命令  ：
    ```cmd/sh
    set INTERFACE_INSTANCE_NAME=environmentalSensor
    set COMMAND_NAME=blink
    ```

1. 使用以下命令来运行用于调用该命令的示例：

    ```cmd/sh
    cd ..\InvokeCommand
    dotnet run
    ```

1. _服务_终端中的输出应显示以下确认消息：

    ```cmd/sh
    Invoking blink on device <YourDeviceID> with interface instance name environmentalSensor
    Command blink invoked on the device successfully, the returned status was 200 and the request id was <some ID value>
    The returned payload was
    {"description": "abc"}
    Enter any key to finish
    ```

1. 转到设备终端，将看到命令已确认： 

    ```cmd/sh
    Command - blink was invoked from the service
    Data - null
    Request Id - <some ID value>.
    ```

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>后续步骤

本快速入门已介绍如何将 IoT 即插即用设备连接到 IoT 解决方案。 若要详细了解如何生成可与 IoT 即插即用设备交互的解决方案，请参阅：

> [!div class="nextstepaction"]
> [操作说明：连接到设备并与之交互](howto-develop-solution.md)
