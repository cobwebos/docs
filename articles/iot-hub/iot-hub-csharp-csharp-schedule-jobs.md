---
title: 使用 Azure IoT 中心安排作业 (.NET/.NET) | Microsoft Docs
description: 如何安排 Azure IoT 中心作业对多台设备调用直接方法。 使用适用于 .NET 的 Azure IoT 设备 SDK 实现模拟设备应用以及用于运行作业的服务应用。
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/06/2018
ms.author: dobett
ms.openlocfilehash: beb1e1e166325cb41a5d4e4fa07565b1f3d4b3bb
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2018
ms.locfileid: "38666910"
---
# <a name="schedule-and-broadcast-jobs-netnet"></a>计划和广播作业 (.NET/.NET)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

使用 Azure IoT 中心来计划和跟踪可更新数百万台设备的作业。 使用作业可以：

* 更新所需属性
* 更新标记
* 调用直接方法

一个作业包装上述一项操作，并跟踪一组设备中的执行进度（由设备孪生查询定义）。 例如，后端应用可以使用作业在 10,000 台设备上调用直接方法来重启设备。 使用设备孪生查询指定设备集，并将作业计划为在以后运行。 每个设备接收和执行“重新启动”直接方法时，该作业会跟踪进度。

若要详细了解其中的每项功能，请参阅：

* 设备孪生和属性：[设备孪生入门][lnk-get-started-twin]和[教程：如何使用设备孪生属性][lnk-twin-props]
* 直接方法：[IoT 中心开发人员指南 - 直接方法][lnk-dev-methods]和[教程：使用直接方法][lnk-c2d-methods]

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

本教程演示如何：

* 创建一个设备应用，用于实现名为 LockDoor、可由后端应用调用的直接方法。
* 创建一个后端应用，用于创建一个作业在多个设备上调用 LockDoor 直接方法。 另一个作业将所需的属性更新发送到多个设备。

在本教程结束时，会获得两个 .NET (C#) 控制台应用：

SimulateDeviceMethods，该应用连接到 IoT 中心并实现 LockDoor 直接方法。

ScheduleJob，该应用使用作业来调用 LockDoor 直接方法，并在多个设备上更新设备孪生的所需属性。

要完成本教程，需要以下各项：

* Visual Studio 2015 或 Visual Studio 2017。
* 有效的 Azure 帐户。 如果没有帐户，只需几分钟即可创建一个[免费帐户][lnk-free-trial]。

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]


## <a name="create-a-simulated-device-app"></a>创建模拟设备应用程序
在本部分中，会创建一个 .NET 控制台应用，用于响应解决方案后端调用的直接方法。

1. 在 Visual Studio 中，使用“ **控制台应用程序** ”项目模板将 Visual C# Windows 经典桌面项目添加到当前解决方案。 将项目命名为 **SimulateDeviceMethods**。
   
    ![新建 Visual C# Windows 经典设备应用][img-createdeviceapp]
    
1. 在解决方案资源管理器中，右键单击“SimulateDeviceMethods”项目，并单击“管理 NuGet 包...”。

1. 在“NuGet 包管理器”窗口中，选择“浏览”，然后搜索“microsoft.azure.devices.client”。 选择“安装”以安装“Microsoft.Azure.Devices.Client”包，并接受使用条款。 该过程将下载、安装 [Azure IoT 设备 SDK][lnk-nuget-client-sdk] NuGet 包及其依赖项并添加对它的引用。
   
    ![“NuGet 包管理器”窗口客户端应用][img-clientnuget]

1. 在 **Program.cs** 文件顶部添加以下 `using` 语句：
   
    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;

    using Newtonsoft.Json;
    ```

1. 将以下字段添加到 Program 类。 将占位符值替换为上一部分中所述的设备连接字符串：

    ```csharp
    static string DeviceConnectionString = "<yourDeviceConnectionString>";
    static DeviceClient Client = null;
    ```

1. 添加以下函数，实现设备上的直接方法：

    ```csharp
    static Task<MethodResponse> LockDoor(MethodRequest methodRequest, object userContext)
    {
        Console.WriteLine();
        Console.WriteLine("Locking Door!");
        Console.WriteLine("\nReturning response for method {0}", methodRequest.Name);
            
        string result = "'Door was locked.'";
        return Task.FromResult(new MethodResponse(Encoding.UTF8.GetBytes(result), 200));
    }
    ```

1. 添加以下内容，实现设备上的设备孪生侦听程序：

    ```csharp
    private static async Task OnDesiredPropertyChanged(TwinCollection desiredProperties, object userContext)
    {
        Console.WriteLine("Desired property change:");
        Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));
    }
    ```

1. 最后，将以下代码添加到 **Main** 方法，打开与 IoT 中心的连接并初始化方法侦听器：
   
    ```csharp
    try
    {
        Console.WriteLine("Connecting to hub");
        Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, TransportType.Mqtt);

        Client.SetMethodHandlerAsync("LockDoor", LockDoor, null);
        Client.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertyChanged, null);

        Console.WriteLine("Waiting for direct method call and device twin update\n Press enter to exit.");
        Console.ReadLine();

        Console.WriteLine("Exiting...");

        Client.SetMethodHandlerAsync("LockDoor", null, null);
        Client.CloseAsync().Wait();
    }
    catch (Exception ex)
    {
        Console.WriteLine();
        Console.WriteLine("Error in sample: {0}", ex.Message);
    }
    ```
        
1. 保存所做工作并构建解决方案。         

> [!NOTE]
> 为简单起见，本教程不实现任何重试策略。 在生产代码中，应按 MSDN 文章 [暂时性故障处理][lnk-transient-faults]中所述实施重试策略（例如连接重试）。
> 


## <a name="schedule-jobs-for-calling-a-direct-method-and-sending-device-twin-updates"></a>计划用于调用直接方法和发送设备孪生更新的作业

本部分使用 C# 创建一个 .NET 控制台应用，该应用使用作业来调用 LockDoor 直接方法，并将所需属性更新发送到多个设备。

1. 在 Visual Studio 中，使用“ **控制台应用程序** ”项目模板将 Visual C# Windows 经典桌面项目添加到当前解决方案。 将该项目命名为 **ScheduleJob**。

    ![新的 Visual C# Windows 经典桌面项目][img-createapp]

1. 在“解决方案资源管理器”中，右键单击“ScheduleJob”项目，并单击“管理 NuGet 包...”。

1. 在“NuGet 包管理器”窗口中，选择“浏览”，搜索 **microsoft.azure.devices**，选择“安装”以安装 **Microsoft.Azure.Devices** 包，并接受使用条款。 此步骤将下载、安装 [Azure IoT 服务 SDK][lnk-nuget-service-sdk] NuGet 包及其依赖项并添加对它的引用。

    ![“NuGet 包管理器”窗口][img-servicenuget]

1. 在 Program.cs 文件顶部添加以下 `using` 语句：
    
    ```csharp
    using Microsoft.Azure.Devices;
    using Microsoft.Azure.Devices.Shared;
    ```

1. 如果默认语句中不存在下面的 `using` 语句，请添加该语句。

    ```csharp
    using System.Threading;
    using System.Threading.Tasks;
    ```

1. 将以下字段添加到 Program 类。 将占位符替换为在上一部分为中心创建的 IoT 中心连接字符串以及设备的名称。

    ```csharp
    static JobClient jobClient;
    static string connString = "<yourIotHubConnectionString>";
    static string deviceId = "<yourDeviceId>";
    ```

1. 将以下方法添加到 **Program** 类：

    ```csharp
    public static async Task MonitorJob(string jobId)
    {
        JobResponse result;
        do
        {
            result = await jobClient.GetJobAsync(jobId);
            Console.WriteLine("Job Status : " + result.Status.ToString());
            Thread.Sleep(2000);
        } while ((result.Status != JobStatus.Completed) && (result.Status != JobStatus.Failed));
    }
    ```

1. 将以下方法添加到 **Program** 类：

    ```csharp
    public static async Task StartMethodJob(string jobId)
    {
        CloudToDeviceMethod directMethod = new CloudToDeviceMethod("LockDoor", TimeSpan.FromSeconds(5), TimeSpan.FromSeconds(5));
       
        JobResponse result = await jobClient.ScheduleDeviceMethodAsync(jobId,
            $"DeviceId IN ['{deviceId}']",
            directMethod,
            DateTime.UtcNow,
            (long)TimeSpan.FromMinutes(2).TotalSeconds);

        Console.WriteLine("Started Method Job");
    }
    ```

1. 向 Program 类添加其他方法：

    ```csharp
    public static async Task StartTwinUpdateJob(string jobId)
    {
        Twin twin = new Twin(deviceId);
        twin.Tags = new TwinCollection();
        twin.Tags["Building"] = "43";
        twin.Tags["Floor"] = "3";
        twin.ETag = "*";

        twin.Properties.Desired["LocationUpdate"] = DateTime.UtcNow;

        JobResponse createJobResponse = jobClient.ScheduleTwinUpdateAsync(
            jobId,
            $"DeviceId IN ['{deviceId}']", 
            twin, 
            DateTime.UtcNow, 
            (long)TimeSpan.FromMinutes(2).TotalSeconds).Result;

        Console.WriteLine("Started Twin Update Job");
    }
    ```

    > [!NOTE]
    > 有关查询语法的详细信息，请参阅 [IoT 中心查询语言][lnk-query]。
    > 

1. 最后，在 **Main** 方法中添加以下行：

    ```csharp
    Console.WriteLine("Press ENTER to start running jobs.");
    Console.ReadLine();

    jobClient = JobClient.CreateFromConnectionString(connString);

    string methodJobId = Guid.NewGuid().ToString();

    StartMethodJob(methodJobId);
    MonitorJob(methodJobId).Wait();
    Console.WriteLine("Press ENTER to run the next job.");
    Console.ReadLine();

    string twinUpdateJobId = Guid.NewGuid().ToString();

    StartTwinUpdateJob(twinUpdateJobId);
    MonitorJob(twinUpdateJobId).Wait();
    Console.WriteLine("Press ENTER to exit.");
    Console.ReadLine();
    ```

1. 保存所做工作并构建解决方案。 


## <a name="run-the-apps"></a>运行应用

现在可以运行应用了。

1. 在 Visual Studio 的“解决方案资源管理器”中，右键单击解决方案，再单击“生成”。 **多个启动项目**。 请确保 `SimulateDeviceMethods` 位于列表顶部且后接 `ScheduleJob`。 将这两项的操作均设置为“启动”，然后单击“确定”。

1. 单击“启动”以运行项目或转到“调试”菜单，然后单击“启动调试”。

1. 会出现设备和后端应用的输出。

    ![运行应用以计划作业][img-schedulejobs]


## <a name="next-steps"></a>后续步骤

在本教程中，使用了作业来安排用于设备的直接方法以及设备孪生属性的更新。

若要继续完成 IoT 中心和设备管理模式（如远程无线固件更新）的入门内容，请参阅[教程：如何更新固件][lnk-fwupdate]。

要了解如何将 AI 部署到具有 Azure IoT Edge 的边缘设备，请参阅 [IoT Edge 入门][lnk-iot-edge]。

<!-- images -->
[img-createdeviceapp]: ./media/iot-hub-csharp-csharp-schedule-jobs/create-device-app.png
[img-clientnuget]: ./media/iot-hub-csharp-csharp-schedule-jobs/device-app-nuget.png
[img-servicenuget]: media/iot-hub-csharp-csharp-schedule-jobs/servicesdknuget.png
[img-createapp]: media/iot-hub-csharp-csharp-schedule-jobs/createnetapp.png
[img-schedulejobs]: media/iot-hub-csharp-csharp-schedule-jobs/schedulejobs.png

[lnk-get-started-twin]: iot-hub-csharp-csharp-twin-getstarted.md
[lnk-twin-props]: iot-hub-csharp-csharp-twin-how-to-configure.md
[lnk-c2d-methods]: iot-hub-csharp-csharp-direct-methods.md
[lnk-dev-methods]: iot-hub-devguide-direct-methods.md
[lnk-fwupdate]: iot-hub-csharp-csharp-firmware-update.md
[lnk-iot-edge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://docs.microsoft.com/azure/architecture/best-practices/transient-faults
[lnk-nuget-client-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[lnk-query]: https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-query-language
