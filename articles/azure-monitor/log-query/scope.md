---
title: Azure Monitor Log Analytics 中的日志查询范围 |Microsoft Docs
description: 描述 Azure Monitor Log Analytics 中的日志查询的范围和时间范围。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/25/2019
ms.openlocfilehash: 897eff62fcbab5996b6b9493bd825ae412aa4c3e
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79249589"
---
# <a name="log-query-scope-and-time-range-in-azure-monitor-log-analytics"></a>Azure Monitor 中的日志查询范围和时间范围 Log Analytics
当你在[Azure 门户中 Log Analytics](get-started-portal.md)运行[日志查询](log-query-overview.md)时，查询计算的数据集取决于所选范围和时间范围。 本文介绍范围和时间范围，以及如何根据需要设置每个范围和时间范围。 还介绍了不同类型的作用域的行为。


## <a name="query-scope"></a>查询范围
查询范围定义查询计算的记录。 这通常包括单个 Log Analytics 工作区中的所有记录或 Application Insights 应用程序。 Log Analytics 还允许你为特定监视的 Azure 资源设置作用域。 这允许资源所有者仅关注其数据，即使该资源写入多个工作区。

范围始终显示在 "Log Analytics" 窗口的左上角。 图标指示作用域是 Log Analytics 工作区还是 Application Insights 应用程序。 "无" 图标表示其他 Azure 资源。

![范围](media/scope/scope.png)

范围由你开始 Log Analytics 所使用的方法决定，在某些情况下，你可以通过单击来更改范围。 下表列出了使用的不同类型的作用域，以及每个作用域的不同详细信息。

| 查询范围 | 范围内的记录 | 如何选择 | 更改范围 |
|:---|:---|:---|:---|
| Log Analytics 工作区 | Log Analytics 工作区中的所有记录。 | 从 " **Azure Monitor** " 菜单或 " **Log Analytics 工作区**" 菜单中选择 "**日志**"。  | 可将作用域更改为任何其他资源类型。 |
| Application Insights 应用程序 | Application Insights 应用程序中的所有记录。 | 从 Application Insights 的 "**概述**" 页中选择 "**分析**"。 | 只能将作用域更改为另一个 Application Insights 应用程序。 |
| 资源组 | 资源组中的所有资源创建的记录。 可能包含来自多个 Log Analytics 工作区的数据。 | 从 "资源组" 菜单中选择 "**日志**"。 | 无法更改范围。|
| 订阅 | 订阅中的所有资源创建的记录。 可能包含来自多个 Log Analytics 工作区的数据。 | 从 "订阅" 菜单中选择 "**日志**"。   | 无法更改范围。 |
| 其他 Azure 资源 | 由资源创建的记录。 可能包含来自多个 Log Analytics 工作区的数据。  | 从 "资源" 菜单中选择 "**日志**"。<br>或<br>从 " **Azure Monitor** " 菜单中选择 "**日志**"，然后选择新的作用域。 | 只能将作用域更改为同一资源类型。 |

### <a name="limitations-when-scoped-to-a-resource"></a>作用域限定为资源的限制

当查询范围是 Log Analytics 工作区或 Application Insights 应用程序时，门户中的所有选项和所有查询命令都可用。 不过，当作用域为资源时，门户中的以下选项不可用，因为它们与单个工作区或应用程序关联：

- 保存
- 查询资源管理器
- 新建警报规则

当作用域为资源时，不能在查询中使用以下命令，因为查询范围已经包含了包含该资源或一组资源的数据的任何工作区：

- [app](app-expression.md)
- [工作区](workspace-expression.md)
 

## <a name="query-limits"></a>查询限制
对于将数据写入多个 Log Analytics 工作区的 Azure 资源，你可能有业务要求。 工作区不需要与资源位于同一区域，一个工作区可能会从多个区域中的资源收集数据。  

将作用域设置为资源或资源集是 Log Analytics 的一项特别强大的功能，因为它允许您在单个查询中自动合并分布式数据。 但如果需要跨多个 Azure 区域从工作区中检索数据，则可能会显著影响性能。

当使用特定数量的区域时，Log Analytics 可通过发出警告或错误，帮助防止跨多个区域中的工作区的查询产生过多的开销。 如果作用域包括5个或更多区域中的工作区，则查询将收到警告。 它仍将运行，但可能需要花费很长时间才能完成。

![查询警告](media/scope/query-warning.png)

如果作用域包括20个或更多区域中的工作区，则查询将被阻止运行。 在这种情况下，系统将提示你减少工作区区域的数量，并尝试再次运行查询。 下拉列表将显示查询范围内的所有区域，并且应在尝试再次运行查询之前减少区域数。

![查询失败](media/scope/query-failed.png)


## <a name="time-range"></a>时间范围
时间范围指定根据创建记录时针对查询计算的记录集。 这是根据下表中指定的工作区或应用程序中每条记录的标准属性定义的。

| 位置 | properties |
|:---|:---|
| Log Analytics 工作区          | TimeGenerated |
| Application Insights 应用程序 | timestamp     |

通过从 "Log Analytics" 窗口顶部的时间选取器中选择时间范围来设置时间范围。  您可以选择预定义的时间段，或选择 "**自定义**" 来指定特定的时间范围。

![时间选取器](media/scope/time-picker.png)

如果在查询中设置了一个筛选器，该筛选器使用上表中所示的标准时间属性，则时间选取器将更改为 "**在查询中设置**"，并禁用时间选择器。 在这种情况下，最有效的方法是将筛选器放在查询顶部，以便任何后续处理只需处理筛选的记录。

![筛选查询](media/scope/query-filtered.png)

如果使用 "[工作区](workspace-expression.md)" 或 "[应用](app-expression.md)" 命令从另一个工作区或应用程序中检索数据，时间选择器的行为可能会有所不同。 如果范围是 Log Analytics 的工作区，并且你使用的是**应用**程序，或者如果范围是 Application Insights 应用程序，并且你使用的是**工作区**，则 Log Analytics 可能不会理解筛选器中使用的属性应确定时间筛选器。

在下面的示例中，范围设置为 Log Analytics 工作区。  查询使用**工作区**从另一个 Log Analytics 工作区检索数据。 时间选取器将更改为 **"在查询中设置"** ，因为它会看到一个使用所需**TimeGenerated**属性的筛选器。

![查询工作区](media/scope/query-workspace.png)

如果查询使用**应用**从 Application Insights 应用程序检索数据，则 Log Analytics 不会识别筛选器中的**时间戳**属性，时间选择器保持不变。 在这种情况下，将应用两个筛选器。 在此示例中，查询中仅包含在过去24小时内创建的记录，即使在**where**子句中指定了7天。

![应用程序查询](media/scope/query-app.png)

## <a name="next-steps"></a>后续步骤

- 演练[有关在 Azure 门户中使用 Log Analytics 的教程](get-started-portal.md)。
- 演练[有关编写查询的教程](get-started-queries.md)。
