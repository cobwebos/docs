---
title: "Azure IoT 中心入门 (.NET) | Microsoft 文档"
description: "如何使用用于 .NET 的 Azure IoT SDK，将设备到云的消息从设备发送到 Azure IoT 中心。 将创建一个用于发送消息的模拟设备、一个用于在注册表中标识注册设备的服务应用，以及一个用于从 IoT 中心读取设备到云消息的服务应用。"
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: f40604ff-8fd6-4969-9e99-8574fbcf036c
ms.service: iot-hub
ms.devlang: dotnet
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/08/2017
ms.author: dobett
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 31ecec607c78da2253fcf16b3638cc716ba3ab89
ms.openlocfilehash: 477f618c09c8cf572a16d142f63c9b3553050b20
ms.contentlocale: zh-cn
ms.lasthandoff: 06/23/2017


---
# 使用 .NET 将模拟设备连接到 IoT 中心
<a id="connect-your-simulated-device-to-your-iot-hub-using-net" class="xliff"></a>
[!INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

本教程结束时，将会创建三个 .NET 控制台应用：

* **CreateDeviceIdentity**，用于创建设备标识和关联的安全密钥以连接模拟设备应用。
* **ReadDeviceToCloudMessages**，显示模拟设备应用发送的遥测数据。
* **SimulatedDevice**，它使用前面创建的设备标识连接到 IoT 中心，并使用 MQTT 协议每秒发送一次遥测消息。

可以下载或克隆 Visual Studio 解决方案，其中包含 GitHub 提供的三个应用。

```bash
git clone https://github.com/Azure-Samples/iot-hub-dotnet-simulated-device-client-app.git
```

> [!NOTE]
> 有关 Azure IoT SDK 的信息（可以使用这些 SDK 构建可在设备和解决方案后端上运行的应用程序），请参阅 [Azure IoT SDK][lnk-hub-sdks]。
> 
> 

若要完成本教程，您需要以下各项：

* Visual Studio 2015 或 Visual Studio 2017。
* 有效的 Azure 帐户。 （如果没有帐户，只需花费几分钟就能创建一个[免费帐户][lnk-free-trial]。）

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

现已创建 IoT 中心，因此已获得完成本教程的其余部分所需的主机名和 IoT 中心连接字符串。

<a id="DeviceIdentity_csharp"></a>
[!INCLUDE [iot-hub-get-started-create-device-identity-csharp](../../includes/iot-hub-get-started-create-device-identity-csharp.md)]

<a id="D2C_csharp"></a>
## 接收设备到云的消息
<a id="receive-device-to-cloud-messages" class="xliff"></a>
本部分将创建一个 .NET 控制台应用，用于从 IoT 中心读取设备到云的消息。 IoT 中心公开与 [Azure 事件中心][lnk-event-hubs-overview]兼容的终结点，以让用户读取设备到云的消息。 为了简单起见，本教程创建的基本读取器不适用于高吞吐量部署。 若要了解如何大规模处理设备到云的消息，请参阅[处理设备到云的消息][lnk-process-d2c-tutorial]教程。 若要深入了解如何处理来自事件中心的消息，请参阅[事件中心入门][lnk-eventhubs-tutorial]教程。 （本教程适用与 IoT 中心和事件中心相兼容的终结点。）

> [!NOTE]
> 读取设备到云消息的事件中心兼容终结点始终使用 AMQP 协议。
> 
> 

1. 在 Visual Studio 中，使用“控制台应用(.NET Framework)”项目模板将 Visual C# Windows 经典桌面项目添加到当前解决方案。 确保 .NET Framework 版本为 4.5.1 或更高。 将项目命名为 **ReadDeviceToCloudMessages**。
   
    ![新的 Visual C# Windows 经典桌面项目][10a]
2. 在解决方案资源管理器中，右键单击“ReadDeviceToCloudMessages”项目，然后单击“管理 NuGet 包”。
3. 在“NuGet 包管理器”窗口中，搜索 **WindowsAzure.ServiceBus**，选择“安装”并接受使用条款。 该过程将下载、安装 [Azure 服务总线][lnk-servicebus-nuget]及其所有依赖项并添加对它的引用。 此包可让应用程序连接到 IoT 中心上与事件中心兼容的终结点。
4. 在 **Program.cs** 文件顶部添加以下 `using` 语句：
   
   ```csharp
   using Microsoft.ServiceBus.Messaging;
   using System.Threading;
   ```
5. 将以下字段添加到 **Program** 类。 将占位符值替换为在“创建 IoT 中心”部分为中心创建的 IoT 中心连接字符串。
   
   ```csharp
   static string connectionString = "{iothub connection string}";
   static string iotHubD2cEndpoint = "messages/events";
   static EventHubClient eventHubClient;
   ```
6. 将以下方法添加到 **Program** 类：
   
   ```csharp
    private static async Task ReceiveMessagesFromDeviceAsync(string partition, CancellationToken ct)
    {
        var eventHubReceiver = eventHubClient.GetDefaultConsumerGroup().CreateReceiver(partition, DateTime.UtcNow);
        while (true)
        {
            if (ct.IsCancellationRequested) break;
            EventData eventData = await eventHubReceiver.ReceiveAsync();
            if (eventData == null) continue;

            string data = Encoding.UTF8.GetString(eventData.GetBytes());
            Console.WriteLine("Message received. Partition: {0} Data: '{1}'", partition, data);
        }
    }
   ```
   
    此方法使用 **EventHubReceiver** 实例接收来自所有 IoT 中心设备到云接收分区的消息。 请注意在创建 **EventHubReceiver** 对象时传递 `DateTime.Now` 参数的方式，使它仅接收启动后发送的消息。 此筛选器在测试环境中非常有用，因为这样可以看到当前的消息集。 在生产环境中，代码应确保处理所有消息。 有关详细信息，请参阅[如何处理 IoT 中心设备到云的消息][lnk-process-d2c-tutorial]教程。
7. 最后，在 **Main** 方法中添加以下行：
   
   ```csharp
    Console.WriteLine("Receive messages. Ctrl-C to exit.\n");
    eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, iotHubD2cEndpoint);

    var d2cPartitions = eventHubClient.GetRuntimeInformation().PartitionIds;

    CancellationTokenSource cts = new CancellationTokenSource();

    System.Console.CancelKeyPress += (s, e) =>
    {
        e.Cancel = true;
        cts.Cancel();
        Console.WriteLine("Exiting...");
    };

    var tasks = new List<Task>();
    foreach (string partition in d2cPartitions)
    {
        tasks.Add(ReceiveMessagesFromDeviceAsync(partition, cts.Token));
    }  
    Task.WaitAll(tasks.ToArray());
   ```

## 创建模拟设备应用程序
<a id="create-a-simulated-device-app" class="xliff"></a>
本部分将创建一个 .NET 控制台应用，用于模拟向 IoT 中心发送设备到云消息的设备。

1. 在 Visual Studio 中，使用“控制台应用(.NET Framework)”项目模板将 Visual C# Windows 经典桌面项目添加到当前解决方案。 确保 .NET Framework 版本为 4.5.1 或更高。 将项目命名为 **SimulatedDevice**。
   
    ![新的 Visual C# Windows 经典桌面项目][10b]
2. 在解决方案资源管理器中，右键单击“SimulatedDevice”项目，然后单击“管理 NuGet 包”。
3. 在“NuGet 包管理器”窗口中，选择“浏览”，搜索 **Microsoft.Azure.Devices.Client**，选择“安装”以安装 **Microsoft.Azure.Devices.Client** 包，然后接受使用条款。 该过程将下载、安装 [Azure IoT 设备 SDK][lnk-device-nuget] NuGet 包及其依赖项并添加对它的引用。
4. 在 **Program.cs** 文件顶部添加以下 `using` 语句：
   
   ```csharp
   using Microsoft.Azure.Devices.Client;
   using Newtonsoft.Json;
   ```
5. 将以下字段添加到 **Program** 类。 将占位符值替换为在“创建 IoT 中心”部分中检索到的 IoT 中心主机名，以及在“创建设备标识”部分中检索到的设备密钥。
   
   ```csharp
   static DeviceClient deviceClient;
   static string iotHubUri = "{iot hub hostname}";
   static string deviceKey = "{device key}";
   ```

6. 将以下方法添加到 **Program** 类：
   
   ```csharp
    private static async void SendDeviceToCloudMessagesAsync()
    {
        double minTemperature = 20;
        double minHumidity = 60;
        int messageId = 1;
        Random rand = new Random();

        while (true)
        {
            double currentTemperature = minTemperature + rand.NextDouble() * 15;
            double currentHumidity = minHumidity + rand.NextDouble() * 20;

            var telemetryDataPoint = new
            {
                messageId = messageId++,
                deviceId = "myFirstDevice",
                temperature = currentTemperature,
                humidity = currentHumidity
            };
            var messageString = JsonConvert.SerializeObject(telemetryDataPoint);
            var message = new Message(Encoding.ASCII.GetBytes(messageString));
            message.Properties.Add("temperatureAlert", (currentTemperature > 30) ? "true" : "false");

            await deviceClient.SendEventAsync(message);
            Console.WriteLine("{0} > Sending message: {1}", DateTime.Now, messageString);

            await Task.Delay(1000);
        }
    }
   ```
    此方法每隔一秒发送一条新的设备到云消息。 该消息包含一个具有设备 ID 的 JSON 序列化对象和一个随机生成的编号，用于模拟温度传感器和湿度传感器。
7. 最后，在 **Main** 方法中添加以下行：
   
   ```csharp
   Console.WriteLine("Simulated device\n");
   deviceClient = DeviceClient.Create(iotHubUri, new DeviceAuthenticationWithRegistrySymmetricKey("myFirstDevice", deviceKey), TransportType.Mqtt);

   SendDeviceToCloudMessagesAsync();
   Console.ReadLine();
   ```
   
   默认情况下，.NET Framework 应用中的 **Create** 方法会创建一个 **DeviceClient** 实例，该实例使用 AMQP 协议与 IoT 中心通信（UWP 和 PCL 客户端默认使用 HTTP）。若要使用 MQTT 或 HTTP 协议，请使用 **Create** 方法的重写，它使用户能够指定协议。 如果使用 HTTP 协议，则还应在项目中添加 **Microsoft.AspNet.WebApi.Client** NuGet 包，以包含 **System.Net.Http.Formatting** 命名空间。

本教程将逐步讲解创建 IoT 中心模拟设备应用的步骤。 也可以使用 [Azure IoT 中心的连接服务][lnk-connected-service] Visual Studio 扩展将所需的代码添加到设备应用。

> [!NOTE]
> 为简单起见，本教程不实现任何重试策略。 在生产代码中，应该按 MSDN 文章 [Transient Fault Handling][lnk-transient-faults]（暂时性故障处理）中所述实施重试策略（例如指数性的回退）。
> 
> 

## 运行应用
<a id="run-the-apps" class="xliff"></a>
现在可以运行应用了。

1. 在 Visual Studio 的“解决方案资源管理器”中右键单击解决方案，然后单击“设置启动项目”。 选择“多个启动项目”，然后针对“ReadDeviceToCloudMessages”和“SimulatedDevice”项目选择“启动”作为操作。
   
    ![启动项目属性][41]
2. 按 **F5** 启动这两个应用，使其运行。 来自 **SimulatedDevice** 应用的控制台输出会显示模拟设备应用发送到 IoT 中心的消息。 来自 **ReadDeviceToCloudMessages** 应用的控制台输出则会显示 IoT 中心接收的消息。
   
    ![来自应用的控制台输出][42]
3. [Azure 门户][lnk-portal]中的“使用情况”磁贴显示发送到 IoT 中心的消息数：
   
    ![Azure 门户的“使用情况”磁贴][43]

## 后续步骤
<a id="next-steps" class="xliff"></a>
在本教程中，你已在 Azure 门户中配置了 IoT 中心，并在 IoT 中心的标识注册表中创建了设备标识。 你已使用此设备标识来让模拟设备应用向 IoT 中心发送设备到云的消息。 还创建了用于显示 IoT 中心所接收消息的应用。 

若要继续了解 IoT 中心入门知识并浏览其他 IoT 方案，请参阅：

* [连接你的设备][lnk-connect-device]
* [设备管理入门][lnk-device-management]
* [IoT 边缘入门][lnk-iot-edge]

若要了解如何扩展 IoT 解决方案和如何大规模处理设备到云的消息，请参阅 [Process device-to-cloud messages][lnk-process-d2c-tutorial]（处理设备到云的消息）教程。

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]

<!-- Images. -->
[41]: ./media/iot-hub-csharp-csharp-getstarted/run-apps1.png
[42]: ./media/iot-hub-csharp-csharp-getstarted/run-apps2.png
[43]: ./media/iot-hub-csharp-csharp-getstarted/usage.png
[10a]: ./media/iot-hub-csharp-csharp-getstarted/create-receive-csharp1.png
[10b]: ./media/iot-hub-csharp-csharp-getstarted/create-device-csharp1.png


<!-- Links -->
[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-servicebus-nuget]: https://www.nuget.org/packages/WindowsAzure.ServiceBus
[lnk-event-hubs-overview]: ../event-hubs/event-hubs-overview.md

[lnk-device-nuget]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-connected-service]: https://visualstudiogallery.msdn.microsoft.com/e254a3a5-d72e-488e-9bd3-8fee8e0cd1d6
[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-iot-edge]: iot-hub-linux-iot-edge-get-started.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/

