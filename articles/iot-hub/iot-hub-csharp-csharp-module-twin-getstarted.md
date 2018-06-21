---
title: Azure IoT 中心模块标识和模块孪生 (.NET) 入门 | Microsoft Docs
description: 了解如何使用用于 .NET 的 IoT SDK 创建模块标识和更新模块孪生。
author: chrissie926
manager: ''
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 04/26/2018
ms.author: menchi
ms.openlocfilehash: 5855396fc87b7d8de17be65a66af40963c59fc71
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34633472"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-using-net-back-end-and-net-device"></a>使用 .NET 后端和 .NET 设备创建 IoT 中心模块标识和模块孪生入门

> [!NOTE]
> [模块标识和模块孪生](iot-hub-devguide-module-twins.md)类似于 Azure IoT 中心设备标识和设备孪生，但提供更精细的粒度。 Azure IoT 中心设备标识和设备孪生允许后端应用程序配置设备并提供设备条件的可见性，而模块标识和模块孪生为设备的各个组件提供这些功能。 在支持多个组件的设备上（例如基于操作系统的设备或固件设备），它允许每个部件拥有独立的配置和条件。

在本教程结束时，会获得两个 .NET 控制台应用：

* **CreateIdentities**，用于创建设备标识、模块标识和相关的安全密钥，以连接设备和模块客户端。
* **UpdateModuleTwinReportedProperties**，用于将更新的模块孪生报告属性发送到 IoT 中心。

> [!NOTE]
> 有关 Azure IoT SDK 的信息（可以使用这些 SDK 构建可在设备和解决方案后端上运行的应用程序），请参阅 [Azure IoT SDK][lnk-hub-sdks]。

要完成本教程，需要以下各项：

* Visual Studio 2015 或 Visual Studio 2017。
* 有效的 Azure 帐户。 （如果没有帐户，只需花费几分钟就能创建一个[免费帐户][lnk-free-trial]。）

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

现已创建 IoT 中心，因此已获得完成本教程的其余部分所需的主机名和 IoT 中心连接字符串。

<a id="DeviceIdentity_csharp"></a>
[!INCLUDE [iot-hub-get-started-create-module-identity-csharp](../../includes/iot-hub-get-started-create-module-identity-csharp.md)]


<a id="D2C_csharp"></a>
## <a name="update-the-module-twin-using-net-device-sdk"></a>使用 .NET 设备 SDK 更新模块孪生

在本节中，将在更新模块孪生报告属性的模拟设备上创建 .NET 控制台应用。

1. **创建 Visual Studio 项目** - 在 Visual Studio 中，使用“控制台应用 (.NET Framework)”项目模板将 Visual C# Windows 经典桌面项目添加到现有解决方案。 确保 .NET Framework 为 4.6.1 或更高版本。 将项目命名为“UpdateModuleTwinReportedProperties”。

    ![创建 Visual Studio 项目][13]

2. **安装最新的 Azure IoT 中心 .NET 设备 SDK** - 模块标识和模块孪生为公开预览版。 仅在 IoT 中心预发行设备 SDK 中提供它。 在 Visual Studio 中，打开“工具”>“NuGet 包管理器”>“管理解决方案的 NuGet 包”。 搜索 Microsoft.Azure.Devices.Client。 确保已选中“包括预发行版”复选框。 选择最新版本并安装。 现在即可访问所有模块功能。 

    ![安装 Azure IoT 中心 .NET 服务 SDK V1.16.0-preview-005][14]

3. **获取模块连接字符串** -- 现在，如果登录到 [Azure 门户][lnk-portal]。 导航到 IoT 中心并单击 IoT 设备。 查找并打开 myFirstDevice，可以看到 myFirstModule 已成功创建。 复制模块连接字符串。 下一步将需要它。

    ![Azure 门户模块详细信息][15]

4. **创建 UpdateModuleTwinReportedProperties 控制台应用** 在“Program.cs”文件顶部添加以下 `using` 语句：

    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using System.Threading.Tasks;
    using Newtonsoft.Json;
    ```

    将以下字段添加到 **Program** 类。 将占位符值替换为模块连接字符串。

    ```csharp
    private const string ModuleConnectionString = "<Your module connection string>";
    private static ModuleClient Client = null;
    static void ConnectionStatusChangeHandler(ConnectionStatus status, ConnectionStatusChangeReason reason)
    {
        Console.WriteLine("Connection Status Changed to {0}; the reason is {1}", status, reason);
    }
    ```

    将以下方法“OnDesiredPropertyChanged”添加到“Program”类：

    ```csharp
    private static async Task OnDesiredPropertyChanged(TwinCollection desiredProperties, object userContext)
        {
            Console.WriteLine("desired property change:");
            Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));
            Console.WriteLine("Sending current time as reported property");
            TwinCollection reportedProperties = new TwinCollection
            {
                ["DateTimeLastDesiredPropertyChangeReceived"] = DateTime.Now
            };

            await Client.UpdateReportedPropertiesAsync(reportedProperties).ConfigureAwait(false);
        }
    ```

    最后，在 **Main** 方法中添加以下行：

    ```csharp
    static void Main(string[] args)
    {
        Microsoft.Azure.Devices.Client.TransportType transport = Microsoft.Azure.Devices.Client.TransportType.Amqp;

        try
        {
            Client = ModuleClient.CreateFromConnectionString(ModuleConnectionString, transport);
            Client.SetConnectionStatusChangesHandler(ConnectionStatusChangeHandler);
            Client.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertyChanged, null).Wait();

            Console.WriteLine("Retrieving twin");
            var twinTask = Client.GetTwinAsync();
            twinTask.Wait();
            var twin = twinTask.Result;
            Console.WriteLine(JsonConvert.SerializeObject(twin.Properties)); 

            Console.WriteLine("Sending app start time as reported property");
            TwinCollection reportedProperties = new TwinCollection();
            reportedProperties["DateTimeLastAppLaunch"] = DateTime.Now;

            Client.UpdateReportedPropertiesAsync(reportedProperties);
        }
        catch (AggregateException ex)
        {
            Console.WriteLine("Error in sample: {0}", ex);
        }

        Console.WriteLine("Waiting for Events.  Press enter to exit...");
        Console.ReadLine();
        Client.CloseAsync().Wait();
    }
    ```

    此代码示例演示如何检索模块孪生和借助 AMQP 协议更新报告属性。 在公开预览版中，我们仅支持通过 AMQP 进行模块孪生操作。

5. 除上述“Main”方法外，还可添加以下代码块将事件从模块发送到 IoT 中心：
    ```csharp
    Byte[] bytes = new Byte[2];
    bytes[0] = 0;
    bytes[1] = 1;
    var sendEventsTask = Client.SendEventAsync(new Message(bytes));
    sendEventsTask.Wait();
    Console.WriteLine("Event sent to IoT Hub.");
    ```

## <a name="run-the-apps"></a>运行应用

现在可以运行应用了。 在 Visual Studio 的“解决方案资源管理器”中右键单击解决方案，并单击“设置启动项目”。 选择“多个启动项目”，然后选择“启动”作为控制台应用的操作。 然后按 F5 启动应用。 

## <a name="next-steps"></a>后续步骤

若要继续了解 IoT 中心入门知识并浏览其他 IoT 方案，请参阅：

* [设备管理入门][lnk-device-management]
* [IoT 边缘入门][lnk-iot-edge]


<!-- Images. -->
[13]: ./media\iot-hub-csharp-csharp-module-twin-getstarted/update-twins-csharp1.JPG
[14]: ./media\iot-hub-csharp-csharp-module-twin-getstarted/install-sdk.png
[15]: ./media\iot-hub-csharp-csharp-module-twin-getstarted/module-detail.JPG
<!-- Links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-iot-edge]: ../iot-edge/tutorial-simulate-device-linux.md
