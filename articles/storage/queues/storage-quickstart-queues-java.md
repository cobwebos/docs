---
title: 快速入门：Azure 队列存储库 v12 - Java
description: 了解如何使用 Azure 队列 Java v12 库创建队列并向队列中添加消息。 接下来，介绍如何在队列中读取和删除消息。 还将介绍如何删除队列。
author: mhopkins-msft
ms.custom: devx-track-java
ms.author: mhopkins
ms.date: 09/10/2020
ms.service: storage
ms.subservice: queues
ms.topic: quickstart
ms.openlocfilehash: 9b9f68737ca714c0661ffb008347149235886c68
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "90015018"
---
# <a name="quickstart-azure-queue-storage-client-library-v12-for-java"></a>快速入门：适用于 Java 的 Azure 队列存储客户端库 v12

适用于 Java 的 Azure 队列存储客户端库版本 12 入门。 Azure 队列存储服务可存储大量消息供稍后检索和处理。 请按照以下步骤安装程序包并试用基本任务的示例代码。

使用适用于 Java 的 Azure 队列存储客户端库 v12：

* 创建队列
* 向队列添加消息
* 查看队列中的消息
* 更新队列中的消息
* 接收和删除队列中的消息
* 删除队列

其他资源：

* [API 参考文档](/java/api/overview/azure/storage-queue-readme)
* [库源代码](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-queue)
* [包 (Maven)](https://mvnrepository.com/artifact/com.azure/azure-storage-queue)
* [示例](/azure/storage/common/storage-samples-java?toc=%2fazure%2fstorage%2fqueues%2ftoc.json#queue-samples)

## <a name="prerequisites"></a>先决条件

* [Java 开发工具包 (JDK)](/java/azure/jdk/) 8 或更高版本
* [Apache Maven](https://maven.apache.org/download.cgi)
* Azure 订阅 - [创建免费帐户](https://azure.microsoft.com/free/)
* Azure 存储帐户 - [创建存储帐户](/azure/storage/common/storage-quickstart-create-account)

## <a name="setting-up"></a>设置

本部分逐步指导如何准备一个项目，使其与适用于 Java 的 Azure 队列存储客户端库 v12 配合使用。

### <a name="create-the-project"></a>创建项目

创建名为 queues-quickstart-v12 的 Java 应用程序。

1. 在控制台窗口（例如 cmd、PowerShell 或 Bash）中，使用 Maven 创建名为 queues-quickstart-v12 的新控制台应用。 键入以下“mvn”命令，创建“Hello World!” Java 项目。

   ```console
   mvn archetype:generate -DgroupId=com.queues.quickstart \
                          -DartifactId=queues-quickstart-v12 \
                          -DarchetypeArtifactId=maven-archetype-quickstart \
                          -DarchetypeVersion=1.4 \
                          -DinteractiveMode=false
   ```

1. 生成项目的输出应如下所示：

    ```console
    [INFO] Scanning for projects...
    [INFO]
    [INFO] ------------------< org.apache.maven:standalone-pom >-------------------
    [INFO] Building Maven Stub Project (No POM) 1
    [INFO] --------------------------------[ pom ]---------------------------------
    [INFO]
    [INFO] >>> maven-archetype-plugin:3.1.2:generate (default-cli) > generate-sources @ standalone-pom >>>
    [INFO]
    [INFO] <<< maven-archetype-plugin:3.1.2:generate (default-cli) < generate-sources @ standalone-pom <<<
    [INFO]
    [INFO]
    [INFO] --- maven-archetype-plugin:3.1.2:generate (default-cli) @ standalone-pom ---
    [INFO] Generating project in Batch mode
    [INFO] ----------------------------------------------------------------------------
    [INFO] Using following parameters for creating project from Archetype: maven-archetype-quickstart:1.4
    [INFO] ----------------------------------------------------------------------------
    [INFO] Parameter: groupId, Value: com.queues.quickstart
    [INFO] Parameter: artifactId, Value: queues-quickstart-v12
    [INFO] Parameter: version, Value: 1.0-SNAPSHOT
    [INFO] Parameter: package, Value: com.queues.quickstart
    [INFO] Parameter: packageInPathFormat, Value: com/queues/quickstart
    [INFO] Parameter: version, Value: 1.0-SNAPSHOT
    [INFO] Parameter: package, Value: com.queues.quickstart
    [INFO] Parameter: groupId, Value: com.queues.quickstart
    [INFO] Parameter: artifactId, Value: queues-quickstart-v12
    [INFO] Project created from Archetype in dir: C:\quickstarts\queues\queues-quickstart-v12
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time:  6.394 s
    [INFO] Finished at: 2019-12-03T09:58:35-08:00
    [INFO] ------------------------------------------------------------------------
    ```

1. 切换到新创建的 queues-quickstart-v12 目录。

   ```console
   cd queues-quickstart-v12
   ```

### <a name="install-the-package"></a>安装包

在文本编辑器中打开 pom.xml 文件。 将以下依赖项元素添加到依赖项组。

```xml
<dependency>
  <groupId>com.azure</groupId>
  <artifactId>azure-storage-queue</artifactId>
  <version>12.0.1</version>
</dependency>
```

### <a name="set-up-the-app-framework"></a>设置应用框架

从项目目录中执行以下操作：

1. 导航到 /src/main/java/com/queues/quickstart 目录
1. 在编辑器中打开 App.java 文件
1. 删除 `System.out.println("Hello world!");` 语句
1. 添加 `import` 指令

代码如下：

```java
package com.queues.quickstart;

/**
 * Azure queue storage v12 SDK quickstart
 */
import com.azure.storage.queue.*;
import com.azure.storage.queue.models.*;
import java.io.*;
import java.time.*;

public class App
{
    public static void main( String[] args ) throws IOException
    {
    }
}
```

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>对象模型

Azure 队列存储是一个可存储大量消息的服务。 队列消息大小最大可为 64 KB。 一个队列可以包含数百万条消息，直至达到存储帐户的总容量限值。 队列通常用于创建要异步处理的积压工作 (backlog)。 队列存储提供三种类型的资源：

* 存储帐户
* 存储帐户中的队列
* 队列中的消息

以下图示显示了这些资源之间的关系。

![队列存储体系结构的图示](./media/storage-queues-introduction/queue1.png)

使用以下 Java 类与这些资源进行交互：

* [QueueClientBuilder](/java/api/com.azure.storage.queue.queueclientbuilder)：`QueueClientBuilder` 类配置并实例化一个 `QueueClient` 对象。
* [QueueServiceClient](/java/api/com.azure.storage.queue.queueserviceclient)：通过 `QueueServiceClient` 可管理存储帐户中的所有队列。
* [QueueClient](/java/api/com.azure.storage.queue.queueclient)：通过 `QueueClient` 类可管理和操纵单个队列及其消息。
* [QueueMessageItem](/java/api/com.azure.storage.queue.models.queuemessageitem)：`QueueMessageItem` 类表示在队列中调用 [receiveMessages](/java/api/com.azure.storage.queue.queueclient.receivemessages) 时返回的各个对象。

## <a name="code-examples"></a>代码示例

这些示例代码片段演示如何使用适用于 Java 的 Azure 队列存储客户端库执行以下操作：

* [获取连接字符串](#get-the-connection-string)
* [创建队列](#create-a-queue)
* [向队列添加消息](#add-messages-to-a-queue)
* [查看队列中的消息](#peek-at-messages-in-a-queue)
* [更新队列中的消息](#update-a-message-in-a-queue)
* [接收和删除队列中的消息](#receive-and-delete-messages-from-a-queue)
* [删除队列](#delete-a-queue)

### <a name="get-the-connection-string"></a>获取连接字符串

以下代码检索存储帐户的连接字符串。 连接字符串存储在[配置存储连接字符串](#configure-your-storage-connection-string)部分中创建的环境变量中。

在 `main` 方法内添加此代码：

```java
System.out.println("Azure Queues storage v12 - Java quickstart sample\n");

// Retrieve the connection string for use with the application. The storage
// connection string is stored in an environment variable on the machine
// running the application called AZURE_STORAGE_CONNECTION_STRING. If the environment variable
// is created after the application is launched in a console or with
// Visual Studio, the shell or application needs to be closed and reloaded
// to take the environment variable into account.
String connectStr = System.getenv("AZURE_STORAGE_CONNECTION_STRING");
```

### <a name="create-a-queue"></a>创建队列

确定新队列的名称。 以下代码将 GUID 值追加到队列名称，从而确保其是唯一的。

> [!IMPORTANT]
> 队列名称只能包含小写字母、数字和连字符，并且必须以字母或数字开头。 每个连字符的前后必须为非连字符字符。 名称的长度还必须介于 3 到 63 个字符之间。 有关命名队列的详细信息，请参阅[命名队列和元数据](/rest/api/storageservices/naming-queues-and-metadata)。


创建 [QueueClient](/java/api/com.azure.storage.queue.queueclient) 类的实例。 然后，调用 [create](/java/api/com.azure.storage.queue.queueclient.create) 方法，以便在存储帐户中创建队列。

将此代码添加到 `main` 方法的末尾：

```java
// Create a unique name for the queue
String queueName = "quickstartqueues-" + java.util.UUID.randomUUID();

System.out.println("Creating queue: " + queueName);

// Instantiate a QueueClient which will be
// used to create and manipulate the queue
QueueClient queueClient = new QueueClientBuilder()
                                .connectionString(connectStr)
                                .queueName(queueName)
                                .buildClient();

// Create the queue
queueClient.create();
```

### <a name="add-messages-to-a-queue"></a>向队列添加消息

下面的代码片段调用 [sendMessage](/java/api/com.azure.storage.queue.queueclient.sendmessage) 方法将消息添加到队列中。 它还保存从 `sendMessage` 调用返回的 [SendMessageResult](/java/api/com.azure.storage.queue.models.sendmessageresult)。 结果用于稍后在程序中更新消息。

将此代码添加到 `main` 方法的末尾：

```java
System.out.println("\nAdding messages to the queue...");

// Send several messages to the queue
queueClient.sendMessage("First message");
queueClient.sendMessage("Second message");

// Save the result so we can update this message later
SendMessageResult result = queueClient.sendMessage("Third message");
```

### <a name="peek-at-messages-in-a-queue"></a>查看队列中的消息

调用 [peekMessages](/java/api/com.azure.storage.queue.queueclient.peekmessages) 方法来查看队列中的消息。 `peelkMessages` 方法从队列的前部检索一条或多条消息，但不会更改消息的可见性。

将此代码添加到 `main` 方法的末尾：

```java
System.out.println("\nPeek at the messages in the queue...");

// Peek at messages in the queue
queueClient.peekMessages(10, null, null).forEach(
    peekedMessage -> System.out.println("Message: " + peekedMessage.getMessageText()));
```

### <a name="update-a-message-in-a-queue"></a>更新队列中的消息

调用 [updateMessage](/java/api/com.azure.storage.queue.queueclient.updatemessage) 方法来更新消息的内容。 `updateMessage` 方法可以更改消息的可见性超时和内容。 消息内容必须是不超过 64 KB 的 UTF-8 编码字符串。 除消息的新内容外，还要使用 `SendMessageResult` 在代码中传入之前保存的消息 ID 和 pop 收据。 消息 ID 和 pop 收据标识要更新的消息。

```java
System.out.println("\nUpdating the third message in the queue...");

// Update a message using the result that
// was saved when sending the message
queueClient.updateMessage(result.getMessageId(),
                          result.getPopReceipt(), 
                          "Third message has been updated",
                          Duration.ofSeconds(1));
```

### <a name="receive-and-delete-messages-from-a-queue"></a>接收和删除队列中的消息

调用 [receiveMessages](/java/api/com.azure.storage.queue.queueclient.receivemessages) 方法来下载以前添加的消息。 接收并处理消息后，此示例代码还会从队列中删除消息。 在本例中，“处理”即在控制台上显示消息。

在接收和删除消息之前，应用会调用 `System.console().readLine();` 以暂停并等待用户输入。 在删除资源之前，请先在 [Azure 门户](https://portal.azure.com)中验证资源已正确创建。 未显式删除的任何消息都将在队列中再次变为可见，以方便再一次进行处理。

将此代码添加到 `main` 方法的末尾：

```java
System.out.println("\nPress Enter key to receive messages and delete them from the queue...");
System.console().readLine();

// Get messages from the queue
queueClient.receiveMessages(10).forEach(
    // "Process" the message
    receivedMessage -> {
        System.out.println("Message: " + receivedMessage.getMessageText());

        // Let the service know we're finished with
        // the message and it can be safely deleted.
        queueClient.deleteMessage(receivedMessage.getMessageId(), receivedMessage.getPopReceipt());
    }
);
```

### <a name="delete-a-queue"></a>删除队列

以下代码使用 [delete](/java/api/com.azure.storage.queue.queueclient.delete) 方法删除队列，从而清除该应用所创建的资源。

将此代码添加到 `main` 方法的末尾：

```java
System.out.println("\nPress Enter key to delete the queue...");
System.console().readLine();

// Clean up
System.out.println("Deleting queue: " + queueClient.getQueueName());
queueClient.delete();

System.out.println("Done");
```

## <a name="run-the-code"></a>运行代码

此应用创建三条消息并将其添加到 Azure 队列。 此代码列出队列中的消息，然后检索并删除它们，最后删除队列。

在控制台窗口中，导航到应用程序目录，然后生成并运行应用程序。

```console
mvn compile
```

然后，生成包。

```console
mvn package
```

运行以下 `mvn` 命令以执行应用。

```console
mvn exec:java -Dexec.mainClass="com.queues.quickstart.App" -Dexec.cleanupDaemonThreads=false
```

应用的输出类似于以下示例：

```output
Azure Queues storage v12 - Java quickstart sample

Adding messages to the queue...

Peek at the messages in the queue...
Message: First message
Message: Second message
Message: Third message

Updating the third message in the queue...

Press Enter key to receive messages and delete them from the queue...

Message: First message
Message: Second message
Message: Third message has been updated

Press Enter key to delete the queue...

Deleting queue: quickstartqueues-fbf58f33-4d5a-41ac-ac0e-1a05d01c7003
Done
```

当应用在接收到消息之前暂停时，请在 [Azure 门户](https://portal.azure.com)中检查存储帐户。 验证消息是否在队列中。

按“Enter”键接收和删除消息。 出现提示时，请再次按“Enter”键，删除队列并完成演示。

## <a name="next-steps"></a>后续步骤

通过本快速入门，你了解了如何使用 Java 代码创建队列并向其添加消息。 然后了解了如何查看、检索和删除消息。 最后，你还了解了如何删除消息队列。

有关教程、示例、快速入门和其他文档，请访问：

> [!div class="nextstepaction"]
> [面向 Java 云开发人员的 Azure](/azure/developer/java/)

* 若要查看更多 Azure 队列存储示例应用，请继续阅读 [Azure 队列存储 SDK v12 Java 客户端库示例](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-queue/src/samples/java/com/azure/storage/queue)。
