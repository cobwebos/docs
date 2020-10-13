---
title: 优化 Azure Monitor 中的日志查询
description: 用于优化 Azure Monitor 中的日志查询的最佳做法。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/30/2019
ms.openlocfilehash: 31b1ff3324c610c385ad793f124735be30cab9f9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91327708"
---
# <a name="optimize-log-queries-in-azure-monitor"></a>优化 Azure Monitor 中的日志查询
Azure Monitor 日志使用 [Azure 数据资源管理器 (ADX)](/azure/data-explorer/) 来存储日志数据，并运行查询来分析这些数据。 它为你创建、管理和维护 ADX 群集，并针对你的日志分析工作负荷优化它们。 运行查询时，将对其进行优化，并将其路由到存储着工作区数据的相应 ADX 群集。 Azure Monitor 日志和 Azure 数据资源管理器都使用许多自动查询优化机制。 虽然自动优化已提供了显著的性能提升，但在某些情况下，你还可以显著提高查询性能。 本文介绍了性能注意事项和解决相关问题的几种方法。

大多数方法对于直接在 Azure 数据资源管理器和 Azure Monitor 日志上运行的查询是通用的，但我们在这里讨论的是几个独特的 Azure Monitor 日志注意事项。 如需更多的 Azure 数据资源管理器优化技巧，请参阅[查询最佳做法](/azure/kusto/query/best-practices)。

优化的查询具有以下特点：

- 运行速度更快，缩短了查询执行操作的总持续时间。
- 被限制或拒绝的可能性更小。

应特别注意反复使用的和阵发性的查询，例如涉及仪表板、警报、逻辑应用和 Power BI 的查询。 在这些情况下，无效查询的影响是巨大的。

## <a name="query-performance-pane"></a>查询性能窗格
在 Log Analytics 中运行查询后，单击查询结果上方的向下箭头可查看查询性能窗格，其中显示查询的多个性能指标的结果。 下一部分介绍了这些性能指标中的每一个。

![查询性能窗格](media/query-optimization/query-performance-pane.png)


## <a name="query-performance-indicators"></a>查询性能指标

针对所执行的每个查询提供了以下查询性能指标：

- [总 CPU 时间](#total-cpu)：用来在所有计算节点中处理此查询的总体计算。 它表示用于计算、分析和数据提取的时间。 

- [用于已处理查询的数据](#data-used-for-processed-query)：处理查询时访问的总体数据。 受目标表大小、所用时间跨度、已应用筛选器和已引用列数影响。

- [已处理查询的时间跨度](#time-span-of-the-processed-query)：处理查询时访问的最新数据与最旧数据之间的间隔。 受为查询指定的显式时间范围影响。

- [已处理数据的年限](#age-of-processed-data)：当前时间与处理查询时访问最旧数据的时间之间的间隔。 它会极大影响数据提取效率。

- [工作区数量](#number-of-workspaces)：在查询处理过程中按隐式或显式选择要求访问的工作区数。

- [区域数量](#number-of-regions)：在查询处理过程中按照对工作区的隐式或显式选择要求访问的区域数。 多区域查询的效率要低得多，并且性能指标只覆盖了部分区域。

- [并行度](#parallelism)：指明系统能够在多个节点上执行此查询的程度。 仅适用于 CPU 消耗较高的查询。 受使用的具体函数和运算符影响。


## <a name="total-cpu"></a>总 CPU 时间
在所有查询处理节点中处理此查询而投入的实际计算 CPU。 由于大多数查询是在大量节点上执行的，因此此时间通常会比查询实际执行的持续时间长得多。 

利用超过100秒 CPU 的查询被视为消耗过多资源的查询。 利用超过1000秒 CPU 的查询被视为滥用查询，可能会受到限制。

查询处理时间花费在：
- 数据检索–旧数据的检索时间比检索最新数据所用时间更长。
- 数据处理–数据的逻辑和计算。 

除了在查询处理节点中所花费的时间以外，Azure Monitor 日志还需要花费额外的时间来执行以下操作：对用户进行身份验证，并验证是否允许他们访问此数据、查找数据存储、分析查询和分配查询处理节点。 此时间不包括在查询总 CPU 时间内。

### <a name="early-filtering-of-records-prior-of-using-high-cpu-functions"></a>使用 CPU 使用率过高的函数之前提前筛选记录

某些查询命令和函数的 CPU 消耗很高。 对于分析 JSON 和 XML 或提取复杂的正则表达式的命令尤其如此。 这种分析可以通过 [parse_json()](/azure/kusto/query/parsejsonfunction) 或 [parse_xml()](/azure/kusto/query/parse-xmlfunction) 函数显式地进行，也可以在引用动态列时隐式地进行。

这些函数消耗的 CPU 与它们正在处理的行数成正比。 最高效的优化是在查询中提前添加 where 条件，这些条件可以在执行 CPU 密集型函数之前筛选出尽可能多的记录。

例如，以下查询产生完全相同的结果，但第二个查询的效率最高，因为其中的 [where](/azure/kusto/query/whereoperator) 条件在分析之前排除了许多记录：

```Kusto
//less efficient
SecurityEvent
| extend Details = parse_xml(EventData)
| extend FilePath = tostring(Details.UserData.RuleAndFileData.FilePath)
| extend FileHash = tostring(Details.UserData.RuleAndFileData.FileHash)
| where FileHash != "" and FilePath !startswith "%SYSTEM32"  // Problem: irrelevant results are filtered after all processing and parsing is done
| summarize count() by FileHash, FilePath
```
```Kusto
//more efficient
SecurityEvent
| where EventID == 8002 //Only this event have FileHash
| where EventData !has "%SYSTEM32" //Early removal of unwanted records
| extend Details = parse_xml(EventData)
| extend FilePath = tostring(Details.UserData.RuleAndFileData.FilePath)
| extend FileHash = tostring(Details.UserData.RuleAndFileData.FileHash)
| where FileHash != "" and FilePath !startswith "%SYSTEM32"  // exact removal of results. Early filter is not accurate enough
| summarize count() by FileHash, FilePath
| where FileHash != "" // No need to filter out %SYSTEM32 here as it was removed before
```

### <a name="avoid-using-evaluated-where-clauses"></a>避免使用涉及计算的 where 子句

如果查询包含的 [where](/azure/kusto/query/whereoperator) 子句基于某个计得列而不是数据集中实际存在的列，则查询的效率会降低。 在处理大型数据集时，针对计得列进行筛选会妨碍某些系统优化。
例如，以下查询产生完全相同的结果，但第二个查询的效率更高，因为其中的 [where](/azure/kusto/query/whereoperator) 条件引用了内置的列

```Kusto
//less efficient
Syslog
| extend Msg = strcat("Syslog: ",SyslogMessage)
| where  Msg  has "Error"
| count 
```
```Kusto
//more efficient
Syslog
| where  SyslogMessage  has "Error"
| count 
```

在某些情况下，计算列由查询处理 enine 隐式创建，因为筛选操作不只是在字段上执行：
```Kusto
//less efficient
SecurityEvent
| where tolower(Process) == "conhost.exe"
| count 
```
```Kusto
//more efficient
SecurityEvent
| where Process =~ "conhost.exe"
| count 
```




### <a name="use-effective-aggregation-commands-and-dimensions-in-summarize-and-join"></a>在汇总和联接中使用高效的聚合命令和维度

某些聚合命令，例如 [max()](/azure/kusto/query/max-aggfunction)、[sum()](/azure/kusto/query/sum-aggfunction)、[count()](/azure/kusto/query/count-aggfunction) 和 [avg()](/azure/kusto/query/avg-aggfunction)，由于它们的逻辑，对 CPU 的影响很小，而另一些则较复杂，包括试探方法和估算，这使它们能够高效地执行。 例如，[dcount()](/azure/kusto/query/dcount-aggfunction) 使用 HyperLogLog 算法提供对大型数据集的非重复计数的近似估算，而不对每个值进行实际计数；百分位函数使用最近的秩百分位算法执行类似的粗略估算。 多个命令包括了可选参数来降低其影响。 例如，[makeset()](/azure/kusto/query/makeset-aggfunction) 函数使用一个可选参数来定义最大集大小，该大小会显著影响 CPU 和内存。

[Join](/azure/kusto/query/joinoperator?pivots=azuremonitor) 和 [summarize](/azure/kusto/query/summarizeoperator) 命令在处理大型数据集时可能会导致 CPU 利用率较高。 它们的复杂性与在汇总中用作 `by` 或用作联接属性的列的可能值的数量（称为“基数”）直接相关。 有关对联接和汇总的说明和优化，请参阅它们的文档文章和优化技巧。

例如，下面的查询产生完全相同的结果，因为 **CounterPath** 始终一对一映射到 **CounterName** 和 **ObjectName**。 第二个查询更为高效，因为聚合维度较小：

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

CPU 消耗还可能会受 where 条件或需要密集计算的扩展列的影响。 所有琐碎的字符串比较（例如 [equal ==](/azure/kusto/query/datatypes-string-operators) 和 [startswith](/azure/kusto/query/datatypes-string-operators)）对 CPU 的影响大致相同，而高级文本匹配则影响更大。 具体而言，[has](/azure/kusto/query/datatypes-string-operators) 运算符比 [contains](/azure/kusto/query/datatypes-string-operators) 运算符更高效。 查找长度超过四个字符的字符串比查找短字符串更高效，因为存在字符串处理技术。

例如，下面的查询将产生类似的结果，具体取决于计算机命名策略，但第二个查询更高效：

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
> 此指标仅显示来自紧邻群集的 CPU。 在多区域查询中，它仅显示其中一个区域。 在多工作区查询中，它可能不包括所有工作区。

### <a name="avoid-full-xml-and-json-parsing-when-string-parsing-works"></a>当字符串分析有效时，请避免使用完全 XML 和 JSON 分析
完全分析某个 XML 或 JSON 对象可能会消耗大量 CPU 和内存资源。 在许多情况下，当只需要一两个参数并且 XML 或 JSON 对象很简单时，可以使用 [parse 运算符](/azure/kusto/query/parseoperator)或其他[文本分析技术](./parse-text.md)将它们分析为字符串，这样更简单。 随着 XML 或 JSON 对象中的记录数增加，性能提升会更明显。 当记录的数量达到数千万时，这一点至关重要。

例如，下面的查询将返回与上面的查询完全相同的结果，但不执行完全 XML 分析。 请注意，它对 XML 文件结构做了一些假设，例如，FilePath 元素位于 FileHash 之后，并且它们都没有属性。 

```Kusto
//even more efficient
SecurityEvent
| where EventID == 8002 //Only this event have FileHash
| where EventData !has "%SYSTEM32" //Early removal of unwanted records
| parse EventData with * "<FilePath>" FilePath "</FilePath>" * "<FileHash>" FileHash "</FileHash>" *
| summarize count() by FileHash, FilePath
| where FileHash != "" // No need to filter out %SYSTEM32 here as it was removed before
```


## <a name="data-used-for-processed-query"></a>用于已处理查询的数据

在处理查询的过程中，一个重要因素是经扫描后用于查询处理的数据量。 与其他数据平台相比，Azure 数据资源管理器使用严格的优化，大大减少了数据量。 尽管如此，查询中也存在一些重要因素，这些因素可能会影响所使用的数据量。

处理2个以上的000KB 数据的查询被视为消耗过多资源的查询。 正在处理的查询超过20，000KB 的数据被视为滥用查询，可能会受到限制。

在 Azure Monitor 日志中，**TimeGenerated** 列用作为数据编制索引的方式。 将 **TimeGenerated** 值限制在尽可能窄的范围内会显著限制必须处理的数据量，从而显著提高查询性能。

### <a name="avoid-unnecessary-use-of-search-and-union-operators"></a>避免不必要地使用 search 和 union 运算符

增加处理数据的另一个因素是使用大量的表。 使用 `search *` 和 `union *` 命令时通常会发生这种情况。 这些命令强制系统对工作区中所有表的数据进行评估和扫描。 在某些情况下，工作区中可能有数百个表。 尽量避免使用“search *”或未将范围限定为特定表的任何搜索。

例如，下面的查询生成完全相同的结果，但最后一个查询最高效：

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

减少数据量的另一种方法是在查询中提前使用 [where](/azure/kusto/query/whereoperator) 条件。 Azure 数据资源管理器平台包含一个缓存，该缓存可让它知道哪些分区包含与特定 where 条件相关的数据。 例如，如果查询包含 `where EventID == 4624`，则它只将查询分布到对包含匹配事件的分区进行处理的节点。

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

### <a name="avoid-multiple-scans-of-same-source-data-using-conditional-aggregation-functions-and-materialize-function"></a>使用条件聚合函数和 materialize 函数避免多次扫描相同源数据
如果查询包含多个使用 join 或 union 运算符合并的子查询，则每个子查询会分别扫描整个源，然后合并结果。 这样就会导致扫描数据的次数倍增，这对于大型数据集是个至关重要的因素。

避免这种情况的一种方法是使用条件聚合函数。 在 summarize 运算符中使用的大多数[聚合函数](/azure/data-explorer/kusto/query/summarizeoperator#list-of-aggregation-functions)都有一个带条件的版本，该版本允许配合多个条件使用一个 summarize 运算符。 

例如，下面的查询显示了每个帐户的登录事件数以及进程执行事件数。 它们返回相同的结果，但第一个扫描两次数据，第二个只扫描一次数据：

```Kusto
//Scans the SecurityEvent table twice and perform expensive join
SecurityEvent
| where EventID == 4624 //Login event
| summarize LoginCount = count() by Account
| join 
(
    SecurityEvent
    | where EventID == 4688 //Process execution event
    | summarize ExecutionCount = count(), ExecutedProcesses = make_set(Process) by Account
) on Account
```

```Kusto
//Scan only once with no join
SecurityEvent
| where EventID == 4624 or EventID == 4688 //early filter
| summarize LoginCount = countif(EventID == 4624), ExecutionCount = countif(EventID == 4688), ExecutedProcesses = make_set_if(Process,EventID == 4688)  by Account
```

另一种不必使用子查询的情况是，对 [parse 运算符](/azure/data-explorer/kusto/query/parseoperator?pivots=azuremonitor)进行预筛选以确保它只处理符合特定模式的记录。 这样做是不必要的，因为 parse 运算符和其他类似的运算符在模式不匹配时会返回空结果。 下面两个查询返回完全相同的结果，但第二个查询只扫描一次数据。 在第二个查询中，每个 parse 命令只与其事件相关。 之后，extend 运算符会显示如何引用空数据情况。

```Kusto
//Scan SecurityEvent table twice
union(
SecurityEvent
| where EventID == 8002 
| parse EventData with * "<FilePath>" FilePath "</FilePath>" * "<FileHash>" FileHash "</FileHash>" *
| distinct FilePath
),(
SecurityEvent
| where EventID == 4799
| parse EventData with * "CallerProcessName\">" CallerProcessName1 "</Data>" * 
| distinct CallerProcessName1
)
```

```Kusto
//Single scan of the SecurityEvent table
SecurityEvent
| where EventID == 8002 or EventID == 4799
| parse EventData with * "<FilePath>" FilePath "</FilePath>" * "<FileHash>" FileHash "</FileHash>" * //Relevant only for event 8002
| parse EventData with * "CallerProcessName\">" CallerProcessName1 "</Data>" *  //Relevant only for event 4799
| extend FilePath = iif(isempty(CallerProcessName1),FilePath,"")
| distinct FilePath, CallerProcessName1
```

当上述情况不允许避免使用子查询时，另一种方法是使用 [materialize() 函数](/azure/data-explorer/kusto/query/materializefunction?pivots=azuremonitor)来提示查询引擎：有一个在这些子查询中的每一个都用到的源数据。 当源数据来自在查询中多次用到的某个函数时，适合使用这种方法。 当子查询的输出比输入小得多时，具体化将有效。 查询引擎将缓存并重复使用输出。



### <a name="reduce-the-number-of-columns-that-is-retrieved"></a>减少检索的列数

由于 Azure 数据资源管理器是一个列式数据存储，因此对每一列的检索都独立于其他列。 检索的列数直接影响总体数据量。 只应在输出中包含对结果进行[汇总](/azure/kusto/query/summarizeoperator)或对特定列进行[投影](/azure/kusto/query/projectoperator)所需的列。 Azure 数据资源管理器采取了多项优化来减少检索的列的数量。 如果它确定不需要某个列（例如，如果在 [summarize](/azure/kusto/query/summarizeoperator) 命令中没有引用该列），它将不会检索该列。

例如，第二个查询可能会处理三倍的数据，因为它需要获取的不是一列而是三列：

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

Azure Monitor 日志中的所有日志都根据 **TimeGenerated** 列进行分区。 访问的分区数与时间跨度直接相关。 减小时间范围是确保快速执行查询的最有效方法。

超过15天的时间跨度的查询被视为消耗过多资源的查询。 超过90天的时间跨度的查询被视为滥用查询，可能会受到限制。

可以使用 Log Analytics 屏幕中的时间范围选择器设置时间范围，如 [Azure Monitor Log Analytics 中的日志查询范围和时间范围](scope.md#time-range)中所述。 这是推荐使用的方法，因为选定的时间范围将使用查询元数据传递到后端。 

另一种方法是在查询中显式地包含针对 **TimeGenerated** 的 [where](/azure/kusto/query/whereoperator) 条件。 你应当使用此方法，因为它可以确保时间跨度是固定的，即使查询是从不同的接口使用的。
你应确保查询的所有部分都具有 **TimeGenerated** 筛选器。 当查询有子查询从不同的表或同一个表中获取数据时，每个查询都必须包含自己的 [where](/azure/kusto/query/whereoperator) 条件。

### <a name="make-sure-all-sub-queries-have-timegenerated-filter"></a>确保所有子查询都具有 TimeGenerated 筛选器

例如，在下面的查询中，对于 **Perf** 表，将只扫描最后一天；对于 **Heartbeat** 表，将扫描其所有历史记录，可能长达两年：

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

出现这种错误的一种常见情况是使用 [arg_max()](/azure/kusto/query/arg-max-aggfunction) 查找最近的匹配项。 例如：

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

通过在内部查询中添加时间筛选器，可以很容易地纠正此问题：

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

此错误的另一个示例是在对多个表进行 [union](/azure/kusto/query/unionoperator?pivots=azuremonitor) 之后执行时间范围筛选。 执行 union 运算时，应确定每个子查询的范围。 可以使用 [let](/azure/kusto/query/letstatement) 语句来确保范围一致性。

例如，以下查询将扫描 *Heartbeat* 和 *Perf* 表中的所有数据，而不是仅仅扫描最后 1 天：

```Kusto
Heartbeat 
| summarize arg_min(TimeGenerated,*) by Computer
| union (
    Perf 
    | summarize arg_min(TimeGenerated,*) by Computer) 
| where TimeGenerated > ago(1d)
| summarize min(TimeGenerated) by Computer
```

应按以下代码所示纠正此查询：

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

### <a name="time-span-measurement-limitations"></a>时间跨度度量限制

度量值始终大于指定的实际时间。 例如，如果查询上的筛选器为 7 天，则系统可能会扫描 7.5 或 8.1 天。 这是因为系统将数据分成大小可变的块。 为了确保扫描所有相关记录，它会扫描整个分区，该分区可能涵盖几个小时甚至不止一天的数据。

在某些情况下，系统无法准确度量时间范围。 在大多数情况下，如果查询范围少于一天或查询为多工作区查询，就会出现这种现象。


> [!IMPORTANT]
> 此指标仅显示在紧邻群集中处理的数据。 在多区域查询中，它仅显示其中一个区域。 在多工作区查询中，它可能不包括所有工作区。

## <a name="age-of-processed-data"></a>已处理数据的年限
Azure 数据资源管理器使用多个存储层：内存中、本地 SSD 磁盘，以及速度慢得多的 Azure Blob。 数据越新，越有可能存储在性能更高且延迟更小的层中，从而减少查询持续时间和 CPU 占用。 除了数据本身以外，系统还有元数据的缓存。 数据越旧，其元数据在缓存中的可能性就越小。

处理大于14天以前的数据的查询被视为消耗过多资源的查询。


虽然有些查询需要使用旧数据，但也有误用旧数据的情况。 执行查询时，如果没有在其元数据中提供时间范围并且不是所有表引用都包含针对 **TimeGenerated** 列的筛选器，则会发生这种情况。 在这些情况下，系统将扫描该表中存储的所有数据。 当数据保留时间较长时，它可能会涵盖较长的时间范围，因此会涵盖在时间上与数据保留期一样长的数据。

下面是这种情况的示例：

- 未使用无限制的子查询在 Log Analytics 中设置时间范围。 请参看上述示例。
- 在不使用时间范围可选参数的情况下使用 API。
- 使用不强制限制时间范围的客户端，例如 Power BI 连接器。

请参阅上一部分中的示例和注释，因为它们在本例中也是相关的。

## <a name="number-of-regions"></a>区域数量
在以下几种情况下，可能会跨不同的区域执行单个查询：

- 当显式列出多个工作区并且它们位于不同的区域中时。
- 当范围为资源的查询提取数据并且数据存储在位于不同区域中的多个工作区中时。

跨区域执行查询要求系统在后端序列化和传输通常比查询最终结果大得多的大块中间数据。 它还限制了系统执行优化和试探法以及使用缓存的能力。
如果没有真正的理由要扫描所有这些区域，则应调整范围，使其涵盖较少的区域。 如果资源范围已最小化，但仍使用了许多区域，则可能是因配置错误而导致的。 例如，审核日志和诊断设置发送到不同区域中的不同工作区，或者存在多个诊断设置配置。 

跨超过3个区域的查询被视为消耗过多资源的查询。 跨超过6个区域的查询被视为滥用查询，可能会受到限制。

> [!IMPORTANT]
> 当查询跨多个区域运行时，CPU 和数据度量将不准确，并且将仅显示其中一个区域上的度量。

## <a name="number-of-workspaces"></a>工作区数量
工作区是用于隔离和管理日志数据的逻辑容器。 后端会优化选定区域内物理群集上的工作区放置。

使用多个工作区的原因可能是： 

- 显式列出了多个工作区。
- 范围为资源的查询提取数据并且数据存储在多个工作区中。
 
跨区域和跨群集执行查询要求系统在后端序列化和传输通常比查询最终结果大得多的大块中间数据。 它还限制了系统执行优化和试探法以及使用缓存的能力。

跨超过5个工作区的查询被视为消耗过多资源的查询。 查询不能跨超过100个工作区。

> [!IMPORTANT]
> 在某些多工作区方案中，CPU 和数据度量会不准确，并且只会显示几个工作区的度量。

## <a name="parallelism"></a>并行度
Azure Monitor 日志使用 Azure 数据资源管理器的大型群集来运行查询，这些群集的规模各不相同，可能会产生多达数十个计算节点。 系统会根据工作区放置逻辑和容量自动缩放群集。

为了高效地执行查询，系统会根据处理查询时所需的数据将查询分区并分布到不同的计算节点上。 在某些情况下，系统无法高效地执行此操作。 这可能会导致查询持续很长时间。 

可能会降低并行度的查询行为包括：

- 使用序列化和窗口函数，例如 [serialize 运算符](/azure/kusto/query/serializeoperator)、[next()](/azure/kusto/query/nextfunction)、[prev()](/azure/kusto/query/prevfunction) 和 [row](/azure/kusto/query/rowcumsumfunction) 函数。 在这些情况下，有时候可能会使用时序和用户分析功能。 如果在非查询末尾的位置使用了以下运算符，则可能会导致序列化低效：[range](/azure/kusto/query/rangeoperator)、[sort](/azure/kusto/query/sortoperator)、[order](/azure/kusto/query/orderoperator)、[top](/azure/kusto/query/topoperator)、[top-hitters](/azure/kusto/query/tophittersoperator)、[getschema](/azure/kusto/query/getschemaoperator)。
-    使用 [dcount()](/azure/kusto/query/dcount-aggfunction) 聚合函数会强制系统将非重复值存储在中心副本中。 当数据规模较大时，请考虑使用 dcount 函数可选参数来降低精度。
-    在许多情况下，[join](/azure/kusto/query/joinoperator?pivots=azuremonitor) 运算符会降低整体并行度。 当性能有问题时，看是否可以使用 shuffle join 作为替代方法。
-    在资源范围查询中，预执行 RBAC 检查在存在海量 Azure 角色分配的情况下可能会延迟。 这可能会导致检查时间延长，并且会导致并行度降低。 例如，查询在有数千个资源的订阅上执行，每个资源在资源级别（而不是在订阅或资源组上）有许多角色分配。
-    如果查询处理的是小块数据，那么它的并行度将很低，因为系统不会将它分布到许多计算节点上。



## <a name="next-steps"></a>后续步骤

- [Kusto 查询语言的参考文档](/azure/kusto/query/)。
