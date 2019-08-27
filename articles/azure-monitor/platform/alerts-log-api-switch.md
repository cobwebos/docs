---
title: 从旧 Log Analytics 警报 API 切换到新 Azure 警报 API
description: 基于旧的基于 savedSearch Log Analytics 警报 API 和处理将警报规则切换到新 ScheduledQueryRules API 的过程的概述, 并提供了解决常见客户问题的详细信息。
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: vinagara
ms.subservice: alerts
ms.openlocfilehash: 8f23d19b06152b633df6688489753498c86aee27
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/26/2019
ms.locfileid: "70034785"
---
# <a name="switch-api-preference-for-log-alerts"></a>切换日志警报的 API 首选项

> [!NOTE]
> 所述内容适用于仅适用于 azure 公有云的用户,**不适**用于 azure 政府版或 azure 中国云。  

直到最近都是在 Microsoft Operations Management Suite 门户中管理警报规则。 新警报体验与 Microsoft Azure 中的各种服务集成（包括 Log Analytics），我们要求[将警报规则从 OMS 门户扩展到 Azure](alerts-extend.md)。 但是为了确保针对客户尽量减少中断，该过程未更改供其使用的编程接口（基于 SavedSearch 的 [Log Analytics 警报 API](api-alerts.md)）。

但是现在宣布面向 Log Analytics 警报用户推出了真正的 Azure 编程替代方式，即 [Azure Monitor - ScheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules)，这也在 [Azure 计费 - 用于日志警报](alerts-unified-log.md#pricing-and-billing-of-log-alerts)中得到了反映。 若要详细了解如何使用 API 管理日志警报, 请参阅使用[Azure 资源模板管理日志警报](alerts-log.md#managing-log-alerts-using-azure-resource-template)和[使用 PowerShell 管理日志警报](alerts-log.md#managing-log-alerts-using-powershell)。

## <a name="benefits-of-switching-to-new-azure-api"></a>切换到新 Azure API 的好处

与[旧 Log Analytics 警报 API](api-alerts.md) 相比，使用 [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) 创建和管理警报具有多个优势；我们在下面列出了一些主要优势：

- 能够在警报规则中[跨工作区进行日志搜索](../log-query/cross-workspace-query.md)并跨越外部资源（如 Log Analytics 工作区，甚至是 Application Insights 应用）
- 当多个字段过去经常在查询中进行分组时，用户使用 [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) 可以指定要在 Azure 门户中聚合的字段
- 使用 [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) 创建的日志警报可以定义最长 48 小时的时段，并获取比以前更长的时段内的数据
- 在一个操作中将警报规则创建为单个资源，无需与[旧 Log Analytics 警报 API](api-alerts.md) 一样创建三个级别的资源
- 单个程序接口用于 Azure 中基于查询的日志警报的所有变体 - 新 [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) 可以用于为 Log Analytics 以及 Application Insights 管理规则
- 使用 [Powershell cmdlet](alerts-log.md#managing-log-alerts-using-powershell) 管理日志警报
- 所有新日志警报功能和未来开发都只能通过新 [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) 使用

## <a name="process-of-switching-from-legacy-log-alerts-api"></a>从旧日志警报 API 进行切换的过程

用户可以自由使用[旧 Log Analytics 警报 API](api-alerts.md) 或新 [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules)。 通过任一 API 创建的警报规则都*只能由同一 API 进行管理* - 以及从 Azure 门户进行管理。 默认情况下，Azure Monitor 将继续使用[旧版 Log Analytics 警报 API](api-alerts.md) 从 Azure 门户为 Log Analytics 的现有工作区创建任何新警报规则。 随着[宣布在 2019 年 6 月 1 日或之后创建新的日志工作区](https://azure.microsoft.com/updates/switch-api-preference-log-alerts/)，默认情况下将自动使用新的 [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules)，包括在 Azure 门户中。

下面汇总了首选项切换为 scheduledQueryRules API 的影响：

- 通过编程接口为管理日志警报而进行的所有交互现在都必须改用 [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) 完成。 有关详细信息，请参阅[通过 Azure 资源模板的示例用法](alerts-log.md#managing-log-alerts-using-azure-resource-template)和[通过 PowerShell 的示例用法](alerts-log.md#managing-log-alerts-using-powershell)
- 在 Azure 门户中创建的任何新日志警报规则都会仅使用 [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) 创建，还允许用户通过 Azure 门户使用[新 API 的其他功能](#benefits-of-switching-to-new-azure-api)
- 日志警报规则的严重性将从*严重、警告和信息*转换为*严重性值 0、1 和 2*。 以及用于创建/更新严重性为3和4的警报规则的选项。

从[旧 Log Analytics 警报 API](api-alerts.md) 移动警报规则的过程不涉及以任何方式更改警报定义、查询或配置。 你的警报规则和监视不受影响，警报不会在切换期间或之后停止或停滞。 惟一的更改是 API 首选项的更改，以及通过新的 API 访问规则。

> [!NOTE]
> 用户选择将首选项切换到新的 [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) 后，你就不能选择返回或还原到使用旧的[旧版 Log Analytics 警告 API](api-alerts.md)。

希望自愿切换到新 [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) 和阻止使用 [旧 Log Analytics 警报 API](api-alerts.md) 的任何客户可以通过对下面的 API 执行 PUT 调用以切换与特定 Log Analytics 工作区关联的所有警报规则来做到这一点。

```
PUT /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

使用包含以下 JSON 的请求正文。

```json
{
    "scheduledQueryRulesEnabled" : true
}
```

还可以从 PowerShell 命令行使用 [ARMClient](https://github.com/projectkudu/ARMClient) 访问该 API。ARMClient 是可简化 Azure 资源管理器 API 调用的开源命令行工具。 如下所示，示例 PUT 调用使用 ARMclient 工具来切换与特定 Log Analytics 工作区关联的所有警报规则。

```powershell
$switchJSON = '{"scheduledQueryRulesEnabled": "true"}'
armclient PUT /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview $switchJSON
```

如果成功地将 Log Analytics 工作区中的所有警报规则切换为使用新 [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules)，则提供以下响应。

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : true
}
```

用户还可以检查 Log Analytics 工作区的当前状态，并查看它是否已切换为仅使用 [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules)。 若要检查，用户可以对下面的 API 执行 GET 调用。

```
GET /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

若要在 PowerShell 命令行中使用 [ARMClient](https://github.com/projectkudu/ARMClient) 工具执行上述调用，请参阅以下示例。

```powershell
armclient GET /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

如果指定 Log Analytics 工作区已切换为仅使用 [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules)，则响应 JSON 如下所示。

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : true
}
```
否则，如果指定 Log Analytics 工作区尚未切换为仅使用 [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules)，则响应 JSON 如下所示。

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : false
}
```

## <a name="next-steps"></a>后续步骤

- 了解 [Azure Monitor - 日志警报](alerts-unified-log.md)。
- 了解然后创建 [Azure 警报中的日志警报](alerts-log.md)。
- 详细了解 [Azure 警报体验](../../azure-monitor/platform/alerts-overview.md)。
