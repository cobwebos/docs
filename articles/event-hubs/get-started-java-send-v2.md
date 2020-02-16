---
title: 使用 Java 向/从 Azure 事件中心发送/接收事件（最新版）
description: 本文演练如何创建一个可使用最新 azure-messaging-eventhubs 包向/从 Azure 事件中心发送/接收事件的 Java 应用程序。
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.date: 02/11/2020
ms.author: spelluru
ms.openlocfilehash: 4ebb52aa3e8d4ccfee6b36fb60c7f041df08a69a
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2020
ms.locfileid: "77163018"
---
# <a name="use-java-to-send-events-to-or-receive-events-from-azure-event-hubs-azure-messaging-eventhubs"></a>使用 Java 向/从 Azure 事件中心 (azure-messaging-eventhubs) 发送/接收事件
本快速入门介绍如何使用 **azure-messaging-eventhubs** Java 包向事件中心发送事件以及从事件中心接收事件。

> [!IMPORTANT]
> 本快速入门使用新的 **azure-messaging-eventhubs** 库。 有关使用旧的 **azure-eventhubs** 和 **azure-eventhubs-eph** 包的快速入门，请参阅[使用 azure-eventhubs 和 azure-eventhubs-eph 发送和接收事件](event-hubs-java-get-started-send.md)。 


## <a name="prerequisites"></a>必备条件
如果不熟悉 Azure 事件中心，请在阅读本快速入门之前参阅[事件中心概述](event-hubs-about.md)。 

若要完成本快速入门，需要具备以下先决条件：

- **Microsoft Azure 订阅**。 若要使用 Azure 服务（包括 Azure 事件中心），需要一个订阅。  如果没有现有的 Azure 帐户，可以注册[免费试用](https://azure.microsoft.com/free/)帐户，或者在[创建帐户](https://azure.microsoft.com)时使用 MSDN 订阅者权益。
- Java 开发环境。 本快速入门使用 [Eclipse](https://www.eclipse.org/)。 需要 Java 开发工具包 (JDK) 版本 8 或更高版本。 
- **创建事件中心命名空间和事件中心**。 第一步是使用 [Azure 门户](https://portal.azure.com)创建事件中心类型的命名空间，并获取应用程序与事件中心进行通信所需的管理凭据。 要创建命名空间和事件中心，请按照[此文](event-hubs-create.md)中的步骤操作。 然后，按照以下文章中的说明获取**事件中心命名空间的连接字符串**：[获取连接字符串](event-hubs-get-connection-string.md#get-connection-string-from-the-portal)。 稍后将在本快速入门中使用连接字符串。

## <a name="send-events"></a>发送事件 
本部分介绍如何创建一个向事件中心发送事件的 Java 应用程序。 

### <a name="add-reference-to-azure-event-hubs-library"></a>将引用添加到 Azure 事件中心库

事件中心的 Java 客户端库可用于[Maven 中央存储库](https://search.maven.org/search?q=a:azure-messaging-eventhubs)中的 Marven 项目。 可使用 Maven 项目文件中的以下依赖项声明引用此库：

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-messaging-eventhubs</artifactId>
    <version>5.0.1</version>
</dependency>
```

### <a name="write-code-to-send-messages-to-the-event-hub"></a>编写代码以将消息发送到事件中心

对于以下示例，请首先在你最喜欢的 Java 开发环境中为控制台/shell 应用程序创建一个新的 Maven 项目。 添加一个名为 `SimpleSend` 的类，并向该类中添加以下代码：

```java
import com.azure.messaging.eventhubs.*;
import static java.nio.charset.StandardCharsets.UTF_8;

public class Sender {
    public static void main(String[] args) {
    }
}
```

### <a name="connection-string-and-event-hub"></a>连接字符串和事件中心
此代码使用事件中心命名空间的连接字符串以及事件中心的名称来生成事件中心客户端。 

```java
String connectionString = "<CONNECTION STRING to EVENT HUBS NAMESPACE>";
String eventHubName = "<EVENT HUB NAME>";
```

### <a name="create-an-event-hubs-producer-client"></a>创建事件中心生成者客户端 
此代码创建一个生成者客户端对象，用于生成事件/向事件中心发送事件。 

```java
EventHubProducerClient producer = new EventHubClientBuilder()
    .connectionString(connectionString, eventHubName)
    .buildProducerClient();
```

### <a name="prepare-a-batch-of-events"></a>准备一批事件
此代码准备一批事件。 

```java
EventDataBatch batch = producer.createBatch();
batch.tryAdd(new EventData("First event"));
batch.tryAdd(new EventData("Second event"));
batch.tryAdd(new EventData("Third event"));
batch.tryAdd(new EventData("Fourth event"));
batch.tryAdd(new EventData("Fifth event"));
```

### <a name="send-the-batch-of-events-to-the-event-hub"></a>向事件中心发送事件批
此代码将上一步骤中准备的事件批发送到事件中心。 以下代码将在执行发送操作时阻塞。 

```java
producer.send(batch);
```

### <a name="close-and-cleanup"></a>关闭和清理
此代码关闭生成者。 

```java
producer.close();
```
### <a name="complete-code-to-send-events"></a>发送事件的完整代码
下面是将事件发送到事件中心的完整代码。 

```java
import com.azure.messaging.eventhubs.*;

public class Sender {
    public static void main(String[] args) {
        final String connectionString = "EVENT HUBS NAMESPACE CONNECTION STRING";
        final String eventHubName = "EVENT HUB NAME";

        // create a producer using the namespace connection string and event hub name
        EventHubProducerClient producer = new EventHubClientBuilder()
            .connectionString(connectionString, eventHubName)
            .buildProducerClient();

        // prepare a batch of events to send to the event hub    
        EventDataBatch batch = producer.createBatch();
        batch.tryAdd(new EventData("First event"));
        batch.tryAdd(new EventData("Second event"));
        batch.tryAdd(new EventData("Third event"));
        batch.tryAdd(new EventData("Fourth event"));
        batch.tryAdd(new EventData("Fifth event"));

        // send the batch of events to the event hub
        producer.send(batch);

        // close the producer
        producer.close();
    }
}
```

生成程序，并确保没有引发任何错误。 将在运行接收器程序后运行此程序。 

## <a name="receive-events"></a>接收事件
本教程中的代码基于 [GitHub 上的 EventProcessorClient 示例](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs/EventProcessorClientSample.java)，可检查该代码以查看完整的工作应用程序。

### <a name="create-a-java-project"></a>创建一个 Java 项目

事件中心的 Java 客户端库可用于 [Maven 中央存储库](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22)中的 Maven 项目，并且可以使用 Maven 项目文件中的以下依赖项声明进行引用： 

```xml
<dependencies>
    <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-messaging-eventhubs</artifactId>
        <version>5.0.1</version>
    </dependency>
</dependencies>
```

1. 使用以下代码创建名为 `Receiver`的新类。 将占位符替换为创建事件中心和存储帐户时所使用的值：
   
   ```java
     import com.azure.messaging.eventhubs.*;
     import com.azure.messaging.eventhubs.models.ErrorContext;
     import com.azure.messaging.eventhubs.models.EventContext;
     import java.util.concurrent.TimeUnit;
     import java.util.function.Consumer;
    
     public class Receiver {
    
         final static String connectionString = "<EVENT HUBS NAMESPACE - CONNECTION STRING>";
         final static String eventHubName = "<EVENT HUB NAME>";
         
         public static void main(String[] args) throws Exception {
    
             // function to process events
             Consumer<EventContext> processEvent = eventContext  -> {
                 System.out.print("Received event: ");
                 // print the body of the event
                 System.out.println(eventContext.getEventData().getBodyAsString());
                 eventContext.updateCheckpoint();
             };
    
             // function to process errors
             Consumer<ErrorContext> processError = errorContext -> {
                 // print the error message
                 System.out.println(errorContext.getThrowable().getMessage());
             };
    
            
             EventProcessorClient eventProcessorClient = new EventProcessorClientBuilder()
                     .connectionString(connectionString, eventHubName)
                     .processEvent(processEvent)
                     .processError(processError)
                     .consumerGroup(EventHubClientBuilder.DEFAULT_CONSUMER_GROUP_NAME)
                     .checkpointStore(new InMemoryCheckpointStore())
                     .buildEventProcessorClient();
    
             System.out.println("Starting event processor");
             eventProcessorClient.start();
    
             System.out.println("Press enter to stop.");
             System.in.read();
    
             System.out.println("Stopping event processor");
             eventProcessorClient.stop();
             System.out.println("Event processor stopped.");
    
             System.out.println("Exiting process");
         }
     }
    ```
    
2. 从 [GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs) 下载 **InMemoryCheckpointStore.java** 文件，并将其添加到项目。 
3. 生成程序，并确保没有引发任何错误。 

## <a name="run-the-applications"></a>运行应用程序
1. 先运行**接收器**应用程序。
1. 然后运行**发送器**应用程序。 
1. 在**接收器**应用程序窗口中，确认已看到发送器应用程序发布的事件。
1. 在接收器应用程序窗口中按 **ENTER** 停止该应用程序。 

## <a name="next-steps"></a>后续步骤
查看 [GitHub 上的 Java SDK 示例](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs)

