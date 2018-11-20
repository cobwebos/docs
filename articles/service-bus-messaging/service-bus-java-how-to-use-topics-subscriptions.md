---
title: 如何通过 Java 使用 Azure 服务总线主题 | Microsoft Docs
description: 在 Azure 中使用服务总线主题和订阅。
services: service-bus-messaging
documentationcenter: java
author: spelluru
manager: timlt
editor: ''
ms.assetid: 63d6c8bd-8a22-4292-befc-545ffb52e8eb
ms.service: service-bus-messaging
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 09/17/2018
ms.author: spelluru
ms.openlocfilehash: 501d15ebbb373c100dd735e97bebf2f085a9579e
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2018
ms.locfileid: "50241304"
---
# <a name="how-to-use-service-bus-topics-and-subscriptions-with-java"></a>如何通过 Java 使用服务总线主题和订阅

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

在本快速入门中，你将执行以下步骤： 

- 使用 Azure 门户创建一个主题
- 使用 Azure 门户中创建该主题的三个订阅
- 编写 Java 代码来向该主题发送消息
- 编写 Java 代码来从订阅接收消息

## <a name="prerequisites"></a>先决条件

- Azure 订阅。 如果没有订阅，请在开始之前[创建一个免费帐户](https://azure.microsoft.com/free)。
- [Azure SDK for Java][Azure SDK for Java]。 

## <a name="what-are-service-bus-topics-and-subscriptions"></a>什么是服务总线主题和订阅？
服务总线主题和订阅支持 *发布/订阅* 消息通信模型。 在使用主题和订阅时，分布式应用程序的组件不会直接相互通信，而是通过充当中介的主题交换消息。

![TopicConcepts](./media/service-bus-java-how-to-use-topics-subscriptions/sb-topics-01.png)

与每条消息都由单个使用方处理的服务总线队列相比，主题和订阅通过发布/订阅模式提供一对多通信方式。 可向一个主题注册多个订阅。 当消息发送到主题时，每个订阅会分别对该消息进行处理。 主题订阅类似于接收发送至该主题的消息副本的虚拟队列。 可以选择基于每个订阅注册主题的筛选规则，这样就可以筛选或限制哪些主题订阅接收发送至某个主题的哪些消息。

利用服务总线主题和订阅，可以进行扩展以处理跨大量用户和应用程序的许多消息。

## <a name="create-a-service-bus-namespace"></a>创建服务总线命名空间

服务总线消息传送命名空间提供唯一的作用域容器，可以通过其[完全限定域名](https://wikipedia.org/wiki/Fully_qualified_domain_name)进行引用，而在该域名中，可以创建一个或多个队列、主题和订阅。 以下示例在新的或现有的[资源组](/azure/azure-resource-manager/resource-group-portal)中创建一个服务总线消息传送命名空间：

1. 在门户的左侧导航窗格中，依次单击“+ 创建资源”、“企业集成”和“服务总线”。
2. 在“创建命名空间”  对话框中，输入命名空间名称。 系统会立即检查该名称是否可用。
3. 在确保命名空间名称可用后，请选择定价层（标准或高级）。
4. 在“订阅”  字段中，选择要创建命名空间的 Azure 订阅。
5. 在“资源组”字段中，选择将存放命名空间的现有资源组，或者创建一个新资源组。      
6. 在“位置” 中，选择应托管命名空间的国家或地区。
7. 单击“创建”。 系统现已创建命名空间并已将其启用。 可能需要等待几分钟，因为系统将为帐户配置资源。

  ![命名空间](./media/service-bus-tutorial-topics-subscriptions-portal/create-namespace.png)

### <a name="obtain-the-management-credentials"></a>获取管理凭据

创建新的命名空间时，会自动生成一项初始的共享访问签名 (SAS) 规则，将一对主密钥和辅助密钥关联到一起，向每个密钥授予对命名空间的所有资产的完全控制权限。 若要复制初始规则，请执行以下步骤：

1. 单击“所有资源”，然后单击新创建的命名空间名称。
2. 在命名空间窗口中，单击“共享访问策略”。
3. 在“共享访问策略”屏幕中，单击“RootManageSharedAccessKey”。
4. 在“策略: RootManageSharedAccessKey”窗口中，单击“主连接字符串”旁边的“复制”按钮，将连接字符串复制到剪贴板供以后使用。 将此值粘贴到记事本或其他某个临时位置。

    ![connection-string](./media/service-bus-tutorial-topics-subscriptions-portal/connection-string.png)
5. 重复上述步骤，将**主键**的值复制和粘贴到临时位置，供以后使用。

## <a name="create-a-topic"></a>创建主题 
若要创建服务总线主题，请指定要在其中创建该主题的命名空间。 以下示例演示如何在门户中创建主题：

1. 在门户左侧的导航窗格中，单击“服务总线”（如果未看到“服务总线”，请单击“所有服务”）。
2. 单击要在其中创建主题的命名空间。
3. 在命名空间窗口中单击“主题”，然后在“主题”窗口中单击“+ 主题”。
4. 输入 **BasicTopic** 作为主题**名称**，并将其他值保留默认值。
5. 在窗口底部，单击“创建”。


## <a name="create-subscriptions-for-the-topic"></a>创建主题的订阅
1. 选择你创建的主题。
2. 单击“+ 订阅”，输入订阅名称 **Subscription1**，将所有其他值保留默认值。
3. 再重复上述步骤两次，创建名为 **Subscription2** 和 **Subscription3** 的订阅。


## <a name="configure-your-application-to-use-service-bus"></a>配置应用程序以使用服务总线
在生成本示例之前，请确保已安装 [Azure SDK for Java][Azure SDK for Java]。 如果使用 Eclipse，则可以安装包含 Azure SDK for Java 的[用于 Eclipse 的 Azure 工具包][Azure Toolkit for Eclipse]。 然后，可将 Microsoft Azure Libraries for Java 添加到项目：

![Eclipse 生成路径中的库](media/service-bus-java-how-to-use-topics-subscriptions/eclipselibs.png)

还需要将以下 JAR 添加到 Java 生成路径中：

- gson-2.6.2.jar
- commons-cli-1.4.jar
- proton-j-0.21.0.jar

添加一个具有 **Main** 方法的类，然后在 Java 文件顶部添加以下 `import` 语句：

```java
import com.google.gson.reflect.TypeToken;
import com.microsoft.azure.servicebus.*;
import com.microsoft.azure.servicebus.primitives.ConnectionStringBuilder;
import com.google.gson.Gson;
import static java.nio.charset.StandardCharsets.*;
import java.time.Duration;
import java.util.*;
import java.util.concurrent.*;
import java.util.function.Function;
import org.apache.commons.cli.*;
import org.apache.commons.cli.DefaultParser;
```

## <a name="send-messages-to-a-topic"></a>将消息发送到主题
更新 **main** 方法来创建一个 **TopicClient** 对象，并调用一个帮助程序方法来以异步方式向服务总线主题发送示例消息。

> [!NOTE] 
> - 将 `<NameOfServiceBusNamespace>` 替换为你的服务总线命名空间的名称。 
> - 将 `<AccessKey>` 替换为你的命名空间的访问密钥。

```java
public class MyServiceBusTopicClient {

    static final Gson GSON = new Gson();
    
    public static void main(String[] args) throws Exception, ServiceBusException {
        // TODO Auto-generated method stub

        TopicClient sendClient;
        String connectionString = "Endpoint=sb://<NameOfServiceBusNamespace>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<AccessKey>";
        sendClient = new TopicClient(new ConnectionStringBuilder(connectionString, "BasicTopic"));       
        sendMessagesAsync(sendClient).thenRunAsync(() -> sendClient.closeAsync());
    }

    static CompletableFuture<Void> sendMessagesAsync(TopicClient sendClient) {
        List<HashMap<String, String>> data =
                GSON.fromJson(
                        "[" +
                                "{'name' = 'Einstein', 'firstName' = 'Albert'}," +
                                "{'name' = 'Heisenberg', 'firstName' = 'Werner'}," +
                                "{'name' = 'Curie', 'firstName' = 'Marie'}," +
                                "{'name' = 'Hawking', 'firstName' = 'Steven'}," +
                                "{'name' = 'Newton', 'firstName' = 'Isaac'}," +
                                "{'name' = 'Bohr', 'firstName' = 'Niels'}," +
                                "{'name' = 'Faraday', 'firstName' = 'Michael'}," +
                                "{'name' = 'Galilei', 'firstName' = 'Galileo'}," +
                                "{'name' = 'Kepler', 'firstName' = 'Johannes'}," +
                                "{'name' = 'Kopernikus', 'firstName' = 'Nikolaus'}" +
                                "]",
                        new TypeToken<List<HashMap<String, String>>>() {
                        }.getType());

        List<CompletableFuture> tasks = new ArrayList<>();
        for (int i = 0; i < data.size(); i++) {
            final String messageId = Integer.toString(i);
            Message message = new Message(GSON.toJson(data.get(i), Map.class).getBytes(UTF_8));
            message.setContentType("application/json");
            message.setLabel("Scientist");
            message.setMessageId(messageId);
            message.setTimeToLive(Duration.ofMinutes(2));
            System.out.printf("Message sending: Id = %s\n", message.getMessageId());
            tasks.add(
                    sendClient.sendAsync(message).thenRunAsync(() -> {
                        System.out.printf("\tMessage acknowledged: Id = %s\n", message.getMessageId());
                    }));
        }
        return CompletableFuture.allOf(tasks.toArray(new CompletableFuture<?>[tasks.size()]));
    }
}
```

服务总线主题在[标准层](service-bus-premium-messaging.md)中支持的最大消息容量为 256 KB，在[高级层](service-bus-premium-messaging.md)中则为 1 MB。 标头最大大小为 64 KB，其中包括标准和自定义应用程序属性。 一个主题中包含的消息数量不受限制，但消息的总大小受限制。 此主题大小是在创建时定义的，上限为 5 GB。

## <a name="how-to-receive-messages-from-a-subscription"></a>如何从订阅接收消息
更新 **main** 方法来为三个订阅创建三个 **SubscriptionClient** 对象，并调用一个帮助程序方法来以异步方式从服务总线主题接收消息。 示例代码假定你创建了名为 **BasicTopic** 的主题，并且三个订阅分别名为 **Subscription1**、**Subscription2** 和 **Subscription3**。 如果你为它们使用了不同的名称，请在测试代码之前更新代码。 

```java
public class MyServiceBusTopicClient {

    static final Gson GSON = new Gson();
    
    public static void main(String[] args) throws Exception, ServiceBusException {
        SubscriptionClient subscription1Client = new SubscriptionClient(new ConnectionStringBuilder(connectionString, "BasicTopic/subscriptions/Subscription1"), ReceiveMode.PEEKLOCK);
        SubscriptionClient subscription2Client = new SubscriptionClient(new ConnectionStringBuilder(connectionString, "BasicTopic/subscriptions/Subscription2"), ReceiveMode.PEEKLOCK);
        SubscriptionClient subscription3Client = new SubscriptionClient(new ConnectionStringBuilder(connectionString, "BasicTopic/subscriptions/Subscription3"), ReceiveMode.PEEKLOCK);        

        registerMessageHandlerOnClient(subscription1Client);
        registerMessageHandlerOnClient(subscription2Client);
        registerMessageHandlerOnClient(subscription3Client);
    }
    
    static void registerMessageHandlerOnClient(SubscriptionClient receiveClient) throws Exception {

        // register the RegisterMessageHandler callback
        IMessageHandler messageHandler = new IMessageHandler() {
            // callback invoked when the message handler loop has obtained a message
            public CompletableFuture<Void> onMessageAsync(IMessage message) {
                // receives message is passed to callback
                if (message.getLabel() != null &&
                        message.getContentType() != null &&
                        message.getLabel().contentEquals("Scientist") &&
                        message.getContentType().contentEquals("application/json")) {

                    byte[] body = message.getBody();
                    Map scientist = GSON.fromJson(new String(body, UTF_8), Map.class);

                    System.out.printf(
                            "\n\t\t\t\t%s Message received: \n\t\t\t\t\t\tMessageId = %s, \n\t\t\t\t\t\tSequenceNumber = %s, \n\t\t\t\t\t\tEnqueuedTimeUtc = %s," +
                                    "\n\t\t\t\t\t\tExpiresAtUtc = %s, \n\t\t\t\t\t\tContentType = \"%s\",  \n\t\t\t\t\t\tContent: [ firstName = %s, name = %s ]\n",
                            receiveClient.getEntityPath(),
                            message.getMessageId(),
                            message.getSequenceNumber(),
                            message.getEnqueuedTimeUtc(),
                            message.getExpiresAtUtc(),
                            message.getContentType(),
                            scientist != null ? scientist.get("firstName") : "",
                            scientist != null ? scientist.get("name") : "");
                }
                return receiveClient.completeAsync(message.getLockToken());
            }
            
            public void notifyException(Throwable throwable, ExceptionPhase exceptionPhase) {
                System.out.printf(exceptionPhase + "-" + throwable.getMessage());
            }
        };

 
        receiveClient.registerMessageHandler(
                    messageHandler,
                    // callback invoked when the message handler has an exception to report
                // 1 concurrent call, messages are auto-completed, auto-renew duration
                new MessageHandlerOptions(1, false, Duration.ofMinutes(1)));

    }
}
```

## <a name="run-the-program"></a>运行程序
运行程序，可以看到类似于以下输出的输出：

```java
Message sending: Id = 0
Message sending: Id = 1
Message sending: Id = 2
Message sending: Id = 3
Message sending: Id = 4
Message sending: Id = 5
Message sending: Id = 6
Message sending: Id = 7
Message sending: Id = 8
Message sending: Id = 9
    Message acknowledged: Id = 0
    Message acknowledged: Id = 9
    Message acknowledged: Id = 7
    Message acknowledged: Id = 8
    Message acknowledged: Id = 5
    Message acknowledged: Id = 6
    Message acknowledged: Id = 3
    Message acknowledged: Id = 2
    Message acknowledged: Id = 4
    Message acknowledged: Id = 1

                BasicTopic/subscriptions/Subscription1 Message received: 
                        MessageId = 0, 
                        SequenceNumber = 11, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.442Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.442Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Albert, name = Einstein ]

                BasicTopic/subscriptions/Subscription2 Message received: 
                        MessageId = 0, 
                        SequenceNumber = 11, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.442Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.442Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Albert, name = Einstein ]

                BasicTopic/subscriptions/Subscription1 Message received: 
                        MessageId = 9, 
                        SequenceNumber = 12, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.520Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.520Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Nikolaus, name = Kopernikus ]

                BasicTopic/subscriptions/Subscription1 Message received: 
                        MessageId = 8, 
                        SequenceNumber = 13, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.520Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.520Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Johannes, name = Kepler ]

                BasicTopic/subscriptions/Subscription3 Message received: 
                        MessageId = 0, 
                        SequenceNumber = 11, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.442Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.442Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Albert, name = Einstein ]

                BasicTopic/subscriptions/Subscription2 Message received: 
                        MessageId = 9, 
                        SequenceNumber = 12, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.520Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.520Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Nikolaus, name = Kopernikus ]

                BasicTopic/subscriptions/Subscription1 Message received: 
                        MessageId = 7, 
                        SequenceNumber = 14, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.520Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.520Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Galileo, name = Galilei ]

                BasicTopic/subscriptions/Subscription3 Message received: 
                        MessageId = 9, 
                        SequenceNumber = 12, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.520Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.520Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Nikolaus, name = Kopernikus ]

                BasicTopic/subscriptions/Subscription2 Message received: 
                        MessageId = 8, 
                        SequenceNumber = 13, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.520Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.520Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Johannes, name = Kepler ]

                BasicTopic/subscriptions/Subscription1 Message received: 
                        MessageId = 6, 
                        SequenceNumber = 15, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.520Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.520Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Michael, name = Faraday ]

                BasicTopic/subscriptions/Subscription3 Message received: 
                        MessageId = 8, 
                        SequenceNumber = 13, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.520Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.520Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Johannes, name = Kepler ]

                BasicTopic/subscriptions/Subscription2 Message received: 
                        MessageId = 7, 
                        SequenceNumber = 14, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.520Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.520Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Galileo, name = Galilei ]

                BasicTopic/subscriptions/Subscription1 Message received: 
                        MessageId = 5, 
                        SequenceNumber = 16, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.520Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.520Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Niels, name = Bohr ]

                BasicTopic/subscriptions/Subscription3 Message received: 
                        MessageId = 7, 
                        SequenceNumber = 14, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.520Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.520Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Galileo, name = Galilei ]

                BasicTopic/subscriptions/Subscription2 Message received: 
                        MessageId = 6, 
                        SequenceNumber = 15, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.520Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.520Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Michael, name = Faraday ]

                BasicTopic/subscriptions/Subscription1 Message received: 
                        MessageId = 4, 
                        SequenceNumber = 17, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.520Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.520Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Isaac, name = Newton ]

                BasicTopic/subscriptions/Subscription3 Message received: 
                        MessageId = 6, 
                        SequenceNumber = 15, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.520Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.520Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Michael, name = Faraday ]

                BasicTopic/subscriptions/Subscription2 Message received: 
                        MessageId = 5, 
                        SequenceNumber = 16, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.520Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.520Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Niels, name = Bohr ]

                BasicTopic/subscriptions/Subscription1 Message received: 
                        MessageId = 3, 
                        SequenceNumber = 18, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.520Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.520Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Steven, name = Hawking ]

                BasicTopic/subscriptions/Subscription3 Message received: 
                        MessageId = 5, 
                        SequenceNumber = 16, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.520Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.520Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Niels, name = Bohr ]

                BasicTopic/subscriptions/Subscription2 Message received: 
                        MessageId = 4, 
                        SequenceNumber = 17, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.520Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.520Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Isaac, name = Newton ]

                BasicTopic/subscriptions/Subscription1 Message received: 
                        MessageId = 2, 
                        SequenceNumber = 19, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.520Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.520Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Marie, name = Curie ]

                BasicTopic/subscriptions/Subscription3 Message received: 
                        MessageId = 4, 
                        SequenceNumber = 17, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.520Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.520Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Isaac, name = Newton ]

                BasicTopic/subscriptions/Subscription2 Message received: 
                        MessageId = 3, 
                        SequenceNumber = 18, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.520Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.520Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Steven, name = Hawking ]

                BasicTopic/subscriptions/Subscription1 Message received: 
                        MessageId = 1, 
                        SequenceNumber = 20, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.520Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.520Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Werner, name = Heisenberg ]

                BasicTopic/subscriptions/Subscription2 Message received: 
                        MessageId = 2, 
                        SequenceNumber = 19, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.520Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.520Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Marie, name = Curie ]

                BasicTopic/subscriptions/Subscription3 Message received: 
                        MessageId = 3, 
                        SequenceNumber = 18, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.520Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.520Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Steven, name = Hawking ]

                BasicTopic/subscriptions/Subscription3 Message received: 
                        MessageId = 2, 
                        SequenceNumber = 19, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.520Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.520Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Marie, name = Curie ]

                BasicTopic/subscriptions/Subscription2 Message received: 
                        MessageId = 1, 
                        SequenceNumber = 20, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.520Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.520Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Werner, name = Heisenberg ]

                BasicTopic/subscriptions/Subscription3 Message received: 
                        MessageId = 1, 
                        SequenceNumber = 20, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.520Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.520Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Werner, name = Heisenberg ]
```


## <a name="next-steps"></a>后续步骤
有关详细信息，请参阅[服务总线队列、主题和订阅][Service Bus queues, topics, and subscriptions]。

[Azure SDK for Java]: http://azure.microsoft.com/develop/java/
[Azure Toolkit for Eclipse]: ../azure-toolkit-for-eclipse.md
[Service Bus queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[SqlFilter]: /dotnet/api/microsoft.azure.servicebus.sqlfilter
[SqlFilter.SqlExpression]: /dotnet/api/microsoft.azure.servicebus.sqlfilter.sqlexpression
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage

[0]: ./media/service-bus-java-how-to-use-topics-subscriptions/sb-queues-13.png
[2]: ./media/service-bus-java-how-to-use-topics-subscriptions/sb-queues-04.png
[3]: ./media/service-bus-java-how-to-use-topics-subscriptions/sb-queues-09.png
