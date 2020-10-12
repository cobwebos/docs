---
title: 升级到当前 Azure Monitor 日志警报 API
description: 了解如何切换到日志警报 ScheduledQueryRules API
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 05/30/2019
ms.subservice: alerts
ms.openlocfilehash: 868a8eb6cf38d471eb9dc1f47c903404d05ffc0c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91294506"
---
# <a name="upgrade-to-the-current-log-alerts-api-from-legacy-log-analytics-alert-api"></a>从旧的 Log Analytics 警报 API 升级到当前日志警报 API

> [!NOTE]
> 本文仅适用于 Azure 公共 (**不适** 用于 azure 政府版或 azure 中国云) 。

> [!NOTE]
> 用户选择将首选项切换到当前 [SCHEDULEDQUERYRULES api](/rest/api/monitor/scheduledqueryrules) 后，便无法还原到较旧的 [旧 Log Analytics 警报 API](api-alerts.md)。

过去，用户使用 [旧 Log Analytics 警报 API](api-alerts.md) 来管理日志警报规则。 当前工作区使用 [SCHEDULEDQUERYRULES API](/rest/api/monitor/scheduledqueryrules)。 本文介绍从旧 API 切换到当前 API 的优点和过程。

## <a name="benefits"></a>好处

- 用于创建警报规则的单个模板 (以前需要三个单独的模板) 。
- Log Analytics 工作区或 Application Insights 资源的单个 API。
- [PowerShell cmdlet 支持](alerts-log.md#managing-log-alerts-using-powershell)。
- 与所有其他警报类型的严重性对齐。
- 能够创建跨多个外部资源（如 Log Analytics 工作区或 Application Insights 资源）的 [跨工作区日志警报](../log-query/cross-workspace-query.md) 。
- 用户可以通过使用 "聚合 On" 参数来指定要拆分警报的维度。
- 日志警报的持续时间最长为两天，数据 (之前限制为一天) 。

## <a name="impact"></a>影响

- 必须通过当前 API 创建/编辑所有新规则。 请参阅 [通过 Azure 资源模板使用的示例](alerts-log-create-templates.md) ，以及 [通过 PowerShell 使用的示例](alerts-log.md#managing-log-alerts-using-powershell)。
- 当规则成为 Azure 资源管理器跟踪当前 API 中的资源，并且必须是唯一的，规则资源 ID 将更改为以下结构： `<WorkspaceName>|<savedSearchId>|<scheduleId>|<ActionId>` 。 警报规则的显示名称将保持不变。

## <a name="process"></a>进程

在大多数情况下，切换过程不是交互式的，不需要手动步骤。 警报规则在交换机期间或之后不会停止或停止。
执行此调用可切换与特定 Log Analytics 工作区关联的所有警报规则：

```
PUT /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

包含以下 JSON 的请求正文：

```json
{
    "scheduledQueryRulesEnabled" : true
}
```

下面是使用 [ARMClient](https://github.com/projectkudu/ARMClient)（一个开源命令行工具）的示例，该工具简化了上述 API 调用的调用：

```powershell
$switchJSON = '{"scheduledQueryRulesEnabled": "true"}'
armclient PUT /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview $switchJSON
```

如果开关成功，响应为：

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : true
}
```

## <a name="check-switching-status-of-workspace"></a>检查工作区的切换状态

你还可以使用此 API 调用来检查交换机状态：

```
GET /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

还可以使用 [ARMClient](https://github.com/projectkudu/ARMClient) 工具：

```powershell
armclient GET /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

如果 Log Analytics 工作区切换到 [SCHEDULEDQUERYRULES API](/rest/api/monitor/scheduledqueryrules)，响应为：

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : true
}
```
如果未切换 Log Analytics 工作区，则响应为：

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : false
}
```

## <a name="next-steps"></a>后续步骤

- 了解 [Azure Monitor - 日志警报](alerts-unified-log.md)。
- 了解如何 [使用 API 管理日志警报](alerts-log-create-templates.md)。
- 了解如何 [使用 PowerShell 管理日志警报](alerts-log.md#managing-log-alerts-using-powershell)。
- 详细了解 [Azure 警报体验](./alerts-overview.md)。
