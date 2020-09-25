---
title: Azure Monitor 日志查询中的审核查询
description: 日志查询审核日志的详细信息，该日志提供有关 Azure Monitor 中运行的日志查询的遥测。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/03/2020
ms.openlocfilehash: df937ba7f23f2789d929a043c7239ababb24374f
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91285054"
---
# <a name="audit-queries-in-azure-monitor-logs-preview"></a> (预览版 Azure Monitor 日志中审核查询) 
日志查询审核日志提供有关 Azure Monitor 中运行的日志查询的遥测。 这包括如下所述的信息：运行查询、运行查询的人员、使用的工具、查询文本，以及描述查询执行的性能统计信息。


## <a name="configure-query-auditing"></a>配置查询审核
使用 "Log Analytics" 工作区上的 [诊断设置](../platform/diagnostic-settings.md) 启用查询审核。 这允许你将审核数据发送到订阅中的当前工作区或其他任何工作区，并发送到 azure 事件中心，以在 Azure 外部发送或用于存档的 Azure 存储。 

### <a name="azure-portal"></a>Azure 门户
在以下任一位置，访问 Azure 门户中的 Log Analytics 工作区的诊断设置：

- 从 " **Azure Monitor** " 菜单中，选择 " **诊断设置**"，然后找到并选择工作区。

    [![诊断设置 Azure Monitor ](media/log-query-audit/diagnostic-setting-monitor.png)](media/log-query-audit/diagnostic-setting-monitor.png#lightbox) 

- 从 " **Log Analytics 工作区** " 菜单中，选择工作区，然后选择 " **诊断设置**"。

    [![Log Analytics 工作区 ](media/log-query-audit/diagnostic-setting-workspace.png) 的诊断设置 ](media/log-query-audit/diagnostic-setting-workspace.png#lightbox) 

### <a name="resource-manager-template"></a>资源管理器模板
可以从 [Log Analytics 工作区的诊断设置](../samples/resource-manager-diagnostic-settings.md#diagnostic-setting-for-log-analytics-workspace)中获取资源管理器模板的示例。

## <a name="audit-data"></a>审核数据
每次运行查询时都会创建一个审核记录。 如果将数据发送到 Log Analytics 工作区，则该数据将存储在名为 *LAQueryLogs*的表中。 下表描述了审核数据的每个记录中的属性。

| 字段 | 说明 |
|:---|:---|
| TimeGenerated         | 提交查询时的 UTC 时间。 |
| CorrelationId         | 用于标识查询的唯一 ID。 与 Microsoft 联系以获取帮助时，可用于故障排除方案。 |
| AADObjectId           | 启动查询的用户帐户 Azure Active Directory ID。  |
| AADTenantId           | 启动查询的用户帐户的租户的 ID。  |
| AADEmail              | 启动查询的用户帐户的租户的电子邮件。  |
| AADClientId           | 用于启动查询的应用程序 ID 和解析的名称。 |
| RequestClientApp      | 用于启动查询的已解析应用程序的名称。 |
| QueryTimeRangeStart   | 为查询选择的时间范围的开始时间。 在某些情况下（例如从 Log Analytics 启动查询时），并在查询中指定时间范围，而不是在时间选取器中进行填充。 |
| QueryTimeRangeEnd     | 为查询选择的时间范围的结束时间。 在某些情况下（例如从 Log Analytics 启动查询时），并在查询中指定时间范围，而不是在时间选取器中进行填充。  |
| QueryText             | 运行的查询的文本。 |
| RequestTarget         | API URL 用于提交查询。  |
| RequestContext        | 请求运行查询的资源列表。 最多包含三个字符串数组：工作区、应用程序和资源。 订阅或资源组目标查询将显示为 *资源*。 包括 RequestTarget 隐含的目标。<br>如果可以解析每个资源，则将包括该资源的资源 ID。 如果在访问资源时返回错误，可能无法解决此问题。 在这种情况下，将使用查询中的特定文本。<br>如果查询使用不明确的名称（例如多个订阅中现有的工作区名称），则将使用此不明确的名称。 |
| RequestContextFilters | 指定为查询调用一部分的一组筛选器。 最多包含三个可能的字符串数组：<br>-ResourceTypes-要限制查询范围的资源类型<br>-工作区-要将查询限制到的工作区列表<br>-WorkspaceRegions-要限制查询的工作区区域列表 |
| ResponseCode          | 提交查询时返回的 HTTP 响应代码。 |
| ResponseDurationMs    | 返回响应的时间。  |
| ResponseRowCount     | 查询返回的总行数。 |
| StatsCPUTimeMs       | 用于计算、分析和数据提取的总计算时间。 仅在 query 返回时填充了状态代码200。 |
| StatsDataProcessedKB | 用于处理查询的数据量。 受目标表大小、所用时间跨度、已应用筛选器和已引用列数影响。 仅在 query 返回时填充了状态代码200。 |
| StatsDataProcessedStart | 用于处理查询的最早数据的时间。 受查询显式时间范围和应用的筛选器的影响。 由于数据分区，这可能大于显式时间范围。 仅在 query 返回时填充了状态代码200。 |
| StatsDataProcessedEnd  |处理查询时所访问的最新数据的时间。 受查询显式时间范围和应用的筛选器的影响。 由于数据分区，这可能大于显式时间范围。 仅在 query 返回时填充了状态代码200。 |
| StatsWorkspaceCount | 查询访问的工作区数。 仅在 query 返回时填充了状态代码200。 |
| StatsRegionCount | 查询访问的区域数。 仅在 query 返回时填充了状态代码200。 |

## <a name="considerations"></a>注意事项

- 仅当在用户上下文中执行时才记录查询。 不会记录 Azure 中的服务到服务。 此排除的两个主要查询集包括计费计算和自动执行警报。 对于警报，将只记录计划的警报查询本身;警报创建屏幕中的初始执行警报在用户上下文中执行，并可用于审核目的。 
- 性能统计信息不适用于来自 Azure 数据资源管理器代理的查询。 这些查询的所有其他数据仍将填充。
- [进行模糊处理字符串文本](/azure/data-explorer/kusto/query/scalar-data-types/string#obfuscated-string-literals)的字符串上的*h*提示不会影响查询审核日志。 将完全按提交方式捕获查询，而不会对字符串进行模糊处理。 应确保只有具有相容性权限才能查看此数据的用户才能使用 Log Analytics 工作区中提供的各种 RBAC 模式来完成此操作。
- 对于包含多个工作区中数据的查询，只能在用户有权访问的工作区中捕获查询。

## <a name="next-steps"></a>后续步骤

- 详细了解 [诊断设置](../platform/diagnostic-settings.md)。
- 详细了解如何 [优化日志查询](query-optimization.md)。
