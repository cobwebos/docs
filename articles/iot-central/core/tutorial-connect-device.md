---
title: 教程 - 将通用 Node.js 客户端应用连接到 Azure IoT Central | Microsoft Docs
description: 本教程介绍如何以设备开发人员的身份将运行 Node.js 客户端应用的设备连接到 Azure IoT Central 应用程序。 通过导入设备功能模型创建设备模板，并添加视图来与连接设备交互
author: dominicbetts
ms.author: dobett
ms.date: 02/26/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 1bcfc949eff0639dd1b4a063687e2c198f480ea3
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/26/2020
ms.locfileid: "77624333"
---
# <a name="tutorial-create-and-connect-a-nodejs-client-application-to-your-azure-iot-central-application-nodejs"></a>教程：创建 Node.js 客户端应用程序并将其连接到 Azure IoT Central 应用程序 (Node.js)

本教程介绍如何以设备开发人员的身份将 Node.js 客户端应用程序连接到 Azure IoT Central 应用程序。 该 Node.js 应用程序模拟真实设备的行为。 使用环境传感器设备的示例设备功能模型在 IoT Central 中创建设备模板。   将视图添加到设备模板，以便将设备遥测数据可视化，管理设备属性，并使用命令来控制设备。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 导入设备功能模型以创建设备模板。
> * 将默认视图和自定义视图添加到设备模板。
> * 发布设备模板，并将真实设备添加到 IoT Central 应用程序。
> * 创建并运行 Node.js 设备代码，并查看它是否可连接到 IoT Central 应用程序。
> * 查看设备发送的模拟遥测数据。
> * 使用视图管理设备属性。
> * 调用命令来控制设备。

## <a name="prerequisites"></a>必备条件

若要完成本文中的步骤，需要以下各项：

* 使用“自定义应用程序”模板创建的 Azure IoT Central 应用程序。 有关详细信息，请参阅[创建应用程序快速入门](quick-deploy-iot-central.md)。
* 安装了 [Node.js](https://nodejs.org/) 10.0.0 或更高版本的开发计算机。 若要检查版本，可以在命令行中运行 `node --version`。 Node.js 适用于各种操作系统。 本教程中的说明假设在 Windows 命令提示符下运行 **node** 命令。 可以在各种操作系统中使用 Node.js。

## <a name="create-a-device-template"></a>创建设备模板

在本地计算机上创建名为 `environmental-sensor` 的文件夹。

下载[环境传感器功能模型](https://raw.githubusercontent.com/Azure/IoTPlugandPlay/master/samples/EnvironmentalSensorInline.capabilitymodel.json) JSON 文件，并将其保存到 `environmental-sensor` 文件夹中。

使用文本编辑器将 `{YOUR_COMPANY_NAME_HERE}` 的两个实例替换为下载的 `EnvironmentalSensorInline.capabilitymodel.json` 文件中的公司名称。

在 Azure IoT Central 应用程序中，通过导入 `EnvironmentalSensorInline.capabilitymodel.json` 设备功能模型文件创建名为“环境传感器”的设备模板： 

![包含导入的设备功能模型的设备模板](./media/tutorial-connect-device/device-template.png)

设备功能模型包括两个接口：标准的“设备信息”接口，以及自定义的“环境传感器”接口。   “环境传感器”接口定义以下功能： 

| 类型 | 显示名称 | 说明 |
| ---- | ------------ | ----------- |
| properties | 设备状态     | 设备的状态。 有两种状态：联机/脱机。 |
| properties | 客户名称    | 当前正在操作设备的客户的名称。 |
| properties | 亮度级别 | 设备上指示灯的亮度级别。 可以指定为 1（高）、2（中）、3（低）。 |
| 遥测 | 温度 | 设备上的当前温度。 |
| 遥测 | 湿度    | 设备上的当前湿度。 |
| Command | blink          | 按给定的时间间隔开始闪烁 LED。 |
| Command | turnon         | 打开设备上的 LED 指示灯。 |
| Command | turnoff        | 关闭设备上的 LED 指示灯。 |
| Command | rundiagnostics | 此命令启动诊断运行。 |

若要自定义“设备状态”属性在 IoT Central 应用程序中的显示方式，请在设备模板中选择“自定义”。   展开“设备状态”项，输入“联机”作为“True 名称”，输入“脱机”作为“False 名称”。      保存更改：

![自定义设备模板](media/tutorial-connect-device/customize-template.png)

## <a name="create-views"></a>创建视图

使用视图可与连接到 IoT Central 应用程序的设备交互。 例如，可以让某些视图显示遥测数据，让某些视图显示属性，并在某些视图中编辑可写属性和云属性。 视图是设备模板的一部分。

若要将一些默认视图添加到“环境传感器”设备模板，请导航到该设备模板，选择“视图”，然后选择“生成默认视图”磁贴。    确保“概述”和“关于”的选择状态为“打开”，然后选择“生成默认仪表板视图”。     现已在模板中定义了两个默认视图。

“环境传感器”接口包括两个可写属性 -“客户名称”和“亮度级别”。    若要创建视图，可以编辑以下属性：

1. 选择“视图”，然后选择“编辑设备和云数据”磁贴。  

1. 输入“属性”作为窗体名称。 

1. 选择“亮度级别”和“客户名称”属性。   然后选择“添加部分”。 

1. 保存所做更改。

![添加一个用于编辑属性的视图](media/tutorial-connect-device/properties-view.png)

## <a name="publish-the-template"></a>发布模板

在将设备添加到使用“环境传感器”设备模板的 IoT Central 应用程序之前，需要发布该模板。 

在设备模板中选择“发布”。  在显示了要发布的更改的面板上，选择“发布”。 

若要检查模板是否可供使用，请导航到 IoT Central 应用程序中的“设备”页。  “设备”部分显示了应用程序中已发布设备的列表： 

![“设备”页上已发布的模板](media/tutorial-connect-device/published-templates.png)

## <a name="add-a-real-device"></a>添加真实设备

在 Azure IoT Central 应用程序中，将真实设备添加到在上一部分中创建的设备模板：

1. 在“设备”页上，选择“环境传感器”设备模板。  

1. 选择“+ 新建”  。

1. 确保“模拟”的选择状态为“关闭”。   然后选择“创建”  。

单击设备名称，然后选择“连接”。  记下“设备连接”页上的设备连接信息 -“ID 范围”、“设备 ID”和“主密钥”。     创建设备代码时需要这些值：

![设备连接信息](./media/tutorial-connect-device/device-connection.png)

### <a name="create-a-nodejs-application"></a>创建 Node.js 应用程序

以下步骤说明如何创建一个 Node.js 客户端应用程序，用于实施已添加到应用程序的真实设备。 此 Node.js 应用程序模拟真实设备的行为。

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

1. 若要向 Azure IoT Central 应用程序发送遥测数据，请将以下函数添加到文件中：

    ```javascript
    // Send device measurements.
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

1. 若要向 Azure IoT Central 应用程序发送设备属性，请将以下函数添加到文件：

    ```javascript
    // Send device reported properties.
    function sendDeviceProperties(twin, properties) {
      twin.properties.reported.update(properties, (err) => console.log(`Sent device properties: ${JSON.stringify(properties)}; ` +
        (err ? `error: ${err.toString()}` : `status: success`)));
    }
    ```

1. 若要定义和处理设备响应的可写属性，请添加以下代码：

    ```javascript
    // Add any writeable properties your device supports,
    // mapped to a function that is called when the setting is changed.
    var settings = {
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

    // Handle writeable property changes that come from Azure IoT Central via the device twin.
    function handleSettings(twin) {
      twin.on('properties.desired', function (desiredChange) {
        for (let setting in desiredChange) {
          if (settings[setting]) {
            console.log(`Received setting: ${setting}: ${desiredChange[setting].value}`);
            settings[setting](desiredChange[setting].value, (newValue, status, message) => {
              var patch = {
                [setting]: {
                  value: newValue,
                  status: status,
                  desiredVersion: desiredChange.$version,
                  message: message
                }
              }
              sendDeviceProperties(twin, patch);
            });
          }
        }
      });
    }
    ```

1. 添加以下代码来处理从 IoT Central 应用程序发送的命令：

    ```javascript
    // Handle blink command
    function onBlink(request, response) {
      console.log('Received asynchronous call to blink');
      response.send(200, (err) => {
        if (err) {
          console.error('Unable to send method response: ' + err.toString());
        } else {
          console.log('Blinking LED every ' + request.payload  + ' seconds');
        }
      });
    }

    // Handle LED turn on command
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

    // Handle LED turn off command
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

    // Handle sensor diagnostics command
    function diagnostics(request, response) {
      console.log('Received asynchronous call to run diagnostics');
      response.send(200, (err) => {
        if (err) {
          console.error('Unable to send method response: ' + err.toString());
        } else {
          console.log('Running diagnostics...');
        }
      });
    }
    ```

1. 添加以下代码以完成与 Azure IoT Central 的连接，并在客户端代码中挂接函数：

    ```javascript
    // Handle device connection to Azure IoT Central.
    var connectCallback = (err) => {
      if (err) {
        console.log(`Device could not connect to Azure IoT Central: ${err.toString()}`);
      } else {
        console.log('Device successfully connected to Azure IoT Central');
        // Create handlers for commands
        hubClient.onDeviceMethod('blink', onBlink);
        hubClient.onDeviceMethod('turnon', turnOn);
        hubClient.onDeviceMethod('turnoff', turnOff);
        hubClient.onDeviceMethod('rundiagnostics', diagnostics);
        // Send telemetry measurements to Azure IoT Central every 1 second.
        setInterval(sendTelemetry, 1000);
        // Get device twin from Azure IoT Central.
        hubClient.getTwin((err, twin) => {
          if (err) {
            console.log(`Error getting device twin: ${err.toString()}`);
          } else {
            // Send Environmental Sensor device properties once on device start up.
            var properties = {
              state: 'true'
            };
            sendDeviceProperties(twin, properties);
            handleSettings(twin);
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

![运行客户端应用程序](media/tutorial-connect-device/run-application.png)

Azure IoT Central 应用程序中的操作员可以：

* 在“概述”页上查看设备发送的遥测数据： 

    ![查看遥测数据](media/tutorial-connect-device/view-telemetry.png)

* 更新“属性”页上的可写属性值： 

    ![更新属性](media/tutorial-connect-device/update-properties.png)

    ![更新设备属性](media/tutorial-connect-device/update-properties-device.png)

* 从“命令”页中调用命令： 

    ![调用 blink 命令](media/tutorial-connect-device/call-command.png)

    ![在设备上调用 blink 命令](media/tutorial-connect-device/call-command-device.png)

* 在“关于”页上查看设备属性： 

    ![查看属性](media/tutorial-connect-device/about-properties.png)

## <a name="next-steps"></a>后续步骤

若要详细了解设备功能模型以及如何创建自己的设备模板，请继续阅读操作指南：

> [!div class="nextstepaction"]
> [定义新的 IoT 设备类型](./howto-set-up-template.md)
