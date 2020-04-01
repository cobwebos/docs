---
title: 优化 Azure 监视器中的日志查询
description: 优化 Azure 监视器中的日志查询的最佳做法。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/30/2019
ms.openlocfilehash: 29d5213b8eecd94ed8c8ce565972c9f98872a362
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/31/2020
ms.locfileid: "80411430"
---
# <a name="optimize-log-queries-in-azure-monitor"></a>优化 Azure 监视器中的日志查询
Azure 监视器日志使用[Azure 数据资源管理器 （ADX）](/azure/data-explorer/)来存储日志数据并运行用于分析该数据的查询。 它为您创建、管理和维护 ADX 群集，并针对日志分析工作负荷对其进行优化。 运行查询时，查询已优化，并路由到存储工作区数据的相应 ADX 群集。 Azure 监视器日志和 Azure 数据资源管理器都使用许多自动查询优化机制。 虽然自动优化可以显著提升，但在某些情况下，您可以显著提高查询性能。 本文介绍了性能注意事项和修复它们的几个技术。

大多数技术对于直接在 Azure 数据资源管理器和 Azure 监视器日志上运行的查询是通用的，但此处将讨论几个唯一的 Azure 监视器日志注意事项。 有关更多 Azure 数据资源管理器优化提示，请参阅[查询最佳做法](/azure/kusto/query/best-practices)。

优化的查询将：

- 运行得更快，减少查询执行的总体持续时间。
- 被限制或拒绝的可能性较小。

您应该特别注意用于重复和突发性使用的查询，如仪表板、警报、逻辑应用和 Power BI。 在这些情况下，无效查询的影响是巨大的。

## <a name="query-performance-pane"></a>查询性能窗格
在 Log Analytics 中运行查询后，单击查询结果上方的向下箭头以查看显示查询多个性能指标结果的查询性能窗格。 下一节将分别介绍这些绩效指标。

![查询性能窗格](media/query-optimization/query-performance-pane.png)


## <a name="query-performance-indicators"></a>查询性能指标

执行的每个查询都可以使用以下查询性能指标：

- [总 CPU：](#total-cpu)用于处理所有计算节点查询的总体计算。 它表示用于计算、分析和数据提取的时间。 

- [用于处理查询的数据](#data-used-for-processed-query)：用于处理查询的总体数据。 受目标表大小、使用的时间跨度、应用的筛选器和引用的列数的影响。

- [已处理查询的时间跨度](#time-span-of-the-processed-query)：用于处理查询的最新数据与最旧数据之间的差距。 受为查询指定的显式时间范围的影响。

- [处理数据的年龄](#age-of-processed-data)：现在与用于处理查询的最早数据之间的差距。 它对数据提取的效率有很大的影响。

- [工作区数](#number-of-workspaces)：由于隐式或显式选择，在查询处理期间访问了多少工作区。

- [区域数](#number-of-regions)：由于默示或显式选择工作区，在查询处理期间访问的区域数。 多区域查询的效率要低得多，绩效指标具有部分覆盖率。

- [并行性](#parallelism)：指示系统能够在多个节点上执行此查询的多少。 仅与 CPU 消耗高的查询相关。 受特定功能和运算符使用的影响。


## <a name="total-cpu"></a>总 CPU
用于跨所有查询处理节点处理此查询的实际计算 CPU。 由于大多数查询是在大量节点上执行的，因此这通常比查询实际执行的持续时间大得多。 

查询处理时间花在：
- 数据检索 – 与检索最新数据相比，检索旧数据将消耗更多时间。
- 数据处理 – 数据的逻辑和评估。 

除了在查询处理节点中花费的时间之外，Azure 监视器日志还花费额外的时间：对用户进行身份验证，并验证是否允许他们访问此数据、查找数据存储、分析查询和分配查询处理节点。 此时间不包括在查询总 CPU 时间中。

### <a name="early-filtering-of-records-prior-of-using-high-cpu-functions"></a>在使用高 CPU 功能之前提前筛选记录

某些查询命令和函数的 CPU 消耗量很大。 对于解析 JSON 和 XML 或提取复杂正则表达式的命令尤其如此。 此类解析可以通过[parse_json（）](/azure/kusto/query/parsejsonfunction)或[parse_xml（）](/azure/kusto/query/parse-xmlfunction)函数显式进行，也可以在引用动态列时隐式进行。

这些函数消耗 CPU 与其处理的行数成比例。 最有效的优化是在查询的早期添加可在执行 CPU 密集型函数之前筛选出尽可能多的记录的条件。

例如，以下查询产生的结果完全相同，但第二个查询是[最有效的，因为](/azure/kusto/query/whereoperator)解析之前的条件排除了许多记录：

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
| where FileHash != "" // No need to filter out %SYSTEM32 here as it was removed before
```

### <a name="avoid-using-evaluated-where-clauses"></a>避免在子句的位置使用评估

[包含计算](/azure/kusto/query/whereoperator)列上子句而不是数据集中实际存在的列子句的查询会提高效率。 在处理大型数据集时，对计算的列进行筛选会阻止某些系统优化。
例如，以下查询产生的结果完全相同，但第二个查询的效率更高，因为[条件](/azure/kusto/query/whereoperator)引用内置列的位置

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

### <a name="use-effective-aggregation-commands-and-dimmentions-in-summarize-and-join"></a>在汇总和联接中使用有效的聚合命令和暗调

虽然某些聚合命令（如 max（）、sum（）、count（） 和[avg（）](/azure/kusto/query/avg-aggfunction)由于其逻辑而具有较低的 CPU 影响，但其他命令则更为复杂，包括启发式和估计，以便有效地执行它们。 [max()](/azure/kusto/query/max-aggfunction) [sum()](/azure/kusto/query/sum-aggfunction) [count()](/azure/kusto/query/count-aggfunction) 例如[，dcount（）](/azure/kusto/query/dcount-aggfunction)使用 HyperLog 算法提供与大型数据集的不同计数的紧密估计，而无需实际计算每个值;百分位数函数使用最近的百分位数算法执行类似的近似值。 其中一些命令包括可选参数，以减少其影响。 例如[，makeset（）](/azure/kusto/query/makeset-aggfunction)函数具有用于定义最大集大小的可选参数，这显著影响 CPU 和内存。

[联接](/azure/kusto/query/joinoperator?pivots=azuremonitor)和[汇总](/azure/kusto/query/summarizeoperator)命令在处理大量数据时可能会导致高 CPU 利用率。 它们的复杂性与用作`by`汇总或联接属性的列的可能值的数量（称为*基数*）直接相关。 有关联接和汇总的说明和优化，请参阅其文档文章和优化提示。

例如，以下查询产生的结果完全相同，因为**反路径**始终以一对一方式映射到**反名称**和**对象名称**。 第二个越高效，因为聚合维度较小：

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

CPU 消耗也可能受到需要密集计算的条件或扩展列的影响。 所有琐碎的字符串比较（如[等号 ）](/azure/kusto/query/datatypes-string-operators)和[开头](/azure/kusto/query/datatypes-string-operators)的字符串都具有大致相同的 CPU 影响，而高级文本匹配的影响更大。 具体来说[，has](/azure/kusto/query/datatypes-string-operators)运算符的效率比[包含](/azure/kusto/query/datatypes-string-operators)运算符的效率更高。 由于字符串处理技术，查找比短字符串长的字符串效率更高。

例如，根据计算机命名策略，以下查询会产生类似的结果，但第二个查询的效率更高：

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
> 此指示器仅显示来自直接群集的 CPU。 在多区域查询中，它将仅表示其中一个区域。 在多工作区查询中，它可能不包括所有工作区。

### <a name="avoid-full-xml-and-json-parsing-when-string-parsing-works"></a>避免在字符串解析工作时完全 XML 和 JSON 解析
完全解析 XML 或 JSON 对象可能会消耗高 CPU 和内存资源。 在许多情况下，当只需要一个或两个参数，而XML或JSON对象很简单时，使用[解析运算符](/azure/kusto/query/parseoperator)或其他[文本解析技术](/azure/azure-monitor/log-query/parse-text)将它们解析为字符串更容易。 随着 XML 或 JSON 对象中记录数量的增加，性能提升将更为显著。 当记录数达到数千万时，这一点至关重要。

例如，以下查询将返回与上述查询完全相同的结果，而不执行完整的 XML 分析。 请注意，它对 XML 文件结构进行了一些假设，例如该文件路径元素在 FileHash 之后出现，并且它们都没有属性。 

```Kusto
//even more efficient
SecurityEvent
| where EventID == 8002 //Only this event have FileHash
| where EventData !has "%SYSTEM32" //Early removal of unwanted records
| parse EventData with * "<FilePath>" FilePath "</FilePath>" * "<FileHash>" FileHash "</FileHash>" *
| summarize count() by FileHash, FilePath
| where FileHash != "" // No need to filter out %SYSTEM32 here as it was removed before
```


## <a name="data-used-for-processed-query"></a>用于处理查询的数据

处理查询的一个关键因素是扫描和使用用于查询处理的数据量。 与其他数据平台相比，Azure 数据资源管理器使用积极的优化可显著减少数据量。 不过，查询中仍有一些关键因素会影响所使用的数据量。

在 Azure 监视器日志中，**时间生成**列用作对数据进行索引的一种方式。 通过将**Time生成**值限制为尽可能缩小范围，将显著限制必须处理的数据量，从而显著提高查询性能。

### <a name="avoid-unnecessary-use-of-search-and-union-operators"></a>避免不必要地使用搜索和联合运算符

增加正在处理的数据的另一个因素是使用大量表。 这通常发生在使用`search *`和`union *`命令时。 这些命令强制系统评估和扫描工作区中所有表的数据。 在某些情况下，工作区中可能有数百个表。 尽量避免使用"搜索+"或任何搜索，而不将其范围界定到特定表。

例如，以下查询产生的结果完全相同，但最后一个查询是迄今为止最有效的结果：

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

### <a name="add-early-filters-to-the-query"></a>向查询添加早期筛选器

减少数据量的另一种方法是在查询的早期具有[条件](/azure/kusto/query/whereoperator)。 Azure 数据资源管理器平台包含一个缓存，用于告知它知道哪些分区包含与特定条件相关的数据。 例如，如果查询包含`where EventID == 4624`，则它将仅将查询分发到处理具有匹配事件的分区的节点。

以下示例查询产生完全相同的结果，但第二个查询的效率更高：

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

### <a name="reduce-the-number-of-columns-that-is-retrieved"></a>减少检索的列数

由于 Azure 数据资源管理器是列数据存储，因此检索每个列独立于其他列。 检索的列数直接影响整个数据量。 应仅通过[汇总](/azure/kusto/query/summarizeoperator)结果或[投影](/azure/kusto/query/projectoperator)特定列来在输出中包含所需的列。 Azure 数据资源管理器具有多个优化，以减少检索列的数量。 如果它确定不需要列，例如，如果在[汇总](/azure/kusto/query/summarizeoperator)命令中未引用该列，则它不会检索该列。

例如，第二个查询可能处理三倍以上的数据，因为它不需要获取一列，而是需要获取三个列：

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

Azure 监视器日志中的所有日志都根据**时间生成**列进行分区。 访问的分区数与时间跨度直接相关。 缩短时间范围是保证快速查询执行的最有效方法。

时间范围可以使用日志分析屏幕中的时间范围选择器进行设置，如[Azure 监视器日志分析中的日志查询范围和时间范围中](scope.md#time-range)所述。 这是推荐的方法，因为所选时间范围使用查询元数据传递到后端。 

另一种方法是显式在查询中包含**Time生成**上的[where](/azure/kusto/query/whereoperator)条件。 应使用此方法，因为它可确保时间跨度是固定的，即使查询是从其他接口使用的。
应确保查询的所有部分都有**时间生成**筛选器。 当查询具有从各种表或同一表提取数据的子查询时，每个查询必须包括其自己的[位置](/azure/kusto/query/whereoperator)条件。

### <a name="make-sure-all-sub-queries-have-timegenerated-filter"></a>确保所有子查询都有时间生成筛选器

例如，在以下查询中，虽然**Perf**表将仅扫描最后一天，但将扫描**检测信号**表的所有历史记录，这些历史记录可能长达两年：

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

发生此类错误的常见情况是，当[arg_max（）](/azure/kusto/query/arg-max-aggfunction)用于查找最新发生情况时。 例如：

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

通过在内部查询中添加时间筛选器，可以轻松更正此问题：

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

此故障的另一个示例是在多个表上的[联合](/azure/kusto/query/unionoperator?pivots=azuremonitor)之后执行时间范围筛选时。 执行联合时，应限定每个子查询的范围。 您可以使用[let](/azure/kusto/query/letstatement)语句来确保范围的一致性。

例如，以下查询将扫描*检测信号*表和*Perf*表中的所有数据，而不仅仅是最后 1 天：

```Kusto
Heartbeat 
| summarize arg_min(TimeGenerated,*) by Computer
| union (
    Perf 
    | summarize arg_min(TimeGenerated,*) by Computer) 
| where TimeGenerated > ago(1d)
| summarize min(TimeGenerated) by Computer
```

此查询应按如下方式修复：

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

### <a name="time-span-measurement-limitations"></a>时间跨度测量限制

测量始终大于指定的实际时间。 例如，如果查询上的筛选器为 7 天，则系统可能会扫描 7.5 天或 8.1 天。 这是因为系统将数据划分为可变大小的区块。 为了确保扫描所有相关记录，它会扫描整个分区，该分区可能涵盖几个小时甚至超过一天。

在以下几种情况下，系统无法提供时间范围的精确测量。 在大多数情况下，查询的跨度小于一天或在多工作区查询中发生这种情况。


> [!IMPORTANT]
> 此指标仅显示在直接群集中处理的数据。 在多区域查询中，它将仅表示其中一个区域。 在多工作区查询中，它可能不包括所有工作区。

## <a name="age-of-processed-data"></a>处理数据的年龄
Azure 数据资源管理器使用多个存储层：内存中、本地 SSD 磁盘和速度慢得多的 Azure Blob。 数据越新，就越有可能存储在具有较小延迟的更具性能的层中，从而缩短查询持续时间和 CPU。 除了数据本身之外，系统还具有元数据缓存。 数据越旧，其元数据在缓存中的可能性就越小。

虽然某些查询需要使用旧数据，但在某些情况下，旧数据被错误地使用。 当执行查询时，未在其元数据中提供时间范围，并且并非所有表引用都包含**Time生成**列上的筛选器，则会发生这种情况。 在这些情况下，系统将扫描存储在该表中的所有数据。 当数据保留时间长时，它可以覆盖很长的时间段，因此数据与数据保留期一样旧。

例如，这种情况可以：

- 未使用不限制的子查询在日志分析中设置时间范围。 请参看上述示例。
- 使用无时间范围可选参数的 API。
- 使用不强制时间范围（如 Power BI 连接器）的客户端。

请参阅"可渗透"部分中的示例和注释，因为它们与本例中也相关。

## <a name="number-of-regions"></a>区域数
在以下几种情况下，可以在不同区域执行单个查询：

- 显式列出多个工作区，并且它们位于不同的区域中时。
- 当资源范围的查询正在提取数据，并且数据存储在位于不同区域的多个工作区中时。

跨区域查询执行要求系统在后端序列化和传输通常比查询最终结果大得多的大块中间数据。 它还限制了系统执行优化、启发式和利用缓存的能力。
如果没有实际理由扫描所有这些区域，则应调整范围，使其覆盖较少的区域。 如果资源范围最小化，但仍使用许多区域，则可能是由于配置错误。 例如，审核日志和诊断设置将发送到不同区域的不同工作区，或者有多个诊断设置配置。 

> [!IMPORTANT]
> 在多个区域上运行查询时，CPU 和数据测量将不准确，并且仅表示其中一个区域的度量值。

## <a name="number-of-workspaces"></a>工作区数
工作区是用于隔离和管理日志数据的逻辑容器。 后端优化所选区域中物理群集上的工作区位置。

多个工作区的使用可能源于： 

- 显式列出多个工作区的位置。
- 当资源范围的查询正在提取数据，并且数据存储在多个工作区中时。
 
跨区域和跨群集执行查询要求系统在通常比查询最终结果大得多的后端大块中间数据中序列化和传输。 它还限制了系统执行优化、启发式和利用缓存的能力。

> [!IMPORTANT]
> 在某些多工作区方案中，CPU 和数据测量将不准确，并且仅表示对少数工作区的度量。

## <a name="parallelism"></a>并行度
Azure 监视器日志正在使用 Azure 数据资源管理器的大型群集来运行查询，这些群集的规模各不相同，可能最多获得数十个计算节点。 系统根据工作区放置逻辑和容量自动缩放群集。

为了有效地执行查询，它根据处理所需的数据将其分区并分发到计算节点。 在某些情况下，系统无法有效地执行此操作。 这可能导致查询的持续时间长。 

可以减少并行性的查询行为包括：

- 使用序列化和窗口函数，如[序列化运算符](/azure/kusto/query/serializeoperator)、[下一个（）](/azure/kusto/query/nextfunction)[和](/azure/kusto/query/prevfunction)[行](/azure/kusto/query/rowcumsumfunction)函数。 在某些情况下，可以使用时间序列和用户分析功能。 如果查询末尾不使用以下运算符，则也可能发生低效序列化：[范围](/azure/kusto/query/rangeoperator)、[排序](/azure/kusto/query/sortoperator)、[顺序](/azure/kusto/query/orderoperator)、[顶部](/azure/kusto/query/topoperator)、[顶部、getschema。](/azure/kusto/query/tophittersoperator) [getschema](/azure/kusto/query/getschemaoperator)
-    [dcount（）](/azure/kusto/query/dcount-aggfunction)聚合函数的使用强制系统具有不同值的中央副本。 当数据量级较高时，请考虑使用 dcount 函数可选参数来降低准确性。
-    在许多情况下，[联接](/azure/kusto/query/joinoperator?pivots=azuremonitor)运算符降低整体并行性。 当性能有问题时，检查随机连接作为替代方案。
-    在资源范围查询中，在执行前 RBAC 检查可能会在 RBAC 分配数量非常多的情况下继续。 这可能导致较长的检查，从而导致较低的并行性。 例如，在订阅上执行查询，其中有数千个资源，并且每个资源在资源级别中具有许多角色分配，而不是在订阅或资源组上。
-    如果查询正在处理小块数据，则其并行性将很低，因为系统不会将其分散到许多计算节点上。



## <a name="next-steps"></a>后续步骤

- [Kusto 查询语言的参考文档](/azure/kusto/query/)。
