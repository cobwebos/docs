---
title: Log Analytics 中的 Azure SQL Analytics 解决方案 | Microsoft 文档
description: Azure SQL Analytics 解决方案可帮助用户管理 Azure SQL 数据库。
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: b2712749-1ded-40c4-b211-abc51cc65171
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/03/2018
ms.author: magoedte
ms.openlocfilehash: 722a10e853f6d61bb5349e92754954e3bb199225
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
ms.locfileid: "33775071"
---
# <a name="monitor-azure-sql-database-using-azure-sql-analytics-preview-in-log-analytics"></a>在 Log Analytics 中使用 Azure SQL Analytics（预览版）监视 Azure SQL 数据库

![Azure SQL Analytics 符号](./media/log-analytics-azure-sql/azure-sql-symbol.png)

Azure Log Analytics 中的 Azure SQL Analytics 解决方案收集和可视化重要的 Azure SQL 性能指标。 使用解决方案收集指标后，即可利用这些指标创建自定义监视规则和警报。 此外，还可以跨多个 Azure 订阅和弹性池监视 Azure SQL 数据库和弹性池指标并将其可视化。 该解决方案还可以帮助用户确定应用程序堆栈的每个层的问题。  它使用 [Azure 诊断指标](log-analytics-azure-storage.md)和 Log Analytics 视图，在单个 Log Analytics 工作区中呈现有关所有 Azure SQL 数据库和弹性池的数据。

此预览版解决方案目前支持每个工作区使用多达 150,000 个 Azure SQL 数据库和 5,000 个 SQL 弹性池。

与其他适用于 Log Analytics 的解决方案一样，Azure SQL Analytics 解决方案可以帮助用户监视和接收有关 Azure 资源（在此示例中为 Azure SQL 数据库）运行状况的通知。 Microsoft Azure SQL 数据库是可缩放的关系数据库服务，为运行在 Azure 云中的应用程序提供熟悉的类似于 SQL Server 的功能。 Log Analytics 可帮助用户收集、关联和可视化结构化和非结构化数据。

有关使用 Azure SQL Analytics 解决方案的实践概述和典型使用方案，请观看嵌入视频：

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Get-Intelligent-Insights-for-Improving-Azure-SQL-Database-Performance/player]
>

## <a name="connected-sources"></a>连接的源

Azure SQL Analytics 解决方案不使用代理连接 Log Analytics 服务。

下表介绍了该解决方案支持的连接的源。

| 连接的源 | 支持 | 说明 |
| --- | --- | --- |
| [Windows 代理](log-analytics-windows-agent.md) | 否 | 该解决方案不使用直接 Windows 代理。 |
| [Linux 代理](log-analytics-linux-agents.md) | 否 | 该解决方案不使用直接 Linux 代理。 |
| [SCOM 管理组](log-analytics-om-agents.md) | 否 | 该解决方案不使用从 SCOM 代理到 Log Analytics 的直接连接。 |
| [Azure 存储帐户](log-analytics-azure-storage.md) | 否 | Log Analytics 不会从存储帐户中读取数据。 |
| [Azure 诊断](log-analytics-azure-storage.md) | 是 | Azure 指标和日志数据由 Azure 直接发送到 Log Analytics。 |

## <a name="prerequisites"></a>先决条件

- 一个 Azure 订阅。 如果没有该订阅，可以[免费](https://azure.microsoft.com/free/)创建一个。
- Log Analytics 工作区。 在开始使用此解决方案之前，用户可以使用现有的工作区，也可以[创建一个新的](log-analytics-quick-create-workspace.md)。
- 为 Azure SQL 数据库和弹性池启用 Azure 诊断，并[将其配置为发送数据到 Log Analytics](../sql-database/sql-database-metrics-diag-logging.md)。

## <a name="configuration"></a>配置

执行以下步骤，将 Azure SQL Analytics 解决方案添加到你的工作区。

1. 从 [Azure 应用商店](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.AzureSQLAnalyticsOMS?tab=Overview)或使用[从解决方案库中添加 Log Analytics 解决方案](log-analytics-add-solutions.md)中所述的过程，将 Azure SQL Analytics 解决方案添加到工作区。
2. 在 Azure 门户中，单击“创建资源” > “监视 + 管理”。  
    ![监视 + 管理](./media/log-analytics-azure-sql/monitoring-management.png)
3. 在“监视 + 管理”列表中，单击“全部查看”。
4. 在“推荐”列表中，单击“更多”，并在新列表中找到并选中“Azure SQL Analytics (预览版)”。  
    ![Azure SQL Analytics 解决方案](./media/log-analytics-azure-sql/azure-sql-solution-portal.png)
5. 在“Azure SQL Analytics (预览版)”区域中，单击“创建”。  
    ![创建](./media/log-analytics-azure-sql/portal-create.png)
6. 在“创建新的解决方案”区域中，选择要向其添加解决方案的工作区，并单击“创建”。  
    ![添加到工作区](./media/log-analytics-azure-sql/add-to-workspace.png)


### <a name="to-configure-multiple-azure-subscriptions"></a>配置多个 Azure 订阅

若要支持多个订阅，请使用 [Enable Azure resource metrics logging using PowerShell](https://blogs.technet.microsoft.com/msoms/2017/01/17/enable-azure-resource-metrics-logging-using-powershell/)（通过 PowerShell 启用 Azure 资源指标日志记录）中的 PowerShell 脚本。 在执行脚本时提供工作区资源 ID 作为参数，以便将诊断数据从一个 Azure 订阅中的资源发送到另一 Azure 订阅中的工作区。

**示例**

```
PS C:\> $WSID = "/subscriptions/<subID>/resourcegroups/oms/providers/microsoft.operationalinsights/workspaces/omsws"
```

```
PS C:\> .\Enable-AzureRMDiagnostics.ps1 -WSID $WSID
```

## <a name="using-the-solution"></a>使用解决方案

将解决方案添加到工作区时，“Azure SQL Analytics”磁贴也会添加到工作区并显示在“概览”中。 该磁贴显示解决方案连接到的 Azure SQL 数据库和 Azure SQL 弹性池的数目。

![“Azure SQL Analytics”磁贴](./media/log-analytics-azure-sql/azure-sql-sol-tile.png)

### <a name="viewing-azure-sql-analytics-data"></a>查看 Azure SQL Analytics 数据

单击“Azure SQL Analytics”磁贴打开“Azure SQL Analytics”仪表板。 该仪表板包含通过不同透视监视的所有数据库的概览。 要使不同透视起效，必须允许将有关 SQL 资源的适当指标或日志流式传输到 Azure Log Analytics 工作区。

![Azure SQL Analytics 概述](./media/log-analytics-azure-sql/azure-sql-sol-overview.png)

选择任意磁贴，打开特定透视的向下钻取报告。 选择透视后，可看到向下钻取报表。

![Azure SQL Analytics 超时](./media/log-analytics-azure-sql/azure-sql-sol-metrics.png)

每个透视都提供了有关订阅、服务器、弹性池和数据库级别的概述。 此外，每个透视在右侧都显示了特定于透视的报表。 从列表选择订阅、服务器、池或数据库会继续向下钻取。

| 透视 | 说明 |
| --- | --- |
| 资源（按类型） | 对监视的所有资源进行计数的透视。 向下钻取会提供 DTU 和 GB 指标的摘要。 |
| 洞察力 | 提供对智能见解的分层向下钻取。 详细了解智能见解。 |
| Errors | 提供对数据库上发生的 SQL 错误的分层向下钻取。 |
| 超时 | 提供对数据库上发生的 SQL 超时的分层向下钻取。 |
| 阻止 | 提供对数据库上发生的 SQL 阻止的分层向下钻取。 |
| 数据库等待 | 提供对数据库级别上的 SQL 等待统计信息的分层向下钻取。 包含总等待时间汇总和每个类型的等待时间。 |
| 查询持续时间 | 提供对查询执行统计信息的分层向下钻取，例如查询持续时间、CPU 使用情况、数据 IO 使用情况和日志 IO 使用情况。 |
| 查询等待 | 按等待类型提供对查询等待统计信息的分层向下钻取。 |

### <a name="intelligent-insights-report"></a>智能见解报告

使用 Azure SQL 数据库 [Intelligent Insights](../sql-database/sql-database-intelligent-insights.md) 可以知道数据库发生了什么情况。 可通过见解透视，对收集的所有智能见解进行可视化和访问。

![Azure SQL Analytics 见解](./media/log-analytics-azure-sql/azure-sql-sol-insights.png)

### <a name="elastic-pool-and-database-reports"></a>弹性池和数据库报表

弹性池和数据库都有其特定的报表，这些报表显示特定时间内对该资源收集的所有数据。

![Azure SQL Analytics 数据库](./media/log-analytics-azure-sql/azure-sql-sol-database.png)

![Azure SQL Analytics 弹性池](./media/log-analytics-azure-sql/azure-sql-sol-pool.png)

### <a name="query-reports"></a>查询报表

通过查询持续时间和查询等待透视，可使用查询报表关联任何查询的性能。 此报表比较不同数据库之间的查询性能，从而可轻松查找良好执行所选查询的数据库以及查询缓慢的数据库。

![Azure SQL Analytics 查询](./media/log-analytics-azure-sql/azure-sql-sol-queries.png)

### <a name="analyze-data-and-create-alerts"></a>分析数据和创建警报

可以使用来自 Azure SQL 数据库资源的数据轻松[创建警报](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md)。 以下是部分有用的可用于日志警报的[日志搜索](log-analytics-log-searches.md)查询：



*Azure SQL 数据库上的高 DTU*

```
AzureMetrics 
| where ResourceProvider=="MICROSOFT.SQL" and ResourceId contains "/DATABASES/" and MetricName=="dtu_consumption_percent" 
| summarize AggregatedValue = max(Maximum) by bin(TimeGenerated, 5m)
| render timechart
```

*Azure SQL 数据库弹性池上的高 DTU*

```
AzureMetrics 
| where ResourceProvider=="MICROSOFT.SQL" and ResourceId contains "/ELASTICPOOLS/" and MetricName=="dtu_consumption_percent" 
| summarize AggregatedValue = max(Maximum) by bin(TimeGenerated, 5m)
| render timechart
```



## <a name="next-steps"></a>后续步骤

- 使用 Log Analytics 中的[日志搜索](log-analytics-log-searches.md)查看 Azure SQL 的详细数据。
- [创建自己的仪表板](log-analytics-dashboards.md)，显示 Azure SQL 数据。
- 发生特定 Azure SQL 事件时[创建警报](log-analytics-alerts.md)。
