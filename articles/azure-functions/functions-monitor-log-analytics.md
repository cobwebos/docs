---
title: 利用 Azure Monitor 日志监视 Azure Functions
description: 了解如何在 Azure Functions 中使用 Azure Monitor 日志来监视函数执行。
author: ahmedelnably
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: aelnably
ms.openlocfilehash: f4af646569edc8a9274af752e7e4f2a36585ae4d
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75769092"
---
# <a name="monitoring-azure-functions-with-azure-monitor-logs"></a>利用 Azure Monitor 日志监视 Azure Functions

Azure Functions 提供与[Azure Monitor 日志](../azure-monitor/platform/data-platform-logs.md)的集成以监视函数。 本文介绍如何配置 Azure Functions 以将系统生成的日志和用户生成的日志发送到 Azure Monitor 日志。

利用 Azure Monitor 日志，您可以从同一工作区中的不同资源合并日志，在这种情况下，可以通过[查询](../azure-monitor/log-query/log-query-overview.md)进行分析，以便快速检索、整合和分析收集的数据。  可以使用 Azure 门户中的 [Log Analytics](../azure-monitor/log-query/portals.md) 创建和测试查询，然后可以直接使用这些工具分析数据，或者保存查询以便与[可视化效果](../azure-monitor/visualizations.md)或[警报规则](../azure-monitor/platform/alerts-overview.md)配合使用。

Azure Monitor 使用 Azure 数据资源管理器使用的 [Kusto 查询语言](/azure/kusto/query/)的某个版本，该查询语言适用于简单的日志查询，但也包括高级功能，例如聚合、联接和智能分析。 可以通过[多个课程](../azure-monitor/log-query/get-started-queries.md)快速了解此查询语言。

> [!NOTE]
> 与 Azure Monitor 日志的集成当前以公共预览版的方式用于 Windows 消耗、高级和专用托管计划中运行的函数应用。

## <a name="setting-up"></a>设置

从 "**监视**" 部分，选择 "**诊断设置**"，然后单击 "**添加诊断设置**"。

![添加诊断设置](media/functions-monitor-log-analytics/diagnostic-settings-add.png)

在 "**诊断设置**" 页上，选择 "**发送到 Log Analytics**"，然后选择 Log Analytics 工作区。 在 "**日志**" 下，选择 " **FunctionAppLogs**"，此表包含所需的日志。

![添加诊断设置](media/functions-monitor-log-analytics/choose-table.png)

## <a name="user-generated-logs"></a>用户生成的日志

若要生成自定义日志，可以使用特定的日志记录语句，具体取决于你的语言，下面是示例代码片段：


# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
log.LogInformation("My app logs here.");
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
context.getLogger().info("My app logs here.");
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
context.log('My app logs here.');
```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

```powershell
Write-Host "My app logs here."
```

# <a name="pythontabpython"></a>[Python](#tab/python)

```python
logging.info('My app logs here.')
```

---

## <a name="querying-the-logs"></a>查询日志

若要查询生成的日志，请参阅配置为将函数日志发送到的 Log Analytics 工作区，并单击 "**日志**"。

![LA 工作区中的查询窗口](media/functions-monitor-log-analytics/querying.png)

Azure Functions 将所有日志写入**FunctionAppLogs**表，以下是一些示例查询。

### <a name="all-logs"></a>所有日志

```

FunctionAppLogs
| order by TimeGenerated desc

```

### <a name="a-specific-function-logs"></a>特定函数日志

```

FunctionAppLogs
| where FunctionName == "<Function name>" 

```

### <a name="exceptions"></a>异常

```

FunctionAppLogs
| where ExceptionDetails != ""  
| order by TimeGenerated asc

```

## <a name="next-steps"></a>后续步骤

- 查看[Azure Functions 概述](functions-overview.md)
- 详细了解[Azure Monitor 日志](../azure-monitor/platform/data-platform-logs.md)
- 详细了解[查询语言](../azure-monitor/log-query/get-started-queries.md)。
