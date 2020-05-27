---
title: Azure Monitor Log Analytics 中的日志查询范围 | Microsoft Docs
description: 介绍了 Azure Monitor Log Analytics 中的日志查询范围和时间范围。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/01/2020
ms.openlocfilehash: 2840e5b8ff16d44f76aaafcf68264c65e4401ff7
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83199024"
---
# <a name="log-query-scope-and-time-range-in-azure-monitor-log-analytics"></a>Azure Monitor Log Analytics 中的日志查询范围和时间范围
当你在 [Azure 门户上的 Log Analytics](get-started-portal.md) 中运行[日志查询](log-query-overview.md)时，查询所评估的数据集取决于你选择的范围和时间范围。 本文介绍了范围和时间范围，以及如何根据需求设置这两项。 另外还介绍了不同类型范围的行为。


## <a name="query-scope"></a>查询范围
查询范围定义了由查询评估的记录。 这通常包括单个 Log Analytics 工作区或 Application Insights 应用中的所有记录。 Log Analytics 还允许为受监控的特定 Azure 资源设置范围。 这样，资源所有者就可以只关注自己的数据，即使相应资源写入到多个工作区，也不例外。

范围始终显示在 Log Analytics 窗口的左上角。 显示的图标指明范围是 Log Analytics 工作区，还是 Application Insights 应用。 如果没有图标，则指明范围是其他 Azure 资源。

![范围](media/scope/scope.png)

范围是由你用于启动 Log Analytics 的方法决定的，在某些情况下，可以通过单击它来更改范围。 下表列出了所用的不同类型范围及其各种详细信息。

> [!IMPORTANT]
> 如果使用的是 APM 2.1，那么 Application Insights 应用与其他所有日志数据一起存储在 Log Analytics 工作区中，且 Application Insights 范围不可用。 如果你选择 Application Insights 菜单中的“日志”，那么它的作用相当于“其他 Azure 资源”范围，并且只有 Application Insights 表中相应的应用数据可用。

| 查询范围 | 范围内的记录 | 如何选择 | 更改范围 |
|:---|:---|:---|:---|
| Log Analytics 工作区 | Log Analytics 工作区中的所有记录。 | 选择“Azure Monitor”或“Log Analytics 工作区”菜单中的“日志”。  | 可以将范围更改为其他任何资源类型。 |
| Application Insights 应用 | Application Insights 应用中的所有记录。 | 选择 Application Insights 的“概述”页中的“分析”。 | 只能将范围更改为另一个 Application Insights 应用。 |
| 资源组 | 由资源组中的所有资源创建的记录。 可能包含来自多个 Log Analytics 工作区的数据。 | 选择资源组菜单中的“日志”。 | 无法更改范围。|
| 订阅 | 由订阅中的所有资源创建的记录。 可能包含来自多个 Log Analytics 工作区的数据。 | 选择订阅菜单中的“日志”。   | 无法更改范围。 |
| 其他 Azure 资源 | 由资源创建的记录。 可能包含来自多个 Log Analytics 工作区的数据。  | 选择资源菜单中的“日志”。<br>OR<br>选择“Azure Monitor”菜单中的“日志”，然后选择新范围。 | 只能将范围更改为同一资源类型。 |

### <a name="limitations-when-scoped-to-a-resource"></a>将范围限定为资源时的限制

如果查询范围是 Log Analytics 工作区或 Application Insights 应用，门户中的所有选项和所有查询命令都可用。 不过，当范围限定为资源时，门户中的以下选项不可用，因为它们与单个工作区或应用关联：

- 保存
- 查询资源管理器
- 新建警报规则

当范围限定为资源时，无法在查询中使用以下命令，因为查询范围已涵盖包含相应资源或资源集的数据的任何工作区：

- [app](app-expression.md)
- [工作区](workspace-expression.md)
 

## <a name="query-limits"></a>查询限制
你可能有业务需求，需要 Azure 资源将数据写入到多个 Log Analytics 工作区。 工作区不需要与资源位于同一区域，一个工作区可能会从各种区域中的资源收集数据。  

将范围设置为资源或资源集是 Log Analytics 的一项特别强大的功能，因为这样可以在一个查询中自动合并分布式数据。 不过，如果需要跨多个 Azure 区域从工作区中检索数据，可能会显著影响性能。

Log Analytics 通过在使用的区域达到特定数量时发出警告或错误，帮助防止跨多个区域中的工作区的查询产生过多的开销。 如果范围包括 5 个或更多区域中的工作区，查询就会收到警告。 它仍然会运行，但可能需要过长的时间才能完成。

![查询警告](media/scope/query-warning.png)

如果范围包括 20 个或更多区域中的工作区，查询就会被阻止运行。 在这种情况下，系统会提示你减少工作区的区域数，然后尝试重新运行查询。 下拉列表中会显示查询范围内的所有区域，应先减少区域数，再尝试重新运行查询。

![查询失败](media/scope/query-failed.png)


## <a name="time-range"></a>时间范围
时间范围指定了查询根据记录创建时间所评估的记录集。 这是由工作区或应用中每个记录的标准属性定义的，如下表所示。

| 位置 | properties |
|:---|:---|
| Log Analytics 工作区          | TimeGenerated |
| Application Insights 应用 | timestamp     |

若要设置时间范围，请在 Log Analytics 窗口顶部的时间选取器中进行选择。  可以选择预定义时间段，也可以选择“自定义”来指定特定的时间范围。

![时间选取器](media/scope/time-picker.png)

如果你在使用上表所示标准时间属性的查询中设置筛选器，时间选取器会更改为“在查询中设置”并处于禁用状态。 在这种情况下，最有效的方法是将筛选器置于查询的最前面，这样任何后续处理就只需要处理筛选出的记录。

![筛选后的查询](media/scope/query-filtered.png)

如果你使用 [workspace](workspace-expression.md) 或 [app](app-expression.md) 命令从另一个工作区或应用中检索数据，时间选取器的行为可能会有所不同。 如果范围是 Log Analytics 工作区，而你使用的是 app，或者如果范围是 Application Insights 应用，而你使用的是 workspace，那么 Log Analytics 可能就无法理解应由筛选器中使用的属性来确定时间筛选器。

在下面的示例中，范围设置为 Log Analytics 工作区。  查询使用 workspace 从另一个 Log Analytics 工作区检索数据。 时间选取器变成“在查询中设置”，因为它识别到使用预期的 TimeGenerated 属性的筛选器。

![使用 workspace 进行查询](media/scope/query-workspace.png)

不过，如果查询使用 app 从 Application Insights 应用中检索数据，Log Analytics 就无法识别筛选器中的 timestamp 属性，导致时间选取器保持不变。 在这种情况下，会应用两个筛选器。 在此示例中，查询中只包含在过去 24 小时内创建的记录，尽管在 where 子句中指定的是 7 天。

![使用 app 进行查询](media/scope/query-app.png)

## <a name="next-steps"></a>后续步骤

- 演练关于[使用 Azure 门户上的 Log Analytics 的教程](get-started-portal.md)。
- 演练关于[编写查询的教程](get-started-queries.md)。