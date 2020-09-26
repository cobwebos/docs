---
title: 如何使用 Node.js-Azure 存储中的 Azure 队列存储
description: 了解如何使用 Azure 队列服务创建和删除队列。 了解如何使用 Node.js 插入、获取和删除消息。
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/31/2020
ms.service: storage
ms.subservice: queues
ms.topic: how-to
ms.reviewer: dineshm
ms.custom: seo-javascript-september2019, devx-track-js
ms.openlocfilehash: 77c35ae4b9e845cd3c0f638407c0d71c36fcf9f7
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91289678"
---
# <a name="how-to-use-azure-queue-storage-from-nodejs"></a>如何从 Node.js 使用 Azure 队列存储

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

## <a name="overview"></a>概述

本指南演示如何使用 Microsoft Azure 队列服务完成常见方案。 相关示例是使用 Node.js API 编写的。 涉及的方案包括插入、扫视、获取和删除队列消息。 还将学习如何创建和删除队列。

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-nodejs-application"></a>创建 Node.js 应用程序

若要创建空白 Node.js 应用程序，请参阅[在 Azure App Service 中创建 Node.js web 应用][Create a Node.js web app in Azure App Service]，使用 Windows PowerShell 或[Visual Studio Code][Visual Studio Code][构建 Node.js 应用程序并将其部署到 Azure 云服务][Build and deploy a Node.js application to an Azure Cloud Service]。

## <a name="configure-your-application-to-access-storage"></a>配置应用程序以访问存储

[适用于 JavaScript 的 Azure 存储客户端库][Azure Storage client library for JavaScript]包括一组便于与存储 REST 服务进行通信的库。

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>使用 Node 包管理器 (NPM) 可获取该程序包

1. 使用命令行接口（例如 PowerShell (Windows) 、终端 (Mac) 或 Bash (Unix) ）导航到在其中创建了示例应用程序的文件夹。

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

1. 在命令窗口中键入 **npm install \@ azure/存储-queue** 。

1. 验证 **节点 \_ 模块** 文件夹是否已创建。 在该文件夹中，你将找到** \@ azure/存储队列**包，其中包含访问存储所需的客户端库。

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

1. 在命令窗口中键入 **npm install azure-storage** 。

1. 验证 **节点 \_ 模块** 文件夹是否已创建。 在该文件夹中，你将找到 **azure 存储** 包，其中包含访问存储所需的库。

---

### <a name="import-the-package"></a>导入包

使用代码编辑器将以下代码添加到要在其中使用队列的 JavaScript 文件的顶部。

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_ImportStatements":::

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

```javascript
var azure = require('azure-storage');
```

---

## <a name="how-to-create-a-queue"></a>如何创建队列

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

下面的代码获取名为的环境变量的值 `AZURE_STORAGE_CONNECTION_STRING` ，并使用它来创建 [QueueServiceClient](/javascript/api/@azure/storage-queue/queueserviceclient) 对象。 然后，使用 **QueueServiceClient** 对象创建 [QueueClient](/javascript/api/@azure/storage-queue/queueclient) 对象。 通过 **QueueClient** 对象，您可以使用特定的队列。

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_CreateQueue":::

如果队列已存在，则会引发异常。

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

Azure 模块将读取环境变量 `AZURE_STORAGE_ACCOUNT` `AZURE_STORAGE_ACCESS_KEY` 、或，以 `AZURE_STORAGE_CONNECTION_STRING` 获取连接到 Azure 存储帐户所需的信息。 如果未设置这些环境变量，则在调用 **createQueueService**时必须指定帐户信息。

以下代码创建一个 **QueueService** 对象，可通过该对象来操作队列。

```javascript
var queueSvc = azure.createQueueService();
```

调用 **createQueueIfNotExists** 方法以创建具有指定名称的新队列，如果该队列已存在，则返回该队列。

```javascript
queueSvc.createQueueIfNotExists('myqueue', function(error, results, response){
  if(!error){
    // Queue created or exists
  }
});
```

如果创建了队列，则 `result.created` 为 true。 如果队列已存在，则 `result.created` 为 false。

---

## <a name="how-to-insert-a-message-into-a-queue"></a>如何在队列中插入消息

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

若要将消息添加到队列，请调用 [sendMessage](/javascript/api/@azure/storage-queue/queueclient#sendmessage-string--queuesendmessageoptions-) 方法。

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_AddMessage":::

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

若要在队列中插入消息，请调用 **createMessage** 方法来创建新消息并将其添加到队列中。

```javascript
queueSvc.createMessage('myqueue', "Hello world!", function(error, results, response){
  if(!error){
    // Message inserted
  }
});
```

---

## <a name="how-to-peek-at-the-next-message"></a>如何查看下一条消息

通过调用 **peekMessages** 方法，可以查看队列中的消息，而不必从队列中将其删除。

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

默认情况下， [peekMessages](/javascript/api/@azure/storage-queue/queueclient#peekmessages-queuepeekmessagesoptions-) 扫视单条消息。 以下示例查看队列中的前五个消息。 如果显示的消息不到5个，则只返回可见消息。

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_PeekMessage":::

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

默认情况下， **peekMessages** 扫视单条消息。

```javascript
queueSvc.peekMessages('myqueue', function(error, results, response){
  if(!error){
    // Message text is in results[0].messageText
  }
});
```

`result` 包含该消息。

---

当队列中没有消息时调用 **peekMessages** 不会返回错误。 但是，不会返回消息。

## <a name="how-to-change-the-contents-of-a-queued-message"></a>如何更改已排队消息的内容

下面的示例将更新消息的文本。

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

通过调用 [updateMessage](/javascript/api/@azure/storage-queue/queueclient#updatemessage-string--string--string--number--queueupdatemessageoptions-)在队列中就地更改消息的内容。 

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_UpdateMessage":::

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

通过调用 **updateMessage**在队列中就地更改消息的内容。 

```javascript
queueSvc.getMessages('myqueue', function(error, getResults, getResponse){
  if(!error){
    // Got the message
    var message = getResults[0];
    queueSvc.updateMessage('myqueue', message.messageId, message.popReceipt, 10, {messageText: 'new text'}, function(error, updateResults, updateResponse){
      if(!error){
        // Message updated successfully
      }
    });
  }
});
```

---

## <a name="how-to-dequeue-a-message"></a>如何取消消息排队

取消排队消息的过程分为两个阶段：

1. 获取消息。

1. 删除消息。

下面的示例获取一条消息，然后将其删除。

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

若要获取消息，请调用 [receiveMessages](/javascript/api/@azure/storage-queue/queueclient#receivemessages-queuereceivemessageoptions-) 方法。 此调用会使消息在队列中不可见，因此其他任何客户端都不能处理它们。 当应用程序处理完某条消息后，即可调用 [deleteMessage](/javascript/api/@azure/storage-queue/queueclient#deletemessage-string--string--queuedeletemessageoptions-) 将其从队列中删除。

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_DequeueMessage":::

默认情况下，一条消息只会隐藏30秒。 30秒后，其他客户端就会看到它。 调用**receiveMessages**时，可以通过设置[visibilityTimeout](/javascript/api/@azure/storage-queue/queuereceivemessageoptions#visibilitytimeout)来指定其他值。

当队列中没有消息时调用 **receiveMessages** 不会返回错误。 但是，不会返回消息。

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

若要获取消息，请调用 **getMessages** 方法。 此调用会使消息在队列中不可见，因此其他任何客户端都不能处理它们。 当应用程序处理完某条消息后，即可调用 **deleteMessage** 将其从队列中删除。

```javascript
queueSvc.getMessages('myqueue', function(error, results, response){
  if(!error){
    // Message text is in results[0].messageText
    var message = results[0];
    queueSvc.deleteMessage('myqueue', message.messageId, message.popReceipt, function(error, response){
      if(!error){
        //message deleted
      }
    });
  }
});
```

默认情况下，一条消息只会隐藏30秒。 30秒后，其他客户端就会看到它。 可以将 `options.visibilityTimeout` 与 **getMessages** 配合使用，以指定其他值。

当队列中没有消息时使用 **getMessages** 不会返回错误。 但是，不会返回消息。

---

## <a name="additional-options-for-dequeuing-messages"></a>用于取消对消息进行排队的其他选项

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

可以通过两种方式自定义队列中的消息检索：

* [numberOfMessages](/javascript/api/@azure/storage-queue/queuereceivemessageoptions#numberofmessages) -检索一批消息，最多 (32。 ) 
* [visibilityTimeout](/javascript/api/@azure/storage-queue/queuereceivemessageoptions#visibilitytimeout) -设置较长或较短的不可见超时。

下面的示例使用 **receiveMessages** 方法在一次调用中获取5条消息。 然后，使用 `for` 循环处理每条消息。 它还会将通过此方法返回的所有消息的不可见性超时设置为 5 分钟。

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_DequeueMessages":::

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

可以通过两种方式自定义队列中的消息检索：

* `options.numOfMessages` - 获取一批消息（最多 32 条）。
* `options.visibilityTimeout` - 设置较长或较短的不可见性超时。

以下示例使用 **getMessages** 方法通过一次调用获取 15 条消息。 然后，使用 `for` 循环处理每条消息。 它还会将通过此方法返回的所有消息的不可见性超时设置为 5 分钟。

```javascript
queueSvc.getMessages('myqueue', {numOfMessages: 15, visibilityTimeout: 5 * 60}, function(error, results, getResponse){
  if(!error){
    // Messages retrieved
    for(var index in results){
      // text is available in result[index].messageText
      var message = results[index];
      queueSvc.deleteMessage(queueName, message.messageId, message.popReceipt, function(error, deleteResponse){
        if(!error){
          // Message deleted
        }
      });
    }
  }
});
```

---

## <a name="how-to-get-the-queue-length"></a>如何获取队列长度

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

[GetProperties](/javascript/api/@azure/storage-queue/queueclient#getproperties-queuegetpropertiesoptions-)方法返回有关队列的元数据，包括队列中等待的消息的大致数目。

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_QueueLength":::

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

**GetQueueMetadata**方法返回有关队列的元数据，包括队列中等待的消息的大致数目。

```javascript
queueSvc.getQueueMetadata('myqueue', function(error, results, response){
  if(!error){
    // Queue length is available in results.approximateMessageCount
  }
});
```

---

## <a name="how-to-list-queues"></a>如何列出队列

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

若要检索队列列表，请调用 [QueueServiceClient. listQueues]()。 若要检索按特定前缀筛选的列表，请在对**listQueues**的调用中设置[选项。](/javascript/api/@azure/storage-queue/servicelistqueuesoptions#prefix)

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_ListQueues":::

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

若要检索队列的列表，请使用 **listQueuesSegmented**。 若要检索按特定前缀筛选的列表，请使用 **listQueuesSegmentedWithPrefix**。

```javascript
queueSvc.listQueuesSegmented(null, function(error, results, response){
  if(!error){
    // results.entries contains the list of queues
  }
});
```

如果无法返回所有队列，请将 `result.continuationToken` 作为 **listQueuesSegmented** 的第一个参数或 **listQueuesSegmentedWithPrefix** 的第二个参数传递，以检索更多结果。

---

## <a name="how-to-delete-a-queue"></a>如何删除队列

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

若要删除队列及其中包含的所有消息，请对**QueueClient**对象调用[deleteQueue](/javascript/api/@azure/storage-queue/queueclient#delete-queuedeleteoptions-)方法。

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_DeleteQueue":::

若要清除队列中的所有消息而不删除它，请调用 [clearMessages](/javascript/api/@azure/storage-queue/queueclient#clearmessages-queueclearmessagesoptions-)。

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

若要删除队列及其中包含的所有消息，请对队列对象调用 **deleteQueue** 方法。

```javascript
queueSvc.deleteQueue(queueName, function(error, response){
  if(!error){
    // Queue has been deleted
  }
});
```

若要清除队列中的所有消息而不删除它，请调用 **clearMessages**。

---

[!INCLUDE [storage-check-out-samples-all](../../../includes/storage-check-out-samples-all.md)]

## <a name="next-steps"></a>后续步骤

现在，了解了有关队列存储的基础知识，可单击下面的链接来了解更复杂的存储任务。

* 访问 [Azure 存储团队博客][Azure Storage Team Blog] ，了解新增功能
* 访问 GitHub 上的[适用于 JavaScript 的 Azure 存储客户端库][Azure Storage client library for JavaScript]

[Azure Storage client library for JavaScript]: https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage#azure-storage-client-library-for-javascript
[Azure Storage Team Blog]: https://techcommunity.microsoft.com/t5/azure-storage/bg-p/AzureStorageBlog
[Build and deploy a Node.js application to an Azure Cloud Service]: ../../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Create a Node.js web app in Azure App Service]: ../../app-service/quickstart-nodejs.md
[Visual Studio Code]: https://code.visualstudio.com/docs/nodejs/nodejs-tutorial
