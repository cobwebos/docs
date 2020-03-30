---
title: 使用 Azure 监视日志监视 Azure 函数
description: 了解如何将 Azure 监视器日志与 Azure 函数一起监视函数执行。
author: craigshoemaker
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: cshoe
ms.openlocfilehash: 13c72a1cf8a0dd4a1124e51b9ceee04ae04bf261
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77649868"
---
# <a name="monitoring-azure-functions-with-azure-monitor-logs"></a>使用 Azure 监视日志监视 Azure 函数

Azure 函数提供与[Azure 监视器日志](../azure-monitor/platform/data-platform-logs.md)的集成，以监视函数。 本文介绍如何配置 Azure 函数，将系统生成的和用户生成的日志发送到 Azure 监视器日志。

Azure 监视器日志使您能够在同一工作区中合并来自不同资源的日志，通过[查询](../azure-monitor/log-query/log-query-overview.md)对其进行分析，以快速检索、合并和分析收集的数据。  可以使用 Azure 门户中的 [Log Analytics](../azure-monitor/log-query/portals.md) 创建和测试查询，然后可以直接使用这些工具分析数据，或者保存查询以便与[可视化效果](../azure-monitor/visualizations.md)或[警报规则](../azure-monitor/platform/alerts-overview.md)配合使用。

Azure Monitor 使用 Azure 数据资源管理器使用的 [Kusto 查询语言](/azure/kusto/query/)的某个版本，该查询语言适用于简单的日志查询，但也包括高级功能，例如聚合、联接和智能分析。 可以通过[多个课程](../azure-monitor/log-query/get-started-queries.md)快速了解此查询语言。

> [!NOTE]
> 与 Azure 监视器日志的集成当前处于在 Windows 消费、高级和专用托管计划上运行的功能应用的公共预览版中。

## <a name="setting-up"></a>设置

在 **"监视**"部分中，选择 **"诊断设置**"，然后单击"**添加诊断设置**"。

![添加诊断设置](media/functions-monitor-log-analytics/diagnostic-settings-add.png)

在 **"诊断设置"** 页中，选择 **"发送到日志分析**"，然后选择日志分析工作区。 在**日志**下选择**函数AppLogs，** 此表包含所需的日志。

![添加诊断设置](media/functions-monitor-log-analytics/choose-table.png)

## <a name="user-generated-logs"></a>用户生成的日志

要生成自定义日志，可以使用特定的日志记录语句，具体取决于您的语言，下面是示例代码段：


# <a name="c"></a>[C#](#tab/csharp)

```csharp
log.LogInformation("My app logs here.");
```

# <a name="java"></a>[Java](#tab/java)

```java
context.getLogger().info("My app logs here.");
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
context.log('My app logs here.');
```

# <a name="powershell"></a>[电源外壳](#tab/powershell)

```powershell
Write-Host "My app logs here."
```

# <a name="python"></a>[Python](#tab/python)

```python
logging.info('My app logs here.')
```

---

## <a name="querying-the-logs"></a>查询日志

要查询生成的日志，请转到配置为将函数日志发送到的日志分析工作区，然后单击 **"日志**"。

![洛杉矶工作区中的查询窗口](media/functions-monitor-log-analytics/querying.png)

Azure 函数将所有日志写入**函数AppLogs**表，下面是一些示例查询。

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

- 查看[Azure 函数概述](functions-overview.md)
- 了解有关[Azure 监视器日志的更多信息](../azure-monitor/platform/data-platform-logs.md)
- 详细了解[查询语言](../azure-monitor/log-query/get-started-queries.md)。
