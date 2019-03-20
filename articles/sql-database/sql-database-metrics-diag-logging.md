---
title: Azure SQL 数据库指标和诊断日志记录 | Microsoft Docs
description: 了解如何配置 Azure SQL 数据库以存储资源使用情况和查询执行统计信息。
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: 76c7519a166bfbfe5d2a7695a077b809aa971489
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/18/2019
ms.locfileid: "57904025"
---
# <a name="azure-sql-database-metrics-and-diagnostics-logging"></a>Azure SQL 数据库指标和诊断日志记录

单一数据库、弹性池中的入池数据库和托管实例中的实例数据库可以流式传输指标和诊断日志，以便更轻松地进行性能监视。 可以配置数据库，以将资源使用情况、辅助角色和会话以及连接性传输到以下 Azure 资源之一：

- **Azure SQL Analytics**：使用报表、警报和缓解建议对 Azure SQL 数据库进行智能监视。
- **Azure 事件中心**：将 SQL 数据库遥测与自定义监视解决方案或热管道相集成。
- **Azure 存储**：低价存档大量遥测数据。

    ![体系结构](./media/sql-database-metrics-diag-logging/architecture.png)

有关各种 Azure 服务支持的指标和日志类别的详细信息，请参阅：

- [Microsoft Azure 中的指标概述](../monitoring-and-diagnostics/monitoring-overview-metrics.md)
- [Azure 诊断日志概述](../azure-monitor/platform/diagnostic-logs-overview.md)

本文提供的指导可帮助你为 Azure SQL 数据库、弹性池和托管实例启用诊断遥测。 它还可以帮助你了解如何将 Azure SQL Analytics 配置为监视工具用于查看数据库诊断遥测数据。

## <a name="enable-logging-of-diagnostics-telemetry"></a>启用诊断遥测的日志记录

可使用以下方法之一来启用和管理指标与诊断遥测日志记录：

- Azure 门户
- PowerShell
- Azure CLI
- Azure Monitor REST API
- Azure 资源管理器模板

当你启用指标和诊断日志记录时，需要指定收集诊断遥测数据的 Azure 资源目标。 可用选项包括：

- Azure SQL 分析
- Azure 事件中心
- Azure 存储

可预配新的 Azure 资源或选择现有资源。 使用“诊断设置”选项选择资源之后，指定要收集的数据。

## <a name="supported-diagnostic-logging-for-azure-sql-databases-and-instance-databases"></a>Azure SQL 数据库和实例数据库支持的诊断日志记录

对 SQL 数据库启用指标与诊断日志记录 - 默认未启用此功能。

您可以设置 Azure SQL 数据库和实例数据库以收集以下诊断遥测数据：

| 数据库的监视遥测 | 单一数据库和入池数据库支持 | 实例数据库支持 |
| :------------------- | ----- | ----- |
| [所有指标](#all-metrics)：包含 DTU/CPU 百分比、DTU/CPU 限制、物理数据读取百分比、日志写入百分比、成功/失败/防火墙阻止的连接数、会话百分比、辅助角色百分比、存储、存储百分比和 XTP 存储百分比。 | 是 | 否 |
| [QueryStoreRuntimeStatistics](#query-store-runtime-statistics)：包含有关查询运行时统计信息的信息，例如 CPU 使用率、查询持续时间统计信息。 | 是 | 是 |
| [QueryStoreWaitStatistics](#query-store-wait-statistics)：包含有关查询等待统计信息的信息（查询正在等待什么），例如 CPU、日志和锁定。 | 是 | 是 |
| [Errors](#errors-dataset):包含有关数据库发生的 SQL 错误的信息。 | 是 | 是 |
| [DatabaseWaitStatistics](#database-wait-statistics-dataset)：包含有关数据库针对不同等待类型花费多少时间等待的信息。 | 是 | 否 |
| [Timeouts](#time-outs-dataset)：包含有关数据库发生的超时的信息。 | 是 | 否 |
| [Blocks](#blockings-dataset)：包含有关数据库发生的阻塞事件的信息。 | 是 | 否 |
| [SQLInsights](#intelligent-insights-dataset)：包含性能的智能见解。 有关详细信息，请参阅[智能见解](sql-database-intelligent-insights.md)。 | 是 | 是 |

> [!IMPORTANT]
> 弹性池和托管的实例都有其自己单独的诊断遥测数据从它们所包含的数据库。 这一点需要注意，即为每种资源，单独配置诊断遥测数据按如下所述。

## <a name="azure-portal"></a>Azure 门户

可以使用**诊断设置**菜单为每个单一汇集在一起，或实例在 Azure 门户中配置的诊断遥测数据的流式处理的数据库。 此外，诊断遥测数据还可以配置单独的数据库的容器： 弹性池和托管的实例。 您可以设置要流式传输诊断遥测的以下目标：Azure 存储、 Azure 事件中心和 Azure Monitor 日志。

### <a name="configure-streaming-of-diagnostics-telemetry-for-elastic-pools"></a>配置弹性池的诊断遥测流

   ![弹性池图标](./media/sql-database-metrics-diag-logging/icon-elastic-pool-text.png)

可将弹性池资源设置为收集以下诊断遥测数据：

| 资源 | 监视遥测数据 |
| :------------------- | ------------------- |
| **弹性池** | [所有指标](sql-database-metrics-diag-logging.md#all-metrics)包含 eDTU/CPU 百分比、eDTU/CPU 限制、物理数据读取百分比、日志写入百分比、会话百分比、辅助角色百分比、存储、存储百分比、存储限制，以及 XTP 存储百分比。 |

若要为弹性池资源启用诊断遥测流，请执行以下步骤：

1. 在 Azure 门户中转到弹性池资源。
1. 选择“诊断设置”。
1. 选择“启用诊断”（如果不存在以前的设置），或选择“编辑设置”来编辑以前的设置

   ![为弹性池启用诊断](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-elasticpool-enable.png)

1. 输入设置名称供自己参考。
1. 选择诊断数据要流式传输到的目标资源：“存档到存储帐户”、“流式传输到事件中心”或“发送到 Log Analytics”。
1. 对于 log analytics 中，选择**配置**，并通过选择创建新的工作区 **+ 创建新工作区**，或选择现有的工作区。
1. 选中弹性池诊断遥测对应的复选框：**AllMetrics**。
1. 选择“保存”。

   ![为弹性池配置诊断](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-elasticpool-selection.png)

> [!NOTE]
> 除了配置弹性池的诊断遥测数据，您还需要配置诊断遥测的每个数据库在弹性池中，按如下所述。 

### <a name="configure-streaming-of-diagnostics-telemetry-for-single-datatbase-or-database-in-elastic-pool"></a>配置的单一数据库或弹性池中的数据库的诊断遥测数据的流式处理

   ![SQL 数据库图标](./media/sql-database-metrics-diag-logging/icon-sql-database-text.png)

若要为单一数据库、入池数据库或实例数据库启用诊断遥测数据的流式传输，请执行以下步骤：

1. 转到 Azure SQL 数据库资源。
1. 选择“诊断设置”。
1. 选择“启用诊断”（如果不存在以前的设置），或选择“编辑设置”来编辑以前的设置
   - 最多可以创建三个并行连接用于流式传输诊断遥测数据。
   - 选择“+添加诊断设置”，配置为将诊断数据并行流式传输到多个资源。

   ![为单一数据库、入池数据库或实例数据库启用诊断](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-sql-enable.png)
1. 输入设置名称供自己参考。
1. 选择诊断数据要流式传输到的目标资源：“存档到存储帐户”、“流式传输到事件中心”或“发送到 Log Analytics”。
1. 对于标准的基于事件的监视体验，请选中数据库诊断日志遥测对应的以下复选框：“SQLInsights”、“AutomaticTuning”、“QueryStoreRuntimeStatistics”、“QueryStoreWaitStatistics”、“Errors”、“DatabaseWaitStatistics”、“Timeouts”、“Blocks”和“Deadlocks”。
1. 对于高级的一分钟间隔监视体验，请选中“AllMetrics”对应的复选框。
1. 选择“保存”。

   ![为单一数据库、入池数据库或实例数据库配置诊断](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-sql-selection.png)

> [!NOTE]
> 无法从数据库诊断设置启用安全审核日志。 若要启用审核日志流式处理，请参阅[为数据库设置审核](sql-database-auditing.md#subheading-2)，并[审核日志在 Azure Monitor 日志和 Azure 事件中心](https://blogs.msdn.microsoft.com/sqlsecurity/2018/09/13/sql-audit-logs-in-azure-log-analytics-and-azure-event-hubs/)。
> [!TIP]
> 针对要监视的每个 Azure SQL 数据库重复上述步骤。

### <a name="configure-streaming-of-diagnostics-telemetry-for-managed-instances"></a>为托管实例配置诊断遥测数据的流式传输

   ![托管实例图标](./media/sql-database-metrics-diag-logging/icon-managed-instance-text.png)

可将托管实例资源设置为收集以下诊断遥测数据：

| 资源 | 监视遥测数据 |
| :------------------- | ------------------- |
| **托管实例** | ResourceUsageStats 包含 Vcore 计数、 平均 CPU 百分比、 IO 请求数、 字节读取/写入、 保留存储空间，并使用存储空间。 |

若要为托管实例资源启用诊断遥测数据的流式传输，请执行以下步骤：

1. 在 Azure 门户中转到托管实例资源。
1. 选择“诊断设置”。
1. 选择“启用诊断”（如果不存在以前的设置），或选择“编辑设置”来编辑以前的设置

   ![为托管实例启用诊断](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-mi-enable.png)

1. 输入设置名称供自己参考。
1. 选择诊断数据要流式传输到的目标资源：“存档到存储帐户”、“流式传输到事件中心”或“发送到 Log Analytics”。
1. 对于 log analytics 中，选择**配置**，并通过选择创建新的工作区 **+ 创建新工作区**，或使用现有的工作区。
1. 选中实例诊断遥测对应的复选框：**ResourceUsageStats**。
1. 选择“保存”。

   ![为托管实例配置诊断](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-mi-selection.png)

> [!NOTE]
> 除了配置的托管实例的诊断遥测数据，您还需要配置诊断遥测的每个实例数据库中，按如下所述。 

### <a name="configure-streaming-of-diagnostics-telemetry-for-instance-databases"></a>配置的流式传输诊断遥测实例数据库

   ![托管实例中的实例数据库图标](./media/sql-database-metrics-diag-logging/icon-mi-database-text.png)

若要启用流式传输诊断遥测的实例的数据库，请执行以下步骤：

1. 转到托管实例中的实例数据库。
2. 选择“诊断设置”。
3. 选择“启用诊断”（如果不存在以前的设置），或选择“编辑设置”来编辑以前的设置
   - 最多可以创建三 (3) 个并行连接用于流式传输诊断遥测数据。
   - 选择“+添加诊断设置”，配置为将诊断数据并行流式传输到多个资源。

   ![为实例数据库启用诊断](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-mi-enable.png)

4. 输入设置名称供自己参考。
5. 选择诊断数据要流式传输到的目标资源：“存档到存储帐户”、“流式传输到事件中心”或“发送到 Log Analytics”。
6. 选中数据库诊断遥测对应的复选框：“SQLInsights”、“QueryStoreRuntimeStatistics”、“QueryStoreWaitStatistics”和“Errors”。
7. 选择“保存”。

   ![为实例数据库配置诊断](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-mi-selection.png)

> [!TIP]
> 为你想要监视每个实例数据库重复这些步骤。

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure 资源管理器模块仍受 Azure SQL 数据库，但未来的所有开发都不适用于 Az.Sql 模块。 有关这些 cmdlet，请参阅[AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)。 命令在 Az 模块和 AzureRm 模块中的参数是大体上相同的。

可以使用 PowerShell 启用指标和诊断日志记录。

- 若要允许在存储帐户中存储诊断日志，请使用以下命令：

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
   ```

   存储帐户 ID 是目标存储帐户的资源 ID。

- 要允许将诊断日志流式传输到事件中心，请使用以下命令：

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
   ```

   Azure 服务总线规则 ID 是以下格式的字符串：

   ```powershell
   {service bus resource ID}/authorizationrules/{key name}
   ```

- 若要允许将诊断日志发送到 Log Analytics 工作区，请使用以下命令：

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
   ```

- 可以使用以下命令获取 Log Analytics 工作区的资源 ID：

   ```powershell
   (Get-AzOperationalInsightsWorkspace).ResourceId
   ```

可以组合这些参数以启用多个输出选项。

### <a name="to-configure-multiple-azure-resources"></a>配置多个 Azure 资源

若要支持多个订阅，请使用 [Enable Azure resource metrics logging using PowerShell](https://blogs.technet.microsoft.com/msoms/20../../enable-azure-resource-metrics-logging-using-powershell/)（通过 PowerShell 启用 Azure 资源指标日志记录）中的 PowerShell 脚本。

在执行脚本 `Enable-AzureRMDiagnostics.ps1` 时提供工作区资源 ID \<$WSID\> 作为参数，以便将诊断数据从多个资源发送到工作区。

- 若要获取诊断数据的目标的工作区 ID \<$WSID\>，请使用以下脚本：

    ```powershell
    PS C:\> $WSID = "/subscriptions/<subID>/resourcegroups/<RG_NAME>/providers/microsoft.operationalinsights/workspaces/<WS_NAME>"
    PS C:\> .\Enable-AzureRMDiagnostics.ps1 -WSID $WSID
    ```
   请将 \<subID\> 替换为订阅 ID，将 \<RG_NAME\> 替换为资源组名称，将 \<WS_NAME\> 替换为工作区名称。

### <a name="azure-cli"></a>Azure CLI

可以使用 Azure CLI 启用指标和诊断日志记录。

> [!NOTE]
> Azure CLI v1.0 支持脚本以启用诊断日志记录。 请注意这一次 CLI 2.0 版不受支持。

- 若要启用在存储帐户中存储诊断日志，请使用以下命令：

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true
   ```

   存储帐户 ID 是目标存储帐户的资源 ID。

- 若要启用将诊断日志流式传输到事件中心，请使用以下命令：

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
   ```

   服务总线规则 ID 是以下格式的字符串：

   ```azurecli-interactive
   {service bus resource ID}/authorizationrules/{key name}
   ```

- 若要启用将诊断日志发送到 Log Analytics 工作区，请使用以下命令：

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --workspaceId <resource id of the log analytics workspace> --enabled true
   ```

可以组合这些参数以启用多个输出选项。

### <a name="rest-api"></a>REST API

阅读有关如何[使用 Azure Monitor REST API 更改诊断设置](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings)的信息。

### <a name="resource-manager-template"></a>资源管理器模板

阅读有关如何[在创建资源时使用资源管理器模板启用诊断设置](../azure-monitor/platform/diagnostic-logs-stream-template.md)的信息。

## <a name="stream-into-azure-sql-analytics"></a>流式传输到 Azure SQL Analytics

Azure SQL Analytics 是一种云解决方案，可跨多个订阅大规模监视 Azure SQL 数据库、弹性池和托管实例的性能。 它可以帮助你收集和可视化 Azure SQL 数据库性能指标，并提供内置智能进行性能故障排除。

![Azure SQL Analytics 概述](../azure-monitor/insights/media/azure-sql/azure-sql-sol-overview.png)

在门户中使用“诊断设置”选项卡上的内置“发送到 Log Analytics”选项，可将 SQL 数据库指标和诊断日志流式处理到 Azure SQL Analytics。 此外可以使用 PowerShell cmdlet、 Azure CLI 或 Azure Monitor REST API 通过诊断设置来启用 log analytics。

### <a name="installation-overview"></a>安装概述

可以使用 Azure SQL Analytics 监视 SQL 数据库群。 执行以下步骤：

1. 从 Azure 市场创建 Azure SQL Analytics 解决方案。
2. 在解决方案中创建监视工作区。
3. 将数据库配置为向该工作区流式传输诊断遥测数据。

如果使用的是弹性池或托管实例，则还要配置从这些资源流式传输诊断遥测数据。

### <a name="create-azure-sql-analytics-resource"></a>创建 Azure SQL Analytics 资源

1. 在 Azure 市场中搜索 Azure SQL Analytics 并选择它。

   ![在门户中搜索 Azure SQL Analytics](./media/sql-database-metrics-diag-logging/sql-analytics-in-marketplace.png)

2. 在解决方案的概述屏幕上选择“创建”。

3. 在“Azure SQL Analytics”窗体中填写所需的附加信息：工作区名称、订阅、资源组、位置和定价层。

   ![在门户中配置 Azure SQL Analytics](./media/sql-database-metrics-diag-logging/sql-analytics-configuration-blade.png)

4. 选择“确定”以确认，然后选择“创建”。

### <a name="configure-databases-to-record-metrics-and-diagnostics-logs"></a>将数据库配置为记录指标和诊断日志

使用 Azure 门户配置数据库记录其指标的位置是最简单的方式。 如前所述，在 Azure 门户中转到 SQL 数据库资源，然后选择“诊断设置”。

如果使用的是弹性池或托管实例，则还需要在这些资源中配置诊断设置，以便将诊断遥测数据流式传输到工作区。

### <a name="use-the-sql-analytics-solution"></a>使用 SQL Analytics 解决方案

可以使用 SQL Analytics 作为分层仪表板来查看 SQL 数据库资源。 若要了解如何使用 SQL Analytics 解决方案，请参阅[使用 SQL Analytics 解决方案监视 SQL 数据库](../log-analytics/log-analytics-azure-sql.md)。

## <a name="stream-into-event-hubs"></a>流式传输到事件中心

在 Azure 门户中使用内置的“流式传输到事件中心”选项可将 SQL 数据库指标和诊断日志流式传输到事件中心。 此外，还可以通过 PowerShell cmdlet、Azure CLI 或 Azure Monitor REST API 使用诊断设置来启用服务总线规则 ID。

### <a name="what-to-do-with-metrics-and-diagnostics-logs-in-event-hubs"></a>如何处理事件中心内的指标和诊断日志

将选定的数据流式传输到事件中心后，就离启动高级监视方案更进一步了。 事件中心充当事件管道的前门。 将数据收集到事件中心后，可以使用实时分析提供程序或存储适配器转换和存储这些数据。 事件中心将事件流的生成从这些事件的使用中分离出来。 通过这种方式，事件使用者可以访问自己的计划中的事件。 有关事件中心的详细信息，请参阅：

- [什么是 Azure 事件中心？](../event-hubs/event-hubs-what-is-event-hubs.md)
- [事件中心入门](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

使用在事件中心流式传输的指标可以：

- **通过将热路径数据流式传输到 Power BI 来查看服务运行状况**。 使用事件中心、流分析和 PowerBI，可以在 Azure 服务中轻松地将指标和诊断数据转换成几近实时的分析结果。 有关如何设置事件中心、如何使用流分析处理数据，以及如何使用 PowerBI 作为输出的概述，请参阅[流分析和 Power BI](../stream-analytics/stream-analytics-power-bi-dashboard.md)。

- **将日志流式传输到第三方日志记录和遥测流**。 使用事件中心流式传输，可将指标和诊断日志引入不同的第三方监视和日志分析解决方案。

- **生成自定义遥测和日志记录平台**。 是否已有一个自定义生成的遥测平台，或者正在考虑生成一个？ 可以利用事件中心高度可缩放的发布-订阅功能灵活引入诊断日志。 请参阅 [Dan Rosanova 的指南：了解如何在全局规模的遥测平台中使用事件中心](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/)。

## <a name="stream-into-storage"></a>流式传输到存储

在 Azure 门户中使用内置的“存档到存储帐户”选项，可以在 Azure 存储中存储 SQL 数据库指标和诊断日志。 此外，还可以通过 PowerShell cmdlet、Azure CLI 或 Azure Monitor REST API 使用诊断设置来启用存储。

### <a name="schema-of-metrics-and-diagnostics-logs-in-the-storage-account"></a>存储帐户中指标和诊断日志的架构

设置指标和诊断日志集合后，当第一行数据可用时，将在你选择的存储帐户中创建一个存储容器。 这些 Blob 的结构为：

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ databases/{database_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

或者使用更简单的形式：

```powershell
insights-{metrics|logs}-{category name}/resourceId=/{resource Id}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

例如，所有指标的 blob 名称可能是：

```powershell
insights-metrics-minute/resourceId=/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.SQL/ servers/Server1/databases/database1/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

如果存储弹性池中的数据，Blob 名称类似于：

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ elasticPools/{elastic_pool_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

### <a name="download-metrics-and-logs-from-storage"></a>从存储下载指标和日志

了解如何[从存储下载指标和诊断日志](../storage/blobs/storage-quickstart-blobs-dotnet.md#download-the-sample-application)。

## <a name="data-retention-policy-and-pricing"></a>数据保留策略和定价

如果选择事件中心或存储帐户，可以指定保留策略。 此策略删除早于选定时间段的数据。 如果指定 Log analytics，保留策略将取决于所选的定价层。 在这种情况下，提供的免费数据引入单位每月可免费监视多个数据库。 消耗的诊断遥测量超过免费单位可能会产生费用。 请注意，与空闲数据相比，工作负荷较重的活动数据库越多，引入的数据就越多。 有关详细信息，请参阅[Log analytics 定价](https://azure.microsoft.com/pricing/details/monitor/)。

如果使用 Azure SQL Analytics，则可以选择 Azure SQL Analytics 导航菜单上的“OMS 工作区”，然后选择“使用情况”和“预估成本”，来监视解决方案中的数据引入消耗量。

## <a name="metrics-and-logs-available"></a>可用的指标和日志

收集的监视遥测数据可用于你自己的_自定义分析_，并结合 [SQL Analytics 语言](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries)用于_应用程序开发_。

## <a name="all-metrics"></a>所有指标

请参阅下表来详细了解按资源列出的所有指标。

### <a name="all-metrics-for-elastic-pools"></a>弹性池的所有指标

|**资源**|**指标**|
|---|---|
|弹性池|eDTU 百分比、已用 eDTU、eDTU 限制、CPU 百分比、物理数据读取百分比、日志写入百分比、会话百分比、辅助角色百分比、存储、存储百分比、存储限制、XTP存储百分比 |

### <a name="all-metrics-for-azure-sql-databases"></a>Azure SQL 数据库的所有指标

|**资源**|**指标**|
|---|---|
|Azure SQL 数据库|DTU 百分比、已用 DTU、DTU 限制、CPU 百分比、物理数据读取百分比、日志写入百分比、成功/失败/防火墙阻止的连接数、会话百分比、辅助角色百分比、存储、存储百分比、XTP 存储百分比和死锁 |

## <a name="all-logs"></a>所有日志

在下表中被公开的遥测数据可用于所有日志的详细信息。 请参阅[支持诊断日志记录](#supported-diagnostic-logging-for-azure-sql-databases-and-instance-databases)汇集在一起，了解哪些日志支持的特定数据库 flavor-单一 Azure SQL 或实例数据库。

### <a name="resource-usage-stats-for-managed-instance"></a>托管实例的资源使用情况统计信息

|属性|描述|
|---|---|
|TenantId|租户 ID |
|SourceSystem|始终为：Azure|
|TimeGenerated [UTC]|记录日志时的时间戳 |
|Type|始终为：AzureDiagnostics |
|ResourceProvider|资源提供程序的名称。 始终为：MICROSOFT.SQL |
|类别|类别的名称。 始终为：ResourceUsageStats |
|资源|资源名称 |
|ResourceType|资源类型的名称。 始终为：MANAGEDINSTANCES |
|SubscriptionId|数据库的订阅 GUID |
|resourceGroup|数据库的资源组名称 |
|LogicalServerName_s|托管实例的名称 |
|ResourceId|资源 URI |
|SKU_s|托管实例产品 SKU |
|virtual_core_count_s|可用 vCore 的数目 |
|avg_cpu_percent_s|CPU 平均百分比 |
|reserved_storage_mb_s|托管实例上的保留存储容量 |
|storage_space_used_mb_s|托管实例上的已用存储 |
|io_requests_s|IOPS 计数 |
|io_bytes_read_s|已读取的 IOPS 字节数 |
|io_bytes_written_s|已写入的 IOPS 字节数 |

### <a name="query-store-runtime-statistics"></a>查询数据存储运行时统计信息

|属性|描述|
|---|---|
|TenantId|租户 ID |
|SourceSystem|始终为：Azure |
|TimeGenerated [UTC]|记录日志时的时间戳 |
|Type|始终为：AzureDiagnostics |
|ResourceProvider|资源提供程序的名称。 始终为：MICROSOFT.SQL |
|类别|类别的名称。 始终为：QueryStoreRuntimeStatistics |
|OperationName|操作的名称。 始终为：QueryStoreRuntimeStatisticsEvent |
|资源|资源名称 |
|ResourceType|资源类型的名称。 始终为：SERVERS/DATABASES |
|SubscriptionId|数据库的订阅 GUID |
|resourceGroup|数据库的资源组名称 |
|LogicalServerName_s|数据库的服务器名称 |
|ElasticPoolName_s|数据库的弹性池（如果有）名称 |
|DatabaseName_s|数据库的名称 |
|ResourceId|资源 URI |
|query_hash_s|查询哈希 |
|query_plan_hash_s|查询计划哈希 |
|statement_sql_handle_s|语句 SQL 句柄 |
|interval_start_time_d|间隔的开始 datetimeoffset，以从 1900-1-1 开始的时钟周期数计 |
|interval_end_time_d|间隔的结束 datetimeoffset，以从 1900-1-1 开始的时钟周期数计 |
|logical_io_writes_d|逻辑 IO 写入总次数 |
|max_logical_io_writes_d|每次执行逻辑 IO 写入的最大次数 |
|physical_io_reads_d|物理 IO 读取总次数 |
|max_physical_io_reads_d|每次执行逻辑 IO 读取的最大次数 |
|logical_io_reads_d|逻辑 IO 读取总次数 |
|max_logical_io_reads_d|每次执行逻辑 IO 读取的最大次数 |
|execution_type_d|执行类型 |
|count_executions_d|执行查询的次数 |
|cpu_time_d|查询使用的总 CPU 时间（以微秒为单位） |
|max_cpu_time_d|单个执行消耗的最大 CPU 时间（以微秒为单位） |
|dop_d|并行度总和 |
|max_dop_d|用于单个执行的最大并行度 |
|rowcount_d|返回的总行数 |
|max_rowcount_d|单个执行中返回的最大行数 |
|query_max_used_memory_d|已使用的内存总量（以 KB 为单位） |
|max_query_max_used_memory_d|单个执行使用的最大内存量（以 KB 为单位） |
|duration_d|总执行时间（以微秒为单位） |
|max_duration_d|单个执行的最大执行时间 |
|num_physical_io_reads_d|总物理读取次数 |
|max_num_physical_io_reads_d|每次执行最大物理读取次数 |
|log_bytes_used_d|使用的日志字节总量 |
|max_log_bytes_used_d|每次执行使用的日志字节最大数量 |
|query_id_d|查询存储中查询的 ID |
|plan_id_d|查询存储中计划的 ID |

详细了解[查询存储运行时统计信息数据](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-runtime-stats-transact-sql)。

### <a name="query-store-wait-statistics"></a>查询存储等待统计信息

|属性|描述|
|---|---|
|TenantId|租户 ID |
|SourceSystem|始终为：Azure |
|TimeGenerated [UTC]|记录日志时的时间戳 |
|Type|始终为：AzureDiagnostics |
|ResourceProvider|资源提供程序的名称。 始终为：MICROSOFT.SQL |
|类别|类别的名称。 始终为：QueryStoreWaitStatistics |
|OperationName|操作的名称。 始终为：QueryStoreWaitStatisticsEvent |
|资源|资源名称 |
|ResourceType|资源类型的名称。 始终为：SERVERS/DATABASES |
|SubscriptionId|数据库的订阅 GUID |
|resourceGroup|数据库的资源组名称 |
|LogicalServerName_s|数据库的服务器名称 |
|ElasticPoolName_s|数据库的弹性池（如果有）名称 |
|DatabaseName_s|数据库的名称 |
|ResourceId|资源 URI |
|wait_category_s|等待的类别 |
|is_parameterizable_s|查询是否可以参数化 |
|statement_type_s|语句的类型 |
|statement_key_hash_s|语句密钥哈希 |
|exec_type_d|执行类型 |
|total_query_wait_time_ms_d|查询特定等待类别的总等待时间 |
|max_query_wait_time_ms_d|单个执行中针对具体等待类别的查询的最大等待时间 |
|query_param_type_d|0 |
|query_hash_s|查询存储中的查询哈希 |
|query_plan_hash_s|查询存储中的查询计划哈希。 |
|statement_sql_handle_s|查询存储中的语句句柄 |
|interval_start_time_d|间隔的开始 datetimeoffset，以从 1900-1-1 开始的时钟周期数计 |
|interval_end_time_d|间隔的结束 datetimeoffset，以从 1900-1-1 开始的时钟周期数计 |
|count_executions_d|执行查询的次数 |
|query_id_d|查询存储中查询的 ID |
|plan_id_d|查询存储中计划的 ID |

详细了解[查询存储等待统计数据](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql)。

### <a name="errors-dataset"></a>错误数据集

|属性|描述|
|---|---|
|TenantId|租户 ID |
|SourceSystem|始终为：Azure |
|TimeGenerated [UTC]|记录日志时的时间戳 |
|Type|始终为：AzureDiagnostics |
|ResourceProvider|资源提供程序的名称。 始终为：MICROSOFT.SQ |
|类别|类别的名称。 始终为：错误 |
|OperationName|操作的名称。 始终为：ErrorEvent |
|资源|资源名称 |
|ResourceType|资源类型的名称。 始终为：SERVERS/DATABASES |
|SubscriptionId|数据库的订阅 GUID |
|resourceGroup|数据库的资源组名称 |
|LogicalServerName_s|数据库的服务器名称 |
|ElasticPoolName_s|数据库的弹性池（如果有）名称 |
|DatabaseName_s|数据库的名称 |
|ResourceId|资源 URI |
|消息|纯文本格式的错误消息 |
|user_defined_b|是否是用户定义位错误 |
|error_number_d|错误代码 |
|严重性|错误的严重性 |
|state_d|错误的状态 |
|query_hash_s|失败查询的查询哈希（如果有） |
|query_plan_hash_s|失败查询的查询计划哈希（如果有） |

详细了解 [SQL Server 错误消息](https://msdn.microsoft.com/library/cc645603.aspx)。

### <a name="database-wait-statistics-dataset"></a>数据库等待统计数据集

|属性|描述|
|---|---|
|TenantId|租户 ID |
|SourceSystem|始终为：Azure |
|TimeGenerated [UTC]|记录日志时的时间戳 |
|Type|始终为：AzureDiagnostics |
|ResourceProvider|资源提供程序的名称。 始终为：MICROSOFT.SQL |
|类别|类别的名称。 始终为：DatabaseWaitStatistics |
|OperationName|操作的名称。 始终为：DatabaseWaitStatisticsEvent |
|资源|资源名称 |
|ResourceType|资源类型的名称。 始终为：SERVERS/DATABASES |
|SubscriptionId|数据库的订阅 GUID |
|resourceGroup|数据库的资源组名称 |
|LogicalServerName_s|数据库的服务器名称 |
|ElasticPoolName_s|数据库的弹性池（如果有）名称 |
|DatabaseName_s|数据库的名称 |
|ResourceId|资源 URI |
|wait_type_s|等待类型的名称 |
|start_utc_date_t [UTC]|测量周期开始时间 |
|end_utc_date_t [UTC]|测量周期结束时间 |
|delta_max_wait_time_ms_d|每次执行最大等待时间 |
|delta_signal_wait_time_ms_d|总信号等待时间 |
|delta_wait_time_ms_d|期间内的总等待时间 |
|delta_waiting_tasks_count_d|等待任务数 |

详细了解[数据库等待统计信息](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql)。

### <a name="time-outs-dataset"></a>超时数据集

|属性|描述|
|---|---|
|TenantId|租户 ID |
|SourceSystem|始终为：Azure |
|TimeGenerated [UTC]|记录日志时的时间戳 |
|Type|始终为：AzureDiagnostics |
|ResourceProvider|资源提供程序的名称。 始终为：MICROSOFT.SQL |
|类别|类别的名称。 始终为：超时 |
|OperationName|操作的名称。 始终为：TimeoutEvent |
|资源|资源名称 |
|ResourceType|资源类型的名称。 始终为：SERVERS/DATABASES |
|SubscriptionId|数据库的订阅 GUID |
|resourceGroup|数据库的资源组名称 |
|LogicalServerName_s|数据库的服务器名称 |
|ElasticPoolName_s|数据库的弹性池（如果有）名称 |
|DatabaseName_s|数据库的名称 |
|ResourceId|资源 URI |
|error_state_d|错误状态代码 |
|query_hash_s|查询哈希（如果有） |
|query_plan_hash_s|查询计划哈希（如果有） |

### <a name="blockings-dataset"></a>阻塞数据集

|属性|描述|
|---|---|
|TenantId|租户 ID |
|SourceSystem|始终为：Azure |
|TimeGenerated [UTC]|记录日志时的时间戳 |
|Type|始终为：AzureDiagnostics |
|ResourceProvider|资源提供程序的名称。 始终为：MICROSOFT.SQL |
|类别|类别的名称。 始终为：块 |
|OperationName|操作的名称。 始终为：BlockEvent |
|资源|资源名称 |
|ResourceType|资源类型的名称。 始终为：SERVERS/DATABASES |
|SubscriptionId|数据库的订阅 GUID |
|resourceGroup|数据库的资源组名称 |
|LogicalServerName_s|数据库的服务器名称 |
|ElasticPoolName_s|数据库的弹性池（如果有）名称 |
|DatabaseName_s|数据库的名称 |
|ResourceId|资源 URI |
|lock_mode_s|查询所使用的锁模式 |
|resource_owner_type_s|锁的所有者 |
|blocked_process_filtered_s|阻塞进程报告 XML |
|duration_d|锁定持续时间（以毫秒为单位） |

### <a name="deadlocks-dataset"></a>死锁数据集

|属性|描述|
|---|---|
|TenantId|租户 ID |
|SourceSystem|始终为：Azure |
|TimeGenerated [UTC] |记录日志时的时间戳 |
|Type|始终为：AzureDiagnostics |
|ResourceProvider|资源提供程序的名称。 始终为：MICROSOFT.SQL |
|类别|类别的名称。 始终为：死锁数 |
|OperationName|操作的名称。 始终为：DeadlockEvent |
|资源|资源名称 |
|ResourceType|资源类型的名称。 始终为：SERVERS/DATABASES |
|SubscriptionId|数据库的订阅 GUID |
|resourceGroup|数据库的资源组名称 |
|LogicalServerName_s|数据库的服务器名称 |
|ElasticPoolName_s|数据库的弹性池（如果有）名称 |
|DatabaseName_s|数据库的名称 |
|ResourceId|资源 URI |
|deadlock_xml_s|死锁报告 XML |

### <a name="automatic-tuning-dataset"></a>自动优化数据集

|属性|描述|
|---|---|
|TenantId|租户 ID |
|SourceSystem|始终为：Azure |
|TimeGenerated [UTC]|记录日志时的时间戳 |
|Type|始终为：AzureDiagnostics |
|ResourceProvider|资源提供程序的名称。 始终为：MICROSOFT.SQL |
|类别|类别的名称。 始终为：AutomaticTuning |
|资源|资源名称 |
|ResourceType|资源类型的名称。 始终为：SERVERS/DATABASES |
|SubscriptionId|数据库的订阅 GUID |
|resourceGroup|数据库的资源组名称 |
|LogicalServerName_s|数据库的服务器名称 |
|LogicalDatabaseName_s|数据库的名称 |
|ElasticPoolName_s|数据库的弹性池（如果有）名称 |
|DatabaseName_s|数据库的名称 |
|ResourceId|资源 URI |
|RecommendationHash_s|自动优化建议的唯一哈希值 |
|OptionName_s|自动优化操作 |
|Schema_s|数据库架构 |
|Table_s|受影响的表 |
|IndexName_s|索引名称 |
|IndexColumns_s|列名称 |
|IncludedColumns_s|包括的列 |
|EstimatedImpact_s|自动优化建议 JSON 的估计影响 |
|Event_s|自动优化事件的类型 |
|Timestamp_t|上次更新时间戳 |

### <a name="intelligent-insights-dataset"></a>智能见解数据集

详细了解 [Intelligent Insights 日志格式](sql-database-intelligent-insights-use-diagnostics-log.md)。

## <a name="next-steps"></a>后续步骤

若要了解如何启用日志记录并了解各种 Azure 服务支持的指标和日志类别，请参阅：

- [Microsoft Azure 中的指标概述](../monitoring-and-diagnostics/monitoring-overview-metrics.md)
- [Azure 诊断日志概述](../azure-monitor/platform/diagnostic-logs-overview.md)

若要了解事件中心，请阅读以下主题：

- [什么是 Azure 事件中心？](../event-hubs/event-hubs-what-is-event-hubs.md)
- [事件中心入门](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

若要详细了解 Azure 存储，请参阅[如何从存储下载指标和诊断日志](../storage/blobs/storage-quickstart-blobs-dotnet.md#download-the-sample-application)。
