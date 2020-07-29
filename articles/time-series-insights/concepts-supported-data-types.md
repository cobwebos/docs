---
title: 支持的数据类型-Azure 时序见解 Gen2 |Microsoft Docs
description: 了解 Azure 时序见解 Gen2 中支持的数据类型。
author: lyrana
ms.author: lyhughes
manager: deepakpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 07/23/2020
ms.openlocfilehash: 9d29d83ed92ee0fa2217bb1c27fbf6c2fbb3584c
ms.sourcegitcommit: d7bd8f23ff51244636e31240dc7e689f138c31f0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/24/2020
ms.locfileid: "87170881"
---
# <a name="supported-data-types"></a>支持的数据类型

下表列出了 Azure 时序见解 Gen2 支持的数据类型

| 数据类型 | 说明 | 示例 | Parquet 中的属性列名称
|---|---|---|---|
| **bool** | 具有两种状态之一的数据类型：`true` 或 `false`。 | `"isQuestionable" : true` | isQuestionable_bool
| **datetime** | 表示时间上的一刻，通常以日期和当天的时间表示。 以 [ISO 8601](https://www.iso.org/iso-8601-date-and-time-format.html) 格式表示。 Datetime 属性始终以 UTC 格式存储。 如果格式正确，时区偏移量将被应用，然后是以 UTC 格式存储的值。 有关环境时间戳属性和日期时间偏移的详细信息，请参阅[此](concepts-streaming-ingestion-event-sources.md#event-source-timestamp)部分 | `"eventProcessedLocalTime": "2020-03-20T09:03:32.8301668Z"` | eventProcessedLocalTime_datetime
| **double** | 双精度64位数字  | `"value": 31.0482941` | value_double
| **long** | 已签名的64位整数  | `"value" : 31` | value_long
| **string** | 文本值必须包含有效 UTF-8。 空字符串和空字符串的处理方式相同。 |  `"site": "DIM_MLGGG"` | site_string
| **dynamic** | 由数组或属性包（字典）组成的复杂（非基元）类型。 目前只能将基元的字符串化 JSON 数组或不包含 TS ID 或时间戳属性的对象数组存储为动态数组。 阅读[本文](./concepts-json-flattening-escaping-rules.md)以了解对象将如何平展，并且数组可能 unrolled。 作为此类型存储的负载属性可通过 Azure 时序见解 Gen2 资源管理器和 `GetEvents`   查询 API 进行访问。 |  `"values": "[197, 194, 189, 188]"` | values_dynamic

## <a name="sending-mixed-data-types"></a>发送混合数据类型

Azure 时序见解 Gen2 环境为强类型。 如果设备或标记为设备属性发送不同类型的数据，则值将存储在两个分隔列中，并且应在 API 调用中定义时序模型变量表达式时使用[联合（）函数](https://docs.microsoft.com/rest/api/time-series-insights/preview#other-functions)。

Azure 时序见解资源管理器提供了一种方法来自动合并同一设备属性的单独列。 在下面的示例中，传感器将发送一个 `PresentValue` 属性，该属性可以是 Long 或 Double。 若要查询属性的所有存储值（不管数据类型如何） `PresentValue` ，请选择 `PresentValue (Double | Long)` ，将为您合并列。

[![资源管理器自动合并](media\concepts-supported-data-types/explorer-auto-coalesce-sample.png)](media\concepts-supported-data-types/explorer-auto-coalesce-sample.png#lightbox)

## <a name="objects-and-arrays"></a>对象和数组

您可以在事件负载中发送复杂类型（如对象和数组）。 嵌套对象将被平展，并且数组将存储为 `dynamic` 或平展，以生成多个事件，具体取决于您的环境配置和 JSON 形状。 若要了解有关[JSON 平展和转义规则](./concepts-json-flattening-escaping-rules.md)的详细信息，请参阅

## <a name="next-steps"></a>后续步骤

* 阅读[JSON 平展和转义规则](./concepts-json-flattening-escaping-rules.md)以了解如何存储事件。

* 了解环境的[吞吐量限制](./concepts-streaming-ingress-throughput-limits.md)

* 了解用于引入流式处理数据的[事件源](concepts-streaming-ingestion-event-sources.md)。
