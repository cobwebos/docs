---
title: "Azure 事件网格 Blob 存储事件架构"
description: "介绍为 Azure 事件网格 Blob 存储事件提供的属性"
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 01/30/2018
ms.author: tomfitz
ms.openlocfilehash: d0a8a3726ac3c33668d8ad91c97c35937c299b46
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2018
---
# <a name="azure-event-grid-event-schema-for-blob-storage"></a>Blob 存储的 Azure 事件网格事件架构

本文提供 Blob 存储事件的属性和架构。 有关事件架构的简介，请参阅 [Azure 事件网格事件架构](event-schema.md)。

## <a name="available-event-types"></a>可用事件类型

Blob 存储发出以下事件类型：

| 事件类型 | 说明 |
| ---------- | ----------- |
| Microsoft.Storage.BlobCreated | 创建 Blob 时引发。 |
| Microsoft.Storage.BlobDeleted | 删除 Blob 时引发。 |

## <a name="example-event"></a>示例事件

以下示例显示了 Blob 创建事件的架构： 

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/xstoretestaccount",
  "subject": "/blobServices/default/containers/testcontainer/blobs/testfile.txt",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "eTag": "0x8D4BCC2E4835CD0",
    "contentType": "text/plain",
    "contentLength": 524288,
    "blobType": "BlockBlob",
    "url": "https://example.blob.core.windows.net/testcontainer/testfile.txt",
    "sequencer": "00000000000004420000000000028963",
    "storageDiagnostics": {
      "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

Blob 删除事件的架构与此类似： 

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/xstoretestaccount",
  "subject": "/blobServices/default/containers/testcontainer/blobs/testfile.txt",
  "eventType": "Microsoft.Storage.BlobDeleted",
  "eventTime": "2017-11-07T20:09:22.5674003Z",
  "id": "4c2359fe-001e-00ba-0e04-58586806d298",
  "data": {
    "api": "DeleteBlob",
    "requestId": "4c2359fe-001e-00ba-0e04-585868000000",
    "contentType": "text/plain",
    "blobType": "BlockBlob",
    "url": "https://example.blob.core.windows.net/testcontainer/testfile.txt",
    "sequencer": "0000000000000281000000000002F5CA",
    "storageDiagnostics": {
      "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```
 
## <a name="event-properties"></a>事件属性

事件具有以下顶级数据：

| 属性 | Type | 说明 |
| -------- | ---- | ----------- |
| 主题 | 字符串 | 事件源的完整资源路径。 此字段不可写入。 事件网格提供此值。 |
| subject | 字符串 | 事件主题的发布者定义路径。 |
| eventType | 字符串 | 此事件源的一个注册事件类型。 |
| EventTime | 字符串 | 基于提供程序 UTC 时间的事件生成时间。 |
| id | 字符串 | 事件的唯一标识符。 |
| 数据 | 对象 | Blob 存储事件数据。 |
| dataVersion | 字符串 | 数据对象的架构版本。 发布者定义架构版本。 |
| metadataVersion | 字符串 | 事件元数据的架构版本。 事件网格定义顶级属性的架构。 事件网格提供此值。 |

数据对象具有以下属性：

| 属性 | Type | 说明 |
| -------- | ---- | ----------- |
| api | 字符串 | 触发事件的操作。 |
| ClientRequestId | 字符串 | 客户端生成的不透明值，限制为 1-KB 字符。 启用存储分析日志记录后，该值会记录在分析日志中。 |
| requestId | 字符串 | 请求的唯一标识符。 可使用它来排查请求问题。 |
| eTag | 字符串 | 可用于根据条件执行操作的值。 |
| contentType | 字符串 | 为 Blob 指定的内容类型。 |
| contentLength | integer | Blob 大小，以字节为单位。 |
| blobType | 字符串 | Blob 的类型。 有效值为“BlockBlob”或“PageBlob”。 |
| url | 字符串 | Blob 的路径。 |
| sequencer | 字符串 | 可用于跟踪请求的用户控制值。 |
| storageDiagnostics | 对象 | 有关存储诊断的信息。 |
 
## <a name="next-steps"></a>后续步骤

* 有关 Azure 事件网格的简介，请参阅[什么是事件网格？](overview.md)
* 有关创建 Azure 事件网格订阅的详细信息，请参阅[事件网格订阅架构](subscription-creation-schema.md)。
* 有关使用 Blob 存储事件的简介，请参阅[路由 Blob 存储事件 - Azure CLI](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)。 
