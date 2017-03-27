---
title: "Azure 搜索的搜索流量分析 | Microsoft Docs"
description: "为 Azure 搜索（Microsoft Azure 上云托管的搜索服务）启用搜索流量分析，以解锁有关用户和数据的洞察力。"
services: search
documentationcenter: 
author: bernitorres
manager: pablocas
editor: 
ms.assetid: b31d79cf-5924-4522-9276-a1bb5d527b13
ms.service: search
ms.devlang: multiple
ms.workload: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 01/22/2017
ms.author: betorres
translationtype: Human Translation
ms.sourcegitcommit: cdcf121e52eaf6a1fc45194b7a4f5a02e9e5c001
ms.openlocfilehash: f6b354caf37f94906865b5a2f334e2b7a02f9d5b

---

# <a name="enabling-and-using-search-traffic-analytics"></a>允许并使用搜索流量分析
搜索流量分析是一项 Azure 搜索功能，让你可以了解搜索服务并解锁有关用户及其行为的洞察力。 当启用此功能时，搜索服务数据将复制到选择的存储帐户。 此数据包括搜索服务日志和聚合操作度量值，可进行处理和操作以供进一步分析。

## <a name="how-to-enable-search-traffic-analytics"></a>如何启用搜索流量分析
在相同区域和订阅中需要存储帐户作为搜索服务。

> [!IMPORTANT]
> 针对此存储帐户收取标准费用
>
>

可以在门户上或者通过 PowerShell 启用搜索流量分析。 启用后，数据会在 5-10 分钟内开始流入存储帐户，再流入这两个 blob 容器：

    insights-logs-operationlogs: search traffic logs
    insights-metrics-pt1m: aggregated metrics


### <a name="a-using-the-portal"></a>A. 使用门户
在 [Azure 门户](http://portal.azure.com)中打开 Azure 搜索服务。 在“设置”下，找到“搜索流量分析”选项。

![][1]

将状态更改为“开”、选择要使用的 Azure 存储帐户，然后选择要复制的数据：日志和/或度量值。 我们建议复制日志和度量值。
可以数据的保留策略设置为 1 到 365 天。 若要无限期保留数据，请将保留（天数）设置为 0。

![][2]

### <a name="b-using-powershell"></a>B. 使用 PowerShell
首先，确保已安装最新的 [Azure PowerShell cmdlet](https://github.com/Azure/azure-powershell/releases)。

然后，获取搜索服务和存储帐户的资源 ID。 可以通过导航到“设置”->“属性”->“ResourceId”，在门户中找到它们。

![][3]

```PowerShell
Login-AzureRmAccount
$SearchServiceResourceId = "Your Search service resource id"
$StorageAccountResourceId = "Your Storage account resource id"
Set-AzureRmDiagnosticSetting -ResourceId $SearchServiceResourceId StorageAccountId $StorageAccountResourceId -Enabled $true
```

## <a name="understanding-the-data"></a>了解数据
数据存储在 Azure 存储 blob 中，它的格式为 JSON。

每个容器每小时会有一个 blob。

示例路径：`resourceId=/subscriptions/<subscriptionID>/resourcegroups/<resourceGroupName>/providers/microsoft.search/searchservices/<searchServiceName>/y=2015/m=12/d=25/h=01/m=00/name=PT1H.json`

### <a name="logs"></a>日志
日志 blob 包含搜索服务流量日志。
每个 Blob 具有一个名为 **records** 的根对象，该对象包含一组日志对象。
每个 blob 会记录同一小时内发生的所有操作。

#### <a name="log-schema"></a>日志架构
| Name | 类型 | 示例 | 说明 |
| --- | --- | --- | --- |
| time |datetime |“2015-12-07T00:00:43.6872559Z” |操作的时间戳 |
| resourceId |字符串 |“/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/DEFAULT/PROVIDERS/<br/> MICROSOFT.SEARCH/SEARCHSERVICES/SEARCHSERVICE” |你的 ResourceId |
| operationName |字符串 |“Query.Search” |操作的名称 |
| operationVersion |字符串 |“2016-09-01” |使用的 api-version |
| category |字符串 |“OperationLogs” |常量 |
| resultType |字符串 |“Success” |可能的值：成功或失败 |
| resultSignature |int |200 |HTTP 结果代码 |
| durationMS |int |50 |操作持续时间，以毫秒为单位 |
| 属性 |对象 |请参阅下表 |包含特定于操作的数据的对象 |

#### <a name="properties-schema"></a>属性架构
| Name | 类型 | 示例 | 说明 |
| --- | --- | --- | --- |
| 说明 |字符串 |“GET /indexes('content')/docs” |操作的终结点 |
| 查询 |字符串 |“?search=AzureSearch&$count=true&api-version=2016-09-01” |查询参数 |
| 文档 |int |42 |处理的文档数目 |
| IndexName |字符串 |“testindex” |与操作关联的索引名称 |

### <a name="metrics"></a>度量值
度量值 blob 包含搜索服务的聚合值。
每个文件都有一个名为 **records** 的根对象，该对象包含度量值对象的数组。 此根对象包含可用数据的每分钟度量值。

可用度量值：

* SearchLatency：搜索服务处理搜索查询所需的时间（每分钟汇总一次）。
* SearchQueriesPerSecond：每秒接收的搜索查询次数（每分钟汇总一次）。
* ThrottledSearchQueriesPercentage：已限制的搜索查询百分比（每分钟汇总一次）。

> [!IMPORTANT]
> 如果由于发送的查询过多，耗尽了服务的预配资源容量，将进行限制。 考虑将更多副本添加到服务。
>
>

#### <a name="metrics-schema"></a>度量值架构
| Name | 类型 | 示例 | 说明 |
| --- | --- | --- | --- |
| resourceId |字符串 |“/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/DEFAULT/PROVIDERS/<br/>MICROSOFT.SEARCH/SEARCHSERVICES/SEARCHSERVICE” |你的资源 ID |
| metricName |字符串 |“Latency” |度量值名称 |
| time |datetime |“2015-12-07T00:00:43.6872559Z” |操作的时间戳 |
| average |int |64 |度量值时间间隔内原始样本的平均值 |
| minimum |int |37 |度量值时间间隔内原始样本的最小值 |
| maximum |int |78 |度量值时间间隔内原始样本的最大值 |
| total |int |258 |度量值时间间隔内原始样本的总计值 |
| count |int |4 |用于生成度量值的原始样本数 |
| timegrain |字符串 |“PT1M” |采用 ISO 8601 的度量值时间粒度 |

所有度量值会按一分钟的时间间隔报告。 每个度量值都会显示每分钟的最小、最大和平均值。

对于 SearchQueriesPerSecond 度量值，最小值是该分钟内已注册的每秒搜索查询次数最低值。 最大值也是如此。 平均值是一分钟内的聚合值。
假设一分钟内出现以下情形：有 1 秒出现高负载（这是 SearchQueriesPerSecond 的最大值），紧接着有 58 秒的平均负载，最后 1 秒只有 1 个查询（这是最小值）。

对于 ThrottledSearchQueriesPercentage、minimum、maximum、average 和 total，全都具有相同的值：在一分钟内的搜索查询总数中，已限制搜索查询百分比。

## <a name="analyzing-your-data"></a>分析数据
数据位于存储帐户中，我们建议采用最适合自身情况的方式浏览此数据。

作为起点，我们建议使用 [Power BI](https://powerbi.microsoft.com) 来浏览和可视化数据。 可轻松连接到 Azure 存储帐户，并快速开始分析数据。

#### <a name="power-bi-online"></a>Power BI Online
[Power BI 内容包](https://app.powerbi.com/getdata/services/azure-search)：创建 Power BI 仪表板和一组 Power BI 报告以便自动显示数据，并提供关于搜索服务的可视洞察力。 请参阅[内容包帮助页](https://powerbi.microsoft.com/en-us/documentation/powerbi-content-pack-azure-search/)。

![][4]

#### <a name="power-bi-desktop"></a>Power BI Desktop
[Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop)：浏览数据并为数据创建你自己的可视化。 请参阅以下部分中的入门查询：

1. 打开新的 PowerBI Desktop 报告。

2. 依次选择“获取数据”->“更多...”

    ![][5]

3. 依次选择“Microsoft Azure Blob 存储”和“连接”。

    ![][6]

4. 输入存储帐户的名称和帐户密钥。

5. 选择“insight-logs-operationlogs”和“insights-metrics-pt1m”，然后单击“编辑”。

6. 当查询编辑器打开时，请确保已选择左侧的“insight-logs-operationlogss”。 现在，通过依次选择“查看”->“高级编辑器”，打开高级编辑器。

    ![][7]
    
7. 保留前两行并将其余部分替换为以下查询：

   ~~~~
   > # "insights-logs-operationlogs" = Source{[Name="insights-logs-operationlogs"]}[Data],
   > # "Sorted Rows" = Table.Sort(#"insights-logs-operationlogs",{{"Date modified", Order.Descending}}),
   > # "Kept First Rows" = Table.FirstN(#"Sorted Rows",744),
   > # "Removed Columns" = Table.RemoveColumns(#"Kept First Rows",{"Name", "Extension", "Date accessed", "Date modified", "Date created", "Attributes", "Folder Path"}),
   > # "Parsed JSON" = Table.TransformColumns(#"Removed Columns",{},Json.Document),
   > # "Expanded Content" = Table.ExpandRecordColumn(#"Parsed JSON", "Content", {"records"}, {"records"}),
   > # "Expanded records" = Table.ExpandListColumn(#"Expanded Content", "records"),
   > # "Expanded records1" = Table.ExpandRecordColumn(#"Expanded records", "records", {"time", "resourceId", "operationName", "operationVersion", "category", "resultType", "resultSignature", "durationMS", "properties"}, {"time", "resourceId", "operationName", "operationVersion", "category", "resultType", "resultSignature", "durationMS", "properties"}),
   > # "Expanded properties" = Table.ExpandRecordColumn(#"Expanded records1", "properties", {"Description", "Query", "IndexName", "Documents"}, {"Description", "Query", "IndexName", "Documents"}),
   > # "Renamed Columns" = Table.RenameColumns(#"Expanded properties",{{"time", "Datetime"}, {"resourceId", "ResourceId"}, {"operationName", "OperationName"}, {"operationVersion", "OperationVersion"}, {"category", "Category"}, {"resultType", "ResultType"}, {"resultSignature", "ResultSignature"}, {"durationMS", "Duration"}}),
   > # "Added Custom2" = Table.AddColumn(#"Renamed Columns", "QueryParameters", each Uri.Parts("http://tmp" & [Query])),
   > # "Expanded QueryParameters" = Table.ExpandRecordColumn(#"Added Custom2", "QueryParameters", {"Query"}, {"Query.1"}),
   > # "Expanded Query.1" = Table.ExpandRecordColumn(#"Expanded QueryParameters", "Query.1", {"search", "$skip", "$top", "$count", "api-version", "searchMode", "$filter"}, {"search", "$skip", "$top", "$count", "api-version", "searchMode", "$filter"}),
   > # "Removed Columns1" = Table.RemoveColumns(#"Expanded Query.1",{"OperationVersion"}),
   > # "Changed Type" = Table.TransformColumnTypes(#"Removed Columns1",{{"Datetime", type datetimezone}, {"ResourceId", type text}, {"OperationName", type text}, {"Category", type text}, {"ResultType", type text}, {"ResultSignature", type text}, {"Duration", Int64.Type}, {"Description", type text}, {"Query", type text}, {"IndexName", type text}, {"Documents", Int64.Type}, {"search", type text}, {"$skip", Int64.Type}, {"$top", Int64.Type}, {"$count", type logical}, {"api-version", type text}, {"searchMode", type text}, {"$filter", type text}}),
   > # "Inserted Date" = Table.AddColumn(#"Changed Type", "Date", each DateTime.Date([Datetime]), type date),
   > # "Duplicated Column" = Table.DuplicateColumn(#"Inserted Date", "ResourceId", "Copy of ResourceId"),
   > # "Split Column by Delimiter" = Table.SplitColumn(#"Duplicated Column","Copy of ResourceId",Splitter.SplitTextByEachDelimiter({"/"}, null, true),{"Copy of ResourceId.1", "Copy of ResourceId.2"}),
   > # "Changed Type1" = Table.TransformColumnTypes(#"Split Column by Delimiter",{{"Copy of ResourceId.1", type text}, {"Copy of ResourceId.2", type text}}),
   > # "Removed Columns2" = Table.RemoveColumns(#"Changed Type1",{"Copy of ResourceId.1"}),
   > # "Renamed Columns1" = Table.RenameColumns(#"Removed Columns2",{{"Copy of ResourceId.2", "ServiceName"}}),
   > # "Lowercased Text" = Table.TransformColumns(#"Renamed Columns1",{{"ServiceName", Text.Lower}}),
   > # "Added Custom" = Table.AddColumn(#"Lowercased Text", "DaysFromToday", each Duration.Days(DateTimeZone.UtcNow() - [Datetime])),
   > # "Changed Type2" = Table.TransformColumnTypes(#"Added Custom",{{"DaysFromToday", Int64.Type}})
   > in
   >
   > # "Changed Type2"
   >
   ~~~~

8. 单击“完成”。

9. 现在从左方最后的查询中选择“insights-metrics-pt1m”，然后再次打开“高级编辑器”。 保留前两行并将其余部分替换为以下查询：

   ~~~~
   > # "insights-metrics-pt1m1" = Source{[Name="insights-metrics-pt1m"]}[Data],
   > # "Sorted Rows" = Table.Sort(#"insights-metrics-pt1m1",{{"Date modified", Order.Descending}}),
   > # "Kept First Rows" = Table.FirstN(#"Sorted Rows",744),
   > # "Removed Columns" = Table.RemoveColumns(#"Kept First Rows",{"Name", "Extension", "Date accessed", "Date modified", "Date created", "Attributes", "Folder Path"}),
   > # "Parsed JSON" = Table.TransformColumns(#"Removed Columns",{},Json.Document),
   > # "Expanded Content" = Table.ExpandRecordColumn(#"Parsed JSON", "Content", {"records"}, {"records"}),
   > # "Expanded records" = Table.ExpandListColumn(#"Expanded Content", "records"),
   > # "Expanded records1" = Table.ExpandRecordColumn(#"Expanded records", "records", {"resourceId", "metricName", "time", "average", "minimum", "maximum", "total", "count", "timeGrain"}, {"resourceId", "metricName", "time", "average", "minimum", "maximum", "total", "count", "timeGrain"}),
   > # "Filtered Rows" = Table.SelectRows(#"Expanded records1", each ([metricName] = "Latency")),
   > # "Removed Columns1" = Table.RemoveColumns(#"Filtered Rows",{"timeGrain"}),
   > # "Renamed Columns" = Table.RenameColumns(#"Removed Columns1",{{"time", "Datetime"}, {"resourceId", "ResourceId"}, {"metricName", "MetricName"}, {"average", "Average"}, {"minimum", "Minimum"}, {"maximum", "Maximum"}, {"total", "Total"}, {"count", "Count"}}),
   > # "Changed Type" = Table.TransformColumnTypes(#"Renamed Columns",{{"ResourceId", type text}, {"MetricName", type text}, {"Datetime", type datetimezone}, {"Average", type number}, {"Minimum", Int64.Type}, {"Maximum", Int64.Type}, {"Total", Int64.Type}, {"Count", Int64.Type}}),
   > Rounding = Table.TransformColumns(#"Changed Type",{{"Average", each Number.Round(_, 2)}}),
   >
   > # "Changed Type1" = Table.TransformColumnTypes(Rounding,{{"Average", type number}}),
   > # "Inserted Date" = Table.AddColumn(#"Changed Type1", "Date", each DateTime.Date([Datetime]), type date)
   > in
   >
   > # "Inserted Date"
   >
   ~~~~

10. 单击“完成”，然后选择“主页”选项卡中的“关闭并应用”。

11. 过去 30 天的数据现随时可供使用。 继续并创建一些[可视化](https://powerbi.microsoft.com/en-us/documentation/powerbi-desktop-report-view/)。

## <a name="next-steps"></a>后续步骤
了解有关搜索语法和查询参数的详细信息。 有关详细信息，请参阅[搜索文档（Azure 搜索 REST API）](https://msdn.microsoft.com/library/azure/dn798927.aspx)。

了解有关创建出色报告的详细信息。 有关详细信息，请参阅 [Power BI Desktop 入门](https://powerbi.microsoft.com/en-us/documentation/powerbi-desktop-getting-started/)

<!--Image references-->

[1]: ./media/search-traffic-analytics/SettingsBlade.png
[2]: ./media/search-traffic-analytics/DiagnosticsBlade.png
[3]: ./media/search-traffic-analytics/ResourceId.png
[4]: ./media/search-traffic-analytics/Dashboard.png
[5]: ./media/search-traffic-analytics/GetData.png
[6]: ./media/search-traffic-analytics/BlobStorage.png
[7]: ./media/search-traffic-analytics/QueryEditor.png



<!--HONumber=Jan17_HO4-->


