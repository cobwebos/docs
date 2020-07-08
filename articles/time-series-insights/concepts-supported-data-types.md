---
title: 支持的数据类型-Azure 时序见解 |Microsoft Docs
description: 了解 Azure 时序见解预览版中支持的数据类型。
author: lyrana
ms.author: lyhughes
manager: deepakpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 06/03/2020
ms.custom: seodec18
ms.openlocfilehash: 1a1ab9fa19956341e930c85d13d7067be4449cea
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2020
ms.locfileid: "86049777"
---
# <a name="supported-data-types"></a>支持的数据类型

下表列出了时序见解支持的数据类型

| 数据类型 | 说明 | 示例 | Parquet 中的属性列名称
|---|---|---|---|
| **bool** | 具有两种状态之一的数据类型：`true` 或 `false`。 | "isQuestionable"： true | isQuestionable_bool
| **datetime** | 表示时间上的一刻，通常以日期和当天的时间表示。 以 [ISO 8601](https://www.iso.org/iso-8601-date-and-time-format.html) 格式表示。 Datetime 属性始终以 UTC 格式存储。 如果格式正确，时区偏移量将被应用，然后是以 UTC 格式存储的值。 有关环境时间戳属性和日期时间偏移的详细信息，请参阅[此](concepts-streaming-ingestion-event-sources.md#event-source-timestamp)部分 | "eventProcessedLocalTime"： "2020-03-20T09：03： 32.8301668 Z" | eventProcessedLocalTime_datetime 
| **double** | 双精度64位数字  | "value"：31.0482941 | value_double
| **long** | 已签名的64位整数  | "value"：31 | value_long
| **string** | 文本值必须包含有效 UTF-8。 |  "站点"： "DIM_MLGGG" | site_string
| **dynamic** | 由数组或属性包（字典）组成的复杂（非基元）类型。 目前只能将基元的字符串化 JSON 数组或不包含 TS ID 或时间戳属性的对象数组存储为动态。 阅读[本文](./concepts-json-flattening-escaping-rules.md)以了解如何平展对象以及如何取消对数组进行汇总 |  "values"： "[197，194，189，188]" | values_dynamic

> [!IMPORTANT]
>
> * 你的 TSI 环境为强类型。 如果设备或标记同时发送整型和非整型数据，则设备属性值将存储在两个分隔的 double 列中，并且在进行 API 调用和定义时序模型变量表达式时，应使用[联合（）函数](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax)。

#### <a name="objects-and-arrays"></a>对象和数组

您可以在事件负载中发送复杂类型（如对象和数组）。 嵌套对象将被平展，并且数组将存储为 `dynamic` 或平展，以生成多个事件，具体取决于您的环境配置和 JSON 形状。 若要了解有关[JSON 平展和转义规则](./concepts-json-flattening-escaping-rules.md)的详细信息，请参阅

## <a name="next-steps"></a>后续步骤

* 阅读[JSON 平展和转义规则](./concepts-json-flattening-escaping-rules.md)以了解如何存储事件。 

* 了解环境的[吞吐量限制](concepts-streaming-throughput-limitations.md)

* 了解用于引入流式处理数据的[事件源](concepts-streaming-ingestion-event-sources.md)。
