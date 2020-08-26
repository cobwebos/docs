---
title: 与连接到 Azure IoT 解决方案的 IoT 即插即用预览版设备交互 (Node.js) | Microsoft Docs
description: 使用 Node.js 连接到已与 Azure IoT 解决方案连接的 IoT 即插即用预览版设备并与之交互。
author: elhorton
ms.author: elhorton
ms.date: 08/11/2020
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc, devx-track-javascript
ms.openlocfilehash: fd65dcc9ce0be07daa5848a0ac583cf795150e47
ms.sourcegitcommit: faeabfc2fffc33be7de6e1e93271ae214099517f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2020
ms.locfileid: "88184731"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-preview-device-thats-connected-to-your-solution-nodejs"></a>快速入门：与连接到解决方案的 IoT 即插即用预览版设备交互 (Node.js)

[!INCLUDE [iot-pnp-quickstarts-service-selector.md](../../includes/iot-pnp-quickstarts-service-selector.md)]

IoT 即插即用预览版简化了 IoT 的使用，无需了解底层设备实现，就能使用它来与某个设备的功能交互。 本快速入门介绍如何使用 Node.js 来连接和控制已与解决方案连接的 IoT 即插即用设备。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>先决条件

若要完成本快速入门，需要在开发计算机上使用 Node.js。 可以从 [nodejs.org](https://nodejs.org) 下载适用于多个平台的最新推荐版本。

可以使用以下命令验证开发计算机上 Node.js 当前的版本：

```cmd/sh
node --version
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

### <a name="clone-the-sdk-repository-with-the-sample-code"></a>使用示例代码克隆 SDK 存储库

服务 SDK 处于预览阶段，因此需要从 [Node SDK 的预览分支](https://github.com/Azure/azure-iot-sdk-node/tree/pnp-preview-refresh)克隆示例。 在所选文件夹中打开一个终端窗口。 运行以下命令，克隆[适用于 Node.js 的 Microsoft Azure IoT SDK](https://github.com/Azure/azure-iot-sdk-node) GitHub 存储库的 pnp-preview-refresh 分支：

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-node -b pnp-preview-refresh
```

## <a name="run-the-sample-device"></a>运行示例设备

在本快速入门中，可以使用一个以 Node.js 编写的示例恒温器设备作为 IoT 即插即用设备。 运行示例设备：

1. 打开终端窗口，然后导航到包含从 GitHub 克隆的适用于 Node.js 的 Microsoft Azure IoT SDK 存储库的本地文件夹。

1. 此终端窗口将用作设备终端。 前往克隆的存储库的文件夹，然后导航到 /azure-iot-sdk-node/device/samples/pnp 文件夹。 运行以下命令安装所有依赖项：

    ```cmd/sh
    npm install
    ```

1. 配置设备连接字符串：

    ```cmd/sh
    set IOTHUB_DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

1. 使用以下命令运行示例恒温器设备：

    ```cmd/sh
    node simple_thermostat.js
    ```

1. 你会看到一些消息，指出设备已发送部分信息并报告其自身处于联机状态。 这些消息表明设备已开始向中心发送遥测数据，现在可以接收命令和属性更新。 请不要关闭此终端，需要在其中确认服务示例是否在正常运行。

## <a name="run-the-sample-solution"></a>运行示例解决方案

在本快速入门中，你将使用以 Node.js 编写的示例 IoT 解决方案与你刚刚设置的示例设备进行交互。

1. 打开另一个终端窗口用作服务终端。 服务 SDK 处于预览阶段，因此需要从 [Node SDK 的预览分支](https://github.com/Azure/azure-iot-sdk-node/tree/pnp-preview-refresh)克隆示例：

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-node -b pnp-preview-refresh
    ```

1. 转到该克隆的存储库分支所在的文件夹，然后导航到 /azure-iot-sdk-node/digitaltwins/samples/service/javascript 文件夹。 运行以下命令安装所有依赖项：

    ```cmd/sh
    npm install
    ```

1. 为设备 ID 和 IoT 中心连接字符串配置环境变量：

    ```cmd/sh
    set IOTHUB_CONNECTION_STRING=<YourIOTHubConnectionString>
    set IOTHUB_DEVICE_ID=<Your device ID>
    ```

### <a name="read-a-property"></a>读取属性

1. 在设备终端运行示例恒温器设备时，你会看到以下消息，指示其已联机：

    ```cmd/sh
    properties have been reported for component
    sending telemetry message 0...
    ```

1. 转到**服务**终端，并使用以下命令来运行用于读取设备信息的示例：

    ```cmd/sh
    node get_digital_twin.js
    ```

1. 在服务终端输出中，请注意数字孪生体的响应。 你会看到所报告的设备的模型 ID 以及相关属性：

    ```json
    "$dtId": "mySimpleThermostat",
    "serialNumber": "123abc",
    "maxTempSinceLastReboot": 51.96167432818655,
    "$metadata": {
      "$model": "dtmi:com:example:Thermostat;1",
      "serialNumber": { "lastUpdateTime": "2020-07-09T14:04:00.6845182Z" },
      "maxTempSinceLastReboot": { "lastUpdateTime": "2020-07-09T14:04:00.6845182" }
    }
    ```

1. 以下代码片段显示了 get_digital_twin .js 中用于检索设备孪生的模型 ID 的代码：

    ```javascript
    console.log("Model Id: " + inspect(digitalTwin.$metadata.$model))
    ```

在本方案中，此代码输出 `Model Id: dtmi:com:example:Thermostat;1`。

### <a name="update-a-writable-property"></a>更新可写属性

1. 在代码编辑器中打开文件 update_digital_twin.js。

1. 查看示例代码。 你可以了解如何创建 JSON 修补程序来更新设备的数字孪生体。 在此示例中，代码将恒温器的温度替换为值 42：

    ```javascript
    const patch = [{
        op: 'add',
        path: '/targetTemperature',
        value: '42'
      }]
    ```

1. 在服务终端中，使用以下命令来运行用于更新属性的示例：

    ```cmd/sh
    node update_digital_twin.js
    ```

1. 在设备终端中，将看到设备已接收更新：

    ```cmd/sh
    The following properties will be updated for root interface:
    {
      targetTemperature: {
        value: 42,
        ac: 200,
        ad: 'Successfully executed patch for targetTemperature',
        av: 2
      }
    }
    updated the property
    Properties have been reported for component
    ```

1. 在服务终端中，运行以下命令以确认属性已更新：

    ```cmd/sh
    node get_digital_twin.js
    ```

1. 在服务终端输出的 `thermostat1` 组件下的数字孪生体响应中，你会看到所报告的已更新的目标温度。 设备可能需要花费一段时间来完成更新。 请重复此步骤，直到设备已处理属性更新为止：

    ```json
    targetTemperature: 42,
    ```

### <a name="invoke-a-command"></a>调用命令

1. 打开文件 invoke_command.js，并查看代码。

1. 转到**服务**终端。 使用以下命令来运行用于调用该命令的示例：

    ```cmd/sh
    set IOTHUB_COMMAND_NAME=getMaxMinReport
    set IOTHUB_COMMAND_PAYLOAD=commandpayload
    node invoke_command.js
    ```

1. 服务终端中的输出显示以下确认消息：

    ```cmd/sh
    {
        xMsCommandStatuscode: 200,  
        xMsRequestId: 'ee9dd3d7-4405-4983-8cee-48b4801fdce2',  
        connection: 'close',  'content-length': '18',  
        'content-type': 'application/json; charset=utf-8',  
        date: 'Thu, 09 Jul 2020 15:05:14 GMT',  
        server: 'Microsoft-HTTPAPI/2.0',  vary: 'Origin',  
        body: 'min/max response'
    }
    ```

1. 在设备终端中，将看到命令已确认：

    ```cmd/sh
    MaxMinReport commandpayload
    Response to method 'getMaxMinReport' sent successfully.
    ```

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>后续步骤

本快速入门已介绍如何将 IoT 即插即用设备连接到 IoT 解决方案。 若要详细了解 IoT 即插即用设备模型，请参阅：

> [!div class="nextstepaction"]
> [IoT 即插即用预览版建模开发人员指南](concepts-developer-guide.md)
