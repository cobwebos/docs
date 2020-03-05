---
title: 快速入门：Azure 队列存储库 v12 - JavaScript
description: 了解如何使用 Azure 队列 JavaScript v12 库创建队列并向队列添加消息。 接下来，介绍如何在队列中读取和删除消息。 另外还介绍如何删除队列。
author: mhopkins-msft
ms.author: mhopkins
ms.date: 12/13/2019
ms.service: storage
ms.subservice: queues
ms.topic: quickstart
ms.openlocfilehash: 59a5308d2c0a1fa2e1f38f2fe3da3a2cc29448be
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/29/2020
ms.locfileid: "78199778"
---
# <a name="quickstart-azure-queue-storage-client-library-v12-for-javascript"></a>快速入门：适用于 JavaScript 的 Azure 队列存储客户端库 v12

适用于 JavaScript 的 Azure 队列存储客户端库 v12 入门。 Azure 队列存储是一项可存储大量消息供以后检索和处理的服务。 请按照以下步骤安装包并试用基本任务的示例代码。

使用适用于 JavaScript 的 Azure 队列存储客户端库 v12 完成以下操作：

* 创建队列
* 向队列添加消息
* 查看队列中的消息
* 更新队列中的消息
* 从队列接收消息
* 删除队列中的消息
* 删除队列

[API 参考文档](https://docs.microsoft.com/javascript/api/@azure/storage-queue/) | [库源代码](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-queue) | [包（节点包管理器）](https://www.npmjs.com/package/@azure/storage-queue) | [示例](https://docs.microsoft.com/azure/storage/common/storage-samples-javascript?toc=%2fazure%2fstorage%2fqueues%2ftoc.json#queue-samples)

## <a name="prerequisites"></a>先决条件

* Azure 订阅 - [创建免费帐户](https://azure.microsoft.com/free/)
* Azure 存储帐户 - [创建存储帐户](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
* 适用于操作系统的当前 [Node.js](https://nodejs.org/en/download/)。

## <a name="setting-up"></a>设置

本部分逐步指导如何准备一个项目，使其与适用于 JavaScript 的 Azure 队列存储客户端库 v12 配合使用。

### <a name="create-the-project"></a>创建项目

创建名为 queues-quickstart-v12 的 Node.js 应用程序  。

1. 在控制台窗口（例如 cmd、PowerShell 或 Bash）中，为项目创建新目录。

    ```console
    mkdir queues-quickstart-v12
    ```

1. 切换到新创建的 queues-quickstart-v12 目录  。

    ```console
    cd queues-quickstart-v12
    ```

1. 创建名为 package.json 的新文本文件  。 此文件定义 Node.js 项目。 将此文件保存到 queues-quickstart-v12 目录中  。 下面是文件的内容：

    ```json
    {
        "name": "queues-quickstart-v12",
        "version": "1.0.0",
        "description": "Use the @azure/storage-queue SDK version 12 to interact with Azure Queue storage",
        "main": "queues-quickstart-v12.js",
        "scripts": {
            "start": "node queues-quickstart-v12.js"
        },
        "author": "Your Name",
        "license": "MIT",
        "dependencies": {
            "@azure/storage-queue": "^12.0.0",
            "@types/dotenv": "^4.0.3",
            "dotenv": "^6.0.0"
        }
    }
    ```

    如果需要，可以在 `author` 字段中输入自己的名字。

### <a name="install-the-package"></a>安装包

当仍在 queues-quickstart-v12 目录中时，使用 `npm install` 命令安装适用于 JavaScript 包的 Azure 队列存储客户端库  。

```console
npm install
```

 此命令读取 package.json 文件，并安装适用于 JavaScript 包的 Azure 队列存储客户端库 v12 及其依赖的所有库  。

### <a name="set-up-the-app-framework"></a>设置应用框架

从项目目录中执行以下操作：

1. 在代码编辑器中打开另一个新文本文件
1. 添加 `require` 调用以加载 Azure 和 Node.js 模块
1. 为程序创建结构，包括非常基本的异常处理

    代码如下：

    ```javascript
    const { QueueClient } = require("@azure/storage-queue");
    const uuidv1 = require("uuid/v1");

    async function main() {
        console.log("Azure Queue storage v12 - JavaScript quickstart sample");
        // Quick start code goes here
    }

    main().then(() => console.log("\nDone")).catch((ex) => console.log(ex.message));

    ```

1. 将新文件在 queues-quickstart-v12 目录中另存为 queues-quickstart-v12.js   。

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>对象模型

Azure 队列存储是一个可存储大量消息的服务。 队列消息大小最大可为 64 KB。 一个队列可以包含数百万条消息，直至达到存储帐户的总容量限值。 队列通常用于创建要异步处理的积压工作 (backlog)。 队列存储提供了三种类型的资源：

* 存储帐户
* 存储帐户中的队列
* 队列中的消息

以下图示显示了这些资源之间的关系。

![队列存储体系结构的图示](./media/storage-queues-introduction/queue1.png)

使用以下 JavaScript 类与这些资源进行交互：

* [QueueServiceClient](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queueserviceclient)：可以通过 `QueueServiceClient` 管理存储帐户中的所有队列。
* [QueueClient](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queueclient)：可以通过 `QueueClient` 类管理和操作单个队列及其消息。
* [QueueMessage](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queuemessage)：`QueueMessage` 类表示在队列上调用 [receiveMessages](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queueclient?view=azure-node-latest#receivemessages-queuereceivemessageoptions-) 时返回的单个对象。

## <a name="code-examples"></a>代码示例

这些示例代码片段演示如何使用适用于 JavaScript 的 Azure 队列存储客户端库执行以下操作：

* [获取连接字符串](#get-the-connection-string)
* [创建队列](#create-a-queue)
* [向队列添加消息](#add-messages-to-a-queue)
* [查看队列中的消息](#peek-at-messages-in-a-queue)
* [更新队列中的消息](#update-a-message-in-a-queue)
* [从队列接收消息](#receive-messages-from-a-queue)
* [删除队列中的消息](#delete-messages-from-a-queue)
* [删除队列](#delete-a-queue)

### <a name="get-the-connection-string"></a>获取连接字符串

下面的代码从[配置存储连接字符串](#configure-your-storage-connection-string)部分中创建的环境变量中检索存储帐户的连接字符串。

在 `main` 函数内添加此代码：

```javascript
// Retrieve the connection string for use with the application. The storage
// connection string is stored in an environment variable on the machine
// running the application called AZURE_STORAGE_CONNECTION_STRING. If the
// environment variable is created after the application is launched in a
// console or with Visual Studio, the shell or application needs to be 
// closed and reloaded to take the environment variable into account.
const AZURE_STORAGE_CONNECTION_STRING = process.env.AZURE_STORAGE_CONNECTION_STRING;
```

### <a name="create-a-queue"></a>创建队列

确定新队列的名称。 以下代码将 UUID 值追加到队列名称，确保其独一无二。

> [!IMPORTANT]
> 队列名称只能包含小写字母、数字和连字符，且必须以字母或数字开头。 每个连字符的前后必须为非连字符字符。 名称的长度还必须介于 3 到 63 个字符之间。 若要详细了解如何命名队列，请参阅[命名队列和元数据](https://docs.microsoft.com/rest/api/storageservices/naming-queues-and-metadata)。

创建 [QueueClient](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queueclient) 类的实例。 然后，调用 [create](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queueclient#create-queuecreateoptions-) 方法在存储帐户中创建队列。

将此代码添加到 `main` 函数的末尾：

```javascript
// Create a unique name for the queue
const queueName = "quickstart" + uuidv1();

console.log("\nCreating queue...");
console.log("\t", queueName);

// Instantiate a QueueClient which will be used to create and manipulate a queue
const queueClient = new QueueClient(AZURE_STORAGE_CONNECTION_STRING, queueName);

// Create the queue
const createQueueResponse = await queueClient.create();
console.log("Queue created, requestId:", createQueueResponse.requestId);
```

### <a name="add-messages-to-a-queue"></a>向队列添加消息

以下代码片段通过调用 [sendMessage](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queueclient#sendmessage-string--queuesendmessageoptions-) 方法，将消息添加到队列。 它还保存从第三个 `sendMessage` 调用返回的 [QueueMessage](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queuemessage)。 返回的 `sendMessageResponse` 用于在稍后的程序中更新消息内容。

将此代码添加到 `main` 函数的末尾：

```javascript
console.log("\nAdding messages to the queue...");

// Send several messages to the queue
await queueClient.sendMessage("First message");
await queueClient.sendMessage("Second message");
const sendMessageResponse = await queueClient.sendMessage("Third message");

console.log("Messages added, requestId:", sendMessageResponse.requestId);
```

### <a name="peek-at-messages-in-a-queue"></a>查看队列中的消息

通过调用 [peekMessages](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queueclient#peekmessages-queuepeekmessagesoptions-) 方法，查看队列中的消息。 `peekMessages` 方法从队列前面检索一条或多条消息，但不更改消息的可见性。

将此代码添加到 `main` 函数的末尾：

```javascript
console.log("\nPeek at the messages in the queue...");

// Peek at messages in the queue
const peekedMessages = await queueClient.peekMessages({ numberOfMessages : 5 });

for (i = 0; i < peekedMessages.peekedMessageItems.length; i++) {
    // Display the peeked message
    console.log("\t", peekedMessages.peekedMessageItems[i].messageText);
}
```

### <a name="update-a-message-in-a-queue"></a>更新队列中的消息

通过调用 [updateMessage](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queueclient#updatemessage-string--string--string--undefined---number--queueupdatemessageoptions-) 方法来更新消息的内容。 `updateMessage` 方法可以更改消息的可见性超时和内容。 消息内容必须是最大为 64 KB 的 UTF-8 编码的字符串。 除了新内容，还会传入代码中之前保存的响应中的 `messageId` 和 `popReceipt`。 `sendMessageResponse` 属性标识要更新的消息。

```javascript
console.log("\nUpdating the third message in the queue...");

// Update a message using the response saved when calling sendMessage earlier
updateMessageResponse = await queueClient.updateMessage(
    sendMessageResponse.messageId,
    sendMessageResponse.popReceipt,
    "Third message has been updated"
);

console.log("Message updated, requestId:", updateMessageResponse.requestId);
```

### <a name="receive-messages-from-a-queue"></a>从队列接收消息

通过调用 [receiveMessages](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queueclient#receivemessages-queuereceivemessageoptions-) 方法，下载以前添加的消息。  在 `numberOfMessages` 字段中，传入要针对此调用接收的最大消息数。

将此代码添加到 `main` 函数的末尾：

```javascript
console.log("\nReceiving messages from the queue...");

// Get messages from the queue
const receivedMessagesResponse = await queueClient.receiveMessages({ numberOfMessages : 5 });

console.log("Messages received, requestId:", receivedMessagesResponse.requestId);
```

### <a name="delete-messages-from-a-queue"></a>删除队列中的消息

在接收和处理完消息后，将消息从队列中删除。 在这种情况下，处理只是在控制台上显示消息。

通过调用 [deleteMessage](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queueclient#deletemessage-string--string--queuedeletemessageoptions-) 方法删除消息。 未显式删除的任何消息最终都会在队列中再次变为可见，给用户另一个处理它们的机会。

将此代码添加到 `main` 函数的末尾：

```javascript
// 'Process' and delete messages from the queue
for (i = 0; i < receivedMessagesResponse.receivedMessageItems.length; i++) {
    receivedMessage = receivedMessagesResponse.receivedMessageItems[i];

    // 'Process' the message
    console.log("\tProcessing:", receivedMessage.messageText);

    // Delete the message
    const deleteMessageResponse = await queueClient.deleteMessage(
        receivedMessage.messageId,
        receivedMessage.popReceipt
    );
    console.log("\tMessage deleted, requestId:", deleteMessageResponse.requestId);
}
```

### <a name="delete-a-queue"></a>删除队列

以下代码使用 [delete](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queueclient#delete-queuedeleteoptions-) 方法来删除队列，以便清除该应用所创建的资源。

将此代码添加到 `main` 函数的末尾并保存文件：

```javascript
// Delete the queue
console.log("\nDeleting queue...");
const deleteQueueResponse = await queueClient.delete();
console.log("Queue deleted, requestId:", deleteQueueResponse.requestId);
```

## <a name="run-the-code"></a>运行代码

此应用创建三条消息并将其添加到 Azure 队列。 此代码列出队列中的消息，并在最终删除队列之前检索并删除这些消息。

在控制台窗口中，导航到包含 *queues-quickstart-v12.js* 文件的目录，然后执行以下 `node` 命令来运行应用。

```console
node queues-quickstart-v12.js
```

应用的输出类似于以下示例：

```output
Azure Queue storage v12 - JavaScript quickstart sample

Creating queue...
         quickstartc095d120-1d04-11ea-af30-090ee231305f
Queue created, requestId: 5c0bc94c-6003-011b-7c11-b13d06000000

Adding messages to the queue...
Messages added, requestId: a0390321-8003-001e-0311-b18f2c000000

Peek at the messages in the queue...
         First message
         Second message
         Third message

Updating the third message in the queue...
Message updated, requestId: cb172c9a-5003-001c-2911-b18dd6000000

Receiving messages from the queue...
Messages received, requestId: a039036f-8003-001e-4811-b18f2c000000
        Processing: First message
        Message deleted, requestId: 4a65b82b-d003-00a7-5411-b16c22000000
        Processing: Second message
        Message deleted, requestId: 4f0b2958-c003-0030-2a11-b10feb000000
        Processing: Third message has been updated
        Message deleted, requestId: 6c978fcb-5003-00b6-2711-b15b39000000

Deleting queue...
Queue deleted, requestId: 5c0bca05-6003-011b-1e11-b13d06000000

Done
```

逐步执行调试器中的代码，并在整个过程中检查 [Azure 门户](https://portal.azure.com)。 检查存储帐户，验证是否已创建和删除队列中的消息。

## <a name="next-steps"></a>后续步骤

在本快速入门中，你学习了如何使用 JavaScript 代码创建队列并向其添加消息。 然后，你学习了如何扫视、检索和删除消息。 最后，你学习了如何删除消息队列。

有关教程、示例、快速入门和其他文档，请访问：

> [!div class="nextstepaction"]
> [Azure for JavaScript 文档](https://docs.microsoft.com/azure/javascript/)

* 若要了解详细信息，请参阅[适用于 JavaScript 的 Azure 存储队列客户端库](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-queue)。
* 若要查看更多 Azure 队列存储示例应用，请继续学习 [Azure 队列存储客户端库 v12 JavaScript 示例](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-queue/samples)。
