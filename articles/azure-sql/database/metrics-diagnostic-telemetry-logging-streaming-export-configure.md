---
title: 配置指标和资源日志的流式导出
description: 了解如何配置指标和资源日志的流式导出，包括将 Azure SQL 数据库和 Azure SQL 托管实例中的智能诊断分析导出到所选目标，以存储有关资源利用率和查询执行统计的信息。
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: seoapril2019
ms.devlang: sqldbrb=2
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, sstein
ms.date: 04/06/2020
ms.openlocfilehash: 2c8c31219f014935c14371cf4dd30b61ba35819b
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91319497"
---
# <a name="configure-streaming-export-of-azure-sql-database-and-sql-managed-instance-diagnostic-telemetry"></a>配置 Azure SQL 数据库和 Azure SQL 托管实例诊断遥测的流式导出
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

本文介绍可导出到多个目标之一以进行分析的 Azure SQL 数据库性能指标和资源日志。 其中将会介绍如何通过 Azure 门户、PowerShell、Azure CLI、REST API 和 Azure 资源管理器模板配置这些诊断遥测数据的流式导出。

此外，介绍可将此诊断遥测数据流式传输到哪些目标，以及如何选择不同的目标。 目标选项包括：

- [Log Analytics 和 SQL Analytics](#stream-into-sql-analytics)
- [事件中心](#stream-into-event-hubs)
- [Azure 存储](#stream-into-azure-storage)

## <a name="diagnostic-telemetry-for-export"></a>导出的诊断遥测

在可导出的诊断遥测数据中，最重要的是智能见解 (SQLInsights) 日志。 [智能见解](intelligent-insights-overview.md)使用内置智能，通过人工智能持续监视数据库使用情况，并检测导致性能不佳的干扰性事件。 检测后，将执行详细分析，并生成包含对问题的智能评估的智能见解日志。 此评估包含对数据库性能问题的根本原因分析，以及为性能改进而提供的可行性建议。 需要配置此日志的流式导出才能查看其内容。

除了流式导出智能见解日志以外，还可以导出各种性能指标和其他数据库日志。 下表描述了可以配置为流式导出到多个目标之一的性能指标和资源日志。 可为单一数据库、弹性池和共用数据库以及托管实例和实例数据库配置此诊断遥测。

| 数据库的诊断遥测 | Azure SQL 数据库支持 | Azure SQL 托管实例支持 |
| :------------------- | ----- | ----- |
| [基本指标](#basic-metrics)：包含 DTU/CPU 百分比、DTU/CPU 限制、物理数据读取百分比、日志写入百分比、成功/失败/防火墙阻止的连接数、会话百分比、辅助角色百分比、存储、存储百分比和 XTP 存储百分比。 | 是 | 否 |
| [实例和应用高级指标](#advanced-metrics)：包含所使用的 tempdb 系统数据库数据和日志文件大小以及 tempdb 百分比日志文件。 | 是 | 否 |
| [QueryStoreRuntimeStatistics](#query-store-runtime-statistics)：包含有关查询运行时统计信息的信息，例如 CPU 使用率、查询持续时间统计信息。 | 是 | 是 |
| [QueryStoreWaitStatistics](#query-store-wait-statistics)：包含有关查询等待统计信息的信息（查询正在等待什么），例如 CPU、日志和锁定。 | 是 | 是 |
| [错误](#errors-dataset)：包含有关数据库发生的 SQL 错误的信息。 | 是 | 是 |
| [DatabaseWaitStatistics](#database-wait-statistics-dataset)：包含有关数据库针对不同等待类型花费多少时间等待的信息。 | 是 | 否 |
| [Timeouts](#time-outs-dataset)：包含有关数据库发生的超时的信息。 | 是 | 否 |
| [Blocks](#blockings-dataset)：包含有关数据库发生的阻塞事件的信息。 | 是 | 否 |
| [死锁数](#deadlocks-dataset)：包含有关数据库发生的死锁事件的信息。 | 是 | 否 |
| [AutomaticTuning](#automatic-tuning-dataset)：包含有关数据库的自动优化建议的信息。 | 是 | 否 |
| [SQLInsights](#intelligent-insights-dataset)：包含针对数据库性能的智能见解。 有关详细信息，请参阅[智能见解](intelligent-insights-overview.md)。 | 是 | 是 |

> [!NOTE]
> 不能为**系统数据库**（例如 master、msdb、model、resource 和 tempdb 数据库）配置诊断设置。

## <a name="streaming-export-destinations"></a>流式导出目标

此诊断遥测数据可流式传输到以下 Azure 资源之一进行分析。

- **[Log Analytics 工作区](#stream-into-sql-analytics)**：

  流式传输到 [Log Analytics 工作区](../../azure-monitor/platform/resource-logs-collect-workspace.md) 的数据可由 [SQL Analytics](../../azure-monitor/insights/azure-sql.md)使用。 SQL Analytics 是一种仅限云的监视解决方案，提供对数据库的智能监视，其中包括性能报告、警报和缓解建议。 可以使用收集的其他监视数据对流式传输到 Log Analytics 工作区的数据进行分析，还可以利用其他 Azure Monitor 功能，如警报和可视化效果
- **[Azure 事件中心](#stream-into-event-hubs)** ：

  将数据流式传输到 [Azure 事件中心](../../azure-monitor/platform/resource-logs-stream-event-hubs.md)可提供以下功能：

  - 将日志流式传输到第三方日志记录和遥测系统：将所有指标和资源日志流式传输到单个事件中心，以通过管道将日志数据传送到第三方 SIEM 或日志分析工具。
  - **生成自定义遥测和日志记录平台**：可利用事件中心高度可缩放的发布-订阅功能，灵活地将指标和资源日志引入到自定义遥测平台。 有关详细信息，请参阅 [Designing and Sizing a Global Scale Telemetry Platform on Azure Event Hubs](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/)（在 Azure 事件中心设计全球规模的遥测平台并设置其大小）。
  - **通过将数据流式传输到 Power BI 来查看服务运行状况**：使用事件中心、流分析和 Power BI 将诊断数据转换为 Azure 服务的近乎实时见解。 有关此解决方案的详细信息，请参阅 [流分析和 Power BI：用于流式处理数据的实时分析仪表板](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-power-bi-dashboard) 。
- **[Azure 存储](#stream-into-azure-storage)** ：

  将数据流式传输到 [Azure 存储](../../azure-monitor/platform/resource-logs-collect-storage.md)可以存档大量诊断遥测数据，并且成本只是前两种流式传输选项的一小部分。

流式传输到这些目标之一的诊断遥测数据可用于度量资源利用率和查询执行统计信息，以便更轻松地监视性能。

![关系图显示了多个托管实例中的多个 SQL 数据库和数据库，将遥测数据发送到 Azure 诊断，后者将信息转发到 Azure SQL Analytics、事件中心和存储。](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/architecture.png)

## <a name="enable-and-configure-the-streaming-export-of-diagnostic-telemetry"></a>启用和配置诊断遥测数据的流式导出

可使用以下方法之一来启用和管理指标与诊断遥测日志记录：

- Azure 门户
- PowerShell
- Azure CLI
- Azure Monitor REST API
- Azure Resource Manager 模板

> [!NOTE]
> 若要启用安全遥测的审核日志流式传输，请参阅[为数据库设置审核](../../sql-database/sql-database-auditing.md#setup-auditing)以及 [Azure Monitor 日志和 Azure 事件中心中的审核日志](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/SQL-Audit-logs-in-Azure-Log-Analytics-and-Azure-Event-Hubs/ba-p/386242)。

## <a name="configure-the-streaming-export-of-diagnostic-telemetry"></a>配置诊断遥测数据的流式导出

可以使用 Azure 门户中的“诊断设置”菜单来启用和配置诊断遥测数据的流式传输。 此外，还可以使用 PowerShell、Azure CLI、[REST API](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings) 和[资源管理器模板](../../azure-monitor/platform/diagnostic-settings-template.md)来配置诊断遥测数据的流式传输。 可设置以下目标来流式传输诊断遥测数据：Azure 存储、Azure 事件中心和 Azure Monitor 日志。

> [!IMPORTANT]
> 默认不会启用诊断遥测数据的流式导出。

选择以下选项卡之一，获取有关在 Azure 门户中配置诊断遥测数据的流式导出的分步指导，以及用于在 PowerShell 和 Azure CLI 中实现相同目的的脚本。

# <a name="azure-portal"></a>[Azure 门户](#tab/azure-portal)

### <a name="elastic-pools-in-azure-sql-database"></a>Azure SQL 数据库中的弹性池

可将弹性池资源设置为收集以下诊断遥测数据：

| 资源 | 监视遥测数据 |
| :------------------- | ------------------- |
| **弹性池** | [基本指标](metrics-diagnostic-telemetry-logging-streaming-export-configure.md#basic-metrics)包含 eDTU/CPU 百分比、eDTU/CPU 限制、物理数据读取百分比、日志写入百分比、会话百分比、辅助角色百分比、存储、存储百分比、存储限制，以及 XTP 存储百分比。 |

若要为弹性池和共用数据库配置诊断遥测数据的流式传输，需要分别配置每一项：

- 为弹性池启用诊断遥测数据的流式传输
- 为弹性池中的每个数据库启用诊断遥测数据的流式传输

弹性池容器具有其自己的遥测数据，与每个单独的共用数据库的遥测数据分开。

若要为弹性池资源启用诊断遥测流，请执行以下步骤：

1. 在 Azure 门户中转到**弹性池**资源。
2. 选择“诊断设置”。
3. 选择“启用诊断”（如果不存在以前的设置），或选择“编辑设置”来编辑以前的设置 

   ![为弹性池启用诊断](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/diagnostics-settings-container-elasticpool-enable.png)

4. 输入设置名称供自己参考。
5. 选择诊断数据要流式传输到的目标资源：“存档到存储帐户”、“流式传输到事件中心”或“发送到 Log Analytics”。  
6. 对于 Log Analytics，请选择“配置”，并通过选择“+创建新工作区”来创建新工作区；或者选择现有的工作区。 
7. 选中弹性池诊断遥测对应的复选框：**基本**指标。
   ![为弹性池配置诊断](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/diagnostics-settings-container-elasticpool-selection.png)

8. 选择“保存” 。
9. 另外，请为弹性池中需要监视的每个数据库配置诊断遥测数据的流式传输，只需按下一部分所述的步骤操作即可。

> [!IMPORTANT]
> 除了为弹性池配置诊断遥测数据，还需为弹性池中的每个数据库配置诊断遥测数据。

### <a name="databases-in-azure-sql-database"></a>Azure SQL 数据库中的数据库

可将数据库资源设置为收集以下诊断遥测数据：

| 资源 | 监视遥测数据 |
| :------------------- | ------------------- |
| **单一数据库或共用数据库** | [基本指标](metrics-diagnostic-telemetry-logging-streaming-export-configure.md#basic-metrics)包含 DTU 百分比、已用 DTU、DTU 限制、CPU 百分比、物理数据读取百分比、日志写入百分比、成功/失败/防火墙阻止的连接数、会话百分比、辅助角色百分比、存储、存储百分比、XTP 存储百分比和死锁。 |

若要为单一数据库或共用数据库启用诊断遥测数据的流式传输，请执行以下步骤：

1. 转到 Azure **SQL 数据库**资源。
2. 选择“诊断设置”。
3. 选择“启用诊断”（如果不存在以前的设置），或选择“编辑设置”来编辑以前的设置  最多可以创建三个并行连接用于流式传输诊断遥测数据。
4. 选择“添加诊断设置”，以便配置将诊断数据并行流式传输到多个资源。

   ![为单一数据库和共用数据库启用诊断](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/diagnostics-settings-database-sql-enable.png)

5. 输入设置名称供自己参考。
6. 选择诊断数据要流式传输到的目标资源：“存档到存储帐户”、“流式传输到事件中心”或“发送到 Log Analytics”。  
7. 对于标准的基于事件的监视体验，请选中数据库诊断日志遥测对应的以下复选框：“SQLInsights”、“AutomaticTuning”、“QueryStoreRuntimeStatistics”、“QueryStoreWaitStatistics”、“Errors”、“DatabaseWaitStatistics”、“Timeouts”、“Blocks”和“Deadlocks”。        
8. 对于高级的一分钟间隔监视体验，请选中“基本”指标对应的复选框。

   ![为 Azure SQL 数据库配置诊断](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/diagnostics-settings-database-sql-selection.png)
9. 选择“保存” 。
10. 针对要监视的每个数据库重复上述步骤。

> [!TIP]
> 针对要监视的每个单一数据库和共用数据库重复上述步骤。

### <a name="instances-in-azure-sql-managed-instance"></a>Azure SQL 托管实例中的实例

可将托管实例资源设置为收集以下诊断遥测数据：

| 资源 | 监视遥测数据 |
| :------------------- | ------------------- |
| **托管实例** | [ResourceUsageStats](#resource-usage-stats-for-managed-instances) 包含 vCore 计数、平均 CPU 百分比、IO 请求数、读取/写入的字节数、保留的存储空间和已使用的存储空间。 |

若要为托管实例和实例数据库配置诊断遥测数据的流式传输，需要分别配置每一项：

- 为托管实例启用诊断遥测数据的流式传输
- 为每个实例数据库启用诊断遥测流

托管实例容器具有其自己的遥测数据，与每个实例数据库的遥测数据分开。

若要为托管实例资源启用诊断遥测数据的流式传输，请执行以下步骤：

1. 在 Azure 门户中转到**托管实例**资源。
2. 选择“诊断设置”。
3. 选择“启用诊断”（如果不存在以前的设置），或选择“编辑设置”来编辑以前的设置 

   ![为托管实例启用诊断](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/diagnostics-settings-container-mi-enable.png)

4. 输入设置名称供自己参考。
5. 选择诊断数据要流式传输到的目标资源：“存档到存储帐户”、“流式传输到事件中心”或“发送到 Log Analytics”。  
6. 对于 Log Analytics，请选择“配置”，并通过选择“+创建新工作区”来创建新工作区；或者使用现有的工作区。 
7. 选中实例诊断遥测对应的复选框：**ResourceUsageStats**。

   ![为托管实例配置诊断](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/diagnostics-settings-container-mi-selection.png)

8. 选择“保存” 。
9. 另外，请为托管实例中需要监视的每个实例数据库配置诊断遥测流，只需按下一部分所述的步骤操作即可。

> [!IMPORTANT]
> 除了为托管实例配置诊断遥测数据，还需为每个实例数据库配置诊断遥测数据。

### <a name="databases-in-azure-sql-managed-instance"></a>Azure SQL 托管实例中的数据库

可将实例数据库资源设置为收集以下诊断遥测数据：

| 资源 | 监视遥测数据 |
| :------------------- | ------------------- |
| **实例数据库** | [ResourceUsageStats](#resource-usage-stats-for-managed-instances) 包含 vCore 计数、平均 CPU 百分比、IO 请求数、读取/写入的字节数、保留的存储空间和已使用的存储空间。 |

若要为实例数据库启用诊断遥测数据的流式传输，请执行以下步骤：

1. 转到托管实例中的**实例数据库**资源。
2. 选择“诊断设置”。
3. 选择“启用诊断”（如果不存在以前的设置），或选择“编辑设置”来编辑以前的设置 
   - 最多可以创建三 (3) 个并行连接用于流式传输诊断遥测数据。
   - 选择“+添加诊断设置”，配置为将诊断数据并行流式传输到多个资源。

   ![为实例数据库启用诊断](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/diagnostics-settings-database-mi-enable.png)

4. 输入设置名称供自己参考。
5. 选择诊断数据要流式传输到的目标资源：“存档到存储帐户”、“流式传输到事件中心”或“发送到 Log Analytics”。  
6. 选中数据库诊断遥测对应的复选框：“SQLInsights”、“QueryStoreRuntimeStatistics”、“QueryStoreWaitStatistics”和“Errors”。   
   ![为实例数据库配置诊断](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/diagnostics-settings-database-mi-selection.png)
7. 选择“保存” 。
8. 针对要监视的每个实例数据库重复上述步骤。

> [!TIP]
> 针对要监视的每个实例数据库重复上述步骤。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!IMPORTANT]
> 仍然支持 PowerShell Azure 资源管理器模块，但是所有未来的开发都是针对 Az.Sql 模块。 若要了解这些 cmdlet，请参阅 [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)。 Az 模块和 AzureRm 模块中的命令参数大体上是相同的。

可以使用 PowerShell 启用指标和诊断日志记录。

- 若要在存储帐户中启用指标和资源日志的存储，请使用以下命令：

  ```powershell
  Set-AzDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
  ```

  存储帐户 ID 是目标存储帐户的资源 ID。

- 若要启用将指标和资源日志流式传输到事件中心，请使用以下命令：

  ```powershell
  Set-AzDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
  ```

  Azure 服务总线规则 ID 是以下格式的字符串：

  ```powershell
  {service bus resource ID}/authorizationrules/{key name}
  ```

- 若要启用将指标和资源日志发送到 Log Analytics 工作区，请使用以下命令：

  ```powershell
  Set-AzDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
  ```

- 可以使用以下命令获取 Log Analytics 工作区的资源 ID：

  ```powershell
  (Get-AzOperationalInsightsWorkspace).ResourceId
  ```

可以结合这些参数启用多个输出选项。

**配置多个 Azure 资源**

若要支持多个订阅，请使用 [Enable Azure resource metrics logging using PowerShell](https://blogs.technet.microsoft.com/msoms/20../../enable-azure-resource-metrics-logging-using-powershell/)（通过 PowerShell 启用 Azure 资源指标日志记录）中的 PowerShell 脚本。

\<$WSID\>执行脚本 `Enable-AzureRMDiagnostics.ps1` 将诊断数据从多个资源发送到工作区时，提供工作区资源 ID 作为参数。

- 若要获取诊断数据的目标的工作区 ID \<$WSID\> ，请使用以下脚本：

  ```powershell
  $WSID = "/subscriptions/<subID>/resourcegroups/<RG_NAME>/providers/microsoft.operationalinsights/workspaces/<WS_NAME>"
  .\Enable-AzureRMDiagnostics.ps1 -WSID $WSID
  ```

  将替换 \<subID\> 为订阅 ID，将替换为 \<RG_NAME\> 资源组名称，将替换为 \<WS_NAME\> 工作区名称。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

可以使用 Azure CLI 启用指标和诊断日志记录。

> [!IMPORTANT]
> Azure CLI v1.0 支持通过脚本来启用诊断日志记录。 目前不支持 Azure CLI v2.0。

- 若要在存储帐户中启用指标和资源日志的存储，请使用以下命令：

  ```azurecli-interactive
  azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true
  ```

  存储帐户 ID 是目标存储帐户的资源 ID。

- 若要启用将指标和资源日志流式传输到事件中心，请使用以下命令：

  ```azurecli-interactive
  azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
  ```

  服务总线规则 ID 是以下格式的字符串：

  ```azurecli-interactive
  {service bus resource ID}/authorizationrules/{key name}
  ```

- 若要启用将指标和资源日志发送到 Log Analytics 工作区，请使用以下命令：

  ```azurecli-interactive
  azure insights diagnostic set --resourceId <resourceId> --workspaceId <resource id of the log analytics workspace> --enabled true
  ```

可以结合这些参数启用多个输出选项。

---

## <a name="stream-into-sql-analytics"></a>流式传输到 SQL Analytics

Azure SQL 数据库和 Azure SQL 托管实例度量值以及流式传输到 Log Analytics 工作区中的资源日志可 Azure SQL Analytics 使用。 Azure SQL Analytics 是一种云解决方案，可跨多个订阅大规模监视单一数据库、弹性池和池中的数据库以及托管实例和实例数据库的性能。 它可以帮助您收集和可视化性能指标，并提供内置的性能故障排除智能。

![Azure SQL Analytics 概述](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/azure-sql-sol-overview.png)

### <a name="installation-overview"></a>安装概述

可以通过执行以下步骤，使用 Azure SQL Analytics 监视数据库和数据库集合的集合：

1. 从 Azure 市场创建 Azure SQL Analytics 解决方案。
2. 在解决方案中创建 Log Analytics 工作区。
3. 将数据库配置为将诊断遥测流式传输到工作区中。

您可以使用 Azure 门户的 "诊断设置" 选项卡中的内置 " **发送到 Log Analytics** " 选项配置此诊断遥测的流式导出。 还可以通过 [PowerShell cmdlet](metrics-diagnostic-telemetry-logging-streaming-export-configure.md?tabs=azure-powershell#configure-the-streaming-export-of-diagnostic-telemetry)、 [Azure CLI](metrics-diagnostic-telemetry-logging-streaming-export-configure.md?tabs=azure-cli#configure-the-streaming-export-of-diagnostic-telemetry)、 [Azure Monitor REST API](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings)或 [资源管理器模板](../../azure-monitor/platform/diagnostic-settings-template.md)使用诊断设置，启用到 Log Analytics 工作区的流式传输。

### <a name="create-an-azure-sql-analytics-resource"></a>创建 Azure SQL Analytics 资源

1. 在 Azure 市场中搜索 Azure SQL Analytics 并选择它。

   ![在门户中搜索 Azure SQL Analytics](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/sql-analytics-in-marketplace.png)

2. 在解决方案的 "概述" 屏幕上选择 " **创建** "。

3. 在“Azure SQL Analytics”窗体中填写所需的附加信息：工作区名称、订阅、资源组、位置和定价层。

   ![在门户中配置 Azure SQL Analytics](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/sql-analytics-configuration-blade.png)

4. 选择“确定”以确认，然后选择“创建”********。

### <a name="configure-the-resource-to-record-metrics-and-resource-logs"></a>将资源配置为记录指标和资源日志

需要为单一数据库、共用数据库、弹性池、托管实例和实例数据库单独配置诊断遥测流。 使用 Azure 门户配置资源记录指标的最简单方法。 有关详细步骤，请参阅 [配置诊断遥测的流式导出](metrics-diagnostic-telemetry-logging-streaming-export-configure.md?tabs=azure-portal#configure-the-streaming-export-of-diagnostic-telemetry)。

### <a name="use-azure-sql-analytics-for-monitoring-and-alerting"></a>使用 Azure SQL Analytics 进行监视和警报

您可以使用 SQL Analytics 作为分层仪表板来查看数据库资源。

- 若要了解如何使用 Azure SQL Analytics，请参阅 [使用 SQL Analytics 进行监视](../../azure-monitor/insights/azure-sql.md)。
- 若要了解如何在 SQL 分析中设置警报，请参阅为 [数据库、弹性池和托管实例创建警报](../../azure-monitor/insights/azure-sql.md#analyze-data-and-create-alerts)。

## <a name="stream-into-event-hubs"></a>流式传输到事件中心

在 Azure 门户中使用内置的“流式传输到事件中心”选项可将 Azure SQL 数据库和 Azure SQL 托管实例指标和资源日志流式传输到事件中心。 此外，还可以通过 PowerShell cmdlet、Azure CLI 或 Azure Monitor REST API 使用诊断设置来启用服务总线规则 ID。 请确保事件中心与数据库和服务器位于同一区域。

### <a name="what-to-do-with-metrics-and-resource-logs-in-event-hubs"></a>如何处理事件中心内的指标和资源日志

将选定的数据流式传输到事件中心后，就离启动高级监视方案更进一步了。 事件中心充当事件管道的前门。 将数据收集到事件中心后，可以使用实时分析提供程序或存储适配器转换和存储这些数据。 事件中心将事件流的生成从这些事件的使用中分离出来。 通过这种方式，事件使用者可以访问自己的计划中的事件。 有关事件中心的详细信息，请参阅：

- [什么是 Azure 事件中心？](../../event-hubs/event-hubs-about.md)
- [事件中心入门](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)

使用在事件中心流式传输的指标可以：

- **通过将热路径数据流式传输到 Power BI 来查看服务运行状况**

  使用事件中心、流分析和 PowerBI，可以在 Azure 服务中轻松地将指标和诊断数据转换成几近实时的分析结果。 有关如何设置事件中心、如何使用流分析处理数据，以及如何使用 PowerBI 作为输出的概述，请参阅[流分析和 Power BI](../../stream-analytics/stream-analytics-power-bi-dashboard.md)。

- **将日志流式传输到第三方日志记录和遥测流**

  使用事件中心流式传输，可将指标和资源日志引入到不同的第三方监视和日志分析解决方案。

- **生成自定义遥测和日志记录平台**

  是否已有一个自定义生成的遥测平台，或者正在考虑生成一个？ 可以利用事件中心高度可缩放的发布-订阅功能灵活引入指标和资源日志。 请参阅 [Dan Rosanova 的指南：了解如何在全局规模的遥测平台中使用事件中心](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/)。

## <a name="stream-into-azure-storage"></a>流式传输到 Azure 存储中

使用 Azure 门户中内置的“存档到存储帐户”选项，可以在 Azure 存储中存储指标和资源日志。 此外，还可以通过 PowerShell cmdlet、Azure CLI 或 Azure Monitor REST API 使用诊断设置来启用存储。

### <a name="schema-of-metrics-and-resource-logs-in-the-storage-account"></a>存储帐户中指标和资源日志的架构

设置指标和资源日志收集后，当第一行数据可用时，将在所选的存储帐户中创建一个存储容器。 这些 Blob 的结构为：

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ databases/{database_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

或者使用更简单的形式：

```powershell
insights-{metrics|logs}-{category name}/resourceId=/{resource Id}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

例如，基本指标的 blob 名称可能是：

```powershell
insights-metrics-minute/resourceId=/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.SQL/ servers/Server1/databases/database1/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

如果存储弹性池中的数据，Blob 名称类似于：

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ elasticPools/{elastic_pool_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

## <a name="data-retention-policy-and-pricing"></a>数据保留策略和定价

如果选择事件中心或存储帐户，可以指定保留策略。 此策略删除早于选定时间段的数据。 如果指定 Log analytics，保留策略将取决于所选的定价层。 在这种情况下，提供的免费数据引入单位每月可免费监视多个数据库。 消耗的诊断遥测量超过免费单位可能会产生费用。

> [!IMPORTANT]
> 与空闲数据相比，工作负荷较重的活动数据库越多，引入的数据就越多。 有关详细信息，请参阅 [Log Analytics 定价](https://azure.microsoft.com/pricing/details/monitor/)。

如果使用 Azure SQL Analytics，则可以选择 Azure SQL Analytics 导航菜单上的“OMS 工作区”，然后选择“使用情况”和“预估成本”，来监视数据引入消耗量  。

## <a name="metrics-and-logs-available"></a>可用的指标和日志

本文的本部分介绍了如何监视适用于单一数据库、共用数据库、弹性池、托管实例和实例数据库的遥测数据。 可以使用 [Azure Monitor 日志查询](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries)语言将在 SQL Analytics 内收集的监视遥测数据用于你自己的自定义分析和应用程序开发。

### <a name="basic-metrics"></a>基本指标

请参阅下表来详细了解按资源列出的基本指标。

> [!NOTE]
> 基本指标选项以前称为“所有指标”。 所做的更改仅限于命名，并未更改所监视的指标。 启动此更改是为了允许将来引入更多的指标类别。

#### <a name="basic-metrics-for-elastic-pools"></a>弹性池的基本指标

|**资源**|**度量值**|
|---|---|
|弹性池|eDTU 百分比、已用 eDTU、eDTU 限制、CPU 百分比、物理数据读取百分比、日志写入百分比、会话百分比、辅助角色百分比、存储、存储百分比、存储限制、XTP存储百分比 |

#### <a name="basic-metrics-for-single-and-pooled-databases"></a>单一数据库和共用数据库的基本指标

|**资源**|**度量值**|
|---|---|
|单一数据库和共用数据库|DTU 百分比、已用 DTU、DTU 限制、CPU 百分比、物理数据读取百分比、日志写入百分比、成功/失败/防火墙阻止的连接数、会话百分比、辅助角色百分比、存储、存储百分比、XTP 存储百分比和死锁 |

### <a name="advanced-metrics"></a>高级指标

请参阅下表，详细了解高级指标。

|**指标**|**指标显示名称**|**说明**|
|---|---|---|
|sqlserver_process_core_percent<sup>1</sup>|SQL 进程核心百分比|SQL 进程的 CPU 使用率百分比（由操作系统度量）。|
|sqlserver_process_memory_percent<sup>1</sup> |SQL 进程内存百分比|SQL 进程的内存使用率百分比（由操作系统度量）。|
|tempdb_data_size<sup>2</sup>| Tempdb 数据文件大小 (KB) |Tempdb 数据文件大小 (KB)。|
|tempdb_log_size<sup>2</sup>| Tempdb 日志文件大小 (KB) |Tempdb 日志文件大小 (KB)。|
|tempdb_log_used_percent<sup>2</sup>| Tempdb 日志已用百分比 |Tempdb 日志已用百分比。|

<sup>1</sup> 此指标可用于这样的数据库：使用具有 2 vCore 或更多 vCore 的 vCore 购买模型，或使用具有 200 DTU 或更多 DTU 的基于 DTU 的购买模型。

<sup>2</sup> 此指标可用于这样的数据库：使用具有 2 vCore 或更多 vCore 的 vCore 购买模型，或使用具有 200 DTU 或更多 DTU 的基于 DTU 的购买模型。 此指标目前不可用于超大规模数据库或数据仓库。

### <a name="basic-logs"></a>基本日志

下面的表中记录了适用于所有日志的遥测数据的详细信息。 有关详细信息，请参阅[支持的诊断遥测](#diagnostic-telemetry-for-export)。

#### <a name="resource-usage-stats-for-managed-instances"></a>托管实例的资源使用情况统计信息

|属性|说明|
|---|---|
|TenantId|租户 ID |
|SourceSystem|始终：Azure|
|TimeGenerated [UTC]|记录日志时的时间戳 |
|类型|始终：AzureDiagnostics |
|ResourceProvider|资源提供程序的名称。 始终：MICROSOFT.SQL |
|类别|类别的名称。 始终：ResourceUsageStats |
|资源|资源名称 |
|ResourceType|资源类型的名称。 始终：MANAGEDINSTANCES |
|SubscriptionId|数据库的订阅 GUID |
|resourceGroup|数据库的资源组名称 |
|LogicalServerName_s|托管实例的名称 |
|ResourceId|资源 URI |
|SKU_s|SQL 托管实例产品 SKU |
|virtual_core_count_s|可用 vCore 的数目 |
|avg_cpu_percent_s|CPU 平均百分比 |
|reserved_storage_mb_s|托管实例上的保留存储容量 |
|storage_space_used_mb_s|托管实例上的已用存储 |
|io_requests_s|IOPS 计数 |
|io_bytes_read_s|已读取的 IOPS 字节数 |
|io_bytes_written_s|已写入的 IOPS 字节数 |

#### <a name="query-store-runtime-statistics"></a>查询数据存储运行时统计信息

|属性|说明|
|---|---|
|TenantId|租户 ID |
|SourceSystem|始终：Azure |
|TimeGenerated [UTC]|记录日志时的时间戳 |
|类型|始终：AzureDiagnostics |
|ResourceProvider|资源提供程序的名称。 始终：MICROSOFT.SQL |
|类别|类别的名称。 始终：QueryStoreRuntimeStatistics |
|OperationName|操作的名称。 始终：QueryStoreRuntimeStatisticsEvent |
|资源|资源名称 |
|ResourceType|资源类型的名称。 始终：SERVERS/DATABASES |
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

#### <a name="query-store-wait-statistics"></a>查询存储等待统计信息

|属性|说明|
|---|---|
|TenantId|租户 ID |
|SourceSystem|始终：Azure |
|TimeGenerated [UTC]|记录日志时的时间戳 |
|类型|始终：AzureDiagnostics |
|ResourceProvider|资源提供程序的名称。 始终：MICROSOFT.SQL |
|类别|类别的名称。 始终：QueryStoreWaitStatistics |
|OperationName|操作的名称。 始终：QueryStoreWaitStatisticsEvent |
|资源|资源名称 |
|ResourceType|资源类型的名称。 始终：SERVERS/DATABASES |
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

#### <a name="errors-dataset"></a>错误数据集

|属性|说明|
|---|---|
|TenantId|租户 ID |
|SourceSystem|始终：Azure |
|TimeGenerated [UTC]|记录日志时的时间戳 |
|类型|始终：AzureDiagnostics |
|ResourceProvider|资源提供程序的名称。 始终：MICROSOFT.SQL |
|类别|类别的名称。 始终：错误 |
|OperationName|操作的名称。 始终：ErrorEvent |
|资源|资源名称 |
|ResourceType|资源类型的名称。 始终：SERVERS/DATABASES |
|SubscriptionId|数据库的订阅 GUID |
|resourceGroup|数据库的资源组名称 |
|LogicalServerName_s|数据库的服务器名称 |
|ElasticPoolName_s|数据库的弹性池（如果有）名称 |
|DatabaseName_s|数据库的名称 |
|ResourceId|资源 URI |
|Message|纯文本格式的错误消息 |
|user_defined_b|是否是用户定义位错误 |
|error_number_d|错误代码 |
|severity|错误的严重性 |
|state_d|错误的状态 |
|query_hash_s|失败查询的查询哈希（如果有） |
|query_plan_hash_s|失败查询的查询计划哈希（如果有） |

详细了解 [SQL 错误消息](https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors?view=sql-server-ver15)。

#### <a name="database-wait-statistics-dataset"></a>数据库等待统计数据集

|属性|说明|
|---|---|
|TenantId|租户 ID |
|SourceSystem|始终：Azure |
|TimeGenerated [UTC]|记录日志时的时间戳 |
|类型|始终：AzureDiagnostics |
|ResourceProvider|资源提供程序的名称。 始终：MICROSOFT.SQL |
|类别|类别的名称。 始终：DatabaseWaitStatistics |
|OperationName|操作的名称。 始终：DatabaseWaitStatisticsEvent |
|资源|资源名称 |
|ResourceType|资源类型的名称。 始终：SERVERS/DATABASES |
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

#### <a name="time-outs-dataset"></a>超时数据集

|属性|说明|
|---|---|
|TenantId|租户 ID |
|SourceSystem|始终：Azure |
|TimeGenerated [UTC]|记录日志时的时间戳 |
|类型|始终：AzureDiagnostics |
|ResourceProvider|资源提供程序的名称。 始终：MICROSOFT.SQL |
|类别|类别的名称。 始终：超时 |
|OperationName|操作的名称。 始终：TimeoutEvent |
|资源|资源名称 |
|ResourceType|资源类型的名称。 始终：SERVERS/DATABASES |
|SubscriptionId|数据库的订阅 GUID |
|resourceGroup|数据库的资源组名称 |
|LogicalServerName_s|数据库的服务器名称 |
|ElasticPoolName_s|数据库的弹性池（如果有）名称 |
|DatabaseName_s|数据库的名称 |
|ResourceId|资源 URI |
|error_state_d|错误状态代码 |
|query_hash_s|查询哈希（如果有） |
|query_plan_hash_s|查询计划哈希（如果有） |

#### <a name="blockings-dataset"></a>阻塞数据集

|属性|说明|
|---|---|
|TenantId|租户 ID |
|SourceSystem|始终：Azure |
|TimeGenerated [UTC]|记录日志时的时间戳 |
|类型|始终：AzureDiagnostics |
|ResourceProvider|资源提供程序的名称。 始终：MICROSOFT.SQL |
|类别|类别的名称。 始终：块 |
|OperationName|操作的名称。 始终：BlockEvent |
|资源|资源名称 |
|ResourceType|资源类型的名称。 始终：SERVERS/DATABASES |
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

#### <a name="deadlocks-dataset"></a>死锁数据集

|属性|说明|
|---|---|
|TenantId|租户 ID |
|SourceSystem|始终：Azure |
|TimeGenerated [UTC] |记录日志时的时间戳 |
|类型|始终：AzureDiagnostics |
|ResourceProvider|资源提供程序的名称。 始终：MICROSOFT.SQL |
|类别|类别的名称。 始终：死锁数 |
|OperationName|操作的名称。 始终：DeadlockEvent |
|资源|资源名称 |
|ResourceType|资源类型的名称。 始终：SERVERS/DATABASES |
|SubscriptionId|数据库的订阅 GUID |
|resourceGroup|数据库的资源组名称 |
|LogicalServerName_s|数据库的服务器名称 |
|ElasticPoolName_s|数据库的弹性池（如果有）名称 |
|DatabaseName_s|数据库的名称 |
|ResourceId|资源 URI |
|deadlock_xml_s|死锁报告 XML |

#### <a name="automatic-tuning-dataset"></a>自动优化数据集

|属性|说明|
|---|---|
|TenantId|租户 ID |
|SourceSystem|始终：Azure |
|TimeGenerated [UTC]|记录日志时的时间戳 |
|类型|始终：AzureDiagnostics |
|ResourceProvider|资源提供程序的名称。 始终：MICROSOFT.SQL |
|类别|类别的名称。 始终：AutomaticTuning |
|资源|资源名称 |
|ResourceType|资源类型的名称。 始终：SERVERS/DATABASES |
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

#### <a name="intelligent-insights-dataset"></a>智能见解数据集

详细了解 [Intelligent Insights 日志格式](intelligent-insights-use-diagnostics-log.md)。

## <a name="next-steps"></a>后续步骤

若要了解如何启用日志记录并了解各种 Azure 服务支持的指标和日志类别，请参阅：

- [Microsoft Azure 中的指标概述](../../azure-monitor/platform/data-platform.md)
- [Azure 平台日志概述](../../azure-monitor/platform/platform-logs-overview.md)

若要了解事件中心，请阅读以下主题：

- [什么是 Azure 事件中心？](../../event-hubs/event-hubs-about.md)
- [事件中心入门](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)

若要了解如何基于 log analytics 中的遥测设置警报，请参阅：

- [为 Azure SQL 数据库和 Azure SQL 托管实例创建警报](../../azure-monitor/insights/azure-sql.md#analyze-data-and-create-alerts)
