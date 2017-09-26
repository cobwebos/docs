---
title: "使用 Azure IoT 中心的直接方法 (.NET/.NET) | Microsoft Docs"
description: "如何使用 Azure IoT 中心直接方法。 使用适用于 .NET 的 Azure IoT 设备 SDK 实现包含直接方法的模拟设备应用，并使用适用于 .NET 的 Azure IoT 服务 SDK 实现调用直接方法的服务应用。"
services: iot-hub
documentationcenter: 
author: dsk-2015
manager: timlt
editor: 
ms.assetid: ab035b8e-bff8-4e12-9536-f31d6b6fe425
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2017
ms.author: dkshir
ms.translationtype: HT
ms.sourcegitcommit: c3ea7cfba9fbf1064e2bd58344a7a00dc81eb148
ms.openlocfilehash: 9ce1fbebb6417c10618aa182e3c1d9ddf8132fb6
ms.contentlocale: zh-cn
ms.lasthandoff: 07/20/2017

---
# <a name="use-direct-methods-netnet"></a>使用直接方法 (.NET/.NET)
[!INCLUDE [iot-hub-selector-c2d-methods](../../includes/iot-hub-selector-c2d-methods.md)]

在本教程中，我们将开发两个 .NET 控制台应用：

* **CallMethodOnDevice**：一个后端应用，可调用模拟设备应用中的方法并显示响应。
* **SimulateDeviceMethods**：一个控制台应用，可模拟使用先前创建的设备标识连接到 IoT 中心的设备，并响应通过云调用的方法。

> [!NOTE]
> [Azure IoT SDK][lnk-hub-sdks] 一文提供了各种 Azure IoT SDK 的相关信息，用户可以使用这些 SDK 构建可在设备和解决方案后端上运行的应用程序。
> 
> 

要完成本教程，需要：

* Visual Studio 2015 或 Visual Studio 2017。
* 有效的 Azure 帐户。 （如果没有帐户，只需花费几分钟就能创建一个[免费帐户][lnk-free-trial]。）

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

如果想改为使用编程方式创建设备标识，请参阅[使用 .NET 将模拟设备连接到 IoT 中心][lnk-device-identity-csharp]一文中的相应部分。


## <a name="create-a-simulated-device-app"></a>创建模拟设备应用程序
在本部分中，会创建一个 .NET 控制台应用，用于响应解决方案后端调用的方法。

1. 在 Visual Studio 中，使用“ **控制台应用程序** ”项目模板将 Visual C# Windows 经典桌面项目添加到当前解决方案。 将项目命名为 **SimulateDeviceMethods**。
   
    ![新建 Visual C# Windows 经典设备应用][img-createdeviceapp]
    
1. 在解决方案资源管理器中，右键单击“SimulateDeviceMethods”项目，并单击“管理 NuGet 包...”。
1. 在“NuGet 包管理器”窗口中，选择“浏览”，然后搜索“microsoft.azure.devices.client”。 选择“安装”以安装“Microsoft.Azure.Devices.Client”包，并接受使用条款。 该过程将下载、安装 [Azure IoT 设备 SDK][lnk-nuget-client-sdk] NuGet 包及其依赖项并添加对它的引用。
   
    ![“NuGet 包管理器”窗口客户端应用][img-clientnuget]
1. 在 **Program.cs** 文件顶部添加以下 `using` 语句：
   
        using Microsoft.Azure.Devices.Client;
        using Microsoft.Azure.Devices.Shared;

1. 将以下字段添加到 **Program** 类。 将占位符值替换为上一部分中所述的设备连接字符串。
   
        static string DeviceConnectionString = "HostName=<yourIotHubName>.azure-devices.net;DeviceId=<yourIotDeviceName>;SharedAccessKey=<yourIotDeviceAccessKey>";
        static DeviceClient Client = null;

1. 添加以下函数，实现设备上的直接方法：

        static Task<MethodResponse> WriteLineToConsole(MethodRequest methodRequest, object userContext)
        {
            Console.WriteLine();
            Console.WriteLine("\t{0}", methodRequest.DataAsJson);
            Console.WriteLine("\nReturning response for method {0}", methodRequest.Name);

            string result = "'Input was written to log.'";
            return Task.FromResult(new MethodResponse(Encoding.UTF8.GetBytes(result), 200));
        }

1. 最后，将以下代码添加到 **Main** 方法，打开与 IoT 中心的连接并初始化方法侦听器：
   
        try
        {
            Console.WriteLine("Connecting to hub");
            Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, TransportType.Mqtt);

            // setup callback for "writeLine" method
            Client.SetMethodHandlerAsync("writeLine", WriteLineToConsole, null).Wait();
            Console.WriteLine("Waiting for direct method call\n Press enter to exit.");
            Console.ReadLine();

            Console.WriteLine("Exiting...");

            // as a good practice, remove the "writeLine" handler
            Client.SetMethodHandlerAsync("writeLine", null, null).Wait();
            Client.CloseAsync().Wait();
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
        }
        
1. 在 Visual Studio 的“解决方案资源管理器”中右键单击解决方案，并单击“设置启动项目...”。选择“单个启动项目”，并在下拉菜单中选择“SimulateDeviceMethods”项目。        

> [!NOTE]
> 为简单起见，本教程不实现任何重试策略。 在生产代码中，应按 MSDN 文章 [暂时性故障处理][lnk-transient-faults]中所述实施重试策略（例如连接重试）。
> 
> 

## <a name="call-a-direct-method-on-a-device"></a>在设备上调用直接方法
在本部分中，用户需创建一个 .NET 控制台应用，以便调用模拟设备应用中的方法，并显示响应。

1. 在 Visual Studio 中，使用“ **控制台应用程序** ”项目模板将 Visual C# Windows 经典桌面项目添加到当前解决方案。 确保 .NET Framework 版本为 4.5.1 或更高。 将项目命名为 **CallMethodOnDevice**。
   
    ![新的 Visual C# Windows 经典桌面项目][img-createserviceapp]
2. 在“解决方案资源管理器”中，右键单击“CallMethodOnDevice”项目，并单击“管理 NuGet 包...”。
3. 在“NuGet 包管理器”窗口中，选择“浏览”，搜索 **microsoft.azure.devices**，选择“安装”以安装 **Microsoft.Azure.Devices** 包，并接受使用条款。 该过程将下载、安装 [Azure IoT 服务 SDK][lnk-nuget-service-sdk] NuGet 包及其依赖项并添加对它的引用。
   
    ![“NuGet 包管理器”窗口][img-servicenuget]

4. 在 **Program.cs** 文件顶部添加以下 `using` 语句：
   
        using System.Threading.Tasks;
        using Microsoft.Azure.Devices;
5. 将以下字段添加到 **Program** 类。 将占位符值替换为在上一部分为中心创建的 IoT 中心连接字符串。
   
        static ServiceClient serviceClient;
        static string connectionString = "{iot hub connection string}";
6. 将以下方法添加到 **Program** 类：
   
        private static async Task InvokeMethod()
        {
            var methodInvocation = new CloudToDeviceMethod("writeLine") { ResponseTimeout = TimeSpan.FromSeconds(30) };
            methodInvocation.SetPayloadJson("'a line to be written'");

            var response = await serviceClient.InvokeDeviceMethodAsync("myDeviceId", methodInvocation);

            Console.WriteLine("Response status: {0}, payload:", response.Status);
            Console.WriteLine(response.GetPayloadAsJson());
        }
   
    此方法在 `myDeviceId` 设备上调用名为 `writeLine` 的直接方法。 然后，在控制台上写入设备提供的响应。 请注意，如何指定设备响应的超时值。
7. 最后，在 **Main** 方法中添加以下行：
   
        serviceClient = ServiceClient.CreateFromConnectionString(connectionString);
        InvokeMethod().Wait();
        Console.WriteLine("Press Enter to exit.");
        Console.ReadLine();

1. 在 Visual Studio 的“解决方案资源管理器”中右键单击解决方案，并单击“设置启动项目...”。选择“单个启动项目”，并在下拉菜单中选择“CallMethodOnDevice”项目。

## <a name="run-the-applications"></a>运行应用程序
现在，已准备就绪，可以运行应用程序了。

1. 运行 .NET 设备应用 **SimulateDeviceMethods**。 它应开始侦听来自 IoT 中心的方法调用： 

    ![运行设备应用][img-deviceapprun]
1. 设备已连接，正在等待方法调用，此时可运行 .NET **CallMethodOnDevice** 应用，调用模拟设备应用中的方法。 此时会看到写入控制台的设备响应。
   
    ![运行服务应用][img-serviceapprun]
1. 然后，该设备通过输出此消息来响应该方法：
   
    ![在设备上调用的直接方法][img-directmethodinvoked]

## <a name="next-steps"></a>后续步骤
在本教程中，你已在 Azure 门户中配置了新的 IoT 中心，并在 IoT 中心的标识注册表中创建了设备标识。 已通过此设备标识启用模拟设备应用的相关功能，使之能够响应通过云调用的方法。 还创建了一个应用，用于调用设备上的方法并显示来自设备的响应。 

若要继续了解 IoT 中心入门知识并浏览其他 IoT 方案，请参阅：

* [IoT 中心入门]
* [在多台设备上安排作业][lnk-devguide-jobs]

若要了解如何扩展 IoT 解决方案以及在多个设备上计划方法调用，请参阅[计划和广播作业][lnk-tutorial-jobs]教程。

<!-- Images. -->
[img-createdeviceapp]: ./media/iot-hub-csharp-csharp-direct-methods/create-device-app.png
[img-clientnuget]: ./media/iot-hub-csharp-csharp-direct-methods/device-app-nuget.png
[img-createserviceapp]: ./media/iot-hub-csharp-csharp-direct-methods/create-service-app.png
[img-servicenuget]: ./media/iot-hub-csharp-csharp-direct-methods/service-app-nuget.png
[img-deviceapprun]: ./media/iot-hub-csharp-csharp-direct-methods/run-device-app.png
[img-serviceapprun]: ./media/iot-hub-csharp-csharp-direct-methods/run-service-app.png
[img-directmethodinvoked]: ./media/iot-hub-csharp-csharp-direct-methods/direct-method-invoked.png

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-nuget-client-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/

[lnk-device-identity-csharp]: iot-hub-csharp-csharp-getstarted.md#DeviceIdentity_csharp

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-tutorial-jobs]: iot-hub-node-node-schedule-jobs.md

[IoT 中心入门]: iot-hub-node-node-getstarted.md

