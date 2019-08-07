---
title: 通过更新逻辑应用和 Runbook 来准备 Azure Monitor 经典警报迁移
description: 了解如何修改 Webhook、逻辑应用和 Runbook，以准备自愿性迁移。
author: snehithm
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: 5235db5cab39be6e36bdf145d3edc7c73fe9da54
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2019
ms.locfileid: "68827388"
---
# <a name="prepare-your-logic-apps-and-runbooks-for-migration-of-classic-alert-rules"></a>准备逻辑应用和 Runbook 以迁移经典警报规则

根据[之前的公告](monitoring-classic-retirement.md)，Azure Monitor 中的经典警报即将在 2019 年 9 月（原来是 2019 年 7 月）停用。 Azure 门户中为使用经典警报规则并想要自行触发迁移的客户提供了一个迁移工具。

> [!NOTE]
> 由于迁移工具的延迟推出，经典警报迁移的停用日期已从原来宣布的 2019 年 6 月 30 日推迟至 2019 年 8 月 31 日。

如果你选择自愿将经典警报规则迁移到新的警报规则，请注意，这两个系统存在一些差异。 本文将会解释这些差异以及如何做好相应的准备。

## <a name="api-changes"></a>API 更改

用于创建和管理经典警报规则 (`microsoft.insights/alertrules`) 的 API 不同于用于创建和管理新指标警报 (`microsoft.insights/metricalerts`) 的 API。 如果当前以编程方式创建和管理经典警报规则，请更新部署脚本以使用新的 API。

下表提供了经典警报和新警报的编程接口的参考信息：

|         |经典警报  |新指标警报 |
|---------|---------|---------|
|REST API     | [microsoft.insights/alertrules](https://docs.microsoft.com/rest/api/monitor/alertrules)         | [microsoft.insights/metricalerts](https://docs.microsoft.com/rest/api/monitor/metricalerts)       |
|Azure CLI     | [az monitor alert](https://docs.microsoft.com/cli/azure/monitor/alert?view=azure-cli-latest)        | [az monitor metrics alert](https://docs.microsoft.com/cli/azure/monitor/metrics/alert?view=azure-cli-latest)        |
|PowerShell      | [参考](https://docs.microsoft.com/powershell/module/az.monitor/add-azmetricalertrule)       |  [参考](https://docs.microsoft.com/powershell/module/az.monitor/add-azmetricalertrulev2)    |
| Azure 资源管理器模板 | [经典警报](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-enable-template)|[新指标警报](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-create-templates)|

## <a name="notification-payload-changes"></a>通知有效负载更改

[经典警报规则](alerts-webhooks.md)和[新指标警报](alerts-metric-near-real-time.md#payload-schema)的通知有效负载格式略有不同。 如果经典警报规则会触发你的任何 Webhook、逻辑应用或 Runbook 操作，则必须更新这些通知终结点，以接受新指标警报的有效负载格式。

使用下表将经典格式中的 Webhook 有效负载字段映射到新格式：

|  |经典警报  |新指标警报 |
|---------|---------|---------|
|警报是否已激活或解决？    | **status**       | **data.status** |
|有关警报的上下文信息     | **context**        | **data.context**        |
|激活或解决警报时的时间戳     | **context.timestamp**       | **data.context.timestamp**        |
| 警报规则 ID | **context.id** | **data.context.id** |
| 警报规则名称 | **context.name** | **data.context.name** |
| 警报规则的说明 | **context.description** | **data.context.description** |
| 警报规则条件 | **context.condition** | **data.context.condition** |
| 指标名称 | **context.condition.metricName** | **data.context.condition.allOf[0].metricName** |
| 时间聚合（在评估期限内聚合指标的方式）| **timeAggregation** | **timeAggregation** |
| 评估期 | **context.condition.windowSize** | **data.context.condition.windowSize** |
| 运算符（如何将聚合指标值与阈值进行比较） | **context.condition.operator** | **data.context.condition.operator** |
| 阈值 | **context.condition.threshold** | **data.context.condition.allOf[0].threshold** |
| 指标值 | **context.condition.metricValue** | **data.context.condition.allOf[0].metricValue** |
| 订阅 ID | **context.subscriptionId** | **data.context.subscriptionId** |
| 受影响资源的资源组 | **context.resourceGroup** | **data.context.resourceGroup** |
| 受影响资源的名称 | **context.resourceName** | **data.context.resourceName** |
| 受影响资源的类型 | **context.resourceType** | **data.context.resourceType** |
| 受影响资源的资源 ID | **context.resourceId** | **data.context.resourceId** |
| 门户资源摘要页的直接链接 | **context.portalLink** | **data.context.portalLink** |
| 要传递给 Webhook 或逻辑应用的自定义有效负载字段 | **properties** | **data.properties** |

可以看到，有效负载是类似的。 以下部分提供：

- 有关修改逻辑应用以使用新格式的详细信息。
- 一个 Runbook 示例，用于分析新警报的通知有效负载。

## <a name="modify-a-logic-app-to-receive-a-metric-alert-notification"></a>修改逻辑应用以接收指标警报通知

如果将逻辑应用与经典警报配合使用，则必须修改逻辑应用代码，以分析新指标警报有效负载。 请执行以下步骤：

1. 创建新的逻辑应用。

1. 使用“Azure Monitor - 指标警报处理程序”模板。 此模板包含一个定义了相应架构的 **HTTP 请求**触发器。

    ![logic-app-template](media/alerts-migration/logic-app-template.png "指标警报模板")

1. 添加用于托管处理逻辑的操作。

## <a name="use-an-automation-runbook-that-receives-a-metric-alert-notification"></a>使用可接收指标警报通知的自动化 Runbook

以下示例提供了要在 Runbook 中使用的 PowerShell 代码。 此代码可以分析经典指标警报规则和新指标警报规则的有效负载。

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

有关在触发警报时停止虚拟机的完整 Runbook 示例，请参阅 [Azure 自动化文档](https://docs.microsoft.com/azure/automation/automation-create-alert-triggered-runbook)。

## <a name="partner-integration-via-webhooks"></a>通过 Webhook 进行合作伙伴集成

[我们的大部分与经典警报集成的合作伙伴](https://docs.microsoft.com/azure/azure-monitor/platform/partners)已能够支持通过其集成处理新型指标警报。 能够处理新指标警报的已知集成包括：

- [PagerDuty](https://www.pagerduty.com/docs/guides/azure-integration-guide/)
- [OpsGenie](https://docs.opsgenie.com/docs/microsoft-azure-integration)
- [Signl4](https://www.signl4.com/blog/mobile-alert-notifications-azure-monitor/)

如果你使用的合作伙伴集成未在此处列出，请与集成提供商确认其集成是否能够处理新的指标警报。

## <a name="next-steps"></a>后续步骤

- [如何使用迁移工具](alerts-using-migration-tool.md)
- [了解迁移工具的工作原理](alerts-understand-migration.md)
