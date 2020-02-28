---
title: 通过更新逻辑应用和 runbook，为 Azure Monitor 经典警报迁移做准备
description: 了解如何修改 webhook、逻辑应用和 runbook，以便为自愿迁移做准备。
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 03/19/2018
ms.subservice: alerts
ms.openlocfilehash: 9219e105acb98424939030af76b526d475585619
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77665586"
---
# <a name="prepare-your-logic-apps-and-runbooks-for-migration-of-classic-alert-rules"></a>为经典警报规则的迁移准备逻辑应用和 runbook

如前所述[，Azure Monitor](monitoring-classic-retirement.md)中的经典警报在2019年9月（最初是2019年7月）内停用。 向使用经典警报规则的客户和想要触发迁移的客户提供 Azure 门户迁移工具。

> [!NOTE]
> 由于迁移工具的推出延迟，经典警报的停用日期已于2019年6月 30 2019 日最初发布日期延长到了年8月31日。

如果选择主动将经典警报规则迁移到新的警报规则，请注意两个系统之间存在一些差异。 本文介绍了这些差异以及如何为更改做准备。

## <a name="api-changes"></a>API 更改

用于创建和管理经典警报规则（`microsoft.insights/alertrules`）的 Api 不同于创建和管理新指标警报（`microsoft.insights/metricalerts`）的 Api。 如果你现在以编程方式创建和管理经典警报规则，请更新你的部署脚本以使用新的 Api。

下表是典型和新警报的编程接口的参考：

|         |经典警报  |新的指标警报 |
|---------|---------|---------|
|REST API     | [microsoft insights/alertrules](https://docs.microsoft.com/rest/api/monitor/alertrules)         | [microsoft insights/metricalerts](https://docs.microsoft.com/rest/api/monitor/metricalerts)       |
|Azure CLI     | [az monitor 警报](https://docs.microsoft.com/cli/azure/monitor/alert?view=azure-cli-latest)        | [az monitor 指标警报](https://docs.microsoft.com/cli/azure/monitor/metrics/alert?view=azure-cli-latest)        |
|PowerShell      | [参考](https://docs.microsoft.com/powershell/module/az.monitor/add-azmetricalertrule)       |  [参考](https://docs.microsoft.com/powershell/module/az.monitor/add-azmetricalertrulev2)    |
| Azure 资源管理器模板 | [对于经典警报](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-enable-template)|[对于新的指标警报](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-create-templates)|

## <a name="notification-payload-changes"></a>通知有效负载更改

[经典警报规则](alerts-webhooks.md)和[新的指标警报](alerts-metric-near-real-time.md#payload-schema)之间的通知负载格式略有不同。 如果有由经典警报规则触发的任何 webhook、逻辑应用或 runbook 操作，则必须更新这些通知终结点以接受新指标警报的有效负载格式。

使用下表将 webhook 负载字段从经典格式映射到新格式：

|  |经典警报  |新的指标警报 |
|---------|---------|---------|
|警报是否已激活或已解决？    | **status**       | **数据。状态** |
|有关警报的上下文信息     | **context**        | **数据。上下文**        |
|激活或解决警报的时间戳     | **context。 timestamp**       | **data. context.subname**        |
| 警报规则 ID | **context.id** | **data.context.id** |
| 警报规则名称 | **context.name** | **data.context.name** |
| 警报规则的说明 | **context。说明** | **数据。上下文。说明** |
| 警报规则条件 | **context。条件** | **data. context。 condition** |
| 指标名称 | **metricName** | **allOf [0]. metricName** |
| 时间聚合（度量值的聚合方式）| **timeAggregation** | **timeAggregation** |
| 评估期 | **windowSize** | **data. windowSize** |
| 运算符（聚合指标值如何与阈值进行比较） | **context. condition 运算符** | **data. context.subname. 运算符** |
| 阈值 | **context。 condition。阈值** | **allOf [0]。阈值** |
| 指标值 | **metricValue** | **allOf [0]. metricValue** |
| 订阅 ID | **context.subname** | **data. Context.subscriptionid** |
| 受影响资源的资源组 | **context.subname** | **数据. 资源组** |
| 受影响资源的名称 | **Context.resourcename** | **Context.resourcename** |
| 受影响资源的类型 | **context.subname** | **数据. resourceType** |
| 受影响资源的资源 ID | **上下文 resourceId** | **数据. 上下文 Id** |
| 指向门户资源摘要页的直接链接 | **portalLink** | **portalLink** |
| 要传递到 webhook 或逻辑应用的自定义负载字段 | **properties** | **data. properties** |

有效负载类似，如您所见。 以下部分提供了：

- 有关修改逻辑应用以使用新格式的详细信息。
- 一个 runbook 示例，用于分析新警报的通知有效负载。

## <a name="modify-a-logic-app-to-receive-a-metric-alert-notification"></a>修改逻辑应用以接收指标警报通知

如果使用具有经典警报的逻辑应用，则必须修改逻辑应用代码，以分析新的指标警报负载。 执行以下步骤:

1. 创建新的逻辑应用。

1. 使用 "Azure Monitor-度量值警报处理程序" 模板。 此模板包含一个**HTTP 请求**触发器，其中定义了适当的架构。

    ![逻辑应用-模板](media/alerts-migration/logic-app-template.png "指标警报模板")

1. 添加操作以托管处理逻辑。

## <a name="use-an-automation-runbook-that-receives-a-metric-alert-notification"></a>使用接收指标警报通知的自动化 runbook

以下示例提供了要在 runbook 中使用的 PowerShell 代码。 此代码可以分析经典指标警报规则和新的指标警报规则的有效负载。

```PowerShell
## Example PowerShell code to use in a runbook to handle parsing of both classic and new metric alerts.

[OutputType("PSAzureOperationResponse")]

param
(
    [Parameter (Mandatory=$false)]
    [object] $WebhookData
)

$ErrorActionPreference = "stop"

if ($WebhookData)
{
    # Get the data object from WebhookData.
    $WebhookBody = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)

    # Determine whether the alert triggering the runbook is a classic metric alert or a new metric alert (depends on the payload schema).
    $schemaId = $WebhookBody.schemaId
    Write-Verbose "schemaId: $schemaId" -Verbose
    if ($schemaId -eq "AzureMonitorMetricAlert") {

        # This is the new metric alert schema.
        $AlertContext = [object] ($WebhookBody.data).context
        $status = ($WebhookBody.data).status

        # Parse fields related to alert rule condition.
        $metricName = $AlertContext.condition.allOf[0].metricName
        $metricValue = $AlertContext.condition.allOf[0].metricValue
        $threshold = $AlertContext.condition.allOf[0].threshold
        $timeAggregation = $AlertContext.condition.allOf[0].timeAggregation
    }
    elseif ($schemaId -eq $null) {
        # This is the classic metric alert schema.
        $AlertContext = [object] $WebhookBody.context
        $status = $WebhookBody.status

        # Parse fields related to alert rule condition.
        $metricName = $AlertContext.condition.metricName
        $metricValue = $AlertContext.condition.metricValue
        $threshold = $AlertContext.condition.threshold
        $timeAggregation = $AlertContext.condition.timeAggregation
    }
    else {
        # The schema is neither a classic metric alert nor a new metric alert.
        Write-Error "The alert data schema - $schemaId - is not supported."
    }

    # Parse fields related to resource affected.
    $ResourceName = $AlertContext.resourceName
    $ResourceType = $AlertContext.resourceType
    $ResourceGroupName = $AlertContext.resourceGroupName
    $ResourceId = $AlertContext.resourceId
    $SubId = $AlertContext.subscriptionId

    ## Your logic to handle the alert here.
}
else {
    # Error
    Write-Error "This runbook is meant to be started from an Azure alert webhook only."
}

```

有关在触发警报时停止虚拟机的 runbook 的完整示例，请参阅[Azure 自动化文档](https://docs.microsoft.com/azure/automation/automation-create-alert-triggered-runbook)。

## <a name="partner-integration-via-webhooks"></a>通过 webhook 的合作伙伴集成

[与经典警报集成](https://docs.microsoft.com/azure/azure-monitor/platform/partners)的大多数合作伙伴已经通过其集成支持较新的指标警报。 已使用新指标警报的已知集成包括：

- [PagerDuty](https://www.pagerduty.com/docs/guides/azure-integration-guide/)
- [OpsGenie](https://docs.opsgenie.com/docs/microsoft-azure-integration)
- [Signl4](https://www.signl4.com/blog/mobile-alert-notifications-azure-monitor/)

如果你使用的是未在此处列出的合作伙伴集成，请使用集成提供商确认集成可用于新的指标警报。

## <a name="next-steps"></a>后续步骤

- [如何使用迁移工具](alerts-using-migration-tool.md)
- [了解迁移工具的工作原理](alerts-understand-migration.md)
