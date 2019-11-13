---
title: 开始用 Azure IoT 中心模块标识 & 模块（.NET）
description: 了解如何使用用于 .NET 的 IoT SDK 创建模块标识和更新模块孪生。
author: chrissie926
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: menchi
ms.openlocfilehash: e728d0ef8f52927687d56bd1d4c64f03c53ef401
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2019
ms.locfileid: "73947681"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-net"></a>IoT 中心模块标识和模块孪生 (.NET) 入门

[!INCLUDE [iot-hub-selector-module-twin-getstarted](../../includes/iot-hub-selector-module-twin-getstarted.md)]

> [!NOTE]
> [模块标识和模块孪生](iot-hub-devguide-module-twins.md)类似于 Azure IoT 中心设备标识和设备孪生，但提供更精细的粒度。 Azure IoT 中心设备标识和设备孪生允许后端应用程序配置设备并提供设备条件的可见性，而模块标识和模块孪生为设备的各个组件提供这些功能。 在支持多个组件的设备上（例如基于操作系统的设备或固件设备），模块标识和模块孪生允许每个部件拥有独立的配置和条件。

在本教程结束时，会得到两个 .NET 控制台应用：

* **CreateIdentities**。 此应用创建设备标识、模块标识和相关的安全密钥，以连接设备和模块客户端。

* **UpdateModuleTwinReportedProperties**。 此应用将更新的模块孪生报告属性发送到 IoT 中心。

> [!NOTE]
> 有关 Azure IoT SDK 的信息（可以使用这些 SDK 构建可在设备和解决方案后端上运行的应用程序），请参阅 [Azure IoT SDK](iot-hub-devguide-sdks.md)。

## <a name="prerequisites"></a>先决条件

* Visual Studio。

* 有效的 Azure 帐户。 如果没有帐户，只需花费几分钟就能创建一个 [免费帐户](https://azure.microsoft.com/pricing/free-trial/) 。

## <a name="create-a-hub"></a>创建中心

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="get-the-iot-hub-connection-string"></a>获取 IoT 中心连接字符串

[!INCLUDE [iot-hub-howto-module-twin-shared-access-policy-text](../../includes/iot-hub-howto-module-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-registryrw-connection-string](../../includes/iot-hub-include-find-registryrw-connection-string.md)]

[!INCLUDE [iot-hub-get-started-create-module-identity-csharp](../../includes/iot-hub-get-started-create-module-identity-csharp.md)]

## <a name="update-the-module-twin-using-net-device-sdk"></a>使用 .NET 设备 SDK 更新模块孪生

在本节中，将在更新模块孪生报告属性的模拟设备上创建 .NET 控制台应用。

在开始之前，请获取模块连接字符串。 登录到 [Azure 门户](https://portal.azure.com/)。 导航到中心并选择“IoT 设备”。 找到 **myFirstDevice**。 选择 **myFirstDevice** 将其打开，然后选择 **myFirstModule** 将其打开。 执行以下过程时，在“模块标识详细信息”中根据需要复制“连接字符串(主密钥)”。

   ![Azure 门户模块详细信息](./media/iot-hub-csharp-csharp-module-twin-getstarted/module-identity-detail.png)

1. 在 Visual Studio 中将新项目添加到解决方案，只需选择“文件” **“新建”** “项目”即可。 >  >  在“创建新项目”中，选择“控制台应用(.NET Framework)”，然后选择“下一步”。

1. 将项目命名为“UpdateModuleTwinReportedProperties”。 对于“解决方案”，请选择“添加到解决方案”。 确保 .NET Framework 为 4.6.1 或更高版本。

    ![创建 Visual Studio 项目](./media/iot-hub-csharp-csharp-module-twin-getstarted/configure-update-twins-csharp1.png)

1. 选择“创建”来创建项目。

1. 在 Visual Studio 中，打开“工具” **“NuGet 包管理器”** “管理解决方案的 NuGet 包”。 >  >  选择“浏览”按钮。

1. 搜索并选择 **Microsoft.Azure.Devices.Client**，然后选择“安装”。

    ![安装 Azure IoT 中心 .NET 服务 SDK 当前版本](./media/iot-hub-csharp-csharp-module-twin-getstarted/install-client-sdk.png)

1. 在 `using`Program.cs**文件顶部添加以下** 语句：

    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using System.Threading.Tasks;
    using Newtonsoft.Json;
    ```

1. 将以下字段添加到 Program 类。 将占位符值替换为模块连接字符串。

    ```csharp
    private const string ModuleConnectionString = "<Your module connection string>";
    private static ModuleClient Client = null;
    static void ConnectionStatusChangeHandler(ConnectionStatus status, 
      ConnectionStatusChangeReason reason)
    {
        Console.WriteLine("Connection Status Changed to {0}; the reason is {1}", 
          status, reason);
    }
    ```

1. 将以下方法“OnDesiredPropertyChanged”添加到“Program”类：

    ```csharp
    private static async Task OnDesiredPropertyChanged(TwinCollection desiredProperties, 
      object userContext)
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

1. 在 **Main** 方法中添加以下行：

    ```csharp
    static void Main(string[] args)
    {
        Microsoft.Azure.Devices.Client.TransportType transport = 
          Microsoft.Azure.Devices.Client.TransportType.Amqp;

        try
        {
            Client = 
              ModuleClient.CreateFromConnectionString(ModuleConnectionString, transport);
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

1. 也可将这些语句添加到 **Main** 方法，以便将事件从模块发送到 IoT 中心。 将这些行置于 `try catch` 块下。

    ```csharp
    Byte[] bytes = new Byte[2];
    bytes[0] = 0;
    bytes[1] = 1;
    var sendEventsTask = Client.SendEventAsync(new Message(bytes));
    sendEventsTask.Wait();
    Console.WriteLine("Event sent to IoT Hub.");
    ```

## <a name="run-the-apps"></a>运行应用

现在可以运行这些应用了。

1. 在 Visual Studio 的“解决方案资源管理器”中，右键单击解决方案并选择“设置启动项目”。

1. 在“通用属性”下选择“启动项目”。

1. 选择“多个启动项目”，接着选择“启动”作为应用的操作，然后选择“确定”接受所做的更改。

1. 按 **F5** 启动应用。

## <a name="next-steps"></a>后续步骤

若要继续了解 IoT 中心入门知识并浏览其他 IoT 方案，请参阅：

* [设备管理入门](iot-hub-node-node-device-management-get-started.md)

* [IoT Edge 入门](../iot-edge/tutorial-simulate-device-linux.md)
