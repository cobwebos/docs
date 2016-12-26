---
title: "使用设备克隆属性 | Microsoft Docs"
description: "本教程演示如何使用设备克隆属性"
services: iot-hub
documentationcenter: .net
author: fsautomata
manager: timlt
editor: 
ms.assetid: 3c627476-f982-43c9-bd17-e0698c5d236d
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/13/2016
ms.author: elioda
translationtype: Human Translation
ms.sourcegitcommit: 00746fa67292fa6858980e364c88921d60b29460
ms.openlocfilehash: 34e59b5ef344b48b57418d5cdb6e84b06ee07c43


---
# <a name="tutorial-use-desired-properties-to-configure-devices"></a>教程：使用所需属性配置设备
[!INCLUDE [iot-hub-selector-twin-how-to-configure](../../includes/iot-hub-selector-twin-how-to-configure.md)]

在本教程结束时，你将具有两个 Node.js 控制台应用程序：

* **SimulateDeviceConfiguration.js**，一个模拟设备应用，它等待所需配置更新并报告模拟配置更新过程的状态。
* **SetDesiredConfigurationAndQuery**，一个旨在从后端运行的 .NET 控制台应用程序，它在设备上设置所需配置并查询配置更新过程。

> [!NOTE]
> [Azure IoT SDK][lnk-hub-sdks] 一文提供了可用于构建设备和后端应用程序的 Azure IoT SDK 的信息。
> 
> 

若要完成本教程，需要满足以下条件：

* Microsoft Visual Studio 2015。
* Node.js 版本 0.10.x 或更高版本。
* 有效的 Azure 帐户。 （如果没有帐户，只需花费几分钟就能创建一个[免费帐户][lnk-free-trial]。）

如果按照[设备克隆入门][lnk-twin-tutorial]教程操作，则你已经有一个 IoT 中心和一个称作 **myDeviceId** 的设备标识；你可以跳到[创建模拟设备应用][lnk-how-to-configure-createapp]部分。

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
4. 将以下代码添加到 **SimulateDeviceConfiguration.js** 文件中，并将 **{设备连接字符串}** 占位符替换为在创建 **myDeviceId** 设备标识时复制的连接字符串：
   
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
   
    **客户端**对象公开从设备与设备克隆进行交互所需的所有方法。 上面的代码在初始化“客户端”对象后会检索 **myDeviceId** 的设备克隆，并在所需属性上附加用于更新的处理程序。 该处理程序通过比较 configId 验证是否有实际的配置更改，然后调用用于启动配置更改的方法。
   
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
   
    **initConfigChange** 方法使用配置更新请求更新本地设备克隆对象的报告属性，并将状态设置为“挂起”，然后更新服务的设备克隆。 成功更新设备克隆后，它会模拟在执行 **completeConfigChange** 期间终止的长时间运行的进程。 此方法更新本地报告属性，将状态设置为“成功”并删除 **pendingConfig** 对象。 然后，它会更新服务的设备克隆。
   
    请注意，为了节省带宽，仅通过指定要修改的属性（在上述代码中名为 **patch**）而不是替换整个文档来更新报告属性。
   
   > [!NOTE]
   > 本教程不模拟并发配置更新的任何行为。 某些配置更新进程在更新运行过程中可能能够适应目标配置的更改，某些配置更新进程则可能必须将它们排队，而其他配置更新进程会拒绝它们并显示错误情况。 请确保考虑你的特定配置过程所需的行为，并在开始配置更改之前添加相应的逻辑。
   > 
   > 
6. 运行设备应用：
   
        node SimulateDeviceConfiguration.js
   
    你应该看到消息 `retrieved device twin`。 保持运行该应用。

## <a name="create-the-service-app"></a>创建服务应用
在本部分中，将创建一个 .NET 控制台应用，该应用通过新的遥测配置对象更新与 **myDeviceId** 关联的设备克隆的所需属性。 然后，它查询存储在 IoT 中心的设备克隆，并显示该设备的所需配置和报告配置之间的差异。

1. 在 Visual Studio 中，使用“ **控制台应用程序** ”项目模板将 Visual C# Windows 经典桌面项目添加到当前解决方案。 将项目命名为 **SetDesiredConfigurationAndQuery**。
   
    ![新的 Visual C# Windows 经典桌面项目][img-createapp]
2. 在“解决方案资源管理器”中，右键单击“**SetDesiredConfigurationAndQuery**”项目，然后单击“**管理 NuGet 包**”。
3. 在“Nuget 包管理器”窗口中，选择“浏览”，搜索 **microsoft.azure.devices**，选择“安装”以安装 **Microsoft.Azure.Devices** 包，然后接受使用条款。 此过程将下载、安装 [Microsoft Azure IoT Service SDK][lnk-nuget-service-sdk]（Microsoft Azure IoT 服务 SDK）NuGet 包及其依赖项并添加对它的引用。
   
    ![“Nuget 包管理器”窗口][img-servicenuget]
4. 在 **Program.cs** 文件顶部添加以下 `using` 语句：
   
        using Microsoft.Azure.Devices;
        using System.Threading;
        using Newtonsoft.Json;
5. 将以下字段添加到 **Program** 类。 将占位符值替换为在上一部分中为 IoT 中心创建的连接字符串。
   
        static RegistryManager registryManager;
        static string connectionString = "{iot hub connection string}";
6. 将以下方法添加到 **Program** 类：
   
        static private async Task SetDesiredConfigurationAndQuery()
        {
            var twin = await registryManager.GetTwinAsync("myDeviceId");
            var patch = new {
                    properties = new {
                        desired = new {
                            telemetryConfig = new {
                                configId = Guid.NewGuid().ToString(),
                                sendFrequency = "5m"
                            }
                        }
                    }
                };
   
            await registryManager.UpdateTwinAsync(twin.DeviceId, JsonConvert.SerializeObject(patch), twin.ETag);
            Console.WriteLine("Updated desired configuration");
   
            while (true)
            {
                var query = registryManager.CreateQuery("SELECT * FROM devices WHERE deviceId = 'myDeviceId'");
                var results = await query.GetNextAsTwinAsync();
                foreach (var result in results)
                {
                    Console.WriteLine("Config report for: {0}", result.DeviceId);
                    Console.WriteLine("Desired telemetryConfig: {0}", JsonConvert.SerializeObject(result.Properties.Desired["telemetryConfig"], Formatting.Indented));
                    Console.WriteLine("Reported telemetryConfig: {0}", JsonConvert.SerializeObject(result.Properties.Reported["telemetryConfig"], Formatting.Indented));
                    Console.WriteLine();
                }
                Thread.Sleep(10000);
            }
        }
   
    **Registry** 对象公开从服务与设备克隆进行交互所需的所有方法。 前面的代码在初始化 **Registry** 对象后检索 **myDeviceId** 的设备克隆，并使用新的遥测配置对象更新其所需属性。
    然后，该代码会每隔 10 秒钟查询一次存储在 IoT 中心的设备克隆，并打印所需遥测配置和报告遥测配置。 若要了解如何跨所有设备生成丰富的报告，请参阅 [IoT 中心查询语言][lnk-query]。
   
   > [!IMPORTANT]
   > 此应用程序每 10 秒查询一次 IoT 中心以用于说明目的。 使用查询跨多个设备生成面向用户的报表，而不检测更改。 如果解决方案需要设备事件的实时通知，请使用[设备到云消息][lnk-d2c]。
   > 
   > 
7. 最后，在 **Main** 方法中添加以下行：
   
        registryManager = RegistryManager.CreateFromConnectionString(connectionString);
        SetDesiredConfigurationAndQuery();
        Console.WriteLine("Press any key to quit.");
        Console.ReadLine();
8. 在 **SimulateDeviceConfiguration.js** 运行时，使用 **F5** 从 Visual Studio 运行 .NET 应用程序，你应看到报告配置采用五分钟（而不是 24 小时）的新活动发送频率从 **Success** 更改为 **Pending**，然后再次更改为 **Success**。
   
   > [!IMPORTANT]
   > 设备报告操作与查询结果之间最多存在一分钟的延迟。 这是为了使查询基础结构可以采用非常大的规模来工作。 若要检索单个设备克隆的一致视图，请使用 **Registry** 类中的 **getDeviceTwin** 方法。
   > 
   > 

## <a name="next-steps"></a>后续步骤
在本教程中，从后端将所需配置设置为所需属性，以及编写了一个设备应用来检测该更改并模拟多步骤更新过程（通过报告属性报告其状态）。

使用下列资源了解如何执行以下操作：

* 通过 [IoT 中心入门][lnk-iothub-getstarted]教程学习如何从设备发送遥测；
* 查看[计划和广播作业][lnk-schedule-jobs]教程，学习如何对大型设备集计划或执行操作。
* 通过[使用直接方法][lnk-methods-tutorial]教程学习如何以交互方式控制设备（例如从用户控制的应用打开风扇）。

<!-- images -->
[img-servicenuget]: media/iot-hub-csharp-node-twin-getstarted/servicesdknuget.png
[img-createapp]: media/iot-hub-csharp-node-twin-getstarted/createnetapp.png

<!-- links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/1.1.0/

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-dm-overview]: iot-hub-device-management-overview.md
[lnk-twin-tutorial]: iot-hub-node-node-twin-getstarted.md
[lnk-schedule-jobs]: iot-hub-node-node-schedule-jobs.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/
[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-iothub-getstarted]: iot-hub-node-node-getstarted.md
[lnk-methods-tutorial]: iot-hub-node-node-direct-methods.md

[lnk-guid]: https://en.wikipedia.org/wiki/Globally_unique_identifier

[lnk-how-to-configure-createapp]: iot-hub-node-node-twin-how-to-configure.md#create-the-simulated-device-app



<!--HONumber=Nov16_HO5-->


