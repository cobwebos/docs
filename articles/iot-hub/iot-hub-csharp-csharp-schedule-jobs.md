---
title: 使用 Azure IoT 中心安排作业 (.NET/.NET) | Microsoft Docs
description: 逼 ﹚ Azure IoT 中心作业调用多个设备上的直接方法，请按照。 使用适用于 .NET 的 Azure IoT 设备 SDK 实现模拟设备应用以及用于运行作业的服务应用。
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: robinsh
ms.openlocfilehash: 4847a69e7ffaefca8e0c738aee6e1077f421ec62
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2019
ms.locfileid: "70049055"
---
# <a name="schedule-and-broadcast-jobs-net"></a>计划和广播作业 (.NET)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

使用 Azure IoT 中心来计划和跟踪可更新数百万台设备的作业。 使用作业可以：

* 更新所需属性

* 更新标记

* 调用直接方法

一个作业包装上述一项操作，并跟踪一组设备中的执行进度（由设备孪生查询定义）。 例如，后端应用可以使用作业在 10,000 台设备上调用直接方法来重启设备。 使用设备孪生查询指定设备集，并将作业计划为在以后运行。 每个设备接收和执行“重新启动”直接方法时，该作业会跟踪进度。

若要详细了解其中的每项功能，请参阅：

* 设备孪生和属性：[设备孪生入门](iot-hub-csharp-csharp-twin-getstarted.md)和[教程：如何使用设备孪生属性](tutorial-device-twins.md)

* 直接方法：[IoT 中心开发人员指南 - 直接方法](iot-hub-devguide-direct-methods.md)和[教程：使用直接方法](quickstart-control-device-dotnet.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

本教程演示如何：

* 创建一个设备应用, 用于实现一个名为**LockDoor**的直接方法, 可由后端应用调用。

* 创建一个后端应用，用于创建一个作业在多个设备上调用 LockDoor 直接方法。 另一个作业将所需的属性更新发送到多个设备。

在本教程结束时，会获得两个 .NET (C#) 控制台应用：

* **SimulateDeviceMethods**。 此应用连接到 IoT 中心并实现**LockDoor**直接方法。

* **ScheduleJob**。 此应用使用作业来调用**LockDoor**直接方法, 并在多个设备上更新设备的所需属性。

## <a name="prerequisites"></a>先决条件

要完成本教程，需要：

* Visual Studio。

* 有效的 Azure 帐户。 如果没有帐户，只需花费几分钟就能创建一个[免费帐户](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="create-an-iot-hub"></a>创建 IoT 中心

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>在 IoT 中心内注册新设备

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="create-a-simulated-device-app"></a>创建模拟设备应用程序

在本部分中，会创建一个 .NET 控制台应用，用于响应解决方案后端调用的直接方法。

1. 在 Visual Studio 中, 选择 "**创建新项目**", 然后选择 "**控制台应用 (.NET Framework)** " 项目模板。 选择“下一步”继续。

1. 在 "**配置新项目**" 中, 将项目命名为*SimulateDeviceMethods*, 然后选择 "**创建**"。

    ![配置 SimulateDeviceMethods 项目](./media/iot-hub-csharp-csharp-schedule-jobs/configure-device-app.png)

1. 在解决方案资源管理器中, 右键单击**SimulateDeviceMethods**项目, 然后选择 "**管理 NuGet 包**"。

1. 在**NuGet 包管理器**中, 选择 "**浏览**", 搜索并选择 " **Microsoft Azure**"。 选择“安装”。

    ![“NuGet 包管理器”窗口客户端应用](./media/iot-hub-csharp-csharp-schedule-jobs/device-app-nuget.png)

    此步骤将下载、安装[Azure IoT 设备 SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/) NuGet 包及其依赖项并添加对它的引用。

1. 在 Program.cs 文件顶部添加以下 `using` 语句：

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

1. 添加以下代码以实现设备上的直接方法:

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

1. 添加以下方法, 在设备上实现设备孪生侦听器:

    ```csharp
    private static async Task OnDesiredPropertyChanged(TwinCollection desiredProperties, 
      object userContext)
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
        Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, 
          TransportType.Mqtt);

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
> 为简单起见, 本教程不实现任何重试策略。 在生产代码中, 应按照[暂时性故障处理](/azure/architecture/best-practices/transient-faults)中的建议, 实现重试策略 (例如连接重试)。
>

## <a name="get-the-iot-hub-connection-string"></a>获取 IoT 中心连接字符串

[!INCLUDE [iot-hub-howto-schedule-jobs-shared-access-policy-text](../../includes/iot-hub-howto-schedule-jobs-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-registryrw-connection-string](../../includes/iot-hub-include-find-registryrw-connection-string.md)]

## <a name="schedule-jobs-for-calling-a-direct-method-and-sending-device-twin-updates"></a>计划用于调用直接方法和发送设备孪生更新的作业

本部分使用 C# 创建一个 .NET 控制台应用，该应用使用作业来调用 LockDoor 直接方法，并将所需属性更新发送到多个设备。

1. 在 Visual Studio 中，选择“文件” > “新建” > “项目”。 在 "**创建新项目**" 中, 选择 "**控制台应用 (.NET Framework)** ", 然后选择 "**下一步**"。

1. 在 "**配置新项目**" 中, 将项目命名为*ScheduleJob*。 对于 "**解决方案**", 选择 "**添加到解决方案**", 然后选择 "**创建**"。

    ![命名并配置 ScheduleJob 项目](./media/iot-hub-csharp-csharp-schedule-jobs/config-schedule-job-app.png)

1. 在解决方案资源管理器中, 右键单击**ScheduleJob**项目, 然后选择 "**管理 NuGet 包**"。

1. 在**NuGet 包管理器**中, 选择 "**浏览**", 搜索并选择 " **Microsoft Azure. 设备**", 然后选择 "**安装**"。

   此步骤将下载、安装 [Azure IoT 服务 SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices/) NuGet 包及其依赖项并添加对其的引用。

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

1. 将以下字段添加到 Program 类。 将占位符替换为之前在[获取 iot 中心连接字符串](#get-the-iot-hub-connection-string)和设备名称中复制的 iot 中心连接字符串。

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
        } while ((result.Status != JobStatus.Completed) && 
          (result.Status != JobStatus.Failed));
    }
    ```

1. 将以下方法添加到 **Program** 类：

    ```csharp
    public static async Task StartMethodJob(string jobId)
    {
        CloudToDeviceMethod directMethod = 
          new CloudToDeviceMethod("LockDoor", TimeSpan.FromSeconds(5), 
          TimeSpan.FromSeconds(5));

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
    > 有关查询语法的详细信息，请参阅 [IoT 中心查询语言](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-query-language)。
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

1. 在 Visual Studio 解决方案资源管理器中, 右键单击解决方案, 然后选择 "**设置启动项目**"。

1. 选择 "**通用属性** > " "**启动项目**", 然后选择 "**多启动项目**"。

1. 请确保 `SimulateDeviceMethods` 位于列表顶部且后接 `ScheduleJob`。 将它们的操作都设置为 "**启动**", 然后选择 **"确定"** 。

1. 单击“启动”以运行项目或转到“调试”菜单，然后单击“启动调试”。

   会出现设备和后端应用的输出。

    ![运行应用以计划作业](./media/iot-hub-csharp-csharp-schedule-jobs/schedule-jobs-console-results.png)

## <a name="next-steps"></a>后续步骤

在本教程中，使用了作业来安排用于设备的直接方法以及设备孪生属性的更新。

* 若要继续完成 IoT 中心和设备管理模式（如远程无线固件更新）的入门内容，请阅读[教程：如何执行固件更新](tutorial-firmware-update.md)。

* 若要了解如何将 AI 部署到具有 Azure IoT Edge 的边缘设备，请参阅 [IoT Edge 入门](../iot-edge/tutorial-simulate-device-linux.md)。
