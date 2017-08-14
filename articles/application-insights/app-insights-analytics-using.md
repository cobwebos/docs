---
title: "使用 Analytics - Azure Application Insights 的强大搜索工具 | Microsoft Docs"
description: "使用 Analytics - Application Insights 的强大诊断搜索工具。 "
services: application-insights
documentationcenter: 
author: danhadari
manager: carmonm
ms.assetid: c3b34430-f592-4c32-b900-e9f50ca096b3
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: sewhee
ms.translationtype: HT
ms.sourcegitcommit: c30998a77071242d985737e55a7dc2c0bf70b947
ms.openlocfilehash: 28d32d1e2d82519fc7b2ad4edca8435c3759594f
ms.contentlocale: zh-cn
ms.lasthandoff: 08/02/2017

---
# <a name="using-analytics-in-application-insights"></a>使用 Application Insights 中的 Analytics
[Analytics](app-insights-analytics.md) 是 [Application Insights](app-insights-overview.md) 的强大搜索功能。 这些页面介绍 Log Analytics 查询语言。

* **[观看介绍视频](https://applicationanalytics-media.azureedge.net/home_page_video.mp4)**。
* **[针对模拟数据测试驱动 Analytics](https://analytics.applicationinsights.io/demo)**（如果应用尚未将数据发送到 Application Insights）。

## <a name="open-analytics"></a>打开 Analytics
在 Application Insights 上的应用主页资源中单击“Analytics”。

![依次打开 portal.azure.com 和 Application Insights 资源，并单击“Analytics”。](./media/app-insights-analytics-using/001.png)

内联教程提供了有关可执行的操作的一些思路。

[此处提供了更详细的教程](app-insights-analytics-tour.md)。

## <a name="query-your-telemetry"></a>查询遥测数据
### <a name="write-a-query"></a>编写查询
![架构显示](./media/app-insights-analytics-using/150.png)

首先，请查看左侧列出的所有表的名称（或者 [range](https://docs.loganalytics.io/queryLanguage/query_language_rangeoperator.html) 或 [union](https://docs.loganalytics.io/queryLanguage/query_language_unionoperator.html) 运算符）。 使用 `|` 创建[运算符](https://docs.loganalytics.io/learn/cheatsheets/useful_operators.html)的分隔竖线。 

IntelliSense 会提示可以使用哪些运算符和表达式元素。 单击信息图标（或按 CTRL+空格键）可以获取更长的说明，以及演示如何使用每个元素的示例。

请参阅 [Analytics 语言教程](app-insights-analytics-tour.md)和[语言参考](app-insights-analytics-reference.md)。

### <a name="run-a-query"></a>运行查询
![运行查询](./media/app-insights-analytics-using/130.png)

1. 可以在查询中使用单个换行符。
2. 将光标放在要运行的查询的内部或末尾。
3. 检查查询的时间范围。 （可以更改该时间范围，或者通过在查询中包含自己的 [`where...timestamp...`](https://docs.loganalytics.io/concepts/concepts_datatypes_timespan.html) 来重写该值。）
3. 单击“运行”运行查询。
4. 不要在查询中插入空行。 可以通过使用空行分隔多个查询，将这些分隔的查询保留在一个查询中。 只会运行光标所在处的查询。

### <a name="save-a-query"></a>保存查询
![保存查询](./media/app-insights-analytics-using/140.png)

1. 保存当前查询文件。
2. 打开已保存的查询文件。
3. 创建新查询文件。

## <a name="see-the-details"></a>查看详细信息
展开结果中的任一行可以查看其完整属性列表。 可以进一步展开用作结构化值的任何属性 - 例如，自定义维度或异常中的堆栈列表。

![展开行](./media/app-insights-analytics-using/070.png)

## <a name="arrange-the-results"></a>排列结果
可将查询返回的结果排序、筛选、分页和分组。

> [!NOTE]
> 在浏览器中排序、分组和筛选不会重新运行查询。 这些操作只会重新排列最后一个查询返回的结果。 
> 
> 若要在返回结果之前在服务器中执行这些任务，请使用 [sort](https://docs.loganalytics.io/queryLanguage/query_language_sortoperator.html)、[summarize](https://docs.loganalytics.io/queryLanguage/query_language_summarizeoperator.html) 和 [where](https://docs.loganalytics.io/queryLanguage/query_language_whereoperator.html) 运算符编写查询。
> 
> 

选择要查看的列，拖动列标题重新排列列，并拖动其边框来调整列的大小。

![排列列](./media/app-insights-analytics-using/030.png)

### <a name="sort-and-filter-items"></a>排序和筛选项
可以通过单击列标题将结果排序。 再次单击以另一种方式排序，又一次单击可还原为查询返回数据时采用的原始顺序。

使用筛选图标缩小搜索范围。

![排序和筛选列](./media/app-insights-analytics-using/040.png)

### <a name="group-items"></a>将项分组
若要按多个列排序，请使用分组。 请先启用分组，然后将列标题拖到表格上方的空位中。

![组](./media/app-insights-analytics-using/060.png)

### <a name="missing-some-results"></a>缺少一些结果？

如果有些结果并未出现，原因可能有几种。

* **时间范围筛选器**。 默认情况下，只会显示过去 24 小时的结果。 有一个自动筛选器会限制从源表检索的结果范围。 

    但是，可以使用下拉菜单更改时间范围筛选器。

    或者，通过在查询中包含自己的 [`where  ... timestamp ...` 子句](https://docs.loganalytics.io/queryLanguage/query_language_whereoperator.html)来重写自动范围。 例如：

    `requests | where timestamp > ago('2d')`

* **结果限制**。 从门户返回的结果限制为大约 1 万行。 如果超过此限制，会显示警告。 如果发生这种情况，为表中的结果排序不一定总会显示所有实际的第一批或最后一批结果。 

    请尽量避免达到限制。 使用时间范围筛选器，或使用如下所示的运算符：

  * [top 100 by timestamp](https://docs.loganalytics.io/queryLanguage/query_language_topoperator.html) 
  * [take 100](https://docs.loganalytics.io/queryLanguage/query_language_takeoperator.html)
  * [summarize ](https://docs.loganalytics.io/queryLanguage/query_language_summarizeoperator.html) 
  * [where timestamp > ago(3d)](https://docs.loganalytics.io/queryLanguage/query_language_whereoperator.html)

（希望结果中包含 1 万行以上？ 请考虑改用[连续导出](app-insights-export-telemetry.md)。 Analytics 旨在用于分析，而不是检索原始数据。）

## <a name="diagrams"></a>图表
选择所需的图表类型：

![选择图表类型](./media/app-insights-analytics-using/230.png)

如果有多个正确类型的列，可以选择 x 和 y 轴以及一个维度列作为拆分结果的依据。

默认情况下，结果最初以表的形式显示，可以手动选择图表。 但也可以在查询末尾使用 [render 指令](https://docs.loganalytics.io/queryLanguage/query_language_renderoperator.html)选择图表。

### <a name="analytics-diagnostics"></a>Analytics Diagnostics


时间图表中，如果数据出现突增或上了一个台阶，则可在图线中看到突出显示的点。 这表示 Analytics Diagnostics 已标识筛选出发生突然变化的属性组合。 单击相应的点可获取有关筛选器的详细信息以及查看筛选器版本。 这有助于确定导致出现变化的原因。 

[详细了解 Analytics Diagnostics](app-insights-analytics-diagnostics.md)


![Analytics Diagnostics](./media/app-insights-analytics-using/analytics-diagnostics.png)

## <a name="pin-to-dashboard"></a>固定到仪表板
可以将图表或表固定到某个[共享仪表板](app-insights-dashboards.md) - 单击“固定”即可。 （可能需要[升级应用的资费套餐](app-insights-pricing.md)才能启用此功能。） 

![单击“固定”](./media/app-insights-analytics-using/pin-01.png)

这意味着，在生成仪表板来帮助监视 Web 服务的性能或使用情况时，可以在其中添加相当复杂的分析和其他指标。 

如果表包含四个或更少的列，可将其固定到仪表板。 只显示前七行。

### <a name="dashboard-refresh"></a>仪表板刷新
固定到仪表板的图表大约每小时通过自动重新运行查询进行刷新。 也可以单击“刷新”按钮。

### <a name="automatic-simplifications"></a>自动简化

在某些情况下，将图表固定到仪表板时，图表会应用特定的简化功能。

**时间限制：**查询自动限定为过去 14 天。 这种效果就相当于在查询中包含 `where timestamp > ago(14d)`。

**Bin 计数限制：**如果显示的图表包含许多离散的 Bin（通常为条形图），所占比例较少的 Bin 将自动分组到单个“其他”Bin。 例如，以下查询：

    requests | summarize count_search = count() by client_CountryOrRegion

在 Analytics 中如下所示：

![具有长尾的图表](./media/app-insights-analytics-using/pin-07.png)

将它固定到仪表板时，看起来如下所示：

![具有有限 Bin 的图表](./media/app-insights-analytics-using/pin-08.png)

## <a name="export-to-excel"></a>导出到 Excel
运行查询后，可以下载 .csv 文件。 单击“导出”->“Excel”。

## <a name="export-to-power-bi"></a>导出到 Power BI
将光标放在查询中，选择“导出”-&gt;“Power BI”。

![从 Analytics 导出到 Power BI](./media/app-insights-analytics-using/240.png)

在 Power BI 中运行查询。 可将图表设置为按计划刷新。

使用 Power BI，可以创建用于将从各种源中的数据组合在一起的仪表板。

[详细了解如何导出到 Power BI](app-insights-export-power-bi.md)

## <a name="deep-link"></a>深层链接

在“导出”->“共享链接”中获取可发送给另一用户的链接。 如果用户有[资源组的访问权限](app-insights-resources-roles-access-control.md)，则会在 Analytics UI 中打开查询。

（在此链接中，查询文本出现在“?q=”后面，并使用 gzip 进行压缩和使用 base-64 编码。 可以编写代码以生成向用户提供的深层链接。 但是，建议使用 [REST API](https://dev.applicationinsights.io/) 在代码中运行Analytics。）


## <a name="automation"></a>自动化

使用[数据访问 REST API](https://dev.applicationinsights.io/)运行 Analytics 查询。 [例如](https://dev.applicationinsights.io/apiexplorer/query?appId=DEMO_APP&apiKey=DEMO_KEY&query=requests%0A%7C%20where%20timestamp%20%3E%3D%20ago%2824h%29%0A%7C%20count)（使用 PowerShell）：

```PS
curl "https://api.applicationinsights.io/beta/apps/DEMO_APP/query?query=requests%7C%20where%20timestamp%20%3E%3D%20ago(24h)%7C%20count" -H "x-api-key: DEMO_KEY"
```

与 Analytics UI 不同，REST API 不会向查询中自动添加任何时间戳限制。 请记住添加自己的 where 子句，以避免巨大的数据响应量。



## <a name="import-data"></a>导入数据

可以从 CSV 文件导入数据。 典型的用法是从遥测数据中导入可与表联接的静态数据。 

例如，如果在遥测数据中，已按别名或经过模糊处理的 ID 标识了已身份验证的用户，则可以导入一个可将别名映射为实际姓名的表。 通过对请求遥测数据执行联接，可在 Analytics 报告中按用户的实际姓名来标识用户。

### <a name="define-your-data-schema"></a>定义数据架构

1. 单击“设置”（左上角），并单击“数据源”。 
2. 遵照说明添加数据源。 系统会要求提供数据样本，其中应至少包含 10 行。 然后请更正架构。

这样就会定义数据源，稍后可以用它来导入单个表。

### <a name="import-a-table"></a>导入表

1. 从列表中打开数据源定义。
2. 单击“上传”并遵照说明上传该表。 这包括对 REST API 的调用，以便能够轻松自动化。 

现在，可在 Analytics 中使用该表。 它会显示在 Analytics 中 

### <a name="use-the-table"></a>使用表

假设数据源定义的名称为 `usermap`，并且包含两个字段：`realName` 和 `user_AuthenticatedId`。 `requests` 表有一个名为 `user_AuthenticatedId` 的字段，因此可以轻松联接数据：

```AIQL

    requests
    | where notempty(user_AuthenticatedId) | take 10
    | join kind=leftouter ( usermap ) on user_AuthenticatedId 
```
生成的请求表包含一个额外的列：`realName`。

### <a name="import-from-logstash"></a>从 LogStash 导入

如果使用 [LogStash](https://www.elastic.co/guide/en/logstash/current/getting-started-with-logstash.html)，可以通过 Analytics 来查询日志。 使用[可通过管道将数据传送到 Analytics 的插件](https://github.com/Microsoft/logstash-output-application-insights)。 

## <a name="video"></a>视频

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/123/player] 

[!INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]


