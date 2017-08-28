---
title: "Azure 事件网格事件架构"
description: "介绍为 Azure 事件网格中事件所提供的属性。"
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 08/15/2017
ms.author: babanisa
ms.translationtype: HT
ms.sourcegitcommit: b6c65c53d96f4adb8719c27ed270e973b5a7ff23
ms.openlocfilehash: 6736c6a60021b51db612f0a596086a9e988d7aef
ms.contentlocale: zh-cn
ms.lasthandoff: 08/17/2017

---

# <a name="event-grid-event-schema"></a>事件网格事件架构

本文提供事件的属性和架构。 事件由 5 个所需的字符串属性和 1 个 所需的数据对象构成。 这些属性在任何发布服务器的所有事件中通用。 数据对象包含特定于每个发布服务器的属性。 对于系统主题，这些属性特定于资源提供程序，例如存储或事件中心。

事件会发送到数组中的 Azure 事件网格（其中可包含多个事件对象）。 如果只存在单个事件，则数组长度为 1。 
 
## <a name="event-properties"></a>事件属性

所有事件包含以下相同的顶级数据。

| 属性 | 类型 | 说明 |
| -------- | ---- | ----------- |
| 主题 | 字符串 | 事件源的完整资源路径。 此字段不可写入。 |
| subject | 字符串 | 事件主题的发布者定义路径。 |
| eventType | 字符串 | 此事件源的一个注册事件类型。 |
| EventTime | 字符串 | 基于提供程序 UTC 时间的事件生成时间。 |
| id | 字符串 | 事件的唯一标识符。 |
| 数据 | 对象 | 特定于资源提供程序的事件数据。 |

## <a name="available-event-sources"></a>可用事件源

以下事件源通过事件网格发布要使用的事件：

* 资源组（管理操作）
* Azure 订阅（管理操作）
* 事件中心
* 自定义主题

## <a name="azure-subscriptions"></a>Azure 订阅

Azure 订阅现在可从 Azure 资源管理器发出管理事件，例如，在创建 VM 或删除存储帐户时。

### <a name="available-event-types"></a>可用事件类型

- Microsoft.Resources.ResourceWriteSuccess：在资源创建或更新操作成功时引发。  
- Microsoft.Resources.ResourceWriteFailure：在资源创建或更新操作失败时引发。  
- Microsoft.Resources.ResourceWriteCancel：在取消资源创建或更新操作时引发。  
- Microsoft.Resources.ResourceDeleteSuccess：在资源删除操作成功时引发。  
- Microsoft.Resources.ResourceDeleteFailure：在资源删除操作失败时引发。  
- Microsoft.Resources.ResourceDeleteCancel：在取消资源删除时引发。 取消模板部署时会出现此情况。

### <a name="example-event-schema"></a>示例事件架构

```json
[
    {
    "topic":"/subscriptions/{subscription-id}",
    "subject":"/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/eventSubscriptions/LogicAppdd584bdf-8347-49c9-b9a9-d1f980783501",
    "eventType":"Microsoft.Resources.ResourceWriteSuccess",
    "eventTime":"2017-08-16T03:54:38.2696833Z",
    "id":"25b3b0d0-d79b-44d5-9963-440d4e6a9bba",
    "data": {
        "authorization":"{azure_resource_manager_authorizations}",
        "claims":"{azure_resource_manager_claims}",
        "correlationId":"54ef1e39-6a82-44b3-abc1-bdeb6ce4d3c6",
        "httpRequest":"",
        "resourceProvider":"Microsoft.EventGrid",
        "resourceUri":"/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/eventSubscriptions/LogicAppdd584bdf-8347-49c9-b9a9-d1f980783501",
        "operationName":"Microsoft.EventGrid/eventSubscriptions/write",
        "status":"Succeeded",
        "subscriptionId":"{subscription-id}",
        "tenantId":"72f988bf-86f1-41af-91ab-2d7cd011db47"
        },
    }
]
```



## <a name="resource-groups"></a>资源组

资源组现在可从 Azure 资源管理器发出管理事件，例如，在创建 VM 或删除存储帐户时。

### <a name="available-event-types"></a>可用事件类型

- Microsoft.Resources.ResourceWriteSuccess：在资源创建或更新操作成功时引发。  
- Microsoft.Resources.ResourceWriteFailure：在资源创建或更新操作失败时引发。  
- Microsoft.Resources.ResourceWriteCancel：在取消资源创建或更新操作时引发。  
- Microsoft.Resources.ResourceDeleteSuccess：在资源删除操作成功时引发。  
- Microsoft.Resources.ResourceDeleteFailure：在资源删除操作失败时引发。  
- Microsoft.Resources.ResourceDeleteCancel：在取消资源删除时引发。 取消模板部署时会出现此情况。

### <a name="example-event"></a>示例事件

```json
[
    {
    "topic":"/subscriptions/{subscription-id}/resourceGroups/{resource-group}",
    "subject":"/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/eventSubscriptions/LogicAppdd584bdf-8347-49c9-b9a9-d1f980783501",
    "eventType":"Microsoft.Resources.ResourceWriteSuccess",
    "eventTime":"2017-08-16T03:54:38.2696833Z",
    "id":"25b3b0d0-d79b-44d5-9963-440d4e6a9bba",
    "data": {
        "authorization":"{azure_resource_manager_authorizations}",
        "claims":"{azure_resource_manager_claims}",
        "correlationId":"54ef1e39-6a82-44b3-abc1-bdeb6ce4d3c6",
        "httpRequest":"",
        "resourceProvider":"Microsoft.EventGrid",
        "resourceUri":"/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/eventSubscriptions/LogicAppdd584bdf-8347-49c9-b9a9-d1f980783501",
        "operationName":"Microsoft.EventGrid/eventSubscriptions/write",
        "status":"Succeeded",
        "subscriptionId":"{subscription-id}",
        "tenantId":"72f988bf-86f1-41af-91ab-2d7cd011db47"
        },
    }
]
```



## <a name="event-hubs"></a>事件中心

当前仅在使用“捕获”功能将文件自动发送至存储时才会发出事件中心事件。

### <a name="available-event-types"></a>可用事件类型

- **Microsoft.EventHub.CaptureFileCreated**：在创建捕获文件时引发。

### <a name="example-event"></a>示例事件

此示例事件显示“捕获”在存储文件时引发的事件中心事件的架构。 

```json
[
    {
        "topic": "/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.EventHub/namespaces/{event-hubs-ns}",
        "subject": "eventhubs/eh1",
        "eventType": "Microsoft.EventHub.CaptureFileCreated",
        "eventTime": "2017-07-11T00:55:55.0120485Z",
        "id": "bd440490-a65e-4c97-8298-ef1eb325673c",
        "data": {
            "fileUrl": "https://gridtest1.blob.core.windows.net/acontainer/eventgridtest1/eh1/1/2017/07/11/00/54/54.avro",
            "fileType": "AzureBlockBlob",
            "partitionId": "1",
            "sizeInBytes": 0,
            "eventCount": 0,
            "firstSequenceNumber": -1,
            "lastSequenceNumber": -1,
            "firstEnqueueTime": "0001-01-01T00:00:00",
            "lastEnqueueTime": "0001-01-01T00:00:00"
        },
    }
]

```



## <a name="azure-blob-storage"></a>Azure Blob 存储

个人预览版 Azure Blob 存储（可进行注册以与事件网格集成）。

### <a name="available-event-types"></a>可用事件类型

- **Microsoft.Storage.BlobCreated**：在创建 blob 时引发。
- **Microsoft.Storage.BlobDeleted**：在删除 blob 时引发。

### <a name="example-event"></a>示例事件

此示例事件显示在创建 blob 时引发的存储事件的架构。 

```json
[
  {
    "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/xstoretestaccount",
    "subject": "/blobServices/default/containers/oc2d2817345i200097container/blobs/oc2d2817345i20002296blob",
    "eventType": "Microsoft.Storage.BlobCreated",
    "eventTime": "2017-06-26T18:41:00.9584103Z",
    "id": "831e1650-001e-001b-66ab-eeb76e069631",
    "data": {
      "api": "PutBlockList",
      "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
      "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
      "eTag": "0x8D4BCC2E4835CD0",
      "contentType": "application/octet-stream",
      "contentLength": 524288,
      "blobType": "BlockBlob",
      "url": "https://oc2d2817345i60006.blob.core.windows.net/oc2d2817345i200097container/oc2d2817345i20002296blob",
      "sequencer": "00000000000004420000000000028963",
      "storageDiagnostics": {
        "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
      }
    }
  }
]
```




## <a name="custom-topics"></a>自定义主题

自定义事件的数据有效负载由你定义，可以是任何格式正确的 JSON。 顶级数据应包含与标准资源所定义事件相同的字段。 将事件发布到自定义主题时，应考虑对事件主题建模，以辅助路由和筛选。

### <a name="example-event"></a>示例事件

以下示例显示自定义主题的一个事件：
````json
[
  {
    "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.EventGrid/topics/myeventgridtopic",
    "subject": "/myapp/vehicles/motorcycles",    
    "id": "b68529f3-68cd-4744-baa4-3c0498ec19e2",
    "eventType": "recordInserted",
    "eventTime": "2017-06-26T18:41:00.9584103Z",
    "data":{
      "make": "Ducati",
      "model": "Monster"
    }
  }
]

````

## <a name="next-steps"></a>后续步骤

* 有关事件网格的介绍，请参阅[什么是事件网格？](overview.md)
* 若要了解有关创建事件网格订阅的信息，请参阅[事件网格订阅架构](subscription-creation-schema.md)。

