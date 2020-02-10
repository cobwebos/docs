---
title: 使用 Azure IoT 中心发送云到设备消息 (.NET) | Microsoft Docs
description: 如何使用用于 .NET 的 Azure IoT SDK 将云到设备消息从 Azure IoT 中心发送到设备。 修改设备应用以接收云到设备消息，并修改后端应用以发送云到设备消息。
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: robinsh
ms.openlocfilehash: 7805b9b3f000b2bc2e45272ab9ff469d5711e581
ms.sourcegitcommit: 9add86fb5cc19edf0b8cd2f42aeea5772511810c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/09/2020
ms.locfileid: "77110201"
---
# <a name="send-messages-from-the-cloud-to-your-device-with-iot-hub-net"></a>使用 IoT 中心 (.NET) 将消息从云发送到设备

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Azure IoT 中心是一项完全托管的服务，有助于在数百万台设备和单个解决方案后端之间实现安全可靠的双向通信。 将[遥测数据从设备发送到 iot 中心](quickstart-send-telemetry-dotnet.md)快速入门介绍了如何创建 iot 中心、在其中预配设备标识，以及编写用于发送设备到云消息的设备应用程序。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

本教程的基础[是将遥测数据从设备发送到 IoT 中心](quickstart-send-telemetry-dotnet.md)。 其中介绍了如何执行以下任务：

* 通过 IoT 中心，将云到设备的消息从解决方案后端发送到单个设备。

* 在设备上接收云到设备的消息。

* 从解决方案后端，请求传递从 IoT 中心发送到设备的消息的确认（*反馈*）。

可以在 [IoT 中心的 D2C 和 C2D 消息传送](iot-hub-devguide-messaging.md)中找到有关“云到设备”消息的详细信息。

在本教程结束时，将运行两个 .NET 控制台应用。

* **SimulatedDevice**。 此应用连接到 IoT 中心并接收云到设备的消息。 此应用是在[将遥测从设备发送到 IoT 中心](quickstart-send-telemetry-dotnet.md)中创建的应用的修改版本。

* **SendCloudToDevice**。 此应用通过 IoT 中心将云到设备的消息发送到设备应用，然后接收其传递确认。

> [!NOTE]
> IoT 中心通过[Azure IoT 设备 sdk](iot-hub-devguide-sdks.md)对许多设备平台和语言（包括 C、Java、Python 和 Javascript）提供 SDK 支持。 有关如何将设备连接到本教程中的代码（通常是连接到 Azure IoT 中心）的分步说明，请参阅 [IoT 中心开发人员指南](iot-hub-devguide.md)。
>

## <a name="prerequisites"></a>先决条件

* Visual Studio

* 有效的 Azure 帐户。 如果没有帐户，只需花费几分钟就能创建一个[免费帐户](https://azure.microsoft.com/pricing/free-trial/)。

* 请确保已在防火墙中打开端口8883。 本文中的设备示例使用了 MQTT 协议，该协议通过端口8883进行通信。 此端口可能在某些企业和教育网络环境中被阻止。 有关此问题的详细信息和解决方法，请参阅[连接到 IoT 中心（MQTT）](iot-hub-mqtt-support.md#connecting-to-iot-hub)。

## <a name="receive-messages-in-the-device-app"></a>在设备应用中接收消息

在本部分中，请修改在[将遥测从设备发送到 iot 中心](quickstart-send-telemetry-dotnet.md)中创建的设备应用，以接收来自 iot 中心的云到设备消息。

1. 在 Visual Studio 的 **SimulatedDevice** 项目中，将以下方法添加到 **Program** 类。

   ```csharp
    private static async void ReceiveC2dAsync()
    {
        Console.WriteLine("\nReceiving cloud to device messages from service");
        while (true)
        {
            Message receivedMessage = await s_deviceClient.ReceiveAsync();
            if (receivedMessage == null) continue;

            Console.ForegroundColor = ConsoleColor.Yellow;
            Console.WriteLine("Received message: {0}", 
            Encoding.ASCII.GetString(receivedMessage.GetBytes()));
            Console.ResetColor();

            await s_deviceClient.CompleteAsync(receivedMessage);
        }
    }
   ```

1. 在 **Main** 方法中的 `Console.ReadLine()` 行前面添加以下方法：

   ```csharp
   ReceiveC2dAsync();
   ```

在设备收到消息时，`ReceiveAsync` 方法以异步方式返回收到的消息。 它在可指定的超时期限后返回*null* 。 在此示例中，将使用默认值一分钟。 当应用收到 *null* 时，它应继续等待新消息。 此要求是使用 `if (receivedMessage == null) continue` 行的原因。

对 `CompleteAsync()` 的调用将通知 IoT 中心，指出已成功处理消息。 可以安全地从设备队列中删除该消息。 如果因故导致设备应用无法完成消息处理作业，IoT 中心将再传递一次。 设备应用中的消息处理逻辑必须是*幂*等的，以便多次接收同一消息会生成相同的结果。

应用程序也可以暂时放弃消息，让 IoT 中心将消息保留在队列中以供将来使用。 或者，应用程序可以拒绝消息，以永久性从队列中删除该消息。 有关云到设备消息生命周期的详细信息，请参阅 [IoT 中心的 D2C 和 C2D 消息传送](iot-hub-devguide-messaging.md)。

   > [!NOTE]
   > 使用 HTTPS 而不使用 MQTT 或 AMQP 作为传输时，`ReceiveAsync` 方法将立即返回。 使用 HTTPS 的云到设备消息，其支持模式是间歇连接到设备，且不常检查消息（时间间隔小于 25 分钟）。 发出更多 HTTPS 接收会导致 IoT 中心限制请求。 有关 MQTT、AMQP 和 HTTPS 支持之间的差异，以及 IoT 中心限制的详细信息，请参阅 [IoT 中心的 D2C 和 C2D 消息传送](iot-hub-devguide-messaging.md)。
   >

## <a name="get-the-iot-hub-connection-string"></a>获取 IoT 中心连接字符串

在本文中，将创建一个后端服务，通过[从设备向 IoT 中心发送遥测](quickstart-send-telemetry-dotnet.md)数据创建的 iot 中心发送云到设备的消息。 若要发送云到设备的消息，服务需要**服务连接**权限。 默认情况下，每个 IoT 中心都创建有一个名为 "**服务**" 的共享访问策略，该策略将授予此权限。

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>发送云到设备的消息

现在，编写一个 .NET 控制台应用，用于将云到设备的消息发送到设备应用。

1. 在当前的 Visual Studio 解决方案中，选择 "**文件**" > **新建** > **项目**"。 在 "**创建新项目**" 中，选择 "**控制台应用（.NET Framework）** "，然后选择 "**下一步**"。

1. 将项目命名为 *SendCloudToDevice*。 在 "**解决方案**" 下，选择 "**添加到解决方案**" 并接受最新版本的 .NET Framework。 选择“创建”来创建项目。

   ![在 Visual Studio 中配置新项目](./media/iot-hub-csharp-csharp-c2d/sendcloudtodevice-project-configure.png)

1. 在解决方案资源管理器中，右键单击新的解决方案，然后选择 "**管理 NuGet 包**"。

1. 在 "**管理 NuGet 包**" 中，选择 "**浏览**"，然后搜索并选择 " **Microsoft Azure. 设备**"。 选择 "**安装**"。

   此步骤将下载、安装[Azure IoT 服务 SDK NuGet 包](https://www.nuget.org/packages/Microsoft.Azure.Devices/)并添加对它的引用。

1. 将以下 `using` 语句添加到**Program.cs**文件的顶部。

   ``` csharp
   using Microsoft.Azure.Devices;
   ```

1. 将以下字段添加到 Program 类。 将占位符值替换为之前在[获取 iot 中心连接字符串](#get-the-iot-hub-connection-string)中复制的 iot 中心连接字符串。

   ``` csharp
   static ServiceClient serviceClient;
   static string connectionString = "{iot hub connection string}";
   ```

1. 将以下方法添加到 **Program** 类。 在将[遥测数据从设备发送到 IoT 中心](quickstart-send-telemetry-dotnet.md)时，将设备名称设置为所用的名称。

   ``` csharp
   private async static Task SendCloudToDeviceMessageAsync()
   {
        var commandMessage = new
         Message(Encoding.ASCII.GetBytes("Cloud to device message."));
        await serviceClient.SendAsync("myFirstDevice", commandMessage);
   }
   ```

   此方法会将新的云到设备消息发送到 ID 为 `myFirstDevice` 的设备。 仅当从从[设备向 IoT 中心发送遥测](quickstart-send-telemetry-dotnet.md)数据时使用的参数修改此参数时，才更改此参数。

1. 最后，将下面的行添加到 **Main** 方法。

   ``` csharp
   Console.WriteLine("Send Cloud-to-Device message\n");
   serviceClient = ServiceClient.CreateFromConnectionString(connectionString);

   Console.WriteLine("Press any key to send a C2D message.");
   Console.ReadLine();
   SendCloudToDeviceMessageAsync().Wait();
   Console.ReadLine();
   ```

1. 在 "解决方案资源管理器" 中，右键单击解决方案，然后选择 "**设置启动项目**"。

1. 在 "**通用属性** > **启动项目**" 中，选择 "**多个启动项目**"，然后选择**ReadDeviceToCloudMessages**、 **SimulatedDevice**和**SendCloudToDevice**的 "**启动**" 操作。 选择“确定”保存更改。

1. 按 **F5**。 这三个应用程序应该都会启动。 选择“**SendCloudToDevice**”窗口并按 **Enter**。 应会看到设备应用正在接收的消息。

   ![应用接收消息](./media/iot-hub-csharp-csharp-c2d/sendc2d1.png)

## <a name="receive-delivery-feedback"></a>接收送达反馈

可以为每个云到设备消息的 IoT 中心请求送达（或过期）确认。 借助此选项，解决方案后端能够轻松地通知重试或补偿逻辑。 有关云到设备反馈的详细信息，请参阅 [IoT 中心的 D2C 和 C2D 消息传送](iot-hub-devguide-messaging.md)。

在本部分中，修改 **SendCloudToDevice** 应用以请求反馈，并接收来自 IoT 中心的反馈。

1. 在 Visual Studio 中的 **SendCloudToDevice** 项目内，将以下方法添加到 **Program** 类。

   ```csharp
   private async static void ReceiveFeedbackAsync()
   {
        var feedbackReceiver = serviceClient.GetFeedbackReceiver();

        Console.WriteLine("\nReceiving c2d feedback from service");
        while (true)
        {
            var feedbackBatch = await feedbackReceiver.ReceiveAsync();
            if (feedbackBatch == null) continue;

            Console.ForegroundColor = ConsoleColor.Yellow;
            Console.WriteLine("Received feedback: {0}",
              string.Join(", ", feedbackBatch.Records.Select(f => f.StatusCode)));
            Console.ResetColor();

            await feedbackReceiver.CompleteAsync(feedbackBatch);
        }
    }
    ```

    请注意，此接收模式与用于从设备应用接收云到设备消息的模式相同。

1. 在**Main**方法中，在 `serviceClient = ServiceClient.CreateFromConnectionString(connectionString)`后面添加以下行。

   ``` csharp
   ReceiveFeedbackAsync();
   ```

1. 若要请求针对传递云到设备消息的反馈，必须在 **SendCloudToDeviceMessageAsync** 方法中指定一个属性。 在 `var commandMessage = new Message(...);` 行的后面添加以下行。

   ``` csharp
   commandMessage.Ack = DeliveryAcknowledgement.Full;
   ```

1. 按 **F5** 运行应用。 应会看到三个应用程序都在启动。 选择“**SendCloudToDevice**”窗口并按 **Enter**。 应会看到设备应用正在接收的消息，几秒钟后，**SendCloudToDevice** 应用程序将收到反馈消息。

   ![应用接收消息](./media/iot-hub-csharp-csharp-c2d/sendc2d2.png)

> [!NOTE]
> 为简单起见，本教程不实现任何重试策略。 在生产代码中，应按照[暂时性故障处理](/azure/architecture/best-practices/transient-faults)中的建议，实现重试策略（如指数回退）。
>

## <a name="next-steps"></a>后续步骤

本操作说明已介绍了如何发送和接收云到设备的消息。

若要查看使用 IoT 中心完成端到端解决方案的示例，请参阅 [Azure IoT 远程监视解决方案加速器](https://docs.microsoft.com/azure/iot-suite/)。

若要了解有关使用 IoT 中心开发解决方案的详细信息，请参阅 [IoT 中心开发人员指南](iot-hub-devguide.md)。
