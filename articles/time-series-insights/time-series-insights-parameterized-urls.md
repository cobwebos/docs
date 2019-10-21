---
title: 通过参数化 URL 共享 Azure 时序见解自定义视图 | Microsoft Docs
description: 本文介绍如何在 Azure 时序见解中生成参数化 URL，以便轻松共享自定义视图。
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.topic: conceptual
ms.workload: big-data
ms.date: 10/18/2019
ms.custom: seodec18
ms.openlocfilehash: e862cd604263c5125259344baf2e990ad56a3291
ms.sourcegitcommit: 9a4296c56beca63430fcc8f92e453b2ab068cc62
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/20/2019
ms.locfileid: "72675828"
---
# <a name="share-a-custom-view-using-a-parameterized-url"></a>通过参数化 URL 共享自定义视图

若要在时序见解资源管理器中共享自定义视图，可以通过编程方式创建自定义视图的参数化 URL。

时序见解资源管理器支持 URL 查询参数，以便直接从 URL 指定体验中的视图。 例如，只使用 URL 就可以指定目标环境、搜索谓词以及所需的时间跨度。 当用户选择自定义 URL 时，该界面会直接在时序见解门户中提供指向该资产的链接。 数据访问策略适用。

> [!TIP]
> * 查看免费时序[见解演示](https://insights.timeseries.azure.com/samples)。
> * 阅读随附的时序[见解资源管理器](./time-series-insights-explorer.md)文档。

## <a name="environment-id"></a>环境 ID

`environmentId=<guid>` 参数指定目标环境 ID。 它是数据访问 FQDN 的一个组件，你可以在 Azure 门户中环境概述的右上角找到它。 它是 `env.timeseries.azure.com` 之前的所有内容。

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

接受的值对应于时序见解资源管理器的 "**快速时间**" 菜单，包括：

* `1800000` （过去30分钟）
* `3600000` （过去60分钟）
* `10800000` （过去3小时）
* `21600000` （过去6小时）
* `43200000` （过去12小时）
* `86400000` （最近24小时）
* `604800000` （过去7天）
* `2592000000` （过去30小时）

### <a name="optional-parameters"></a>可选参数

@No__t_0 参数指定时序见解视图的条款：

| 参数 | URL 项 | 描述 |
| --- | --- | --- |
| name | `\<string>` | 术语的名称。 |
| **splitBy** | `\<string>` | 要按其进行拆分的列名。 |
| **measureName** | `\<string>` | 度量值的列名。 |
| **推断** | `\<string>` | 用于服务器端筛选的where 子句。 |
| **useSum** | `true` | 一个可选参数，它指定度量值的使用 sum。 </br>  请注意，如果 `Events` 是所选度量值，则默认情况下将选择 "计数"。  </br>  如果未选择 `Events`，则默认情况下将选择 "平均值"。 |

* @No__t_0 键-值对在图表中启用堆栈。
* @No__t_0 键-值对在可选参数内的各项之间实现相同的 Y 轴比例。  
* 使用 `timeBucketUnit=<Unit>&timeBucketSize=<integer>` 可以调整间隔滑块，以提供更精细或更平滑、更多、更多的图表视图。  
* 使用 `timezoneOffset=<integer>` 参数，您可以将图表的时区设置为作为 UTC 的偏移量进行查看。

| 对（s） | 描述 |
| --- | --- |
| `multiChartStack=false` | 默认情况下启用 `true`，因此将 `false` 传递到堆栈。 |
| `multiChartStack=false&multiChartSameScale=true` | 必须启用堆叠才能在不同的条件中使用同一 Y 轴比例。  默认情况下，它是 `false` 的，因此传递 "true" 可启用此功能。 |
| `timeBucketUnit=<Unit>&timeBucketSize=<integer>` | 单位为天、小时、分钟、秒、毫秒。  请始终将单位大写。 </br> 为 timeBucketSize 传递所需的整数即可定义单位数。  注意，平滑度最多可以调整为 7 天。  |
| `timezoneOffset=-<integer>` | 整数的单位始终为毫秒。 </br> 请注意，此功能与我们在时序见解资源管理器中启用的功能略有不同，我们可以在其中选择本地（浏览器时间）或 UTC。 |

### <a name="examples"></a>示例

若要将时序定义作为 URL 参数添加到时序见解环境，请追加：

```plaintext
&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},
{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

使用的示例时序定义：

* 环境 ID
* 最后60分钟的数据
* 构成可选参数的术语（F1PressureID、F2TempStation 和 F3VibrationPL）

可以为视图构造以下参数化 URL：

```plaintext
https://insights.timeseries.azure.com/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

> [!TIP]
> [使用 URL](https://insights.timeseries.azure.com/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}])查看 "资源管理器"。

上述 URL 介绍并构建时序见解资源管理器视图：

[![Time Series Insights 资源管理器术语](media/parameterized-url/url1.png)](media/parameterized-url/url1.png#lightbox)

完整视图（包括图表）：

[![Chart 视图](media/parameterized-url/url2.png)](media/parameterized-url/url2.png#lightbox)

## <a name="next-steps"></a>后续步骤

* 了解如何[ C#使用查询数据](time-series-insights-query-data-csharp.md)。

* 了解[时序见解资源管理器](./time-series-insights-explorer.md)。
