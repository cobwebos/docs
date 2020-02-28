---
title: Azure Monitor 中的 Azure SQL Analytics 解决方案 |Microsoft Docs
description: Azure SQL Analytics 解决方案可帮助你管理 Azure SQL 数据库
ms.subservice: logs
ms.topic: conceptual
author: danimir
ms.author: danil
ms.date: 02/21/2020
ms.reviewer: carlrab
ms.openlocfilehash: 921a05c4dc6c1d5cfa663ac71b469573b8f1925b
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77667034"
---
# <a name="monitor-azure-sql-database-using-azure-sql-analytics-preview"></a>使用 Azure SQL Analytics（预览版）监视 Azure SQL 数据库

![Azure SQL Analytics 符号](./media/azure-sql/azure-sql-symbol.png)

Azure SQL Analytics 是一种高级的云监视解决方案，用于在单个视图中大规模、跨多个订阅监视所有 Azure SQL 数据库的性能。 Azure SQL Analytics 收集和直观显示关键性能指标，并提供内置的智能性能故障排除。

通过使用这些收集的指标，你可以创建自定义监视规则和警报。 Azure SQL Analytics 有助于确定应用程序堆栈的每一层上的问题。 它使用 Azure 诊断指标以及 Azure Monitor 视图在单个 Log Analytics 工作区中显示有关所有 Azure SQL 数据库的数据。 Azure Monitor 帮助收集、关联和可视化结构化和非结构化数据。

有关使用 Azure SQL Analytics 解决方案的实践概述和典型使用方案，请观看嵌入视频：

>[!VIDEO https://www.youtube.com/embed/j-NDkN4GIzg]
>

## <a name="connected-sources"></a>连接的源

Azure SQL Analytics 是一种仅限云的监视解决方案，支持所有 Azure SQL 数据库的诊断遥测流。 由于 Azure SQL Analytics 不使用代理连接到 Azure Monitor，因此它不支持对本地或虚拟机中托管的 SQL Server 进行监视。

| 连接的源 | 支持 | 说明 |
| --- | --- | --- |
| [诊断设置](../platform/diagnostic-settings.md) | **是** | Azure 指标和日志数据由 Azure 直接发送到 Azure Monitor 日志。 |
| [Azure 存储帐户](../platform/collect-azure-metrics-logs.md) | 否 | Azure Monitor 不会从存储帐户读取数据。 |
| [Windows 代理](../platform/agent-windows.md) | 否 | Azure SQL Analytics 不使用直接 Windows 代理。 |
| [Linux 代理](../learn/quick-collect-linux-computer.md) | 否 | Azure SQL Analytics 不使用直接 Linux 代理。 |
| [System Center Operations Manager 管理组](../platform/om-agents.md) | 否 | Azure SQL Analytics 不使用从 Operations Manager 代理到 Azure Monitor 的直接连接。 |

## <a name="azure-sql-analytics-options"></a>Azure SQL Analytics 选项

下表概述了 Azure SQL Analytics 仪表板的两个版本支持的选项，一种用于单个数据库和池中的数据库和弹性池，另一个用于托管实例和实例数据库。

| Azure SQL Analytics 选项 | 说明 | 单一数据库和池数据库和弹性池支持 | 托管实例和实例数据库支持 |
| --- | ------- | ----- | ----- |
| 资源（按类型） | 对监视的所有资源进行计数的透视。 | 是 | 是 |
| 洞察力 | 提供对性能智能见解的分层向下钻取。 | 是 | 是 |
| 错误 | 提供对数据库上发生的 SQL 错误的分层向下钻取。 | 是 | 是 |
| 超时 | 提供对数据库上发生的 SQL 超时的分层向下钻取。 | 是 | 否 |
| 阻止 | 提供对数据库上发生的 SQL 阻止的分层向下钻取。 | 是 | 否 |
| 数据库等待 | 提供对数据库级别上的 SQL 等待统计信息的分层向下钻取。 包含总等待时间汇总和每个类型的等待时间。 |是 | 否 |
| 查询持续时间 | 提供对查询执行统计信息的分层向下钻取，例如查询持续时间、CPU 使用情况、数据 IO 使用情况和日志 IO 使用情况。 | 是 | 是 |
| 查询等待 | 按等待类型提供对查询等待统计信息的分层向下钻取。 | 是 | 是 |

## <a name="configuration"></a>配置

使用[从解决方案库中添加 Azure Monitor 解决方案](../../azure-monitor/insights/solutions.md)中所述的过程，将 Azure SQL Analytics （预览版）添加到 Log Analytics 工作区。

### <a name="configure-azure-sql-databases-to-stream-diagnostics-telemetry"></a>将 Azure SQL 数据库配置为流式传输诊断遥测

在工作区中创建 Azure SQL Analytics 解决方案后，需要配置要监视的**每个**资源，以将其诊断遥测流式传输到 Azure SQL Analytics 中。 请遵循此页面上的详细说明：

- 为 Azure SQL 数据库启用 Azure 诊断，以便[将诊断遥测数据流式传输到 Azure SQL Analytics](../../sql-database/sql-database-metrics-diag-logging.md)。

上述页面还提供了有关启用通过单个 Azure SQL Analytics 工作区，在单个视图中监视多个 Azure 订阅的支持。

## <a name="using-azure-sql-analytics"></a>使用 Azure SQL Analytics

将 Azure SQL Analytics 添加到工作区时，"Azure SQL Analytics" 磁贴将添加到工作区，并显示在 "概述" 中。 选择 "查看摘要" 链接以加载磁贴内容。

![Azure SQL Analytics 摘要磁贴](./media/azure-sql/azure-sql-sol-tile-01.png)

加载后，该磁贴将显示单个和共用数据库、弹性池、托管实例以及 Azure SQL Analytics 接收诊断遥测的托管实例数据库的数目。

![“Azure SQL Analytics”磁贴](./media/azure-sql/azure-sql-sol-tile-02.png)

Azure SQL Analytics 提供了两个单独的视图，一个用于监视单一数据库和池数据库和弹性池，另一个视图用于监视托管实例和实例数据库。

若要查看单数据库和池中的数据库和弹性池 Azure SQL Analytics 监视仪表板，请单击该磁贴的上半部分。 若要查看托管实例和实例数据库 Azure SQL Analytics 监视仪表板，请单击磁贴下半部分。

### <a name="viewing-azure-sql-analytics-data"></a>查看 Azure SQL Analytics 数据

该仪表板包含通过不同透视监视的所有数据库的概览。 若要运行不同的透视，必须在要流式传输到 Log Analytics 工作区的 SQL 资源上启用适当的指标或日志。

如果某些指标或日志未流式传输到 Azure Monitor，则 Azure SQL Analytics 中的磁贴不会填充监视信息。

### <a name="single-and-pooled-databases-and-elastic-pools-view"></a>单个和共用数据库与弹性池视图

选择数据库的 Azure SQL Analytics 磁贴后，会显示监视仪表板。

![Azure SQL Analytics 概述](./media/azure-sql/azure-sql-sol-overview.png)

选择任意磁贴，打开特定透视的向下钻取报告。 选择透视后，可看到向下钻取报表。

![Azure SQL Analytics 超时](./media/azure-sql/azure-sql-sol-metrics.png)

此视图中的每个透视都提供了订阅、服务器、弹性池和数据库级别的汇总。 此外，每个透视在右侧都显示了特定于透视的报表。 从列表选择订阅、服务器、池或数据库会继续向下钻取。

### <a name="managed-instance-and-instances-databases-view"></a>托管实例和实例数据库视图

选择数据库的 Azure SQL Analytics 磁贴后，会显示监视仪表板。

![Azure SQL Analytics 概述](./media/azure-sql/azure-sql-sol-overview-mi.png)

选择任意磁贴，打开特定透视的向下钻取报告。 选择透视后，可看到向下钻取报表。

选择 "托管实例" 视图，将显示有关托管实例利用率、其包含的数据库和通过实例执行的查询的遥测数据的详细信息。

![Azure SQL Analytics 超时](./media/azure-sql/azure-sql-sol-metrics-mi.png)

### <a name="intelligent-insights-report"></a>智能见解报告

使用 Azure SQL 数据库 [Intelligent Insights](../../sql-database/sql-database-intelligent-insights.md)，可以了解所有 Azure SQL 数据库的性能状况。 可通过见解透视，对收集的所有智能见解进行可视化和访问。

![Azure SQL Analytics 见解](./media/azure-sql/azure-sql-sol-insights.png)

### <a name="elastic-pools-and-database-reports"></a>弹性池和数据库报表

弹性池和数据库都有自己的特定报表，这些报表显示在指定时间内为资源收集的所有数据。

![Azure SQL Analytics 数据库](./media/azure-sql/azure-sql-sol-database.png)

![Azure SQL 弹性池](./media/azure-sql/azure-sql-sol-pool.png)

### <a name="query-reports"></a>查询报表

通过查询持续时间和查询等待透视，可以通过查询报告来关联任何查询的性能。 此报表比较不同数据库之间的查询性能，从而可轻松查找良好执行所选查询的数据库以及查询缓慢的数据库。

![Azure SQL Analytics 查询](./media/azure-sql/azure-sql-sol-queries.png)

## <a name="permissions"></a>权限

若要使用 Azure SQL Analytics，至少需要为用户授予 Azure 中的“读者”角色的权限。 但是，此角色不允许用户查看查询文本，或执行任何自动优化操作。 Azure 中的更多允许使用 Azure SQL Analytics 到最大范围的角色是所有者、参与者、SQL 数据库参与者或 SQL Server 参与者。 可能还需要考虑在门户中创建一个自定义角色，该角色只拥有 Azure SQL Analytics 的特定使用权限，而无权管理其他资源。

### <a name="creating-a-custom-role-in-portal"></a>在门户中创建自定义角色

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

认识到某些组织会在 Azure 中强制实施严格的权限控制，请查看以下 PowerShell 脚本，以便在 Azure 门户中创建“SQL Analytics 监视操作员”自定义角色，该角色拥有最大程度地使用 Azure SQL Analytics 所需的最低读取和写入权限。

将以下脚本中的“{SubscriptionId}”替换为自己的 Azure 订阅 ID，并在以 Azure 中“所有者”或“参与者”角色的身份登录后执行该脚本。

   ```powershell
    Connect-AzAccount
    Select-AzSubscription {SubscriptionId}
    $role = Get-AzRoleDefinition -Name Reader
    $role.Name = "SQL Analytics Monitoring Operator"
    $role.Description = "Lets you monitor database performance with Azure SQL Analytics as a reader. Does not allow change of resources."
    $role.IsCustom = $true
    $role.Actions.Add("Microsoft.SQL/servers/databases/read");
    $role.Actions.Add("Microsoft.SQL/servers/databases/topQueries/queryText/*");
    $role.Actions.Add("Microsoft.Sql/servers/databases/advisors/read");
    $role.Actions.Add("Microsoft.Sql/servers/databases/advisors/write");
    $role.Actions.Add("Microsoft.Sql/servers/databases/advisors/recommendedActions/read");
    $role.Actions.Add("Microsoft.Sql/servers/databases/advisors/recommendedActions/write");
    $role.Actions.Add("Microsoft.Sql/servers/databases/automaticTuning/read");
    $role.Actions.Add("Microsoft.Sql/servers/databases/automaticTuning/write");
    $role.Actions.Add("Microsoft.Sql/servers/advisors/read");
    $role.Actions.Add("Microsoft.Sql/servers/advisors/write");
    $role.Actions.Add("Microsoft.Sql/servers/advisors/recommendedActions/read");
    $role.Actions.Add("Microsoft.Sql/servers/advisors/recommendedActions/write");
    $role.Actions.Add("Microsoft.Resources/deployments/write");
    $role.AssignableScopes = "/subscriptions/{SubscriptionId}"
    New-AzRoleDefinition $role
   ```

创建新角色后，请将此角色分配给你需要向其授予自定义权限，使其能够使用 Azure SQL Analytics 的每个用户。

## <a name="analyze-data-and-create-alerts"></a>分析数据和创建警报

Azure SQL Analytics 中的数据分析基于自定义查询和报告的 [Log Analytics 语言](../log-query/get-started-queries.md)。 在[可用的指标和日志](../../sql-database/sql-database-metrics-diag-logging.md#metrics-and-logs-available)中查看从数据库资源收集的、用于自定义查询的数据的说明。

Azure SQL Analytics 中的自动警报基于编写一个 Log Analytics 查询，该查询在满足条件时触发警报。 在下面的几个示例中查找 Log Analytics 查询，可以在 Azure SQL Analytics 中设置警报。

### <a name="creating-alerts-for-azure-sql-database"></a>针对 Azure SQL 数据库创建警报

可以使用来自 Azure SQL 数据库资源的数据轻松[创建警报](../platform/alerts-metric.md)。 以下是部分有用的可用于日志警报的[日志查询](../log-query/log-query-overview.md)：

#### <a name="high-cpu-on-azure-sql-database"></a>Azure SQL 数据库的 CPU 利用率较高

```
AzureMetrics
| where ResourceProvider=="MICROSOFT.SQL"
| where ResourceId contains "/DATABASES/"
| where MetricName=="cpu_percent"
| summarize AggregatedValue = max(Maximum) by bin(TimeGenerated, 5m)
| render timechart
```

> [!NOTE]
>
> - 设置此警报的先决条件是监视的数据库会将基本指标流式传输到 Azure SQL Analytics。
> - 若要改为获取高 DTU 结果，请将 MetricName 值 cpu_percent 替换为 dtu_consumption_percent。

#### <a name="high-cpu-on-azure-sql-database-elastic-pools"></a>Azure SQL 数据库弹性池的 CPU 利用率较高

```
AzureMetrics
| where ResourceProvider=="MICROSOFT.SQL"
| where ResourceId contains "/ELASTICPOOLS/"
| where MetricName=="cpu_percent"
| summarize AggregatedValue = max(Maximum) by bin(TimeGenerated, 5m)
| render timechart
```

> [!NOTE]
>
> - 设置此警报的先决条件是监视的数据库会将基本指标流式传输到 Azure SQL Analytics。
> - 若要改为获取高 DTU 结果，请将 MetricName 值 cpu_percent 替换为 dtu_consumption_percent。

#### <a name="azure-sql-database-storage-in-average-above-95-in-the-last-1-hr"></a>Azure SQL 数据库存储在过去 1小时的平均值超过 95%

```
let time_range = 1h;
let storage_threshold = 95;
AzureMetrics
| where ResourceId contains "/DATABASES/"
| where MetricName == "storage_percent"
| summarize max_storage = max(Average) by ResourceId, bin(TimeGenerated, time_range)
| where max_storage > storage_threshold
| distinct ResourceId
```

> [!NOTE]
>
> - 设置此警报的先决条件是监视的数据库会将基本指标流式传输到 Azure SQL Analytics。
> - 此查询要求将警报规则设置为当存在来自查询的结果时（> 0 个结果，表示某些数据库上存在此条件）触发。 输出是在所定义的 time_range 内高于 storage_threshold 的数据库资源的列表。
> - 输出是在所定义的 time_range 内高于 storage_threshold 的数据库资源的列表。

#### <a name="alert-on-intelligent-insights"></a>Intelligent insights 上的警报

```
let alert_run_interval = 1h;
let insights_string = "hitting its CPU limits";
AzureDiagnostics
| where Category == "SQLInsights" and status_s == "Active"
| where TimeGenerated > ago(alert_run_interval)
| where rootCauseAnalysis_s contains insights_string
| distinct ResourceId
```

> [!NOTE]
>
> - 设置此警报的先决条件是监视的数据库流 SQLInsights 诊断日志到 Azure SQL Analytics。
> - 此查询要求将警报规则设置为以与 alert_run_interval 相同的频率运行以避免重复的结果。 此规则应当设置为当存在来自查询的结果时（> 0 个结果）触发。
> - 自定义 alert_run_interval 以指定时间范围，以检查配置为将 SQLInsights 日志流式传输到 Azure SQL Analytics 的数据库中是否出现条件。
> - 请自定义 insights_string 来捕获 Insights 根本原因分析文本的输出。 这与 Azure SQL Analytics 的 UI 中显示的文本相同，可以从现有的见解中使用。 另外，还可以使用下面的查询来查看在你的订阅上生成的所有见解的文本。 可以使用查询的输出来获取用于在 Insights 上设置警报的不同字符串。

```
AzureDiagnostics
| where Category == "SQLInsights" and status_s == "Active"
| distinct rootCauseAnalysis_s
```

### <a name="creating-alerts-for-managed-instances"></a>为托管实例创建警报

#### <a name="managed-instance-storage-is-above-90"></a>托管实例存储超过90%

```
let storage_percentage_threshold = 90;
AzureDiagnostics
| where Category =="ResourceUsageStats"
| summarize (TimeGenerated, calculated_storage_percentage) = arg_max(TimeGenerated, todouble(storage_space_used_mb_s) *100 / todouble (reserved_storage_mb_s))
   by ResourceId
| where calculated_storage_percentage > storage_percentage_threshold
```

> [!NOTE]
>
> - 设置此警报的先决条件在于，监视托管实例是否已启用 ResourceUsageStats 日志流式处理，以便 Azure SQL Analytics。
> - 此查询需要设置警报规则，以便在查询中存在现有结果（> 结果）时发出警报，这表示该条件存在于托管实例上。 输出是托管实例上的存储占用百分比。

#### <a name="managed-instance-cpu-average-consumption-is-above-95-in-the-last-1-hr"></a>托管实例 CPU 平均消耗在过去1小时内超过95%

```
let cpu_percentage_threshold = 95;
let time_threshold = ago(1h);
AzureDiagnostics
| where Category == "ResourceUsageStats" and TimeGenerated > time_threshold
| summarize avg_cpu = max(todouble(avg_cpu_percent_s)) by ResourceId
| where avg_cpu > cpu_percentage_threshold
```

> [!NOTE]
>
> - 设置此警报的先决条件是监视的托管实例已启用 ResourceUsageStats 日志流式处理，以 Azure SQL Analytics。
> - 此查询需要设置警报规则，以便在查询中存在现有结果（> 结果）时发出警报，这表示该条件存在于托管实例上。 输出是托管实例上已定义期间内的平均 CPU 使用率百分比消耗。

### <a name="pricing"></a>定价

虽然 Azure SQL Analytics 可供使用，但诊断遥测的使用情况高于每个月分配的数据引入的免费单位数，请参阅[Log Analytics 定价](https://azure.microsoft.com/pricing/details/monitor)。 提供的免费数据引入单位每月可免费监控多个数据库。 具有较大工作负荷的活动数据库会引入更多数据与空闲数据库。 通过在 Azure SQL Analytics 的导航菜单上选择 "OMS 工作区"，然后选择 "使用情况和估计成本"，可以轻松监视 Azure SQL Analytics 中的数据引入使用量。

## <a name="next-steps"></a>后续步骤

- 使用 Azure Monitor 中的[日志查询](../log-query/log-query-overview.md)查看详细的 Azure SQL 数据。
- [创建自己的仪表板](../learn/tutorial-logs-dashboards.md)，显示 Azure SQL 数据。
- 发生特定 Azure SQL 事件时[创建警报](../platform/alerts-overview.md)。
