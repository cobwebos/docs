---
title: 通过参数化 URL 共享 Azure 时序见解自定义视图 | Microsoft Docs
description: 本文介绍如何在 Azure 时序见解中生成参数化 URL，以便轻松共享自定义视图。
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.topic: conceptual
ms.workload: big-data
ms.date: 10/18/2019
ms.custom: seodec18
ms.openlocfilehash: e62455a0c8412a579c0fab9d7fabd6016f47dbf3
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/28/2019
ms.locfileid: "72991109"
---
# <a name="share-a-custom-view-using-a-parameterized-url"></a>通过参数化 URL 共享自定义视图

若要在时序见解资源管理器中共享自定义视图，可以通过编程方式创建自定义视图的参数化 URL。

时序见解资源管理器支持使用 URL 查询参数，在 URL 中直接指定体验中的视图。 例如，只使用 URL 就可以指定目标环境、搜索谓词以及所需的时间跨度。 当用户选择自定义的 URL 时，界面会提供一个链接，直接指向时序见解门户中的相应资产。 数据访问策略适用。

> [!TIP]
> * 查看免费的[时序见解演示](https://insights.timeseries.azure.com/samples)。
> * 阅读随附的[时序见解资源管理器](./time-series-insights-explorer.md)文档。

## <a name="environment-id"></a>环境 ID

`environmentId=<guid>` 参数指定目标环境 ID。 这是数据访问 FQDN 的一个组件，可以在 Azure 门户的环境概览的右上角找到。 它是 `env.timeseries.azure.com` 之前的所有内容。

例如，`?environmentId=10000000-0000-0000-0000-100000000108` 是环境 ID 参数。

## <a name="time"></a>时间

可以通过参数化 URL 来指定绝对或相对时间值。

### <a name="absolute-time-values"></a>绝对时间值

对于绝对时间值，请使用 `from=<integer>` 和 `to=<integer>` 参数。

* `from=<integer>` 是以 JavaScript 毫秒为单位的一个值，代表搜索时间跨度的开始时间。
* `to=<integer>` 是以 JavaScript 毫秒为单位的一个值，代表搜索时间跨度的结束时间。

若要确定某个日期对应的 JavaScript 毫秒，请参阅[纪元和 Unix 时间戳转换器](https://www.freeformatter.com/epoch-timestamp-to-date-converter.html)。

### <a name="relative-time-values"></a>相对时间值

对于相对时间值，请使用 `relativeMillis=<value>`，其中value 为 JavaScript 毫秒，来自后端的最新数据。

例如，`&relativeMillis=3600000` 显示最新的 60 分钟的数据。

接受的值对应于时序见解资源管理器的“快速时间”菜单，其中包括：

* `1800000`（过去 30 分钟）
* `3600000`（过去 60 分钟）
* `10800000`（过去 3 小时）
* `21600000`（过去 6 小时）
* `43200000`（过去 12 小时）
* `86400000`（过去 24 小时）
* `604800000`（过去 7 天）
* `2592000000`（过去 30 小时）

### <a name="optional-parameters"></a>可选参数

`timeSeriesDefinitions=<collection of term objects>` 参数指定时序见解视图的术语：

| 参数 | URL 项 | 说明 |
| --- | --- | --- |
| **name** | `\<string>` | 术语的名称。 |
| **splitBy** | `\<string>` | 要按其进行拆分的列名。 |
| **measureName** | `\<string>` | 度量值的列名。 |
| **predicate** | `\<string>` | 用于服务器端筛选的where 子句。 |
| **useSum** | `true` | 一个可选参数，指定对度量值使用总和。 </br>  请注意，如果所选度量值为 `Events`，则默认选择计数。  </br>  如果未选择 `Events`，则默认选择平均值。 |

* `multiChartStack=<true/false>` 键值对在图表中启用堆栈。
* `multiChartSameScale=<true/false>` 键值对不同可选参数的条件启用相同的 Y 轴比例。  
* 可以通过 `timeBucketUnit=<Unit>&timeBucketSize=<integer>` 来调整时间间隔滑块，使图表的视图更精细或更光滑，聚合程度更高。  
* 可以通过 `timezoneOffset=<integer>` 参数将显示图表的时区设置为 UTC 的偏移量。

| 对 | 说明 |
| --- | --- |
| `multiChartStack=false` | `true` 是默认启用的，因此请将 `false` 传递到堆栈。 |
| `multiChartStack=false&multiChartSameScale=true` | 必须启用堆叠才能在不同的条件中使用同一 Y 轴比例。  它在默认情况下为 `false`，因此传递“true”即可启用此功能。 |
| `timeBucketUnit=<Unit>&timeBucketSize=<integer>` | 单位为天、小时、分钟、秒、毫秒。  请始终将单位大写。 </br> 为 timeBucketSize 传递所需的整数即可定义单位数。  注意，平滑度最多可以调整为 7 天。  |
| `timezoneOffset=-<integer>` | 整数的单位始终为毫秒。 </br> 注意，此功能与我们在时序见解资源管理器中启用的功能稍有差别，该资源管理器允许选择本地浏览器时间或 UTC。 |

### <a name="examples"></a>示例

若要将时序定义以 URL 参数的形式添加到时序见解环境，请追加：

```plaintext
&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},
{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

将示例时序定义用于：

* 环境 ID
* 过去 60 分钟的数据
* 构成可选参数的术语（F1PressureID、F2TempStation 和 F3VibrationPL）

可以为视图构造以下参数化 URL：

```plaintext
https://insights.timeseries.azure.com/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

> [!TIP]
> [使用 URL](https://insights.timeseries.azure.com/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]) 查看实时资源管理器。

上述 URL 描述并生成时序见解资源管理器视图：

[![时序见解资源管理器术语](media/parameterized-url/url1.png)](media/parameterized-url/url1.png#lightbox)

完整视图（包括图表）：

[![图表视图](media/parameterized-url/url2.png)](media/parameterized-url/url2.png#lightbox)

## <a name="next-steps"></a>后续步骤

* 了解如何[使用 C# 查询数据](time-series-insights-query-data-csharp.md)。

* 了解[时序见解资源管理器](./time-series-insights-explorer.md)。
