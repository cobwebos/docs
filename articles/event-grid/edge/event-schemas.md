---
title: 事件架构 = Azure 事件网格 IoT 边缘 |微软文档
description: IoT 边缘事件网格中的事件架构。
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: ba261aeedf6574f69d3c05f8fd005c912dcc59d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "73242467"
---
# <a name="event-schemas"></a>事件架构

事件网格模块以 JSON 格式接受并提供事件。 事件网格当前支持三个架构：

* **事件网格架构**
* **自定义架构**
* **云事件架构**

您可以配置发布者在主题创建期间必须满足的架构。 如果未指定，则默认为**事件网格架构**。 不符合预期架构的事件将被拒绝。

订阅者还可以配置他们希望传递事件的架构。 如果未指定，则默认为主题的架构。
当前，订阅者交付架构必须与其主题的输入架构匹配。 

## <a name="eventgrid-schema"></a>事件网格架构

EventGrid 架构由一组发布实体必须遵循的必需属性组成。 每个发布者必须填充顶级字段。

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

### <a name="eventgrid-schema-properties"></a>事件网格架构属性

所有事件都有以下顶级数据：

| properties | 类型 | 必选 | 描述 |
| -------- | ---- | ----------- |-----------
| 主题 | 字符串 | 否 | 应与发布它的主题匹配。 事件网格使用发布主题（如果未指定）的名称填充它。 |
| subject | 字符串 | 是 | 事件主题的发布者定义路径。 |
| eventType | 字符串 | 是 | 此事件源的事件类型，例如 Blob 创建。 |
| EventTime | 字符串 | 是 | 基于提供程序 UTC 时间的事件生成时间。 |
| ID | 字符串 | 否 | 事件的唯一标识符。 |
| data | 对象 (object) | 否 | 用于捕获特定于发布实体的事件数据。 |
| dataVersion | 字符串 | 是 | 数据对象的架构版本。 发布者定义架构版本。 |
| metadataVersion | 字符串 | 否 | 事件元数据的架构版本。 事件网格定义顶级属性的架构。 事件网格提供此值。 |

### <a name="example--eventgrid-schema-event"></a>示例 = 事件网格架构事件

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

## <a name="customevent-schema"></a>自定义事件架构

在自定义架构中，没有强制属性像 EventGrid 架构那样强制执行。 发布实体可以完全控制事件架构。 它提供了最大的灵活性，并启用了已建立基于事件的系统并希望重用现有事件和/或不希望绑定到特定架构的方案。

### <a name="custom-schema-properties"></a>自定义架构属性

没有强制属性。 由发布实体确定有效负载。

### <a name="example--custom-schema-event"></a>示例 = 自定义架构事件

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

除了上述架构外，事件网格本机支持[云事件 JSON 架构](https://github.com/cloudevents/spec/blob/master/json-format.md)中的事件。 CloudEvents 是一种用于描述事件数据的开放规范。 它通过提供用于发布和使用事件的通用事件架构来简化互操作性。 它是[CNCF](https://www.cncf.io/)的一部分，当前可用的版本为 1.0-rc1。

### <a name="cloudevent-schema-properties"></a>云事件架构属性

请参阅强制信封属性上的[云事件规范](https://github.com/cloudevents/spec/blob/master/json-format.md#3-envelope)。

### <a name="example--cloud-event"></a>示例 = 云事件
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
