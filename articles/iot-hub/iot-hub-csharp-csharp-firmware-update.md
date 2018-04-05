---
title: 使用 Azure IoT 中心更新设备固件 (.NET/.NET) | Microsoft Docs
description: 如何使用 Azure IoT 中心上的设备管理进行设备固件更新。 使用适用于 .NET 的 Azure IoT 设备 SDK 实现模拟设备应用，并使用适用于 .NET 的 Azure IoT 服务 SDK 实现可触发固件更新的服务应用。
services: iot-hub
documentationcenter: .net
author: JimacoMS2
manager: timlt
editor: ''
ms.assetid: ''
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/19/2017
ms.author: v-jamebr
ms.openlocfilehash: 984fd7516b5c05973c45891f4eda31c41a5a2be5
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2018
---
# <a name="use-device-management-to-initiate-a-device-firmware-update-netnet"></a>使用设备管理启动设备固件更新 (.NET/.NET)
[!INCLUDE [iot-hub-selector-firmware-update](../../includes/iot-hub-selector-firmware-update.md)]

## <a name="introduction"></a>介绍
在[设备管理入门][lnk-dm-getstarted]教程中，已了解如何使用[设备孪生][lnk-devtwin]和[直接方法][lnk-c2dmethod]基元来远程重新启动设备。 本教程使用相同的 IoT 中心基元，并演示如何进行端到端模拟固件更新。  此模式用于 [Raspberry Pi 设备实现示例][lnk-rpi-implementation]的固件更新实现。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

本教程演示如何：

* 创建一个 .NET 控制台应用，其通过 IoT 中心在模拟设备应用上调用 **firmwareUpdate** 直接方法。
* 创建实现 **firmwareUpdate** 直接方法的模拟设备应用。 此方法会启动等待下载固件映像、下载固件映像以及最后应用固件映像的多阶段过程。 在更新的每个阶段中，设备使用报告属性来报告进度。

本教程结束时，用户会有一个 .NET (C#) 控制台设备应用，以及一个 .NET (C#) 控制台后端应用：

* **SimulatedDeviceFwUpdate**，它使用先前创建的设备标识连接到 IoT 中心，接收 **firmwareUpdate** 直接方法，运行一个多状态过程以模拟固件更新，此过程包括：等待映像下载、下载新映像以及最后应用映像。

* **TriggerFWUpdate**，它使用服务 SDK 在模拟设备上远程调用 **firmwareUpdate** 直接方法、显示响应并定期（每 500 毫秒）显示更新的报告属性。

要完成本教程，需要以下各项：

* Visual Studio 2015 或 Visual Studio 2017。
* 有效的 Azure 帐户。 （如果没有帐户，只需花费几分钟就能创建一个[免费帐户][lnk-free-trial]。）

按照[设备管理入门](iot-hub-csharp-csharp-device-management-get-started.md)一文创建 IoT 中心，并获取 IoT 中心连接字符串。

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

## <a name="trigger-a-remote-firmware-update-on-the-device-using-a-direct-method"></a>使用直接方法在设备上触发远程固件更新
在本部分中，会创建一个 .NET 控制台应用（使用 C#）以启动设备上的远程固件更新。 该应用使用直接方法来启动更新，并使用设备孪生查询定期获取活动固件更新的状态。

1. 在 Visual Studio 中，使用“ **控制台应用程序** ”项目模板将 Visual C# Windows 经典桌面项目添加到当前解决方案。 将项目命名为 **TriggerFWUpdate**。

    ![新的 Visual C# Windows 经典桌面项目][img-createserviceapp]

2. 在“解决方案资源管理器”中，右键单击“TriggerFWUpdate”项目，并单击“管理 NuGet 包”。
3. 在“NuGet 包管理器”窗口中，选择“浏览”，搜索 **microsoft.azure.devices**，选择“安装”以安装 **Microsoft.Azure.Devices** 包，并接受使用条款。 该过程将下载、安装 [Azure IoT 服务 SDK][lnk-nuget-service-sdk] NuGet 包及其依赖项并添加对它的引用。

    ![“NuGet 包管理器”窗口][img-servicenuget]
4. 在 **Program.cs** 文件顶部添加以下 `using` 语句：

    ```csharp   
    using Microsoft.Azure.Devices;
    using Microsoft.Azure.Devices.Shared;
    ```

5. 将以下字段添加到 **Program** 类。 将多个占位符值替换为在上一部分中为中心创建的 IoT 中心连接字符串和设备的 ID。
   
    ```csharp   
    static RegistryManager registryManager;
    static string connString = "{iot hub connection string}";
    static ServiceClient client;
    static string targetDevice = "{deviceIdForTargetDevice}";
    ```
        
6. 将以下方法添加到 **Program** 类。 此方法每隔 500 毫秒在设备孪生中轮询更新的状态。 仅当状态实际发生更改时，它才向控制器写入数据。 在本示例中，为了防止在订阅中使用额外的 IoT 中心消息，当设备报告 **applyComplete** 状态或错误时，轮询将会停止。  
   
    ```csharp   
    public static async Task QueryTwinFWUpdateReported(DateTime startTime)
    {
        DateTime lastUpdated = startTime;

        while (true)
        {
            Twin twin = await registryManager.GetTwinAsync(targetDevice);

            if (twin.Properties.Reported.GetLastUpdated().ToUniversalTime() > lastUpdated.ToUniversalTime())
            {
                lastUpdated = twin.Properties.Reported.GetLastUpdated().ToUniversalTime();
                Console.WriteLine("\n" + twin.Properties.Reported["iothubDM"].ToJson());

                var status = twin.Properties.Reported["iothubDM"]["firmwareUpdate"]["status"].Value;
                if ((status == "downloadFailed") || (status == "applyFailed") || (status == "applyComplete"))
                {
                    Console.WriteLine("\nStop polling.");
                    return;
                }
            }
            await Task.Delay(500);
        }
    }
    ```
        
7. 将以下方法添加到 **Program** 类：

    ```csharp   
    public static async Task StartFirmwareUpdate()
    {
        client = ServiceClient.CreateFromConnectionString(connString);
        CloudToDeviceMethod method = new CloudToDeviceMethod("firmwareUpdate");
        method.ResponseTimeout = TimeSpan.FromSeconds(30);
        method.SetPayloadJson(
            @"{
               fwPackageUri : 'https://someurl'
            }");

        CloudToDeviceMethodResult result = await client.InvokeDeviceMethodAsync(targetDevice, method);

        Console.WriteLine("Invoked firmware update on device.");
    }
    ```

8. 最后，将下面的行添加到 **Main** 方法。 这会创建一个注册表管理器用于读取设备孪生，在工作线程上启动轮询任务，然后触发固件更新。
   
    ```csharp   
    registryManager = RegistryManager.CreateFromConnectionString(connString);

    Task queryTask = Task.Run(() => (QueryTwinFWUpdateReported(DateTime.Now)));

    StartFirmwareUpdate().Wait();
    Console.WriteLine("Press ENTER to exit.");
    Console.ReadLine();
    ```
        
9. 生成解决方案。

## <a name="create-a-simulated-device-app"></a>创建模拟设备应用程序
本部分的操作：

* 创建一个 .NET 控制台应用，用于响应通过云调用的直接方法
* 模拟后端服务通过直接方法触发的固件更新
* 使用报告属性使设备孪生查询能够识别设备及其上次完成固件更新的时间

1. 在 Visual Studio 中，使用“ **控制台应用程序** ”项目模板将 Visual C# Windows 经典桌面项目添加到当前解决方案。 将项目命名为 **SimulateDeviceFWUpdate**。
   
    ![新建 Visual C# Windows 经典设备应用][img-createdeviceapp]
    
2. 在“解决方案资源管理器”中，右键单击“SimulateDeviceFWUpdate”项目，并单击“管理 NuGet 包”。
3. 在“NuGet 包管理器”窗口中，选择“浏览”，然后搜索“microsoft.azure.devices.client”。 选择“安装”以安装“Microsoft.Azure.Devices.Client”包，并接受使用条款。 该过程将下载、安装 [Azure IoT 设备 SDK][lnk-nuget-client-sdk] NuGet 包及其依赖项并添加对它的引用。
   
    ![“NuGet 包管理器”窗口客户端应用][img-clientnuget]
4. 在 **Program.cs** 文件顶部添加以下 `using` 语句：
   
    ```csharp   
    using Newtonsoft.Json.Linq;
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    ```

5. 将以下字段添加到 **Program** 类。 将占位符值替换为在**创建设备标识**部分中记下的设备连接字符串。
   
    ```csharp   
    static string DeviceConnectionString = "HostName=<yourIotHubName>.azure-devices.net;DeviceId=<yourIotDeviceName>;SharedAccessKey=<yourIotDeviceAccessKey>";
    static DeviceClient Client = null;
    ```

6. 添加以下方法，以通过设备孪生向云报告状态： 

    ```csharp   
    static async Task reportFwUpdateThroughTwin(Twin twin, TwinCollection fwUpdateValue)
    {
        try
        {
            TwinCollection patch = new TwinCollection();
            TwinCollection iothubDM = new TwinCollection();

            iothubDM["firmwareUpdate"] = fwUpdateValue;
            patch["iothubDM"] = iothubDM;

            await Client.UpdateReportedPropertiesAsync(patch);
            Console.WriteLine("Twin state reported: {0}", fwUpdateValue["status"]);
        }
        catch 
        {
            Console.WriteLine("Error updating device twin");
            throw;
        }
    }
    ```

7. 添加以下方法，以模拟固件映像下载：
        
    ```csharp   
    static async Task<byte[]> simulateDownloadImage(string imageUrl)
    {
        var image = "[fake image data]";

        Console.WriteLine("Downloading image from " + imageUrl);

        await Task.Delay(4000);

        return Encoding.ASCII.GetBytes(image);
            
    }
    ```

8. 添加以下方法，以模拟向设备应用固件映像：
        
    ```csharp   
    static async Task simulateApplyImage(byte[] imageData)
    {
        if (imageData == null)
        {
            throw new ArgumentNullException();
        }

        await Task.Delay(4000);

    }
    ```
 
9.  添加以下方法，以等待下载模拟固件映像。 将状态更新为 **waiting**，并清除孪生上的其他固件更新属性。 清除这些属性会删除先前固件更新中的所有现有值。 之所以需要执行此操作，是因为报告属性是作为 PATCH 操作（增量）而不是 PUT 操作（替换所有先前值的整组属性）发送的。 通常，设备会收到有关可用更新的通知，并且管理员定义的策略会使设备开始下载和应用更新。 此函数是用于启用该策略的逻辑运行的位置。 
        
    ```csharp   
    static async Task waitToDownload(Twin twin, string fwUpdateUri)
    {
        var now = DateTime.Now;
        TwinCollection status = new TwinCollection();
        status["fwPackageUri"] = fwUpdateUri;
        status["status"] = "waiting";
        status["error"] = null;
        status["startedWaitingTime"] = DateTime.Now;
        status["downloadCompleteTime"] = null;
        status["startedApplyingImage"] = null;
        status["lastFirmwareUpdate"] = null;

        await reportFwUpdateThroughTwin(twin, status);

        await Task.Delay(2000);
    }
    ```

10. 添加以下方法以执行下载。 此方法通过设备孪生将状态更新为 **downloading**，调用 simulate download 方法，并根据 download 操作的结果，通过孪生报告 **downloadComplete** 或 **downloadFailed** 状态。 
        
    ```csharp   
    static async Task<byte[]> downloadImage(Twin twin, string fwUpdateUri)
    {
        try
        {
            TwinCollection statusUpdate = new TwinCollection();
            statusUpdate["status"] = "downloading";
            await reportFwUpdateThroughTwin(twin, statusUpdate);

            byte[] imageData = await simulateDownloadImage(fwUpdateUri);

            statusUpdate = new TwinCollection();
            statusUpdate["status"] = "downloadComplete";
            statusUpdate["downloadCompleteTime"] = DateTime.Now;
            await reportFwUpdateThroughTwin(twin, statusUpdate);
            return imageData;
        }
        catch (Exception ex)
        {
            TwinCollection statusUpdate = new TwinCollection();
            statusUpdate["status"] = "downloadFailed";
            statusUpdate["error"] = new TwinCollection();
            statusUpdate["error"]["code"] = ex.GetType().ToString();
            statusUpdate["error"]["message"] = ex.Message;
            await reportFwUpdateThroughTwin(twin, statusUpdate);
            throw;
        }
    }
    ```

11. 添加以下方法以应用映像。 此方法通过设备孪生将状态更新为 **applying**，调用 simulate apply image 方法，并根据 apply 操作的结果，通过孪生更新 **applyComplete** 或 **applyFailed** 状态。 
        
    ```csharp   
    static async Task applyImage(Twin twin, byte[] imageData)
    {
        try
        {
            TwinCollection statusUpdate = new TwinCollection();
            statusUpdate["status"] = "applying";
            statusUpdate["startedApplyingImage"] = DateTime.Now;
            await reportFwUpdateThroughTwin(twin, statusUpdate);

            await simulateApplyImage(imageData);

            statusUpdate = new TwinCollection();
            statusUpdate["status"] = "applyComplete";
            statusUpdate["lastFirmwareUpdate"] = DateTime.Now;
            await reportFwUpdateThroughTwin(twin, statusUpdate);
        }
        catch (Exception ex)
        {
            TwinCollection statusUpdate = new TwinCollection();
            statusUpdate["status"] = "applyFailed";
            statusUpdate["error"] = new TwinCollection();
            statusUpdate["error"]["code"] = ex.GetType().ToString();
            statusUpdate["error"]["message"] = ex.Message;
            await reportFwUpdateThroughTwin(twin, statusUpdate);
            throw;
        }
    }
    ```

12. 添加以下方法，将处于“正在等待下载映像”到“正在将映像应用到设备”状态的固件更新操作定序：
        
    ```csharp   
    static async Task doUpdate(string fwUpdateUrl)
    {
        try
        {
            Twin twin = await Client.GetTwinAsync();
            await waitToDownload(twin, fwUpdateUrl);
            byte[] imageData = await downloadImage(twin, fwUpdateUrl);
            await applyImage(twin, imageData);
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error during update: {0}", ex.Message);
        }
    }
    ```

13. 添加以下方法，以从云中处理 **updateFirmware** 直接方法。 此方法会将 URL 从消息有效负载提取到固件更新，并将其传递给在另一个 threadpool 线程上启动的 **doUpdate** 任务。 然后，此方法立即向云返回方法响应。
        
    ```csharp   
    static Task<MethodResponse> onFirmwareUpdate(MethodRequest methodRequest, object userContext)
    {
        string fwUpdateUrl = (string)JObject.Parse(methodRequest.DataAsJson)["fwPackageUri"];
        Console.WriteLine("\nMethod: {0} triggered by service, URI is: {1}", methodRequest.Name, fwUpdateUrl);

        Task updateTask = Task.Run(() => (doUpdate(fwUpdateUrl)));

        string result = "'FirmwareUpdate started.'";
        return Task.FromResult(new MethodResponse(Encoding.UTF8.GetBytes(result), 200));
    }
    ```
> [!NOTE]
> 此方法触发以**任务**形式运行的模拟更新，然后立即响应方法调用，告知服务已启动固件更新。 更新状态和完成进度将通过设备孪生的报告属性发送到服务。 我们会在启动更新时而不是在完成更新后响应此方法调用，因为：
> * 实际的更新过程所花费的时间很可能比方法调用超时的时间要长。
> * 实际的更新过程很可能要求重新启动，这会重新启动此应用，使 **MetodRequest** 对象不可用。 （但是，即使在重新启动之后，也能更新报告属性。） 

14. 最后，将以下代码添加到 **Main** 方法，打开与 IoT 中心的连接并初始化方法侦听器：
   
    ```csharp   
    try
    {
        Console.WriteLine("Connecting to hub");
        Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, TransportType.Mqtt);
        
        // setup callback for "firmware update" method
        Client.SetMethodHandlerAsync("firmwareUpdate", onFirmwareUpdate, null).Wait();
        Console.WriteLine("Waiting for firmware update direct method call\n Press enter to exit.");
        Console.ReadLine();

        Console.WriteLine("Exiting...");

        // as a good practice, remove the firmware update handler
        Client.SetMethodHandlerAsync("firmwareUpdate", null, null).Wait();
        Client.CloseAsync().Wait();
    }
    catch (Exception ex)
    {
        Console.WriteLine();
        Console.WriteLine("Error in sample: {0}", ex.Message);
    }
    ```
        
15. 生成解决方案。       

> [!NOTE]
> 为简单起见，本教程不实现任何重试策略。 在生产代码中，应该按 MSDN 文章 [Transient Fault Handling][lnk-transient-faults]（暂时性故障处理）中所述实施重试策略（例如指数性的回退）。


## <a name="run-the-apps"></a>运行应用
现在可以运行应用了。
1. 运行 .NET 设备应用 **SimulatedDeviceFWUpdate**。  右键单击“SimulatedDeviceFWUpdate”项目，选择“调试”，并选择“启动新实例”。 它应开始侦听来自 IoT 中心的方法调用： 

2. 连接设备后等待方法调用时，可以运行 .NET **TriggerFWUpdate** 应用来调用模拟设备应用中的固件更新方法。 右键单击“TriggerFWUpdate”项目，选择“调试”，并选择“启动新实例”。 此时，应会看到更新序列已写入 **SimulatedDeviceFWUpdate** 控制台，另外，通过设备报告属性报告的序列已写入 **TriggerFWUpdate** 控制台。 请注意，此过程需要花费几秒钟时间才能完成。 
   
    ![运行的服务和设备应用][img-combinedrun]


## <a name="next-steps"></a>后续步骤
在本教程中，已使用直接方法在设备上触发远程固件更新，并使用报告属性跟踪固件更新的进度。

若要了解如何扩展 IoT 解决方案以及在多个设备上计划方法调用，请参阅[计划和广播作业][lnk-tutorial-jobs]教程。

<!-- images -->
[img-servicenuget]: media/iot-hub-csharp-csharp-firmware-update/servicesdknuget.png
[img-clientnuget]: media/iot-hub-csharp-csharp-firmware-update/clientsdknuget.png
[img-createserviceapp]: media/iot-hub-csharp-csharp-firmware-update/createserviceapp.png
[img-createdeviceapp]: media/iot-hub-csharp-csharp-firmware-update/createdeviceapp.png
[img-combinedrun]: media/iot-hub-csharp-csharp-firmware-update/combinedrun.png

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-dm-getstarted]: iot-hub-csharp-csharp-device-management-get-started.md
[lnk-tutorial-jobs]: iot-hub-csharp-node-schedule-jobs.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-rpi-implementation]: https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iothub_client_sample_mqtt_dm/pi_device
[lnk-nuget-client-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/