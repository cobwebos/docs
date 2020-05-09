---
title: 使用 Java（旧版）向/从 Azure 事件中心发送/接收事件
description: 本文提供了创建 Java 应用程序的演练，该应用程序使用旧的 azure-eventhubs 包向/从 Azure 事件中心发送/接收事件。
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.date: 02/11/2020
ms.author: spelluru
ms.openlocfilehash: 379739533e15e60bc47bfc883a67037d4a58d0e0
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "81417626"
---
# <a name="use-java-to-send-events-to-or-receive-events-from-azure-event-hubs-azure-eventhubs"></a>使用 Java 向/从 Azure 事件中心 (azure-eventhubs) 发送/接收事件

本快速入门介绍如何使用 **azure-eventhubs** Java 包向事件中心发送事件以及从事件中心接收事件。

> [!WARNING]
> 本快速入门使用旧的 **azure-eventhubs** 和 **azure-eventhubs-eph** 包。 有关使用最新 **azure-messaging-eventhubs** 包的快速入门，请参阅[使用 azure-messaging-eventhubs 发送和接收事件](get-started-java-send-v2.md)。 若要将应用程序从使用旧包迁移到使用新包，请参阅[从 azure-eventhubs 迁移到 azure-messaging-eventhubs 的指南](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs/migration-guide.md)。 


## <a name="prerequisites"></a>先决条件

如果不熟悉 Azure 事件中心，请在阅读本快速入门之前参阅[事件中心概述](event-hubs-about.md)。 

若要完成本快速入门，需要具备以下先决条件：

- **Microsoft Azure 订阅**。 若要使用 Azure 服务（包括 Azure 事件中心），需要一个订阅。  如果没有现有的 Azure 帐户，可以注册[免费试用](https://azure.microsoft.com/free/)帐户，或者在[创建帐户](https://azure.microsoft.com)时使用 MSDN 订阅者权益。
- Java 开发环境。 本快速入门使用 [Eclipse](https://www.eclipse.org/)。
- **创建事件中心命名空间和事件中心**。 第一步是使用 [Azure 门户](https://portal.azure.com)创建事件中心类型的命名空间，并获取应用程序与事件中心进行通信所需的管理凭据。 要创建命名空间和事件中心，请按照[此文](event-hubs-create.md)中的步骤操作。 然后，按照以下文章中的说明获取事件中心访问密钥的值：[获取连接字符串](event-hubs-get-connection-string.md#get-connection-string-from-the-portal)。 你将在本快速入门中稍后编写的代码中使用访问密钥。 默认密钥名称为：RootManageSharedAccessKey  。

## <a name="send-events"></a>发送事件 
本部分介绍如何创建一个向事件中心发送事件的 Java 应用程序。 

> [!NOTE]
> 可以从 [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java/Basic/SimpleSend) 下载此用作示例的快速入门，将 `EventHubConnectionString` 和 `EventHubName` 字符串替换为事件中心值，并运行它。 或者，也可以按照本快速入门中的步骤创建自己的应用程序。

### <a name="add-reference-to-azure-event-hubs-library"></a>将引用添加到 Azure 事件中心库

事件中心的 Java 客户端库可用于[Maven 中央存储库](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22)中的 Marven 项目。 可使用 Maven 项目文件中的以下依赖项声明引用此库：

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs</artifactId>
    <version>2.2.0</version>
</dependency>
```

对于不同类型的生成环境，可以从 [Maven 中央存储库](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22)显式获取最新发布的 JAR 文件。  

对于简单的事件发布服务器，请导入事件中心客户端类的 *com.microsoft.azure.eventhubs* 包和实用程序类（如与 Azure 服务总线消息传递客户端共享的常见异常）的 *com.microsoft.azure.servicebus* 包。 

### <a name="write-code-to-send-messages-to-the-event-hub"></a>编写代码以将消息发送到事件中心

对于以下示例，请首先在你最喜欢的 Java 开发环境中为控制台/shell 应用程序创建一个新的 Maven 项目。 添加一个名为 `SimpleSend` 的类，并向该类中添加以下代码：

```java
import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.microsoft.azure.eventhubs.ConnectionStringBuilder;
import com.microsoft.azure.eventhubs.EventData;
import com.microsoft.azure.eventhubs.EventHubClient;
import com.microsoft.azure.eventhubs.EventHubException;

import java.io.IOException;
import java.nio.charset.Charset;
import java.time.Instant;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.Executors;
import java.util.concurrent.ScheduledExecutorService;

public class SimpleSend {

    public static void main(String[] args)
            throws EventHubException, ExecutionException, InterruptedException, IOException {
            
            
    }
 }
```

### <a name="construct-connection-string"></a>构造连接字符串

使用 ConnectionStringBuilder 类构造要传递到事件中心客户端实例的连接字符串值。 将占位符替换为创建命名空间和事件中心时获取的值：

```java
        final ConnectionStringBuilder connStr = new ConnectionStringBuilder()
                .setNamespaceName("<EVENTHUB NAMESPACE") 
                .setEventHubName("EVENT HUB")
                .setSasKeyName("RootManageSharedAccessKey")
                .setSasKey("SHARED ACCESS KEY");
```

### <a name="write-code-to-send-events"></a>编写代码以发送事件

通过将字符串转换为其 UTF-8 字节编码创建单一事件。 然后，使用连接字符串创建一个新的事件中心客户端实例并发送该消息：   

```java 
        final Gson gson = new GsonBuilder().create();

        // The Executor handles all asynchronous tasks and this is passed to the EventHubClient instance.
        // This enables the user to segregate their thread pool based on the work load.
        // This pool can then be shared across multiple EventHubClient instances.
        // The following sample uses a single thread executor, as there is only one EventHubClient instance,
        // handling different flavors of ingestion to Event Hubs here.
        final ScheduledExecutorService executorService = Executors.newScheduledThreadPool(4);

        // Each EventHubClient instance spins up a new TCP/TLS connection, which is expensive.
        // It is always a best practice to reuse these instances. The following sample shows this.
        final EventHubClient ehClient = EventHubClient.createSync(connStr.toString(), executorService);


        try {
            for (int i = 0; i < 10; i++) {

                String payload = "Message " + Integer.toString(i);
                byte[] payloadBytes = gson.toJson(payload).getBytes(Charset.defaultCharset());
                EventData sendEvent = EventData.create(payloadBytes);

                // Send - not tied to any partition
                // Event Hubs service will round-robin the events across all Event Hubs partitions.
                // This is the recommended & most reliable way to send to Event Hubs.
                ehClient.sendSync(sendEvent);
            }

            System.out.println(Instant.now() + ": Send Complete...");
            System.out.println("Press Enter to stop.");
            System.in.read();
        } finally {
            ehClient.closeSync();
            executorService.shutdown();
        }

``` 

生成并运行程序，并确保没有引发任何错误。

祝贺你！ 现在已向事件中心发送消息。

### <a name="appendix-how-messages-are-routed-to-eventhub-partitions"></a>附录：如何将消息路由到 EventHub 分区

在使用者检索消息之前，必须先由发布者将消息发布到分区。 当使用 com.microsoft.azure.eventhubs.EventHubClient 对象上的 sendSync() 方法同步将消息发布到事件中心时，可以将消息发送到特定分区或以循环方式分发到所有可用分区，具体取决于 是否指定了分区键。

当指定了表示分区键的字符串时，将对该键进行哈希处理以确定要将事件发送到哪个分区。

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

// close the client at the end of your program
eventHubClient.closeSync();

```

## <a name="receive-events"></a>接收事件
本教程中的代码基于 [GitHub 上的 EventProcessorSample 代码](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java/Basic/EventProcessorSample)，可检查该代码以查看完整的工作应用程序。

### <a name="receive-messages-with-eventprocessorhost-in-java"></a>使用 Java 中的 EventProcessorHost 接收消息

EventProcessorHost  是一个 Java 类，通过在这些事件中心管理持久检查点和并行接收来简化从事件中心接收事件的过程。 使用 EventProcessorHost，可跨多个接收方拆分事件，即使在不同节点中托管也是如此。 此示例演示如何为单一接收方使用 EventProcessorHost。

### <a name="create-a-storage-account"></a>创建存储帐户

若要使用 EventProcessorHost，必须拥有 [Azure 存储帐户][Azure 存储帐户]：

1. 登录到 [Azure 门户](https://portal.azure.com)，选择屏幕左侧的“创建资源”。 
2. 选择“存储”，然后选择“存储帐户”   。 在“创建存储帐户”  窗口中，键入存储帐户的名称。 填写其余字段，选择所需区域，然后选择“创建”  。
   
    ![在 Azure 门户中创建存储帐户](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-azure-storage-account.png)

3. 选择新创建的存储帐户，然后选择“访问密钥”  ：
   
    ![在 Azure 门户中获取访问密钥](./media/event-hubs-dotnet-framework-getstarted-receive-eph/select-azure-storage-access-keys.png)

    将 key1 值复制到临时位置。 本教程后面部分需要使用它。

### <a name="create-a-java-project-using-the-eventprocessor-host"></a>EventProcessor Host 创建一个 Java 项目

事件中心的 Java 客户端库可用于 [Maven 中央存储库](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22)中的 Maven 项目，并且可以使用 Maven 项目文件中的以下依赖项声明进行引用： 

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs</artifactId>
    <version>2.2.0</version>
</dependency>
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs-eph</artifactId>
    <version>2.4.0</version>
</dependency>
```

对于不同类型的生成环境，可以从 [Maven 中央存储库](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22)显式获取最新发布的 JAR 文件。

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

### <a name="publishing-messages-to-eventhub"></a>将消息发布到 EventHub

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

### <a name="implementing-a-custom-checkpointmanager-for-eventprocessorhost-eph"></a>为 EventProcessorHost (EPH) 实现自定义 CheckpointManager

对于默认实现与你的用例不兼容的情况，此 API 提供了一种机制来实现自定义检查点管理器。

默认检查点管理器使用 blob 存储，但是，如果你使用自己的实现替代了 EPH 使用的检查点管理器，则可以使用所需的任何存储来为你的检查点管理器实现提供支持。

创建一个类，用于实现 com.microsoft.azure.eventprocessorhost.ICheckpointManager

使用检查点管理器的自定义实现 (com.microsoft.azure.eventprocessorhost.ICheckpointManager)

在实现中，你可以重写默认检查点机制并根据自己的数据存储（例如 SQL Server、CosmosDB 和 Azure Cache for Redis）实现我们自己的检查点。 对于用于为检查点管理器实现提供支持的存储，建议使其可供为使用者组处理事件的所有 EPH 实例访问。

你可以使用你的环境中提供的任何数据存储。

com.microsoft.azure.eventprocessorhost.EventProcessorHost 类提供了 2 个构造函数，可以使用它们来替代你的 EventProcessorHost 的检查点管理器。


## <a name="next-steps"></a>后续步骤
请阅读以下文章： 

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Azure 事件中心的功能和术语](event-hubs-features.md)
- [事件中心常见问题解答](event-hubs-faq.md)

