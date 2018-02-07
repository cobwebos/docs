---
title: "使用 Azure IoT 中心路由消息 (.NET) | Microsoft Docs"
description: "如何使用路由规则和自定义终结点将消息发送到其他后端服务，从而处理 Azure IoT 中心的设备到云消息。"
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 5177bac9-722f-47ef-8a14-b201142ba4bc
ms.service: iot-hub
ms.devlang: csharp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/25/2017
ms.author: dobett
ms.openlocfilehash: 4794f81787fd575c34fa8a2ef66431d85dcf4a5d
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2018
---
# <a name="routing-messages-with-iot-hub-net"></a>使用 IoT 中心路由消息 (.NET)

[!INCLUDE [iot-hub-selector-process-d2c](../../includes/iot-hub-selector-process-d2c.md)]

本教程是在 [IoT 中心入门]教程的基础上制作的。 本教程：

* 介绍如何以基于配置的轻松方式，使用路由规则发送设备到云的消息。
* 介绍如何隔离需要解决方案后端立即执行操作以进行进一步处理的交互式消息。 例如，设备可能将发送一条警报消息，触发在 CRM 系统中插入票证。 与此相反，数据点消息（例如温度遥测）则送入分析引擎。

在本教程结束时，会运行三个 .NET 控制台应用：

* **SimulatedDevice**，（在 [IoT 中心入门]教程中创建的应用的修改版本）每秒发送一次设备到云的数据点消息，每 10 秒发送一次设备到云的交互式消息。
* **ReadDeviceToCloudMessages**，显示设备应用发送的非关键遥测数据。
* **ReadCriticalQueue**，从服务总线队列取消设备应用发送的关键消息的排队。 此队列附加到 IoT 中心。

> [!NOTE]
> IoT 中心对许多设备平台和语言（包括 C、Java 和 JavaScript）提供 SDK 支持。 若要了解如何将本教程中的模拟设备替换为物理设备，请参阅 [Azure IoT 开发人员中心]。

要完成本教程，需要以下各项：

* Visual Studio 2015 或 Visual Studio 2017。
* 有效的 Azure 帐户。 <br/>如果没有帐户，只需花费几分钟就能创建一个[免费帐户](https://azure.microsoft.com/free/)。

我们建议另外阅读有关 [Azure 存储]和 [Azure 服务总线]的主题。

## <a name="send-interactive-messages"></a>发送交互式消息

修改在 [IoT 中心入门]教程中创建的设备应用，以不定期发送交互式消息。

在 Visual Studio 的 **SimulatedDevice** 项目中，将 `SendDeviceToCloudMessagesAsync` 方法替换为以下代码：

```csharp
private static async void SendDeviceToCloudMessagesAsync()
{
    double minTemperature = 20;
    double minHumidity = 60;
    Random rand = new Random();

    while (true)
    {
        double currentTemperature = minTemperature + rand.NextDouble() * 15;
        double currentHumidity = minHumidity + rand.NextDouble() * 20;

        var telemetryDataPoint = new
        {
            deviceId = "myFirstDevice",
            temperature = currentTemperature,
            humidity = currentHumidity
        };
        var messageString = JsonConvert.SerializeObject(telemetryDataPoint);
        string levelValue;

        if (rand.NextDouble() > 0.7)
        {
            if (rand.NextDouble() > 0.5)
            {
                messageString = "This is a critical message";
                levelValue = "critical";
            }
            else 
            {
                messageString = "This is a storage message";
                levelValue = "storage";
            }
        }
        else
        {
            levelValue = "normal";
        }
        
        var message = new Message(Encoding.ASCII.GetBytes(messageString));
        message.Properties.Add("level", levelValue);
        
        await deviceClient.SendEventAsync(message);
        Console.WriteLine("{0} > Sent message: {1}", DateTime.Now, messageString);

        await Task.Delay(1000);
    }
}
```

此方法会将 `"level": "critical"` 和 `"level": "storage"` 属性随机添加到设备发送的消息，此设备模拟需要应用程序后端立即执行操作的消息，或需要永久存储的消息。 应用程序支持基于消息正文路由消息。

> [!NOTE]
> 可使用消息属性根据各种方案路由消息，包括冷路径处理和此处所示的热路径示例。

> [!NOTE]
> 我们强烈建议根据 MSDN 文章 [暂时性故障处理]中的建议实施重试策略（如指数退让）。

## <a name="route-messages-to-a-queue-in-your-iot-hub"></a>将消息路由到 IoT 中心中的队列

本部分的操作：

* 创建服务总线队列。
* 将其连接到 IoT 中心。
* 配置 IoT 中心，根据所存在的消息属性将消息发送到队列。

若要深入了解如何处理来自服务总线队列的消息，请参阅[队列入门][Service Bus queue]教程。

1. 按[队列入门][Service Bus queue]中所述，创建服务总线队列。 队列必须与 IoT 中心位于同一订阅和区域中。 记下命名空间和队列名称。

    > [!NOTE]
    > 用作 IoT 中心终结点的服务总线队列和主题不得启用会话或重复检测选项。 如果启用了其中任一选项，该终结点会在 Azure 门户中显示为“无法访问”。

2. 在 Azure 门户中，打开 IoT 中心并单击“终结点”。
    
    ![IoT 中心的终结点][30]

3. 在“终结点”边栏选项卡中，单击顶部的“添加”，将队列添加到 IoT 中心。 将终结点命名为“CriticalQueue”，并使用下拉列表选择“服务总线队列”、队列所在的服务总线命名空间和队列名称。 完成后，单击底部的“**保存**”。
    
    ![添加终结点][31]
    
4. 现在单击 IoT 中心的“路由”。 单击边栏选项卡顶部的“添加”，创建将消息路由到刚添加的队列的路由规则。 选择“DeviceTelemetry”作为数据源。 输入 `level="critical"` 作为条件，并选择刚添加为自定义终结点的队列作为路由规则终结点。 完成后，单击底部的“**保存**”。
    
    ![添加路由][32]
    
    请确保回退路由设为“开”。 此值是 IoT 中心的默认配置。
    
    ![回退路由][33]

## <a name="read-from-the-queue-endpoint"></a>从队列终结点读取

在本部分中，会从队列终结点读取消息。

1. 在 Visual Studio 中，使用“控制台应用(.NET Framework)”项目模板将 Visual C# Windows 经典桌面项目添加到当前解决方案。 将项目命名为 **ReadCriticalQueue**。

2. 在解决方案资源管理器中，右键单击 **ReadCriticalQueue** 项目，并单击“管理 NuGet 包”。 此操作会显示“**NuGet 包管理器**”窗口。

3. 搜索“**WindowsAzure.ServiceBus**”，单击“**安装**”，并接受使用条款。 此操作会下载和安装 Azure 服务总线及其所有依赖项，并添加对它的引用。

4. 在 **Program.cs** 文件顶部添加以下 **using** 语句：
   
    ```csharp
    using System.IO;
    using Microsoft.ServiceBus.Messaging;
    ```

5. 最后，将下面的行添加到 **Main** 方法。 将连接字符串替换为队列的“侦听”权限：
   
    ```csharp
    Console.WriteLine("Receive critical messages. Ctrl-C to exit.\n");
    var connectionString = "{service bus listen string}";
    var queueName = "{queue name}";
    
    var client = QueueClient.CreateFromConnectionString(connectionString, queueName);

    client.OnMessage(message =>
        {
            Stream stream = message.GetBody<Stream>();
            StreamReader reader = new StreamReader(stream, Encoding.ASCII);
            string s = reader.ReadToEnd();
            Console.WriteLine(String.Format("Message body: {0}", s));
        });
        
    Console.ReadLine();
    ```

## <a name="run-the-applications"></a>运行应用程序
现在，已准备就绪，可以运行应用程序了。

1. 在 Visual Studio 的解决方案资源管理器中，右键单击解决方案并选择“**设置启动项目**”。 选择“多个启动项目”，并为 **ReadDeviceToCloudMessages**、**SimulatedDevice** 和 **ReadCriticalQueue** 项目选择“启动”作为操作。
2. 按 **F5** 启动三个控制台应用。 **ReadDeviceToCloudMessages** 应用仅拥有 **SimulatedDevice** 应用程序发送的非关键消息，而 **ReadCriticalQueue** 应用仅拥有关键消息。
   
   ![3 个控制台应用][50]

## <a name="optional-add-storage-container-to-your-iot-hub-and-route-messages-to-it"></a>（可选）将存储容器添加到 IoT 中心并向其路由消息

在本部分中，将创建一个存储帐户并将其连接到 IoT 中心，还会配置 IoT 中心，根据消息上的现有属性发送消息到该帐户。 有关如何管理存储的详细信息，请参阅 [Azure 存储入门][Azure 存储]。

 > [!NOTE]
   > 如果并不局限于一个**终结点**，则除了 **CriticalQueue** 以外，还可以设置 **StorageContainer**，并同时运行两者。

1. 根据 [Azure 存储文档][lnk-storage] 中所述创建存储帐户。 记下帐户名称。

2. 在 Azure 门户中，打开 IoT 中心并单击“终结点”。

3. 在“终结点”边栏选项卡中选择“CriticalQueue”终结点，单击“删除”。 单击“是”，然后单击“添加”。 将终结点命名为 **StorageContainer**，使用下拉列表选择“Azure 存储容器”，并创建**存储帐户**和**存储容器**。  请记下名称。  完成后，单击底部的“确定”。 

 > [!NOTE]
   > 如果并不局限于一个**终结点**，则不需要删除 **CriticalQueue**。

4. 在 IoT 中心单击“路由”。 单击边栏选项卡顶部的“添加”，创建将消息路由到刚添加的队列的路由规则。 选择“设备消息”作为数据源。 输入 `level="storage"` 作为条件，并选择自定义终结点 **StorageContainer** 作为路由规则终结点。 在底部单击“保存”。  

    请确保回退路由设为“开”。 此设置是 IoT 中心的默认配置。

1. 确保以前的应用程序仍在运行。 

1. 在 Azure 门户中，转到自己的存储帐户，在“Blob 服务”下面单击“浏览 Blob...”。选择自己的容器，导航到并单击 JSON 文件，然后单击“下载”以查看数据。

## <a name="next-steps"></a>后续步骤
在本教程中，介绍了如何使用 IoT 中心的消息路由功能可靠地分派设备到云的消息。

[如何使用 IoT 中心发送云到设备的消息][lnk-c2d]介绍了如何从解决方案后端向设备发送消息。

若要查看使用 IoT 中心的完整端到端解决方案的示例，请参阅 [Azure IoT 套件][lnk-suite]。

若要了解有关使用 IoT 中心开发解决方案的详细信息，请参阅 [IoT 中心开发人员指南]。

若要详细了解 IoT 中心的消息路由，请参阅[使用 IoT 中心发送和接收消息][lnk-devguide-messaging]。

<!-- Images. -->
[50]: ./media/iot-hub-csharp-csharp-process-d2c/run1.png
[30]: ./media/iot-hub-csharp-csharp-process-d2c/click-endpoints.png
[31]: ./media/iot-hub-csharp-csharp-process-d2c/endpoint-creation.png
[32]: ./media/iot-hub-csharp-csharp-process-d2c/route-creation.png
[33]: ./media/iot-hub-csharp-csharp-process-d2c/fallback-route.png

<!-- Links -->
[Service Bus queue]: ../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md
[Azure 存储]: https://azure.microsoft.com/documentation/services/storage/
[Azure 服务总线]: https://azure.microsoft.com/documentation/services/service-bus/
[IoT 中心开发人员指南]: iot-hub-devguide.md
[IoT 中心入门]: iot-hub-csharp-csharp-getstarted.md
[lnk-devguide-messaging]: iot-hub-devguide-messaging.md
[Azure IoT 开发人员中心]: https://azure.microsoft.com/develop/iot
[暂时性故障处理]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-c2d]: iot-hub-csharp-csharp-c2d.md
[lnk-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
