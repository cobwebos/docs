---
title: 优化 Azure Monitor 中的日志查询
description: Azure Monitor 中优化日志查询的最佳实践。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/28/2019
ms.openlocfilehash: e5c3da94cf2440b30dc59fe20bc51a34095f7d5f
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2020
ms.locfileid: "78269064"
---
# <a name="optimize-log-queries-in-azure-monitor"></a>优化 Azure Monitor 中的日志查询
Azure Monitor 日志使用[Azure 数据资源管理器（ADX）](/azure/data-explorer/)来存储日志数据，并运行查询来分析这些数据。 它为你创建、管理和维护 ADX 群集，并为日志分析工作负荷优化它们。 运行查询时，将对其进行优化，并将其路由到存储工作区数据的相应 ADX 群集。 Azure Monitor 日志和 Azure 数据资源管理器使用许多自动查询优化机制。 虽然自动优化可显著提高性能，但在某些情况下，可以显著提高查询性能。 本文介绍了性能注意事项和解决这些问题的几种方法。

大多数方法对于直接在 Azure 数据资源管理器和 Azure Monitor 日志上运行的查询是通用的，尽管此处讨论了几个独特的 Azure Monitor 日志注意事项。 有关更多 Azure 数据资源管理器优化提示，请参阅[查询最佳实践](/azure/kusto/query/best-practices)。

优化的查询将：

- 运行速度更快，缩短了查询执行的总持续时间。
- 可能会受到限制或拒绝。

应特别注意用于重复性和突发的查询，如仪表板、警报、逻辑应用和 Power BI。 在这些情况下，无效查询造成的影响非常重要。

## <a name="query-performance-pane"></a>查询性能窗格
在 Log Analytics 中运行查询后，单击查询结果上方的向下箭头，查看 "查询性能" 窗格，其中显示查询的多个性能指标的结果。 下一节中介绍了这些性能指标。

![查询性能窗格](media/query-optimization/query-performance-pane.png)


## <a name="query-performance-indicators"></a>查询性能指标

以下查询性能指标可用于执行的每个查询：

- [总 CPU](#total-cpu)：用于跨所有计算节点处理查询的总体计算。 它表示用于计算、分析和数据提取的时间。 

- [用于处理查询的数据](#data-used-for-processed-query)：用于处理查询的总体数据。 受目标表的大小、使用的时间范围、应用的筛选器以及所引用的列数的影响。

- 已[处理查询的时间跨度](#time-span-of-the-processed-query)：用于处理查询的最新数据和最旧数据之间的间隔。 受为查询指定的显式时间范围影响。

- [处理的数据的使用时间](#age-of-processed-data)：目前和用于处理查询的最早的数据之间的差距。 它会极大影响数据提取的效率。

- [工作区数量](#number-of-workspaces)：由于隐式或显式选择，在查询处理期间访问了多少个工作区。

- [区域数](#number-of-regions)：根据隐式或显式选择的工作区，在查询处理期间访问了多少个区域。 多区域查询效率较低，性能指标存在部分覆盖。

- [并行度](#parallelism)：指示系统在多个节点上执行此查询的能力。 仅适用于 CPU 消耗较高的查询。 通过特定函数和运算符的使用影响。


## <a name="total-cpu"></a>总 CPU
为在所有查询处理节点中处理此查询而投入的实际计算 CPU。 由于大多数查询是在大量节点上执行的，因此通常会比查询实际执行的持续时间大得多。 

查询处理时间花费在：
- 数据检索–旧数据的检索时间比检索最新数据所用时间更长。
- 数据处理–数据的逻辑和计算。 

除了在查询处理节点中所花费的时间以外，Azure Monitor 日志还需要额外的时间来执行以下操作：对用户进行身份验证，并验证它们是否允许访问此数据、查找数据存储、分析查询和分配查询处理结点. 此时间不包括在查询总 CPU 时间内。

某些查询命令和函数的 CPU 消耗较高。 这对于分析 JSON 和 XML 或提取复杂的正则表达式的命令尤其如此。 这种分析可以通过[parse_json （）](/azure/kusto/query/parsejsonfunction)或[parse_xml （）](/azure/kusto/query/parse-xmlfunction)函数显式发生，也可以在引用动态列时隐式进行。

这些函数将 CPU 与正在处理的行数成正比。 最有效的优化是在查询中提前添加 where 条件，在执行 CPU 密集型函数之前，可以筛选出尽可能多的记录。

例如，下面的查询生成完全相同的结果，但第二个查询的结果是最有效的，因为分析之前[的条件不](/azure/kusto/query/whereoperator)包括许多记录：

```Kusto
//less efficient
SecurityEvent
| extend Details = parse_xml(EventData)
| extend FilePath = tostring(Details.UserData.RuleAndFileData.FilePath)
| extend FileHash = tostring(Details.UserData.RuleAndFileData.FileHash)
| summarize count() by FileHash, FilePath
| where FileHash != "" and FilePath !startswith "%SYSTEM32"  // Problem: irrelevant results are filtered after all processing and parsing is done
```
```Kusto
//more efficient
SecurityEvent
| where EventID == 8002 //Only this event have FileHash
| where EventData !has "%SYSTEM32" //Early removal of unwanted records
| extend Details = parse_xml(EventData)
| extend FilePath = tostring(Details.UserData.RuleAndFileData.FilePath)
| extend FileHash = tostring(Details.UserData.RuleAndFileData.FileHash)
| summarize count() by FileHash, FilePath
```

在计算列上包含[where](/azure/kusto/query/whereoperator)子句的查询，而不是在数据集中实际存在的列将失去效率。 在处理大型数据集时，对计算列进行筛选可防止一些系统优化。
例如，下面的查询将生成完全相同的结果，但第二个查询的结果更有效率，因为[where](/azure/kusto/query/whereoperator)条件指的是内置列

```Kusto
//less efficient
Heartbeat 
| extend IPRegion = iif(RemoteIPLongitude  < -94,"WestCoast","EastCoast")
| where IPRegion == "WestCoast"
| summarize count() by Computer
```
```Kusto
//more efficient
Heartbeat 
| where RemoteIPLongitude  < -94
| extend IPRegion = iif(RemoteIPLongitude  < -94,"WestCoast","EastCoast")
| summarize count() by Computer
```

尽管一些聚合命令[（如 max （）](/azure/kusto/query/max-aggfunction)、 [sum （）](/azure/kusto/query/sum-aggfunction)、 [count （](/azure/kusto/query/count-aggfunction)）和[avg （））](/azure/kusto/query/avg-aggfunction)对 CPU 的影响较低，但其他一些则更复杂，其中包括允许有效执行它们的试探法和估计。 例如， [dcount （）](/azure/kusto/query/dcount-aggfunction)使用 HyperLogLog 算法来提供较大数据集的非重复计数，而不是实际计算每个值;百分点函数使用最近排名百分比算法执行类似的近似值。 几个命令包含可选参数以降低其影响。 例如， [makeset （）](/azure/kusto/query/makeset-aggfunction)函数有一个可选参数，用于定义最大集大小，这会明显影响 CPU 和内存。

[联接](/azure/kusto/query/joinoperator?pivots=azuremonitor)和[汇总](/azure/kusto/query/summarizeoperator)命令在处理大量数据时可能会导致 CPU 使用率较高。 它们的复杂性直接与用作汇总或联接属性中的 `by` 的列的可能值数（称为*基数*）相关。 有关联接和汇总的说明和优化，请参阅其文档文章和优化提示。

例如，下面的查询将产生完全相同的结果，因为**CounterPath**始终映射到**CounterName**和**ObjectName**。 第二个是更有效的方式，因为聚合维度较小：

```Kusto
//less efficient
Perf
| summarize avg(CounterValue) 
by CounterName, CounterPath, ObjectName
```
```Kusto
//make the group expression more compact improve the performance
Perf
| summarize avg(CounterValue), any(CounterName), any(ObjectName) 
by CounterPath
```

CPU 消耗还可能会受到需要密集型计算的条件或扩展列的影响。 所有普通的字符串比较（例如[等于 = =](/azure/kusto/query/datatypes-string-operators)和[startswith](/azure/kusto/query/datatypes-string-operators) ）都具有大约相同的 CPU 影响，而高级文本匹配会影响更大。 具体而言， [has 运算符对于](/azure/kusto/query/datatypes-string-operators) [contains](/azure/kusto/query/datatypes-string-operators)运算符更有效。 由于字符串处理技术，查找长度超过四个字符的字符串比短字符串更有效。

例如，以下查询将产生类似的结果，具体取决于计算机命名策略，但第二个查询更高效：

```Kusto
//less efficient – due to filter based on contains
Heartbeat
| where Computer contains "Production" 
| summarize count() by ComputerIP 
```
```Kusto
//less efficient – due to filter based on extend
Heartbeat
| extend MyComputer = Computer
| where MyComputer startswith "Production" 
| summarize count() by ComputerIP 
```
```Kusto
//more efficient
Heartbeat
| where Computer startswith "Production" 
| summarize count() by ComputerIP 
```

> [!NOTE]
> 此指标仅显示来自即时群集的 CPU。 在多区域查询中，它只表示一个区域。 在多工作区查询中，它可能不包括所有工作区。


## <a name="data-used-for-processed-query"></a>用于已处理查询的数据

查询处理中的一个重要因素是扫描并用于查询处理的数据量。 与其他数据平台相比，Azure 数据资源管理器使用严格的优化，大大减少了数据量。 尽管如此，查询中也存在一些重要因素，这些因素可能会影响所使用的数据量。
在 Azure Monitor 日志中， **TimeGenerated**列用作数据的索引方式。 如果将**TimeGenerated**值限制为尽可能窄的范围，则可以显著地限制必须处理的数据量，从而显著提高查询性能。

增加正在处理的数据的另一个因素是使用大量的表。 当使用 `search *` 和 `union *` 命令时通常会发生这种情况。 这些命令强制系统对工作区中的所有表的数据进行评估和扫描。 在某些情况下，工作区中可能有数百个表。 使用 "搜索" 或任何搜索，尽量避免使用 "搜索" 或任何搜索，而无需将其限定到特定的表。

例如，下面的查询生成完全相同的结果，但最后一个查询最有效：

```Kusto
// This version scans all tables though only Perf has this kind of data
search "Processor Time" 
| summarize count(), avg(CounterValue)  by Computer
```
```Kusto
// This version scans all strings in Perf tables – much more efficient
Perf
| search "Processor Time" 
| summarize count(), avg(CounterValue)  by Computer
```
```Kusto
// This is the most efficient version 
Perf 
| where CounterName == "% Processor Time"  
| summarize count(), avg(CounterValue)  by Computer
```

减少数据量的另一种方法是在查询的早期[使用条件。](/azure/kusto/query/whereoperator) Azure 数据资源管理器平台包含一个缓存，可让它知道哪些分区包含与特定 where 条件相关的数据。 例如，如果查询包含 `where EventID == 4624`，则它仅将查询分发到处理具有匹配事件的分区的节点。

下面的示例查询生成完全相同的结果，但第二个查询更高效：

```Kusto
//less efficient
SecurityEvent
| summarize LoginSessions = dcount(LogonGuid) by Account
```
```Kusto
//more efficient
SecurityEvent
| where EventID == 4624 //Logon GUID is relevant only for logon event
| summarize LoginSessions = dcount(LogonGuid) by Account
```

由于 Azure 数据资源管理器是纵栏式数据存储，因此，每个列的检索与其他列无关。 直接检索的列数会影响总体数据量。 只应在输出中包含列，这些列是[汇总](/azure/kusto/query/summarizeoperator)结果或[投影](/azure/kusto/query/projectoperator)特定列所需要的。 Azure 数据资源管理器具有若干优化，可减少检索到的列数。 如果它确定不需要的列（例如，在 "[汇总](/azure/kusto/query/summarizeoperator)" 命令中未引用），则不会检索该列。

例如，第二个查询可能会处理更多的数据，因为它需要提取一列，而不是三列：

```Kusto
//Less columns --> Less data
SecurityEvent
| summarize count() by Computer  
```
```Kusto
//More columns --> More data
SecurityEvent
| summarize count(), dcount(EventID), avg(Level) by Computer  
```

## <a name="time-span-of-the-processed-query"></a>已处理查询的时间跨度

将根据**TimeGenerated**列对 Azure Monitor 日志中的所有日志进行分区。 访问的分区数与时间跨度直接相关。 缩短时间范围是确保执行提示查询的最有效方法。

可以使用 Log Analytics 屏幕中的时间范围选择器设置时间范围，如[Azure Monitor Log Analytics 中的日志查询范围和时间范围](scope.md#time-range)中所述。 这是建议的方法，因为所选时间范围将使用查询元数据传递到后端。 

另一种方法是在查询中的**TimeGenerated**上显式包含[where](/azure/kusto/query/whereoperator)条件。 应使用此方法，因为它可以确保时间范围是固定的，即使是从其他接口使用查询时也是如此。
应确保查询的所有部分都具有**TimeGenerated**的筛选器。 当查询具有从不同表或同一个表中提取数据的子查询时，每个查询都必须包含其自身[的 where](/azure/kusto/query/whereoperator)条件。

例如，在下面的查询中，虽然只会在最后一天扫描**Perf**表，但会扫描**检测信号**表的所有历史记录，最多可达两年：

```Kusto
Perf
| where TimeGenerated > ago(1d)
| summarize avg(CounterValue) by Computer, CounterName
| join kind=leftouter (
    Heartbeat
    //No time span filter in this part of the query
    | summarize IPs = makeset(ComputerIP, 10) by  Computer
) on Computer
```

出现这种错误的常见情况是[arg_max （）](/azure/kusto/query/arg-max-aggfunction)用于查找最近发生的情况。 例如：

```Kusto
Perf
| where TimeGenerated > ago(1d)
| summarize avg(CounterValue) by Computer, CounterName
| join kind=leftouter (
    Heartbeat
    //No time span filter in this part of the query
    | summarize arg_max(TimeGenerated, *), min(TimeGenerated)   
by Computer
) on Computer
```

这可以通过在内部查询中添加时间筛选器轻松纠正：

```Kusto
Perf
| where TimeGenerated > ago(1d)
| summarize avg(CounterValue) by Computer, CounterName
| join kind=leftouter (
    Heartbeat
    | where TimeGenerated > ago(1d) //filter for this part
    | summarize arg_max(TimeGenerated, *), min(TimeGenerated)   
by Computer
) on Computer
```

此错误的另一个示例是，在对多个表进行[联合](/azure/kusto/query/unionoperator?pivots=azuremonitor)后，执行时间范围筛选。 执行 union 运算时，应确定每个子查询的作用域。 可以使用[let](/azure/kusto/query/letstatement)语句确保范围一致性。

例如，以下查询将扫描*检测信号*表和*性能*表中的所有数据，而不仅仅是过去1天的数据：

```Kusto
Heartbeat 
| summarize arg_min(TimeGenerated,*) by Computer
| union (
    Perf 
    | summarize arg_min(TimeGenerated,*) by Computer) 
| where TimeGenerated > ago(1d)
| summarize min(TimeGenerated) by Computer
```

应按如下所示修复此查询：

```Kusto
let MinTime = ago(1d);
Heartbeat 
| where TimeGenerated > MinTime
| summarize arg_min(TimeGenerated,*) by Computer
| union (
    Perf 
    | where TimeGenerated > MinTime
    | summarize arg_min(TimeGenerated,*) by Computer) 
| summarize min(TimeGenerated) by Computer
```

度量值始终大于指定的实际时间。 例如，如果查询的筛选器为7天，则系统可能会扫描7.5 或8.1 天。 这是因为系统会将数据分区到大小可变的块区。 为了确保扫描所有相关记录，它会扫描整个分区，该分区可能涵盖几个小时甚至超过一天。

在某些情况下，系统无法准确度量时间范围。 在大多数情况下，如果查询范围少于一天或多个工作区查询，就会发生这种情况。


> [!IMPORTANT]
> 此指标仅显示立即群集中处理的数据。 在多区域查询中，它只表示一个区域。 在多工作区查询中，它可能不包括所有工作区。

## <a name="age-of-processed-data"></a>处理的数据的期限
Azure 数据资源管理器使用多个存储层：内存中的本地 SSD 磁盘和更慢的 Azure Blob。 数据越新，就越有可能以较小的延迟存储在性能更高的层中，从而减少查询持续时间和 CPU。 除了数据本身以外，系统还具有元数据的缓存。 数据越旧，其元数据在缓存中的可能性就越小。

尽管某些查询需要使用旧数据，但在某些情况下，会错误地使用旧数据。 如果执行查询时不在其元数据中提供时间范围，并且并非所有表引用都包括**TimeGenerated**列上的筛选器，则会发生这种情况。 在这些情况下，系统将扫描该表中存储的所有数据。 当数据保留时间较长时，它可能会涵盖长时间范围，以及与数据保持期相同的数据。

例如：

- 不要使用不受限制的子查询来设置 Log Analytics 中的时间范围。 请参看上述示例。
- 在不使用时间范围可选参数的情况下使用 API。
- 使用不强制时间范围（如 Power BI 连接器）的客户端。

请参阅早期部分中的示例和说明，因为在这种情况下它们也是相关的。

## <a name="number-of-regions"></a>区域数
在以下几种情况下，可以在不同的区域中执行单个查询：

- 当多个工作区显式列出时，它们位于不同的区域中。
- 当资源范围内的查询正在获取数据时，数据存储在位于不同区域的多个工作区中。

跨区域查询执行要求系统在后端大型中间数据块中进行序列化和传输，这些数据通常比查询最终结果大很多。 它还限制了系统执行优化、试探法和使用缓存的能力。
如果没有实际原因要扫描所有这些区域，则应调整范围，使其涵盖更少的区域。 如果资源范围已最小化但仍在使用多个区域，则可能是由于配置错误引起的。 例如，审核日志和诊断设置将发送到不同区域中的不同工作区，或者有多个诊断设置配置。 

> [!IMPORTANT]
> 在多个区域中运行查询时，CPU 和数据度量值将不准确，并且仅表示一个区域的度量值。

## <a name="number-of-workspaces"></a>工作区数量
工作区是用于隔离和管理日志数据的逻辑容器。 后端优化所选区域内物理群集上的工作区放置。

多个工作区的使用可能由以下原因导致： 

- ，其中有多个工作区显式列出。
- 当资源范围内的查询正在提取数据并且数据存储在多个工作区中时。
 
跨区域和跨群集执行查询要求系统在后端大型中间数据块中进行序列化和传输，这些数据通常比查询最终结果大很多。 它还限制了系统执行优化、试探法和利用缓存的能力。

> [!IMPORTANT]
> 在某些多工作区方案中，CPU 和数据度量值将不准确，只会将度量值仅表示给几个工作区。

## <a name="parallelism"></a>并行度
Azure Monitor 日志使用 Azure 数据资源管理器的大型群集来运行查询，这些群集在规模上各不相同，可能会获得多达数十个计算节点。 系统会根据工作区位置逻辑和容量自动缩放群集。

为了有效地执行查询，将根据其处理所需的数据对其进行分区和分布。 在某些情况下，系统无法有效地执行此操作。 这可能会导致查询持续很长时间。 

可降低并行度的查询行为包括：

- 使用序列化和窗口函数，如[序列化运算符](/azure/kusto/query/serializeoperator)、 [next （）](/azure/kusto/query/nextfunction)、[上一个（）](/azure/kusto/query/prevfunction)和[行](/azure/kusto/query/rowcumsumfunction)函数。 在某些情况下可以使用时序和用户分析函数。 如果未在查询结尾使用以下运算符，则可能还会出现低效的序列化： [range](/azure/kusto/query/rangeoperator)、 [sort](/azure/kusto/query/sortoperator)、 [order](/azure/kusto/query/orderoperator)、 [top](/azure/kusto/query/topoperator)、 [top-hitters](/azure/kusto/query/tophittersoperator)、 [getschema](/azure/kusto/query/getschemaoperator)。
-   使用[dcount （）](/azure/kusto/query/dcount-aggfunction)聚合函数会强制系统具有非重复值的中心副本。 如果数据的小数位数很高，请考虑使用 dcount 函数可选参数以降低准确性。
-   在许多情况下，[联接](/azure/kusto/query/joinoperator?pivots=azuremonitor)运算符降低了总体并行度。 如果性能有问题，请查看无序联接作为替代项。
-   在资源范围内的查询中，在有大量 RBAC 赋值的情况下，执行前 RBAC 检查可能会逗留。 这可能会导致较长的检查，这会导致较低的并行度。 例如，在有上千个资源的订阅上执行查询，每个资源在资源级别（而不是订阅或资源组）具有多个角色分配。
-   如果查询正在处理小块数据，则其并行度将较低，因为系统不会将其分散到多个计算节点。



## <a name="next-steps"></a>后续步骤

- [Kusto 查询语言的参考文档](/azure/kusto/query/)。
