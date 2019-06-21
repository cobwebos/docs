---
title: Azure 监视器 Log Analytics 中日志查询作用域 |Microsoft Docs
description: 介绍 Azure 监视器 Log Analytics 中的日志查询的范围和时间范围。
services: log-analytics
author: bwren
manager: carmonm
ms.service: log-analytics
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: bwren
ms.openlocfilehash: a948b80f6524339f0908a2fb19c4a83d70b3b140
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/20/2019
ms.locfileid: "67297354"
---
# <a name="log-query-scope-and-time-range-in-azure-monitor-log-analytics"></a>Azure 监视器 Log Analytics 中的日志查询作用域和时间范围
在运行时[日志查询](log-query-overview.md)中[在 Azure 门户中的 Log Analytics](get-started-portal.md)，计算由查询的数据集取决于作用域和你选择的时间范围。 本文介绍了范围和时间范围以及如何设置每个具体取决于您的要求。 它还介绍了不同类型的作用域的行为。


## <a name="query-scope"></a>查询作用域
查询作用域定义计算由查询的记录。 通常，这将在单个 Log Analytics 工作区或 Application Insights 应用程序中包括的所有记录。 Log Analytics 还可以设置特定的受监视 Azure 资源的作用域。 这允许资源所有者仅专注于其数据，即使该资源将写入到多个工作区。

作用域始终显示在顶部的 Log Analytics 窗口中。 一个图标指示作用域是 Log Analytics 工作区或 Application Insights 应用程序。 没有图标表示另一个 Azure 资源。

![范围](media/scope/scope.png)

你用于启动 Log Analytics，以及在某些情况下可以更改作用域，通过单击方法决定作用域。 下表列出了不同类型的使用作用域并为每个不同的详细信息。

| 查询作用域 | 作用域中的记录 | 如何选择 | 更改作用域 |
|:---|:---|:---|:---|
| Log Analytics 工作区 | Log Analytics 工作区中的所有记录。 | 选择**日志**从**Azure Monitor**菜单或**Log Analytics 工作区**菜单。  | 可以更改为任何其他资源类型的作用域。 |
| Application Insights 应用程序 | 在 Application Insights 应用程序中的所有记录。 | 选择**Analytics**从**概述**Application Insights 的页。 | 只能将作用域更改为另一个 Application Insights 应用程序中。 |
| 资源组 | 创建的资源组中的所有资源记录。 可能包括多个 Log Analytics 工作区中的数据。 | 选择**日志**从资源组菜单。 | 不能更改作用域。|
| 订阅 | 创建的订阅中的所有资源记录。 可能包括多个 Log Analytics 工作区中的数据。 | 选择**日志**从订阅菜单。   | 不能更改作用域。 |
| 其他 Azure 资源 | 创建的资源记录。 可能包括多个 Log Analytics 工作区中的数据。  | 选择**日志**资源菜单中。<br>或<br>选择**日志**从**Azure Monitor**菜单，然后选择新作用域。 | 只能更改作用域为相同的资源类型。 |

### <a name="limitations-when-scoped-to-a-resource"></a>应用范围限定为某个资源时的限制

查询作用域是 Log Analytics 工作区或 Application Insights 应用程序，在门户中的所有选项和所有查询命令时可用。 因为它们与单个工作区或应用程序相关联的以下时但仅限于资源，选项在门户中不可用：

- 保存
- 查询资源管理器
- 新建警报规则

在查询时作用域为资源，因为查询作用域将已包含该资源或资源组的数据的任何工作区中，不能使用以下命令：

- [app](app-expression.md)
- [工作区](workspace-expression.md)
 


## <a name="time-range"></a>时间范围
时间范围内指定的组，其计算结果为时已创建了记录，基于查询的记录。 这被定义工作区或为下表中指定的应用程序中的每个记录的标准属性。

| Location | 属性 |
|:---|:---|
| Log Analytics 工作区          | TimeGenerated |
| Application Insights 应用程序 | timestamp     |

设置 Log Analytics 窗口顶部的时间选取器中选择时间范围。  可以选择一个预定义时间段，也可以选择**自定义**来指定特定的时间范围。

![时间选取器](media/scope/time-picker.png)

如果使用标准时间属性，如上面的表中所示的查询中设置筛选器，时间选取器将更改为**在查询中设置**，且时间选取器被禁用。 在这种情况下，它是查询的最有效，将顶部筛选器，以便任何后续处理仅需要使用经过筛选的记录。

![筛选的查询](media/scope/query-filtered.png)

如果您使用[工作区](workspace-expression.md)或[应用](app-expression.md)命令从另一个工作区或应用程序中检索数据时，时间选取器的行为可能有所不同。 如果作用域是 Log Analytics 工作区，并且使用**应用程序**，或如果作用域是 Application Insights 应用程序并使用**工作区**，然后 Log Analytics 可能不了解该属性使用中筛选器应确定时间筛选器。

在以下示例中，作用域设置为 Log Analytics 工作区。  该查询使用**工作区**从另一个 Log Analytics 工作区中检索数据。 时间选取器将变为**在查询中设置**因为它会看到使用预期的筛选器**TimeGenerated**属性。

![查询与工作区](media/scope/query-workspace.png)

如果查询使用**应用程序**不过从 Application Insights 应用程序检索数据，Log Analytics 不能识别**时间戳**筛选器和时间选取器的属性保持不变。 在这种情况下，应用这两个筛选器。 在示例中，仅在过去 24 小时中创建的记录都包含在查询即使它指定在 7 天**其中**子句。

![使用应用程序进行查询](media/scope/query-app.png)

## <a name="next-steps"></a>后续步骤

- 逐步[教程在 Azure 门户中使用 Log Analytics](get-started-portal.md)。
- 逐步[查询编写教程](get-started-queries.md)。