---
title: 支持的数据类型 - Azure 时序见解第 2 代 | Microsoft Docs
description: 了解 Azure 时序见解第 2 代中支持的数据类型。
author: lyrana
ms.author: lyhughes
manager: deepakpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 08/12/2020
ms.openlocfilehash: e6fd405d1969a2f40a5f0c3466a57fbec60723e9
ms.sourcegitcommit: a2a7746c858eec0f7e93b50a1758a6278504977e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/12/2020
ms.locfileid: "88141153"
---
# <a name="supported-data-types"></a>支持的数据类型

下表列出了 Azure 时序见解第 2 代支持的数据类型

| 数据类型 | 说明 | 示例 | Parquet 中的属性列名称
|---|---|---|---|
| **bool** | 具有两种状态之一的数据类型：`true` 或 `false`。 | `"isQuestionable" : true` | isQuestionable_bool
| **datetime** | 表示某个时刻，通常以日期和当天的时间表示。 以 [ISO 8601](https://www.iso.org/iso-8601-date-and-time-format.html) 格式表示。 日期/时间属性始终以 UTC 格式存储。 如果格式正确，将应用时区偏移，然后以 UTC 格式存储值。 有关环境时间戳属性和日期/时间偏移的详细信息，请参阅[此](concepts-streaming-ingestion-event-sources.md#event-source-timestamp)部分 | `"eventProcessedLocalTime": "2020-03-20T09:03:32.8301668Z"` | eventProcessedLocalTime_datetime
| **double** | 一个双精度 64 位数字  | `"value": 31.0482941` | value_double
| **long** | 已签名的 64 位整数  | `"value" : 31` | value_long
| **string** | 文本值，必须包含有效的 UTF-8。 Null 字符串和空字符串的处理方式相同。 |  `"site": "DIM_MLGGG"` | site_string
| **dynamic** | 一个复杂的（非基元）类型，由数组或属性包（字典）组成。 目前只会将基元的字符串化 JSON 数组或不包含 TS ID 或时间戳属性的对象数组存储为动态数组。 请阅读此[文章](./concepts-json-flattening-escaping-rules.md)，了解对象如何平展，以及数组如何展开。 作为此类型存储的负载属性可通过 Azure 时序见解 Gen2 资源管理器和 `GetEvents`   查询 API 进行访问。 |  `"values": "[197, 194, 189, 188]"` | values_dynamic

## <a name="sending-mixed-data-types"></a>发送混合数据类型

Azure 时序见解第 2 代环境为强类型环境。 如果设备或标记为某个设备属性发送不同类型的数据，则值会存储在两个不同的列中，而你在 API 调用中定义时序模型变量表达式时应使用 [coalesce() 函数](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax#other-functions)。

Azure 时序见解资源管理器提供了一种方法来自动联合同一设备属性的不同列。 在下面的示例中，传感器会发送一个 `PresentValue` 属性，该属性可以是 Long 或 Double。 若要查询 `PresentValue` 属性的所有已存储值（不管数据类型如何），请选择 `PresentValue (Double | Long)`，系统会替你联合这些列。

[![资源管理器自动联合](media\concepts-supported-data-types/explorer-auto-coalesce-sample.png)](media\concepts-supported-data-types/explorer-auto-coalesce-sample.png#lightbox)

## <a name="objects-and-arrays"></a>对象和数组

你可以在事件有效负载中发送复杂类型，例如对象和数组。 嵌套对象将被平展，数组将存储为 `dynamic` 或进行平展以生成多个事件，具体取决于你的环境配置和 JSON 形状。 若要了解详细信息，请阅读 [JSON 平展和转义规则](./concepts-json-flattening-escaping-rules.md)

## <a name="next-steps"></a>后续步骤

* 阅读 [JSON 平展和转义规则](./concepts-json-flattening-escaping-rules.md)以了解事件将如何存储。

* 了解你的环境的[吞吐量限制](./concepts-streaming-ingress-throughput-limits.md)

* 了解[事件源](concepts-streaming-ingestion-event-sources.md)以引入流数据。
