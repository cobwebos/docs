---
title: 通过更新逻辑应用和 runbook 来为 Azure Monitor 经典警报迁移准备
description: 了解如何修改 webhook、 逻辑应用和 runbook，以准备自愿迁移。
author: snehithm
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: 347c89991cbb4d28b46eafff0a783148793ad2f7
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2019
ms.locfileid: "64727488"
---
# <a name="prepare-your-logic-apps-and-runbooks-for-migration-of-classic-alert-rules"></a>准备迁移经典警报规则的逻辑应用和 runbook

作为[之前公布](monitoring-classic-retirement.md)，在 Azure Monitor 经典警报将被停用在 2019 年 7 月中。 迁移工具是的客户使用经典警报规则的用户，那些想要自行触发迁移到 Azure 门户中可用。

如果您选择自愿迁移到新的警报规则的经典警报规则，请注意，有两个系统之间的一些差异。 本文介绍这些差异以及如何准备更改。

## <a name="api-changes"></a>API 更改

创建和管理经典警报规则的 Api (`microsoft.insights/alertrules`) 不同于创建和管理新指标警报的 Api (`microsoft.insights/metricalerts`)。 如果您以编程方式创建和管理经典警报规则目前，更新你的部署脚本以使用新的 Api。

下表是对经典和新警报的编程接口的引用：

|         |经典警报  |新指标警报 |
|---------|---------|---------|
|REST API     | [microsoft.insights/alertrules](https://docs.microsoft.com/rest/api/monitor/alertrules)         | [microsoft.insights/metricalerts](https://docs.microsoft.com/rest/api/monitor/metricalerts)       |
|Azure CLI     | [az monitor alert](https://docs.microsoft.com/cli/azure/monitor/alert?view=azure-cli-latest)        | [az monitor metrics alert](https://docs.microsoft.com/cli/azure/monitor/metrics/alert?view=azure-cli-latest)        |
|PowerShell      | [参考](https://docs.microsoft.com/powershell/module/az.monitor/add-azmetricalertrule)       |      |
| Azure 资源管理器模板 | [经典警报](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-enable-template)|[为新指标警报](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-create-templates)|

## <a name="notification-payload-changes"></a>通知有效负载的更改

通知有效负载格式是之间略有不同[经典警报规则](alerts-webhooks.md)并[新指标警报](alerts-metric-near-real-time.md#payload-schema)。 如果您有任何 webhook、 逻辑应用或由经典警报规则触发的 runbook 操作，则必须更新这些通知终结点以接受新指标警报有效负载格式。

使用下表来映射 webhook 有效负载的域从经典格式到新的格式：

|  |经典警报  |新指标警报 |
|---------|---------|---------|
|警报激活或解析？    | **status**       | **data.status** |
|有关警报的上下文信息     | **上下文**        | **data.context**        |
|警报已激活或已解决的时间戳     | **context.timestamp**       | **data.context.timestamp**        |
| 警报规则 ID | **context.id** | **data.context.id** |
| 警报规则名称 | **context.name** | **data.context.name** |
| 警报规则的说明 | **context.description** | **data.context.description** |
| 警报规则条件 | **context.condition** | **data.context.condition** |
| 指标名称 | **context.condition.metricName** | **data.context.condition.allOf[0].metricName** |
| 时间聚合 （度量值的聚合方式评估范围内）| **data.context.condition.timeAggregation** | **data.context.condition.timeAggregation** |
| 评估期 | **context.condition.windowSize** | **data.context.condition.windowSize** |
| 运算符 （如何将聚合的指标值进行比较针对阈值） | **context.condition.operator** | **data.context.condition.operator** |
| 阈值 | **context.condition.threshold** | **data.context.condition.allOf[0].threshold** |
| 指标值 | **context.condition.metricValue** | **data.context.condition.allOf[0].metricValue** |
| 订阅 ID | **context.subscriptionId** | **data.context.subscriptionId** |
| 受影响资源的资源组 | **context.resourceGroup** | **data.context.resourceGroup** |
| 受影响资源的名称 | **context.resourceName** | **data.context.resourceName** |
| 受影响资源的类型 | **context.resourceType** | **data.context.resourceType** |
| 受影响资源的资源 ID | **context.resourceId** | **data.context.resourceId** |
| 指向门户资源摘要页的直接链接 | **context.portalLink** | **data.context.portalLink** |
| 要传递给 webhook 或逻辑应用的自定义有效负载字段 | **properties** | **data.properties** |

如您所见，是类似，有效负载。 以下部分提供了：

- 有关修改逻辑应用以使用新格式的详细信息。
- Runbook 示例，将分析新警报的通知有效负载。

## <a name="modify-a-logic-app-to-receive-a-metric-alert-notification"></a>修改逻辑应用以接收指标的警报通知

如果使用的逻辑应用使用经典警报，必须修改您要分析的新指标警报有效负载的逻辑应用代码。 执行以下步骤:

1. 创建新的逻辑应用。

1. 使用"Azure 监视器-指标警报处理程序"模板。 此模板具有**HTTP 请求**相应的架构定义的触发器。

    ![逻辑应用模板](media/alerts-migration/logic-app-template.png "指标的警报模板")

1. 添加要承载您的处理逻辑的操作。

## <a name="use-an-automation-runbook-that-receives-a-metric-alert-notification"></a>使用自动化 runbook 接收指标的警报通知

下面的示例提供用于在 runbook 中使用的 PowerShell 代码。 此代码可以分析经典指标警报规则和新指标警报规则的负载。

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

将虚拟机停止时，会触发警报的 runbook 的完整示例，请参阅[Azure 自动化文档](https://docs.microsoft.com/azure/automation/automation-create-alert-triggered-runbook)。

## <a name="partner-integration-via-webhooks"></a>通过 webhook 的合作伙伴集成

大部分[我们将与经典警报相集成的合作伙伴](https://docs.microsoft.com/azure/azure-monitor/platform/partners)已支持通过其集成的新型指标警报。 已使用新指标警报的已知的集成包括：

- [PagerDuty](https://www.pagerduty.com/docs/guides/azure-integration-guide/)
- [OpsGenie](https://docs.opsgenie.com/docs/microsoft-azure-integration)
- [Signl4](https://www.signl4.com/blog/mobile-alert-notifications-azure-monitor/)

如果要使用此处未列出的合作伙伴集成，确认集成提供商集成适用于新指标警报。

## <a name="next-steps"></a>后续步骤

- [如何使用迁移工具](alerts-using-migration-tool.md)
- [了解迁移工具的工作原理](alerts-understand-migration.md)
