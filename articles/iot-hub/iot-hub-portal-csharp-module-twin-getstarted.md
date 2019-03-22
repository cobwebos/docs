---
title: Azure IoT 中心模块标识和模块孪生（门户和 .NET）入门 | Microsoft Docs
description: 了解如何使用门户和 .NET 创建模块标识和更新模块孪生。
author: robinsh
manager: philmea
ms.author: robin.shahan
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 04/26/2018
ms.openlocfilehash: f323eb777d98a812ead8dd8ac78ee3d6b36a748a
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2019
ms.locfileid: "57545814"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-using-the-portal-and-net-device"></a>使用门户和 .NET 设备创建 IoT 中心模块标识和模块孪生入门

> [!NOTE]
> [模块标识和模块孪生](iot-hub-devguide-module-twins.md)类似于 Azure IoT 中心设备标识和设备孪生，但提供更精细的粒度。 Azure IoT 中心设备标识和设备孪生允许后端应用程序配置设备并提供设备条件的可见性，而模块标识和模块孪生为设备的各个组件提供这些功能。 在支持多个组件的设备上（例如基于操作系统的设备或固件设备），它允许每个部件拥有独立的配置和条件。
>

本教程介绍：

1. 如何在门户中创建模块标识。 

2. 如何使用 .NET 设备 SDK 更新设备的模块孪生。

> [!NOTE]
> 有关 Azure IoT SDK 的信息（可以使用这些 SDK 构建可在设备和解决方案后端上运行的应用程序），请参阅 [Azure IoT SDK][lnk-hub-sdks]。
>

要完成本教程，需要以下各项：

* Visual Studio 2015 或 Visual Studio 2017。
* 有效的 Azure 帐户。 （如果没有帐户，只需花费几分钟就能创建一个[免费帐户][lnk-free-trial]。）

## <a name="create-an-iot-hub"></a>创建 IoT 中心

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="retrieve-connection-string-for-iot-hub"></a>检索 IoT 中心的连接字符串

[!INCLUDE [iot-hub-include-find-connection-string](../../includes/iot-hub-include-find-connection-string.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>在 IoT 中心内注册新设备

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="create-a-module-identity-in-the-portal"></a>在门户中创建模块标识

在一个设备标识中，最多可以创建 20 个模块标识。 单击顶部的“添加模块标识”按钮创建第一个模块标识，名为“myFirstModule”。 

  ![设备详细信息][9]

保存并单击刚才创建的模块标识。 可以看到模块标识详细信息。 保存连接字符串 - 主键。 它将在下一节在设备上设置模块时使用。

  ![设备详细信息][12]

## <a name="update-the-module-twin-using-net-device-sdk"></a>使用 .NET 设备 SDK 更新模块孪生

你已成功在 IoT 中心内创建模块标识。 现在尝试从模拟设备与云进行通信。 创建模块标识后，在 IoT 中心内隐式创建模块孪生。 在本节中，将在更新模块孪生报告属性的模拟设备上创建 .NET 控制台应用。

## <a name="create-a-visual-studio-project"></a>创建 Visual Studio 项目

在 Visual Studio 中，使用“控制台应用(.NET Framework)”项目模板将 Visual C# Windows 经典桌面项目添加到现有解决方案。 确保 .NET Framework 版本为 4.6.1 或更高。 将项目命名为“UpdateModuleTwinReportedProperties”。

  ![创建 Visual Studio 项目][13]

## <a name="install-the-latest-azure-iot-hub-net-device-sdk"></a>安装最新的 Azure IoT 中心 .NET 设备 SDK

模块标识和模块孪生为公共预览版。 仅在 IoT 中心预发行设备 SDK 中提供它。 在 Visual Studio 中，打开“工具”>“NuGet 包管理器”>“管理解决方案的 NuGet 包”。 搜索 Microsoft.Azure.Devices.Client。 确保已选中“包括预发行版”复选框。 选择最新版本并安装。 现在可以访问所有模块功能。 

  ![安装 Azure IoT 中心 .NET 服务 SDK V1.16.0-preview-005][14]

## <a name="get-your-module-connection-string"></a>获取模块连接字符串

登录到 [Azure 门户][lnk-portal]。 导航到 IoT 中心并单击 IoT 设备。 查找并打开 myFirstDevice，可以看到 myFirstModule 已成功创建。 复制模块连接字符串。 下一步将需要它。

  ![Azure 门户模块详细信息][15]

## <a name="create-updatemoduletwinreportedproperties-console-app"></a>创建 UpdateModuleTwinReportedProperties 控制台应用

在 Program.cs 文件顶部添加以下 `using` 语句：

```csharp
using Microsoft.Azure.Devices.Client;
using Microsoft.Azure.Devices.Shared;
```

将以下字段添加到 Program 类。 将占位符值替换为模块连接字符串。

```csharp
private const string ModuleConnectionString = "<Your module connection string>";
private static ModuleClient Client = null;
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
        Console.WriteLine(JsonConvert.SerializeObject(twin));

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
    Console.ReadKey();
    Client.CloseAsync().Wait();
}

private static void ConnectionStatusChangeHandler(ConnectionStatus status, ConnectionStatusChangeReason reason)
{
    Console.WriteLine($"Status {status} changed: {reason}");
}
```

此代码示例演示如何检索模块孪生和借助 AMQP 协议更新报告属性。 在公开预览版中，我们仅支持通过 AMQP 进行模块孪生操作。

## <a name="run-the-apps"></a>运行应用

现在可以运行应用了。 在 Visual Studio 的“解决方案资源管理器”中右键单击解决方案，并单击“设置启动项目”。 选择“多个启动项目”，然后选择“启动”作为控制台应用的操作。 然后按 F5 启动这两个应用，使其运行。 

## <a name="next-steps"></a>后续步骤

若要继续了解 IoT 中心入门知识并浏览其他 IoT 方案，请参阅：

* [通过 .NET 备份和 .NET 设备开始使用 IoT 中心模块标识和模块孪生][lnk-csharp-csharp-getstarted]
* [IoT 边缘入门][lnk-iot-edge]


<!-- Images. -->
[9]:./media/iot-hub-portal-csharp-module-twin-getstarted/create-module-id.JPG
[12]:./media/iot-hub-portal-csharp-module-twin-getstarted/module-details.JPG
[13]: ./media/iot-hub-csharp-csharp-module-twin-getstarted/update-twins-csharp1.JPG
[14]: ./media/iot-hub-csharp-csharp-module-twin-getstarted/install-sdk.png
[15]: ./media/iot-hub-csharp-csharp-module-twin-getstarted/module-detail.JPG
<!-- Links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-csharp-csharp-getstarted]: iot-hub-csharp-csharp-module-twin-getstarted.md
[lnk-iot-edge]: ../iot-edge/tutorial-simulate-device-linux.md
