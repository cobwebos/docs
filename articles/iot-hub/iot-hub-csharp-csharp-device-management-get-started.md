---
title: Azure IoT 中心设备管理入门 (.NET/.NET) | Microsoft Docs
description: 如何使用 Azure IoT 中心设备管理启动远程设备重启。 使用适用于 .NET 的 Azure IoT 设备 SDK 实现包含直接方法的模拟设备应用，并使用适用于 .NET 的 Azure IoT 服务 SDK 实现调用直接方法的服务应用。
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: robinsh
ms.openlocfilehash: 79e65671613364f5cc05153d90cfdcd5959a279f
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2020
ms.locfileid: "76939324"
---
# <a name="get-started-with-device-management-net"></a>设备管理入门（.NET）

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

本教程演示如何：

* 使用 Azure 门户创建 IoT 中心，以及如何在 IoT 中心创建设备标识。

* 创建包含重新启动该设备的直接方法的模拟设备应用。 直接方法是从云中调用的。

* 创建一个 .NET 控制台应用，其通过 IoT 中心在模拟设备应用上调用重新启动直接方法。

在本教程结束时，会获得两个 .NET 控制台应用：

* **SimulateManagedDevice**。 此应用使用之前创建的设备标识连接到 IoT 中心，接收重新启动直接方法，模拟物理重新启动，并报告上次重新启动的时间。

* **TriggerReboot**。 此应用调用模拟设备应用中的直接方法，显示响应，并显示更新后的报告属性。

## <a name="prerequisites"></a>必备组件

* Visual Studio。

* 有效的 Azure 帐户。 如果没有帐户，只需花费几分钟就能创建一个[免费帐户](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="create-an-iot-hub"></a>创建 IoT 中心

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>在 IoT 中心内注册新设备

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="get-the-iot-hub-connection-string"></a>获取 IoT 中心连接字符串

[!INCLUDE [iot-hub-howto-device-management-shared-access-policy-text](../../includes/iot-hub-howto-device-management-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>使用直接方法在设备上触发远程重新启动

在本部分中，将创建一个 .NET 控制台应用， C#该应用使用直接方法在设备上启动远程重新启动。 该应用使用设备孪生查询来搜索该设备的上次重新启动时间。

1. 在 Visual Studio 中选择“创建新项目”。

1. 在 "**创建新项目**" 中，找到并选择 "**控制台应用（.NET Framework）** " 项目模板，然后选择 "**下一步**"。

1. 在 "**配置新项目**" 中，将项目命名为 " *TriggerReboot*"，并选择 .NET Framework 4.5.1 或更高版本。 选择“创建”。

    ![新的 Visual C# Windows 经典桌面项目](./media/iot-hub-csharp-csharp-device-management-get-started/create-trigger-reboot-configure.png)

1. 在**解决方案资源管理器**中，右键单击**TriggerReboot**项目，然后选择 "**管理 NuGet 包**"。

1. 选择 "**浏览**"，搜索并选择 " **Microsoft Azure. 设备**"。 选择 "**安装**" 以安装**Microsoft Azure. 设备**包。

    ![“NuGet 包管理器”窗口](./media/iot-hub-csharp-csharp-device-management-get-started/create-trigger-reboot-nuget-devices.png)

   此步骤将下载、安装 [Azure IoT 服务 SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices/) NuGet 包及其依赖项并添加对其的引用。

1. 在 Program.cs 文件顶部添加以下 `using` 语句：

   ```csharp
   using Microsoft.Azure.Devices;
   using Microsoft.Azure.Devices.Shared;
   ```

1. 将以下字段添加到 Program 类。 将 `{iot hub connection string}` 占位符值替换为之前在[获取 iot 中心连接字符串](#get-the-iot-hub-connection-string)中复制的 iot 中心连接字符串。

   ```csharp
   static RegistryManager registryManager;
   static string connString = "{iot hub connection string}";
   static ServiceClient client;
   static string targetDevice = "myDeviceId";
   ```

1. 将以下方法添加到 **Program** 类。  此代码为重新启动的设备获取设备克隆，并输出报告的属性。

   ```csharp
   public static async Task QueryTwinRebootReported()
   {
       Twin twin = await registryManager.GetTwinAsync(targetDevice);
       Console.WriteLine(twin.Properties.Reported.ToJson());
   }
   ```

1. 将以下方法添加到 **Program** 类。  此代码使用直接方法在设备上启动重启。

   ```csharp
   public static async Task StartReboot()
   {
       client = ServiceClient.CreateFromConnectionString(connString);
       CloudToDeviceMethod method = new CloudToDeviceMethod("reboot");
       method.ResponseTimeout = TimeSpan.FromSeconds(30);

       CloudToDeviceMethodResult result = await 
         client.InvokeDeviceMethodAsync(targetDevice, method);

       Console.WriteLine("Invoked firmware update on device.");
   }
   ```

1. 最后，在 **Main** 方法中添加以下行：

   ```csharp
   registryManager = RegistryManager.CreateFromConnectionString(connString);
   StartReboot().Wait();
   QueryTwinRebootReported().Wait();
   Console.WriteLine("Press ENTER to exit.");
   Console.ReadLine();
   ```

1. 选择“生成” > “生成解决方案”。

> [!NOTE]
> 本教程仅针对设备的报告属性执行单个查询。 在生产代码中，我们建议通过轮询来检测报告属性是否更改。

## <a name="create-a-simulated-device-app"></a>创建模拟设备应用程序

本部分的操作：

* 创建一个 .NET 控制台应用，用于响应通过云调用的直接方法。

* 触发模拟设备重新启动。

* 通过报告的属性，设备孪生查询可标识设备及设备上次重新启动的时间。

若要创建模拟设备应用，请执行以下步骤：

1. 在 Visual Studio 中，在已创建的 TriggerReboot 解决方案中，选择 "**文件** > **新建** > **项目**"。 在 "**创建新项目**" 中，找到并选择 "**控制台应用（.NET Framework）** " 项目模板，然后选择 "**下一步**"。

1. 在 "**配置新项目**" 中，将项目命名为 " *SimulateManagedDevice*"，对于 "**解决方案**"，选择 "**添加到解决方案**"。 选择“创建”。

    ![命名，并将项目添加到解决方案](./media/iot-hub-csharp-csharp-device-management-get-started/configure-device-app.png)

1. 在解决方案资源管理器中，右键单击新的 " **SimulateManagedDevice** " 项目，然后选择 "**管理 NuGet 包**"。

1. 选择 "**浏览**"，搜索并选择 " **Microsoft Azure**"。 选择“安装”。

    ![“NuGet 包管理器”窗口客户端应用](./media/iot-hub-csharp-csharp-device-management-get-started/create-device-nuget-devices-client.png)

   此步骤将下载、安装[Azure IoT 设备 SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/) NuGet 包及其依赖项并添加对它的引用。

1. 在 Program.cs 文件顶部添加以下 `using` 语句：

    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    ```

1. 将以下字段添加到 Program 类。 将 `{device connection string}` 占位符值替换为之前在[IoT 中心注册新设备](#register-a-new-device-in-the-iot-hub)中记下的设备连接字符串。

    ```csharp
    static string DeviceConnectionString = "{device connection string}";
    static DeviceClient Client = null;
    ```

1. 添加以下函数，实现设备上的直接方法：

   ```csharp
   static Task<MethodResponse> onReboot(MethodRequest methodRequest, object userContext)
   {
       // In a production device, you would trigger a reboot 
       //   scheduled to start after this method returns.
       // For this sample, we simulate the reboot by writing to the console
       //   and updating the reported properties.
       try
       {
           Console.WriteLine("Rebooting!");

           // Update device twin with reboot time. 
           TwinCollection reportedProperties, reboot, lastReboot;
           lastReboot = new TwinCollection();
           reboot = new TwinCollection();
           reportedProperties = new TwinCollection();
           lastReboot["lastReboot"] = DateTime.Now;
           reboot["reboot"] = lastReboot;
           reportedProperties["iothubDM"] = reboot;
           Client.UpdateReportedPropertiesAsync(reportedProperties).Wait();
       }
       catch (Exception ex)
       {
           Console.WriteLine();
           Console.WriteLine("Error in sample: {0}", ex.Message);
       }

       string result = @"{""result"":""Reboot started.""}";
       return Task.FromResult(new MethodResponse(Encoding.UTF8.GetBytes(result), 200));
   }
   ```

1. 最后，将以下代码添加到 **Main** 方法，打开与 IoT 中心的连接并初始化方法侦听器：

   ```csharp
   try
   {
       Console.WriteLine("Connecting to hub");
       Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, 
         TransportType.Mqtt);

       // setup callback for "reboot" method
       Client.SetMethodHandlerAsync("reboot", onReboot, null).Wait();
       Console.WriteLine("Waiting for reboot method\n Press enter to exit.");
       Console.ReadLine();

       Console.WriteLine("Exiting...");

       // as a good practice, remove the "reboot" handler
       Client.SetMethodHandlerAsync("reboot", null, null).Wait();
       Client.CloseAsync().Wait();
   }
   catch (Exception ex)
   {
       Console.WriteLine();
       Console.WriteLine("Error in sample: {0}", ex.Message);
   }
   ```

1. 在解决方案资源管理器中，右键单击解决方案，然后选择 "**设置启动项目**"。

1. 对于 "**常见属性** > **启动项目**"，选择 "**单个启动项目**"，然后选择 " **SimulateManagedDevice** " 项目。 选择“确定”保存更改。

1. 选择“生成” > “生成解决方案”。

> [!NOTE]
> 为简单起见，本教程不实现任何重试策略。 在生产代码中，应按照[暂时性故障处理](/azure/architecture/best-practices/transient-faults)中的建议，实现重试策略（例如指数回退）。

## <a name="run-the-apps"></a>运行应用

你现在已准备好运行应用。

1. 若要运行 .NET 设备应用**SimulateManagedDevice**，请在解决方案资源管理器中右键单击 " **SimulateManagedDevice** " 项目，选择 "**调试**"，然后选择 "**启动新实例**"。 应用应开始侦听来自 IoT 中心的方法调用。

1. 在该设备连接并等待方法调用后，右键单击**TriggerReboot**项目，选择 "**调试**"，然后选择 "**启动新实例**"。

   应会看到“正在重新启动！” 在 **SimulatedManagedDevice** 控制台中写入，设备的报告属性（包括上次重新启动时间）在 **TriggerReboot** 控制台中写入。

    ![运行的服务和设备应用](./media/iot-hub-csharp-csharp-device-management-get-started/combinedrun.png)

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]
