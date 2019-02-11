---
title: 将泛型 Node.js 客户端应用程序连接到 Azure IoT Central | Microsoft Docs
description: 如何以设备开发人员的身份将泛型 Node.js 设备连接到 Azure IoT Central 应用程序。
author: dominicbetts
ms.author: dobett
ms.date: 02/04/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: cc659e1138690ffda7e21d25c37fb4dde095e6b9
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/07/2019
ms.locfileid: "55817064"
---
# <a name="connect-a-generic-client-application-to-your-azure-iot-central-application-nodejs"></a>将泛型客户端应用程序连接到 Azure IoT Central 应用程序 (Node.js)

本文介绍如何以设备开发人员的身份将代表真实设备的泛型 Node.js 应用程序连接到 Microsoft Azure IoT Central 应用程序。

## <a name="before-you-begin"></a>开始之前

若要完成本文中的步骤，需要以下各项：

1. Azure IoT Central 应用程序。 有关详细信息，请参阅[创建应用程序快速入门](quick-deploy-iot-central-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)。
1. 安装了 [Node.js](https://nodejs.org/) 4.0.0 或更高版本的开发计算机。 若要检查版本，可以在命令行中运行 `node --version`。 Node.js 适用于各种操作系统。

## <a name="create-a-device-template"></a>创建设备模板

在 Azure IoT Central 应用程序中，需要一个定义了以下度量和设备属性的设备模板：

### <a name="telemetry-measurements"></a>遥测度量

在“度量”页中添加以下遥测：

| 显示名称 | 字段名称  | 单位 | Min | Max | 小数位数 |
| ------------ | ----------- | ----- | --- | --- | -------------- |
| 温度  | 温度 | F     | 60  | 110 | 0              |
| 湿度     | 湿度    | %     | 0   | 100 | 0              |
| 压力     | 压力    | kPa   | 80  | 110 | 0              |

> [!NOTE]
  遥测度量的数据类型是一个浮点数。

将表中所示字段名称准确输入设备模板中。 如果字段名称与对应的设备代码中的属性名称不匹配，则遥测数据无法显示在应用程序中。

### <a name="state-measurements"></a>状态度量

在“度量”页中添加以下状态：

| 显示名称 | 字段名称  | 值 1 | 显示名称 | 值 2 | 显示名称 |
| ------------ | ----------- | --------| ------------ | ------- | ------------ | 
| 风扇模式     | fanmode     | 1       | 正在运行      | 0       | 已停止      |

> [!NOTE]
  状态度量的数据类型为字符串。

将表中所示字段名称准确输入设备模板中。 如果字段名称与对应的设备代码中的属性名称不匹配，则状态无法显示在应用程序中。

### <a name="event-measurements"></a>事件度量

在“度量”页中添加以下事件：

| 显示名称 | 字段名称  | 严重性 |
| ------------ | ----------- | -------- |
| 过热  | 过热    | 错误    |

> [!NOTE]
  事件度量的数据类型为字符串。

### <a name="device-properties"></a>设备属性

在**属性页**中添加以下设备属性：

| 显示名称        | 字段名称        | 数据类型 |
| ------------------- | ----------------- | --------- |
| 序列号       | serialNumber      | text      |
| 设备制造商 | 制造商      | text      |

将表中所示字段名称准确输入设备模板中。 如果字段名称与对应的设备代码中的属性名称不匹配，则应用程序无法显示设备属性值。

### <a name="settings"></a>设置

在**设置页**中添加以下**数字**设置：

| 显示名称    | 字段名称     | 单位 | 小数 | Min | Max  | 初始 |
| --------------- | -------------- | ----- | -------- | --- | ---- | ------- |
| 风扇速度       | fanSpeed       | rpm   | 0        | 0   | 3000 | 0       |
| 设置温度 | setTemperature | F     | 0        | 20  | 200  | 80      |

将表中所示字段名称准确输入设备模板中。 如果字段名称与对应的设备代码中的属性名称不匹配，则设备不能收到设置值。

## <a name="add-a-real-device"></a>添加真实设备

在 Azure IoT Central 应用程序中，从创建的设备模板添加真实设备，并记下设备连接字符串。 有关如何将 Node.js 应用程序连接到 IoT Central 的分步说明，请参阅“教程”>“添加设备”中的[从应用程序为真实设备生成连接字符串](tutorial-add-device-experimental.md#generate-connection-string-for-real-device-from-application)和[准备客户端代码](tutorial-add-device-experimental.md#prepare-the-client-code)。

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

    > [!NOTE]
    > Azure IoT Central 已转换为对所有设备连接使用 Azure IoT 中心设备预配服务 (DPS)，请按照以下说明[获取设备连接字符串](concepts-connectivity-experimental.md#getting-device-connection-string)并继续执行教程的剩余部分。 如果需要更多帮助，还可以在“教程”>“添加设备”中的[准备客户端代码](tutorial-add-device-experimental.md#prepare-the-client-code)中找到一组详细说明。

    将占位符 `{your device connection string}` 更新为设备连接字符串。 在此示例中，我们将 `targetTemperature` 初始化为零。你可以选择从设备获取当前的读取内容，也可以从设备孪生获取值。 

1. 若要向 Azure IoT Central 应用程序发送遥测、状态和事件度量，请将以下函数添加到文件：

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
    // Send device properties.
    function sendDeviceProperties(twin) {
      var properties = {
        serialNumber: '123-ABC',
        manufacturer: 'Contoso'
      };
      twin.properties.reported.update(properties, (err) => console.log(`Sent device properties; ` +
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

1. 添加以下代码以完成到 Azure IoT Central 的连接，并将客户端代码中的函数挂钩：

    ```javascript
    // Handle device connection to Azure IoT Central.
    var connectCallback = (err) => {
      if (err) {
        console.log(`Device could not connect to Azure IoT Central: ${err.toString()}`);
      } else {
        console.log('Device successfully connected to Azure IoT Central');

        // Send telemetry measurements to Azure IoT Central every 1 second.
        setInterval(sendTelemetry, 1000);

        // Get device twin from Azure IoT Central.
        client.getTwin((err, twin) => {
          if (err) {
            console.log(`Error getting device twin: ${err.toString()}`);
          } else {
            // Send device properties once on device start up.
            sendDeviceProperties(twin);
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

    ![查看遥测数据](media/howto-connect-nodejs-experimental/viewtelemetry.png)

* 在“属性”页上查看从设备发送的设备属性值。 如果连接成功，设备属性磁贴将会更新。 

    ![查看设备属性](media/howto-connect-nodejs-experimental/viewproperties.png)

* 在“设置”页中设置风扇速度和目标温度。 如果连接成功，设置值将会同步。 

    ![设置风扇速度](media/howto-connect-nodejs-experimental/setfanspeed.png)

## <a name="next-steps"></a>后续步骤

了解如何将泛型 Node.js 客户端连接到 Azure IoT Central 应用程序后，建议接下来执行以下步骤：
* [准备和连接 Raspberry Pi](howto-connect-raspberry-pi-python.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)
<!-- Next how-tos in the sequence -->
