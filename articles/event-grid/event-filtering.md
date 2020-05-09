---
title: Azure 事件网格事件筛选
description: 介绍如何在创建 Azure 事件网格订阅时筛选事件。
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/28/2020
ms.author: spelluru
ms.openlocfilehash: 0f503b21d5a7d0fdfbee79354c198775789c0b91
ms.sourcegitcommit: b396c674aa8f66597fa2dd6d6ed200dd7f409915
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2020
ms.locfileid: "82888783"
---
# <a name="understand-event-filtering-for-event-grid-subscriptions"></a>了解事件网格订阅的事件筛选

本本介绍了筛选发送到终结点的事件的不同方法。 创建事件订阅时，有三个筛选选项：

* 事件类型
* 主题开头内容或结尾内容
* 高级字段和运算符

## <a name="event-type-filtering"></a>事件类型筛选

默认情况下，事件源的所有[事件类型](event-schema.md)都将发送到终结点。 可以决定仅将某些事件类型发送到终结点。 例如，可以收到有关资源更新的通知，但不会收到删除等其他操作的通知。 在这种情况下，按 `Microsoft.Resources.ResourceWriteSuccess` 事件类型进行筛选。 提供包含事件类型的数组或指定 `All` 以获取事件源的所有事件类型。

按事件类型筛选的 JSON 语法是：

```json
"filter": {
  "includedEventTypes": [
    "Microsoft.Resources.ResourceWriteFailure",
    "Microsoft.Resources.ResourceWriteSuccess"
  ]
}
```

## <a name="subject-filtering"></a>主题筛选

对于按主题的简单筛选，请指定主题的开头值或结尾值。 例如，可以使用 `.txt` 指定主题的结尾，以仅获取与将文本文件上传到存储帐户相关的事件。 或者，可以筛选以 `/blobServices/default/containers/testcontainer` 开头的主题以获取该容器的所有事件，但不获取存储帐户中的其他容器。

将事件发布到自定义主题时，可为事件创建主题，便于订阅者们了解他们是否对该事件感兴趣。 订阅者使用主题属性来筛选和路由事件。 考虑添加事件发生位置的路径，以便订阅者可以按该路径的段进行筛选。 通过路径，订阅者可精确或宽泛地筛选事件。 如果在主题中提供一个由三个段构成的路径（如 `/A/B/C`），订阅者可根据第一个段 `/A` 进行筛选，获取范围较宽泛的一组事件。 这些订阅者会获取主题为 `/A/B/C` 或 `/A/D/E` 这样的事件。 其他订阅者可通过 `/A/B` 进行筛选，这样可以获取范围更精确的一组事件。

按主题筛选的 JSON 语法是：

```json
"filter": {
  "subjectBeginsWith": "/blobServices/default/containers/mycontainer/log",
  "subjectEndsWith": ".jpg"
}

```

## <a name="advanced-filtering"></a>高级筛选

要按数据字段中的值进行筛选并指定比较运算符，请使用高级筛选选项。 在高级筛选中，指定：

* 运算符类型 - 比较的类型。
* 键 - 用于筛选的事件数据中的字段。 它可以是数字、布尔值或字符串。
* values-要与该键进行比较的值。

如果指定具有多个值的单个筛选器，则将执行 **OR** 操作，因此键字段的值必须是这些值之一。 以下是示例：

```json
"advancedFilters": [
    {
        "operatorType": "StringContains",
        "key": "Subject",
        "values": [
            "/providers/microsoft.devtestlab/",
            "/providers/Microsoft.Compute/virtualMachines/"
        ]
    }
]
```

如果指定多个不同的筛选器，则将执行 **AND** 操作，因此必须满足每个筛选器条件。 以下是示例： 

```json
"advancedFilters": [
    {
        "operatorType": "StringContains",
        "key": "Subject",
        "values": [
            "/providers/microsoft.devtestlab/"
        ]
    },
    {
        "operatorType": "StringContains",
        "key": "Subject",
        "values": [
            "/providers/Microsoft.Compute/virtualMachines/"
        ]
    }
]
```

### <a name="operators"></a>运算符

适用于**数字**的运算符包括：

* NumberGreaterThan
* NumberGreaterThanOrEquals
* NumberLessThan
* NumberLessThanOrEquals
* NumberIn
* NumberNotIn

**布尔**值的可用运算符为： 
- BoolEquals

**字符串**的可用运算符包括：

* StringContains
* StringBeginsWith
* StringEndsWith
* StringIn
* StringNotIn

所有字符串比较都**不**区分大小写。

### <a name="key"></a>Key

对于事件网格架构中的事件，请使用以下键值：

* ID
* 主题
* 使用者
* EventType
* DataVersion
* 事件数据（如 Data.key1）

对于云事件架构中的事件，请使用以下键值：

* EventId
* 源
* EventType
* EventTypeVersion
* 事件数据（如 Data.key1）

对于自定义输入架构，请使用事件数据字段（如 Data.key1）。

### <a name="values"></a>值

值可以是：

* number
* string
* boolean
* array

### <a name="limitations"></a>限制

高级筛选具有以下限制：

* 每个事件网格订阅有五个高级筛选器
* 每个字符串值有 512 个字符
* “in”和“not in”运算符有 5 个值********
* 其中包含** `.` （点）** 字符的键。 例如： `http://schemas.microsoft.com/claims/authnclassreference` 或 `john.doe@contoso.com` 。 当前不支持密钥中的转义符。 

可以在多个筛选器中使用相同的键。

### <a name="examples"></a>示例

### <a name="stringcontains"></a>StringContains

```json
"advancedFilters": [{
    "operatorType": "StringContains",
    "key": "data.key1",
    "values": [
        "microsoft", 
        "azure"
    ]
}]
```

### <a name="stringbeginswith"></a>StringBeginsWith

```json
"advancedFilters": [{
    "operatorType": "StringBeginsWith",
    "key": "data.key1",
    "values": [
        "event", 
        "grid"
    ]
}]
```

### <a name="stringendswith"></a>StringEndsWith

```json
"advancedFilters": [{
    "operatorType": "StringEndsWith",
    "key": "data.key1",
    "values": [
        "jpg", 
        "jpeg", 
        "png"
    ]
}]
```

### <a name="stringin"></a>StringIn

```json
"advancedFilters": [{
    "operatorType": "StringIn",
    "key": "data.key1",
    "values": [
        "exact", 
        "string", 
        "matches"
    ]
}]
```

### <a name="stringnotin"></a>StringNotIn

```json
"advancedFilters": [{
    "operatorType": "StringNotIn",
    "key": "data.key1",
    "values": [
        "aws", 
        "bridge"
    ]
}]
```

### <a name="numberin"></a>NumberIn

```json

"advancedFilters": [{
    "operatorType": "NumberIn",
    "key": "data.counter",
    "values": [
        5,
        1
    ]
}]

```

### <a name="numbernotin"></a>NumberNotIn

```json
"advancedFilters": [{
    "operatorType": "NumberNotIn",
    "key": "data.counter",
    "values": [
        41,
        0,
        0
    ]
}]
```

### <a name="numberlessthan"></a>NumberLessThan

```json
"advancedFilters": [{
    "operatorType": "NumberLessThan",
    "key": "data.counter",
    "value": 100
}]
```

### <a name="numbergreaterthan"></a>NumberGreaterThan

```json
"advancedFilters": [{
    "operatorType": "NumberGreaterThan",
    "key": "data.counter",
    "value": 20
}]
```

### <a name="numberlessthanorequals"></a>NumberLessThanOrEquals

```json
"advancedFilters": [{
    "operatorType": "NumberLessThanOrEquals",
    "key": "data.counter",
    "value": 100
}]
```

### <a name="numbergreaterthanorequals"></a>NumberGreaterThanOrEquals

```json
"advancedFilters": [{
    "operatorType": "NumberGreaterThanOrEquals",
    "key": "data.counter",
    "value": 30
}]
```

### <a name="boolequals"></a>BoolEquals

```json
"advancedFilters": [{
    "operatorType": "BoolEquals",
    "key": "data.isEnabled",
    "value": true
}]
```


## <a name="next-steps"></a>后续步骤

* 要了解有关使用 PowerShell 和 Azure CLI 筛选事件的详细信息，请参阅[筛选事件网格的事件](how-to-filter-events.md)。
* 若要快速开始使用事件网格，请参阅[使用 Azure 事件网格创建和路由自定义事件](custom-event-quickstart.md)。
