---
title: 与参数化 Url 共享自定义视图-Azure 时序见解 |Microsoft Docs
description: 了解如何创建参数化 Url，以便在 Azure 时序见解中轻松共享自定义的资源管理器视图。
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.topic: conceptual
ms.workload: big-data
ms.date: 12/12/2019
ms.custom: seodec18
ms.openlocfilehash: fd6de7dfe9509e7f99adeed0e5de3e157335e6bf
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75452814"
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

> [!TIP]
> 若要轻松地将日期转换为 JavaScript 毫秒，请尝试[Epoch & Unix 时间戳转换器](https://www.freeformatter.com/epoch-timestamp-to-date-converter.html)。

### <a name="relative-time-values"></a>相对时间值

对于相对时间值，请使用 `relativeMillis=<value>`，其中*值*为从 API 接收到的最近时间戳的 JavaScript 毫秒。

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

`timeSeriesDefinitions=<collection of term objects>` 参数指定将在时序见解视图中显示的谓词术语：

| 参数 | URL 项 | Description |
| --- | --- | --- |
| name | `\<string>` | 术语的名称。 |
| **splitBy** | `\<string>` | 要按其进行拆分的列名。 |
| **measureName** | `\<string>` | 度量值的列名。 |
| **predicate** | `\<string>` | 用于服务器端筛选的where 子句。 |
| **useSum** | `true` | 一个可选参数，它指定度量值的使用 sum。 |

> [!NOTE]
> 如果 `Events` 是所选**useSum**度量值，则默认情况下将选择 "计数"。  
> 如果未选择 `Events`，则默认情况下将选择 "平均值"。 |

* `multiChartStack=<true/false>` 键-值对在图表中启用堆栈。
* `multiChartSameScale=<true/false>` 键-值对在可选参数内的各项之间实现相同的 Y 轴比例。  
* 使用 `timeBucketUnit=<Unit>&timeBucketSize=<integer>` 可以调整间隔滑块，以提供更精细或更平滑、更多、更多的图表视图。  
* 使用 `timezoneOffset=<integer>` 参数，您可以将图表的时区设置为作为 UTC 的偏移量进行查看。

| 对（s） | Description |
| --- | --- |
| `multiChartStack=false` | 默认情况下启用 `true`，因此将 `false` 传递到堆栈。 |
| `multiChartStack=false&multiChartSameScale=true` | 必须启用堆叠才能在不同的条件中使用同一 Y 轴比例。  默认情况下，它是 `false` 的，因此传递 `true` 启用此功能。 |
| `timeBucketUnit=<Unit>&timeBucketSize=<integer>` | 单位 = `days`、`hours`、`minutes`、`seconds`、`milliseconds`。  请始终将单位大写。 </br> 通过为**timeBucketSize**传递所需的整数来定义单位数。  |
| `timezoneOffset=-<integer>` | 整数的单位始终为毫秒。 |

> [!NOTE]
> **timeBucketUnit**值最多可以平滑7天。
> **timezoneOffset**值既不是 UTC，也不是本地时间。

### <a name="examples"></a>示例

若要将时序定义作为 URL 参数添加到时序见解环境，请追加：

```URL parameter
&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},
{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

使用的示例时序定义：

* 环境 ID
* 最后60分钟的数据
* 构成可选参数的术语（**F1PressureID**、 **F2TempStation**和**F3VibrationPL**）

可以为视图构造以下参数化 URL：

```URL
https://insights.timeseries.azure.com/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

[![时序见解资源管理器参数 URL](media/parameterized-url/share-parameterized-url.png)](media/parameterized-url/share-parameterized-url.png#lightbox)

> [!TIP]
> [使用上述 URL](https://insights.timeseries.azure.com/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}])示例查看资源管理器实时。

上述 URL 描述并显示参数化时序见解资源管理器视图。 

* 参数化谓词。

  [![时序见解资源管理器参数化谓词。](media/parameterized-url/share-parameterized-url-predicates.png)](media/parameterized-url/share-parameterized-url-predicates.png#lightbox)

* 共享的完整图表视图。

  [![共享的完整图表视图。](media/parameterized-url/share-parameterized-url-full-chart.png)](media/parameterized-url/share-parameterized-url-full-chart.png#lightbox)

## <a name="next-steps"></a>后续步骤

* 了解如何[ C#使用查询数据](time-series-insights-query-data-csharp.md)。

* 了解[时序见解资源管理器](./time-series-insights-explorer.md)。
