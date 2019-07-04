---
title: 响应 Azure Blob 存储事件 | Microsoft Docs
description: 使用 Azure 事件网格订阅 Blob 存储事件。
services: storage,event-grid
author: cbrooksmsft
ms.author: cbrooks
ms.date: 01/30/2018
ms.topic: article
ms.service: storage
ms.subservice: blobs
ms.openlocfilehash: c0655d02fd5d0d64c22db286236b2a26f9e70619
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444677"
---
# <a name="reacting-to-blob-storage-events"></a>响应 Blob 存储事件

Azure 存储事件允许应用程序可以响应的事件，如创建和删除 blob，使用新式无服务器体系结构。 为此，它无需复杂的代码或高价低效的轮询服务。

相反，事件推送[Azure 事件网格](https://azure.microsoft.com/services/event-grid/)到订阅服务器，如 Azure Functions、 Azure 逻辑应用，或甚至自己的自定义 http 侦听器，并且您只需支付所使用的内容。

Blob 存储事件可靠地发送到事件网格服务提供可靠的传递服务添加到你的应用程序通过丰富的重试策略和死信传递。

常见的 Blob 存储事件方案包括图像或视频处理、搜索索引或任何面向文件的工作流。 异步文件上传十分适合事件。 基于事件的体系结构对于鲜少更改，但要求立即响应的情况尤为有效。

如果你想要试用此项现在，请参阅这些快速入门文章：

|若要使用此工具：    |请参阅此文： |
|--|-|
|Azure 门户    |[快速入门：将 Blob 存储事件路由到使用 Azure 门户的 web 终结点](https://docs.microsoft.com/azure/event-grid/blob-event-quickstart-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Azure CLI    |[快速入门：将存储事件路由到 web 终结点使用 PowerShell](https://docs.microsoft.com/azure/storage/blobs/storage-blob-event-quickstart-powershell?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Powershell    |[快速入门：将存储事件路由到 web 终结点使用 Azure CLI](https://docs.microsoft.com/azure/storage/blobs/storage-blob-event-quickstart?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|

## <a name="the-event-model"></a>事件模型

使用事件网格[事件订阅](../../event-grid/concepts.md#event-subscriptions)事件消息路由到订阅服务器。 此映像说明事件发布者、 事件订阅和事件处理程序之间的关系。

![事件网格模型](./media/storage-blob-event-overview/event-grid-functional-model.png)

首先，订阅事件的终结点。 然后，当触发事件时，事件网格服务将向端点发送有关该事件的数据。

请参阅[Blob 存储事件架构](../../event-grid/event-schema-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)文章以查看：

> [!div class="checklist"]
> * Blob 存储事件并触发每个事件的方式的完整列表。
> * 数据事件网格的示例会将发送的每个事件。
> * 出现在数据中每个键/值对的目的。

## <a name="filtering-events"></a>筛选事件

可按事件类型以及已创建或已删除对象的容器名称和 blob 名称来筛选 blob 事件订阅。  可在[创建](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest)事件订阅期间或[以后](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest)将筛选器应用于事件订阅。 事件网格中的使用者筛选器基于“开始时间”和“结束时间”的匹配进行筛选，将含有匹配使用者的事件传送给订阅方。

若要了解有关如何应用筛选器的详细信息，请参阅[事件网格筛选事件](https://docs.microsoft.com/azure/event-grid/how-to-filter-events)。

Blob 存储事件使用者使用的格式：

```
/blobServices/default/containers/<containername>/blobs/<blobname>
```

要匹配存储帐户的所有事件，可将主题筛选器留空。

要匹配在一组共享前缀的容器中创建的 blob 的事件，请使用 `subjectBeginsWith` 筛选器，如下所示：

```
/blobServices/default/containers/containerprefix
```

要匹配在特定容器中创建的 blob 的事件，请使用 `subjectBeginsWith` 筛选器，如下所示：

```
/blobServices/default/containers/containername/
```

要匹配在共享 blob 名称前缀的特定容器中创建的 blob 的事件，请使用 `subjectBeginsWith` 筛选器，如下所示：

```
/blobServices/default/containers/containername/blobs/blobprefix
```

要匹配在共享 blob 后缀的特定容器中创建的 blob 事件，请使用 `subjectEndsWith` 筛选器，例如“.log”或“.jpg”。 有关详细信息，请参阅 [事件网格概念](../../event-grid/concepts.md#event-subscriptions)。

## <a name="practices-for-consuming-events"></a>使用事件的做法

处理 Blob 存储事件的应用程序应遵循以下建议的做法：
> [!div class="checklist"]
> * 由于可将多个订阅配置为将事件路由至相同的事件处理程序，因此请勿假定事件来自特定的源，而是应检查消息的主题，确保它来自所期望的存储帐户。
> * 同样，检查 eventType 是否为准备处理的项，并且不假定所接收的全部事件都是期望的类型。
> * 消息在一段延迟时间后会无序到达，请使用 etag 字段来了解对象的相关信息是否是最新的。  此外，还可使用 sequencer 字段来了解任何特定对象的事件顺序。
> * 使用 blobType 字段可了解 blob 中允许何种类型的操作，以及应当使用哪种客户端库类型来访问该 blob。 有效值为 `BlockBlob` 或 `PageBlob`。 
> * 将 URL 字段与 `CloudBlockBlob` 和 `CloudAppendBlob` 构造函数配合使用，以访问 blob。
> * 忽略不了解的字段。 此做法有助于适应将来可能添加的新功能。
> * 如果你想要确保**Microsoft.Storage.BlobCreated**仅在完全提交块 Blob 时触发事件，筛选的事件`CopyBlob`， `PutBlob`，`PutBlockList`或`FlushWithClose`RESTAPI 调用。 这些 API 调用触发器**Microsoft.Storage.BlobCreated**事件仅后数据已完全提交到块 Blob。 若要了解如何创建一个筛选器，请参阅[事件网格筛选事件](https://docs.microsoft.com/azure/event-grid/how-to-filter-events)。


## <a name="next-steps"></a>后续步骤

了解关于事件网格的详细信息，尝试一下 Blob 存储事件：

- [关于事件网格](../../event-grid/overview.md)
- [将 Blob 存储事件路由到自定义 Web 终结点](storage-blob-event-quickstart.md)
