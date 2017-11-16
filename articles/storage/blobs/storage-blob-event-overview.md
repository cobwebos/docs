---
title: "响应 Azure Blob 存储事件（预览版）| Microsoft Docs"
description: "使用 Azure 事件网格订阅 Blob 存储事件。"
services: storage,event-grid
keywords: 
author: cbrooksmsft
ms.author: cbrooks
ms.date: 08/25/2017
ms.topic: article
ms.service: storage
ms.openlocfilehash: a56e6026ed0c2c873030625fa7a9b35b92faf930
ms.sourcegitcommit: e38120a5575ed35ebe7dccd4daf8d5673534626c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2017
---
# <a name="reacting-to-blob-storage-events-preview"></a>响应 Blob 存储事件（预览版）

Azure Blob 存储事件允许应用程序响应使用新式无服务器体系结构创建和删除 blob，无需采用复杂代码或昂贵低效的轮询服务。  相反，可以通过 [Azure 事件网格](https://azure.microsoft.com/services/event-grid/)向订阅者（如 [Azure Functions](https://azure.microsoft.com/services/functions/) 或 [Azure 逻辑应用](https://azure.microsoft.com/services/logic-apps/)，甚至是你的自定义 HTTP 侦听器）推送事件，且仅需为已使用的内容付费。

常见的 Blob 存储事件方案包括图像或视频处理、搜索索引或任何面向文件的工作流。  异步文件上传十分适合事件。  基于事件的体系结构对于鲜少更改，但要求立即响应的情况尤为有效。

事件网格目前为预览版，适用于美国中西部或美国西部这两个地区的账户。  相关简单示例，请参阅[将 Blob 存储事件路由到自定义 Web 终结点](storage-blob-event-quickstart.md)。

![事件网格模型](./media/storage-blob-event-overview/event-grid-functional-model.png)

## <a name="blob-storage-accounts"></a>Blob 存储帐户
[Blob 存储帐户](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-storage-accounts)支持 Blob 存储事件（但常规用途存储帐户不支持）。  Blob 存储帐户是将非结构化数据作为 Blob（对象）存储在 Azure 存储的专用存储帐户。 Blob 存储帐户类似于常规用途存储帐户，并且具有现在使用的所有卓越的耐用性、可用性、伸缩性和性能功能，包括用于块 blob 和追加 blob 的 100% API 一致性。 对于仅需要块 blob 或追加 blob 存储的应用程序，我们建议使用 Blob 存储帐户。

## <a name="available-blob-storage-events"></a>可用的 Blob 存储事件
事件网格使用[事件订阅](../../event-grid/concepts.md#event-subscriptions)将事件消息路由到订阅方。  Blob 存储事件订阅包括两种类型的事件：  

> |事件名称|说明|
> |----------|-----------|
> |`Microsoft.Storage.BlobCreated`|当通过 `PutBlob`、`PutBlockList` 或 `CopyBlob` 操作创建或替换 blob 时触发|
> |`Microsoft.Storage.BlobDeleted`|当通过 `DeleteBlob` 操作删除 blob 时触发|

## <a name="event-schema"></a>事件架构
Blob 存储事件包含响应数据更改所需的所有信息。  可以识别 Blob 存储事件，因为 eventType 属性以“Microsoft.Storage”开头。  
关于事件网格事件属性使用情况的其他信息，请参阅文档[事件网格事件架构](../../event-grid/event-schema.md)。  

> |属性|类型|说明|
> |-------------------|------------------------|-----------------------------------------------------------------------|
> |主题|字符串|发出该事件的存储帐户的完整 Azure 资源管理器 ID。|
> |subject|字符串|作为事件使用者的对象的相对资源路径，使用的扩展 Azure 资源管理器格式与用于描述存储帐户、服务和适用于 Azure RBAC 容器的格式相同。  此格式包含保留大小写的 blob 名称。|
> |EventTime|字符串|事件生成的日期/时间，采用 ISO 8601 格式|
> |eventType|字符串|“Microsoft.Storage.BlobCreated”或“Microsoft.Storage.BlobDeleted”|
> |ID|字符串|此事件的唯一标识符|
> |数据|对象|blob 存储特定事件数据的集合|
> |data.contentType|字符串|blob 的内容类型，由 blob 的 Content-Type 标头返回|
> |data.contentLength|数字|blob 的大小，以整数表示字节数，由 blob 的 Content-Type 标头返回。  随 BlobCreated 事件一起发送，但不包括 BlobDeleted。|
> |data.url|字符串|作为事件使用者的对象的 URL|
> |data.eTag|字符串|此事件触发时，对象的 etag。  不适用于 BlobDeleted 事件。|
> |data.api|字符串|触发此事件的 API 操作的名称。  对于 BlobCreated 事件，其值为“PutBlob”、“PutBlockList”或“CopyBlob”。  对于 BlobDeleted 事件，其值为“DeleteBlob”。  这些值与出现在 Azure 存储诊断日志中的 API 名称相同。  请参阅[记录的操作和状态消息](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)。|
> |data.sequencer|字符串|一个不透明的字符串值，表示任何特定 blob 名称的事件的逻辑顺序。  用户可以使用标准字符串比较，了解同一个 blob 名称上两个事件的相对序列。|
> |data.requestId|字符串|用于存储 API 操作的服务生成的请求 ID。  可用于关联日志中使用“request-id-header”字段的 Azure 存储诊断日志，由 'x-ms-request-id' 标头中的初始化 API 调用返回。 请参阅[日志格式](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format)。|
> |data.clientRequestId|字符串|用于存储 API 操作的客户端提供的请求 ID。  可用于关联日志中使用“client-request-id”字段的 Azure 存储诊断日志，可以由使用“x-ms-client-request-id”标头的客户端请求提供。 请参阅[日志格式](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format)。|
> |data.storageDiagnostics|对象|Azure 存储服务中偶尔附带的诊断数据。  如果存在，事件使用者应忽略它。|

下面是关于 BlobCreated 事件的示例：
```json
[{
  "topic": "/subscriptions/319a9601-1ec0-0000-aebc-8fe82724c81e/resourceGroups/testrg/providers/Microsoft.Storage/storageAccounts/myaccount",
  "subject": "/blobServices/default/containers/testcontainer/blobs/file1.txt",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2017-08-16T01:57:26.005121Z",
  "id": "602a88ef-0001-00e6-1233-1646070610ea",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "799304a4-bbc5-45b6-9849-ec2c66be800a",
    "requestId": "602a88ef-0001-00e6-1233-164607000000",
    "eTag": "0x8D4E44A24ABE7F1",
    "contentType": "text/plain",
    "contentLength": 447,
    "blobType": "BlockBlob",
    "url": "https://myaccount.blob.core.windows.net/testcontainer/file1.txt",
    "sequencer": "00000000000000EB000000000000C65A",
  }
}]

```

有关详细信息，请参阅 [Blob 存储事件架构](../../event-grid/event-schema-blob-storage.md)。

## <a name="filtering-events"></a>筛选事件
可按事件类型以及已创建或已删除对象的容器名称和 blob 名称来筛选 blob 事件订阅。  事件网格中的使用者筛选器基于“开始时间”和“结束时间”的匹配进行筛选，将含有匹配使用者的事件传送给订阅方。
Blob 存储事件使用者使用的格式：
```json
/blobServices/default/containers/<containername>/blobs/<blobname>
```
要匹配存储帐户的所有事件，可将主题筛选器留空。

要匹配在一组共享前缀的容器中创建的 blob 的事件，请使用 `subjectBeginsWith` 筛选器，如下所示：
```json
/blobServices/default/containers/containerprefix
```
要匹配在特定容器中创建的 blob 的事件，请使用 `subjectBeginsWith` 筛选器，如下所示：
```json
/blobServices/default/containers/containername/
```
要匹配在共享 blob 名称前缀的特定容器中创建的 blob 的事件，请使用 `subjectBeginsWith` 筛选器，如下所示：
```json
/blobServices/default/containers/containername/blobs/blobprefix
```

要匹配在共享 blob 后缀的特定容器中创建的 blob 的事件，请使用 `subjectEndsWith` 筛选器，例如“.log”或“.jpg”

有关详细信息，请参阅 [事件网格概念](../../event-grid/concepts.md#filters)。

## <a name="practices-for-consuming-events"></a>使用事件的做法
处理 Blob 存储事件的应用程序应遵循以下建议的做法：
> [!div class="checklist"]
> * 由于可将多个订阅配置为将事件路由至相同的事件处理程序，因此请勿假定事件来自特定的源，而是应检查消息的主题，确保它来自所期望的存储帐户。
> * 同样，检查 eventType 是否为准备处理的项，并且不假定所接收的全部事件都是期望的类型。
> * 消息在一段延迟时间后会无序到达，请使用 etag 字段来了解对象的相关信息是否是最新的。  此外，还可使用 sequencer 字段来了解任何特定对象的事件顺序。
> * 使用 blobType 字段可了解 blob 中允许何种类型的操作，以及应当使用哪种客户端库类型来访问该 blob。
> * 将 URL 字段与 `CloudBlockBlob` 和 `CloudAppendBlob` 构造函数配合使用，以访问 blob。
> * 忽略不了解的字段。  此做法有助于适应将来可能添加的新功能。


## <a name="next-steps"></a>后续步骤

了解关于事件网格的详细信息，尝试一下 Blob 存储事件：

- [关于事件网格](../../event-grid/overview.md)
- [将 Blob 存储事件路由到自定义 Web 终结点](storage-blob-event-quickstart.md)
