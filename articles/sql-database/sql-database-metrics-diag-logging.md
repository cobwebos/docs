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
ms.date: 05/24/2017
ms.author: vvasic
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: bf41aa530c68ea0e94a09d1dab63237c6f42bce7
ms.contentlocale: zh-cn
ms.lasthandoff: 08/22/2017

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

如果指定事件中心或 Azure 存储帐户，则可通过指定保留策略来指定删除早于选定时间段的数据。 如果指定 Log analytics，保留策略将取决于所选的定价层。 阅读有关 [Log Analytics 定价](https://azure.microsoft.com/pricing/details/log-analytics/)的详细信息。 

建议阅读文章 [Microsoft Azure 指标概况](../monitoring-and-diagnostics/monitoring-overview-metrics.md)和 [Azure 诊断日志概述](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)，了解如何启用日志记录，以及各种 Azure 服务支持的指标和日志类别。

### <a name="azure-portal"></a>Azure 门户

若要在 Azure 门户中启用指标和诊断日志集合，请导航至 Azure SQL 数据库或弹性池页，然后单击“诊断设置”。

   ![在 Azure 门户中启用](./media/sql-database-metrics-diag-logging/enable-portal.png)

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

### <a name="cli"></a>CLI

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

1.  创建 Log Analytics 资源
2.  将数据库配置为将指标和诊断日志记录到创建的 Log Analytics 中
3.  在 Log Analytics 中安装库中的 Azure SQL Analytics 解决方案

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

Azure SQL Analytics 是一个分层仪表板，可在 Azure SQL 数据库资源的层次结构中导航。 通过此功能可进行高级监视，也可将监视范围限定为右边的资源集。
仪表板包含所选资源下不同资源的列表。 例如，对于所选订阅，你可看到所有属于所选订阅的服务器、弹性池和数据库。 此外，对于弹性池和数据库，你可查看该资源的资源使用情况指标。 这包括 DTU、CPU、IO、日志、会话、辅助角色、连接和存储（以 GB 为单位）的图表。

## <a name="stream-into-azure-event-hub"></a>流式传输到 Azure 事件中心内

通过使用门户中内置的“流式传输到事件中心”选项，也可通过 Azure PowerShell Cmdlet、Azure CLI 或 Azure Monitor REST API 在诊断设置中启用服务总线规则 ID，将 Azure SQL 数据库指标和诊断日志流式传输到事件中心内。 

### <a name="what-to-do-with-metrics-and-diagnostic-logs-in-event-hub"></a>如何处理事件中心内的指标和诊断日志？
一旦将选定的数据流式传输到事件中心内，就可更进一步启动高级监视方案。 事件中心充当事件管道“前门”，将数据收集到事件中心后，可以使用任何实时分析提供程序或批处理/存储适配器来转换和存储这些数据。 事件中心可将事件流的生成与这些事件的使用分离开来，因此，事件使用者可以根据自己的计划访问事件。 有关事件中心的详细信息，请参阅：

- [什么是 Azure 事件中心](../event-hubs/event-hubs-what-is-event-hubs.md)？
- [事件中心入门](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)


可以通过下述几种方式使用流式传输功能：

-   通过将“热路径”数据流式传输到 PowerBI 来查看服务运行状况 - 可通过事件中心、流分析和 PowerBI 在 Azure 服务中轻松地将指标和诊断数据转换成实时分析结果。 有关如何设置事件中心、如何使用流分析处理数据，以及如何使用 PowerBI 作为输出的概述，请参阅[流分析和 Power BI](../stream-analytics/stream-analytics-power-bi-dashboard.md)。
-   将日志流式传输到第三方日志记录和遥测流 - 使用事件中心流式传输，可将指标和诊断日志放入不同的第三方监视和 Log Analytics 解决方案。 
-   生成自定义遥测和日志记录平台 - 如果已经有一个自定义生成的遥测平台，或者正想生成一个，则可利用事件中心高度可缩放的发布-订阅功能，灵活地引入诊断日志。 请参阅 [Dan Rosanova 的指南，了解如何在全局规模的遥测平台中使用事件中心](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/)。

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

## <a name="1-minute-metrics"></a>1 分钟指标

| |  |
|---|---|
|**资源**|**指标**|
|数据库|DTU 百分比、已用 DTU、 DTU 限制、CPU 百分比、物理数据读取百分比、日志写入百分比、成功/失败/防火墙阻止的连接数、会话百分比、辅助角色百分比、存储、存储百分比、XTP 存储百分比、死锁 |
|弹性池|eDTU 百分比、已用 eDTU、eDTU 限制、CPU 百分比、物理数据读取百分比、日志写入百分比、会话百分比、辅助角色百分比、存储、存储百分比、存储限制、XTP存储百分比 |
|||

## <a name="next-steps"></a>后续步骤

- 阅读文章 [Microsoft Azure 中的指标概述](../monitoring-and-diagnostics/monitoring-overview-metrics.md)和 [Azure 诊断日志概述](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)了解如何启用日志记录，以及各种 Azure 服务支持的指标和日志类别。
- 阅读以下文章，了解事件中心：
   - [什么是 Azure 事件中心](../event-hubs/event-hubs-what-is-event-hubs.md)？
   - [事件中心入门](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
- 请参阅[从 Azure 存储下载指标和诊断日志](../storage/blobs/storage-dotnet-how-to-use-blobs.md#download-blobs)

