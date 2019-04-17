---
title: 将泛型 Node.js 客户端应用程序连接到 Azure IoT Central | Microsoft Docs
description: 作为设备开发人员，如何将通用的 Node.js 设备连接到 Azure IoT Central 应用程序。
author: dominicbetts
ms.author: dobett
ms.date: 04/05/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 5497e4956fbdc74eced302867c33a66d07d6a184
ms.sourcegitcommit: fec96500757e55e7716892ddff9a187f61ae81f7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2019
ms.locfileid: "59617931"
---
# <a name="connect-a-generic-client-application-to-your-azure-iot-central-application-nodejs"></a>将泛型客户端应用程序连接到 Azure IoT Central 应用程序 (Node.js)

本文介绍如何以设备开发人员的身份将代表真实设备的泛型 Node.js 应用程序连接到 Microsoft Azure IoT Central 应用程序。

## <a name="before-you-begin"></a>开始之前

若要完成本文中的步骤，需要以下各项：

1. Azure IoT Central 应用程序。 有关详细信息，请参阅[创建应用程序快速入门](quick-deploy-iot-central.md)。
1. 安装了 [Node.js](https://nodejs.org/) 4.0.0 或更高版本的开发计算机。 若要检查版本，可以在命令行中运行 `node --version`。 Node.js 适用于各种操作系统。

## <a name="create-a-device-template"></a>创建设备模板

Azure IoT Central 应用程序中需要具有以下度量值、 设备属性、 设置和命令的设备模板：

### <a name="telemetry-measurements"></a>遥测数据度量

上添加以下遥测数据**度量**页：

| 显示名称 | 字段名称  | 单位 | Min | Max | 小数位数 |
| ------------ | ----------- | ----- | --- | --- | -------------- |
| 温度  | 温度 | F     | 60  | 110 | 0              |
| 湿度     | 湿度    | %     | 0   | 100 | 0              |
| 压力     | 压力    | kPa   | 80  | 110 | 0              |

> [!NOTE]
> 遥测度量的数据类型是一个浮点数。

将表中所示字段名称准确输入设备模板中。 如果字段名称不匹配相应的设备代码中的属性名称，不能在应用程序中显示遥测数据。

### <a name="state-measurements"></a>状态度量

上添加以下状态**度量**页：

| 显示名称 | 字段名称  | 值 1 | 显示名称 | 值 2 | 显示名称 |
| ------------ | ----------- | --------| ------------ | ------- | ------------ | 
| 风扇模式     | fanmode     | 1       | 正在运行      | 0       | 已停止      |

> [!NOTE]
> 状态度量的数据类型为字符串。

将表中所示字段名称准确输入设备模板中。 如果字段名称不匹配相应的设备代码中的属性名称，不能在应用程序中显示状态。

### <a name="event-measurements"></a>事件度量

添加下面的事件上**度量**页：

| 显示名称 | 字段名称  | Severity |
| ------------ | ----------- | -------- |
| 过热  | 过热    | 错误    |

> [!NOTE]
> 事件度量的数据类型为字符串。

### <a name="device-properties"></a>设备属性

添加以下设备属性上**属性**页：

| 显示名称        | 字段名称        | 数据类型 |
| ------------------- | ----------------- | --------- |
| 序列号       | serialNumber      | text      |
| 设备制造商 | 制造商      | text      |

将表中所示字段名称准确输入设备模板中。 如果字段名称不匹配相应的设备代码中的属性名称，不能在应用程序中显示属性。

### <a name="settings"></a>设置

添加以下**数量**上的设置**设置**页：

| 显示名称    | 字段名称     | 单位 | 小数 | Min | Max  | 初始 |
| --------------- | -------------- | ----- | -------- | --- | ---- | ------- |
| 风扇速度       | fanSpeed       | rpm   | 0        | 0   | 3000 | 0       |
| 设置温度 | setTemperature | F     | 0        | 20  | 200  | 80      |

将表中所示字段名称准确输入设备模板中。 如果字段名称不匹配相应的设备代码中的属性名称，该设备无法接收设置值。

### <a name="commands"></a>命令

在上添加以下命令**命令**页：

| 显示名称    | 字段名称     | 默认超时 | 数据类型 |
| --------------- | -------------- | --------------- | --------- |
| 倒计时       | 倒计时      | 30              | 数字    |

将以下输入的字段添加到倒计时命令：

| 显示名称    | 字段名称     | 数据类型 | 值 |
| --------------- | -------------- | --------- | ----- |
| 从计数      | countFrom      | 数字    | 10    |

严格按照所示的表中的设备模板到输入字段名称。 如果字段名称不匹配相应的设备代码中的属性名称，该设备无法处理该命令。

## <a name="add-a-real-device"></a>添加真实设备

Azure IoT Central 应用程序中将添加到在上一节中创建的设备模板的真实的设备。

在"添加设备"教程到中按照[生成真实的设备的连接字符串](tutorial-add-device.md#generate-connection-string)。 下一节中使用此连接字符串：

### <a name="create-a-nodejs-application"></a>创建 Node.js 应用程序

以下步骤演示如何创建一个客户端应用程序，以便实现已添加到应用程序的真实设备。 此处，Node.js 应用程序代表真实设备。 

1. 在计算机上创建名为 `connected-air-conditioner-adv` 的文件夹。 导航到命令行环境中的该文件夹。

1. 若要初始化 Node.js 项目，请运行以下命令：

    ```cmd/sh
    npm init
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

1. 在 `connected-air-conditioner-adv` 文件夹中创建名为 **connectedAirConditionerAdv.js** 的文件。

1. 在 **connectedAirConditionerAdv.js** 文件的开头添加以下 `require` 语句：

    ```javascript
    "use strict";

    // Use the Azure IoT device SDK for devices that connect to Azure IoT Central.
    var clientFromConnectionString = require('azure-iot-device-mqtt').clientFromConnectionString;
    var Message = require('azure-iot-device').Message;
    var ConnectionString = require('azure-iot-device').ConnectionString;
    ```

1. 将以下变量声明添加到该文件：

    ```javascript
    var connectionString = '{your device connection string}';
    var targetTemperature = 0;
    var client = clientFromConnectionString(connectionString);
    ```

    更新占位符`{your device connection string}`与[设备连接字符串](tutorial-add-device.md#generate-connection-string)。 在此示例中，您初始化`targetTemperature`为零，您可以使用当前读取从设备或设备孪生中的值。

1. 若要发送到 Azure IoT Central 应用程序的遥测数据、 状态和事件度量，请向文件添加以下函数：

    ```javascript
    // Send device measurements.
    function sendTelemetry() {
      var temperature = targetTemperature + (Math.random() * 15);
      var humidity = 70 + (Math.random() * 10);
      var pressure = 90 + (Math.random() * 5);
      var fanmode = 0;
      var data = JSON.stringify({
        temperature: temperature,
        humidity: humidity,
        pressure: pressure,
        fanmode: (temperature > 25) ? "1" : "0",
        overheat: (temperature > 35) ? "ER123" : undefined });
      var message = new Message(data);
      client.sendEvent(message, (err, res) => console.log(`Sent message: ${message.getData()}` +
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

1. 若要定义供设备响应的设置，请添加以下定义：

    ```javascript
    // Add any settings your device supports,
    // mapped to a function that is called when the setting is changed.
    var settings = {
      'fanSpeed': (newValue, callback) => {
          // Simulate it taking 1 second to set the fan speed.
          setTimeout(() => {
            callback(newValue, 'completed');
          }, 1000);
      },
      'setTemperature': (newValue, callback) => {
        // Simulate the temperature setting taking two steps.
        setTimeout(() => {
          targetTemperature = targetTemperature + (newValue - targetTemperature) / 2;
          callback(targetTemperature, 'pending');
          setTimeout(() => {
            targetTemperature = newValue;
            callback(targetTemperature, 'completed');
          }, 5000);
        }, 5000);
      }
    };
    ```

1. 若要处理 Azure IoT Central 应用程序提供的已更新设置，请向文件添加以下代码：

    ```javascript
    // Handle settings changes that come from Azure IoT Central via the device twin.
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
              twin.properties.reported.update(patch, (err) => console.log(`Sent setting update for ${setting}; ` +
                (err ? `error: ${err.toString()}` : `status: success`)));
            });
          }
        }
      });
    }
    ```

1. 添加以下代码以处理从 IoT Central 应用程序发送的倒计时命令：

    ```javascript
    // Handle countdown command
    function onCountdown(request, response) {
      console.log('Received call to countdown');

      var countFrom = (typeof(request.payload.countFrom) === 'number' && request.payload.countFrom < 100) ? request.payload.countFrom : 10;

      response.send(200, (err) => {
        if (err) {
          console.error('Unable to send method response: ' + err.toString());
        } else {
          client.getTwin((err, twin) => {
            function doCountdown(){
              if ( countFrom >= 0 ) {
                var patch = {
                  countdown:{
                    value: countFrom
                  }
                };
                sendDeviceProperties(twin, patch);
                countFrom--;
                setTimeout(doCountdown, 2000 );
              }
            }

            doCountdown();
          });
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

        // Create handler for countdown command
        client.onDeviceMethod('countdown', onCountdown);

        // Send telemetry measurements to Azure IoT Central every 1 second.
        setInterval(sendTelemetry, 1000);

        // Get device twin from Azure IoT Central.
        client.getTwin((err, twin) => {
          if (err) {
            console.log(`Error getting device twin: ${err.toString()}`);
          } else {
            // Send device properties once on device start up.
            var properties = {
              serialNumber: '123-ABC',
              manufacturer: 'Contoso'
            };
            sendDeviceProperties(twin, properties);

            // Apply device settings and handle changes to device settings.
            handleSettings(twin);
          }
        });
      }
    };

    // Start the device (connect it to Azure IoT Central).
    client.open(connectCallback);
    ```

## <a name="run-your-nodejs-application"></a>运行 Node.js 应用程序

在命令行环境中运行以下命令：

```cmd/sh
node connectedAirConditionerAdv.js
```

作为 Azure IoT Central 应用程序中的操作员，你可以对真实设备执行以下操作：

* 在“度量”页中查看遥测：

    ![查看遥测数据](media/howto-connect-nodejs/viewtelemetry.png)

* 在“属性”页上查看从设备发送的设备属性值。 在设备连接的设备属性磁贴更新：

    ![查看设备属性](media/howto-connect-nodejs/viewproperties.png)

* 设置从风扇速度和目标温度**设置**页：

    ![设置风扇速度](media/howto-connect-nodejs/setfanspeed.png)

* 调用中的倒计时命令**命令**页：

    ![调用倒计时命令](media/howto-connect-nodejs/callcountdown.png)

## <a name="next-steps"></a>后续步骤

现在，已了解如何将泛型 Node.js 客户端连接到 Azure IoT Central 应用程序，建议下一步是了解如何[设置自定义设备模板](howto-set-up-template.md)IoT 设备。
