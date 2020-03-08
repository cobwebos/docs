---
title: Azure 事件网格 Blob 存储事件架构
description: 介绍为 Azure 事件网格 Blob 存储事件提供的属性
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: reference
ms.date: 01/17/2019
ms.author: spelluru
ms.openlocfilehash: 4a71f50a130bd9b22965d39fa942b47c70857a86
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78397370"
---
# <a name="azure-event-grid-event-schema-for-blob-storage"></a>Blob 存储的 Azure 事件网格事件架构

本文提供 Blob 存储事件的属性和架构。 有关事件架构的简介，请参阅[Azure 事件网格事件架构](event-schema.md)。

有关示例脚本和教程的列表，请参阅[存储事件源](event-sources.md#storage)。

>[!NOTE]
> 只有种类为“StorageV2 (常规用途 v2)”和“BlobStorage”的存储帐户支持事件集成。 “存储(常规用途 v1)”不支持与事件网格集成。

## <a name="list-of-events-for-blob-rest-apis"></a>Blob REST Api 事件列表

当客户端通过调用 Blob REST Api 创建、替换或删除 blob 时，将触发这些事件。

 |事件名称 |说明|
 |----------|-----------|
 |**BlobCreated** |创建或替换 blob 时触发。 <br>具体而言，当客户端使用 Blob REST API 中提供 `PutBlob`、`PutBlockList`或 `CopyBlob` 操作时，会触发此事件。   |
 |**BlobDeleted** |删除 blob 时触发。 <br>具体而言，当客户端调用 Blob REST API 中可用 `DeleteBlob` 操作时，将触发此事件。 |

> [!NOTE]
> 如果要确保仅在完全提交块 Blob 时触发**BlobCreated**事件，请筛选 `CopyBlob`、`PutBlob`和 `PutBlockList` REST API 调用的事件。 仅当数据完全提交到块 Blob 后，这些 API 调用才会触发**BlobCreated**事件。 若要了解如何创建筛选器，请参阅 "[事件网格的筛选事件](https://docs.microsoft.com/azure/event-grid/how-to-filter-events)"。

## <a name="list-of-the-events-for-azure-data-lake-storage-gen-2-rest-apis"></a>Azure Data Lake Storage 第2代 REST Api 的事件列表

如果在存储帐户上启用分层命名空间，并且客户端调用 Azure Data Lake Storage Gen2 REST Api，则会触发这些事件。

|事件名称|说明|
|----------|-----------|
|**BlobCreated** | 创建或替换 blob 时触发。 <br>具体而言，当客户端使用 Azure Data Lake Storage Gen2 REST API 中提供的 `CreateFile` 和 `FlushWithClose` 操作时，将触发此事件。 |
|**BlobDeleted** |删除 blob 时触发。 <br>具体而言，当客户端调用 Azure Data Lake Storage Gen2 REST API 中可用的 `DeleteFile` 操作时，也会触发此事件。 |
|**BlobRenamed**|重命名 blob 时触发。 <br>具体而言，当客户端使用 Azure Data Lake Storage Gen2 REST API 中可用 `RenameFile` 操作时，将触发此事件。|
|**DirectoryCreated**|创建目录时触发。 <br>具体而言，当客户端使用 Azure Data Lake Storage Gen2 REST API 中可用 `CreateDirectory` 操作时，将触发此事件。|
|**DirectoryRenamed**|重命名目录时触发。 <br>具体而言，当客户端使用 Azure Data Lake Storage Gen2 REST API 中可用 `RenameDirectory` 操作时，将触发此事件。|
|**DirectoryDeleted**|删除目录时触发。 <br>具体而言，当客户端使用 Azure Data Lake Storage Gen2 REST API 中可用 `DeleteDirectory` 操作时，将触发此事件。|

> [!NOTE]
> 如果要确保仅在完全提交块 Blob 时触发**BlobCreated**事件，请筛选 `FlushWithClose` REST API 调用的事件。 仅当数据完全提交到块 Blob 后，此 API 调用才会触发**BlobCreated**事件。 若要了解如何创建筛选器，请参阅 "[事件网格的筛选事件](https://docs.microsoft.com/azure/event-grid/how-to-filter-events)"。

<a id="example-event" />

## <a name="the-contents-of-an-event-response"></a>事件响应的内容

触发事件时，事件网格服务将有关该事件的数据发送到订阅终结点。

本部分包含有关每个 blob 存储事件的数据外观的示例。

### <a name="microsoftstorageblobcreated-event"></a>BlobCreated 事件

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/test-container/blobs/new-file.txt",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "eTag": "\"0x8D4BCC2E4835CD0\"",
    "contentType": "text/plain",
    "contentLength": 524288,
    "blobType": "BlockBlob",
    "url": "https://my-storage-account.blob.core.windows.net/testcontainer/new-file.txt",
    "sequencer": "00000000000004420000000000028963",
    "storageDiagnostics": {
      "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstorageblobcreated-event-data-lake-storage-gen2"></a>BlobCreated 事件（Data Lake Storage Gen2）

如果 blob 存储帐户具有分层命名空间，则该数据与前面的示例类似，但以下更改除外：

* `dataVersion` 项设置为 `2`值。

* `data.api` 项设置为字符串 `CreateFile` 或 `FlushWithClose`。

* `contentOffset` 项包含在数据集中。

> [!NOTE]
> 如果应用程序使用 `PutBlockList` 操作将新的 blob 上传到帐户，则数据不会包含这些更改。

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/new-file.txt",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "CreateFile",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "eTag": "\"0x8D4BCC2E4835CD0\"",
    "contentType": "text/plain",
    "contentLength": 0,
    "contentOffset": 0,
    "blobType": "BlockBlob",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/new-file.txt",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "2",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstorageblobdeleted-event"></a>BlobDeleted 事件

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/testcontainer/blobs/file-to-delete.txt",
  "eventType": "Microsoft.Storage.BlobDeleted",
  "eventTime": "2017-11-07T20:09:22.5674003Z",
  "id": "4c2359fe-001e-00ba-0e04-58586806d298",
  "data": {
    "api": "DeleteBlob",
    "requestId": "4c2359fe-001e-00ba-0e04-585868000000",
    "contentType": "text/plain",
    "blobType": "BlockBlob",
    "url": "https://my-storage-account.blob.core.windows.net/testcontainer/file-to-delete.txt",
    "sequencer": "0000000000000281000000000002F5CA",
    "storageDiagnostics": {
      "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstorageblobdeleted-event-data-lake-storage-gen2"></a>BlobDeleted 事件（Data Lake Storage Gen2）

如果 blob 存储帐户具有分层命名空间，则该数据与前面的示例类似，但以下更改除外：

* `dataVersion` 项设置为 `2`值。

* `data.api` 项设置为字符串 `DeleteFile`。

* `url` 项包含路径 `dfs.core.windows.net`。

> [!NOTE]
> 如果应用程序使用 `DeleteBlob` 操作从帐户中删除 blob，则数据不会包含这些更改。

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/file-to-delete.txt",
  "eventType": "Microsoft.Storage.BlobDeleted",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
    "data": {
    "api": "DeleteFile",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "contentType": "text/plain",
    "blobType": "BlockBlob",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/file-to-delete.txt",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "2",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstorageblobrenamed-event"></a>BlobRenamed 事件

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/my-renamed-file.txt",
  "eventType": "Microsoft.Storage.BlobRenamed",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "RenameFile",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "destinationUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-renamed-file.txt",
    "sourceUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-original-file.txt",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstoragedirectorycreated-event"></a>DirectoryCreated 事件

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/my-new-directory",
  "eventType": "Microsoft.Storage.DirectoryCreated",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "CreateDirectory",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-new-directory",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstoragedirectoryrenamed-event"></a>DirectoryRenamed 事件

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/my-renamed-directory",
  "eventType": "Microsoft.Storage.DirectoryRenamed",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "RenameDirectory",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "destinationUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-renamed-directory",
    "sourceUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-original-directory",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstoragedirectorydeleted-event"></a>DirectoryDeleted 事件

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/directory-to-delete",
  "eventType": "Microsoft.Storage.DirectoryDeleted",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "DeleteDirectory",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/directory-to-delete",
    "recursive": "true", 
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

## <a name="event-properties"></a>事件属性

事件具有以下顶级数据：

| 属性 | 类型 | 说明 |
| -------- | ---- | ----------- |
| 主题 | string | 事件源的完整资源路径。 此字段不可写入。 事件网格提供此值。 |
| subject | string | 事件主题的发布者定义路径。 |
| eventType | string | 此事件源的一个注册事件类型。 |
| EventTime | string | 基于提供程序 UTC 时间的事件生成时间。 |
| id | string | 事件的唯一标识符。 |
| data | 对象 | Blob 存储事件数据。 |
| dataVersion | string | 数据对象的架构版本。 发布者定义架构版本。 |
| metadataVersion | string | 事件元数据的架构版本。 事件网格定义顶级属性的架构。 事件网格提供此值。 |

数据对象具有以下属性：

| 属性 | 类型 | 说明 |
| -------- | ---- | ----------- |
| api | string | 触发事件的操作。 |
| ClientRequestId | string | 用于存储 API 操作的客户端提供的请求 id。 此 id 可用于在日志中使用 "客户端请求 id" 字段与 Azure 存储诊断日志关联，可以使用 "x-客户端请求 id" 标头在客户端请求中提供。 请参阅[日志格式](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format)。 |
| requestId | string | 用于存储 API 操作的服务生成的请求 ID。 可用于通过 Azure 存储诊断日志中的“request-id-header”字段关联到这些日志，并且由“x-ms-request-id”标头中的初始化 API 调用返回。 请参阅[日志格式](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format)。 |
| eTag | string | 可用于根据条件执行操作的值。 |
| contentType | string | 为 Blob 指定的内容类型。 |
| contentLength | integer | Blob 大小，以字节为单位。 |
| blobType | string | Blob 的类型。 有效值为“BlockBlob”或“PageBlob”。 |
| contentOffset | number | 在事件触发应用程序完成写入文件时，所执行的写入操作的偏移量（以字节为单位）。 <br>仅对具有分层命名空间的 blob 存储帐户触发的事件显示。|
| destinationUrl |string | 操作完成后将存在的文件的 url。 例如，如果重命名了某个文件，则 `destinationUrl` 属性将包含新文件名的 url。 <br>仅对具有分层命名空间的 blob 存储帐户触发的事件显示。|
| sourceUrl |string | 操作前存在的文件的 url。 例如，如果重命名了某个文件，则 `sourceUrl` 在重命名操作之前包含原始文件名的 url。 <br>仅对具有分层命名空间的 blob 存储帐户触发的事件显示。 |
| url | string | Blob 的路径。 <br>如果客户端使用 Blob REST API，则 url 将采用以下结构： *\<存储帐户名称\>\<* \>/\<文件名\>。 <br>如果客户端使用 Data Lake Storage REST API，则 url 将具有以下结构： *\<存储帐户名称\>。 dfs.core.windows.net/* \<\>/文件名 \<\>。 |
| 递归的 | string | `True` 对所有子目录执行操作;否则 `False`。 <br>仅对具有分层命名空间的 blob 存储帐户触发的事件显示。 |
| sequencer | string | 一个不透明的字符串值，表示任何特定 blob 名称的事件的逻辑顺序。  用户可以使用标准字符串比较，了解同一个 blob 名称上两个事件的相对序列。 |
| storageDiagnostics | 对象 | Azure 存储服务中偶尔附带的诊断数据。 如果存在，事件使用者应忽略它。 |

## <a name="next-steps"></a>后续步骤

* 有关 Azure 事件网格的简介，请参阅[什么是事件网格？](overview.md)
* 有关创建 Azure 事件网格订阅的详细信息，请参阅[事件网格订阅架构](subscription-creation-schema.md)。
* 有关使用 Blob 存储事件的简介，请参阅[路由 Blob 存储事件 - Azure CLI](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)。 
