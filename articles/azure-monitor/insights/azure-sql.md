---
title: Log Analytics 中的 Azure SQL Analytics 解决方案 | Microsoft 文档
description: Azure SQL Analytics 解决方案可帮助你管理 Azure SQL 数据库
services: log-analytics
ms.service: log-analytics
ms.subservice: performance
ms.custom: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: carlrab
manager: craigg
ms.date: 12/17/2018
ms.openlocfilehash: f12aed12138b1b16fcce90401ea0d65bd083675f
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/17/2018
ms.locfileid: "53546704"
---
# <a name="monitor-azure-sql-database-using-azure-sql-analytics-preview"></a>使用 Azure SQL Analytics（预览版）监视 Azure SQL 数据库

![Azure SQL Analytics 符号](./media/azure-sql/azure-sql-symbol.png)

Azure SQL Analytics 是一种高级云监视解决方案，用于通过单一虚拟管理平台跨多个订阅大规模监视 Azure SQL 数据库、弹性池和托管实例的性能。 它通过内置智能来收集和直观显示重要的 Azure SQL 数据库性能指标，以便进行性能故障排除。

使用解决方案收集指标后，即可利用这些指标创建自定义监视规则和警报。 该解决方案可以帮助你确定应用程序堆栈的每个层的问题。 它使用 Azure 诊断指标和 Log Analytics 视图，在单个 Log Analytics 工作区中呈现有关所有 Azure SQL 数据库和弹性池和托管实例中的数据库的数据。 Log Analytics 可帮助用户收集、关联和可视化结构化和非结构化数据。

有关使用 Azure SQL Analytics 解决方案的实践概述和典型使用方案，请观看嵌入视频：

>[!VIDEO https://www.youtube.com/embed/j-NDkN4GIzg]
>

## <a name="connected-sources"></a>连接的源

Azure SQL Analytics 是一种仅限云的监视解决方案，支持流式传输 Azure SQL 数据库（单一的、池化的和托管的实例数据库）的诊断遥测数据。 由于该解决方案不使用代理连接到 Log Analytics 服务，因此它不支持监视本地或 VM 中托管的 SQL Server，具体请参阅下面的兼容性表。

| 连接的源 | 支持 | Description |
| --- | --- | --- |
| [Azure 诊断](../../azure-monitor/platform/collect-azure-metrics-logs.md) | **是** | Azure 指标和日志数据由 Azure 直接发送到 Log Analytics。 |
| [Azure 存储帐户](../../azure-monitor/platform/collect-azure-metrics-logs.md) | 否 | Log Analytics 不从存储帐户中读取数据。 |
| [Windows 代理](../../azure-monitor/platform/agent-windows.md) | 否 | 该解决方案不使用直接 Windows 代理。 |
| [Linux 代理](../../azure-monitor/learn/quick-collect-linux-computer.md) | 否 | 该解决方案不使用直接Linux 代理。 |
| [System Center Operations Manager 管理组](../platform/om-agents.md) | 否 | 此解决方案不使用从 Operations Manager 代理到 Log Analytics 的直接连接。 |

## <a name="configuration"></a>配置

执行以下步骤，将 Azure SQL Analytics 解决方案添加到 Azure 仪表板。

1. 从 [Azure 市场](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.AzureSQLAnalyticsOMS?tab=Overview)将 Azure SQL Analytics 解决方案添加到你的工作区。
2. 在 Azure 门户中，单击“+ 创建资源”，然后搜索 **Azure SQL Analytics**。  
    ![监视 + 管理](./media/azure-sql/monitoring-management.png)
3. 从列表中选择“Azure SQL Analytics (预览版)”
4. 在“Azure SQL Analytics (预览版)”区域中，单击“创建”。  
    ![创建](./media/azure-sql/portal-create.png)
5. 在“创建新的解决方案”区域中，创建新工作区或选择要向其添加解决方案的现有工作区，并单击“创建”。

    ![添加到工作区](./media/azure-sql/add-to-workspace.png)

### <a name="configure-azure-sql-databases-elastic-pools-and-managed-instances-to-stream-diagnostics-telemetry"></a>配置 Azure SQL 数据库、弹性池和托管实例以流式传输诊断遥测数据

在工作区中创建 Azure SQL Analytics 解决方案后，需要**配置每个**你希望进行监视以将其诊断遥测数据流式传输到解决方案的资源。 请遵循此页面上的详细说明：

- 为 Azure SQL 数据库启用 Azure 诊断，以便[将诊断遥测数据流式传输到 Azure SQL Analytics](../../sql-database/sql-database-metrics-diag-logging.md)。

上述页面还提供了有关启用通过单个 Azure SQL Analytics 工作区，在单个视图中监视多个 Azure 订阅的支持。

## <a name="using-the-solution"></a>使用解决方案

将解决方案添加到工作区时，“Azure SQL Analytics”磁贴也会添加到工作区并显示在“概览”中。 该磁贴显示解决方案从中接收诊断遥测数据的 Azure SQL 数据库、弹性池、托管实例和托管实例中数据库的数目。

![“Azure SQL Analytics”磁贴](./media/azure-sql/azure-sql-sol-tile.png)

该解决方案提供两个独立的视图 - 一个视图用于监视 Azure SQL 数据库和弹性池，另一个视图用于监视托管实例以及托管实例中的数据库。

若要查看 Azure SQL 数据库和弹性池的 Azure SQL Analytics 监视仪表板，请单击磁贴的上半部分。 若要查看托管实例和托管实例中数据库的 Azure SQL Analytics 监视仪表板，请单击磁贴的下半部分。

### <a name="viewing-azure-sql-analytics-data"></a>查看 Azure SQL Analytics 数据

该仪表板包含通过不同透视监视的所有数据库的概览。 要使不同透视起效，必须允许将有关 SQL 资源的适当指标或日志流式传输到 Azure Log Analytics 工作区。

请注意，如果某些指标或日志未流式传输到 Azure Log Analytics，则解决方案中的磁贴不会填充监视信息。

### <a name="azure-sql-database-and-elastic-pool-view"></a>Azure SQL 数据库和弹性池视图

选择数据库的 Azure SQL Analytics 磁贴后，会显示监视仪表板。

![Azure SQL Analytics 概述](./media/azure-sql/azure-sql-sol-overview.png)

选择任意磁贴，打开特定透视的向下钻取报告。 选择透视后，可看到向下钻取报表。

![Azure SQL Analytics 超时](./media/azure-sql/azure-sql-sol-metrics.png)

此视图中的每个透视图提供订阅、服务器、弹性池和数据库级别的摘要。 此外，每个透视在右侧都显示了特定于透视的报表。 从列表选择订阅、服务器、池或数据库会继续向下钻取。

### <a name="managed-instance-and-databases-in-managed-instance-view"></a>“托管实例”视图中的托管实例和数据库

选择数据库的 Azure SQL Analytics 磁贴后，会显示监视仪表板。

![Azure SQL Analytics 概述](./media/azure-sql/azure-sql-sol-overview-mi.png)

选择任意磁贴，打开特定透视的向下钻取报告。 选择透视后，可看到向下钻取报表。

选择“托管实例”视图会显示有关托管实例利用率及其包含的数据库的详细信息，以及有关在整个实例中执行的查询的遥测数据。

![Azure SQL Analytics 超时](./media/azure-sql/azure-sql-sol-metrics-mi.png)

### <a name="perspectives"></a>透视图

下表概述了两个仪表板版本支持的透视图，一个版本适用于 Azure SQL 数据库和弹性池，另一个版本适用于托管实例。

| 透视 | Description | SQL 数据库和弹性池支持 | 托管实例支持 |
| --- | ------- | ----- | ----- |
| 资源（按类型） | 对监视的所有资源进行计数的透视。 | 是 | 是 |
| 洞察力 | 提供对性能智能见解的分层向下钻取。 | 是 | 是 |
| 错误 | 提供对数据库上发生的 SQL 错误的分层向下钻取。 | 是 | 是 |
| 超时 | 提供对数据库上发生的 SQL 超时的分层向下钻取。 | 是 | 否 |
| 阻止 | 提供对数据库上发生的 SQL 阻止的分层向下钻取。 | 是 | 否 |
| 数据库等待 | 提供对数据库级别上的 SQL 等待统计信息的分层向下钻取。 包含总等待时间汇总和每个类型的等待时间。 |是 | 是 |
| 查询持续时间 | 提供对查询执行统计信息的分层向下钻取，例如查询持续时间、CPU 使用情况、数据 IO 使用情况和日志 IO 使用情况。 | 是 | 是 |
| 查询等待 | 按等待类型提供对查询等待统计信息的分层向下钻取。 | 是 | 是 |

### <a name="intelligent-insights-report"></a>智能见解报告

使用 Azure SQL 数据库 [Intelligent Insights](../../sql-database/sql-database-intelligent-insights.md)，可以了解所有 Azure SQL 数据库的性能状况。 可通过见解透视，对收集的所有智能见解进行可视化和访问。

![Azure SQL Analytics 见解](./media/azure-sql/azure-sql-sol-insights.png)

### <a name="elastic-pool-and-database-reports"></a>弹性池和数据库报告

弹性池和 SQL 数据库提供特定于自身的报告，其中显示了指定时间内对该资源收集的所有数据。

![Azure SQL Analytics 数据库](./media/azure-sql/azure-sql-sol-database.png)

![Azure SQL 弹性池](./media/azure-sql/azure-sql-sol-pool.png)

### <a name="query-reports"></a>查询报表

通过查询持续时间和查询等待透视，可使用查询报表关联任何查询的性能。 此报表比较不同数据库之间的查询性能，从而可轻松查找良好执行所选查询的数据库以及查询缓慢的数据库。

![Azure SQL Analytics 查询](./media/azure-sql/azure-sql-sol-queries.png)

## <a name="permissions"></a>权限

若要使用 Azure SQL Analytics，至少需要为用户授予 Azure 中的“读者”角色的权限。 但是，此角色不允许用户查看查询文本，或执行任何自动优化操作。 在 Azure 中，可以最大程度地使用该解决方案的更自由角色包括“所有者”、“参与者”、“SQL DB 参与者”或“SQL Server 参与者”。 可能还需要考虑在门户中创建一个自定义角色，该角色只拥有 Azure SQL Analytics 的特定使用权限，而无权管理其他资源。

### <a name="creating-a-custom-role-in-portal"></a>在门户中创建自定义角色

认识到某些组织会在 Azure 中强制实施严格的权限控制，请查看以下 PowerShell 脚本，以便在 Azure 门户中创建“SQL Analytics 监视操作员”自定义角色，该角色拥有最大程度地使用 Azure SQL Analytics 所需的最低读取和写入权限。

将以下脚本中的“{SubscriptionId}”替换为自己的 Azure 订阅 ID，并在以 Azure 中“所有者”或“参与者”角色的身份登录后执行该脚本。

   ```powershell
    Connect-AzureRmAccount
    Select-AzureRmSubscription {SubscriptionId}
    $role = Get-AzureRmRoleDefinition -Name Reader
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
    $role.Actions.Add("Microsoft.Sql/servers/databases/*");
    $role.Actions.Add("Microsoft.Sql/servers/advisors/read");
    $role.Actions.Add("Microsoft.Sql/servers/advisors/write");
    $role.Actions.Add("Microsoft.Sql/servers/advisors/recommendedActions/read");
    $role.Actions.Add("Microsoft.Sql/servers/advisors/recommendedActions/write");
    $role.Actions.Add("Microsoft.Resources/deployments/write");
    $role.AssignableScopes = "/subscriptions/{SubscriptionId}"
    New-AzureRmRoleDefinition $role
   ```

创建新角色后，请将此角色分配给你需要向其授予自定义权限，使其能够使用 Azure SQL Analytics 的每个用户。

## <a name="analyze-data-and-create-alerts"></a>分析数据和创建警报

Azure SQL Analytics 中的数据分析基于自定义查询和报告的 [Log Analytics 语言](../log-query/get-started-queries.md)。 在[可用的指标和日志](../../sql-database/sql-database-metrics-diag-logging.md#metrics-and-logs-available)中查看从数据库资源收集的、用于自定义查询的数据的说明。

若要在解决方案中实现自动化警报，必须编写一个可在满足条件时触发警报的 Log Analytics 查询。 查看有关 Log Analytics 查询的以下几个示例，可以基于这些示例在解决方案中设置警报。

### <a name="creating-alerts-for-azure-sql-database"></a>针对 Azure SQL 数据库创建警报

可以使用来自 Azure SQL 数据库资源的数据轻松[创建警报](../../azure-monitor/platform/alerts-metric.md)。 以下是部分有用的可用于日志警报的[日志查询](../../azure-monitor/log-query/log-query-overview.md)：

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
> - 设置此警报的前提要求是受监视的数据库将诊断指标（“所有指标”选项）流式传输到解决方案。
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
> - 设置此警报的前提要求是受监视的数据库将诊断指标（“所有指标”选项）流式传输到解决方案。
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
> - 设置此警报的前提要求是受监视的数据库将诊断指标（“所有指标”选项）流式传输到解决方案。
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
> - 设置此警报的前提要求是受监视的数据库将 SQLInsights 诊断日志流式传输到解决方案。
> - 此查询要求将警报规则设置为以与 alert_run_interval 相同的频率运行以避免重复的结果。 此规则应当设置为当存在来自查询的结果时（> 0 个结果）触发。
> - 请自定义 alert_run_interval 来指定时间范围以检查在配置为将 SQLInsights 日志流式传输到解决方案的数据库上是否发生了此条件。
> - 请自定义 insights_string 来捕获 Insights 根本原因分析文本的输出。 这是可以从现有见解使用的解决方案的 UI 中显示的同一文本。 另外，还可以使用下面的查询来查看在你的订阅上生成的所有见解的文本。 可以使用查询的输出来获取用于在 Insights 上设置警报的不同字符串。

```
AzureDiagnostics
| where Category == "SQLInsights" and status_s == "Active"
| distinct rootCauseAnalysis_s
```

### <a name="creating-alerts-for-managed-instance"></a>针对托管实例创建警报

#### <a name="managed-instance-storage-is-above-90"></a>托管实例存储超过 90%

```
let storage_percentage_threshold = 90;
AzureDiagnostics
| where Category =="ResourceUsageStats"
| summarize (TimeGenerated, calculated_storage_percentage) = arg_max(TimeGenerated, todouble(storage_space_used_mb_s) *100 / todouble (reserved_storage_mb_s))
   by ResourceId
| where calculated_storage_percentage > storage_percentage_threshold
```

> [!NOTE]
> - 设置此警报的先决条件是，已在解决方案中为受监视的托管实例启用了 ResourceUsageStats 日志的流式传输。
> - 此查询要求将警报规则设置为当存在来自查询的结果时（> 0 个结果，表示托管实例满足该条件）触发。 输出是托管实例上的存储消耗量百分比。

#### <a name="managed-instance-cpu-average-consumption-is-above-95-in-the-last-1-hr"></a>托管实例的 CPU 平均消耗在过去 1 小时内超过 95%

```
let cpu_percentage_threshold = 95;
let time_threshold = ago(1h);
AzureDiagnostics
| where Category == "ResourceUsageStats" and TimeGenerated > time_threshold
| summarize avg_cpu = max(todouble(avg_cpu_percent_s)) by ResourceId
| where avg_cpu > cpu_percentage_threshold
```

> [!NOTE]
> - 设置此警报的先决条件是，已在解决方案中为受监视的托管实例启用了 ResourceUsageStats 日志的流式传输。
> - 此查询要求将警报规则设置为当存在来自查询的结果时（> 0 个结果，表示托管实例满足该条件）触发。 输出是在定义的期间内在托管实例上消耗的 CPU 的平均利用率百分比。

### <a name="pricing"></a>定价

虽然该解决方案可以免费使用，但超出每月分配的免费数据引入单位的诊断遥测数据消耗量仍适用，请参阅 [Log Analytics 定价](https://azure.microsoft.com/en-us/pricing/details/monitor)。 提供的免费数据引入单位每月可免费监控多个数据库。 请注意，与空闲数据相比，工作负载较重的活动数据库越多，将引入的数据越多。 选择 Azure SQL Analytics 导航菜单上的 OMS 工作区，然后选择使用情况和预估成本，即可轻松监视解决方案中的数据引入消耗量。

## <a name="next-steps"></a>后续步骤

- 使用 Log Analytics 中的[日志搜索](../../azure-monitor/log-query/log-query-overview.md)查看 Azure SQL 的详细数据。
- [创建自己的仪表板](../../azure-monitor/platform/dashboards.md)，显示 Azure SQL 数据。
- 发生特定 Azure SQL 事件时[创建警报](../../azure-monitor/platform/alerts-overview.md)。
