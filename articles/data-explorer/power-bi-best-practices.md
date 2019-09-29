---
title: 使用 Power BI 查询和可视化 Azure 数据资源管理器数据的最佳做法
description: 本文介绍使用 Power BI 查询和可视化 Azure 数据资源管理器数据的最佳做法。
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/26/2019
ms.openlocfilehash: 53bed3fe50afef260ac44f73a9f82e6894015c90
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349007"
---
# <a name="best-practices-for-using-power-bi-to-query-and-visualize-azure-data-explorer-data"></a>使用 Power BI 查询和可视化 Azure 数据资源管理器数据的最佳做法

Azure 数据资源管理器是一项快速且高度可缩放的数据探索服务，适用于日志和遥测数据。 [Power BI](https://docs.microsoft.com/power-bi/)是一种业务分析解决方案，可让你直观显示数据，并在组织中共享结果。 Azure 数据资源管理器提供三个选项用于连接到 Power BI 中的数据。 使用[内置连接器](power-bi-connector.md)，将[查询从 Azure 数据资源管理器导入 Power BI](power-bi-imported-query.md)或使用[SQL 查询](power-bi-sql-query.md)。 本文提供了有关在 Power BI 中查询和可视化 Azure 数据资源管理器数据的技巧。 

## <a name="best-practices-for-using-power-bi"></a>使用 Power BI 的最佳实践 

使用 tb 的全新原始数据时，请遵循以下准则，使 Power BI 的仪表板和报表保持 snappy 和更新：

* **旅行灯**-仅将报表所需的数据 Power BI。 对于深层交互分析，请使用针对 Kusto 查询语言进行即席浏览优化的[Azure 数据资源管理器 WEB UI](web-query-data.md) 。

* **复合模型**-使用[复合模型](https://docs.microsoft.com/power-bi/desktop-composite-models)将顶级仪表板的聚合数据与已筛选的操作原始数据组合在一起。 可以清楚地定义何时使用原始数据以及何时使用聚合视图。 

* **导入模式与 DirectQuery 模式**，使用**导入**模式对较小的数据集进行交互。 将**DirectQuery**模式用于大型、频繁更新的数据集。 例如，使用**导入**模式创建维度表，因为维度表很小并且不经常更改。 根据预期的数据更新速率，设置刷新间隔。 使用**DirectQuery**模式创建事实数据表，因为这些表很大，包含原始数据。 使用这些表可以通过 Power BI[钻取](https://docs.microsoft.com/power-bi/desktop-drillthrough)来显示筛选后的数据。

* **并行**性– Azure 数据资源管理器是可线性缩放的数据平台，因此，你可以通过增加端到端流的并行度来提高仪表板呈现的性能，如下所示：

   * 在 Power BI 中增加[DirectQuery 中的并发连接](https://docs.microsoft.com/power-bi/desktop-directquery-about#maximum-number-of-connections-option-for-directquery)数。

   * 使用[弱一致性提高并行度](/azure/kusto/concepts/queryconsistency)。 这可能会影响数据的新鲜度。

* **有效切片**器–您可以使用[同步切片](https://docs.microsoft.com/power-bi/visuals/power-bi-visualization-slicers#sync-and-use-slicers-on-other-pages)器来防止报表在准备就绪之前加载数据。 构建数据集并标记所有视觉对象并标记所有切片器后，你可以选择同步切片器以仅加载所需数据。

* **使用筛选器**-尽可能多地使用 Power BI 筛选器，使 Azure 数据资源管理器搜索相关的数据分片。

* **有效视觉对象**–为数据选择性能最高的视觉对象。

## <a name="tips-for-using-the-azure-data-explorer-connector-for-power-bi-to-query-data"></a>使用 Azure 数据资源管理器连接器 Power BI 查询数据的技巧

以下部分包含有关在 Power BI 中使用 Kusto 查询语言的提示和技巧。 使用[Azure 数据资源管理器连接器 Power BI](power-bi-connector.md)来可视化数据

### <a name="complex-queries-in-power-bi"></a>Power BI 中的复杂查询

与 Power Query 相比，复杂查询更容易用 Kusto 表示。 它们应该实现为[Kusto 函数](/azure/kusto/query/functions)，并在 Power BI 中调用。 在 Kusto 查询中对 `let` 语句使用**DirectQuery**时，此方法是必需的。 由于 Power BI 联接两个查询，而 `let` 语句不能与 `join` 运算符一起使用，因此可能会出现语法错误。 因此，将联接的每个部分保存为 Kusto 函数，并允许 Power BI 将这两个函数联接在一起。

### <a name="how-to-simulate-a-relative-data-time-operator"></a>如何模拟相对数据时间运算符

Power BI 不包含*相对*日期时间运算符，如 `ago()`。
若要模拟 `ago()`，请结合使用 @no__t 和 @no__t Power BI 函数。

而不是使用 `ago()` 运算符的查询：

```kusto
    StormEvents | where StartTime > (now()-5d)
    StormEvents | where StartTime > ago(5d)
``` 

使用以下等效查询：

```powerquery-m
let
    Source = Kusto.Contents("help", "Samples", "StormEvents", []),
    #"Filtered Rows" = Table.SelectRows(Source, each [StartTime] > (DateTime.FixedLocalNow()-#duration(5,0,0,0)))
in
    #"Filtered Rows"
```

### <a name="reaching-kusto-query-limits"></a>达到 Kusto 查询限制 

默认情况下，Kusto 查询返回最多500000行或 64 MB，如[查询限制](/azure/kusto/concepts/querylimits)中所述。 可以使用 " **Azure 数据资源管理器（Kusto）** 连接" 窗口中的 "**高级选项**" 来替代这些默认值：

![高级选项](media/power-bi-best-practices/advanced-options.png)

这些选项通过查询发出[set 语句](/azure/kusto/query/setstatement)来更改默认查询限制：

  * **限制查询结果记录号**生成 `set truncationmaxrecords`
  * **限制查询结果数据大小（以字节为单位）** 生成 `set truncationmaxsize`
  * **禁用结果集截断**生成 `set notruncation`

### <a name="using-query-parameters"></a>使用查询参数

您可以使用[查询参数](/azure/kusto/query/queryparametersstatement)动态修改查询。 

#### <a name="using-a-query-parameter-in-the-connection-details"></a>使用连接详细信息中的查询参数

使用查询参数来筛选查询中的信息并优化查询性能。
 
在 "**编辑查询**" 窗口中， **Home** > **高级编辑器**

1. 查找下面的查询部分：

    ```powerquery-m
    Source = Kusto.Contents("<Cluster>", "<Database>", "<Query>", [])
    ```
   
   例如：

    ```powerquery-m
    Source = Kusto.Contents("Help", "Samples", "StormEvents | where State == 'ALABAMA' | take 100", [])
    ```

1. 用参数替换查询的相关部分。 将查询拆分为多个部分，并使用 & 符号以及参数将其连接回。

   例如，在上面的查询中， `State == 'ALABAMA'`我们将使用部分，并将其拆分为： `State == '`和`'` ，并且我们会在`State`它们之间放置参数：
   
    ```kusto
    "StormEvents | where State == '" & State & "' | take 100"
    ```

1. 如果查询包含引号，请对其进行正确编码。 例如，以下查询： 

   ```kusto
   "StormEvents | where State == "ALABAMA" | take 100" 
   ```

   将在**高级编辑器**中显示，如下所示：

   ```kusto
    "StormEvents | where State == ""ALABAMA"" | take 100"
   ```

   它应替换为带有三个引号的以下查询：

   ```kusto
   "StormEvents | where State == """ & State & """ | take 100"
   ```

#### <a name="use-a-query-parameter-in-the-query-steps"></a>在查询步骤中使用查询参数

可以在支持查询参数的任何查询步骤中使用该参数。 例如，根据参数的值筛选结果。

![使用参数筛选结果](media/power-bi-best-practices/filter-using-parameter.png)

### <a name="dont-use-power-bi-data-refresh-scheduler-to-issue-control-commands-to-kusto"></a>不要使用 Power BI 数据刷新计划程序向 Kusto 发出控制命令。

Power BI 包括可以定期对数据源发出查询的数据刷新计划程序。 此机制不应用于计划 Kusto 的控制命令，因为 Power BI 假设所有查询都是只读的。

### <a name="power-bi-can-send-only-short-lt2000-characters-queries-to-kusto"></a>Power BI 只能向 Kusto 发送 short （&lt;2000 个字符）的查询

如果在 Power BI 中运行查询将导致以下错误： _"DataSource。错误："无法从 ..." 中获取内容 "_ 查询的长度可能超过2000个字符。 Power BI 使用**PowerQuery**通过发出 HTTP GET 请求来查询 Kusto，该请求将查询编码为要检索的 URI 的一部分。 因此，Power BI 发出的 Kusto 查询限制为请求 URI 的最大长度（2000个字符，减去小偏移量）。 一种解决方法是，可以在 Kusto 中定义[存储的函数](/azure/kusto/query/schema-entities/stored-functions)，Power BI 并在查询中使用该函数。

## <a name="next-steps"></a>后续步骤

[使用 Power BI 的 Azure 数据资源管理器连接器直观显示数据](power-bi-connector.md)




