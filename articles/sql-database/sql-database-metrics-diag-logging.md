---
title: "Azure SQL 数据库指标和诊断日志记录 | Microsoft Docs"
description: "了解如何配置 Azure SQL 数据库资源以存储资源使用情况、连接性和查询执行统计信息。"
services: sql-database
documentationcenter: 
author: vvasic
manager: jhubbard
editor: 
ms.assetid: 89c2a155-c2fb-4b67-bc19-9b4e03c6d3bc
ms.service: sql-database
ms.custom: monitor & tune
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/16/2017
ms.author: vvasic
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: ef73f9036a91d5bac50597d1d96fe134225eef51
ms.contentlocale: zh-cn
ms.lasthandoff: 09/25/2017

---
# <a name="azure-sql-database-metrics-and-diagnostics-logging"></a>Azure SQL 数据库指标和诊断日志记录 
Azure SQL 数据库可发出指标和诊断日志，以便更轻松地监视。 可配置 Azure SQL 数据库，将资源使用情况、辅助角色和会话以及连接性存储到以下 Azure 资源之一：
- Azure 存储：用于低价存档大量遥测
- Azure 事件中心：用于将 Azure SQL 数据库遥测与自定义监视解决方案或热门管道集成
- Azure Log Analytics：用于具有报告、警报和缓解功能的立即可用的监视解决方案 

    ![体系结构](./media/sql-database-metrics-diag-logging/architecture.png)

## <a name="enable-logging"></a>启用日志记录

默认情况下，不启用指标和诊断日志记录。 可使用以下方法之一启用并管理指标和诊断日志记录：
- Azure 门户
- PowerShell
- Azure CLI
- REST API 
- Resource Manager 模板

启用指标和诊断日志记录时，需要指定收集所选数据的 Azure 资源。 可用选项：
- Log Analytics
- 事件中心
- Azure 存储 

可预配新的 Azure 资源或选择现有资源。 选择存储资源后，需要指定要收集的数据。 可用选项包括：

- [1 分钟指标](sql-database-metrics-diag-logging.md#1-minute-metrics) - 包含 DTU 百分比、DTU 限制、CPU 百分比、物理数据读取百分比、日志写入百分比、成功/失败/防火墙阻止的连接数、会话百分比、辅助角色百分比、存储、存储百分比、XTP 存储百分比
- **[QueryStoreRuntimeStatistics](sql-database-metrics-diag-logging.md#query-store-runtime-statistics)** - 包含有关查询运行时统计信息（如 CPU 使用率、查询持续时间等）的信息。
- **[QueryStoreWaitStatistics](sql-database-metrics-diag-logging.md#query-store-wait-statistics)** - 包含有关查询等待统计信息的信息，可告知查询在什么项上等待，如 CPU、日志、锁定...
- **[错误](sql-database-metrics-diag-logging.md#errors-dataset)** - 包含有关此数据库发生的 SQL 错误的信息。
- **[DatabaseWaitStatistics](sql-database-metrics-diag-logging.md#database-waits-dataset)** - 包含有关数据库针对不同等待类型花费多少时间等待的信息。
- **[超时](sql-database-metrics-diag-logging.md#timeouts-dataset)** - 包含有关数据库针对不同等待类型花费多少时间等待的信息。
- **[阻塞](sql-database-metrics-diag-logging.md#blockings-dataset)** - 包含有关在数据库上发生的阻塞事件的信息。
- **[SQLInsights](sql-database-metrics-diag-logging.md#intelligent-insights-dataset)** - 包含智能见解。 [详细了解智能见解](sql-database-intelligent-insights.md)

如果指定事件中心或 Azure 存储帐户，则可通过指定保留策略来指定删除早于选定时间段的数据。 如果指定 Log analytics，保留策略将取决于所选的定价层。 阅读有关 [Log Analytics 定价](https://azure.microsoft.com/pricing/details/log-analytics/)的详细信息。 

建议阅读文章 [Microsoft Azure 指标概况](../monitoring-and-diagnostics/monitoring-overview-metrics.md)和 [Azure 诊断日志概述](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)，了解如何启用日志记录，以及各种 Azure 服务支持的指标和日志类别。

### <a name="azure-portal"></a>Azure 门户

若要在 Azure 门户中启用指标和诊断日志集合，请导航至 Azure SQL 数据库或弹性池页，然后单击“诊断设置”。

   ![在 Azure 门户中启用](./media/sql-database-metrics-diag-logging/enable-portal.png)

通过选择目标和遥测来新建或编辑现有诊断设置。

   ![配置诊断设置](./media/sql-database-metrics-diag-logging/diagnostics-portal.png)

### <a name="powershell"></a>PowerShell

若要使用 PowerShell 启用指标和诊断日志记录，请使用以下命令：

- 若要允许在存储帐户中存储诊断日志，请使用以下命令：

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
   ```

   存储帐户 ID 是需要向其发送日志的存储帐户的资源 ID。

- 要允许将诊断日志流式传输到事件中心，请使用以下命令：

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
   ```

   服务总线规则 ID 是以下格式的字符串：

   ```powershell
   {service bus resource ID}/authorizationrules/{key name}
   ``` 

- 要启用将诊断日志发送到 Log Analytics 工作区，请使用以下命令：

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
   ```

- 可以使用以下命令获取 Log Analytics 工作区的资源 ID：

   ```powershell
   (Get-AzureRmOperationalInsightsWorkspace).ResourceId
   ```

可以组合这些参数以启用多个输出选项。

### <a name="azure-cli"></a>Azure CLI

若要使用 Azure CLI 启用指标和诊断日志记录，请使用以下命令：

- 若要允许在存储帐户中存储诊断日志，请使用以下命令：

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true
   ```

   存储帐户 ID 是需要向其发送日志的存储帐户的资源 ID。

- 要允许将诊断日志流式传输到事件中心，请使用以下命令：

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
   ```

   服务总线规则 ID 是以下格式的字符串：

   ```azurecli-interactive
   {service bus resource ID}/authorizationrules/{key name}
   ```

- 要启用将诊断日志发送到 Log Analytics 工作区，请使用以下命令：

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --workspaceId <resource id of the log analytics workspace> --enabled true
   ```

可以组合这些参数以启用多个输出选项。

### <a name="rest-api"></a>REST API

阅读有关如何[使用 Azure Monitor REST API 更改诊断设置](https://msdn.microsoft.com/library/azure/dn931931.aspx)的信息。 

### <a name="resource-manager-template"></a>Resource Manager 模板

阅读有关如何[在创建资源时使用 Resource Manager 模板启用诊断设置](../monitoring-and-diagnostics/monitoring-enable-diagnostic-logs-using-template.md)的信息。 

## <a name="stream-into-log-analytics"></a>流式传输到 Log Analytics 中 
通过使用门户中内置的“发送到 Log Analytics”选项，也可通过 Azure PowerShell cmdlet、Azure CLI 或 Azure Monitor REST API 在诊断设置中启用 Log Analytics，将 Azure SQL 数据库指标和诊断日志流式传输到 Log Analytics 中。

### <a name="installation-overview"></a>安装概述

使用 Log Analytics 监视 Azure SQL 数据库群很简单。 需要三个步骤：

1. 创建 Log Analytics 资源
2. 将数据库配置为将指标和诊断日志记录到创建的 Log Analytics 中
3. 在 Log Analytics 中安装库中的 Azure SQL Analytics 解决方案

### <a name="create-log-analytics-resource"></a>创建 Log Analytics 资源

1. 在左侧菜单中，单击“ 新建”。
2. 单击“监视 + 管理”
3. 单击“Log Analytics”
4. 在“Log Analytics”窗体中填写所需的附加信息：工作区名称、订阅、资源组、位置和定价层。

   ![Log Analytics](./media/sql-database-metrics-diag-logging/log-analytics.png)

### <a name="configure-databases-to-record-metrics-and-diagnostic-logs"></a>将数据库配置为记录指标和诊断日志

通过 Azure 门户配置数据库记录其指标的位置是最简单的方式。 在 Azure 门户中，导航到 Azure SQL 数据库资源，然后单击“诊断设置”。 

### <a name="install-the-azure-sql-analytics-solution-from-gallery"></a>从库中安装 Azure SQL Analytics 解决方案  

1. 一旦创建了 Log Analytics 并且数据流入其中，则请安装 Azure SQL Analytics 解决方案。 此操作可通过 OMS 主页侧边菜单中的“解决方案库”完成。 在库中，找到并单击“Azure SQL Analytics”解决方案，然后单击“添加”。

   ![监视解决方案](./media/sql-database-metrics-diag-logging/monitoring-solution.png)

2. OMS 主页上出现了一个名为“Azure SQL Analytics”的新磁贴。 选择此磁贴可打开“Azure SQL Analytics”仪表板。

### <a name="using-azure-sql-analytics-solution"></a>使用 Azure SQL Analytics 解决方案

Azure SQL Analytics 是一个分层仪表板，可在 Azure SQL 数据库资源的层次结构中导航。 [单击此处了解如何使用 Azure SQL Analytics 解决方案。](../log-analytics/log-analytics-azure-sql.md)

## <a name="stream-into-azure-event-hub"></a>流式传输到 Azure 事件中心内

通过使用门户中内置的“流式传输到事件中心”选项，也可通过 Azure PowerShell Cmdlet、Azure CLI 或 Azure Monitor REST API 在诊断设置中启用服务总线规则 ID，将 Azure SQL 数据库指标和诊断日志流式传输到事件中心内。 

### <a name="what-to-do-with-metrics-and-diagnostic-logs-in-event-hub"></a>如何处理事件中心内的指标和诊断日志？
一旦将选定的数据流式传输到事件中心内，就可更进一步启动高级监视方案。 事件中心充当事件管道“前门”，将数据收集到事件中心后，可以使用任何实时分析提供程序或批处理/存储适配器来转换和存储这些数据。 事件中心可将事件流的生成与这些事件的使用分离开来，因此，事件使用者可以根据自己的计划访问事件。 有关事件中心的详细信息，请参阅：

- [什么是 Azure 事件中心](../event-hubs/event-hubs-what-is-event-hubs.md)？
- [事件中心入门](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)


可以通过下述几种方式使用流式传输功能：

-             通过将“热路径”数据流式传输到 PowerBI 来查看服务运行状况 - 可通过事件中心、流分析和 PowerBI 在 Azure 服务中轻松地将指标和诊断数据转换成实时分析结果。 有关如何设置事件中心、如何使用流分析处理数据，以及如何使用 PowerBI 作为输出的概述，请参阅[流分析和 Power BI](../stream-analytics/stream-analytics-power-bi-dashboard.md)。
-             将日志流式传输到第三方日志记录和遥测流 - 使用事件中心流式传输，可将指标和诊断日志放入不同的第三方监视和 Log Analytics 解决方案。 
-             生成自定义遥测和日志记录平台 - 如果已经有一个自定义生成的遥测平台，或者正想生成一个，则可利用事件中心高度可缩放的发布-订阅功能，灵活地引入诊断日志。 请参阅 [Dan Rosanova 的指南，了解如何在全局规模的遥测平台中使用事件中心](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/)。

## <a name="stream-into-azure-storage"></a>流式传输到 Azure 存储中

通过使用 Azure 门户中内置的“存档到存储帐户”选项，也可以通过 Azure PowerShell Cmdlet、Azure CLI 或 Azure Monitor REST API 在诊断设置中启用 Azure 存储，将 Azure SQL 数据库指标和诊断日志存储到 Azure 存储中。

### <a name="schema-of-metrics-and-diagnostic-logs-in-the-storage-account"></a>存储帐户中指标和诊断日志的架构

设置指标和诊断日志集合后，当第一行数据可用时，在你选择的存储帐户中将创建一个存储容器。 这些 blob 的结构为：

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ databases/{database_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```
    
或者使用更简单的形式：

```powershell
insights-{metrics|logs}-{category name}/resourceId=/{resource Id}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

例如，1 分钟指标的 blob 名称可能是：

```powershell
insights-metrics-minute/resourceId=/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.SQL/ servers/Server1/databases/database1/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

如果要记录弹性池中的数据，blob 名称则有所不同：

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ elasticPools/{elastic_pool_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

### <a name="download-metrics-and-logs-from-azure-storage"></a>从 Azure 存储下载指标和日志

请参阅[从 Azure 存储下载指标和诊断日志](../storage/blobs/storage-dotnet-how-to-use-blobs.md#download-blobs)

## <a name="metrics-and-logs-available"></a>可用的指标和日志

### <a name="1-minute-metrics"></a>1 分钟指标

|**资源**|**指标**|
|---|---|
|数据库|DTU 百分比、已用 DTU、 DTU 限制、CPU 百分比、物理数据读取百分比、日志写入百分比、成功/失败/防火墙阻止的连接数、会话百分比、辅助角色百分比、存储、存储百分比、XTP 存储百分比、死锁 |
|弹性池|eDTU 百分比、已用 eDTU、eDTU 限制、CPU 百分比、物理数据读取百分比、日志写入百分比、会话百分比、辅助角色百分比、存储、存储百分比、存储限制、XTP存储百分比 |
|||

### <a name="query-store-runtime-statistics"></a>查询数据存储运行时统计信息

|属性|说明|
|---|---|
|TenantId|租户 ID。|
|SourceSystem|始终是：Azure|
|TimeGenerated [UTC]|记录日志时的时间戳。|
|类型|始终是：AzureDiagnostics|
|ResourceProvider|资源提供程序的名称。 始终是：MICROSOFT.SQL|
|类别|类别的名称。 始终是：QueryStoreRuntimeStatistics|
|OperationName|操作的名称。 始终是：QueryStoreRuntimeStatisticsEvent|
|资源|资源名称|
|ResourceType|资源类型的名称。 始终是：SERVERS/DATABASES|
|SubscriptionId|数据库所属的订阅 GUID。|
|resourceGroup|数据库所属的资源组名称。|
|LogicalServerName_s|数据库所属的服务器的名称。|
|ElasticPoolName_s|数据库所属的弹性池的名称（如果存在）。|
|DatabaseName_s|数据库的名称|
|ResourceId|资源 URI|
|query_hash_s|查询哈希|
|query_plan_hash_s|查询计划哈希|
|statement_sql_handle_s|语句 SQL 句柄|
|interval_start_time_d|自 1900-1-1 以时钟周期数开始 datetimeoffset 的时间间隔。|
|interval_end_time_d|自 1900-1-1 以时钟周期数结束 datetimeoffset 的时间间隔。|
|logical_io_writes_d|逻辑 IO 写入总次数。|
|max_logical_io_writes_d|每次执行逻辑 IO 写入的最大次数。|
|physical_io_reads_d|物理 IO 读取总次数。|
|max_physical_io_reads_d|每次执行逻辑 IO 读取的最大次数。|
|logical_io_reads_d|逻辑 IO 读取总次数。|
|max_logical_io_reads_d|每次执行逻辑 IO 读取的最大次数。|
|execution_type_d|执行类型|
|count_executions_d|执行查询的次数。|
|cpu_time_d|查询使用的总 CPU 时间（以微秒为单位）。|
|max_cpu_time_d|单个执行消耗的最大 CPU 时间（以微秒为单位）。|
|dop_d|并行度总和。|
|max_dop_d|用于单个执行的最大并行度。|
|rowcount_d|返回的总行数。|
|max_rowcount_d|单个执行中返回的最大行数。|
|query_max_used_memory_d|已使用的内存总量（以 KB 为单位）。|
|max_query_max_used_memory_d|单个执行使用的最大内存量（以 KB 为单位）。|
|duration_d|总执行时间（以微秒为单位）。|
|max_duration_d|单个执行的最大执行时间。|
|num_physical_io_reads_d|总物理读取次数。|
|max_num_physical_io_reads_d|每次执行最大物理读取次数。|
|log_bytes_used_d|使用的日志字节总量。|
|max_log_bytes_used_d|每次执行使用的日志字节最大数量。|
|query_id_d|查询存储中的查询 ID|
|plan_id_d|查询存储中的计划 ID|

[了解有关查询存储运行时统计信息数据的详细信息。](https://docs.microsoft.com/en-us/sql/relational-databases/system-catalog-views/sys-query-store-runtime-stats-transact-sql)

### <a name="query-store-wait-statistics"></a>查询存储等待统计信息

|属性|说明|
|---|---|
|TenantId|租户 ID。|
|SourceSystem|始终是：Azure|
|TimeGenerated [UTC]|记录日志时的时间戳。|
|类型|始终是：AzureDiagnostics|
|ResourceProvider|资源提供程序的名称。 始终是：MICROSOFT.SQL|
|类别|类别的名称。 始终是：QueryStoreRuntimeStatistics|
|OperationName|操作的名称。 始终是：QueryStoreRuntimeStatisticsEvent|
|资源|资源名称|
|ResourceType|资源类型的名称。 始终是：SERVERS/DATABASES|
|SubscriptionId|数据库所属的订阅 GUID。|
|resourceGroup|数据库所属的资源组名称。|
|LogicalServerName_s|数据库所属的服务器的名称。|
|ElasticPoolName_s|数据库所属的弹性池的名称（如果存在）。|
|DatabaseName_s|数据库的名称|
|ResourceId|资源 URI|
|wait_category_s|等待的类别。|
|is_parameterizable_s|查询是否可以参数化。|
|statement_type_s|语句的类型。|
|statement_key_hash_s|语句密钥哈希。|
|exec_type_d|执行类型|
|total_query_wait_time_ms_d|关于具体等待类别的查询的总等待时间。|
|max_query_wait_time_ms_d|单个执行中针对具体等待类别的查询的最大等待时间|
|query_param_type_d|0|
|query_hash_s|查询存储中的查询哈希。|
|query_plan_hash_s|查询存储中的查询计划哈希。|
|statement_sql_handle_s|查询存储中的语句句柄|
|interval_start_time_d|自 1900-1-1 以时钟周期数开始 datetimeoffset 的时间间隔。|
|interval_end_time_d|自 1900-1-1 以时钟周期数结束 datetimeoffset 的时间间隔。|
|count_executions_d|执行查询的次数|
|query_id_d|查询存储中的查询 ID|
|plan_id_d|查询存储中的计划 ID|

[单击此处了解有关查询存储等待统计数据的详细信息。](https://docs.microsoft.com/en-us/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql)

### <a name="errors-dataset"></a>错误数据集

|属性|说明|
|---|---|
|TenantId|租户 ID。|
|SourceSystem|始终是：Azure|
|TimeGenerated [UTC]|记录日志时的时间戳。|
|类型|始终是：AzureDiagnostics|
|ResourceProvider|资源提供程序的名称。 始终是：MICROSOFT.SQL|
|类别|类别的名称。 始终是：QueryStoreRuntimeStatistics|
|OperationName|操作的名称。 始终是：QueryStoreRuntimeStatisticsEvent|
|资源|资源名称|
|ResourceType|资源类型的名称。 始终是：SERVERS/DATABASES|
|SubscriptionId|数据库所属的订阅 GUID。|
|resourceGroup|数据库所属的资源组名称。|
|LogicalServerName_s|数据库所属的服务器的名称。|
|ElasticPoolName_s|数据库所属的弹性池的名称（如果存在）。|
|DatabaseName_s|数据库的名称|
|ResourceId|资源 URI|
|消息|纯文本格式的错误消息|
|user_defined_b|是否是用户定义位错误|
|error_number_d|错误代码|
|严重性|错误的严重性|
|state_d|错误的状态|
|query_hash_s|失败查询的查询哈希（如果存在）|
|query_plan_hash_s|失败查询的查询计划哈希（如果存在）|

[SQL Server 错误消息](https://msdn.microsoft.com/en-us/library/cc645603.aspx)

### <a name="database-waits-dataset"></a>数据库等待数据集

|属性|说明|
|---|---|
|TenantId|租户 ID。|
|SourceSystem|始终是：Azure|
|TimeGenerated [UTC]|记录日志时的时间戳。|
|类型|始终是：AzureDiagnostics|
|ResourceProvider|资源提供程序的名称。 始终是：MICROSOFT.SQL|
|类别|类别的名称。 始终是：QueryStoreRuntimeStatistics|
|OperationName|操作的名称。 始终是：QueryStoreRuntimeStatisticsEvent|
|资源|资源名称|
|ResourceType|资源类型的名称。 始终是：SERVERS/DATABASES|
|SubscriptionId|数据库所属的订阅 GUID。|
|resourceGroup|数据库所属的资源组名称。|
|LogicalServerName_s|数据库所属的服务器的名称。|
|ElasticPoolName_s|数据库所属的弹性池的名称（如果存在）。|
|DatabaseName_s|数据库的名称|
|ResourceId|资源 URI|
|wait_type_s|等待类型的名称|
|start_utc_date_t [UTC]|测量期间开始时间。|
|end_utc_date_t [UTC]|测量期间结束时间。|
|delta_max_wait_time_ms_d|每次执行最大等待时间|
|delta_signal_wait_time_ms_d|总信号等待时间|
|delta_wait_time_ms_d|期间内的总等待时间|
|delta_waiting_tasks_count_d|等待任务数|

[单击此处了解有关数据库等待统计数据的详细信息。](https://docs.microsoft.com/en-us/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql)

### <a name="timeouts-dataset"></a>超时数据集

|属性|说明|
|---|---|
|TenantId|租户 ID。|
|SourceSystem|始终是：Azure|
|TimeGenerated [UTC]|记录日志时的时间戳。|
|类型|始终是：AzureDiagnostics|
|ResourceProvider|资源提供程序的名称。 始终是：MICROSOFT.SQL|
|类别|类别的名称。 始终是：QueryStoreRuntimeStatistics|
|OperationName|操作的名称。 始终是：QueryStoreRuntimeStatisticsEvent|
|资源|资源名称|
|ResourceType|资源类型的名称。 始终是：SERVERS/DATABASES|
|SubscriptionId|数据库所属的订阅 GUID。|
|resourceGroup|数据库所属的资源组名称。|
|LogicalServerName_s|数据库所属的服务器的名称。|
|ElasticPoolName_s|数据库所属的弹性池的名称（如果存在）。|
|DatabaseName_s|数据库的名称|
|ResourceId|资源 URI|
|error_state_d|错误状态代码|
|query_hash_s|查询哈希（如果存在）|
|query_plan_hash_s|查询计划哈希（如果存在）|

### <a name="blockings-dataset"></a>阻塞数据集

|属性|说明|
|---|---|
|TenantId|租户 ID。|
|SourceSystem|始终是：Azure|
|TimeGenerated [UTC]|记录日志时的时间戳。|
|类型|始终是：AzureDiagnostics|
|ResourceProvider|资源提供程序的名称。 始终是：MICROSOFT.SQL|
|类别|类别的名称。 始终是：QueryStoreRuntimeStatistics|
|OperationName|操作的名称。 始终是：QueryStoreRuntimeStatisticsEvent|
|资源|资源名称|
|ResourceType|资源类型的名称。 始终是：SERVERS/DATABASES|
|SubscriptionId|数据库所属的订阅 GUID。|
|resourceGroup|数据库所属的资源组名称。|
|LogicalServerName_s|数据库所属的服务器的名称。|
|ElasticPoolName_s|数据库所属的弹性池的名称（如果存在）。|
|DatabaseName_s|数据库的名称|
|ResourceId|资源 URI|
|lock_mode_s|查询所使用的锁模式|
|resource_owner_type_s|锁所有者。|
|blocked_process_filtered_s|阻止的进程报告 XML。|
|duration_d|锁定持续时间（以毫秒为单位）。|

### <a name="intelligent-insights-dataset"></a>智能见解数据集
[单击此处了解有关智能见解日志格式的详细信息](sql-database-intelligent-insights-use-diagnostics-log.md)

## <a name="next-steps"></a>后续步骤

- 阅读文章 [Microsoft Azure 中的指标概述](../monitoring-and-diagnostics/monitoring-overview-metrics.md)和 [Azure 诊断日志概述](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)了解如何启用日志记录，以及各种 Azure 服务支持的指标和日志类别。
- 阅读以下文章，了解事件中心：
   - [什么是 Azure 事件中心](../event-hubs/event-hubs-what-is-event-hubs.md)？
   - [事件中心入门](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
- 请参阅[从 Azure 存储下载指标和诊断日志](../storage/blobs/storage-dotnet-how-to-use-blobs.md#download-blobs)

