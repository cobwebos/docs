---
title: 使用 Java 从 Azure 事件中心接收事件 | Microsoft 文档
description: 使用 Java 从事件中心接收事件入门
services: event-hubs
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 08/26/2018
ms.author: shvija
ms.openlocfilehash: dce7c4067ba6d96bf14f4e3300d951b594afe930
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2018
ms.locfileid: "50240626"
---
# <a name="receive-events-from-azure-event-hubs-using-java"></a>使用 Java 从 Azure 事件中心接收事件

事件中心是一个具备高度伸缩性的引入系统，每秒可收入大量事件，从而使应用程序能够处理和分析连接的设备和应用程序所产生的海量数据。 将数据采集到事件中心后，可以使用任何实时分析提供程序或存储群集来转换和存储数据。

有关详细信息，请参阅[事件中心概述][Event Hubs overview]。

本教程演示如何使用用 Java 编写的控制台应用程序从事件中心接收事件。

## <a name="prerequisites"></a>先决条件

若要完成本教程，需要具备以下先决条件：

* Java 开发环境。 对于本教程，我们会采用 [Eclipse](https://www.eclipse.org/)。
* 有效的 Azure 帐户。 如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户][]。

本教程中的代码基于 [GitHub 上的 EventProcessorSample 代码](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java/Basic/EventProcessorSample)，可检查该代码以查看完整的工作应用程序。

## <a name="receive-messages-with-eventprocessorhost-in-java"></a>使用 Java 中的 EventProcessorHost 接收消息

EventProcessorHost 是一个 Java 类，通过在这些事件中心管理持久检查点和并行接收来简化从事件中心接收事件的过程。 使用 EventProcessorHost，可跨多个接收方拆分事件，即使在不同节点中托管也是如此。 此示例演示如何为单一接收方使用 EventProcessorHost。

### <a name="create-a-storage-account"></a>创建存储帐户

若要使用 EventProcessorHost，必须有一个 [Azure 存储帐户][Azure Storage account]：

1. 登录到 [Azure 门户][Azure portal]，然后单击屏幕左侧的“+创建资源”。
2. 依次“存储”、“存储帐户”。 在“创建存储帐户”窗口中，键入存储帐户的名称。 填写其余字段，选择所需区域，然后单击“创建”。
   
    ![](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-storage2.png)

3. 单击新创建的存储帐户，并单击“访问密钥”：
   
    ![](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-storage3.png)

    将 key1 值复制到临时位置。 本教程后面部分需要使用它。

### <a name="create-a-java-project-using-the-eventprocessor-host"></a>EventProcessor Host 创建一个 Java 项目

事件中心的 Java 客户端库可用于 [Maven 中央存储库][Maven Package]中的 Maven 项目，并且可以使用 Maven 项目文件中的以下依赖项声明进行引用。 项目 azure-eventhubs-eph 的当前版本为 2.0.1，项目 azure-eventhubs 的当前版本为 1.0.2：    

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs</artifactId>
    <version>1.0.2</version>
</dependency>
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs-eph</artifactId>
    <version>2.0.1</version>
</dependency>
```

对于不同类型的生成环境，可以从 [Maven 中央存储库][Maven Package]显式获取最新发布的 JAR 文件。  

1. 对于以下示例，请首先在你最喜欢的 Java 开发环境中为控制台/shell 应用程序创建一个新的 Maven 项目。 此类名为 `ErrorNotificationHandler`。     
   
    ```java
    import java.util.function.Consumer;
    import com.microsoft.azure.eventprocessorhost.ExceptionReceivedEventArgs;
   
    public class ErrorNotificationHandler implements Consumer<ExceptionReceivedEventArgs>
    {
        @Override
        public void accept(ExceptionReceivedEventArgs t)
        {
            System.out.println("SAMPLE: Host " + t.getHostname() + " received general error notification during " + t.getAction() + ": " + t.getException().toString());
        }
    }
    ```
2. 使用以下代码创建名为 `EventProcessorSample`的新类。 将占位符替换为创建事件中心和存储帐户时所使用的值：
   
   ```java
   package com.microsoft.azure.eventhubs.samples.eventprocessorsample;

   import com.microsoft.azure.eventhubs.ConnectionStringBuilder;
   import com.microsoft.azure.eventhubs.EventData;
   import com.microsoft.azure.eventprocessorhost.CloseReason;
   import com.microsoft.azure.eventprocessorhost.EventProcessorHost;
   import com.microsoft.azure.eventprocessorhost.EventProcessorOptions;
   import com.microsoft.azure.eventprocessorhost.ExceptionReceivedEventArgs;
   import com.microsoft.azure.eventprocessorhost.IEventProcessor;
   import com.microsoft.azure.eventprocessorhost.PartitionContext;

   import java.util.concurrent.ExecutionException;
   import java.util.function.Consumer;

   public class EventProcessorSample
   {
       public static void main(String args[]) throws InterruptedException, ExecutionException
       {
           String consumerGroupName = "$Default";
           String namespaceName = "----NamespaceName----";
           String eventHubName = "----EventHubName----";
           String sasKeyName = "----SharedAccessSignatureKeyName----";
           String sasKey = "----SharedAccessSignatureKey----";
           String storageConnectionString = "----AzureStorageConnectionString----";
           String storageContainerName = "----StorageContainerName----";
           String hostNamePrefix = "----HostNamePrefix----";
        
           ConnectionStringBuilder eventHubConnectionString = new ConnectionStringBuilder()
                .setNamespaceName(namespaceName)
                .setEventHubName(eventHubName)
                .setSasKeyName(sasKeyName)
                .setSasKey(sasKey);
        
           EventProcessorHost host = new EventProcessorHost(
                EventProcessorHost.createHostName(hostNamePrefix),
                eventHubName,
                consumerGroupName,
                eventHubConnectionString.toString(),
                storageConnectionString,
                storageContainerName);
        
           System.out.println("Registering host named " + host.getHostName());
           EventProcessorOptions options = new EventProcessorOptions();
           options.setExceptionNotification(new ErrorNotificationHandler());

           host.registerEventProcessor(EventProcessor.class, options)
           .whenComplete((unused, e) ->
           {
               if (e != null)
               {
                   System.out.println("Failure while registering: " + e.toString());
                   if (e.getCause() != null)
                   {
                       System.out.println("Inner exception: " + e.getCause().toString());
                   }
               }
           })
           .thenAccept((unused) ->
           {
               System.out.println("Press enter to stop.");
               try 
               {
                   System.in.read();
               }
               catch (Exception e)
               {
                   System.out.println("Keyboard read failed: " + e.toString());
               }
           })
           .thenCompose((unused) ->
           {
               return host.unregisterEventProcessor();
           })
           .exceptionally((e) ->
           {
               System.out.println("Failure while unregistering: " + e.toString());
               if (e.getCause() != null)
               {
                   System.out.println("Inner exception: " + e.getCause().toString());
               }
               return null;
           })
           .get(); // Wait for everything to finish before exiting main!
        
           System.out.println("End of sample");
       }
    ```
3. 使用以下代码另外创建一个名为 `EventProcessor` 的类：
   
    ```java
    public static class EventProcessor implements IEventProcessor
    {
        private int checkpointBatchingCount = 0;

        // OnOpen is called when a new event processor instance is created by the host. 
        @Override
        public void onOpen(PartitionContext context) throws Exception
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " is opening");
        }

        // OnClose is called when an event processor instance is being shut down. 
        @Override
        public void onClose(PartitionContext context, CloseReason reason) throws Exception
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " is closing for reason " + reason.toString());
        }
        
        // onError is called when an error occurs in EventProcessorHost code that is tied to this partition, such as a receiver failure.
        @Override
        public void onError(PartitionContext context, Throwable error)
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " onError: " + error.toString());
        }

        // onEvents is called when events are received on this partition of the Event Hub. 
        @Override
        public void onEvents(PartitionContext context, Iterable<EventData> events) throws Exception
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " got event batch");
            int eventCount = 0;
            for (EventData data : events)
            {
                try
                {
                    System.out.println("SAMPLE (" + context.getPartitionId() + "," + data.getSystemProperties().getOffset() + "," +
                            data.getSystemProperties().getSequenceNumber() + "): " + new String(data.getBytes(), "UTF8"));
                    eventCount++;
                    
                    // Checkpointing persists the current position in the event stream for this partition and means that the next
                    // time any host opens an event processor on this event hub+consumer group+partition combination, it will start
                    // receiving at the event after this one. 
                    this.checkpointBatchingCount++;
                    if ((checkpointBatchingCount % 5) == 0)
                    {
                        System.out.println("SAMPLE: Partition " + context.getPartitionId() + " checkpointing at " +
                            data.getSystemProperties().getOffset() + "," + data.getSystemProperties().getSequenceNumber());
                        // Checkpoints are created asynchronously. It is important to wait for the result of checkpointing
                        // before exiting onEvents or before creating the next checkpoint, to detect errors and to ensure proper ordering.
                        context.checkpoint(data).get();
                    }
                }
                catch (Exception e)
                {
                    System.out.println("Processing failed for an event: " + e.toString());
                }
            }
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " batch size was " + eventCount + " for host " + context.getOwner());
        }
    }
    ```

本教程使用了一个 EventProcessorHost 实例。 若要增加吞吐量，建议运行多个 EventProcessorHost 实例，最好是在单独的计算机上运行。  这也会提供冗余。 在那些情况下，为了对接收到的事件进行负载均衡，各个不同实例会自动相互协调。 如果希望多个接收方都各自处理 *全部* 事件，则必须使用 **ConsumerGroup** 概念。 在从不同计算机中接收事件时，根据部署 EventProcessorHost 实例的计算机（或角色）来指定该实例的名称可能会很有用。

## <a name="publishing-messages-to-eventhub"></a>将消息发布到 EventHub

在使用者检索消息之前，必须先由发布者将消息发布到分区。 值得注意的是，当使用 com.microsoft.azure.eventhubs.EventHubClient 对象上的 sendSync() 方法同步将消息发布到事件中心时，可以将消息发送到特定分区或以循环方式分发到所有可用分区，具体取决于 是否指定了分区键。

如果指定了表示分区键的字符串，则将对该键进行哈希处理以确定事件将发送到的分区。

如果未设置分区键，则消息将循环分发到所有可用分区

```java
// Serialize the event into bytes
byte[] payloadBytes = gson.toJson(messagePayload).getBytes(Charset.defaultCharset());

// Use the bytes to construct an {@link EventData} object
EventData sendEvent = EventData.create(payloadBytes);

// Transmits the event to event hub without a partition key
// If a partition key is not set, then we will round-robin to all topic partitions
eventHubClient.sendSync(sendEvent);

//  the partitionKey will be hash'ed to determine the partitionId to send the eventData to.
eventHubClient.sendSync(sendEvent, partitionKey);

```

## <a name="implementing-a-custom-checkpointmanager-for-eventprocessorhost-eph"></a>为 EventProcessorHost (EPH) 实现自定义 CheckpointManager

对于默认实现与你的用例不兼容的情况，此 API 提供了一种机制来实现自定义检查点管理器。

默认检查点管理器使用 blob 存储，但是，如果你使用自己的实现替代了 EPH 使用的检查点管理器，则可以使用所需的任何存储来为你的检查点管理器实现提供支持。

创建一个类，用于实现 com.microsoft.azure.eventprocessorhost.ICheckpointManager

使用检查点管理器的自定义实现 (com.microsoft.azure.eventprocessorhost.ICheckpointManager)

在你的实现中，你可以替代默认检查点机制并根据你自己的数据存储（SQL Server、CosmosDB、Redis 缓存，等等）实现我们自己的检查点。 对于用于为检查点管理器实现提供支持的存储，建议使其可供为使用者组处理事件的所有 EPH 实例访问。

你可以使用你的环境中提供的任何数据存储。

com.microsoft.azure.eventprocessorhost.EventProcessorHost 类提供了 2 个构造函数，可以使用它们来替代你的 EventProcessorHost 的检查点管理器。

## <a name="next-steps"></a>后续步骤
在本快速入门中，你已创建从事件中心接收消息的 Java 应用程序。 要了解如何使用 Java 将事件发送到事件中心，请参阅[从事件中心发送事件 - Java](event-hubs-java-get-started-send.md)。

<!-- Links -->
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
[Azure Storage account]: ../storage/common/storage-create-storage-account.md
[Azure portal]: https://portal.azure.com
[Maven Package]: https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22

<!-- Images -->
[11]: ./media/service-bus-event-hubs-get-started-receive-ephjava/create-eph-csharp2.png
[12]: ./media/service-bus-event-hubs-get-started-receive-ephjava/create-eph-csharp3.png
[免费帐户]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
