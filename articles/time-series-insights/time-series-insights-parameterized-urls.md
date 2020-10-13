---
title: 通过参数化 URL 共享自定义视图 - Azure 时序见解 | Microsoft Docs
description: 了解如何创建参数化 URL，以便在 Azure 时序见解中轻松共享自定义的资源管理器视图。
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.topic: conceptual
ms.workload: big-data
ms.date: 10/02/2020
ms.custom: seodec18
ms.openlocfilehash: 9bf857a66643b1e95ea2559601761a7217babad4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91665321"
---
# <a name="share-a-custom-view-using-a-parameterized-url"></a>通过参数化 URL 共享自定义视图

若要在 Azure 时序见解资源管理器中共享自定义视图，可以通过编程方式创建一个自定义视图的参数化 URL。

Azure 时序见解资源管理器支持使用 URL 查询参数，在 URL 中直接指定体验中的视图。 例如，只使用 URL 就可以指定目标环境、搜索谓词以及所需的时间跨度。 当用户选择自定义的 URL 时，界面会提供一个链接，直接指向 Azure 时序见解门户中的相应资产。 数据访问策略适用。

> [!TIP]
>
> * 查看免费的 [Azure 时序见解演示](https://insights.timeseries.azure.com/samples)。
> * 阅读随附的 [Azure 时序见解资源管理器](./time-series-insights-explorer.md)文档。

## <a name="environment-id"></a>环境 ID

`environmentId=<guid>` 参数指定目标环境 ID。 这是数据访问 FQDN 的一个组件，可以在 Azure 门户的环境概览的右上角找到。 它是 `env.timeseries.azure.com` 之前的所有内容。

例如，`?environmentId=10000000-0000-0000-0000-100000000108` 是环境 ID 参数。

## <a name="time"></a>时间

可以通过参数化 URL 来指定绝对或相对时间值。

### <a name="absolute-time-values"></a>绝对时间值

对于绝对时间值，请使用 `from=<integer>` 和 `to=<integer>` 参数。

* `from=<integer>` 是以 JavaScript 毫秒为单位的一个值，代表搜索时间跨度的开始时间。
* `to=<integer>` 是以 JavaScript 毫秒为单位的一个值，代表搜索时间跨度的结束时间。

> [!TIP]
> 若要轻松地将日期转换为 JavaScript 毫秒，请尝试 [Epoch 与 Unix 时间戳转换器](https://www.freeformatter.com/epoch-timestamp-to-date-converter.html)。

### <a name="relative-time-values"></a>相对时间值

对于相对时间值，请使用 `relativeMillis=<value>`，其中“值”为从 API 收到的最近时间戳的 JavaScript 毫秒  。

例如，`&relativeMillis=3600000` 显示最新的 60 分钟的数据。

接受的值对应于 Azure 时序见解资源管理器的“快速时间”菜单，其中包括：

* `1800000`（过去 30 分钟）
* `3600000`（过去 60 分钟）
* `10800000`（过去 3 小时）
* `21600000`（过去 6 小时）
* `43200000`（过去 12 小时）
* `86400000`（过去 24 小时）
* `604800000`（过去 7 天）
* `2592000000`（过去 30 小时）

### <a name="optional-parameters"></a>可选参数

`timeSeriesDefinitions=<collection of term objects>` 参数指定将在 Azure 时序见解视图中显示的谓词术语：

| 参数 | URL 项 | 说明 |
| --- | --- | --- |
| **name** | `\<string>` | 术语的名称。  |
| **splitBy** | `\<string>` | 要按其进行拆分的列名。  |
| **measureName** | `\<string>` | 度量值的列名。  |
| **predicate** | `\<string>` | 用于服务器端筛选的  where 子句。 |
| **useSum** | `true` | 一个可选参数，指定对度量值使用总和。 |

> [!NOTE]
> 如果所选 useSum 度量值为 `Events`，则默认选择计数  。  
> 如果未选择 `Events`，则默认选择平均值。 |

* `multiChartStack=<true/false>` 键值对在图表中启用堆栈。
* `multiChartSameScale=<true/false>` 键值对不同可选参数的条件启用相同的 Y 轴比例。  
* 可以通过 `timeBucketUnit=<Unit>&timeBucketSize=<integer>` 来调整时间间隔滑块，使图表的视图更精细或更光滑，聚合程度更高。  
* 可以通过 `timezoneOffset=<integer>` 参数将显示图表的时区设置为 UTC 的偏移量。

| 对 | 说明 |
| --- | --- |
| `multiChartStack=false` | `true` 是默认启用的，因此请将 `false` 传递到堆栈。 |
| `multiChartStack=false&multiChartSameScale=true` | 必须启用堆叠才能在不同的条件中使用同一 Y 轴比例。  它在默认情况下为 `false`，因此传递 `true` 即可启用此功能。 |
| `timeBucketUnit=<Unit>&timeBucketSize=<integer>` | 单位 = `days`、`hours`、`minutes`、`seconds`、`milliseconds`。  请始终将单位大写。 </br> 为 timeBucketSize 传递所需的整数即可定义单位数  。  |
| `timezoneOffset=-<integer>` | 整数的单位始终为毫秒。 |

> [!NOTE]
> timeBucketUnit 值最多可以平滑为 7 天  。
> timezoneOffset 值既不是 UTC 也不是本地时间  。

### <a name="examples"></a>示例

若要将时序定义以 URL 参数的形式添加到 Azure 时序见解环境，请追加：

```URL parameter
&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},
{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

将示例时序定义用于：

* 环境 ID
* 过去 60 分钟的数据
* 构成可选参数的术语（F1PressureID、F2TempStation 和 F3VibrationPL）   

可以为视图构造以下参数化 URL：

```URL
https://insights.timeseries.azure.com/classic/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

[![Azure 时序见解资源管理器参数化 URL](media/parameterized-url/share-parameterized-url.png)](media/parameterized-url/share-parameterized-url.png#lightbox)

> [!TIP]
> [使用 URL](https://insights.timeseries.azure.com/classic/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[%7B%22name%22:%22F1PressureId%22,%22splitBy%22:%22Id%22,%22measureName%22:%22Pressure%22,%22predicate%22:%22%27Factory1%27%22%7D,%7B%22name%22:%22F2TempStation%22,%22splitBy%22:%22Station%22,%22measureName%22:%22Temperature%22,%22predicate%22:%22%27Factory2%27%22%7D,%7B%22name%22:%22F3VibrationPL%22,%22splitBy%22:%22ProductionLine%22,%22measureName%22:%22Vibration%22,%22predicate%22:%22%27Factory3%27%22%7D]
) 上方示例查看实时资源管理器。

上述 URL 描述并显示参数化的 Azure 时序见解资源管理器视图。

* 参数化谓词。

  [![Azure 时序见解资源管理器参数化谓词。](media/parameterized-url/share-parameterized-url-predicates.png)](media/parameterized-url/share-parameterized-url-predicates.png#lightbox)

* 共享的完整图表视图。

  [![共享的完整图表视图。](media/parameterized-url/share-parameterized-url-full-chart.png)](media/parameterized-url/share-parameterized-url-full-chart.png#lightbox)

## <a name="next-steps"></a>后续步骤

* 了解如何[使用 C# 查询数据](time-series-insights-query-data-csharp.md)。

* 了解 [Azure 时序见解资源管理器](./time-series-insights-explorer.md)。
