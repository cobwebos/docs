---
title: "通过参数化 URL 共享 Azure 时序见解自定义视图 | Microsoft Docs"
description: "本文介绍如何在 Azure 时序见解中生成参数化 URL，以便轻松共享自定义视图。"
services: time-series-insights
ms.service: time-series-insights
author: kfile
ms.author: kfile
manager: jhubbard
editor: MicrosoftDocs/tsidocs
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: rest-api
ms.topic: get-started-article
ms.workload: big-data
ms.date: 11/15/2017
ms.openlocfilehash: d7a4401ff87a94265c588aa5ef070bdc8544fe12
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/15/2017
---
# <a name="share-a-custom-view-using-a-parameterized-url"></a>通过参数化 URL 共享自定义视图

若要在时序见解资源管理器中共享自定义视图，可以通过编程方式创建一个自定义视图的参数化 URL。

时序见解资源管理器支持使用 URL 查询参数，在 URL 中直接指定体验中的视图。  例如，只使用 URL 就可以指定目标环境、搜索谓词以及所需的时间跨度。 当用户单击自定义 URL 时，界面会提供一个链接，直接指向时序见解门户中的相应资产。  数据访问策略适用。 

## <a name="environment-id"></a>环境 ID

`environmentId=<guid>` 参数指定目标环境 ID。  这是数据访问 FQDN 的一个组件，可以在 Azure 门户的环境概览的右上角找到。  它是 `env.timeseries.azure.com` 之前的所有内容。 例如，`?environmentId=10000000-0000-0000-0000-100000000108` 是环境 ID 参数。

## <a name="time"></a>时间

可以通过参数化 URL 来指定绝对或相对时间值。

### <a name="absolute-time-values"></a>绝对时间值

对于绝对时间值，请使用 `from=<integer>` 和 `to=<integer>` 参数。 

`from=<integer>` 是以 JavaScript 毫秒为单位的一个值，代表搜索时间跨度的开始时间。

`to=<integer>` 是以 JavaScript 毫秒为单位的一个值，代表搜索时间跨度的结束时间。 

若要确定某个日期对应的 JavaScript 毫秒，请参阅[纪元和 Unix 时间戳转换器](https://www.freeformatter.com/epoch-timestamp-to-date-converter.html)。

### <a name="relative-time-values"></a>相对时间值

对于相对时间值，请使用 `relativeMillis=<value>`，其中 value 为 JavaScript 毫秒，来自后端的最新数据。

例如，`&relativeMillis=3600000` 显示最新的 60 分钟的数据。

接受的值对应于时序见解资源管理器的“快速时间”菜单，包括以下内容：

- 1800000（过去 30 分钟）
- 3600000（过去 60 分钟）
- 10800000（过去 3 小时）
- 21600000（过去 6 小时）
- 43200000（过去 12 小时）
- 86400000（过去 24 小时）
- 604800000（过去 7 天）
- 2592000000（过去 30 小时）

### <a name="optional-parameters"></a>可选参数

`timeSeriesDefinitions=<collection of term objects>` 参数指定时序见解视图的术语，其中：

- `name=<string>`
  - 术语的名称。
- `splitBy=<string>`
  - 要按其进行拆分的列名。
- `measureName=<string>`
  - 度量值的列名。
- `predicate=<string>`
  - 用于服务器端筛选的 where 子句。

 
### <a name="examples"></a>示例

例如，若要以 URL 参数形式添加时序定义，可以使用以下语句：

```
&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},
{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

将这些示例时序定义用于 

- 环境 ID
- 过去 60 分钟的数据
- 构成可选参数的术语（F1PressureID、F2TempStation 和 F3VibrationPL）
 
可以为视图构造以下参数化 URL：

`https://insights.timeseries.azure.com/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]`

如果已使用时序见解资源管理器生成前面的 URL 所描述的视图，该视图将如下所示：

![时序见解资源管理器术语](media/parameterized-url/url1.png)

完整视图（包括图表）将如下所示：

![图表视图](media/parameterized-url/url2.png)

## <a name="next-steps"></a>后续步骤
[使用 C# 查询数据](time-series-insights-query-data-csharp.md)
