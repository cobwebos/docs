---
title: 充当事件网格源的 Azure 事件中心
description: 介绍为 Azure 事件网格中的事件中心事件提供的属性
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 960aa1fe7184e1d02d28fdc135907119fee8f123
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86113677"
---
# <a name="azure-event-hubs-as-an-event-grid-source"></a>充当事件网格源的 Azure 事件中心

本文提供事件中心事件的属性和架构。 有关事件架构的简介，请参阅 [Azure 事件网格事件架构](event-schema.md)。

## <a name="event-grid-event-schema"></a>事件网格事件架构

### <a name="available-event-types"></a>可用事件类型

创建捕获文件时，事件中心发出 **Microsoft.EventHub.CaptureFileCreated** 事件类型。

### <a name="example-event"></a>示例事件

此示例事件显示捕获功能在存储文件时引发的事件中心事件的架构： 

```json
[
    {
        "topic": "/subscriptions/<guid>/resourcegroups/rgDataMigrationSample/providers/Microsoft.EventHub/namespaces/tfdatamigratens",
        "subject": "eventhubs/hubdatamigration",
        "eventType": "Microsoft.EventHub.CaptureFileCreated",
        "eventTime": "2017-08-31T19:12:46.0498024Z",
        "id": "14e87d03-6fbf-4bb2-9a21-92bd1281f247",
        "data": {
            "fileUrl": "https://tf0831datamigrate.blob.core.windows.net/windturbinecapture/tfdatamigratens/hubdatamigration/1/2017/08/31/19/11/45.avro",
            "fileType": "AzureBlockBlob",
            "partitionId": "1",
            "sizeInBytes": 249168,
            "eventCount": 1500,
            "firstSequenceNumber": 2400,
            "lastSequenceNumber": 3899,
            "firstEnqueueTime": "2017-08-31T19:12:14.674Z",
            "lastEnqueueTime": "2017-08-31T19:12:44.309Z"
        },
        "dataVersion": "",
        "metadataVersion": "1"
    }
]
```

### <a name="event-properties"></a>事件属性

事件具有以下顶级数据：

| 属性 | 类型 | 说明 |
| -------- | ---- | ----------- |
| 主题 | string | 事件源的完整资源路径。 此字段不可写入。 事件网格提供此值。 |
| subject | string | 事件主题的发布者定义路径。 |
| eventType | string | 此事件源的一个注册事件类型。 |
| EventTime | string | 基于提供程序 UTC 时间的事件生成时间。 |
| id | string | 事件的唯一标识符。 |
| 数据 | object | 事件中心事件数据。 |
| dataVersion | string | 数据对象的架构版本。 发布者定义架构版本。 |
| metadataVersion | string | 事件元数据的架构版本。 事件网格定义顶级属性的架构。 事件网格提供此值。 |

数据对象具有以下属性：

| 属性 | 类型 | 说明 |
| -------- | ---- | ----------- |
| fileUrl | string | 捕获文件的路径。 |
| fileType | string | 捕获文件的文件类型。 |
| partitionId | string | 分片 ID。 |
| sizeInBytes | integer | 文件大小。 |
| eventCount | integer | 文件中的事件数。 |
| firstSequenceNumber | integer | 队列中的最小序列号。 |
| lastSequenceNumber | integer | 队列中的最后一个序列号。 |
| firstEnqueueTime | string | 队列中的第一个时间。 |
| lastEnqueueTime | string | 队列中的最后一个时间。 |

## <a name="tutorials-and-how-tos"></a>教程和操作指南

|标题  |说明  |
|---------|---------|
| [教程：将大数据流式传输到数据仓库](event-grid-event-hubs-integration.md) | 当事件中心创建捕获文件时，事件网格会将一个事件发送到函数应用。 应用会检索捕获文件并将数据迁移到数据仓库。 |

## <a name="next-steps"></a>后续步骤

* 有关 Azure 事件网格的简介，请参阅[什么是事件网格？](overview.md)
* 有关创建 Azure 事件网格订阅的详细信息，请参阅[事件网格订阅架构](subscription-creation-schema.md)。
* 有关处理事件中心事件的信息，请参阅[将大数据流式传输到数据仓库](event-grid-event-hubs-integration.md)。