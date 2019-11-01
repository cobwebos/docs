---
title: 事件架构-Azure 事件网格 IoT Edge |Microsoft Docs
description: IoT Edge 上的事件网格中的事件架构。
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: ba261aeedf6574f69d3c05f8fd005c912dcc59d1
ms.sourcegitcommit: 3486e2d4eb02d06475f26fbdc321e8f5090a7fac
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/31/2019
ms.locfileid: "73242467"
---
# <a name="event-schemas"></a>事件架构

事件网格模块接受并传递 JSON 格式的事件。 事件网格目前支持三种架构：

* **EventGridSchema**
* **CustomSchema**
* **CloudEventSchema**

您可以在创建主题的过程中配置发布服务器必须遵循的架构。 如果未指定，则默认为**EventGridSchema**。 不符合预期架构的事件将被拒绝。

订阅服务器还可以配置要在其中传递事件的架构。 如果未指定，则默认值为主题的架构。
当前，订阅服务器传递架构必须与其主题的输入架构匹配。 

## <a name="eventgrid-schema"></a>EventGrid 架构

EventGrid 架构包含发布实体必须符合的一组必需的属性。 每个发布者都必须填充顶级字段。

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
    },
    "dataVersion": string,
    "metadataVersion": string
  }
]
```

### <a name="eventgrid-schema-properties"></a>EventGrid 架构属性

所有事件都具有以下顶级数据：

| properties | Type | 需要 | 描述 |
| -------- | ---- | ----------- |-----------
| 主题 | 字符串 | No | 应与其发布的主题匹配。 如果未指定，事件网格将用其发布的主题的名称进行填充。 |
| subject | 字符串 | 是 | 事件主题的发布者定义路径。 |
| eventType | 字符串 | 是 | 此事件源的事件类型，例如，BlobCreated。 |
| EventTime | 字符串 | 是 | 基于提供程序 UTC 时间的事件生成时间。 |
| ID | 字符串 | No | 事件的唯一标识符。 |
| 数据 | 对象 | No | 用于捕获特定于发布实体的事件数据。 |
| dataVersion | 字符串 | 是 | 数据对象的架构版本。 发布者定义架构版本。 |
| metadataVersion | 字符串 | No | 事件元数据的架构版本。 事件网格定义顶级属性的架构。 事件网格提供此值。 |

### <a name="example--eventgrid-schema-event"></a>示例-EventGrid schema 事件

```json
[
  {
    "id": "1807",
    "eventType": "recordInserted",
    "subject": "myapp/vehicles/motorcycles",
    "eventTime": "2017-08-10T21:03:07+00:00",
    "data": {
      "make": "Ducati",
      "model": "Monster"
    },
    "dataVersion": "1.0"
  }
]
```

## <a name="customevent-schema"></a>CustomEvent 架构

在自定义架构中，没有强制执行的、与 EventGrid 架构相同的强制属性。 发布实体可以完全控制事件架构。 它可提供最大的灵活性，并可实现这样的方案：已经有了基于事件的系统，并且想要重复使用现有事件并且/或者不希望将其与特定的架构相关联。

### <a name="custom-schema-properties"></a>自定义架构属性

无强制属性。 它负责确定有效负载。

### <a name="example--custom-schema-event"></a>示例-自定义架构事件

```json
[
  {
    "eventdata": {
      "make": "Ducati",
      "model": "Monster"
    }
  }
]
```

## <a name="cloudevent-schema"></a>CloudEvent 架构

除了上述架构外，事件网格还支持[CLOUDEVENTS JSON 架构](https://github.com/cloudevents/spec/blob/master/json-format.md)中的事件。 CloudEvents 是用于描述事件数据的开放规范。 它通过提供用于发布和使用事件的公用事件架构来简化互操作性。 它是[CNCF](https://www.cncf.io/)的一部分，当前可用的版本为 1.0-rc1。

### <a name="cloudevent-schema-properties"></a>CloudEvent 架构属性

请参阅必需信封属性上的[CloudEvents 规范](https://github.com/cloudevents/spec/blob/master/json-format.md#3-envelope)。

### <a name="example--cloud-event"></a>示例-云事件
```json
[{
       "id": "1807",
       "type": "recordInserted",
       "source": "myapp/vehicles/motorcycles",
       "time": "2017-08-10T21:03:07+00:00",
       "datacontenttype": "application/json",
       "data": {
            "make": "Ducati",
            "model": "Monster"
        },
        "dataVersion": "1.0",
        "specVersion": "1.0-rc1"
}]
```
