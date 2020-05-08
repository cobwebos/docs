---
title: 教程：通过 Azure CLI 使用发布/订阅频道和主题筛选器更新零售库存分类
description: 教程：在本教程中，你将了解如何从主题和订阅发送和接收消息，以及如何使用 Azure CLI 添加和使用筛选器规则
services: service-bus-messaging
author: spelluru
manager: timlt
ms.author: spelluru
ms.date: 11/05/2019
ms.topic: tutorial
ms.service: service-bus-messaging
ms.custom: mvc
ms.openlocfilehash: 0bd0d8eb8abe6f320f73e35b1e3b08e1d8dc1de3
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "73718924"
---
# <a name="tutorial-update-inventory-using-cli-and-topicssubscriptions"></a>教程：使用 CLI 和主题/订阅更新库存

Microsoft Azure 服务总线是一种多租户云消息传送服务，可以在应用程序和服务之间发送信息。 异步操作可实现灵活的中转消息传送、结构化的先进先出 (FIFO) 消息传送以及发布/订阅功能。 本教程展示了如何使用 Azure CLI 和 Java 在零售库存方案中将服务总线主题和订阅与发布/订阅频道配合使用。

在本教程中，你将了解如何执行以下操作：
> [!div class="checklist"]
> * 使用 Azure CLI 创建一个服务总线主题和一个或多个对该主题的订阅
> * 使用 Azure CLI 添加主题筛选器
> * 创建两条具有不同内容的消息
> * 发送消息并验证它们是否已到达预期的订阅
> * 从订阅接收消息

此方案的一个示例是为多个零售店更新库存分类。 在此方案中，每个商店或商店组都获取适用于它们的消息来更新其分类。 本教程展示了如何使用订阅和筛选器实现此方案。 首先，创建一个包含 3 个订阅的主题，添加一些规则和筛选器，然后从主题和订阅发送和接收消息。

![主题](./media/service-bus-tutorial-topics-subscriptions-cli/about-service-bus-topic.png)

如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户][]。

## <a name="prerequisites"></a>先决条件

若要使用 Java 开发服务总线应用，必须安装以下项：

- [Java 开发工具包](https://aka.ms/azure-jdks)最新版本。
- [Azure CLI](https://docs.microsoft.com/cli/azure)
- [Apache Maven](https://maven.apache.org) 3.0 或更高版本。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，本教程要求运行 Azure CLI 2.0.4 或更高版本。 运行 `az --version` 即可查找版本。 如需进行安装或升级，请参阅[安装 Azure CLI]( /cli/azure/install-azure-cli)。

## <a name="service-bus-topics-and-subscriptions"></a>服务总线主题和订阅

每个[对主题的订阅](service-bus-messaging-overview.md#topics)都可以接收每条消息的副本。 主题在协议和语义方面与服务总线队列完全兼容。 服务总线主题支持一系列选择规则，这些规则具有筛选条件和用来设置或修改消息属性的可选操作。 规则每次匹配时，都会生成一条消息。 若要深入了解规则、筛选器和操作，请单击此[链接](topic-filters.md)。

## <a name="sign-in-to-azure"></a>登录 Azure

安装 CLI 后，打开一个命令提示符并发出以下命令来登录到 Azure。 如果使用的是 Cloud Shell，则这些步骤不是必需的：

1. 如果在本地使用 Azure CLI，请运行以下命令来登录到 Azure。 如果在 Cloud Shell 中运行这些命令，则此登录步骤不是必需的：

   ```azurecli-interactive
   az login
   ```

2. 将当前的订阅上下文设置为要使用的 Azure 订阅：

   ```azurecli-interactive
   az account set --subscription Azure_subscription_name
   ```

## <a name="use-cli-to-provision-resources"></a>使用 CLI 来预配资源

发出以下命令来预配服务总线资源。 请务必将所有占位符替换为适当的值：

```azurecli-interactive
# Create a resource group
az group create --name myResourcegroup --location eastus

# Create a Service Bus messaging namespace with a unique name
namespaceName=myNameSpace$RANDOM
az servicebus namespace create \
   --resource-group myResourceGroup \
   --name $namespaceName \
   --location eastus

# Create a Service Bus topic
az servicebus topic create --resource-group myResourceGroup \
   --namespace-name $namespaceName \
   --name myTopic

# Create subscription 1 to the topic
az servicebus subscription create --resource-group myResourceGroup --namespace-name $namespaceName --topic-name myTopic --name S1

# Create filter 1 - use custom properties
az servicebus rule create --resource-group myResourceGroup --namespace-name $namespaceName --topic-name myTopic --subscription-name S1 --name MyFilter --filter-sql-expression "StoreId IN ('Store1','Store2','Store3')"

# Create filter 2 - use custom properties
az servicebus rule create --resource-group myResourceGroup --namespace-name $namespaceName --topic-name myTopic --subscription-name S1 --name MySecondFilter --filter-sql-expression "StoreId = 'Store4'"

# Create subscription 2
az servicebus subscription create --resource-group myResourceGroup --namespace-name $namespaceName --topic-name myTopic --name S2

# Create filter 3 - use message header properties via IN list and 
# combine with custom properties.
az servicebus rule create --resource-group myResourceGroup --namespace-name $namespaceName --topic-name myTopic --subscription-name S2 --name MyFilter --filter-sql-expression "sys.To IN ('Store5','Store6','Store7') OR StoreId = 'Store8'"

# Create subscription 3
az servicebus subscription create --resource-group myResourceGroup --namespace-name $namespaceName --topic-name myTopic --name S3

# Create filter 4 - Get everything except messages for subscription 1 and 2. 
# Also modify and add an action; in this case set the label to a specified value. 
# Assume those stores might not be part of your main store, so you only add 
# specific items to them. For that, you flag them specifically.
az servicebus rule create --resource-group DemoGroup --namespace-name DemoNamespaceSB --topic-name tutorialtest1
 --subscription-name S3 --name MyFilter --filter-sql-expression "sys.To NOT IN ('Store1','Store2','Store3','Store4','Sto
re5','Store6','Store7','Store8') OR StoreId NOT IN ('Store1','Store2','Store3','Store4','Store5','Store6','Store7','Stor
e8')" --action-sql-expression "SET sys.Label = 'SalesEvent'"

# Get the connection string
connectionString=$(az servicebus namespace authorization-rule keys list \
   --resource-group myResourceGroup \
   --namespace-name  $namespaceName \
   --name RootManageSharedAccessKey \
   --query primaryConnectionString --output tsv)
```

运行最后一个命令后，将所选的连接字符串和队列名称复制并粘贴到一个临时位置，例如记事本。 在下一步中将要使用它。

## <a name="create-filter-rules-on-subscriptions"></a>在订阅上创建筛选规则

预配命名空间和主题/订阅并且拥有所需的凭据后，便可以在订阅上创建筛选规则，然后发送和接收消息。 可以在[此 GitHub 示例文件夹](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/azure-servicebus/TopicFilters)中检查代码。

## <a name="send-and-receive-messages"></a>发送和接收消息

1. 请确保 Cloud Shell 已打开并显示了 Bash 提示符。

2. 通过发出以下命令克隆[服务总线 GitHub 存储库](https://github.com/Azure/azure-service-bus/)：

   ```shell
   git clone https://github.com/Azure/azure-service-bus.git
   ```

2. 导航到示例文件夹 `azure-service-bus/samples/Java/quickstarts-and-tutorials/quickstart-java/tutorial-topics-subscriptions-filters-java`。 请注意，在 Bash shell 中，命令区分大小写，并且路径分隔符必须为正斜杠。

3. 发出以下命令来生成应用程序：
   
   ```shell
   mvn clean package -DskipTests
   ```
4. 若要运行程序，请发出以下命令。 请确保将占位符替换为在上一步骤中获取的连接字符串和主题名称：

   ```shell
   java -jar .\target\tutorial-topics-subscriptions-filters-1.0.0-jar-with-dependencies.jar -c "myConnectionString" -t "myTopicName"
   ```

   观察发送到主题并随后从各个订阅中接收的 10 条消息：

   ![程序输出](./media/service-bus-tutorial-topics-subscriptions-cli/service-bus-tutorial-topics-subscriptions-cli.png)

## <a name="clean-up-resources"></a>清理资源

运行以下命令来删除资源组、命名空间和所有相关资源：

```azurecli-interactive
az group delete --resource-group my-resourcegroup
```

## <a name="understand-the-sample-code"></a>了解示例代码

此部分包含有关示例代码功能的更多详细信息。

### <a name="get-connection-string-and-queue"></a>获取连接字符串和队列

首先，此代码声明一组变量，这些变量推动程序的剩余执行：

```java
    public String ConnectionString = null;
    public String TopicName = null;
    static final String[] Subscriptions = {"S1","S2","S3"};
    static final String[] Store = {"Store1","Store2","Store3","Store4","Store5","Store6","Store7","Store8","Store9","Store10"};
    static final String SysField = "sys.To";
    static final String CustomField = "StoreId";
    int NrOfMessagesPerStore = 1; // Send at least 1.
```

连接字符串和主题名称是通过命令行参数添加并传递到 `main()` 的唯一值。 实际代码执行是在 `run()` 方法中触发的并且会从主题接收消息：

```java
public static void main(String[] args) {
    TutorialTopicsSubscriptionsFilters app = new TutorialTopicsSubscriptionsFilters();
        try {
            app.runApp(args);
            app.run();
        } catch (Exception e) {
            System.out.printf("%s", e.toString());
        }
        System.exit(0);
    }
}

public void run() throws Exception {
    // Send sample messages.
    this.sendMessagesToTopic();

    // Receive messages from subscriptions.
    this.receiveAllMessages();
}
```

### <a name="create-topic-client-to-send-messages"></a>创建主题客户端来发送消息

为发送和接收消息，`sendMessagesToTopic()` 方法创建一个主题客户端实例（该实例使用连接字符串和主题名称），然后调用发送消息的另一个方法：

```java
public void sendMessagesToTopic() throws Exception, ServiceBusException {
    // Create client for the topic.
    TopicClient topicClient = new TopicClient(new ConnectionStringBuilder(ConnectionString, TopicName));

    // Create a message sender from the topic client.

    System.out.printf("\nSending orders to topic.\n");

    // Now we can start sending orders.
    CompletableFuture.allOf(
            SendOrders(topicClient,Store[0]),
            SendOrders(topicClient,Store[1]),
            SendOrders(topicClient,Store[2]),
            SendOrders(topicClient,Store[3]),
            SendOrders(topicClient,Store[4]),
            SendOrders(topicClient,Store[5]),
            SendOrders(topicClient,Store[6]),
            SendOrders(topicClient,Store[7]),
            SendOrders(topicClient,Store[8]),
            SendOrders(topicClient,Store[9])
    ).join();

    System.out.printf("\nAll messages sent.\n");
}

    public CompletableFuture<Void> SendOrders(TopicClient topicClient, String store) throws Exception {

        for(int i = 0;i<NrOfMessagesPerStore;i++) {
            Random r = new Random();
            final Item item = new Item(r.nextInt(5),r.nextInt(5),r.nextInt(5));
            IMessage message = new Message(GSON.toJson(item,Item.class).getBytes(UTF_8));
            // We always set the Sent to field
            message.setTo(store);
            final String StoreId = store;
            Double priceToString = item.getPrice();
            final String priceForPut = priceToString.toString();
            message.setProperties(new HashMap<String, String>() {{
                // Additionally we add a customer store field. In reality you would use sys.To or a customer property but not both.
                // This is just for demo purposes.
                put("StoreId", StoreId);
                // Adding more potential filter / rule and action able fields
                put("Price", priceForPut);
                put("Color", item.getColor());
                put("Category", item.getItemCategory());
            }});

            System.out.printf("Sent order to Store %s. Price=%f, Color=%s, Category=%s\n", StoreId, item.getPrice(), item.getColor(), item.getItemCategory());
            topicClient.sendAsync(message);
        }

        return new CompletableFuture().completedFuture(null);
    }
```

### <a name="receive-messages-from-the-individual-subscriptions"></a>从各个订阅接收消息

`receiveAllMessages()` 方法调用 `receiveAllMessageFromSubscription()` 方法，后者为每个调用创建一个订阅客户端并从各个订阅接收消息：

```java
public void receiveAllMessages() throws Exception {
    System.out.printf("\nStart Receiving Messages.\n");

    CompletableFuture.allOf(
            receiveAllMessageFromSubscription(Subscriptions[0]),
            receiveAllMessageFromSubscription(Subscriptions[1]),
            receiveAllMessageFromSubscription(Subscriptions[2])
            ).join();
}

public CompletableFuture<Void> receiveAllMessageFromSubscription(String subscription) throws Exception {

    int receivedMessages = 0;

    // Create subscription client.
    IMessageReceiver subscriptionClient = ClientFactory.createMessageReceiverFromConnectionStringBuilder(new ConnectionStringBuilder(ConnectionString, TopicName+"/subscriptions/"+ subscription), ReceiveMode.PEEKLOCK);

    // Create a receiver from the subscription client and receive all messages.
    System.out.printf("\nReceiving messages from subscription %s.\n\n", subscription);

    while (true)
    {
        // This will make the connection wait for N seconds if new messages are available.
        // If no additional messages come we close the connection. This can also be used to realize long polling.
        // In case of long polling you would obviously set it more to e.g. 60 seconds.
        IMessage receivedMessage = subscriptionClient.receive(Duration.ofSeconds(1));
        if (receivedMessage != null)
        {
            if ( receivedMessage.getProperties() != null ) {
                System.out.printf("StoreId=%s\n", receivedMessage.getProperties().get("StoreId"));
                
                // Show the label modified by the rule action
                if(receivedMessage.getLabel() != null)
                    System.out.printf("Label=%s\n", receivedMessage.getLabel());
            }
            
            byte[] body = receivedMessage.getBody();
            Item theItem = GSON.fromJson(new String(body, UTF_8), Item.class);
            System.out.printf("Item data. Price=%f, Color=%s, Category=%s\n", theItem.getPrice(), theItem.getColor(), theItem.getItemCategory());
            
            subscriptionClient.complete(receivedMessage.getLockToken());
            receivedMessages++;
        }
        else
        {
            // No more messages to receive.
            subscriptionClient.close();
            break;
        }
    }
    System.out.printf("\nReceived %s messages from subscription %s.\n", receivedMessages, subscription);
    
    return new CompletableFuture().completedFuture(null);
}
```

> [!NOTE]
> 可以使用[服务总线资源管理器](https://github.com/paolosalvatori/ServiceBusExplorer/)管理服务总线资源。 服务总线资源管理器允许用户连接到服务总线命名空间并以一种简单的方式管理消息传送实体。 该工具提供高级功能，如导入/导出功能或用于对主题、队列、订阅、中继服务、通知中心和事件中心进行测试的功能。 

## <a name="next-steps"></a>后续步骤

在本教程中，你已使用 Azure CLI 预配了资源，然后从服务总线主题及其订阅发送并接收了消息。 你已了解如何执行以下操作：

> [!div class="checklist"]
> * 使用 Azure 门户创建一个服务总线主题和一个或多个对该主题的订阅
> * 使用 .NET 代码添加主题筛选器
> * 创建两条具有不同内容的消息
> * 发送消息并验证它们是否已到达预期的订阅
> * 从订阅接收消息

若要通过更多示例来了解如何发送和接收消息，请从 [GitHub 上的服务总线示例](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted)着手。

若要详细了解如何使用服务总线的发布/订阅功能，请转到下一教程。

> [!div class="nextstepaction"]
> [使用 PowerShell 和主题/订阅更新库存](service-bus-tutorial-topics-subscriptions-portal.md)

[免费帐户]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[fully qualified domain name]: https://wikipedia.org/wiki/Fully_qualified_domain_name
[Install the Azure CLI]: /cli/azure/install-azure-cli
[az group create]: /cli/azure/group#az_group_create
