---
title: "在 Azure Log Analytics 中使用日志搜索查找数据 | Microsoft 文档"
description: "日志搜索允许你将环境内来自多个源的任意计算机数据进行组合和关联。"
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: 
ms.assetid: 0d7b6712-1722-423b-a60f-05389cde3625
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/07/2017
ms.author: bwren
ms.custom: H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: c3ea7cfba9fbf1064e2bd58344a7a00dc81eb148
ms.openlocfilehash: 6dbe7713c48a60974f1026dddc8ee9d2aeb01708
ms.contentlocale: zh-cn
ms.lasthandoff: 07/20/2017

---
# <a name="find-data-using-log-searches"></a>使用日志搜索查找数据

Log Analytics 的核心是日志搜索功能，该功能允许你将环境内来自多个源的任意计算机数据进行组合和关联。 日志搜索还提供解决方案，以提供围绕某个特定问题区域的度量值。

你可以在“搜索”页上创建查询，然后在搜索时使用 Facet 控件筛选结果。 还可创建高级查询以转换、筛选和报告结果。

常见的日志搜索查询显示在大多数解决方案页面上。 在整个 OMS 控制台中，你可以单击磁贴或钻取其他项目，以使用日志搜索查看有关项目的详细信息。

我们在本教程中演示的示例将涵盖使用日志搜索时的所有基本知识。

我们先从简单、实用的示例开始，然后进行构建，使你了解有关如何使用语法从数据中提取想要的见解的实际用例。

熟悉了搜索技术后，可查看 [Log Analytics 日志搜索引用](log-analytics-search-reference.md)。

## <a name="use-basic-filters"></a>使用基本筛选器
首先需要了解的是，搜索查询的第一部分（在任意“|”竖线前）始终是*筛选器*。 可将其视为 TSQL 中的 Where 子句（它确定从 OMS 数据存储中拉取的数据子集*内容*）。 在数据存储中的搜索主要与指定想要提取的数据特征有关，因此从 Where 子句开始查询是很自然的。

可使用的最基本的筛选器是*关键字*，例如“错误”或“超时”）或计算机名称。 这些简单的查询类型通常返回同一结果集内的各种数据形状。 这是因为 Log Analytics 在系统中有不同的数据*类型*。

### <a name="to-conduct-a-simple-search"></a>若要进行简单搜索
1. 在 OMS 门户中，单击“**日志搜索**”。  
    ![搜索磁贴](./media/log-analytics-log-searches/oms-overview-log-search.png)
2. 在“查询”字段中键入 `error`，然后单击“**搜索**”。  
    ![搜索错误](./media/log-analytics-log-searches/oms-search-error.png)  
    例如，在以下图像中对 `error` 的查询返回了 100,000 条“**事件**”记录（由日志管理收集）、18 条“**ConfigurationAlert**”记录（由配置评估生成）和 12 条“**ConfigurationChange**”记录（由更改跟踪捕获）。   
    ![搜索结果](./media/log-analytics-log-searches/oms-search-results01.png)  

这些筛选器并非真正的对象类型/类。 *类型*只是附加到一段数据的标记、属性或字符串/名称/类别。 系统中的一些文档被标记为“**Type:ConfigurationAlert**”，而另一些文档被标记为“**Type:Perf**或“**Type:Event**”等。 每个搜索结果、文档、记录或条目均显示每段数据的所有原始属性及其值，且可使用这些字段名称在筛选器中指定想要仅对字段中具有该给定值的记录进行检索的时间。

*类型*实际上只是所有记录都具有的一个字段，它与其他任意字段没有差别。 这是基于类型字段的值建立的。 该记录具有不同的形状或形式。 顺便说一句，“**Type=Perf**或“**Type=Event**”也是查询性能数据或事件需要了解的语法。

可在字段名称后和值前使用冒号 (:) 或等号 (=)。 “**Type:Event**和“**Type=Event**”在含义上是等效的，可选择你喜爱的样式。

因此，如果 Type=Perf 记录具有名为“CounterName”的字段，则可编写类似 `Type=Perf CounterName="% Processor Time"` 的查询。

该查询仅提供性能计数器名称为“处理器时间百分比 (%)”的性能数据。

### <a name="to-search-for-processor-time-performance-data"></a>搜索处理器时间性能数据
* 在搜索查询字段中，键入 `Type=Perf CounterName="% Processor Time"`

你也可使搜索更具体，在查询中使用 **InstanceName=_'Total'**，这是 Windows 性能计数器。 也可选择 Facet 和其他“**field:value**”。 该筛选器将被自动添加到查询栏的筛选器中。 可在以下图像中查看此操作。 它演示了在不键入任何内容的情况下，单击并将“**InstanceName:’_Total’**”添加到查询的位置。

![搜索 Facet](./media/log-analytics-log-searches/oms-search-facet.png)

查询现在变为 `Type=Perf CounterName="% Processor Time" InstanceName="_Total"`

在此示例中，无需指定“**Type=Perf**”就能得到此结果。 因为字段 CounterName 和 InstanceName 仅对 Type=Perf 的记录存在，该查询已足够具体，可以返回与前一个较长的结果相同的结果：

```
CounterName="% Processor Time" InstanceName="_Total"
```

这是因为查询中的所有筛选器以 *AND* 相互计算。 实际上，向条件中添加的字段越多，得到的结果越少、越具体且更精确。

例如，查询 `Type=Event EventLog="Windows PowerShell"` 等同于 `Type=Event AND EventLog="Windows PowerShell"`。 它返回所有已登录和从 Windows PowerShell 事件日志中收集的所有事件。 如果通过反复选择同一 Facet 多次添加某个筛选器，则问题很明显 -- 它可能会筛选搜索栏，但仍返回同一结果，因为隐式 AND 运算符始终存在。

可显式使用 NOT 运算符轻松地反转隐式 AND 运算符。 例如：

`Type:Event NOT(EventLog:"Windows PowerShell")` 或与其等效的 `Type=Event EventLog!="Windows PowerShell"` 从不是 Windows PowerShell 日志的其他所有日志中返回所有事件。

或者，可使用其他布尔运算符，例如“OR”。 以下查询返回 EventLog 是应用程序或系统的记录。

```
EventLog=Application OR EventLog=System
```

使用以上查询将同时获得同一结果集中两个日志的条目。

但是，如果通过保留隐式 AND 来删除 OR，则以下查询不会生成任何结果，因为没有同时属于两个日志的事件日志。 每个事件日志条目仅被写入到两个日志之一。

```
EventLog=Application EventLog=System
```


## <a name="use-additional-filters"></a>使用其他筛选器
以下查询对具有已发送数据的所有计算机均返回 2 个事件日志的条目。

```
EventLog=Application OR EventLog=System
```

![搜索结果](./media/log-analytics-log-searches/oms-search-results03.png)

选择其中一个字段或筛选器会将查询范围缩小至某台特定计算机（排除所有其他计算机）。 生成的查询与以下类似。

```
EventLog=Application OR EventLog=System Computer=SERVER1.contoso.com
```

因为是隐式 AND，所以该查询与以下内容等效。

```
EventLog=Application OR EventLog=System AND Computer=SERVER1.contoso.com
```

按以下显式顺序评估每个查询。 请注意括号。

```
(EventLog=Application OR EventLog=System) AND Computer=SERVER1.contoso.com
```

和事件日志字段类似，可通过添加 OR 以只针对一组特定计算机检索数据。 例如：

```
(EventLog=Application OR EventLog=System) AND (Computer=SERVER1.contoso.com OR Computer=SERVER2.contoso.com OR Computer=SERVER3.contoso.com)
```

同样，以下查询仅针对所选的两台计算机返回“**CPU 时间百分比 (%)**”。

```
CounterName="% Processor Time"  AND InstanceName="_Total" AND (Computer=SERVER1.contoso.com OR Computer=SERVER2.contoso.com)
```

### <a name="field-types"></a>字段类型
创建筛选器时，应了解使用日志搜索返回的不同类型字段时的差别。

**可搜索字段**以蓝色显示在搜索结果中。  可以在特定于字段的搜索条件中使用可搜索字段，如下所示：

```
Type: Event EventLevelName: "Error"
Type: SecurityEvent Computer:Contains("contoso.com")
Type: Event EventLevelName IN {"Error","Warning"}
```

**自定义文本搜索字段**以灰色显示在搜索结果中。  它们不能用于特定于可搜索字段等字段的搜索条件中。  仅当对所有字段执行查询时才搜索它们，如下所示。

```
"Error"
Type: Event "Exception"
```


### <a name="boolean-operators"></a>布尔运算符
有了日期时间和数字字段，你可以使用*大于*、*小于*和*小于或等于*搜索值。 可在查询搜索栏中使用简单的运算符，例如 >、<、>=、<=、!=。

可查询某个特定时间段的特定事件日志。 例如，最近 24 小时的事件日志通过以下助记表达式表达。

```
EventLog=System TimeGenerated>NOW-24HOURS
```


#### <a name="to-search-using-a-boolean-operator"></a>使用布尔运算符搜索
* 在搜索查询字段中，键入 `EventLog=System TimeGenerated>NOW-24HOURS`  
    ![使用布尔值搜索](./media/log-analytics-log-searches/oms-search-boolean.png)

尽管可以通过图形方式控制时间间隔（可能在大部分时候想执行此操作），但将时间筛选器直接包括在查询中具有优势。 例如，这非常适用于仪表板，你可以替代每个磁贴的时间，而无需考虑仪表板页面上的*全球*时间选择器）。 有关详细信息，请参阅 [仪表板中的时间问题](http://cloudadministrator.wordpress.com/2014/10/19/system-center-advisor-restarted-time-matters-in-dashboard-part-6/)。

按时间筛选时，请记住，你将会得到两个时间段的*交集*的结果：一个是 OMS 门户 (S1) 中指定的结果，另一个是查询 (S2) 中指定的结果。

![交集](./media/log-analytics-log-searches/oms-search-intersection.png)

这意味着，如果时间段不相交（例如，在 OMS 门户中选择“**本周**”，而在查询中指定“**上周**”），则时间段没有交集，因此不会收到任何结果。

用于 TimeGenerated 字段的比较运算符在其他情况下也很有用。 例如，使用数值型字段。

例如，假设配置评估的警报有以下严重性值：

* 0 = 信息
* 1 = 警告
* 2 = 严重

可使用以下查询查询警告和严重警报，并排除信息值：

```
Type=ConfigurationAlert  Severity>=1
```


你也可以使用范围查询。 这意味着可提供序列中值的开始和结束范围。 例如，如果想查看 Operations Manager 事件日志中 EventID 大于或等于 2100 但不超过 2199 的事件，则以下查询将返回结果。

```
Type=Event EventLog="Operations Manager" EventID:[2100..2199]
```


> [!NOTE]
> 必须使用的范围语法是冒号 (:) 字段:值分隔符和，而*不是*等号 (=)。 将范围的下限和上限括在方括号内，并用两个句点 (..) 将其分隔。
>
>

## <a name="manipulate-search-results"></a>操作搜索结果
在搜索数据时，你会需要优化搜索查询并对结果拥有较好的控制级别。 检索出结果时，可应用命令将其转换。

Log Analytics 搜索中的命令*必须*跟在竖线 (|) 后。 筛选器必须始终是查询字符串的第一部分。 它定义了你使用的数据集，然后将这些结果通过“管道”输入命令。 然后，你可以使用管道来添加其他命令。 这与 Windows PowerShell 管道大致相似。

一般情况下，Log Analytics 搜索语言尝试遵循 PowerShell 样式和指导，使其与 IT 专业人士相似，以减缓学习曲线。

命令具有谓词名称，你可以轻松地识别其执行的操作。  

### <a name="sort"></a>排序
排序命令允许按一个或多个字段定义排序方式。 即使不使用排序命令，默认情况下，系统强制执行按时间降序排序。 最新结果始终显示在搜索结果顶部。 这意味着在运行搜索时，`Type=Event EventID=1234` 执行的实际操作是：

```
Type=Event EventID=1234 **| Sort TimeGenerated desc**
```

这是因为它是你在日志中熟悉的体验类型。 例如，在 Windows 事件查看器中。

可使用 Sort 更改结果返回的方式。 以下示例演示了操作方法。

```
Type=Event EventID=1234 | Sort TimeGenerated asc
```

```
Type=Event EventID=1234 | Sort Computer asc
```

```
Type=Event EventID=1234 | Sort Computer asc,TimeGenerated desc
```


以上简单示例演示了命令的工作方式 -- 它们更改筛选器返回的结果的形状。

### <a name="limit-and-top"></a>LIMIT 和 TOP
另一个少见的命令是 LIMIT。 LIMIT 是类似于 PowerShell 的谓词。 LIMIT 在功能上与 TOP 命令相同。 以下查询返回同一结果。

```
Type=Event EventID=600 | Limit 1
```

```
Type=Event EventID=600 | Top 1
```


#### <a name="to-search-using-top"></a>使用 TOP 搜索
* 在搜索查询字段中，键入 `Type=Event EventID=600 | Top 1`   
    ![搜索 Top](./media/log-analytics-log-searches/oms-search-top.png)

在以上图像中，有 35.8 万条 EventID=600 的记录。 左侧的字段、Facet 和筛选器始终显示有关由查询的*筛选器部分*返回的结果信息，这一部分位于任意管道字符之前。 “**结果**”窗格仅返回最新的 1 个结果，因为示例命令对结果进行了塑造和转换。

### <a name="select"></a>选择
SELECT 命令的行为与 PowerShell 中的 Select-Object 类似。 它返回筛选后的结果，该结果并不具有其全部原始属性。 相反，它仅选择指定的属性。

#### <a name="to-run-a-search-using-the-select-command"></a>使用 SELECT 命令运行搜索
1. 在“查询”中，键入 `Type=Event`，然后单击**搜索**。
2. 在其中一个结果中单击“**+显示更多**”以查看该结果具有的所有属性。
3. 显式选择其中的一些属性，且查询更改为 `Type=Event | Select Computer,EventID,RenderedDescription`。  
    ![搜索 SELECT](./media/log-analytics-log-searches/oms-search-select.png)

想要控制搜索输出并仅选择对浏览有用的数据部分（通常不是完整记录）时，该命令尤为有用。 当不同类型的记录具有*一些*公用属性，但并非*所有*属性都为公用属性时，该命令也很有用。 然后，可生成外观与表更接近（或在导出到 CSV 文件时能够正常运行）的输出，然后在 Excel 中进行修改。

## <a name="use-the-measure-command"></a>使用 Measure 命令
Measure 是 Log Analytics 搜索中功能最全的命令之一。 它允许向数据应用统计*函数*，并聚合按给定字段进行分组的结果。 存在多个 Measure 支持的统计函数。

### <a name="measure-count"></a>Measure count()
要使用的第一个统计函数是 *count()* 函数，它也是最容易理解的函数之一。

来自任意搜素查询的结果（例如 `Type=Event`）在搜索结果左侧显示筛选器（也称为 Facet）。 筛选器根据执行的搜索中结果的给定字段显示值的分布。

![search measure count](./media/log-analytics-log-searches/oms-search-measure-count01.png)

例如，你会在上图中看到“**计算机**”字段，该字段显示，在结果的近 73.9 万个事件中，这些记录中有 68 个针对“**计算机**”字段的唯一和非重复值。 该磁贴仅显示前 5 个值（“**计算机**”字段中最常写入的 5 个值），并按字段中包含该特定值的文档数排序。 在图像中可以看到，在近 36.9 万个事件中，有 9 万个事件来自 OpsInsights04.contoso.com 计算机， 有 8.3 万个事件来自 DB03.contoso.com 计算机，以此类推。

如果想要查看所有值该如何操作（因为磁贴仅显示前 5 个值）？

这就要用到具有 count() 函数的 Measure 命令。 此函数不使用任何参数。 只需指定分组所依据的字段 – 在此示例中为“**计算机**”字段：

`Type=Event | Measure count() by Computer`

![search measure count](./media/log-analytics-log-searches/oms-search-measure-count-computer.png)

但是，“**计算机**”只是在每段数据*中*使用的一个字段（不涉及任何关系数据库，也不存在任何单独的“**计算机**”对象）。 只有数据*中*的值能够描述生成它们的实体类型、一些其他特征和数据方面 – 因此，术语为 *Facet*。 但是，也可按其他字段进行分组。 因为，通过管道输入 Measure 命令的近 73.9 万个事件的原始结果也具有名为“**EventID**”的字段，你可以通过该字段将同一技术应用到组，并通过 EventID 获取事件计数：

```
Type=Event | Measure count() by EventID
```

如果你对包含特定值的实际记录计数不感兴趣，而只想查看值本身的列表，可在末尾处添加 *Select* 命令并仅选择第一列：

```
Type=Event | Measure count() by EventID | Select EventID
```

然后，可在查询中获得更复杂和预先排序的结果，也可仅单击网格中的列。

```
Type=Event | Measure count() by EventID | Select EventID | Sort EventID asc
```

#### <a name="to-search-using-measure-count"></a>使用 Measure count 搜索
* 在搜索查询字段中，键入 `Type=Event | Measure count() by EventID`
* 在查询末尾追加 `| Select EventID`。
* 最后，在查询末尾处追加 `| Sort EventID asc`。

有几个需要注意和强调的要点：

首先，你看到的结果不再是原始结果。 相反，它们是聚合的结果（实质上是结果组）。 这不是问题，但你应该了解，你正在与完全不同的数据形状（与因为聚合/统计函数而匆忙创建的原始形状不同）进行交互。

其次，“**Measure count**”当前仅返回前 100 个不同的结果。 此限制不适用于其他统计函数。 因此，在应用 measure count() 前，通常需要先使用更精确的筛选器来搜索特定项目。

## <a name="use-the-max-and-min-functions-with-the-measure-command"></a>将 Max 和 Min 函数与 Measure 命令结合使用
“**Measure Max()**和“**Measure Min()**”在多种方案中都非常有用。 但是，因为两者互为反函数，所以我们将演示 Max()，而你可以自行尝试 Min()。

如果查询安全事件，它们的“**级别**”属性可能不同。 例如：

```
Type=SecurityEvent
```

![search measure count start](./media/log-analytics-log-searches/oms-search-measure-max01.png)

如果想要查看由常用的分组字段 Computer 提供的所有安全事件的最高值，可使用

```
Type=ConfigurationAlert | Measure Max(Level) by Computer
```

![search measure max computer](./media/log-analytics-log-searches/oms-search-measure-max02.png)

它对具有“**级别**”记录的计算机显示结果（其中大部分记录至少具有 8 级，许多记录有 16 级）。

```
Type=ConfigurationAlert | Measure Max(Severity) by Computer
```

![search measure max time generated computer](./media/log-analytics-log-searches/oms-search-measure-max03.png)

此函数适用于数字，也适用于 DateTime 字段。 它可用于检查为每台计算机进行索引的任意数据块的最后或最近时间戳。 例如，报告每台计算机的最新安全事件的时间？

```
Type=ConfigurationChange | Measure Max(TimeGenerated) by Computer
```

## <a name="use-the-avg-function-with-the-measure-command"></a>将 avg 函数与 Measure 命令结合使用
与 Measure 结合使用的 Avg() 统计函数可计算某些字段的平均值，并按相同或其他字段对结果分组。 这在各种事例中都很有用（如性能数据）。

我们从性能数据开始。 注意，OMS 目前为 Windows 和 Linux 计算机收集性能计数器。

若要搜索*所有*性能数据，最基本的查询是：

```
Type=Perf
```

![search avg start](./media/log-analytics-log-searches/oms-search-avg01.png)

你注意到的第一点是，Log Analytics 显示三个方面：列表（显示图表后的实际记录）；表（显示性能计数器数据的表格视图；以及指标（显示性能计数器的图表）。

在上面的图像中有两组标记的字段，表示以下内容：

* 第一组指示查询筛选器中的 Windows 性能计数器名称、对象名称和实例名称。 这些字段是可能最常用作 Facet/筛选器的字段。
* “**CounterValue**”是计数器的实际值。 在此示例中，该值是 *75*。
* “**TimeGenerated**”是 12:51，采用 24 小时时间格式。

以下是图表中的指标视图。

![search avg start](./media/log-analytics-log-searches/oms-search-avg02.png)

阅读了性能记录形状和其他搜索技术后，可使用 Measure Avg() 聚合此类型的数值数据。

以下是一个简单的示例：

```
Type=Perf  ObjectName:Processor  InstanceName:_Total  CounterName:"% Processor Time" | Measure Avg(CounterValue) by Computer
```

![search avg samplevalue](./media/log-analytics-log-searches/oms-search-avg03.png)

在此示例中，通过计算机选择 CPU 总时间性能计数器和平均值。 如果要将结果范围缩小至过去 6 小时，可使用时间筛选器控件或在查询中指定，如下所示：

```
Type=Perf  ObjectName:Processor  InstanceName:_Total  CounterName:"% Processor Time" TimeGenerated>NOW-6HOURS | Measure Avg(CounterValue) by Computer
```

### <a name="to-search-using-the-avg-function-with-the-measure-command"></a>结合 avg 函数与 Measure 命令进行搜索
* 在搜索查询框中，键入 `Type=Perf  ObjectName:Processor  InstanceName:_Total  CounterName:"% Processor Time" TimeGenerated>NOW-6HOURS | Measure Avg(CounterValue) by Computer`。

可以*跨*计算机聚合并关联数据。 例如，假设在某些服务器场中有一组主机，其中每个节点与其他节点等同并执行同样类型的任务，且负荷应大致平衡。 可使用以下查询一次性获取其计数器，并获取整个服务器场的平均值。 可通过使用以下示例从选择计算机开始：

```
Type=Perf AND (Computer="AzureMktg01" OR Computer="AzureMktg02" OR Computer="AzureMktg03")
```

现在，拥有了计算机，还想仅选择两个关键性能指标 (KPI)：CPU 使用情况百分比和可用磁盘空间百分比。 因此，此部分查询变为：

```
Type=Perf InstanceName:_Total  ((ObjectName:Processor AND CounterName:"% Processor Time") OR (ObjectName="LogicalDisk" AND CounterName="% Free Space")) AND TimeGenerated>NOW-4HOURS
```

现在，可使用以下示例添加计算机和计数器：

```
Type=Perf InstanceName:_Total  ((ObjectName:Processor AND CounterName:"% Processor Time") OR (ObjectName="LogicalDisk" AND CounterName="% Free Space")) AND TimeGenerated>NOW-4HOURS AND (Computer="AzureMktg01" OR Computer="AzureMktg02" OR Computer="AzureMktg03")
```

因为选择非常具体，只需通过按 CounterName 进行分组，“**Measure Avg()**”命令不会按计算机而是跨服务器场返回平均值。 例如：

```
Type=Perf  InstanceName:_Total  ((ObjectName:Processor AND CounterName:"% Processor Time") OR (ObjectName="LogicalDisk" AND CounterName="% Free Space")) AND TimeGenerated>NOW-4HOURS AND (Computer="AzureMktg01" OR Computer="AzureMktg02" OR Computer="AzureMktg03") | Measure Avg(CounterValue) by CounterName
```

提供了几个实用的环境 KPI 的紧凑视图。

![search avg grouping](./media/log-analytics-log-searches/oms-search-avg04.png)

可在仪表板中轻松地使用搜索查询。 例如，可保存搜索查询并从中创建一个名为 *Web 场 KPI* 的仪表板。 若要了解有关使用仪表板的详细信息，请参阅 [在 Log Analytics 中创建自定义仪表板](log-analytics-dashboards.md)。

![search avg dashboard](./media/log-analytics-log-searches/oms-search-avg05.png)

### <a name="use-the-sum-function-with-the-measure-command"></a>将 Sum 函数与 Measure 命令结合使用
Sum 函数与 Measure 命令的其他函数类似。 可在 [Microsoft Azure 操作见解中的 W3C IIS 日志搜索](http://blogs.msdn.com/b/dmuscett/archive/2014/09/20/w3c-iis-logs-search-in-system-center-advisor-limited-preview.aspx) 中查看有关如何使用 SUM 函数的示例。

可将 Max() 和 Min() 与数字、日期时间和文本字符串结合使用。 文本字符串按字母排序，将获得第一个和最后一个字符串。

但是，不能将 Sum() 与任意非数字字段一起使用。 这同样适用于 Avg()。

### <a name="use-the-percentile-function-with-the-measure-command"></a>将 Percentile 函数与 Measure 命令结合使用
Percentile 函数与 Avg() 和 Sum() 的相似之处在于，你仅可将其用于数字字段。 可在数字字段上使用 1 到 99 之间的任意百分位数。 也可同时使用“**Percentile**”和“**PCT**”命令。 以下是几个示例：  

```
Type:Perf CounterName:"DiskTransers/sec" |measure percentile95(CurrentValue) by Computer
```
```
Type:Perf ObjectName=LogicalDisk CounterName="Current Disk Queue Length" Computer="MyComputerName" | measure pct65(CurrentValue) by InstanceName
```

## <a name="use-the-where-command"></a>使用 Where 命令
Where 命令的工作方式与筛选器类似，但它可应用于管道，以进一步筛选由 Measure 命令生成的聚合结果（这与查询开始时筛选的原始结果相反）。

例如：

```
Type=Perf  CounterName="% Processor Time"  InstanceName="_Total" | Measure Avg(CounterValue) as AVGCPU by Computer
```

可添加另一个管道“|”字符和 Where 命令，以仅获取平均 CPU 高于 80% 的计算机，如以下示例中所示：

```
Type=Perf  CounterName="% Processor Time"  InstanceName="_Total" | Measure Avg(CounterValue) as AVGCPU by Computer | Where AVGCPU>80
```

如果熟悉 Microsoft System Center - Operations Manager，可将其视为管理包术语中的 Where 命令。 如果该示例是一条规则，则该查询的第一部分将为数据源，而 Where 命令将为条件检测。

可将查询用作“**我的仪表板**”中的磁贴并作为排序的监视器，以查看计算机 CPU 过度使用的时间。 若要了解有关仪表板的详细信息，请参阅 [在 Log Analytics 中创建自定义仪表板](log-analytics-dashboards.md)。 也可使用移动应用创建和使用仪表板。 有关详细信息，请参阅 [OMS 移动应用](http://www.windowsphone.com/en-us/store/app/operational-insights/4823b935-83ce-466c-82bb-bd0a3f58d865)。 在下图中底部的两个磁贴中，可看到监视器以数字形式显示的列表。 从本质上说，你始终希望该数字为零且列表为空。 否则，它表示警报条件。 如果需要，可用它查看哪些计算机处于压力下。

![移动仪表板](./media/log-analytics-log-searches/oms-search-mobile.png)

## <a name="use-the-in-operator"></a>使用 IN 运算符
*IN* 运算符和 *NOT IN* 运算符允许使用子搜索，该搜索将其他搜索包括为参数。 它们被包含在其他*主*搜索或*外部*搜索的大括号 {} 中。 然后，将子搜索的结果（通常是非重复结果的列表）用作其主搜索中的参数。

可使用子搜索匹配无法在搜索表达式中直接描述，但可从搜索中生成的数据子集。 例如，如果对使用一个搜索查找来自*缺少安全更新的计算机*的所有事件感兴趣，则需设计一个子搜索，该搜索在查找属于这些主机的事件前将首先对*缺少安全更新的计算机*进行标识。

因此，可使用以下查询来表达*当前缺少所需安全更新的计算机*

```
Type:Update UpdateState=Needed Optional=false Classification="Security Updates" TimeGenerated>NOW-24HOURS | measure count() by Computer
```    

![IN 搜索示例](./media/log-analytics-log-searches/oms-search-in01-revised.png)

拥有列表后，即可将搜索用作内部搜索，以将计算机列表注入外部（主）搜索（该搜索将查找这些计算机的事件）。 操作方法是，将内部搜索括在大括号内，并将其结果作为使用 IN 运算符的外部搜索中的筛选器/字段的可能值。 该查询将类似于：

```
Type=Event Computer IN {Type:Update UpdateState=Needed Optional=false Classification="Security Updates" TimeGenerated>NOW-24HOURS | measure count() by Computer}
```
![IN 搜索示例](./media/log-analytics-log-searches/oms-search-in02-revised.png)

另请注意内部搜索中使用的时间筛选器，因为系统更新评估每隔 24 小时会拍摄所有计算机的快照。 可通过仅搜索一天的结果使内部查询更为轻量和精确。 而外部搜索在用户界面中使用时间选择来检索过去 7 天的事件。 请参阅 [布尔运算符](#boolean-operators) 以了解有关时间运算符的详细信息。

因为你实际上仅使用内部搜索的结果作为外部搜索的筛选值，所以仍可在外部搜索中应用命令。 例如，仍可使用其他 Measure 命令对以上事件进行分组：

```
Type=Event Computer IN {Type:Update UpdateState=Needed Optional=false Classification="Security Updates" TimeGenerated>NOW-24HOURS | measure count() by Computer} | measure count() by Source
```

![IN 搜索示例](./media/log-analytics-log-searches/oms-search-in03-revised.png)

通常情况下，你会希望内部查询快速执行，因为 Log Analytics 对其具有服务端超时并返回结果的一小部分。 如果内部查询返回更多结果，结果列表将被截断，这可能导致外部搜索返回不正确的结果。

另一条规则是，内部搜索当前需提供*聚合*结果。 换言之，它必须包含 *Measure* 命令；目前不能将原始结果注入外部搜索。

此外，可只存在一个 IN 运算符，但它必须是查询中的最后一个筛选器。 多个 IN 运算符不能为 OR’d，这实质上阻止了多个子搜索运行：重要的一点是，对于每个外部搜索仅能使用一个子/内部搜索。

即使具有这些限制，IN 仍适用于多种类型的关联搜索，并能够定义计算机、用户或文件等与组类似的项（与数据包含的字段类型无关）。 以下是更多示例：

**禁用了自动更新设置的计算机缺少所有更新**

```
Type=Update UpdateState=Needed Optional=false Computer IN {Type=UpdateSummary WindowsUpdateSetting=Manual | measure count() by Computer} | measure count() by KBID
```

**来自运行 SQL Server 的计算机的所有错误事件（其中 SQL 评估已运行）**

```
Type=Event EventLevelName=error Computer IN {Type=SQLAssessmentRecommendation | measure count() by Computer}
```

**来自具有域控制器的计算机的所有安全事件（其中 AD 评估已运行）**

```
Type=SecurityEvent Computer IN { Type=ADAssessmentRecommendation | measure count() by Computer }
```

**已登录 BACONLAND\jochan 帐户的计算机还登录了哪些其他帐户？**

```
Type=SecurityEvent EventID=4624   Account!="BACONLAND\\jochan" Computer IN { Type=SecurityEvent EventID=4624   Account="BACONLAND\\jochan" | measure count() by Computer } | measure count() by Account
```

## <a name="use-the-distinct-command"></a>使用 distinct 命令
顾名思义，该命令为字段提供非重复值列表。 它很简单，但非常实用。 也可使用 measure count() 命令实现相同结果，如下所示。

```
Type=Event | Measure count() by Computer
```

![DISTINCT 搜索命令示例](./media/log-analytics-log-searches/oms-search-distinct01-revised.png)

但是，如果仅对非重复值列表感兴趣，而并不关注具有该值的文档计数，DISTINCT 还可提供更简洁、更容易的读取输出的方法和更短的语法，如以下所示。

```
Type=Event | Distinct Computer
```
![DISTINCT 搜索命令示例](./media/log-analytics-log-searches/oms-search-distinct02-revised.png)

## <a name="use-the-countdistinct-function-with-the-measure-command"></a>将 countdistinct 函数与 Measure 命令结合使用
Countdistinct 函数计算每个组内的非重复值的数目。 例如，可将其用于计算向每个类型报告的唯一计算机的数目：

```
* | measure countdistinct(Computer) by Type
```

![OMS-countdistinct](./media/log-analytics-log-searches/oms-countdistinct.png)

## <a name="use-the-measure-interval-command"></a>使用 Measure interval 命令
通过接近实时的性能数据收集，可收集和可视化 Log Analytics 中的任意性能计数器。 只需输入查询“**Type:Perf**”即可返回数千个基于 Log Analytics 环境中的计数器数和服务器数的度量值图表。 通过按需度量值聚合，可在高级别下查看环境中的整体度量值，并根据需要深入了解更具体的数据。

假设想要了解跨所有计算机的平均 CPU。 查看每个计算机的平均 CPU 可能没有什么帮助，因为结果可能会被消除。 若要查看详细信息，可在更小的时间窗口区块中聚合结果，并跨不同维度查看时间序列。 例如，可跨所有计算机执行 CPU 使用情况的每小时平均值，如下所示：

```
Type:Perf CounterName="% Processor Time" InstanceName="_Total" | measure avg(CounterValue) by Computer Interval 1HOUR
```

![measure average interval](./media/log-analytics-log-searches/oms-measure-avg-interval.png)

默认情况下，这些结果以多系列交互折线图的形式显示。  此图表支持系列切换（通过 y 轴重新调整）、缩放和将鼠标悬停。  表显示选项仍可用于查看原始数据（如有必要）。

也可按其他字段分组。 在此示例中，我将查看一个特定计算机的所有计数器百分比，并想要了解每个计数器每小时的 70 百分点值。

```
Type:Perf Computer=beefpatty4 CounterName=%* InstanceName=_Total | measure percentile70(CounterValue) by CounterName Interval 1HOUR
```
需要注意的一点是，这些查询并不限于性能计数器。 可将其应用到任意度量值。 在此示例中，我将查看 W3C IIS 日志。 我想要了解处理每个请求的 5 分钟间隔所需的最长时间：

```
Type:W3CIISLog | measure max(TimeTaken) by csMethod Interval 5MINUTES
```

### <a name="use-multiple-aggregates-in-one-query"></a>在一个查询中使用多个聚合
可在 Measure 命令中指定多个聚合子句。  每个子句均可使用独立的别名。  若未提供别名，则生成的字段名称将是用于 avg(CounterValue) 的聚合函数（例如，avg(CounterValue)）。

 ```
Type=WireData | measure avg(ReceivedBytes), avg(SentBytes) by Direction interval 1hour
```
![OMS-multiaggregates1](./media/log-analytics-log-searches/oms-multiaggregates1.png)

以下是另一个示例：

 ```
* | measure countdistinct(Computer) as Computers, count() as TotalRecords by Type
```


## <a name="next-steps"></a>后续步骤
有关日志搜索的其他信息，请参阅：

* 使用 [Log Analytics 中的自定义字段](log-analytics-custom-fields.md)扩展日志搜索。
* 查看 [Log Analytics 日志搜索引用](log-analytics-search-reference.md)以查看 Log Analytics 中所有可用的搜索字段和 Facet。

