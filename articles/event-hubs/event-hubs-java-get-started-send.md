---
title: "使用 Java 将事件发送到 Azure 事件中心 | Microsoft 文档"
description: "使用 Java 发送到事件中心入门"
services: event-hubs
documentationcenter: 
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.workload: core
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2017
ms.author: sethm
ms.translationtype: HT
ms.sourcegitcommit: 1e6fb68d239ee3a66899f520a91702419461c02b
ms.openlocfilehash: b31771001989e20b88bc8d7bca1afceb58ec197c
ms.contentlocale: zh-cn
ms.lasthandoff: 08/16/2017

---

# <a name="send-events-to-azure-event-hubs-using-java"></a>使用 Java 将事件发送到 Azure 事件中心

## <a name="introduction"></a>介绍
事件中心是一个具备高度伸缩性的引入系统，每秒可收入大量事件，从而使应用程序能够处理和分析连接的设备和应用程序所产生的海量数据。 将数据采集到事件中心后，可以使用任何实时分析提供程序或存储群集来转换和存储数据。

有关详细信息，请参阅[事件中心概述][Event Hubs overview]。

本教程演示如何使用用 Java 编写的控制台应用程序将事件发送到事件中心。 若要使用 Java 事件处理器主机库接收事件，请参阅[本文](event-hubs-java-get-started-receive-eph.md)，或单击左侧目录中的相应接收语言。

要完成本教程，需要以下各项：

* Java 开发环境。 对于本教程，我们会采用 [Eclipse](https://www.eclipse.org/)。
* 有效的 Azure 帐户。 <br/>如果没有帐户，只需花费几分钟就能创建一个免费帐户。 有关详细信息，请参阅 <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Azure 免费试用</a>。

## <a name="send-messages-to-event-hubs"></a>将消息发送到事件中心
事件中心的 Java 客户端库可用于[Maven 中央存储库](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22)中的 Marven 项目。 可使用 Maven 项目文件中的以下依赖项声明引用此库：    

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs</artifactId>
    <version>{VERSION}</version>
</dependency>
```

对于不同类型的生成环境，可以从 [Maven 中央存储库](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22)显式获取最新发布的 JAR 文件。  

对于简单的事件发布服务器，请导入事件中心客户端类的 *com.microsoft.azure.eventhubs* 包和实用程序类（如与 Azure 服务总线消息传递客户端共享的常见异常）的 *com.microsoft.azure.servicebus* 包。 

对于以下示例，请首先在你最喜欢的 Java 开发环境中为控制台/shell 应用程序创建一个新的 Maven 项目。 命名类 `Send`。     

```java
import java.io.IOException;
import java.nio.charset.*;
import java.util.*;
import java.util.concurrent.ExecutionException;

import com.microsoft.azure.eventhubs.*;
import com.microsoft.azure.servicebus.*;

public class Send
{
    public static void main(String[] args) 
            throws ServiceBusException, ExecutionException, InterruptedException, IOException
    {
```

将命名空间和事件中心名称替换为创建事件中心时使用的值。

```java
    final String namespaceName = "----ServiceBusNamespaceName-----";
    final String eventHubName = "----EventHubName-----";
    final String sasKeyName = "-----SharedAccessSignatureKeyName-----";
    final String sasKey = "---SharedAccessSignatureKey----";
    ConnectionStringBuilder connStr = new ConnectionStringBuilder(namespaceName, eventHubName, sasKeyName, sasKey);
```

然后，通过将字符串转换为其 UTF-8 字节编码创建单一事件。 再使用连接字符串创建一个新的事件中心客户端实例并发送该消息。   

```java 

    byte[] payloadBytes = "Test AMQP message from JMS".getBytes("UTF-8");
    EventData sendEvent = new EventData(payloadBytes);

    EventHubClient ehClient = EventHubClient.createFromConnectionStringSync(connStr.toString());
    ehClient.sendSync(sendEvent);
    }
}

``` 

## <a name="next-steps"></a>后续步骤
访问以下链接可以了解有关事件中心的详细信息：

* [使用 EventProcessorHost 接收事件](event-hubs-java-get-started-receive-eph.md)
* [事件中心概述][Event Hubs overview]
* [创建事件中心](event-hubs-create.md)
* [事件中心常见问题](event-hubs-faq.md)

<!-- Links -->
[Event Hubs overview]: event-hubs-overview.md
