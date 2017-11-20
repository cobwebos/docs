---
title: "Azure 事件网格事件架构"
description: "介绍为 Azure 事件网格中事件所提供的属性"
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 11/07/2017
ms.author: babanisa
ms.openlocfilehash: caa709fdc2a59472ee812bde91f7300396aa5755
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/11/2017
---
# <a name="azure-event-grid-event-schema"></a>Azure 事件网格事件架构

本文介绍为所有事件提供的属性和架构。 事件由 5 个所需的字符串属性和 1 个 所需的数据对象构成。 这些属性在任何发布服务器的所有事件中通用。 数据对象包含特定于每个发布者的属性。 对于系统主题，这些属性特定于资源提供程序，例如 Azure 存储或 Azure 事件中心。

事件会发送到数组中的 Azure 事件网格（其中可包含多个事件对象）。 如果只存在单个事件，则数组长度为 1。 数组的总大小最大可为 1 MB。 数组中的每个事件被限制为 64 KB。

## <a name="event-schema"></a>事件架构

以下示例显示所有事件发布者使用的属性：

```json
[
  {
    "topic": string,
    "subject": string,    
    "id": string,
    "eventType": string,
    "eventTime": string,
    "data":{
      object-unique-to-each-publisher
    }
  }
]
```

例如，为 Azure Blob 存储事件发布的架构是：

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
 
## <a name="event-properties"></a>事件属性

所有事件均包含以下相同的顶级数据：

| 属性 | 类型 | 说明 |
| -------- | ---- | ----------- |
| 主题 | 字符串 | 事件源的完整资源路径。 此字段不可写入。 |
| subject | 字符串 | 事件主题的发布者定义路径。 |
| eventType | 字符串 | 此事件源的一个注册事件类型。 |
| EventTime | 字符串 | 基于提供程序 UTC 时间的事件生成时间。 |
| id | 字符串 | 事件的唯一标识符。 |
| 数据 | 对象 | 特定于资源提供程序的事件数据。 |

若要了解数据对象中的属性，请参阅事件源：

* [Azure 订阅（管理操作）](event-schema-subscriptions.md)
* [Blob 存储](event-schema-blob-storage.md)
* [事件中心](event-schema-event-hubs.md)
* [资源组（管理操作）](event-schema-resource-groups.md)

对于自定义主题，事件发布者确定数据对象。 顶级数据应包含与标准资源所定义事件相同的字段。 将事件发布到自定义主题时，应考虑对事件主题建模，以辅助路由和筛选。

## <a name="next-steps"></a>后续步骤

* 有关 Azure 事件网格的简介，请参阅[什么是事件网格？](overview.md)
* 有关创建 Azure 事件网格订阅的详细信息，请参阅[事件网格订阅架构](subscription-creation-schema.md)。
