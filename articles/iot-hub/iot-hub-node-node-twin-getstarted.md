---
title: "设备克隆入门 | Microsoft 文档"
description: "本教程介绍如何使用设备克隆"
services: iot-hub
documentationcenter: node
author: fsautomata
manager: timlt
editor: 
ms.assetid: 314c88e4-cce1-441c-b75a-d2e08e39ae7d
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/13/2016
ms.author: elioda
translationtype: Human Translation
ms.sourcegitcommit: 00746fa67292fa6858980e364c88921d60b29460
ms.openlocfilehash: d7b615476bb5e9ed08e1e84f63c1a40c11154b23


---
# <a name="tutorial-get-started-with-device-twins"></a>教程：设备克隆入门
[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

在本教程结束时，你将具有两个 Node.js 控制台应用程序：

* **AddTagsAndQuery.js**，它是应从后端运行的 Node.js 应用程序，用于添加标记并查询设备克隆。
* **TwinSimulatedDevice.js**，它是 Node.js 应用程序，用于模拟使用早先创建的设备标识连接到 IoT 中心的设备，并报告其连接状况。

> [!NOTE]
> [Azure IoT SDK][lnk-hub-sdks] 一文提供了可用于构建设备应用程序和后端应用程序的 Azure IoT SDK 的信息。
> 
> 

若要完成本教程，需要满足以下条件：

* Node.js 版本 0.10.x 或更高版本。
* 有效的 Azure 帐户。 （如果没有帐户，只需花费几分钟就能创建一个[免费帐户][lnk-free-trial]。）

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-the-service-app"></a>创建服务应用
在本部分中，将创建一个 Node.js 控制台应用，该应用将位置元数据添加到与 **myDeviceId** 关联的设备克隆。 然后，该应用将选择位于美国的设备来查询存储在 IoT 中心的设备克隆，然后查询报告移动电话网络连接的设备克隆。

1. 新建名为 **addtagsandqueryapp** 的空文件夹。 在命令提示符下的**addtagsandqueryapp** 文件夹中，使用以下命令创建新的 package.json 文件。 接受所有默认值：
   
    ```
    npm init
    ```
2. 在 **addtagsandqueryapp** 文件夹中，在命令提示符下运行以下命令以安装 **azure-iothub** 包：
   
    ```
    npm install azure-iothub --save
    ```
3. 使用文本编辑器，在 **addtagsandqueryapp** 文件夹中创建一个新的 **AddTagsAndQuery.js** 文件。
4. 将以下代码添加到 **AddTagsAndQuery.js** 文件中，并将 **{服务连接字符串}** 占位符替换为你在创建中心时复制的连接字符串：
   
        'use strict';
        var iothub = require('azure-iothub');
        var connectionString = '{service hub connection string}';
        var registry = iothub.Registry.fromConnectionString(connectionString);
   
        registry.getTwin('myDeviceId', function(err, twin){
            if (err) {
                console.error(err.constructor.name + ': ' + err.message);
            } else {
                var patch = {
                    tags: {
                        location: {
                            region: 'US',
                            plant: 'Redmond43'
                      }
                    }
                };
   
                twin.update(patch, function(err) {
                  if (err) {
                    console.error('Could not update twin: ' + err.constructor.name + ': ' + err.message);
                  } else {
                    console.log(twin.deviceId + ' twin updated successfully');
                    queryTwins();
                  }
                });
            }
        });
   
    **Registry** 对象公开从服务与设备克隆进行交互所需的所有方法。 前面的代码首先初始化 **Registry** 对象，然后检索 **myDeviceId** 的设备克隆，最后使用所需位置信息更新其标记。
   
    更新标记后，它将调用 **queryTwins** 函数。
5. 在 **AddTagsAndQuery.js** 末尾添加以下代码以实现 **queryTwins** 函数：
   
        var queryTwins = function() {
            var query = registry.createQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43'", 100);
            query.nextAsTwin(function(err, results) {
                if (err) {
                    console.error('Failed to fetch the results: ' + err.message);
                } else {
                    console.log("Devices in Redmond43: " + results.map(function(twin) {return twin.deviceId}).join(','));
                }
            });
   
            query = registry.createQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43' AND properties.reported.connectivity.type = 'cellular'", 100);
            query.nextAsTwin(function(err, results) {
                if (err) {
                    console.error('Failed to fetch the results: ' + err.message);
                } else {
                    console.log("Devices in Redmond43 using cellular network: " + results.map(function(twin) {return twin.deviceId}).join(','));
                }
            });
        };
   
    上面的代码执行两个查询：第一个仅选择位于 **Redmond43** 工厂的设备克隆，第二个将查询细化为仅选择还要通过蜂窝网络连接的设备。
   
    请注意上面的代码，当它创建 **query** 对象时，会指定返回的最大文档数。 **query** 对象包含 **hasMoreResults** 布尔值属性，你可以使用它多次调用 **nextAsTwin** 方法来检索所有结果。 名为 **next** 的方法可用于非设备克隆的结果（例如聚合查询的结果）。
6. 使用以下方法运行应用程序：
   
        node AddTagsAndQuery.js
   
    在查询位于 **Redmond43** 的所有设备的查询结果中，你应该会看到一个设备，而在将结果限制为使用蜂窝网络的设备的查询结果中没有任何设备。
   
    ![][1]

在下一部分中，创建的设备应用将报告连接信息，并更改上一部分中查询的结果。

## <a name="create-the-device-app"></a>创建设备应用
在本部分中，将创建一个 Node.js 控制台应用作为 **myDeviceId** 连接到中心，然后更新其设备克隆的报告属性，说明它是使用手机网络进行连接的。

> [!NOTE]
> 此时，只能从使用 MQTT 协议连接到 IoT 中心的设备访问设备克隆。 有关如何转换现有设备应用以使用 MQTT 的说明，请参阅 [MQTT 支持][lnk-devguide-mqtt]一文。
> 
> 

1. 新建名为 **reportconnectivity** 的空文件夹。 在 **reportconnectivity** 文件夹中，在命令提示符下使用以下命令创建新的 package.json 文件。 接受所有默认值：
   
    ```
    npm init
    ```
2. 在 **reportconnectivity** 文件夹中，在命令提示符下运行以下命令以安装 **azure-iot-device** 包和 **azure-iot-device-mqtt** 包：
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```
3. 使用文本编辑器，在 **reportconnectivity** 文件夹中创建一个新的 **ReportConnectivity.js** 文件。
4. 将以下代码添加到 **ReportConnectivity.js** 文件中，并将 **{设备连接字符串}** 占位符替换为你在创建 **myDeviceId** 设备标识时复制的连接字符串：
   
        'use strict';
        var Client = require('azure-iot-device').Client;
        var Protocol = require('azure-iot-device-mqtt').Mqtt;
   
        var connectionString = '{device connection string}';
        var client = Client.fromConnectionString(connectionString, Protocol);
   
        client.open(function(err) {
        if (err) {
            console.error('could not open IotHub client');
        }  else {
            console.log('client opened');
   
            client.getTwin(function(err, twin) {
            if (err) {
                console.error('could not get twin');
            } else {
                var patch = {
                    connectivity: {
                        type: 'cellular'
                    }
                };
   
                twin.properties.reported.update(patch, function(err) {
                    if (err) {
                        console.error('could not update twin');
                    } else {
                        console.log('twin state reported');
                        process.exit();
                    }
                });
            }
            });
        }
        });
   
    **Client** 对象公开从该设备与设备克隆交互所需的所有方法。 上面的代码在初始化 **Client** 对象后会检索 **myDeviceId** 的设备克隆，并使用连接信息更新其报告属性。
5. 运行设备应用
   
        node ReportConnectivity.js
   
    你应该看到消息 `twin state reported`。
6. 既然设备报告其连接的信息，该信息应显示在两个查询中。 转回到 **addtagsandqueryapp** 文件夹，然后再次运行查询：
   
        node AddTagsAndQuery.js
   
    这一次 **myDeviceId** 应显示在两个查询结果中。
   
    ![][3]

## <a name="next-steps"></a>后续步骤
在本教程中，你已在 Azure 门户中配置了新的 IoT 中心，然后在 IoT 中心的标识注册表中创建了设备标识。 你已从后端应用程序以标记形式添加了设备元数据，并编写了模拟的设备应用，用于报告设备克隆中的设备连接信息。 你还学习了如何使用类似于 SQL 的 IoT 中心查询语言查询此信息。

使用下列资源了解如何执行以下操作：

* 通过 [IoT 中心入门][lnk-iothub-getstarted]教程学习如何从设备发送遥测；
* 通过[使用所需属性配置设备][lnk-twin-how-to-configure]教程学习如何使用设备克隆的所需属性配置设备；
* 通过[使用直接方法][lnk-methods-tutorial]教程学习如何以交互方式控制设备（例如如何从用户控制的应用打开风扇）。

<!-- images -->
[1]: media/iot-hub-node-node-twin-getstarted/service1.png
[3]: media/iot-hub-node-node-twin-getstarted/service2.png

<!-- links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-identity]: iot-hub-devguide-identity-registry.md

[lnk-iothub-getstarted]: iot-hub-node-node-getstarted.md
[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/

[lnk-twin-how-to-configure]: iot-hub-node-node-twin-how-to-configure.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md

[lnk-methods-tutorial]: iot-hub-node-node-direct-methods.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md



<!--HONumber=Nov16_HO5-->


