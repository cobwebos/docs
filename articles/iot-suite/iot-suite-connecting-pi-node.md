---
title: "在 Node.js 中将 Raspberry Pi 预配到远程监视 - Azure | Microsoft Docs"
description: "介绍如何使用以 Node.js 编写的应用程序将 Raspberry Pi 设备连接到 Azure IoT 套件预配置远程监视解决方案。"
services: iot-suite
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
ms.date: 01/24/2018
ms.author: dobett
ms.openlocfilehash: 7f489a6b26edb9a58b21d318785d3804197b33cb
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/09/2018
---
# <a name="connect-your-raspberry-pi-device-to-the-remote-monitoring-preconfigured-solution-nodejs"></a>将 Raspberry Pi 设备连接到远程监视预配置解决方案 (Node.js)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

本教程介绍如何将物理设备连接到远程监视预配置解决方案。 在本教程中，将使用 Node.js，它对于资源约束最少的环境是一个不错的选择。

### <a name="required-hardware"></a>所需硬件

一个台式机，用于通过远程方式连接到 Raspberry Pi 上的命令行。

[适用于 Raspberry Pi 3 的 Microsoft IoT 初学者套件](https://azure.microsoft.com/develop/iot/starter-kits/)或等效组件。 本教程使用套件中的以下项目：

- Raspberry Pi 3
- MicroSD 卡（带 NOOBS）
- USB 迷你电缆
- 以太网电缆

### <a name="required-desktop-software"></a>所需的桌面软件

需要在台式机上安装 SSH 客户端，才能远程访问 Raspberry Pi 上的命令行。

- Windows 不包括 SSH 客户端。 建议使用 [PuTTY](http://www.putty.org/)。
- 大多数 Linux 分发版和 Mac OS 包括命令行 SSH 实用工具。 有关详细信息，请参阅 [SSH Using Linux or Mac OS](https://www.raspberrypi.org/documentation/remote-access/ssh/unix.md)（使用 Linux 或 Mac OS 的 SSH）。

### <a name="required-raspberry-pi-software"></a>所需的 Raspberry Pi 软件

如果尚未这样做，请在 Raspberry Pi 上安装 Node.js 4.0.0 或更高版本。 以下步骤演示如何在 Raspberry Pi 上安装 Node.js v6：

1. 使用 `ssh` 连接到 Raspberry Pi。 有关详细信息，请参阅 [Raspberry Pi 网站](https://www.raspberrypi.org/)上的 [SSH（安全外壳）](https://www.raspberrypi.org/documentation/remote-access/ssh/README.md)。

1. 使用以下命令更新 Raspberry Pi：

    ```sh
    sudo apt-get update
    ```

1. 使用以下命令从 Raspberry Pi 中删除任何现有 Node.js 安装：

    ```sh
    sudo apt-get remove nodered -y
    sudo apt-get remove nodejs nodejs-legacy -y
    sudo apt-get remove npm  -y
    ```

1. 使用以下命令在 Raspberry Pi 上下载并安装 Node.js v6：

    ```sh
    curl -sL https://deb.nodesource.com/setup_6.x | sudo bash -
    sudo apt-get install nodejs -y
    ```

1. 使用以下命令验证已成功安装 Node.js v6.11.4：

    ```sh
    node --version
    ```

## <a name="create-a-nodejs-solution"></a>创建 Node.js 解决方案

使用与 Raspberry Pi 的 `ssh` 连接完成以下步骤：

1. 在 Raspberry Pi 上的主文件夹中创建名为 `remotemonitoring` 的文件夹。 在命令行中导航到此文件夹：

    ```sh
    cd ~
    mkdir remotemonitoring
    cd remotemonitoring
    ```

1. 若要下载并安装完成示例应用所需的包，请运行以下命令：

    ```sh
    npm init
    npm install async azure-iot-device azure-iot-device-mqtt --save
    ```

1. 在 `remotemonitoring` 文件夹中，创建名为 **remote_monitoring.js** 的文件。 在文本编辑器中打开此文件。 在 Raspberry Pi 上，可以使用 `nano` 或 `vi` 文本编辑器。

1. 在 **remote_monitoring.js** 文件中，添加以下 `require` 语句：

    ```nodejs
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    var Client = require('azure-iot-device').Client;
    var ConnectionString = require('azure-iot-device').ConnectionString;
    var Message = require('azure-iot-device').Message;
    var async = require('async');
    ```

1. 在 `require` 语句之后添加以下变量声明。 将占位符值 `{device connection string}` 替换为针对远程监视解决方案中预配的设备记下的值：

    ```nodejs
    var connectionString = '{device connection string}';
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
    var deviceOnline = true;
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
      "Longitude": deviceLongitude,
      "Online": deviceOnline
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

1. 添加以下泛型函数以处理解决方案中的直接方法调用。 该函数显示调用的直接方法的相关信息，但在此示例中不以任何方式修改设备。 解决方案使用直接方法对设备进行操作：

    ```nodejs
    function onDirectMethod(request, response) {
      // Implement logic asynchronously here.
      console.log('Simulated ' + request.methodName);

      // Complete the response
      response.send(200, request.methodName + ' was called on the device', function (err) {
        if (err) console.error('Error sending method response :\n' + err.toString());
        else console.log('200 Response to method \'' + request.methodName + '\' sent successfully.');
      });
    }
    ```

1. 添加以下函数以处理解决方案中的 **FirmwareUpdate** 直接方法调用。 该函数验证直接方法负载中传入的参数，并以异步方式运行固件更新模拟：

    ```node.js
    function onFirmwareUpdate(request, response) {
      // Get the requested firmware version from the JSON request body
      var firmwareVersion = request.payload.Firmware;
      var firmwareUri = request.payload.FirmwareUri;
      
      // Ensure we got a firmware values
      if (!firmwareVersion || !firmwareUri) {
        response.send(400, 'Missing firmware value', function(err) {
          if (err) console.error('Error sending method response :\n' + err.toString());
          else console.log('400 Response to method \'' + request.methodName + '\' sent successfully.');
        });
      } else {
        // Respond the cloud app for the device method
        response.send(200, 'Firmware update started.', function(err) {
          if (err) console.error('Error sending method response :\n' + err.toString());
          else {
            console.log('200 Response to method \'' + request.methodName + '\' sent successfully.');

            // Run the simulated firmware update flow
            runFirmwareUpdateFlow(firmwareVersion, firmwareUri);
          }
        });
      }
    }
    ```

1. 添加以下函数以模拟长时间运行的固件更新流（将进度报告回解决方案）：

    ```node.js
    // Simulated firmwareUpdate flow
    function runFirmwareUpdateFlow(firmwareVersion, firmwareUri) {
      console.log('Simulating firmware update flow...');
      console.log('> Firmware version passed: ' + firmwareVersion);
      console.log('> Firmware URI passed: ' + firmwareUri);
      async.waterfall([
        function (callback) {
          console.log("Image downloading from " + firmwareUri);
          var patch = {
            FirmwareUpdateStatus: 'Downloading image..'
          };
          reportUpdateThroughTwin(patch, callback);
          sleep(10000, callback);
        },
        function (callback) {
          console.log("Downloaded, applying firmware " + firmwareVersion);
          deviceOnline = false;
          var patch = {
            FirmwareUpdateStatus: 'Applying firmware..',
            Online: false
          };
          reportUpdateThroughTwin(patch, callback);
          sleep(8000, callback);
        },
        function (callback) {
          console.log("Rebooting");
          var patch = {
            FirmwareUpdateStatus: 'Rebooting..'
          };
          reportUpdateThroughTwin(patch, callback);
          sleep(10000, callback);
        },
        function (callback) {
          console.log("Firmware updated to " + firmwareVersion);
          deviceOnline = true;
          var patch = {
            FirmwareUpdateStatus: 'Firmware updated',
            Online: true,
            Firmware: firmwareVersion
          };
          reportUpdateThroughTwin(patch, callback);
          callback(null);
        }
      ], function(err) {
        if (err) {
          console.error('Error in simulated firmware update flow: ' + err.message);
        } else {
          console.log("Completed simulated firmware update flow");
        }
      });

      // Helper function to update the twin reported properties.
      function reportUpdateThroughTwin(patch, callback) {
        console.log("Sending...");
        console.log(JSON.stringify(patch, null, 2));
        client.getTwin(function(err, twin) {
          if (!err) {
            twin.properties.reported.update(patch, function(err) {
              if (err) callback(err);
            });      
          } else {
            if (err) callback(err);
          }
        });
      }

      function sleep(milliseconds, callback) {
        console.log("Simulate a delay (milleseconds): " + milliseconds);
        setTimeout(function () {
          callback(null);
        }, milliseconds);
      }
    }
    ```

1. 添加以下代码将遥测数据发送到解决方案。 客户端应用将属性添加到消息，以确定消息架构：

    ```node.js
    function sendTelemetry(data, schema) {
      if (deviceOnline) {
        var d = new Date();
        var payload = JSON.stringify(data);
        var message = new Message(payload);
        message.properties.add('$$CreationTimeUtc', d.toISOString());
        message.properties.add('$$MessageSchema', schema);
        message.properties.add('$$ContentType', 'JSON');

        console.log('Sending device message data:\n' + payload);
        client.sendEvent(message, printErrorFor('send event'));
      } else {
        console.log('Offline, not sending telemetry');
      }
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
    * 为直接方法注册处理程序。 此示例对固件更新直接方法使用单独的处理程序。
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
              console.log('Twin state reported');
            });

            // Register handlers for all the method names we are interested in.
            // Consider separate handlers for each method.
            client.onDeviceMethod('Reboot', onDirectMethod);
            client.onDeviceMethod('FirmwareUpdate', onFirmwareUpdate);
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

1. 若要启动示例应用程序，请在 Raspberry Pi 上的命令提示符下运行以下命令：

    ```sh
    node remote_monitoring.js
    ```

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]
