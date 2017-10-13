---
title: "使用 Azure IoT 中心设备孪生属性 (.NET/.NET) | Microsoft Docs"
description: "如何使用 Azure IoT 中心设备孪生配置设备。 使用用于 .NET 的 Azure IoT 设备 SDK 实现模拟设备应用，并使用用于 .NET 的 Azure IoT 服务 SDK 实现可使用设备孪生更改设备配置的服务应用。"
services: iot-hub
documentationcenter: .net
author: dsk-2015
manager: timlt
editor: 
ms.assetid: 3c627476-f982-43c9-bd17-e0698c5d236d
ms.service: iot-hub
ms.devlang: csharp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/10/2017
ms.author: dkshir
ms.openlocfilehash: 679cda28bf3ce9fb207fe3693a3453b355f1de15
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="use-desired-properties-to-configure-devices"></a>使用所需属性配置设备
[!INCLUDE [iot-hub-selector-twin-how-to-configure](../../includes/iot-hub-selector-twin-how-to-configure.md)]

在本教程结束时，会创建两个 .NET 控制台应用：

* **SimulateDeviceConfiguration**，一个模拟设备应用，它等待所需配置更新并报告模拟配置更新过程的状态。
* **SetDesiredConfigurationAndQuery**，一个后端应用，用于在设备上设置所需配置并查询配置更新过程。

> [!NOTE]
> [Azure IoT SDK][lnk-hub-sdks] 文章介绍了可用于构建设备和后端应用的 Azure IoT SDK。
> 
> 

若要完成本教程，需要满足以下条件：

* Visual Studio 2015 或 Visual Studio 2017。
* 有效的 Azure 帐户。 如果没有帐户，只需几分钟即可创建一个[免费帐户][lnk-free-trial]。

如果已按照[设备孪生入门][lnk-twin-tutorial]教程执行了操作，则现在已有一个 IoT 中心和一个名为 **myDeviceId** 的设备标识。 在这种情况下，可以跳到[创建模拟设备应用][lnk-how-to-configure-createapp]部分。

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

<a id="#create-the-simulated-device-app"></a>
## <a name="create-the-simulated-device-app"></a>创建模拟设备应用
在此部分中，会创建一个 .NET 控制台应用，它作为 **myDeviceId** 连接到中心，等待所需配置更新，然后对模拟配置更新过程报告更新。

1. 在 Visual Studio 中，使用“控制台应用程序”项目模板创建新的 Visual C# Windows 经典桌面项目。 将项目命名为 **SimulateDeviceConfiguration**。
   
    ![新建 Visual C# Windows 经典设备应用][img-createdeviceapp]

1. 在解决方案资源管理器中，右键单击“SimulateDeviceConfiguration”项目，单击“管理 NuGet 包...”。
1. 在“NuGet 包管理器”窗口中，选择“浏览”，然后搜索“microsoft.azure.devices.client”。 选择“安装”以安装“Microsoft.Azure.Devices.Client”包，并接受使用条款。 该过程将下载、安装 [Azure IoT 设备 SDK][lnk-nuget-client-sdk] NuGet 包及其依赖项并添加对它的引用。
   
    ![“NuGet 包管理器”窗口客户端应用][img-clientnuget]
1. 在 **Program.cs** 文件顶部添加以下 `using` 语句：
   
        using Microsoft.Azure.Devices.Client;
        using Microsoft.Azure.Devices.Shared;
        using Newtonsoft.Json;

1. 将以下字段添加到 **Program** 类。 将占位符值替换为上一部分中所述的设备连接字符串。
   
        static string DeviceConnectionString = "HostName=<yourIotHubName>.azure-devices.net;DeviceId=<yourIotDeviceName>;SharedAccessKey=<yourIotDeviceAccessKey>";
        static DeviceClient Client = null;
        static TwinCollection reportedProperties = new TwinCollection();

1. 将以下方法添加到 **Program** 类：
 
        public static void InitClient()
        {
            try
            {
                Console.WriteLine("Connecting to hub");
                Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, TransportType.Mqtt);
            }
            catch (Exception ex)
            {
                Console.WriteLine();
                Console.WriteLine("Error in sample: {0}", ex.Message);
            }
        }
    **Client** 对象公开从该设备与设备孪生交互所需的所有方法。 代码如上所示，初始化 Client 对象，然后检索 myDeviceId 的设备孪生。

1. 将以下方法添加到 **Program** 类。 此方法在本地设备上设置遥测初始值，然后更新设备孪生。

        public static async void InitTelemetry()
        {
            try
            {
                Console.WriteLine("Report initial telemetry config:");
                TwinCollection telemetryConfig = new TwinCollection();
                
                telemetryConfig["configId"] = "0";
                telemetryConfig["sendFrequency"] = "24h";
                reportedProperties["telemetryConfig"] = telemetryConfig;
                Console.WriteLine(JsonConvert.SerializeObject(reportedProperties));

                await Client.UpdateReportedPropertiesAsync(reportedProperties);
            }
            catch (AggregateException ex)
            {
                foreach (Exception exception in ex.InnerExceptions)
                {
                    Console.WriteLine();
                    Console.WriteLine("Error in sample: {0}", exception);
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine();
                Console.WriteLine("Error in sample: {0}", ex.Message);
            }
        }

1. 将以下方法添加到 **Program** 类。 这是一个回调，用于检测设备孪生的“所需属性”中的更改。

        private static async Task OnDesiredPropertyChanged(TwinCollection desiredProperties, object userContext)
        {
            try
            {
                Console.WriteLine("Desired property change:");
                Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));

                var currentTelemetryConfig = reportedProperties["telemetryConfig"];
                var desiredTelemetryConfig = desiredProperties["telemetryConfig"];

                if ((desiredTelemetryConfig != null) && (desiredTelemetryConfig["configId"] != currentTelemetryConfig["configId"]))
                {
                    Console.WriteLine("\nInitiating config change");
                    currentTelemetryConfig["status"] = "Pending";
                    currentTelemetryConfig["pendingConfig"] = desiredTelemetryConfig;

                    await Client.UpdateReportedPropertiesAsync(reportedProperties);

                    CompleteConfigChange();
                }
            }
            catch (AggregateException ex)
            {
                foreach (Exception exception in ex.InnerExceptions)
                {
                    Console.WriteLine();
                    Console.WriteLine("Error in sample: {0}", exception);
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine();
                Console.WriteLine("Error in sample: {0}", ex.Message);
            }
        }

    此方法使用配置更新请求更新本地设备孪生对象的报告属性，将状态设置为 **Pending**，然后更新服务的设备孪生。 成功更新设备孪生后，它会通过调用下一要点中所述的方法 `CompleteConfigChange` 来完成配置更改。

1. 将以下方法添加到 **Program** 类。 此方法模拟设备重置，更新将状态设置为 **Success** 的本地报告属性，并删除 **pendingConfig** 元素。 然后，它会在服务上更新设备孪生。 

        public static async void CompleteConfigChange()
        {
            try
            {
                var currentTelemetryConfig = reportedProperties["telemetryConfig"];

                Console.WriteLine("\nSimulating device reset");
                await Task.Delay(30000); 

                Console.WriteLine("\nCompleting config change");
                currentTelemetryConfig["configId"] = currentTelemetryConfig["pendingConfig"]["configId"];
                currentTelemetryConfig["sendFrequency"] = currentTelemetryConfig["pendingConfig"]["sendFrequency"];
                currentTelemetryConfig["status"] = "Success";
                currentTelemetryConfig["pendingConfig"] = null;

                await Client.UpdateReportedPropertiesAsync(reportedProperties);
                Console.WriteLine("Config change complete \nPress any key to exit.");
            }
            catch (AggregateException ex)
            {
                foreach (Exception exception in ex.InnerExceptions)
                {
                    Console.WriteLine();
                    Console.WriteLine("Error in sample: {0}", exception);
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine();
                Console.WriteLine("Error in sample: {0}", ex.Message);
            }
        }

1. 最后，在 **Main** 方法中添加以下行：

        try
        {
            InitClient();
            InitTelemetry();

            Console.WriteLine("Wait for desired telemetry...");
            Client.SetDesiredPropertyUpdateCallback(OnDesiredPropertyChanged, null).Wait();
            Console.ReadKey();
        }
        catch (AggregateException ex)
        {
            foreach (Exception exception in ex.InnerExceptions)
            {
                Console.WriteLine();
                Console.WriteLine("Error in sample: {0}", exception);
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
        }

   > [!NOTE]
   > 本教程不模拟并发配置更新的任何行为。 某些配置更新进程在更新运行过程中可能能够适应目标配置的更改，某些配置更新进程则可能必须将它们排队，某些配置更新进程会拒绝它们并显示错误情况。 请确保考虑特定配置过程所需的行为，并在开始配置更改之前添加相应的逻辑。
   > 
   > 
1. 生成解决方案，单击 **F5** 从 Visual Studio 运行设备应用。 在输出控制台中，应会看到消息指示模拟设备正在检索设备孪生、设置遥测，并等待所需属性发生更改。 保持运行该应用。

## <a name="create-the-service-app"></a>创建服务应用
在本部分中，将创建一个 .NET 控制台应用，该应用通过新的遥测配置对象更新与 **myDeviceId** 关联的设备孪生的所需属性。 然后，它会查询在 IoT 中心内存储的设备孪生，并显示所需的设备配置与报告的设备配置之间的差异。

1. 在 Visual Studio 中，使用“ **控制台应用程序** ”项目模板将 Visual C# Windows 经典桌面项目添加到当前解决方案。 将项目命名为 **SetDesiredConfigurationAndQuery**。
   
    ![新的 Visual C# Windows 经典桌面项目][img-createapp]
1. 在“解决方案资源管理器”中，右键单击“SetDesiredConfigurationAndQuery”项目，并单击“管理 NuGet 包...”。
1. 在“NuGet 包管理器”窗口中，选择“浏览”，搜索 **microsoft.azure.devices**，选择“安装”以安装 **Microsoft.Azure.Devices** 包，并接受使用条款。 该过程将下载、安装 [Azure IoT 服务 SDK][lnk-nuget-service-sdk] NuGet 包及其依赖项并添加对它的引用。
   
    ![“NuGet 包管理器”窗口][img-servicenuget]
1. 在 **Program.cs** 文件顶部添加以下 `using` 语句：
   
        using Microsoft.Azure.Devices;
        using System.Threading;
        using Newtonsoft.Json;
1. 将以下字段添加到 **Program** 类。 将占位符值替换为在上一部分为中心创建的 IoT 中心连接字符串。
   
        static RegistryManager registryManager;
        static string connectionString = "{iot hub connection string}";
1. 将以下方法添加到 **Program** 类：
   
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
   
    **Registry** 对象公开从服务与设备孪生进行交互所需的所有方法。 此代码将初始化 **Registry** 对象，检索 **myDeviceId** 的设备孪生，并使用新的遥测配置对象更新其所需属性。
    然后，该代码会每隔 10 秒钟查询一次存储在 IoT 中心的设备孪生，并打印所需遥测配置和报告遥测配置。 请参阅 [IoT 中心查询语言][lnk-query]以了解如何跨所有设备生成丰富的报告。
   
   > [!IMPORTANT]
   > 此应用程序每 10 秒查询一次 IoT 中心以用于说明目的。 使用查询跨多个设备生成面向用户的报表，而不检测更改。 如果解决方案需要设备事件的实时通知，请使用[孪生通知][lnk-twin-notifications]。
   > 
   > 
1. 最后，在 **Main** 方法中添加以下行：
   
        registryManager = RegistryManager.CreateFromConnectionString(connectionString);
        SetDesiredConfigurationAndQuery();
        Console.WriteLine("Press any key to quit.");
        Console.ReadLine();
1. 在“解决方案资源管理器”中，打开“设置启动项目...”，并确保 **SetDesiredConfigurationAndQuery** 项目的“操作”为“启动”。 生成解决方案。
1. 在运行 **SimulateDeviceConfiguration** 设备应用的情况下，使用 **F5** 从 Visual Studio 运行服务应用。 应会看到报告的配置从 **Pending** 更改为 **Success**，此时新的活动发送频率已变为 5 分钟而不是 24 小时。

 ![已成功配置设备][img-deviceconfigured]
   
   > [!IMPORTANT]
   > 设备报告操作与查询结果之间最多存在一分钟的延迟。 这是为了使查询基础结构可以采用非常大的规模来工作。 若要检索单个设备孪生的一致视图，请使用 **Registry** 类中的 **getDeviceTwin** 方法。
   > 
   > 

## <a name="next-steps"></a>后续步骤
在本教程中，用户已从解决方案后端将所需配置设置为所需属性，此外还编写了一个设备应用来检测该更改并模拟多步骤更新过程（通过报告属性报告其状态）。

使用下列资源了解如何执行以下操作：

* 通过 [IoT 中心入门][lnk-iothub-getstarted]教程学习如何从设备发送遥测；
* 查看[计划和广播作业][lnk-schedule-jobs]教程，学习如何对大型设备集计划或执行操作。
* 通过[使用直接方法][lnk-methods-tutorial]教程学习如何以交互方式控制设备（例如如何从用户控制的应用打开风扇）。

<!-- images -->
[img-servicenuget]: media/iot-hub-csharp-csharp-twin-how-to-configure/servicesdknuget.png
[img-createapp]: media/iot-hub-csharp-csharp-twin-how-to-configure/createnetapp.png
[img-deviceconfigured]: media/iot-hub-csharp-csharp-twin-how-to-configure/deviceconfigured.png
[img-createdeviceapp]: media/iot-hub-csharp-csharp-twin-how-to-configure/createdeviceapp.png
[img-clientnuget]: media/iot-hub-csharp-csharp-twin-how-to-configure/devicesdknuget.png
[img-deviceconfigured]: media/iot-hub-csharp-csharp-twin-how-to-configure/deviceconfigured.png


<!-- links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-nuget-client-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/1.1.0/

[lnk-query]: iot-hub-devguide-query-language.md
[lnk-twin-notifications]: iot-hub-devguide-device-twins.md#back-end-operations
[lnk-twin-tutorial]: iot-hub-csharp-csharp-twin-getstarted.md
[lnk-schedule-jobs]: iot-hub-node-node-schedule-jobs.md
[lnk-iothub-getstarted]: iot-hub-csharp-csharp-getstarted.md
[lnk-methods-tutorial]: iot-hub-node-node-direct-methods.md
[lnk-how-to-configure-createapp]: iot-hub-csharp-csharp-twin-how-to-configure.md#create-the-simulated-device-app
