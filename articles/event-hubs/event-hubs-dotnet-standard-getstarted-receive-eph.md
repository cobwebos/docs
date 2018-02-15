---
title: "使用 .NET Standard 库从 Azure 事件中心接收事件 | Microsoft Docs"
description: "使用 .NET Standard 中的 EventProcessorHost 接收消息入门"
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2017
ms.author: sethm
ms.openlocfilehash: 0dd3533ab1556b334c09ba69d096b06c8be85cc8
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/03/2018
---
# <a name="get-started-receiving-messages-with-the-event-processor-host-in-net-standard"></a>使用 .NET Standard 中的事件处理程序主机接收消息入门

> [!NOTE]
> [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleEphReceiver) 上提供了此示例。

本教程介绍如何编写 .NET Core 控制台应用程序，以使用**事件处理程序主机**库从事件中心接收消息。 可以按原样运行 [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleEphReceiver) 解决方案，将字符串替换为事件中心和存储帐户的值。 或者，可以按照本教程中的步骤创建自己的解决方案。

## <a name="prerequisites"></a>先决条件

* [Microsoft Visual Studio 2015 或 2017](http://www.visualstudio.com)。 本教程中的示例使用 Visual Studio 2017，但也支持 Visual Studio 2015。
* [.NET Core Visual Studio 2015 或 2017 工具](http://www.microsoft.com/net/core)。
* Azure 订阅。
* Azure 事件中心命名空间。
* 一个 Azure 存储帐户。

## <a name="create-an-event-hubs-namespace-and-an-event-hub"></a>创建事件中心命名空间和事件中心  

第一步是使用 [Azure 门户](https://portal.azure.com)创建事件中心类型的命名空间，并获取应用程序与事件中心进行通信所需的管理凭据。 要创建命名空间和事件中心，请按照[此文](event-hubs-create.md)中的步骤操作，并继续学习本教程。  

## <a name="create-an-azure-storage-account"></a>创建 Azure 存储帐户  

1. 登录到 [Azure 门户](https://portal.azure.com)。  
2. 在门户的左侧导航窗格中，依次单击“新建”、“存储”和“存储帐户”。  
3. 完成存储帐户窗口中的字段，并单击“创建”。

    ![创建存储帐户][1]

4. 看到“部署成功”消息后，单击新存储帐户名。 在“概要”窗口中单击“Blob”。 “Blob 服务”对话框打开时，单击顶部的“+ 容器”。 为容器指定名称，并关闭“Blob 服务”。  
5. 单击左侧窗口中的“访问密钥”，复制存储容器、存储帐户的名称和 **key1** 的值。 将这些值保存到记事本或其他临时位置。  

## <a name="create-a-console-application"></a>创建控制台应用程序

启动 Visual Studio。 在“文件”菜单中，单击“新建”，并单击“项目”。 创建 .NET Core 控制台应用程序。

![新建项目][2]

## <a name="add-the-event-hubs-nuget-package"></a>添加事件中心 NuGet 包

遵循以下步骤，将 [**Microsoft.Azure.EventHubs**](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) 和 [**Microsoft.Azure.EventHubs.Processor**](https://www.nuget.org/packages/Microsoft.Azure.EventHubs.Processor/) .NET Standard 库 NuGet 包添加项目： 

1. 右键单击新创建的项目，并选择“管理 NuGet 包” 。
2. 单击“浏览”选项卡，然后搜索“Microsoft.Azure.EventHubs”，并选择“Microsoft.Azure.EventHubs”包。 单击“安装”以完成安装，并关闭此对话框。
3. 重复步骤 1 和步骤 2，安装“Microsoft.Azure.EventHubs.Processor”包。

## <a name="implement-the-ieventprocessor-interface"></a>实现 IEventProcessor 接口

1. 在“解决方案资源管理器”中，右键单击该项目，单击“添加”，并单击“类”。 将新类命名为 **SimpleEventProcessor**。

2. 打开 SimpleEventProcessor.cs 文件，并将以下 `using` 语句添加到文件顶部。

    ```csharp
    using Microsoft.Azure.EventHubs;
    using Microsoft.Azure.EventHubs.Processor;
    using System.Threading.Tasks;
    ```

3. 实现 `IEventProcessor` 接口。 将 `SimpleEventProcessor` 类的全部内容替换为以下代码：

    ```csharp
    public class SimpleEventProcessor : IEventProcessor
    {
        public Task CloseAsync(PartitionContext context, CloseReason reason)
        {
            Console.WriteLine($"Processor Shutting Down. Partition '{context.PartitionId}', Reason: '{reason}'.");
            return Task.CompletedTask;
        }

        public Task OpenAsync(PartitionContext context)
        {
            Console.WriteLine($"SimpleEventProcessor initialized. Partition: '{context.PartitionId}'");
            return Task.CompletedTask;
        }

        public Task ProcessErrorAsync(PartitionContext context, Exception error)
        {
            Console.WriteLine($"Error on Partition: {context.PartitionId}, Error: {error.Message}");
            return Task.CompletedTask;
        }

        public Task ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
        {
            foreach (var eventData in messages)
            {
                var data = Encoding.UTF8.GetString(eventData.Body.Array, eventData.Body.Offset, eventData.Body.Count);
                Console.WriteLine($"Message received. Partition: '{context.PartitionId}', Data: '{data}'");
            }

            return context.CheckpointAsync();
        }
    }
    ```

## <a name="write-a-main-console-method-that-uses-the-simpleeventprocessor-class-to-receive-messages"></a>编写使用 SimpleEventProcessor 类接收消息的主控制台方法

1. 在 Program.cs 文件顶部添加以下 `using` 语句。

    ```csharp
    using Microsoft.Azure.EventHubs;
    using Microsoft.Azure.EventHubs.Processor;
    using System.Threading.Tasks;
    ```

2. 向 `Program` 类添加常量作为事件中心连接字符串、事件中心名称、存储帐户容器名称、存储帐户名称和存储帐户密钥。 添加以下代码，并将占位符替换为其对应的值。

    ```csharp
    private const string EhConnectionString = "{Event Hubs connection string}";
    private const string EhEntityPath = "{Event Hub path/name}";
    private const string StorageContainerName = "{Storage account container name}";
    private const string StorageAccountName = "{Storage account name}";
    private const string StorageAccountKey = "{Storage account key}";

    private static readonly string StorageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", StorageAccountName, StorageAccountKey);
    ```   

3. 将名为 `MainAsync` 的新方法添加到 `Program` 类，如下所示：

    ```csharp
    private static async Task MainAsync(string[] args)
    {
        Console.WriteLine("Registering EventProcessor...");

        var eventProcessorHost = new EventProcessorHost(
            EhEntityPath,
            PartitionReceiver.DefaultConsumerGroupName,
            EhConnectionString,
            StorageConnectionString,
            StorageContainerName);

        // Registers the Event Processor Host and starts receiving messages
        await eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>();

        Console.WriteLine("Receiving. Press ENTER to stop worker.");
        Console.ReadLine();

        // Disposes of the Event Processor Host
        await eventProcessorHost.UnregisterEventProcessorAsync();
    }
    ```

3. 在 `Main` 方法中添加以下代码行：

    ```csharp
    MainAsync(args).GetAwaiter().GetResult();
    ```

    Program.cs 文件的内容如下所示：

    ```csharp
    namespace SampleEphReceiver
    {

        public class Program
        {
            private const string EhConnectionString = "{Event Hubs connection string}";
            private const string EhEntityPath = "{Event Hub path/name}";
            private const string StorageContainerName = "{Storage account container name}";
            private const string StorageAccountName = "{Storage account name}";
            private const string StorageAccountKey = "{Storage account key}";

            private static readonly string StorageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", StorageAccountName, StorageAccountKey);

            public static void Main(string[] args)
            {
                MainAsync(args).GetAwaiter().GetResult();
            }

            private static async Task MainAsync(string[] args)
            {
                Console.WriteLine("Registering EventProcessor...");

                var eventProcessorHost = new EventProcessorHost(
                    EhEntityPath,
                    PartitionReceiver.DefaultConsumerGroupName,
                    EhConnectionString,
                    StorageConnectionString,
                    StorageContainerName);

                // Registers the Event Processor Host and starts receiving messages
                await eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>();

                Console.WriteLine("Receiving. Press ENTER to stop worker.");
                Console.ReadLine();

                // Disposes of the Event Processor Host
                await eventProcessorHost.UnregisterEventProcessorAsync();
            }
        }
    }
    ```

4. 运行程序，并确保没有任何错误。

祝贺你！ 现在已使用事件处理器主机从事件中心接收消息。

## <a name="next-steps"></a>后续步骤
访问以下链接可以了解有关事件中心的详细信息：

* [事件中心概述](event-hubs-what-is-event-hubs.md)
* [创建事件中心](event-hubs-create.md)
* [事件中心常见问题](event-hubs-faq.md)

[1]: ./media/event-hubs-dotnet-standard-getstarted-receive-eph/event-hubs-python1.png
[2]: ./media/event-hubs-dotnet-standard-getstarted-receive-eph/netcore.png
