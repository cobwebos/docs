---
title: 分析 Azure Monitor 日志中的文本数据 | Microsoft Docs
description: 介绍了用于在引入数据时以及在查询中检索时分析 Azure Monitor 记录中的日志数据的不同选项，比较了每个选项的相对优点。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 12/04/2018
ms.openlocfilehash: d7a37d51c411488231205fd036f9a287f5206ce5
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77672440"
---
# <a name="parse-text-data-in-azure-monitor-logs"></a>分析 Azure Monitor 日志中的文本数据
Azure Monitor 收集的某些日志数据会在单个属性中包括多条信息。 将此数据分析为多个属性可以更轻松地在查询中进行使用。 一个常见示例是收集在单个属性中包含多个值的整个日志项目的[自定义日志](../../log-analytics/log-analytics-data-sources-custom-logs.md)。 通过为不同值创建单独属性，可以对每个值进行搜索和聚合。

本文介绍了用于在引入数据时以及在查询中检索时分析 Azure Monitor 中的日志数据的不同选项，比较了每个选项的相对优点。


## <a name="parsing-methods"></a>分析方法
在收集数据的引入时间或是在使用查询分析数据的查询时间，可以分析数据。 每种策略都具有独特的优点，如下所述。

### <a name="parse-data-at-collection-time"></a>在收集时分析数据
如果在收集时分析数据，则配置会在表中创建新属性的[自定义字段](../../log-analytics/log-analytics-custom-fields.md)。 查询不必包含任何分析逻辑，只需将这些属性用作表中的任何其他字段。

此方法的优点包括以下这些：

- 更易于查询收集的数据，因为无需在查询中包含分析命令。
- 查询性能更好，因为查询无需执行分析。
 
此方法的缺点包括以下这些：

- 必须提前定义。 不能包括已收集的数据。
- 如果更改分析逻辑，则它仅应用于新数据。
- 分析选项少于查询中可用的选项。
- 增加收集数据的延迟时间。
- 错误可能难以处理。


### <a name="parse-data-at-query-time"></a>在查询时分析数据
如果在查询时分析数据，则在查询中包含用于将数据分析为多个字段的逻辑。 实际表本身不进行修改。

此方法的优点包括以下这些：

- 应用于任何数据，包括已收集的数据。
- 逻辑中的更改可以立即应用于所有数据。
- 灵活的分析选项，包括用于特定数据结构的预定义逻辑。
 
此方法的缺点包括以下这些：

- 需要更复杂的查询。 这可以通过使用[函数模拟表](#use-function-to-simulate-a-table)来进行缓解。
- 必须在多个查询中复制分析逻辑。 可以通过函数共享某些逻辑。
- 在对非常大的记录集（数十亿个记录）运行复杂逻辑时可能会形成开销。

## <a name="parse-data-as-its-collected"></a>在收集时分析数据
有关在收集时分析数据的详细信息，请参阅[在 Azure Monitor 中创建自定义字段](../platform/custom-fields.md)。 这会在表中创建可以由查询使用的自定义属性（就如同任何其他属性一样）。

## <a name="parse-data-in-query-using-patterns"></a>使用模式在查询中分析数据
当要分析的数据可以通过在记录间重复的某种模式进行标识时，可以使用 [Kusto 查询语言](/azure/kusto/query/)中的不同运算符将特定数据段提取到一个或多个新属性中。

### <a name="simple-text-patterns"></a>简单文本模式

在查询中使用[分析](/azure/kusto/query/parseoperator)运算符创建可以从字符串表达式中提取的一个或多个自定义属性。 指定要标识的模式以及要创建的属性的名称。 这对于具有形式类似于 _key=value_ 的键/值字符串的数据特别有用。

请考虑具有以下格式的数据的自定义日志。

```
Time=2018-03-10 01:34:36 Event Code=207 Status=Success Message=Client 05a26a97-272a-4bc9-8f64-269d154b0e39 connected
Time=2018-03-10 01:33:33 Event Code=208 Status=Warning Message=Client ec53d95c-1c88-41ae-8174-92104212de5d disconnected
Time=2018-03-10 01:35:44 Event Code=209 Status=Success Message=Transaction 10d65890-b003-48f8-9cfc-9c74b51189c8 succeeded
Time=2018-03-10 01:38:22 Event Code=302 Status=Error Message=Application could not connect to database
Time=2018-03-10 01:31:34 Event Code=303 Status=Error Message=Application lost connection to database
```

以下查询会将此数据分析为各个单独属性。 会添加包含 _project_ 的行以便仅返回计算的属性，而不是 _RawData_，这是从自定义日志保存整个条目的单个属性。

```Kusto
MyCustomLog_CL
| parse RawData with * "Time=" EventTime " Event Code=" Code " Status=" Status " Message=" Message
| project EventTime, Code, Status, Message
```

下面是另一个示例，它分解了 _AzureActivity_ 表中的 UPN 的用户名。

```Kusto
AzureActivity
| parse  Caller with UPNUserPart "@" * 
| where UPNUserPart != "" //Remove non UPN callers (apps, SPNs, etc)
| distinct UPNUserPart, Caller
```


### <a name="regular-expressions"></a>正则表达式
如果可以使用正则表达式标识数据，则可以通过[使用正则表达式的函数](/azure/kusto/query/re2)提取各个值。 下面的示例使用[提取](/azure/kusto/query/extractfunction)分解 _AzureActivity_ 记录中的 _UPN_ 字段，然后返回非重复用户。

```Kusto
AzureActivity
| extend UPNUserPart = extract("([a-z.]*)@", 1, Caller) 
| distinct UPNUserPart, Caller
```

为了显示大规模高效分析，Azure Monitor 使用 re2 版本的正则表达式，这与某些其他正则表达式变体相似，但并不完全相同。 有关详细信息，请参阅 [re2 表达式语法](https://aka.ms/kql_re2syntax)。


## <a name="parse-delimited-data-in-a-query"></a>在查询中分析带分隔符的数据
带分隔符的数据使用常见字符（例如 CSV 文件中的逗号）来分隔字段。 通过[拆分](/azure/kusto/query/splitfunction)函数可使用指定分隔符来分析带分隔符的数据。 可以将此方法与[扩展](/azure/kusto/query/extendoperator)运算符结合使用，以返回数据中的所有字段，或指定要包括在输出中的各个字段。

> [!NOTE]
> 由于拆分返回动态对象，因此结果可能需要显式强制转换为数据类型（如字符串）以便在运算符和筛选器中使用。

请考虑具有以下 CSV 格式的数据的自定义日志。

```
2018-03-10 01:34:36, 207,Success,Client 05a26a97-272a-4bc9-8f64-269d154b0e39 connected
2018-03-10 01:33:33, 208,Warning,Client ec53d95c-1c88-41ae-8174-92104212de5d disconnected
2018-03-10 01:35:44, 209,Success,Transaction 10d65890-b003-48f8-9cfc-9c74b51189c8 succeeded
2018-03-10 01:38:22, 302,Error,Application could not connect to database
2018-03-10 01:31:34, 303,Error,Application lost connection to database
```

以下查询会分析此数据和按两个计算的属性进行汇总。 第一行将 _RawData_ 属性拆分为字符串数组。 接下来各行会向单独的属性提供名称，并使用将它们转换为相应数据类型的函数将它们添加到输出。

```Kusto
MyCustomCSVLog_CL
| extend CSVFields  = split(RawData, ',')
| extend EventTime  = todatetime(CSVFields[0])
| extend Code       = toint(CSVFields[1]) 
| extend Status     = tostring(CSVFields[2]) 
| extend Message    = tostring(CSVFields[3]) 
| where getyear(EventTime) == 2018
| summarize count() by Status,Code
```

## <a name="parse-predefined-structures-in-a-query"></a>在查询中分析预定义结构
如果数据采用已知结构设置格式，则可能能够使用 [Kusto 查询语言](/azure/kusto/query/)中的一个函数来分析预定义结构：

- [JSON](/azure/kusto/query/parsejsonfunction)
- [XML](/azure/kusto/query/parse-xmlfunction)
- [IPv4](/azure/kusto/query/parse-ipv4function)
- [URL](/azure/kusto/query/parseurlfunction)
- [URL 查询](/azure/kusto/query/parseurlqueryfunction)
- [文件路径](/azure/kusto/query/parsepathfunction)
- [用户代理](/azure/kusto/query/parse-useragentfunction)
- [版本字符串](/azure/kusto/query/parse-versionfunction)

下面的示例查询分析 _AzureActivity_ 表（采用 JSON 结构）的 _Properties_ 字段。 它将结果保存到一个名为 _parsedProp_ 的动态属性，其中包含采用 JSON 的各个命名值。 这些值用于筛选和汇总查询结果。

```Kusto
AzureActivity
| extend parsedProp = parse_json(Properties) 
| where parsedProp.isComplianceCheck == "True" 
| summarize count() by ResourceGroup, tostring(parsedProp.tags.businessowner)
```

这些分析函数可能是处理器密集型，因此仅当查询使用格式化数据中的多个属性时才应使用它们。 否则，简单模式匹配处理会更快。

下面的示例演示域控制器 TGT 预身份验证类型的分解。 该类型仅存在于 EventData 字段中（这是一个 XML 字符串），但不需要此字段中的任何其他数据。 在这种情况下，[分析](/azure/kusto/query/parseoperator)用于挑选出所需数据段。

```Kusto
SecurityEvent
| where EventID == 4768
| parse EventData with * 'PreAuthType">' PreAuthType '</Data>' * 
| summarize count() by PreAuthType
```

## <a name="use-function-to-simulate-a-table"></a>使用函数模拟表
可能具有对特定表执行相同分析的多个查询。 在这种情况下，[创建一个函数](functions.md)以返回经过分析的数据，而不是在每个查询中复制分析逻辑。 随后可以在其他查询中使用函数别名来代替原始表。

请考虑上面的以逗号分隔的自定义日志示例。 若要在多个查询中使用经过分析的数据，请使用以下查询插件函数，并使用别名 _MyCustomCSVLog_ 保存它。

```Kusto
MyCustomCSVLog_CL
| extend CSVFields = split(RawData, ',')
| extend DateTime  = tostring(CSVFields[0])
| extend Code      = toint(CSVFields[1]) 
| extend Status    = tostring(CSVFields[2]) 
| extend Message   = tostring(CSVFields[3]) 
```

现在可以在查询使用别名 _MyCustomCSVLog_ 代替实际表名，如下所示。

```Kusto
MyCustomCSVLog
| summarize count() by Status,Code
```


## <a name="next-steps"></a>后续步骤
* 了解[日志查询](log-query-overview.md)以便分析从数据源和解决方案中收集的数据。
