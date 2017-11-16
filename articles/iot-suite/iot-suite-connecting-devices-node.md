---
title: "使用 Node.js 将设备预配到远程监视 - Azure | Microsoft Docs"
description: "介绍如何使用以 Node.js 编写的应用程序将设备连接到 Azure IoT 套件预配置远程监视解决方案。"
services: 
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: 
ms.assetid: fc50a33f-9fb9-42d7-b1b8-eb5cff19335e
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/10/2017
ms.author: dobett
ms.openlocfilehash: 663ed53a52ecf8d93329f83302964571461d7f3f
ms.sourcegitcommit: bc8d39fa83b3c4a66457fba007d215bccd8be985
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/10/2017
---
# <a name="connect-your-device-to-the-remote-monitoring-preconfigured-solution-nodejs"></a>将设备连接到远程监视预配置解决方案 (Node.js)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

本教程介绍如何将物理设备连接到远程监视预配置解决方案。 在本教程中，将使用 Node.js，它对于资源约束最少的环境是一个不错的选择。

## <a name="create-a-nodejs-solution"></a>创建 Node.js 解决方案

请确保已在开发计算机上安装 [Node.js](https://nodejs.org/) 版本 4.0.0 或更高版本。 若要检查版本，可以在命令行中运行 `node --version`。

1. 在开发计算机上，创建名为 `RemoteMonitoring` 的文件夹。 导航到命令行环境中的此文件夹。

1. 若要下载并安装完成示例应用所需的包，请运行以下命令：

    ```cmd/sh
    npm init
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

1. 在 `RemoteMonitoring` 文件夹中，创建名为 **remote_monitoring.js** 的文件。 在文本编辑器中打开此文件。

1. 在 **remote_monitoring.js** 文件中，添加以下 `require` 语句：

    ```nodejs
    'use strict';

    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    var Client = require('azure-iot-device').Client;
    var ConnectionString = require('azure-iot-device').ConnectionString;
    var Message = require('azure-iot-device').Message;
    ```

1. 在 `require` 语句之后添加以下变量声明。 将占位符值 `{Device Id}` 和 `{Device Key}` 替换为针对远程监视解决方案中预配的设备记下的值。 使用解决方案中的 IoT 中心主机名替换 `{IoTHub Name}`。 例如，如果 IoT 中心主机名是 `contoso.azure-devices.net`，请将 `{IoTHub Name}` 替换为 `contoso`：

    ```nodejs
    var connectionString = 'HostName={IoTHub Name}.azure-devices.net;DeviceId={Device Id};SharedAccessKey={Device Key}';
    var deviceId = ConnectionString.parse(connectionString).DeviceId;
    ```

1. 若要定义一些基本遥测数据，请添加以下变量：

    ```nodejs
    var temperature = 50;
    var temperatureUnit = 'F';
    var humidity = 50;
    var humidityUnit = '%';
    var pressure = 55;
    var pressureUnit = 'psig';
    ```

1. 若要定义一些属性值，请添加以下变量：

    ```nodejs
    var temperatureSchema = 'chiller-temperature;v1';
    var humiditySchema = 'chiller-humidity;v1';
    var pressureSchema = 'chiller-pressure;v1';
    var interval = "00:00:05";
    var deviceType = "Chiller";
    var deviceFirmware = "1.0.0";
    var deviceFirmwareUpdateStatus = "";
    var deviceLocation = "Building 44";
    var deviceLatitude = 47.638928;
    var deviceLongitude = -122.13476;
    ```

1. 添加以下变量以定义要发送到解决方案的报告属性。 这些属性包括用于说明设备使用的方法和遥测的元数据：

    ```nodejs
    var reportedProperties = {
      "Protocol": "MQTT",
      "SupportedMethods": "Reboot,FirmwareUpdate,EmergencyValveRelease,IncreasePressure",
      "Telemetry": {
        "TemperatureSchema": {
          "Interval": interval,
          "MessageTemplate": "{\"temperature\":${temperature},\"temperature_unit\":\"${temperature_unit}\"}",
          "MessageSchema": {
            "Name": temperatureSchema,
            "Format": "JSON",
            "Fields": {
              "temperature": "Double",
              "temperature_unit": "Text"
            }
          }
        },
        "HumiditySchema": {
          "Interval": interval,
          "MessageTemplate": "{\"humidity\":${humidity},\"humidity_unit\":\"${humidity_unit}\"}",
          "MessageSchema": {
            "Name": humiditySchema,
            "Format": "JSON",
            "Fields": {
              "humidity": "Double",
              "humidity_unit": "Text"
            }
          }
        },
        "PressureSchema": {
          "Interval": interval,
          "MessageTemplate": "{\"pressure\":${pressure},\"pressure_unit\":\"${pressure_unit}\"}",
          "MessageSchema": {
            "Name": pressureSchema,
            "Format": "JSON",
            "Fields": {
              "pressure": "Double",
              "pressure_unit": "Text"
            }
          }
        }
      },
      "Type": deviceType,
      "Firmware": deviceFirmware,
      "FirmwareUpdateStatus": deviceFirmwareUpdateStatus,
      "Location": deviceLocation,
      "Latitude": deviceLatitude,
      "Longitude": deviceLongitude
    }
    ```

1. 若要输出操作结果，请添加以下帮助程序函数：

    ```nodejs
    function printErrorFor(op) {
        return function printError(err) {
            if (err) console.log(op + ' error: ' + err.toString());
        };
    }
    ```

1. 添加以下帮助程序函数，用于随机化遥测值：

    ```nodejs
    function generateRandomIncrement() {
        return ((Math.random() * 2) - 1);
    }
    ```

1. 添加以下函数以处理解决方案中的直接方法调用。 解决方案使用直接方法对设备进行操作：

    ```nodejs
    function onDirectMethod(request, response) {
      // Implement logic asynchronously here.
      console.log('Simulated ' + request.methodName);

      // Complete the response
      response.send(200, request.methodName + ' was called on the device', function (err) {
        if (!!err) {
          console.error('An error ocurred when sending a method response:\n' +
            err.toString());
        } else {
          console.log('Response to method \'' + request.methodName +
            '\' sent successfully.');
        }
      });
    }
    ```

1. 添加以下代码将遥测数据发送到解决方案。 客户端应用将属性添加到消息，以确定消息架构：

    ```node.js
    function sendTelemetry(data, schema) {
      var d = new Date();
      var payload = JSON.stringify(data);
      var message = new Message(payload);
      message.properties.add('$$CreationTimeUtc', d.toISOString());
      message.properties.add('$$MessageSchema', schema);
      message.properties.add('$$ContentType', 'JSON');

      console.log('Sending device message data:\n' + payload);
      client.sendEvent(message, printErrorFor('send event'));
    }
    ```

1. 添加以下代码，创建客户端实例：

    ```nodejs
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```

1. 添加以下代码来执行下述操作：

    * 打开连接。
    * 设置所需属性的处理程序。
    * 发送报告的属性。
    * 为直接方法注册处理程序。
    * 开始发送遥测。

    ```nodejs
    client.open(function (err) {
      if (err) {
        printErrorFor('open')(err);
      } else {
        // Create device Twin
        client.getTwin(function (err, twin) {
          if (err) {
            console.error('Could not get device twin');
          } else {
            console.log('Device twin created');

            twin.on('properties.desired', function (delta) {
              // Handle desired properties set by solution
              console.log('Received new desired properties:');
              console.log(JSON.stringify(delta));
            });

            // Send reported properties
            twin.properties.reported.update(reportedProperties, function (err) {
              if (err) throw err;
              console.log('twin state reported');
            });

            // Register handlers for all the method names we are interested in.
            // Consider separate handlers for each method.
            client.onDeviceMethod('Reboot', onDirectMethod);
            client.onDeviceMethod('FirmwareUpdate', onDirectMethod);
            client.onDeviceMethod('EmergencyValveRelease', onDirectMethod);
            client.onDeviceMethod('IncreasePressure', onDirectMethod);
          }
        });

        // Start sending telemetry
        var sendTemperatureInterval = setInterval(function () {
          temperature += generateRandomIncrement();
          var data = {
            'temperature': temperature,
            'temperature_unit': temperatureUnit
          };
          sendTelemetry(data, temperatureSchema)
        }, 5000);

        var sendHumidityInterval = setInterval(function () {
          humidity += generateRandomIncrement();
          var data = {
            'humidity': humidity,
            'humidity_unit': humidityUnit
          };
          sendTelemetry(data, humiditySchema)
        }, 5000);

        var sendPressureInterval = setInterval(function () {
          pressure += generateRandomIncrement();
          var data = {
            'pressure': pressure,
            'pressure_unit': pressureUnit
          };
          sendTelemetry(data, pressureSchema)
        }, 5000);

        client.on('error', function (err) {
          printErrorFor('client')(err);
          if (sendTemperatureInterval) clearInterval(sendTemperatureInterval);
          if (sendHumidityInterval) clearInterval(sendHumidityInterval);
          if (sendPressureInterval) clearInterval(sendPressureInterval);
          client.close(printErrorFor('client.close'));
        });
      }
    });
    ```

1. 保存对 **remote_monitoring.js** 文件的更改。

1. 若要启动示例应用程序，请在命令提示符处运行以下命令：

    ```cmd/sh
    node remote_monitoring.js
    ```

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]
