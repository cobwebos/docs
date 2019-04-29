---
title: 监视工作负荷 - Azure 门户 | Microsoft Docs
description: 使用 Azure 门户监视 Azure SQL 数据仓库
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 03/22/2019
ms.author: kevin
ms.reviewer: jrasnick
ms.openlocfilehash: 6c8ce090039e3d5cc85c86d920710294de2165f9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60748803"
---
# <a name="monitor-workload---azure-portal"></a>监视工作负荷 - Azure 门户

本文介绍如何使用 Azure 门户监视工作负荷。 这包括设置 Azure Monitor 日志，以使用适用于 [Azure SQL 数据仓库](https://azure.microsoft.com/blog/workload-insights-with-sql-data-warehouse-delivered-through-azure-monitor-diagnostic-logs-pass/)的日志分析来调查查询执行和工作负荷趋势。

## <a name="prerequisites"></a>必备组件

- Azure 订阅：如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。
- Azure SQL 数据仓库：我们将收集 SQL 数据仓库的日志。 如果未预配 SQL 数据仓库，请参阅[创建 SQL 数据仓库](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-tutorial)中的说明。

## <a name="create-a-log-analytics-workspace"></a>创建 Log Analytics 工作区

导航到 Log Analytics 工作区的浏览边栏选项卡并创建工作区 

![Log Analytics 工作区](media/sql-data-warehouse-monitor/log_analytics_workspaces.png)

![添加 Analytics 工作区](media/sql-data-warehouse-monitor/add_analytics_workspace.png)

![添加 Analytics 工作区](media/sql-data-warehouse-monitor/add_analytics_workspace_2.png)

有关工作区的更多详细信息，请访问以下[文档](https://docs.microsoft.com/azure/azure-monitor/platform/manage-access#create-a-workspace)。

## <a name="turn-on-diagnostic-logs"></a>启用诊断日志 

配置诊断设置，以便从 SQL 数据仓库发出日志。 日志由数据仓库的遥测视图组成，相当于 SQL 数据仓库最常用的性能故障排除 DMV。 目前支持以下视图：

- [sys.dm_pdw_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_request_steps](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_dms_workers](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_waits](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_sql_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?view=aps-pdw-2016-au7)


![启用诊断日志](media/sql-data-warehouse-monitor/enable_diagnostic_logs.png)

可将日志发送到 Azure 存储、流分析或 Log Analytics。 本教程选择了“Log Analytics”。

![指定日志](media/sql-data-warehouse-monitor/specify_logs.png)

## <a name="run-queries-against-log-analytics"></a>针对 Log Analytics 运行查询

导航到 Log Analytics 工作区，在其中可执行以下操作：

- 使用日志查询分析日志，并保存查询以便重复使用
- 保存查询以便重复使用
- 创建日志警报
- 将查询结果固定到仪表板

有关日志查询功能的详细信息，请访问以下[文档](https://docs.microsoft.com/azure/azure-monitor/log-query/query-language)。

![Log Analytics 工作区编辑器](media/sql-data-warehouse-monitor/log_analytics_workspace_editor.png)



![Log Analytics 工作区查询](media/sql-data-warehouse-monitor/log_analytics_workspace_queries.png)

## <a name="sample-log-queries"></a>示例日志查询



```Kusto
//List all queries 
AzureDiagnostics
| where Category contains "ExecRequests"
| project TimeGenerated, StartTime_t, EndTime_t, Status_s, Command_s, ResourceClass_s, duration=datetime_diff('millisecond',EndTime_t, StartTime_t)
```
```Kusto
//Chart the most active resource classes
AzureDiagnostics
| where Category contains "ExecRequests"
| where Status_s == "Completed"
| summarize totalQueries = dcount(RequestId_s) by ResourceClass_s
| render barchart 
```
```Kusto
//Count of all queued queries
AzureDiagnostics
| where Category contains "waits" 
| where Type_s == "UserConcurrencyResourceType"
| summarize totalQueuedQueries = dcount(RequestId_s)
```
## <a name="next-steps"></a>后续步骤

设置并配置 Azure Monitor 日志后，请[自定义 Azure 仪表板](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards)以便在整个团队中共享。
