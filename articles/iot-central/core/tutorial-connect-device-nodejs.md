---
title: 教程 - 将通用 Node.js 客户端应用连接到 Azure IoT Central | Microsoft Docs
description: 本教程介绍如何以设备开发人员的身份将运行 Node.js 客户端应用的设备连接到 Azure IoT Central 应用程序。 通过导入设备功能模型创建设备模板，并添加视图来与连接设备交互
author: dominicbetts
ms.author: dobett
ms.date: 03/24/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mqtt
ms.openlocfilehash: a8c5d9479585c0a519d0ad05a4d73f3f15b21287
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758198"
---
# <a name="tutorial-create-and-connect-a-client-application-to-your-azure-iot-central-application-nodejs"></a>教程：创建客户端应用程序并将其连接到 Azure IoT Central 应用程序 (Node.js)

[!INCLUDE [iot-central-selector-tutorial-connect](../../../includes/iot-central-selector-tutorial-connect.md)]

本文适用于解决方案构建者和设备开发人员。 

本教程介绍如何以设备开发人员的身份将 Node.js 客户端应用程序连接到 Azure IoT Central 应用程序。 该 Node.js 应用程序模拟环境传感器设备的行为。 你将使用一个示例设备功能模型在 IoT Central 中创建设备模板。   此外，你将向该设备模板中添加视图，使操作员能够与设备进行交互。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 导入设备功能模型以创建设备模板。
> * 将默认视图和自定义视图添加到设备模板。
> * 发布设备模板，并将真实设备添加到 IoT Central 应用程序。
> * 创建并运行 Node.js 设备代码，并查看它是否可连接到 IoT Central 应用程序。
> * 查看从设备发送的模拟遥测数据。
> * 使用视图管理设备属性。
> * 调用同步和异步命令来控制设备。

## <a name="prerequisites"></a>先决条件

若要完成本文中的步骤，需要以下各项：

* 使用“自定义应用程序”模板创建的 Azure IoT Central 应用程序。  有关详细信息，请参阅[创建应用程序快速入门](quick-deploy-iot-central.md)。
* 安装了 [Node.js](https://nodejs.org/) 10.0.0 或更高版本的开发计算机。 若要检查版本，可以在命令行中运行 `node --version`。 本教程中的说明假设在 Windows 命令提示符下运行 **node** 命令。 但是，可以在许多其他的操作系统上使用 Node.js。

[!INCLUDE [iot-central-add-environmental-sensor](../../../includes/iot-central-add-environmental-sensor.md)]

### <a name="create-a-nodejs-application"></a>创建 Node.js 应用程序

以下步骤说明如何创建一个 Node.js 客户端应用程序，用于连接已添加到应用程序的真实设备。 此 Node.js 应用程序模拟真实设备的行为。

1. 在命令行环境中，导航到前面创建的 `environmental-sensor` 文件夹。

1. 若要初始化 Node.js 项目并安装所需的依赖项，请运行以下命令 - 运行 `npm init` 时请接受所有默认选项：

    ```cmd/sh
    npm init
    npm install azure-iot-device azure-iot-device-mqtt azure-iot-provisioning-device-mqtt azure-iot-security-symmetric-key --save
    ```

1. 在 `environmental-sensor` 文件夹中创建名为 **environmentalSensor.js** 的文件。

1. 在 **environmentalSensor.js** 文件的开头添加以下 `require` 语句：

    ```javascript
    "use strict";

    // Use the Azure IoT device SDK for devices that connect to Azure IoT Central.
    var iotHubTransport = require('azure-iot-device-mqtt').Mqtt;
    var Client = require('azure-iot-device').Client;
    var Message = require('azure-iot-device').Message;
    var ProvisioningTransport = require('azure-iot-provisioning-device-mqtt').Mqtt;
    var SymmetricKeySecurityClient = require('azure-iot-security-symmetric-key').SymmetricKeySecurityClient;
    var ProvisioningDeviceClient = require('azure-iot-provisioning-device').ProvisioningDeviceClient;
    ```

1. 将以下变量声明添加到该文件：

    ```javascript
    var provisioningHost = 'global.azure-devices-provisioning.net';
    var idScope = '{your Scope ID}';
    var registrationId = '{your Device ID}';
    var symmetricKey = '{your Primary Key}';
    var provisioningSecurityClient = new SymmetricKeySecurityClient(registrationId, symmetricKey);
    var provisioningClient = ProvisioningDeviceClient.create(provisioningHost, idScope, new ProvisioningTransport(), provisioningSecurityClient);
    var hubClient;

    var targetTemperature = 0;
    var ledOn = true;
    ```

    将 `{your Scope ID}`、`{your Device ID}` 和 `{your Primary Key}` 占位符更新为前面记下的值。 此示例将 `targetTemperature` 初始化为零。可以使用设备的当前读数，或者使用设备孪生的值。

1. 若要向 Azure IoT Central 应用程序发送模拟的遥测数据，请将以下函数添加到文件中：

    ```javascript
    // Send simulated device telemetry.
    function sendTelemetry() {
      var temp = targetTemperature + (Math.random() * 15);
      var humid = 70 + (Math.random() * 10);
      var data = JSON.stringify({
        temp: temp,
        humid: humid,
        });
      var message = new Message(data);
      hubClient.sendEvent(message, (err, res) => console.log(`Sent message: ${message.getData()}` +
        (err ? `; error: ${err.toString()}` : '') +
        (res ? `; status: ${res.constructor.name}` : '')));
    }
    ```

    遥测项的名称（`temp` 和 `humid`）必须与设备模板中使用的名称相匹配。

1. 若要向 Azure IoT Central 应用程序发送设备孪生属性，请将以下函数添加到文件中：

    ```javascript
    // Send device twin reported properties.
    function sendDeviceProperties(twin, properties) {
      twin.properties.reported.update(properties, (err) => console.log(`Sent device properties: ${JSON.stringify(properties)}; ` +
        (err ? `error: ${err.toString()}` : `status: success`)));
    }
    ```

    IoT Central 使用设备孪生来同步设备与 IoT Central 应用程序之间的属性值。 设备属性值使用设备孪生报告属性。 可写属性使用设备孪生报告属性和所需属性。

1. 若要定义和处理设备响应的可写属性，请添加以下代码：

    ```javascript
    // Add any writeable properties your device supports,
    // mapped to a function that's called when the writeable property
    // is updated in the IoT Central application.
    var writeableProperties = {
      'name': (newValue, callback) => {
          setTimeout(() => {
            callback(newValue, 'completed');
          }, 1000);
      },
      'brightness': (newValue, callback) => {
        setTimeout(() => {
            callback(newValue, 'completed');
        }, 5000);
      }
    };

    // Handle writeable property updates that come from IoT Central via the device twin.
    function handleWriteablePropertyUpdates(twin) {
      twin.on('properties.desired', function (desiredChange) {
        for (let setting in desiredChange) {
          if (writeableProperties[setting]) {
            console.log(`Received setting: ${setting}: ${desiredChange[setting].value}`);
            writeableProperties[setting](desiredChange[setting].value, (newValue, status) => {
              var patch = {
                [setting]: {
                  value: newValue,
                  status: status,
                  desiredVersion: desiredChange.$version
                }
              }
              sendDeviceProperties(twin, patch);
            });
          }
        }
      });
    }
    ```

    当操作员在 IoT Central 应用程序中设置可写属性时，该应用程序会使用设备孪生所需属性将值发送到设备。 然后，设备使用设备孪生报告属性做出响应。 IoT Central 在收到报告属性值时会使用 synced 状态更新属性视图。 

    属性的名称（`name` 和 `brightness`）必须与设备模板中使用的名称相匹配。

1. 添加以下代码来处理从 IoT Central 应用程序发送的命令：

    ```javascript
    // Setup command handlers
    function setupCommandHandlers(twin) {

      // Handle synchronous LED blink command with request and response payload.
      function onBlink(request, response) {
        console.log('Received synchronous call to blink');
        var responsePayload = {
          status: 'Blinking LED every ' + request.payload  + ' seconds'
        }
        response.send(200, responsePayload, (err) => {
          if (err) {
            console.error('Unable to send method response: ' + err.toString());
          } else {
            console.log('Blinking LED every ' + request.payload  + ' seconds');
          }
        });
      }

      // Handle synchronous LED turn on command
      function turnOn(request, response) {
        console.log('Received synchronous call to turn on LED');
        if(!ledOn){
          console.log('Turning on the LED');
          ledOn = true;
        }
        response.send(200, (err) => {
          if (err) {
            console.error('Unable to send method response: ' + err.toString());
          }
        });
      }

      // Handle synchronous LED turn off command
      function turnOff(request, response) {
        console.log('Received synchronous call to turn off LED');
        if(ledOn){
          console.log('Turning off the LED');
          ledOn = false;
        }
        response.send(200, (err) => {
          if (err) {
            console.error('Unable to send method response: ' + err.toString());
          }
        });
      }

      // Handle asynchronous sensor diagnostics command with response payload.
      function diagnostics(request, response) {
        console.log('Starting asynchronous diagnostics run...');
        response.send(202, (err) => {
          if (err) {
            console.error('Unable to send method response: ' + err.toString());
          } else {
            var repetitions = 3;
            var intervalID = setInterval(() => {
              console.log('Generating diagnostics...');
              if (--repetitions === 0) {
                clearInterval(intervalID);
                var properties = {
                  rundiagnostics: {
                    value: 'Diagnostics run complete at ' + new Date().toLocaleString()
                  }
                };
                sendDeviceProperties(twin, properties);
              }
            }, 2000);
          }
        });
      }

      hubClient.onDeviceMethod('blink', onBlink);
      hubClient.onDeviceMethod('turnon', turnOn);
      hubClient.onDeviceMethod('turnoff', turnOff);
      hubClient.onDeviceMethod('rundiagnostics', diagnostics);
    }
    ```

    命令的名称（`blink`、`turnon`、`turnoff` 和 `rundiagnostics`）必须与设备模板中使用的名称相匹配。

    目前，IoT Central 不使用设备功能模型中定义的响应架构。 对于同步命令，响应有效负载可以是任何有效 JSON。 对于异步命令，在工作完成后，设备应立即返回 202 响应，后接报告属性更新。 报告属性更新的格式为：

    ```json
    {
      [command name] : {
        value: 'response message'
      }
    }
    ```

    操作员可以在命令历史记录中查看响应有效负载。

1. 添加以下代码以完成与 Azure IoT Central 的连接，并在客户端代码中挂接函数：

    ```javascript
    // Handle device connection to Azure IoT Central.
    var connectCallback = (err) => {
      if (err) {
        console.log(`Device could not connect to Azure IoT Central: ${err.toString()}`);
      } else {
        console.log('Device successfully connected to Azure IoT Central');

        // Send telemetry to Azure IoT Central every 1 second.
        setInterval(sendTelemetry, 1000);

        // Get device twin from Azure IoT Central.
        hubClient.getTwin((err, twin) => {
          if (err) {
            console.log(`Error getting device twin: ${err.toString()}`);
          } else {
            // Send device properties once on device start up.
            var properties = {
              state: 'true'
            };
            sendDeviceProperties(twin, properties);

            handleWriteablePropertyUpdates(twin);

            setupCommandHandlers(twin);
          }
        });
      }
    };

    // Start the device (register and connect to Azure IoT Central).
    provisioningClient.register((err, result) => {
      if (err) {
        console.log('Error registering device: ' + err);
      } else {
        console.log('Registration succeeded');
        console.log('Assigned hub=' + result.assignedHub);
        console.log('DeviceId=' + result.deviceId);
        var connectionString = 'HostName=' + result.assignedHub + ';DeviceId=' + result.deviceId + ';SharedAccessKey=' + symmetricKey;
        hubClient = Client.fromConnectionString(connectionString, iotHubTransport);

        hubClient.open(connectCallback);
      }
    });
    ```

## <a name="run-your-nodejs-application"></a>运行 Node.js 应用程序

若要启动设备客户端应用程序，请在命令行环境中运行以下命令：

```cmd/sh
node environmentalSensor.js
```

可以看到设备连接到 Azure IoT Central 应用程序并开始发送遥测数据：

![运行客户端应用程序](media/tutorial-connect-device-nodejs/run-application.png)

[!INCLUDE [iot-central-monitor-environmental-sensor](../../../includes/iot-central-monitor-environmental-sensor.md)]

可以看到设备如何对命令和属性更新做出响应：

![观察客户端应用程序](media/tutorial-connect-device-nodejs/run-application-2.png)

## <a name="next-steps"></a>后续步骤

作为设备开发人员，现在你已了解了有关使用 Node.js 创建设备的基础知识，建议执行的后续步骤是：

- 阅读[将 MXChip IoT DevKit 设备连接到 Azure IoT Central 应用程序](./howto-connect-devkit.md)操作方法文章，了解如何将实际设备连接到 IoT Central。
- 阅读[连接到 Azure IoT Central](./concepts-get-connected.md)，详细了解如何向 IoT Central 注册设备以及 IoT Central 如何保护设备连接。

若要继续浏览 IoT Central 系列教程并详细了解如何构建 IoT Central 解决方案，请参阅：

> [!div class="nextstepaction"]
> [创建网关设备模板](./tutorial-define-gateway-device-type.md)
