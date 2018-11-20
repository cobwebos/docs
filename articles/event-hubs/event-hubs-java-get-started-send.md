---
title: 使用 Java 将事件发送到 Azure 事件中心 | Microsoft 文档
description: 使用 Java 发送到事件中心入门
services: event-hubs
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 11/12/2018
ms.author: shvija
ms.openlocfilehash: 510f1a2bc23d14e1bb9e8e561b52936ae9d53685
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/14/2018
ms.locfileid: "51624533"
---
# <a name="send-events-to-azure-event-hubs-using-java"></a>使用 Java 将事件发送到 Azure 事件中心

Azure 事件中心是一个大数据流式处理平台和事件引入服务，每秒能够接收和处理数百万个事件。 事件中心可以处理和存储分布式软件和设备生成的事件、数据或遥测。 可以使用任何实时分析提供程序或批处理/存储适配器转换和存储发送到数据中心的数据。 有关事件中心的详细概述，请参阅[事件中心概述](event-hubs-about.md)和[事件中心功能](event-hubs-features.md)。

本教程演示如何使用用 Java 编写的控制台应用程序将事件发送到事件中心。 

> [!NOTE]
> 可以从 [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java/Basic/SimpleSend) 下载此用作示例的快速入门，将 `EventHubConnectionString` 和 `EventHubName` 字符串替换为事件中心值，并运行它。 或者，可以按照本教程中的步骤创建自己的解决方案。

## <a name="prerequisites"></a>先决条件

若要完成本教程，需要具备以下先决条件：

* Java 开发环境。 本教程使用 [Eclipse](https://www.eclipse.org/)。

## <a name="create-an-event-hubs-namespace-and-an-event-hub"></a>创建事件中心命名空间和事件中心
第一步是使用 [Azure 门户](https://portal.azure.com)创建事件中心类型的命名空间，并获取应用程序与事件中心进行通信所需的管理凭据。 若要创建命名空间和事件中心，请按照[本文](event-hubs-create.md)中的步骤进行操作，然后继续执行本教程的以下步骤。

## <a name="add-reference-to-azure-event-hubs-library"></a>将引用添加到 Azure 事件中心库

事件中心的 Java 客户端库可用于[Maven 中央存储库](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22)中的 Marven 项目。 可在 Maven 项目文件中使用以下依赖项声明引用此库。 当前版本为 1.0.2：    

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs</artifactId>
    <version>1.0.2</version>
</dependency>
```

对于不同类型的生成环境，可以从 [Maven 中央存储库](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22)显式获取最新发布的 JAR 文件。  

对于简单的事件发布服务器，请导入事件中心客户端类的 *com.microsoft.azure.eventhubs* 包和实用程序类（如与 Azure 服务总线消息传递客户端共享的常见异常）的 *com.microsoft.azure.servicebus* 包。 

## <a name="write-code-to-send-messages-to-the-event-hub"></a>编写代码以将消息发送到事件中心

对于以下示例，请首先在你最喜欢的 Java 开发环境中为控制台/shell 应用程序创建一个新的 Maven 项目。 将类 `SimpleSend` 命名为：     

```java
package com.microsoft.azure.eventhubs.samples.send;

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
import java.util.concurrent.ExecutorService;

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
        .setNamespaceName("Your Event Hubs namespace name")
        .setEventHubName("Your event hub")
        .setSasKeyName("Your policy name")
        .setSasKey("Your primary SAS key");
```

### <a name="send-events"></a>发送事件

通过将字符串转换为其 UTF-8 字节编码创建单一事件。 然后，使用连接字符串创建一个新的事件中心客户端实例并发送该消息：   

```java 
String payload = "Message " + Integer.toString(i);
byte[] payloadBytes = gson.toJson(payload).getBytes(Charset.defaultCharset());
EventData sendEvent = EventData.create(payloadBytes);

final EventHubClient ehClient = EventHubClient.createSync(connStr.toString(), executorService);
ehClient.sendSync(sendEvent);
    
// close the client at the end of your program
ehClient.closeSync();

``` 

生成并运行程序，并确保没有引发任何错误。

祝贺你！ 现在已向事件中心发送消息。

### <a name="appendix-how-messages-are-routed-to-eventhub-partitions"></a>附录：如何将消息路由到 EventHub 分区

在使用者检索消息之前，必须先由发布者将消息发布到分区。 当使用 com.microsoft.azure.eventhubs.EventHubClient 对象上的 sendSync() 方法同步将消息发布到事件中心时，可以将消息发送到特定分区或以循环方式分发到所有可用分区，具体取决于 是否指定了分区键。

指定了表示分区键的字符串时，将对该键进行哈希处理以确定将事件发送到哪个分区。

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

## <a name="next-steps"></a>后续步骤

在此快速入门中，已使用 Java 向事件中心发送消息。 若要了解如何使用 Java 从事件中心接收事件，请参阅[从事件中心接收事件 - Java](event-hubs-java-get-started-receive-eph.md)。

<!-- Links -->
[Event Hubs overview]: event-hubs-overview.md
[free account]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio

