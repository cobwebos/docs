---
title: 与连接到 Azure IoT 解决方案的 IoT 即插即用预览版设备交互 | Microsoft Docs
description: 使用 Java 连接到已与 Azure IoT 解决方案连接的 IoT 即插即用预览版设备并与之交互。
author: baanders
ms.author: baanders
ms.date: 12/27/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 217dfe125dcacae5d50645275b20421a23169cb9
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/31/2019
ms.locfileid: "75550841"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-preview-device-thats-connected-to-your-solution-java"></a>快速入门：与已连接到解决方案的 IoT 即插即用预览版设备交互 (Java)

[!INCLUDE [iot-pnp-quickstarts-3-selector.md](../../includes/iot-pnp-quickstarts-3-selector.md)]

IoT 即插即用预览版简化了 IoT 的使用，无需了解底层设备实现，就能使用它来与某个设备的功能交互。 本快速入门介绍如何使用 Java 来连接和控制已与解决方案连接的 IoT 即插即用设备。

## <a name="prerequisites"></a>必备条件

若要完成本快速入门，需要在开发计算机上使用 Java SE 8。 还需要安装 Maven 3。

有关设置的详细信息，请参阅在适用于 Java 的 Microsoft Azure IoT 设备 SDK 中[准备开发环境](https://github.com/Azure/azure-iot-sdk-java/blob/preview/doc/java-devbox-setup.md)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

运行以下命令，获取中心的 _IoT 中心连接字符串_（记录以供稍后使用）：

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="run-the-sample-device"></a>运行示例设备

在本快速入门中，你将使用一个以 Java 编写的示例环境传感器作为 IoT 即插即用设备。 下面说明了如何安装和运行该设备：

1. 在所选目录中打开一个终端窗口。 执行以下命令，将[适用于 Java 的 Azure IoT 示例](https://github.com/Azure-Samples/azure-iot-samples-java) GitHub 存储库克隆到此位置：

    ```cmd/sh
    git clone https://github.com/Azure-Samples/azure-iot-samples-java
    ```

1. 此终端窗口现在将用作_设备_终端。 转到克隆的存储库所在的文件夹，然后导航到 /azure-iot-samples-java/digital-twin/Samples/device/JdkSample 文件夹  。 安装所需的库，并运行以下命令生成模拟设备应用程序：

    ```cmd/sh
    mvn clean install -DskipTests
    ```

1. 配置设备连接字符串： 

    ```cmd/sh
    set DIGITAL_TWIN_DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

1. 运行以下命令，从该设备文件夹运行示例。

    ```cmd/sh
    java -jar environmental-sensor-sample\target\environmental-sensor-sample-with-deps.jar
    ```

1. 你会看到一些消息，分别表示设备已连接、正在执行各种设置步骤以及正在等待服务更新，之后会看到遥测日志。 这表明设备现在可以接收命令和属性更新，并且已开始向中心发送遥测数据。 在执行后续步骤时，保持示例处于运行状态。 请不要关闭此终端，稍后需要在其中确认服务示例是否也能正常运行。

## <a name="run-the-sample-solution"></a>运行示例解决方案

在本快速入门中，你将使用以 Java 编写的示例 IoT 解决方案来与示例设备交互。

1. 打开另一个终端窗口（这将是_服务_终端）。 转到克隆的存储库所在的文件夹，然后导航到 /azure-iot-samples-java\digital-twin\Samples\service\JdkSample 文件夹  。

1. 安装所需的库，并运行以下命令生成服务示例：

    ```cmd/sh
    mvn clean install -DskipTests
    ```

1. 配置 IoT 中心连接字符串和设备 ID，以允许服务可以连接到这两者   ：

    ```cmd/sh
    set IOTHUB_CONNECTION_STRING=<YourIoTHubConnectionString>
    set DEVICE_ID=<YourDeviceID>
    ```

### <a name="read-a-property"></a>读取属性

1. 当连接到其终端的设备时，会出现如下一条输出消息指示其在线状态  。 指示设备是否在线的 `state` 属性为 true  ：

    ```cmd/sh
    State of environmental sensor was set to true
    ```

1. 转到服务终端，并使用以下命令来运行用于读取设备信息的服务示例  ：

    ```cmd/sh
    java -jar get-digital-twin/target/get-digital-twin-with-deps.jar
    ```

1. 在_服务_终端输出中，滚动到 `environmentalSensor` 组件。 你会看到 `state` 属性已报告为 true  ：
    ```JSON
    "environmentalSensor" : {
      "name" : "environmentalSensor",
      "properties" : {
        "state" : {
          "reported" : {
            "value" : true,
            "desiredState" : null
          },
          "desired" : null
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

1. 使用以下命令运行服务示例以更新此属性：

    ```cmd/sh
    java -jar update-digital-twin/target/update-digital-twin-with-deps.jar
    ```

1. _服务_终端输出显示更新的设备信息。 滚动到 `environmentalSensor` 组件，可以看到新的亮度值 42。

    ```json
    "environmentalSensor": {
        "name": "environmentalSensor",
        "properties": {
            "brightness": {
                "reported": null,
                "desired": {
                    "value": "42"
                }
            },
    ```

1. 转到设备终端，将看到设备已接收更新： 

    ```cmd/sh
    OnPropertyUpdate called: propertyName=brightness, reportedValue=null, desiredVersion=2, desiredValue={"value":"42"}
    Report property: propertyName=brightness, reportedValue={"value":"42"}, desiredVersion=2 was DIGITALTWIN_CLIENT_OK
    ```
2. 返回到_服务_终端，并运行以下命令再次获取设备信息，以确认属性已更新。
    
    ```cmd/sh
    java -jar get-digital-twin/target/get-digital-twin-with-deps.jar
    ```
3. 在_服务_终端输出中的 `environmentalSensor` 组件下，你会看到报告了更新的亮度值。 注意：设备可能需要花费一段时间来完成更新。 可以不断重复此步骤，直到设备已实际处理属性更新为止。
    
    ```json
    "environmentalSensor" : {
      "name" : "environmentalSensor",
      "properties" : {
        "brightness" : {
          "reported" : {
            "value" : {
              "value" : "42"
            },
            "desiredState" : {
              "code" : 200,
              "version" : 2,
              "description" : "OK"
            }
          },
          "desired" : {
            "value" : "42"
          }
        },
        "state" : {
          "reported" : {
            "value" : true,
            "desiredState" : null
          },
          "desired" : null
        }
      }
    },       
    ```

### <a name="invoke-a-command"></a>调用命令

1. 转到服务终端，并设置以下变量来定义要调用哪个命令  ：
    ```cmd/sh
    set INTERFACE_INSTANCE_NAME=environmentalSensor
    set COMMAND_NAME=blink
    set PAYLOAD=10
    ```

1. 使用以下命令来运行用于调用该命令的服务示例：

    ```cmd/sh
    java -jar invoke-digital-twin-command/target/invoke-digital-twin-command-with-deps.jar
    ```

1. _服务_终端中的输出应显示以下确认消息：

    ```cmd/sh
    Invoking blink on device <YourDeviceID> with interface instance name environmentalSensor
    Command invoked on the device successfully, the returned status was 200 and the request id was <some ID value>
    The returned PAYLOAD was
    ```

1. 转到设备终端，将看到命令已确认： 

    ```cmd/sh
    OnCommandReceived called: commandName=blink, requestId=<some ID value>, commandPayload="10"
    EnvironmentalSensor is blinking every 10 seconds.
    ```

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>后续步骤

本快速入门已介绍如何将 IoT 即插即用设备连接到 IoT 解决方案。 若要详细了解如何生成可与 IoT 即插即用设备交互的解决方案，请参阅：

> [!div class="nextstepaction"]
> [操作说明：连接到设备并与之交互](howto-develop-solution.md)
