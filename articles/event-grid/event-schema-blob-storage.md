---
title: Azure Event Grid Blob Storage 이벤트 스키마
description: Azure Event Grid를 사용하여 Blob Storage 이벤트에 제공되는 속성을 설명합니다.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: reference
ms.date: 01/17/2019
ms.author: spelluru
ms.openlocfilehash: 9c5b7b4301eb9cf019399795fc45b69fe152061d
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76722316"
---
# <a name="azure-event-grid-event-schema-for-blob-storage"></a>Azure Event Grid Blob Storage에 대한 이벤트 스키마

이 문서에서는 Blob Storage 이벤트에 대한 속성 및 스키마를 제공합니다. 有关事件架构的简介，请参阅[Azure 事件网格事件架构](event-schema.md)。

샘플 스크립트 및 자습서 목록은 [스토리지 이벤트 원본](event-sources.md#storage)을 참조하세요.

## <a name="list-of-events-for-blob-rest-apis"></a>Blob REST Api 事件列表

当客户端通过调用 Blob REST Api 创建、替换或删除 blob 时，将触发这些事件。

 |이벤트 이름 |Description|
 |----------|-----------|
 |**Microsoft.Storage.BlobCreated** |创建或替换 blob 时触发。 <br>具体而言，当客户端使用 Blob REST API 中提供 `PutBlob`、`PutBlockList`或 `CopyBlob` 操作时，会触发此事件。   |
 |**Microsoft.Storage.BlobDeleted** |删除 blob 时触发。 <br>具体而言，当客户端调用 Blob REST API 中可用 `DeleteBlob` 操作时，将触发此事件。 |

> [!NOTE]
> 如果要确保仅在完全提交块 Blob 时触发**BlobCreated**事件，请筛选 `CopyBlob`、`PutBlob`和 `PutBlockList` REST API 调用的事件。 仅当数据完全提交到块 Blob 后，这些 API 调用才会触发**BlobCreated**事件。 若要了解如何创建筛选器，请参阅 "[事件网格的筛选事件](https://docs.microsoft.com/azure/event-grid/how-to-filter-events)"。

## <a name="list-of-the-events-for-azure-data-lake-storage-gen-2-rest-apis"></a>Azure Data Lake Storage 第2代 REST Api 的事件列表

如果在存储帐户上启用分层命名空间，并且客户端调用 Azure Data Lake Storage Gen2 REST Api，则会触发这些事件。

|이벤트 이름|Description|
|----------|-----------|
|**Microsoft.Storage.BlobCreated** | 创建或替换 blob 时触发。 <br>具体而言，当客户端使用 Azure Data Lake Storage Gen2 REST API 中提供的 `CreateFile` 和 `FlushWithClose` 操作时，将触发此事件。 |
|**Microsoft.Storage.BlobDeleted** |删除 blob 时触发。 <br>具体而言，当客户端调用 Azure Data Lake Storage Gen2 REST API 中可用的 `DeleteFile` 操作时，也会触发此事件。 |
|**Microsoft.Storage.BlobRenamed**|重命名 blob 时触发。 <br>具体而言，当客户端使用 Azure Data Lake Storage Gen2 REST API 中可用 `RenameFile` 操作时，将触发此事件。|
|**Microsoft.Storage.DirectoryCreated**|创建目录时触发。 <br>具体而言，当客户端使用 Azure Data Lake Storage Gen2 REST API 中可用 `CreateDirectory` 操作时，将触发此事件。|
|**Microsoft.Storage.DirectoryRenamed**|重命名目录时触发。 <br>具体而言，当客户端使用 Azure Data Lake Storage Gen2 REST API 中可用 `RenameDirectory` 操作时，将触发此事件。|
|**Microsoft.Storage.DirectoryDeleted**|删除目录时触发。 <br>具体而言，当客户端使用 Azure Data Lake Storage Gen2 REST API 中可用 `DeleteDirectory` 操作时，将触发此事件。|

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

## <a name="event-properties"></a>이벤트 속성

이벤트에는 다음과 같은 최상위 데이터가 있습니다.

| 속성 | 유형 | Description |
| -------- | ---- | ----------- |
| 토픽 | 문자열 | 이벤트 원본에 대한 전체 리소스 경로입니다. 이 필드는 쓸 수 없습니다. Event Grid는 이 값을 제공합니다. |
| subject | 문자열 | 게시자가 정의한 이벤트 주체에 대한 경로입니다. |
| eventType | 문자열 | 이 이벤트 원본에 대해 등록된 이벤트 유형 중 하나입니다. |
| eventTime | 문자열 | 공급자의 UTC 시간을 기준으로 이벤트가 생성되는 시간입니다. |
| id | 문자열 | 이벤트에 대한 고유 식별자입니다. |
| 데이터 | object | Blob Storage 이벤트 데이터입니다. |
| dataVersion | 문자열 | 데이터 개체의 스키마 버전입니다. 게시자가 스키마 버전을 정의합니다. |
| metadataVersion | 문자열 | 이벤트 메타데이터의 스키마 버전입니다. Event Grid는 최상위 속성의 스키마를 정의합니다. Event Grid는 이 값을 제공합니다. |

데이터 개체의 속성은 다음과 같습니다.

| 속성 | 유형 | Description |
| -------- | ---- | ----------- |
| api | 문자열 | 이벤트를 트리거하는 작업입니다. |
| clientRequestId | 문자열 | 用于存储 API 操作的客户端提供的请求 id。 此 id 可用于在日志中使用 "客户端请求 id" 字段与 Azure 存储诊断日志关联，可以使用 "x-客户端请求 id" 标头在客户端请求中提供。 [로그 형식](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format)을 참조하세요. |
| requestId | 문자열 | 스토리지 API 작업에 대한 서비스에서 생성된 요청 ID입니다. 로그의 "request-id-header" 필드를 사용하여 Azure Storage 진단 로그와의 상관 관계를 지정하는 데 사용할 수 있으며, 'x-ms-request-id' 헤더에서 API 호출을 시작하여 반환됩니다. [로그 형식](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format)을 참조하세요. |
| eTag | 문자열 | 조건부로 작업을 수행하는 데 사용할 수 있는 값입니다. |
| contentType | 문자열 | Blob에 대해 지정된 콘텐츠 형식입니다. |
| contentLength | integer | Blob의 크기(바이트)입니다. |
| blobType | 문자열 | Blob의 형식입니다. 유효한 값은 "BlockBlob" 또는 "PageBlob"입니다. |
| contentOffset | number | 在事件触发应用程序完成写入文件时，所执行的写入操作的偏移量（以字节为单位）。 <br>仅对具有分层命名空间的 blob 存储帐户触发的事件显示。|
| destinationUrl |문자열 | 操作完成后将存在的文件的 url。 例如，如果重命名了某个文件，则 `destinationUrl` 属性将包含新文件名的 url。 <br>仅对具有分层命名空间的 blob 存储帐户触发的事件显示。|
| sourceUrl |문자열 | 操作前存在的文件的 url。 例如，如果重命名了某个文件，则 `sourceUrl` 在重命名操作之前包含原始文件名的 url。 <br>仅对具有分层命名空间的 blob 存储帐户触发的事件显示。 |
| url | 문자열 | Blob에 대한 경로입니다. <br>如果客户端使用 Blob REST API，则 url 将采用以下结构： *\<存储帐户名称\>\<* \>/\<文件名\>。 <br>如果客户端使用 Data Lake Storage REST API，则 url 将具有以下结构： *\<存储帐户名称\>。 dfs.core.windows.net/* \<\>/文件名 \<\>。 |
| recursive | 문자열 | `True` 对所有子目录执行操作;否则 `False`。 <br>仅对具有分层命名空间的 blob 存储帐户触发的事件显示。 |
| sequencer | 문자열 | 특정 Blob 이름에 대한 이벤트의 논리적 순서를 나타내는 불투명 문자열 값입니다.  사용자는 표준 문자열 비교를 사용하여 동일한 Blob 이름에 대한 두 이벤트의 상대적 순서를 이해할 수 있습니다. |
| storageDiagnostics | object | 경우에 따라 Azure Storage 서비스에 의해 포함되는 진단 데이터입니다. 포함될 경우, 이벤트 소비자는 무시해야 합니다. |

## <a name="next-steps"></a>다음 단계

* Azure Event Grid에 대한 소개는 [Event Grid란?](overview.md)을 참조하세요.
* Azure Event Grid 구독을 만드는 방법에 대한 자세한 내용은 [Event Grid 구독 스키마](subscription-creation-schema.md)를 참조하세요.
* Blob Storage 이벤트를 사용하는 방법에 대한 소개는 [Blob Storage 이벤트 라우팅 - Azure CLI](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)를 참조하세요. 
