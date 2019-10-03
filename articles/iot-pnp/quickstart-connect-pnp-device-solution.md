---
title: 与连接到 Azure IoT 解决方案的 IoT 即插即用预览版设备交互 | Microsoft Docs
description: 使用 Node.js 连接到已与 Azure IoT 解决方案连接的 IoT 即插即用预览版设备并与之交互。
author: miagdp
ms.author: miag
ms.date: 08/02/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: a082e4b7896b317bf2b28971d3693bada95a3445
ms.sourcegitcommit: b8578b14c8629c4e4dea4c2e90164e42393e8064
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/09/2019
ms.locfileid: "70806554"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-preview-device-thats-connected-to-your-solution"></a>快速入门：与已连接到解决方案的 IoT 即插即用预览版设备交互

IoT 即插即用预览版简化了 IoT 的使用，无需了解底层设备实现，就能使用它来与某个设备的功能交互。 本快速入门介绍如何使用 Node.js 来连接和控制已与解决方案连接的 IoT 即插即用设备。

## <a name="prerequisites"></a>先决条件

从 [nodejs.org](https://nodejs.org) 下载并安装 Node.js。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prepare-an-iot-hub"></a>准备 IoT 中心

Azure 订阅中还需要有一个 Azure IoT 中心才能完成本快速入门。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

> [!NOTE]
> 在公共预览版期间，IoT 即插即用功能仅适用于在美国中部、欧洲北部和日本东部区域中创建的 IoT 中心    。

添加适用于 Azure CLI 的 Microsoft Azure IoT 扩展：

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

运行以下命令，在 IoT 中心创建设备标识。 将 **YourIoTHubName** 和 **YourDevice** 替换为实际名称。 如果你没有 IoT 中心，请遵照[此处的说明](../iot-hub/iot-hub-create-using-cli.md)创建一个：

```azurecli-interactive
az iot hub device-identity create --hub-name [YourIoTHubName] --device-id [YourDevice]
```

运行以下命令，获取刚注册设备的设备连接字符串： 

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name [YourIoTHubName] --device-id [YourDevice] --output table
```

运行以下命令，获取中心的 IoT 中心连接字符串： 

```azurecli-interactive
az iot hub show-connection-string --hub-name [YourIoTHubName] --output table
```

## <a name="connect-your-device"></a>连接设备

在本快速入门中，你将使用一个以 Node.js 编写的示例环境传感器作为 IoT 即插即用设备。 下面说明了如何安装和运行该设备：

1. 克隆 GitHub 存储库：

    ```cmd/sh
    git clone https://github.com/azure-samples/azure-iot-samples-node
    ```

1. 在终端中转到克隆的存储库的根文件夹，导航到 **/azure-iot-samples-node/digital-twins/Quickstarts/Device** 文件夹，然后运行以下命令安装所有依赖项：

    ```cmd/sh
    npm install
    ```

1. 配置设备连接字符串： 

    ```cmd/sh
    set DEVICE_CONNECTION_STRING=<your device connection string>
    ```

1. 使用以下命令运行示例：

    ```cmd/sh
    node sample_device.js
    ```

1. 此时会有消息指出设备已发送遥测数据及其属性。 设备现在可以接收命令和属性更新。 请不要关闭此终端，稍后需要在其中确认服务示例是否也能正常运行。

## <a name="build-the-solution"></a>生成解决方案

在本快速入门中，你将使用以 Node.js 编写的示例 IoT 解决方案来与示例设备交互。

1. 打开另一个终端。 转到克隆的存储库所在的文件夹，然后导航到 **/azure-iot-samples-node/digital-twins/Quickstarts/Service** 文件夹。 运行以下命令安装所有依赖项：

    ```cmd/sh
    npm install
    ```

1. 配置中心连接字符串： 

    ```cmd/sh
    set IOTHUB_CONNECTION_STRING=<your hub connection string>
    ```

### <a name="read-a-property"></a>读取属性

1. 在终端中连接设备时，会看到以下消息：

    ```cmd/sh
    reported state property as online
    ```

1. 打开文件 **get_digital_twin.js**。 将 `deviceID` 替换为你的设备 ID，然后保存该文件。

1. 转到运行服务示例时打开的终端，并运行以下命令：

    ```cmd/sh
    node get_digital_twin.js
    ```

1. 在输出中的 _environmentalSensor_ 组成部分下，将看到报告了相同的状态：

    ```JSON
    reported state property as online
    ```

### <a name="update-a-writable-property"></a>更新可写属性

1. 打开文件 **update_digital_twin_property.js**。

1. 在该文件的开头，有一组使用大写占位符定义的常量。 将 **deviceID** 替换为实际设备 ID，使用以下值更新常量，然后保存该文件：

    ```javascript
    const componentName = 'environmentalSensor';
    const propertyName = 'brightness';
    const propertyValue = 60;
    ```

1. 转到运行服务示例时打开的终端，并使用以下命令运行示例：

    ```cmd/sh
    node update_digital_twin_property.js
    ```

1. 在终端中，将看到与设备关联的数字孪生信息。 找到组成部分 _environmentalSensor_，将看到新的亮度值 60。

    ```json
        "environmentalSensor": {
        "name": "environmentalSensor",
        "properties": {
          "brightness": {
            "reported": {
              "value": 60,
              "desiredState": {
                "code": 200,
                "version": 14,
                "description": "helpful descriptive text"
              }
            },
            "desired": {
              "value": 60
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
    Received an update for brightness: 60
    updated the property
    ```
2. 返回到服务终端，再次运行以下命令，以确认属性是否已更新。 
    
    ```cmd/sh
    node get_digital_twin.js
    ```
3. 在输出中的 environmentalSensor 组成部分下，将看到报告了更新的亮度。 注意：设备可能需要花费一段时间来完成更新。 可以不断重复此步骤，直到设备已实际处理属性更新为止。
    
    ```json
      "brightness": {
        "reported": {
          "value": 60,
          }
       }
    ```

### <a name="invoke-a-command"></a>调用命令

1. 打开文件 **invoke_command.js**。

1. 在该文件的开头，将 `deviceID` 替换为实际设备 ID。 使用以下值更新常量，然后保存该文件：

    ```javascript
    const interfaceInstanceName = 'environmentalSensor';
    const commandName = 'blink';
    ```

1. 转到运行服务示例时打开的终端。 使用以下命令运行示例：

    ```cmd/sh
    node invoke_command.js
    ```

1. 如果命令成功，终端中会显示如下所示的输出：

    ```cmd/sh
    invoking command blink on component environmentalSensor for device test...
    {
      "result": "helpful response text",
      "statusCode": 200,
      "requestId": "33e536d3-14f7-4105-88f3-629b9933851c",
      "_response": "helpful response text"
    }
    ```

1. 转到设备终端，将看到命令已确认： 

    ```cmd/sh
    received command: blink for component: environmentalSensor
    acknowledgement succeeded.
    ```

## <a name="clean-up-resources"></a>清理资源

如果你打算继续学习后续文章，可以保留本快速入门中所用的资源。 否则，可以删除为本快速入门创建的资源，以免产生额外的费用。

若要删除中心和注册的设备，请使用 Azure CLI 完成以下步骤：

```azurecli-interactive
az group delete --name <Your group name>
```

如果只需删除已注册到 IoT 中心的设备，请使用 Azure CLI 完成以下步骤：

```azurecli-interactive
az iot hub device-identity delete --hub-name [YourIoTHubName] --device-id [YourDevice]
```

## <a name="next-steps"></a>后续步骤

本快速入门已介绍如何将 IoT 即插即用设备连接到 IoT 解决方案。 若要详细了解如何生成可与 IoT 即插即用设备交互的解决方案，请参阅：

> [!div class="nextstepaction"]
> [操作说明：连接到设备并与之交互](howto-develop-solution.md)
