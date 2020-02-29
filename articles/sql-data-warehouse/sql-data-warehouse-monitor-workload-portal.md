---
title: 监视工作负荷-Azure 门户
description: 使用 Azure 门户监视 SQL Analytics
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 02/04/2020
ms.author: kevin
ms.reviewer: jrasnick
ms.openlocfilehash: 7e93aee405d8a66d850a4e3f07f2e788f1004ef8
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/29/2020
ms.locfileid: "78197213"
---
# <a name="monitor-workload---azure-portal"></a>监视工作负荷-Azure 门户

本文介绍如何使用 Azure 门户来监视工作负荷。 这包括设置 Azure Monitor 日志，以使用 log analytics for [SQL analytics](https://azure.microsoft.com/blog/workload-insights-with-sql-data-warehouse-delivered-through-azure-monitor-diagnostic-logs-pass/)来调查查询执行和工作负荷趋势。

## <a name="prerequisites"></a>必备条件

- Azure 订阅：如果没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/)。
- SQL 池：我们将收集 SQL 池的日志。 如果尚未预配 SQL 池，请参阅[创建 sql 池](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-tutorial)中的说明。

## <a name="create-a-log-analytics-workspace"></a>创建 Log Analytics 工作区

导航到 Log Analytics 工作区的浏览边栏选项卡并创建工作区 

![Log Analytics 工作区](media/sql-data-warehouse-monitor/log_analytics_workspaces.png)

![添加分析工作区](media/sql-data-warehouse-monitor/add_analytics_workspace.png)

![添加分析工作区](media/sql-data-warehouse-monitor/add_analytics_workspace_2.png)

有关工作区的更多详细信息，请访问以下[文档](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace#create-a-workspace)。

## <a name="turn-on-diagnostic-logs"></a>启用诊断日志

配置诊断设置以从 SQL 池发出日志。 日志由与最常用的性能故障排除 Dmv 有关的遥测视图组成。 目前支持以下视图：

- [sys.dm_pdw_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_request_steps](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_dms_workers](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_waits](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_sql_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?view=aps-pdw-2016-au7)


![启用诊断日志](media/sql-data-warehouse-monitor/enable_diagnostic_logs.png)

可以将日志发送到 Azure 存储、流分析或 Log Analytics。 对于本教程，请选择 "Log Analytics"。

![指定日志](media/sql-data-warehouse-monitor/specify_logs.png)

## <a name="run-queries-against-log-analytics"></a>对 Log Analytics 运行查询

导航到 Log Analytics 工作区，可在其中执行以下操作：

- 使用日志查询分析日志并保存查询以供重用
- 保存查询以供重用
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

现在，你已设置并配置了 Azure 监视器日志，接下来可以[自定义 azure 仪表板](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards)以在你的团队间共享。
