---
title: Monitoring Azure Functions with Azure Monitor Logs
description: Learn how to use Azure Monitor Logs with Azure Functions to monitor function executions.
author: ahmedelnably
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: aelnably
ms.openlocfilehash: 9aac6662304395b1bce5dfc21770d296f6a4f2ab
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74226849"
---
# <a name="monitoring-azure-functions-with-azure-monitor-logs"></a>Monitoring Azure Functions with Azure Monitor Logs

Azure Functions offers an integration with [Azure Monitor Logs](../azure-monitor/platform/data-platform-logs.md) to monitor functions. This article shows you how to configure Azure Functions to send system-generated and user-generated logs to Azure Monitor Logs.

Azure Monitor Logs gives you the ability to consolidate logs from different resources in the same workspace, where it can be analyzed with [queries](../azure-monitor/log-query/log-query-overview.md) to quickly retrieve, consolidate, and analyze collected data.  可以使用 Azure 门户中的 [Log Analytics](../azure-monitor/log-query/portals.md) 创建和测试查询，然后可以直接使用这些工具分析数据，或者保存查询以便与[可视化效果](../azure-monitor/visualizations.md)或[警报规则](../azure-monitor/platform/alerts-overview.md)配合使用。

Azure Monitor 使用 Azure 数据资源管理器使用的 [Kusto 查询语言](/azure/kusto/query/)的某个版本，该查询语言适用于简单的日志查询，但也包括高级功能，例如聚合、联接和智能分析。 可以通过[多个课程](../azure-monitor/log-query/get-started-queries.md)快速了解此查询语言。

> [!NOTE]
> Integration with Azure Monitor Logs is currently in public preview for function apps running on Windows Consumption, Premium, and Dedicated hosting plans.

## <a name="setting-up"></a>设置

From the Monitoring section, select **Diagnostic settings** and then click **Add**.

![Add a diagnostic setting](media/functions-monitor-log-analytics/diagnostic-settings-add.png)

In the setting page, choose **Send to Log Analytics**, and under **LOG** choose **FunctionAppLogs**, this table contains the desired logs.

![Add a diagnostic setting](media/functions-monitor-log-analytics/choose-table.png)

## <a name="user-generated-logs"></a>User generated logs

To generate custom logs, you can use the specific logging statement depending on your language, here are sample code snippets:

**JavaScript**

```javascript
    context.log('My app logs here.');
```

**Python**

```python
    logging.info('My app logs here.')
```

**.NET**

```csharp
    log.LogInformation("My app logs here.");
```

**Java**

```java
    context.getLogger().info("My app logs here.");
```

**PowerShell**

```powershell
    Write-Host "My app logs here."
```

## <a name="querying-the-logs"></a>Querying the logs

To query the generated logs, go to the log analytics workspace and click **Logs**.

![Query window in LA workspace](media/functions-monitor-log-analytics/querying.png)

Azure Functions writes all logs to **FunctionAppLogs** table, here are some sample queries.

### <a name="all-logs"></a>All logs

```

FunctionAppLogs
| order by TimeGenerated desc

```

### <a name="a-specific-function-logs"></a>A specific function logs

```

FunctionAppLogs
| where FunctionName == "<Function name>" 

```

### <a name="exceptions"></a>例外

```

FunctionAppLogs
| where ExceptionDetails != ""  
| order by TimeGenerated asc

```

## <a name="next-steps"></a>后续步骤

- Review the [Azure Functions overview](functions-overview.md)
- Learn more about [Azure Monitor Logs](../azure-monitor/platform/data-platform-logs.md)
- 详细了解[查询语言](../azure-monitor/log-query/get-started-queries.md)。
