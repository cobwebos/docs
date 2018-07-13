---
title: 快速入门 - 使用 Azure CLI 和 Java 通过 Azure 服务总线发送和接收消息 | Microsoft Docs
description: 本快速入门介绍如何使用 Azure CLI 和示例 Java 应用程序发送和接收 Azure 服务总线消息
services: service-bus-messaging
author: sethmanheim
manager: timlt
ms.service: service-bus-messaging
ms.devlang: java
ms.topic: quickstart
ms.date: 05/22/2018
ms.author: sethm
ms.openlocfilehash: 717ac3c61ba4e362f01bad91f52ecb217cdbe1cd
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2018
ms.locfileid: "38630379"
---
# <a name="quickstart-send-and-receive-messages-using-azure-cli-and-java"></a>快速入门：使用 Azure CLI 和 Java 发送和接收消息

Microsoft Azure 服务总线是一种提供安全消息传送和可靠性的企业集成消息中转站。 典型的服务总线方案通常涉及将两个或更多应用程序、服务或进程彼此解耦（应用程序不需同时联机）、传输状态或数据更改，以及在应用程序之间发送消息。 

例如，零售公司可能会将其销售点数据发送到后端办公系统或区域配送中心，以便进行补货和库存更新。 在这种情况下，客户端应用会将消息发送到服务总线队列并从中接收消息：

![队列](./media/service-bus-quickstart-cli/quick-start-queue.png)

本快速入门介绍如何使用 Azure CLI 和服务总线 Java 库通过服务总线来发送和接收消息。 最后，如果对更多的技术细节感兴趣，可以[阅读说明](#understand-the-sample-code)，了解示例代码的重要元素。

如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户][]。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="log-in-to-azure"></a>登录 Azure

单击 Azure 门户右上角菜单上的“Cloud Shell”按钮，然后从“选择环境”下拉列表中选择“Bash”。 

## <a name="use-cli-to-create-resources"></a>使用 CLI 创建资源

在 Cloud Shell 中的 Bash 提示符下，发出以下命令以预配服务总线资源。 请务必将所有占位符替换为适当的值：

```azurecli-interactive
# Create a resource group
az group create --name myResourceGroup --location eastus

# Create a Service Bus messaging namespace with a unique name
namespaceName=myNameSpace$RANDOM
az servicebus namespace create \
   --resource-group myResourceGroup \
   --name $namespaceName \
   --location eastus

# Create a Service Bus queue
az servicebus queue create --resource-group myResourceGroup \
   --namespace-name $namespaceName \
   --name myQueue

# Get the connection string for the namespace
connectionString=$(az servicebus namespace authorization-rule keys list \
   --resource-group myResourceGroup \
   --namespace-name  $namespaceName \
   --name RootManageSharedAccessKey \
   --query primaryConnectionString --output tsv)
```

运行最后一个命令后，将所选的连接字符串和队列名称复制并粘贴到一个临时位置，例如记事本。 在下一步中将要使用它。

## <a name="send-and-receive-messages"></a>发送和接收消息

创建命名空间和队列并且拥有所需的凭据后，便可以发送和接收消息。 可以在[此 GitHub 示例文件夹](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/quickstarts-and-tutorials/quickstart-java/src/main/java/samples/quickstart/SendAndReceiveMessages.java)中检查代码。

1. 请确保 Cloud Shell 已打开并显示了 Bash 提示符。

2. 通过发出以下命令克隆[服务总线 GitHub 存储库](https://github.com/Azure/azure-service-bus/)：

   ```bash
   git clone https://github.com/Azure/azure-service-bus.git
   ```

2. 将当前目录更改为示例文件夹，使用正斜杠作为分隔符：

   ```bash
   cd azure-service-bus/samples/Java/quickstarts-and-tutorials/quickstart-java 
   ```

3. 发出以下命令来生成应用程序：
   
   ```bash
   mvn clean package -DskipTests
   ```

4. 若要运行程序，请发出以下命令。 只要没有重启 Bash Shell，就会自动替换包含连接字符串值的变量：

   ```bash
   java -jar ./target/samples.quickstart-1.0.0-jar-with-dependencies.jar -c $connectionString -q myQueue
   ```

6. 观察 10 条消息发送到队列。 请注意，消息的顺序是不确定的，但可以看到消息发送，然后可以看到确认和被接收，此外还可以看到有效负载数据：

   ![程序输出](./media/service-bus-quickstart-cli/javaqs.png)

## <a name="clean-up-resources"></a>清理资源

运行以下命令来删除资源组、命名空间和所有相关资源：

```azurecli-interactive
az group delete --resource-group myResourceGroup
```

## <a name="understand-the-sample-code"></a>了解示例代码

此部分详述了示例代码的重要节。 可以浏览[此处](https://github.com/Azure/azure-service-bus/blob/master/samples/Java/quickstarts-and-tutorials/quickstart-java/src/main/java/samples/quickstart/SendAndReceiveMessages.java)的 GitHub 存储库提供的代码。

### <a name="get-connection-string-and-queue"></a>获取连接字符串和队列

首先，代码声明了两个在命令行中作为参数传递给程序的字符串变量：

```java
String ConnectionString = null;
String QueueName = null;
```

这些值通过参数添加，分配在 `runApp()` 方法中：

```java
public static void main(String[] args) {
    SendAndReceiveMessages app = new SendAndReceiveMessages();
    try {
        app.runApp(args);
        app.run();
    } catch (Exception e) {
        System.out.printf("%s", e.toString());
    }
    System.exit(0);
}

public void runApp(String[] args) {
    try {
        // parse connection string from command line             
        Options options = new Options();
        options.addOption(new Option("c", true, "Connection string"));
        options.addOption(new Option("q", true, "Queue Name"));
        CommandLineParser clp = new DefaultParser();
        CommandLine cl = clp.parse(options, args);
        if (cl.getOptionValue("c") != null && cl.getOptionValue("q") != null) {
            ConnectionString = cl.getOptionValue("c");
            QueueName =  cl.getOptionValue("q");
        }
        else
        {
            HelpFormatter formatter = new HelpFormatter();
            formatter.printHelp("run jar with", "", options, "", true);
        }

    } catch (Exception e) {
        System.out.printf("%s", e.toString());
    }
}
```

### <a name="create-queue-clients-to-send-and-receive"></a>创建用于发送和接收操作的队列客户端

为了发送和接收消息，`run()` 方法创建队列客户端实例，这些实例是根据连接字符串和队列名称构建的。 以下代码创建两个队列客户端，一个用于发送，一个用于接收：

```java
public void run() throws Exception {
// Create a QueueClient instance for receiving using the connection string builder
// We set the receive mode to "PeekLock", meaning the message is delivered
// under a lock and must be acknowledged ("completed") to be removed from the queue
QueueClient receiveClient = new QueueClient(new ConnectionStringBuilder(ConnectionString, QueueName), ReceiveMode.PEEKLOCK);
this.registerReceiver(receiveClient);

// Create a QueueClient instance for sending and then asynchronously send messages.
QueueClient sendClient = new QueueClient(new ConnectionStringBuilder(ConnectionString, QueueName), ReceiveMode.PEEKLOCK);
```

`run()` 方法还启动异步消息发送操作，并在发送操作完成后关闭发送程序：

```java
this.sendMessagesAsync(sendClient).thenRunAsync(() -> sendClient.closeAsync());
``` 

### <a name="construct-and-send-messages"></a>构造和发送消息

`sendMessagesAsync()` 方法创建一组（10 条）消息，并使用队列客户端以异步方式进行发送：

```java
CompletableFuture<Void> sendMessagesAsync(QueueClient sendClient) {
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
                new TypeToken<List<HashMap<String, String>>>() {}.getType());

List<CompletableFuture> tasks = new ArrayList<>();
for (int i = 0; i < data.size(); i++) {
    final String messageId = Integer.toString(i);
    Message message = new Message(GSON.toJson(data.get(i), Map.class).getBytes(UTF_8));
    message.setContentType("application/json");
    message.setLabel("Scientist");
    message.setMessageId(messageId);
    message.setTimeToLive(Duration.ofMinutes(2));
    System.out.printf("\nMessage sending: Id = %s", message.getMessageId());
    tasks.add(
            sendClient.sendAsync(message).thenRunAsync(() -> {
                System.out.printf("\n\tMessage acknowledged: Id = %s", message.getMessageId());
            }));
}
return CompletableFuture.allOf(tasks.toArray(new CompletableFuture<?>[tasks.size()]));
```

### <a name="receive-messages"></a>接收消息

`registerReceiver()` 方法注册 `RegisterMessageHandler` 回调，并设置某些消息处理程序选项：

```java
void registerReceiver(QueueClient queueClient) throws Exception {
    // register the RegisterMessageHandler callback
    queueClient.registerMessageHandler(new IMessageHandler() {
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
                                           "\n\t\t\t\tMessage received: \n\t\t\t\t\t\tMessageId = %s, \n\t\t\t\t\t\tSequenceNumber = %s, \n\t\t\t\t\t\tEnqueuedTimeUtc = %s," +
                                                   "\n\t\t\t\t\t\tExpiresAtUtc = %s, \n\t\t\t\t\t\tContentType = \"%s\",  \n\t\t\t\t\t\tContent: [ firstName = %s, name = %s ]\n",
                                           message.getMessageId(),
                                           message.getSequenceNumber(),
                                           message.getEnqueuedTimeUtc(),
                                           message.getExpiresAtUtc(),
                                           message.getContentType(),
                                           scientist != null ? scientist.get("firstName") : "",
                                           scientist != null ? scientist.get("name") : "");
                               }
                               return CompletableFuture.completedFuture(null);
                           }

                           // callback invoked when the message handler has an exception to report
                           public void notifyException(Throwable throwable, ExceptionPhase exceptionPhase) {
                               System.out.printf(exceptionPhase + "-" + throwable.getMessage());
                           }
                       },
    // 1 concurrent call, messages are auto-completed, auto-renew duration
    new MessageHandlerOptions(1, true, Duration.ofMinutes(1)));

}
```

## <a name="next-steps"></a>后续步骤

本文介绍了如何创建一个服务总线命名空间并从队列发送和接收消息所需的其他资源。 若要详细了解如何编写收发消息的代码，请继续阅读下面的服务总线教程：

> [!div class="nextstepaction"]
> [使用 CLI 和 Java 更新库存](./service-bus-tutorial-topics-subscriptions-cli.md)

[免费帐户]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[fully qualified domain name]: https://wikipedia.org/wiki/Fully_qualified_domain_name
[Install Azure CLI 2.0]: /cli/azure/install-azure-cli
[az group create]: /cli/azure/group#az_group_create