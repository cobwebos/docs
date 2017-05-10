---
title: "Azure Application Insights 中 Analytics 的演示 | Microsoft Docs"
description: "Analytics 是 Application Insights 的强大搜索工具，本演示提供了 Analytics 中所有主要查询的简短示例。"
services: application-insights
documentationcenter: 
author: alancameronwills
manager: carmonm
ms.assetid: bddf4a6d-ea8d-4607-8531-1fe197cc57ad
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/06/2017
ms.author: awills
ms.translationtype: Human Translation
ms.sourcegitcommit: 538f282b28e5f43f43bf6ef28af20a4d8daea369
ms.openlocfilehash: baa8880e47c827e09f6027637d73f2522fec60b9
ms.contentlocale: zh-cn
ms.lasthandoff: 04/07/2017


---
# <a name="a-tour-of-analytics-in-application-insights"></a>Application Insights 中 Analytics 的演示
[Analytics](app-insights-analytics.md) 是 [Application Insights](app-insights-overview.md) 的强大搜索功能。 这些页面介绍 Analytics 查询语言。

* **[观看介绍视频](https://applicationanalytics-media.azureedge.net/home_page_video.mp4)**。
* **[针对模拟数据测试驱动 Analytics](https://analytics.applicationinsights.io/demo)**（如果应用尚未将数据发送到 Application Insights）。
* **[SQL 用户的备忘单](https://aka.ms/sql-analytics)**转换最常见的惯用语言。

让我们一起来逐步了解一些基本查询，帮助你入门。

## <a name="connect-to-your-application-insights-data"></a>连接到 Application Insights 数据
在 Application Insights 中从应用的[概述边栏选项卡](app-insights-dashboards.md)打开 Analytics：

![依次打开 portal.azure.com 和 Application Insights 资源，然后单击“Analytics”。](./media/app-insights-analytics-tour/001.png)

## <a name="takeapp-insights-analytics-referencemdtake-operator-show-me-n-rows"></a>[Take](app-insights-analytics-reference.md#take-operator)：显示 n 行
记录用户操作的数据点（通常是 Web 应用收到的 HTTP 请求）存储在名为 `requests` 的表中。 每一行均为应用从 Application Insights SDK 接收的遥测数据点。

先来检查一下表中的几个示例行：

![结果](./media/app-insights-analytics-tour/010.png)

> [!NOTE]
> 先将光标置于语句中的某一位置，然后单击“转到”。 可将一个语句拆分为多行，但请勿在语句中放置空白行。 使用空白行便于在窗口中保留多个单独查询。
>
>

选择列、拖动列、按列分组并进行筛选：

![在结果的右上角单击列选择](./media/app-insights-analytics-tour/030.png)

展开任意项，查看详细信息：

![选择“表”，并使用“配置列”](./media/app-insights-analytics-tour/040.png)

> [!NOTE]
> 单击列标头，重新排列 Web 浏览器中的结果。 但请注意：对于大型结果集，会限制下载到浏览器的行数。 按此方式排序不一定会显示实际的最高或最低项。 若要对项进行可靠地排序，请使用 `top` 或 `sort` 运算符。
>
>

## <a name="topapp-insights-analytics-referencemdtop-operator-and-sortapp-insights-analytics-referencemdsort-operator"></a>[Top](app-insights-analytics-reference.md#top-operator) 和 [sort](app-insights-analytics-reference.md#sort-operator)
`take` 用于快速获取结果示例，但其不以特定顺序显示表中的行。 若要获取有序视图，请使用 `top`（针对示例）或 `sort`（针对整个表）。

显示前 n 行，按特定列排序：

```AIQL

    requests | top 10 by timestamp desc
```

* 语法：大多数运算符具有 `by` 等关键字参数。
* `desc` = 降序，`asc` = 升序。

![](./media/app-insights-analytics-tour/260.png)

使用 `top...` 可更高效地表示 `sort ... | take...`。 可采用以下编写方式：

```AIQL

    requests | sort by timestamp desc | take 10
```

结果相同，但其运行速度会较慢。 （还可编写 `order`，其为 `sort` 的别名。）

还可使用表视图中的列标头对屏幕上的结果进行排序。 当然，如果已使用 `take` 或 `top` 来检索表中的部分内容，则仅会重新排列已检索到的记录。

## <a name="whereapp-insights-analytics-referencemdwhere-operator-filtering-on-a-condition"></a>[Where](app-insights-analytics-reference.md#where-operator)：按条件筛选

来了解一下仅返回特定结果代码的请求：

```AIQL

    requests
    | where resultCode  == "404"
    | take 10
```

![](./media/app-insights-analytics-tour/250.png)

`where` 运算符采用布尔表达式。 以下为相关要点：

* `and`、`or`：布尔运算符
* `==`、`<>`、`!=`：等于和不等于
* `=~`、`!~`：不区分大小写的字符串等于和不等于。 还有许多其他字符串比较运算符。

了解[标量表达式](app-insights-analytics-reference.md#scalars)的所有相关信息。

### <a name="getting-the-right-type"></a>获取正确的类型
查找失败的请求：

```AIQL

    requests
    | where isnotempty(resultCode) and toint(resultCode) >= 400
```

`resultCode` 具有类型字符串，因此必须[将其转换](app-insights-analytics-reference.md#casts)以进行数值比较。

## <a name="time-range"></a>时间范围

默认情况下，查询限制在最近 24 小时内。 但可更改此范围：

![](./media/app-insights-analytics-tour/change-time-range.png)

在 where 子句中编写任何提及 `timestamp` 的查询，以替代该时间范围。 例如：

```AIQL

    // What were the slowest requests over the past 3 days?
    requests
    | where timestamp > ago(3d)  // Override the time range
    | top 5 by duration
```

时间范围功能相当于在每提及一个源表后插入的“where”子句。

`ago(3d)` 表示“三天前”。 其他时间单位包括小时（`2h`、`2.5h`）、分钟 (`25m`) 和秒 (`10s`)。

其他示例：

```AIQL

    // Last calendar week:
    requests
    | where timestamp > startofweek(now()-7d)
        and timestamp < startofweek(now())
    | top 5 by duration

    // First hour of every day in past seven days:
    requests
    | where timestamp > ago(7d) and timestamp % 1d < 1h
    | top 5 by duration

    // Specific dates:
    requests
    | where timestamp > datetime(2016-11-19) and timestamp < datetime(2016-11-21)
    | top 5 by duration

```

[日期和时间参考](app-insights-analytics-reference.md#date-and-time)。


## <a name="projectapp-insights-analytics-referencemdproject-operator-select-rename-and-compute-columns"></a>[Project](app-insights-analytics-reference.md#project-operator)：选择、重命名和计算列
使用 [`project`](app-insights-analytics-reference.md#project-operator) 挑选出所需列：

```AIQL

    requests | top 10 by timestamp desc
             | project timestamp, name, resultCode
```

![](./media/app-insights-analytics-tour/240.png)

还可重命名列，并定义新的列：

```AIQL

    requests
    | top 10 by timestamp desc
    | project  
            name,
            response = resultCode,
            timestamp,
            ['time of day'] = floor(timestamp % 1d, 1s)
```

![结果](./media/app-insights-analytics-tour/270.png)

* 如果采用括号（`['...']` 或 `["..."]`）将列名称括起来，则[列名称](app-insights-analytics-reference.md#names)可包含空格或符号
* `%` 是常用的取模运算符。
* `1d`（一个数字加上“d”）是一个时间跨度文本，表示一天。 以下为其他时间跨度文本：`12h`、`30m`、`10s`、`0.01s`。
* `floor`（别名 `bin`）会向下舍入值，使其成为所提供的基值的最近倍数。 因此 `floor(aTime, 1s)` 会向下舍入时间，使其成为最近的秒数。

[表达式](app-insights-analytics-reference.md#scalars)可包括所有常用运算符（`+`、`-`...），且含有一系列有用的函数。

## <a name="extendapp-insights-analytics-referencemdextend-operator-compute-columns"></a>[Extend](app-insights-analytics-reference.md#extend-operator)：计算列
如果只想将列添加到现有列，请使用 [`extend`](app-insights-analytics-reference.md#extend-operator)：

```AIQL

    requests
    | top 10 by timestamp desc
    | extend timeOfDay = floor(timestamp % 1d, 1s)
```

如果想保留所有现有列，使用 [`extend`](app-insights-analytics-reference.md#extend-operator) 比 [`project`](app-insights-analytics-reference.md#project-operator) 更简便。

### <a name="convert-to-local-time"></a>转换为本地时间

时间戳始终为 UTC。 因此，如果你位于正值冬季的美国太平洋海岸，则可能需要：

```AIQL

    requests
    | top 10 by timestamp desc
    | extend localTime = timestamp - 8h
```


## <a name="summarizeapp-insights-analytics-referencemdsummarize-operator-aggregate-groups-of-rows"></a>[Summarize](app-insights-analytics-reference.md#summarize-operator)：聚合成组的行
`Summarize` 会向成组的行应用指定的聚合函数。

例如，会在 `duration` 字段中报告 Web 应用响应请求所需的时间。 来看看所有请求的平均响应时间：

![](./media/app-insights-analytics-tour/410.png)

或者，可将结果分为不同名称的请求：

![](./media/app-insights-analytics-tour/420.png)

`Summarize` 会将流中的数据点收集到 `by` 子句求值相等的组。 `by` 表达式中的每个值（即上述示例中的每个运算名称）会在结果表中生成一行。

或者可按每天的时间对结果分组：

![](./media/app-insights-analytics-tour/430.png)

请注意 `bin` 函数（又称 `floor`）的使用方法。 如果只使用 `by timestamp`，则每个输入行最终会位于其自己的小组内。 对于任何连续标量（例如时间或数字），我们必须将连续范围分解为便于管理的离散值数。 `bin` 是熟悉的向下舍入 `floor` 函数，也是执行该操作的最简方法。

可使用相同的技巧来缩小字符串范围：

![](./media/app-insights-analytics-tour/440.png)

请注意，可在聚合表达式或 by 子句中使用 `name=` 设置结果列的名称。

## <a name="counting-sampled-data"></a>采样数据计数
建议使用 `sum(itemCount)` 聚合来对事件计数。 许多情况下，itemCount = = 1，此函数只计算组中的行数。 但运算中采用[采样](app-insights-sampling.md)时，Application Insights 中只会将部分原始事件保留为数据点，因此每个可见的数据点有 `itemCount` 个事件。

例如，如果采样放弃了 75% 的原始事件，则在保留的记录中 itemCount==4，即每个保留的记录有 4 个原始记录。

过度使用应用程序时，自适应采样会使 itemCount 较高。

因此，对 itemCount 求和可有效估计事件的原始数量。

![](./media/app-insights-analytics-tour/510.png)

如果要对组中的行数进行计数，还可使用 `count()` 聚合（以及一个计数运算）。

存在一系列[聚合函数](app-insights-analytics-reference.md#aggregations)。

## <a name="charting-the-results"></a>绘制结果图表
```AIQL

    exceptions
       | summarize count=sum(itemCount)  
         by bin(timestamp, 1h)
```

默认情况下，以表形式显示结果：

![](./media/app-insights-analytics-tour/225.png)

但可采用比表视图更好的形式。 来看看图表视图中的结果，其中包括垂直条形图选项：

![单击“图表”，选择“垂直条形图”，并分配 x 和 y 轴](./media/app-insights-analytics-tour/230.png)

请注意，虽然未按时间对结果排序（如表中所示），但图表始终按正确的日期时间顺序显示。


## <a name="timecharts"></a>时间图表
显示每小时的事件数量：

```AIQL

    requests
      | summarize event_count=sum(itemCount)
        by bin(timestamp, 1h)
```

选择“图表显示”选项：

![时间表](./media/app-insights-analytics-tour/080.png)

## <a name="multiple-series"></a>多个序列
`summarize` 子句中的多个表达式可创建多个列。

`by` 子句中的多个表达式可创建多个行，每行针对每个值组合。

```AIQL

    requests
    | summarize count_=sum(itemCount), avg(duration)
      by bin(timestamp, 1h), client_StateOrProvince, client_City
    | order by timestamp asc, client_StateOrProvince, client_City
```

![按小时和位置排列的请求表](./media/app-insights-analytics-tour/090.png)

### <a name="segment-a-chart-by-dimensions"></a>按维度划分图表
如果绘制的表包含字符串列和数字列，则可使用字符串将数值数据分为一系列独立点。 如果存在多个字符串列，可选择要用作鉴别器的列。

![划分分析图表](./media/app-insights-analytics-tour/100.png)

#### <a name="bounce-rate"></a>跳出率

将布尔值转换为字符串，将其用作鉴别器：

```AIQL

    // Bounce rate: sessions with only one page view
    requests
    | where notempty(session_Id)
    | where tostring(operation_SyntheticSource) == "" // real users
    | summarize pagesInSession=sum(itemCount), sessionEnd=max(timestamp)
               by session_Id
    | extend isbounce= pagesInSession == 1
    | summarize count()
               by tostring(isbounce), bin (sessionEnd, 1h)
    | render timechart
```

### <a name="display-multiple-metrics"></a>显示多个指标
如果绘制的表包含多个数值列，则除时间戳外，还可显示其任意组合。

![划分分析图表](./media/app-insights-analytics-tour/110.png)

在可以选择多个数字列前，必须选择“不拆分”。 无法在显示多个数字列的同时拆分字符串列。

## <a name="daily-average-cycle"></a>每日平均周期
平均一天的使用情况如何变化？

按一天的时间取模对请求计数，将其量化到小时：

```AIQL

    requests
    | where timestamp > ago(30d)  // Override "Last 24h"
    | where tostring(operation_SyntheticSource) == "" // real users
    | extend hour = bin(timestamp % 1d , 1h)
          + datetime("2016-01-01") // Allow render on line chart
    | summarize event_count=sum(itemCount) by hour
```

![平均一天的小时数折线图](./media/app-insights-analytics-tour/120.png)

> [!NOTE]
> 请注意，目前必须将持续时间转化为有序的日期时间以便在折线图上显示。
>
>

## <a name="compare-multiple-daily-series"></a>比较多个每日系列
不同国家/地区一天中不同时间的使用情况如何变化？

```AIQL

     requests  
     | where timestamp > ago(30d)  // Override "Last 24h"
     | where tostring(operation_SyntheticSource) == "" // real users
     | extend hour= floor( timestamp % 1d , 1h)
           + datetime("2001-01-01")
     | summarize event_count=sum(itemCount)
       by hour, client_CountryOrRegion
     | render timechart
```

![按 client_CountryOrRegion 分割](./media/app-insights-analytics-tour/130.png)

## <a name="plot-a-distribution"></a>绘制分布图
存在多少个长度不同的会话？

```AIQL

    requests
    | where timestamp > ago(30d) // override "Last 24h"
    | where isnotnull(session_Id) and isnotempty(session_Id)
    | summarize min(timestamp), max(timestamp)
      by session_Id
    | extend sessionDuration = max_timestamp - min_timestamp
    | where sessionDuration > 1s and sessionDuration < 3m
    | summarize count() by floor(sessionDuration, 3s)
    | project d = sessionDuration + datetime("2016-01-01"), count_
```

需将最后一行转换为日期时间。 目前只有表的 x 轴为日期时间时，才显示为标量。

`where` 子句排除单次会话 (sessionDuration = = 0)，并设置 x 轴的长度。

![](./media/app-insights-analytics-tour/290.png)

## <a name="percentilesapp-insights-analytics-referencemdpercentiles"></a>[百分点值](app-insights-analytics-reference.md#percentiles)
哪些持续时间范围涵盖不同会话百分比？

使用上述查询，但替换掉最后一行：

```AIQL

    requests
    | where isnotnull(session_Id) and isnotempty(session_Id)
    | summarize min(timestamp), max(timestamp)
      by session_Id
    | extend sesh = max_timestamp - min_timestamp
    | where sesh > 1s
    | summarize count() by floor(sesh, 3s)
    | summarize percentiles(sesh, 5, 20, 50, 80, 95)
```

我们还删除了 where 子句的上限，以便获取正确数据，包括含有多个请求的所有会话：

![结果](./media/app-insights-analytics-tour/180.png)

从中我们可以发现：

* 5% 的会话持续时间不超过 3 分 34 秒；
* 50% 的会话持续时间不超过 36 分钟；
* 5% 的会话持续时间超过 7 天

若要获取各个国家/地区的单独细分情况，只需通过两个 summarize 运算符单独运算 client_CountryOrRegion 列：

```AIQL

    requests
    | where isnotnull(session_Id) and isnotempty(session_Id)
    | summarize min(timestamp), max(timestamp)
      by session_Id, client_CountryOrRegion
    | extend sesh = max_timestamp - min_timestamp
    | where sesh > 1s
    | summarize count() by floor(sesh, 3s), client_CountryOrRegion
    | summarize percentiles(sesh, 5, 20, 50, 80, 95)
      by client_CountryOrRegion
```

![](./media/app-insights-analytics-tour/190.png)

## <a name="join"></a>Join
有权访问多个表，其中包括请求和异常。

若要找出与返回失败响应的请求相关的异常，可联接 `session_Id` 上的表：

```AIQL

    requests
    | where toint(resultCode) >= 500
    | join (exceptions) on operation_Id
    | take 30
```


执行联接前，最好使用 `project` 选中所需列。
在同一子句中，重命名时间戳列。

## <a name="letapp-insights-analytics-referencemdlet-clause-assign-a-result-to-a-variable"></a>[Let](app-insights-analytics-reference.md#let-clause)：将结果分配给变量

使用 `let` 分离出上一个表达式的各部分。 结果不变：

```AIQL

    let bad_requests =
      requests
        | where  toint(resultCode) >= 500  ;
    bad_requests
    | join (exceptions) on session_Id
    | take 30
```

> [!Tip] 
> 在 Analytics 客户端中，请勿在此查询的各部分间放入空白行。 请务必执行所有运算。
>

使用 `toscalar` 将单个表单元格转换为一个值：

```AIQL
let topCities =  toscalar (
   requests
   | summarize count() by client_City 
   | top n by count_ 
   | summarize makeset(client_City));
requests
| where client_City in (topCities(3)) 
| summarize count() by client_City;
```


### <a name="functions"></a>函数

使用 *Let* 定义函数：

```AIQL

    let usdate = (t:datetime)
    {
      strcat(getmonth(t), "/", dayofmonth(t),"/", getyear(t), " ",
      bin((t-1h)%12h+1h,1s), iff(t%24h<12h, "AM", "PM"))
    };
    requests  
    | extend PST = usdate(timestamp-8h)
```

## <a name="accessing-nested-objects"></a>访问嵌套对象
可轻松访问嵌套对象。 例如，异常流中会出现如下结构化对象：

![结果](./media/app-insights-analytics-tour/520.png)

可选择感兴趣的属性将其平面化：

```AIQL

    exceptions | take 10
    | extend method1 = tostring(details[0].parsedStack[1].method)
```

请注意，需使用[转换](app-insights-analytics-reference.md#casts)，将其转换为适当类型。


## <a name="custom-properties-and-measurements"></a>自定义属性和度量值
如果应用程序向事件附加[自定义维度（属性）和自定义度量值](app-insights-api-custom-events-metrics.md#properties)，则其将出现在 `customDimensions` 和 `customMeasurements` 对象中。

例如，如果应用包括：

```C#

    var dimensions = new Dictionary<string, string>
                     {{"p1", "v1"},{"p2", "v2"}};
    var measurements = new Dictionary<string, double>
                     {{"m1", 42.0}, {"m2", 43.2}};
    telemetryClient.TrackEvent("myEvent", dimensions, measurements);
```

在 Analytics 中提取这些值：

```AIQL

    customEvents
    | extend p1 = customDimensions.p1,
      m1 = todouble(customMeasurements.m1) // cast to expected type

```

验证自定义维度是否为特定类型：

```AIQL

    customEvents
    | extend p1 = customDimensions.p1,
      iff(notnull(todouble(customMeasurements.m1)), ...
```

## <a name="dashboards"></a>仪表板
可将结果固定到仪表板，以便汇总所有重要的图表和表。

* [Azure 共享仪表板](app-insights-dashboards.md#share-dashboards)：单击大头针图标。 必须具有共享仪表板才能执行此操作。 在 Azure 门户中，打开或创建仪表板并单击“共享”。
* [Power BI 仪表板](app-insights-export-power-bi.md)：依次单击“导出”、“Power BI 查询”。 此替代方法的优点是可显示查询以及来自广泛源的其他结果。

## <a name="combine-with-imported-data"></a>结合导入的数据

Analytics 报表在仪表板上看起来不错，但有时需要将数据转换为更易理解的形式。 例如，假设遥测通过别名识别出了已经过验证的用户。 此时希望在结果中显示其实际名称。 为此，需要一个从别名映射到实际名称的 CSV 文件。

可导入数据文件，其使用方式与标准表（请求、例外等）相同。 可对其进行查询或将其与其他表联接。 例如，如果有一个名为“用户映射”的表，且它具有 `realName` 和 `userId` 列，则可将其用于在请求遥测中转换 `user_AuthenticatedId` 字段：

```AIQL

    requests
    | where notempty(user_AuthenticatedId)
    | project userId = user_AuthenticatedId
      // get the realName field from the usermap table:
    | join kind=leftouter ( usermap ) on userId
      // count transactions by name:
    | summarize count() by realName
```

若要导入表，请在“架构”边栏选项卡的“其他数据源”下，遵循说明，通过上传数据示例，添加新数据源。 然后使用此定义上传表。

导入功能当前处于预览状态，因此一开始将在“其他数据源”下看到“联系我们”的链接。 使用此链接登录预览计划，然后该链接将替换为“添加新数据源”按钮。


## <a name="tables"></a>表
可通过多个表访问从应用接收到的遥测流。 可在窗口左侧查看用于每个表的属性架构。

### <a name="requests-table"></a>请求表
对 Web 应用的 HTTP 请求计数，并按页面名称划分：

![对按名称划分的请求计数](./media/app-insights-analytics-tour/analytics-count-requests.png)

查找失败最多的请求：

![对按名称划分的请求计数](./media/app-insights-analytics-tour/analytics-failed-requests.png)

### <a name="custom-events-table"></a>自定义事件表
如果使用 [TrackEvent()](app-insights-api-custom-events-metrics.md#trackevent) 发送自己的事件，可从此表中读取这些事件。

来看一个示例，其中应用代码包含以下行：

```C#

    telemetry.TrackEvent("Query",
       new Dictionary<string,string> {{"query", sqlCmd}},
       new Dictionary<string,double> {
           {"retry", retryCount},
           {"querytime", totalTime}})
```

显示这些事件的频率：

![显示自定义事件的频率](./media/app-insights-analytics-tour/analytics-custom-events-rate.png)

从事件提取度量值和维度：

![显示自定义事件的频率](./media/app-insights-analytics-tour/analytics-custom-events-dimensions.png)

### <a name="custom-metrics-table"></a>自定义指标表
若要使用 [TrackMetric()](app-insights-api-custom-events-metrics.md#trackmetric) 发送自己的度量值，可在 **customMetrics** 流中找到其结果。 例如：  

![Application Insights Analytics 中的自定义指标](./media/app-insights-analytics-tour/analytics-custom-metrics.png)

> [!NOTE]
> 在[指标资源管理器](app-insights-metrics-explorer.md)中，附加到任何类型遥测的所有自定义度量值会和使用 `TrackMetric()` 发送的指标一起出现在指标边栏选项卡中。 但在 Analytics 中，自定义度量值仍会附加到实施其的任意类型遥测（事件或请求等），而由 TrackMetric 发送的指标会显示在其自己的流中。
>
>

### <a name="performance-counters-table"></a>性能计数器表
[性能计数器](app-insights-performance-counters.md)显示应用的基本系统指标，例如 CPU、内存和网络利用率。 可配置 SDK 发送其他计数器，包括自己的自定义计数器。

**PerformanceCounters** 架构公开每个性能计数器的 `category`、`counter` 名称和 `instance` 名称。 计数器实例名称仅适用于一些性能计数器，且通常表示与计数相关的进程的名称。 在每个应用程序的遥测中，将仅看到该应用程序的计数器。 例如，若要查看哪些计数器可用：

![Application Insights 分析中的性能计数器](./media/app-insights-analytics-tour/analytics-performance-counters.png)

获取所选时间内可用内存的图表：

![Application Insights 分析中的内存时间图表](./media/app-insights-analytics-tour/analytics-available-memory.png)

与其他遥测一样，**performanceCounters** 同样也具有 `cloud_RoleInstance` 列，此列指示正在其上运行应用的主机标识。 例如，若要应用在不同计算机上的性能：

![Application Insights 分析中按角色实例分段的性能](./media/app-insights-analytics-tour/analytics-metrics-role-instance.png)

### <a name="exceptions-table"></a>异常表
此表提供[由应用报告的异常](app-insights-asp-net-exceptions.md)。

查找出现异常时应用正在处理的 HTTP 请求，请联接 operation_Id：

![将异常与 operation_Id 上的请求联接](./media/app-insights-analytics-tour/analytics-exception-request.png)

### <a name="browser-timings-table"></a>浏览器计时表
`browserTimings` 显示用户浏览器中收集的页加载数据。

[为应用设置客户端遥测](app-insights-javascript.md)以查看这些指标。

架构包含[指示页加载进程不同阶段长度的指标](app-insights-javascript.md#page-load-performance)。 （它们不表示用户阅读页面的时长。）  

显示不同页面的热门程度和每个页面的加载次数：

![Analytics 中的页面加载次数](./media/app-insights-analytics-tour/analytics-page-load.png)

### <a name="availability-results-table"></a>可用性结果表
`availabilityResults` 显示 [Web 测试](app-insights-monitor-web-app-availability.md)的结果。 单独报告每个测试位置每次运行的测试。

![Analytics 中的页面加载次数](./media/app-insights-analytics-tour/analytics-availability.png)

### <a name="dependencies-table"></a>依赖项表
包含应用对数据库和 REST API 的调用结果以及对 TrackDependency() 的其他调用结果。 还包括来自浏览器的 AJAX 调用。

来自浏览器的 AJAX 调用：

```AIQL

    dependencies | where client_Type == "Browser"
    | take 10
```

来自服务器的依赖项调用：

```AIQL

    dependencies | where client_Type == "PC"
    | take 10
```

如果未安装 Application Insights 代理，则服务器端依赖项结果始终显示为 `success==False`。 但是，其他数据正确。

### <a name="traces-table"></a>跟踪表
包含应用使用 TrackTrace() 或[其他记录框架](app-insights-asp-net-trace-logs.md)发送的遥测。

## <a name="video"></a>视频 

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/123/player] 

高级查询：

> [!VIDEO https://channel9.msdn.com/Events/Build/2016/P591/player]


## <a name="next-steps"></a>后续步骤
* [Analytics 语言参考](app-insights-analytics-reference.md)
* [SQL 用户的备忘单](https://aka.ms/sql-analytics)转换最常见的惯用语言。

[!INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]

