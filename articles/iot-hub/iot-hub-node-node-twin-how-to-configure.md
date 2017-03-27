---
title: "使用 Azure IoT 中心设备孪生属性 (Node) | Microsoft Docs"
description: "如何使用 Azure IoT 中心设备孪生配置设备。 使用 Azure IoT SDK for Node.js 实现模拟设备应用，并实现可使用设备孪生更改设备配置的服务应用。"
services: iot-hub
documentationcenter: .net
author: fsautomata
manager: timlt
editor: 
ms.assetid: d0bcec50-26e6-40f0-8096-733b2f3071ec
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/13/2016
ms.author: elioda
translationtype: Human Translation
ms.sourcegitcommit: a243e4f64b6cd0bf7b0776e938150a352d424ad1
ms.openlocfilehash: 397dffe8ec93ced9196bce8fcc12a058c6876bd4


---
# <a name="use-desired-properties-to-configure-devices-node"></a>使用所需属性配置设备 (Node)
[!INCLUDE [iot-hub-selector-twin-how-to-configure](../../includes/iot-hub-selector-twin-how-to-configure.md)]

在本教程结束时，将会创建两个 Node.js 控制台应用：

* **SimulateDeviceConfiguration.js**，一个模拟设备应用，它等待所需配置更新并报告模拟配置更新过程的状态。
* **SetDesiredConfigurationAndQuery.js**（Node.js 后端应用），用于在设备上设置所需配置并查询配置更新过程。

> [!NOTE]
> [Azure IoT SDK][lnk-hub-sdks] 文章介绍了可用于构建设备和后端应用的 Azure IoT SDK。
> 
> 

若要完成本教程，需要满足以下条件：

* Node.js 版本 0.10.x 或更高版本。
* 有效的 Azure 帐户。 （如果没有帐户，只需花费几分钟就能创建一个[免费帐户][lnk-free-trial]。）

如果已按照[设备克隆入门][lnk-twin-tutorial]教程执行了操作，则你已经有一个 IoT 中心和一个名为 **myDeviceId** 的设备标识；你可以跳到[创建模拟设备应用][lnk-how-to-configure-createapp]部分。

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-the-simulated-device-app"></a>创建模拟设备应用
在此部分中，你会创建一个 Node.js 控制台应用，它作为 **myDeviceId** 连接到你的中心，等待所需配置更新，然后对模拟配置更新过程报告更新。

1. 新建名为 **simulatedeviceconfiguration** 的空文件夹。 在命令提示符处，使用以下命令在 **simulatedeviceconfiguration** 文件夹中创建新的 package.json 文件。 接受所有默认值：
   
    ```
    npm init
    ```
2. 在命令提示符处，运行以下命令在 **simulatedeviceconfiguration** 文件夹中安装 **azure-iot-device** 和 **azure-iot-device-mqtt** 包：
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```
3. 使用文本编辑器，在 **simulatedeviceconfiguration** 文件夹中创建新的 **SimulateDeviceConfiguration.js** 文件。
4. 将以下代码添加到 **SimulateDeviceConfiguration.js** 文件，并将 **{device connection string}** 占位符替换为创建 **myDeviceId** 设备标识时复制的设备连接字符串：
   
        'use strict';
        var Client = require('azure-iot-device').Client;
        var Protocol = require('azure-iot-device-mqtt').Mqtt;
   
        var connectionString = '{device connection string}';
        var client = Client.fromConnectionString(connectionString, Protocol);
   
        client.open(function(err) {
            if (err) {
                console.error('could not open IotHub client');
            } else {
                client.getTwin(function(err, twin) {
                    if (err) {
                        console.error('could not get twin');
                    } else {
                        console.log('retrieved device twin');
                        twin.properties.reported.telemetryConfig = {
                            configId: "0",
                            sendFrequency: "24h"
                        }
                        twin.on('properties.desired', function(desiredChange) {
                            console.log("received change: "+JSON.stringify(desiredChange));
                            var currentTelemetryConfig = twin.properties.reported.telemetryConfig;
                            if (desiredChange.telemetryConfig &&desiredChange.telemetryConfig.configId !== currentTelemetryConfig.configId) {
                                initConfigChange(twin);
                            }
                        });
                    }
                });
            }
        });
   
    **客户端**对象公开从设备与设备克隆进行交互所需的所有方法。 上面的代码在初始化 **Client** 对象后会检索 **myDeviceId** 的设备克隆，并在所需属性上附加用于更新的处理程序。 该处理程序通过比较 configId 验证是否有实际的配置更改，然后调用用于启动配置更改的方法。
   
    请注意，为了简单起见，上面的代码使用初始配置的硬编码默认值。 实际的应用可能会从本地存储加载该配置。
   
   > [!IMPORTANT]
   > 始终会在设备连接时立即发出所需属性更改事件，因此请务必先检查所需属性中是否有实际的更改，然后再执行任何操作。
   > 
   > 
5. 在 `client.open()` 调用前添加以下方法：
   
        var initConfigChange = function(twin) {
            var currentTelemetryConfig = twin.properties.reported.telemetryConfig;
            currentTelemetryConfig.pendingConfig = twin.properties.desired.telemetryConfig;
            currentTelemetryConfig.status = "Pending";
   
            var patch = {
            telemetryConfig: currentTelemetryConfig
            };
            twin.properties.reported.update(patch, function(err) {
                if (err) {
                    console.log('Could not report properties');
                } else {
                    console.log('Reported pending config change: ' + JSON.stringify(patch));
                    setTimeout(function() {completeConfigChange(twin);}, 60000);
                }
            });
        }
   
        var completeConfigChange =  function(twin) {
            var currentTelemetryConfig = twin.properties.reported.telemetryConfig;
            currentTelemetryConfig.configId = currentTelemetryConfig.pendingConfig.configId;
            currentTelemetryConfig.sendFrequency = currentTelemetryConfig.pendingConfig.sendFrequency;
            currentTelemetryConfig.status = "Success";
            delete currentTelemetryConfig.pendingConfig;
   
            var patch = {
                telemetryConfig: currentTelemetryConfig
            };
            patch.telemetryConfig.pendingConfig = null;
   
            twin.properties.reported.update(patch, function(err) {
                if (err) {
                    console.error('Error reporting properties: ' + err);
                } else {
                    console.log('Reported completed config change: ' + JSON.stringify(patch));
                }
            });
        };
   
    **initConfigChange** 方法使用配置更新请求更新本地设备孪生对象的报告属性，并将状态设置为“等待中”，然后更新服务的设备孪生。 成功更新设备克隆后，它会模拟在执行 **completeConfigChange** 期间终止的长时间运行的进程。 此方法会更新本地设备克隆的报告属性，将状态设置为 **Success** 并删除 **pendingConfig** 对象。 然后，它会更新服务上的设备克隆。
   
    请注意，为了节省带宽，仅通过指定要修改的属性（在上述代码中名为 **patch**）而不是替换整个文档来更新报告属性。
   
   > [!NOTE]
   > 本教程不模拟并发配置更新的任何行为。 某些配置更新进程在更新运行过程中可能能够适应目标配置的更改，某些配置更新进程则可能必须将它们排队，而其他配置更新进程会拒绝它们并显示错误情况。 请确保考虑你的特定配置过程所需的行为，并在开始配置更改之前添加相应的逻辑。
   > 
   > 
6. 运行设备应用：
   
        node SimulateDeviceConfiguration.js
   
    你应该看到消息 `retrieved device twin`。 保持运行该应用。

## <a name="create-the-service-app"></a>创建服务应用
在本部分中，你将创建一个 Node.js 控制台应用，它会使用新的遥测配置对象更新与 **myDeviceId** 关联的设备克隆的*所需属性*。 然后，它查询存储在 IoT 中心的设备克隆，并显示所需的设备配置与所报告的设备配置之间的差异。

1. 创建新的名为 **setdesiredandqueryapp** 的空文件夹。 在命令提示符处，使用以下命令在 **setdesiredandqueryapp** 文件夹中创建新的 package.json 文件。 接受所有默认值：
   
    ```
    npm init
    ```
2. 在命令提示符处，运行以下命令在 **setdesiredandqueryapp** 文件夹中安装 **azure iothub** 包：
   
    ```
    npm install azure-iothub node-uuid --save
    ```
3. 使用文本编辑器，在 **addtagsandqueryapp** 文件夹中创建新的 **SetDesiredAndQuery.js** 文件。
4. 将以下代码添加到 **SetDesiredAndQuery.js** 文件，并将 **{iot hub connection string}** 占位符替换为创建中心时复制的 IoT 中心连接字符串：
   
        'use strict';
        var iothub = require('azure-iothub');
        var uuid = require('node-uuid');
        var connectionString = '{iot hub connection string}';
        var registry = iothub.Registry.fromConnectionString(connectionString);
   
        registry.getTwin('myDeviceId', function(err, twin){
            if (err) {
                console.error(err.constructor.name + ': ' + err.message);
            } else {
                var newConfigId = uuid.v4();
                var newFrequency = process.argv[2] || "5m";
                var patch = {
                    properties: {
                        desired: {
                            telemetryConfig: {
                                configId: newConfigId,
                                sendFrequency: newFrequency
                            }
                        }
                    }
                }
                twin.update(patch, function(err) {
                    if (err) {
                        console.error('Could not update twin: ' + err.constructor.name + ': ' + err.message);
                    } else {
                        console.log(twin.deviceId + ' twin updated successfully');
                    }
                });
                setInterval(queryTwins, 10000);
            }
        });

    **Registry** 对象公开从服务与设备克隆进行交互所需的所有方法。 前面的代码在初始化 **Registry** 对象后检索 **myDeviceId** 的设备克隆，并使用新的遥测配置对象更新其所需属性。 随后，它每 10 秒调用一次 **queryTwins** 函数。

    > [!IMPORTANT]
    > 此应用程序每 10 秒查询一次 IoT 中心以用于说明目的。 使用查询跨多个设备生成面向用户的报表，而不检测更改。 如果解决方案需要设备事件的实时通知，请使用[“设备到云”消息][lnk-d2c]。
    > 
    >。

1. 在紧靠 `registry.getDeviceTwin()` 调用的前面添加下面的代码以实现 **queryTwins** 函数：
   
        var queryTwins = function() {
            var query = registry.createQuery("SELECT * FROM devices WHERE deviceId = 'myDeviceId'", 100);
            query.nextAsTwin(function(err, results) {
                if (err) {
                    console.error('Failed to fetch the results: ' + err.message);
                } else {
                    console.log();
                    results.forEach(function(twin) {
                        var desiredConfig = twin.properties.desired.telemetryConfig;
                        var reportedConfig = twin.properties.reported.telemetryConfig;
                        console.log("Config report for: " + twin.deviceId);
                        console.log("Desired: ");
                        console.log(JSON.stringify(desiredConfig, null, 2));
                        console.log("Reported: ");
                        console.log(JSON.stringify(reportedConfig, null, 2));
                    });
                }
            });
        };
   
    前面的代码查询存储在 IoT 中心内的设备克隆，并打印所需遥测配置和报告遥测配置。 请参阅 [IoT 中心查询语言][lnk-query]以了解如何跨所有设备生成丰富的报告。
2. 在 **SimulateDeviceConfiguration.js** 运行的情况下，使用以下方法运行应用程序：
   
        node SetDesiredAndQuery.js 5m
   
    你应该看到报告的配置从“**成功**”更改为“**挂起**”，再更改回“**成功**”，此时新的活动发送频率已变为五分钟而不是 24 小时。
   
   > [!IMPORTANT]
   > 设备报告操作与查询结果之间最多存在一分钟的延迟。 这是为了使查询基础结构可以采用非常大的规模来工作。 若要检索单个设备克隆的一致视图，请使用 **Registry** 类中的 **getDeviceTwin** 方法。
   > 
   > 

## <a name="next-steps"></a>后续步骤
在本教程中，从后端应用将所需配置设置为所需属性，并编写一个模拟设备应用来检测该更改及模拟多步骤更新过程（将其状态作为报告属性报告给设备孪生）。

使用下列资源了解如何执行以下操作：

* 通过 [IoT 中心入门][lnk-iothub-getstarted]教程学习如何从设备发送遥测；
* 查看[计划和广播作业][lnk-schedule-jobs]教程，学习如何对大型设备集计划或执行操作。
* 通过[使用直接方法][lnk-methods-tutorial]教程学习如何以交互方式控制设备（例如如何从用户控制的应用打开风扇）。

<!-- links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-dm-overview]: iot-hub-device-management-overview.md
[lnk-twin-tutorial]: iot-hub-node-node-twin-getstarted.md
[lnk-schedule-jobs]: iot-hub-node-node-schedule-jobs.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/
[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-iothub-getstarted]: iot-hub-node-node-getstarted.md
[lnk-methods-tutorial]: iot-hub-node-node-direct-methods.md

[lnk-guid]: https://en.wikipedia.org/wiki/Globally_unique_identifier

[lnk-how-to-configure-createapp]: iot-hub-node-node-twin-how-to-configure.md#create-the-simulated-device-app



<!--HONumber=Dec16_HO1-->


