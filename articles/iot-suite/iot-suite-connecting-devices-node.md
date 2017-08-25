---
title: "使用 Node.js 连接设备 | Microsoft 文档"
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
ms.date: 08/24/2017
ms.author: dobett
ms.translationtype: Human Translation
ms.sourcegitcommit: 757d6f778774e4439f2c290ef78cbffd2c5cf35e
ms.openlocfilehash: 8f45d0e86a95779d5ceeddb72638b14e0e7a80eb
ms.contentlocale: zh-cn
ms.lasthandoff: 04/10/2017

---
# <a name="connect-your-device-to-the-remote-monitoring-preconfigured-solution-nodejs"></a>将设备连接到远程监视预配置解决方案 (Node.js)
[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

## <a name="create-a-nodejs-sample-solution"></a>创建 node.js 示例解决方案

请确保已在开发计算机上安装 Node.js 版本 0.11.5 或更高版本。 若要检查版本，可以在命令行中运行 `node --version`。

1. 在开发计算机上，创建名为 **RemoteMonitoring** 的文件夹。 导航到命令行环境中的此文件夹。

1. 运行以下命令，以便下载并安装完成示例应用所需的包：

    ```
    npm init
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

1. 在 **RemoteMonitoring** 文件夹中，创建名为 **remote_monitoring.js** 的文件。 在文本编辑器中打开此文件。

1. 在 **remote_monitoring.js** 文件中，添加以下 `require` 语句：

    ```nodejs
    'use strict';

    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    var Client = require('azure-iot-device').Client;
    var ConnectionString = require('azure-iot-device').ConnectionString;
    var Message = require('azure-iot-device').Message;
    ```

1. 在 `require` 语句之后添加以下变量声明。 将占位符值 [Device Id] 和 [Device Key] 替换为在远程监视解决方案仪表板中记下的设备值。 使用解决方案仪表板中的 IoT 中心主机名替换 [IoTHub Name]。 例如，如果 IoT 中心主机名是 **contoso.azure-devices.net**，则将 [IoTHub Name] 替换为 **contoso**：

    ```nodejs
    var connectionString = 'HostName=[IoTHub Name].azure-devices.net;DeviceId=[Device Id];SharedAccessKey=[Device Key]';
    var deviceId = ConnectionString.parse(connectionString).DeviceId;
    ```

1. 添加以下变量，定义一些基遥测数据：

    ```nodejs
    var temperature = 50;
    var humidity = 50;
    var externalTemperature = 55;
    ```

1. 添加以下帮助程序函数，以便列显操作结果：

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

1. 为设备在启动时发送的 **DeviceInfo** 对象添加以下定义：

    ```nodejs
    var deviceMetaData = {
        'ObjectType': 'DeviceInfo',
        'IsSimulatedDevice': 0,
        'Version': '1.0',
        'DeviceProperties': {
            'DeviceID': deviceId,
            'HubEnabledState': 1
        }
    };
    ```

1. 为设备孪生报告的值添加以下定义。 该定义包括对设备支持的直接方法的说明：

    ```nodejs
    var reportedProperties = {
        "Device": {
            "DeviceState": "normal",
            "Location": {
                "Latitude": 47.642877,
                "Longitude": -122.125497
            }
        },
        "Config": {
            "TemperatureMeanValue": 56.7,
            "TelemetryInterval": 45
        },
        "System": {
            "Manufacturer": "Contoso Inc.",
            "FirmwareVersion": "2.22",
            "InstalledRAM": "8 MB",
            "ModelNumber": "DB-14",
            "Platform": "Plat 9.75",
            "Processor": "i3-9",
            "SerialNumber": "SER99"
        },
        "Location": {
            "Latitude": 47.642877,
            "Longitude": -122.125497
        },
        "SupportedMethods": {
            "Reboot": "Reboot the device",
            "InitiateFirmwareUpdate--FwPackageURI-string": "Updates device Firmware. Use parameter FwPackageURI to specifiy the URI of the firmware file"
        },
    }
    ```

1. 添加以下函数以处理 **Reboot** 直接方法调用：

    ```nodejs
    function onReboot(request, response) {
        // Implement actual logic here.
        console.log('Simulated reboot...');

        // Complete the response
        response.send(200, "Rebooting device", function(err) {
            if(!!err) {
                console.error('An error occurred when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.' );
            }
        });
    }
    ```

1. 添加以下函数以处理 **InitiateFirmwareUpdate** 直接方法调用。 该直接方法使用参数指定要下载的固件映像的位置，并在设备上异步启动固件更新：

    ```nodejs
    function onInitiateFirmwareUpdate(request, response) {
        console.log('Simulated firmware update initiated, using: ' + request.payload.FwPackageURI);

        // Complete the response
        response.send(200, "Firmware update initiated", function(err) {
            if(!!err) {
                console.error('An error occurred when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.' );
            }
        });

        // Add logic here to perform the firmware update asynchronously
    }
    ```

1. 添加以下代码，创建客户端实例：

    ```nodejs
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```

1. 添加以下代码来执行下述操作：

    * 打开连接。
    * 发送 **DeviceInfo** 对象。
    * 设置所需属性的处理程序。
    * 发送报告的属性。
    * 为直接方法注册处理程序。
    * 开始发送遥测。

    ```nodejs
    client.open(function (err) {
        if (err) {
            printErrorFor('open')(err);
        } else {
            console.log('Sending device metadata:\n' + JSON.stringify(deviceMetaData));
            client.sendEvent(new Message(JSON.stringify(deviceMetaData)), printErrorFor('send metadata'));

            // Create device twin
            client.getTwin(function(err, twin) {
                if (err) {
                    console.error('Could not get device twin');
                } else {
                    console.log('Device twin created');

                    twin.on('properties.desired', function(delta) {
                        console.log('Received new desired properties:');
                        console.log(JSON.stringify(delta));
                    });

                    // Send reported properties
                    twin.properties.reported.update(reportedProperties, function(err) {
                        if (err) throw err;
                        console.log('twin state reported');
                    });

                    // Register handlers for direct methods
                    client.onDeviceMethod('Reboot', onReboot);
                    client.onDeviceMethod('InitiateFirmwareUpdate', onInitiateFirmwareUpdate);
                }
            });

            // Start sending telemetry
            var sendInterval = setInterval(function () {
                temperature += generateRandomIncrement();
                externalTemperature += generateRandomIncrement();
                humidity += generateRandomIncrement();

                var data = JSON.stringify({
                    'DeviceID': deviceId,
                    'Temperature': temperature,
                    'Humidity': humidity,
                    'ExternalTemperature': externalTemperature
                });

                console.log('Sending device event data:\n' + data);
                client.sendEvent(new Message(data), printErrorFor('send event'));
            }, 5000);

            client.on('error', function (err) {
                printErrorFor('client')(err);
                if (sendInterval) clearInterval(sendInterval);
                client.close(printErrorFor('client.close'));
            });
        }
    });
    ```

1. 保存对 **remote_monitoring.js** 文件的更改。

1. 在命令提示符处运行以下命令，启动示例应用程序：
   
    ```
    node remote_monitoring.js
    ```

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]

[lnk-github-repo]: https://github.com/azure/azure-iot-sdk-node
[lnk-github-prepare]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md

