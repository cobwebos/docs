---
title: 使用 PowerShell 为 Azure 服务创建经典警报 | Microsoft Docs
description: 满足指定的条件时触发电子邮件或通知、或调用网站 URL (Webhook) 或自动化。
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/28/2018
ms.author: robb
ms.component: alerts
ms.openlocfilehash: b08a8f66add45d64085ac05605fe3c7d7f91b705
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/20/2018
ms.locfileid: "36286193"
---
# <a name="use-powershell-to-create-alerts-for-azure-services"></a>使用 PowerShell 为 Azure 服务创建警报
> [!div class="op_single_selector"]
> * [门户](insights-alerts-portal.md)
> * [PowerShell](insights-alerts-powershell.md)
> * [CLI](insights-alerts-command-line-interface.md)
>
>

> [!NOTE]
> 本文介绍了如何创建旧式经典指标警报。 Azure Monitor 现在支持[较新、更好的指标警报](monitoring-near-real-time-metric-alerts.md)。 这些警报可监视多个指标，并允许对维度指标发出警报。 即将推出对新型指标警报的 Powershell 支持。
>
>

本文演示如何使用 PowerShell 设置 Azure 经典指标警报。  

可以根据 Azure 服务的指标接收警报，也可以接收有关 Azure 中发生的事件的警报。

* **指标值**：当指定指标的值超过在任一方向分配的阈值时，将触发警报。 也就是说，当条件先是满足以及之后不再满足该条件时，警报都会触发。    
* **活动日志事件**：发生每个事件，或当出现特定事件时可触发警报。 若要详细了解活动日志警报，请参阅[创建活动日志警报（经典）](monitoring-activity-log-alerts.md)。

可配置经典指标警报，使警报触发时执行以下任务：

* 向服务管理员和共同管理员发送电子邮件通知。
* 将电子邮件发送到指定的其他电子邮件地址。
* 调用 Webhook。
* 开始执行 Azure Runbook（仅从 Azure 门户）。

可以从以下位置配置并获取有关警报规则的信息： 

* [Azure 门户](insights-alerts-portal.md)
* [PowerShell](insights-alerts-powershell.md)
* [Azure 命令行接口 (CLI)](insights-alerts-command-line-interface.md)
* [Azure 监视器 REST API](https://msdn.microsoft.com/library/azure/dn931945.aspx)

有关其他信息，始终可以键入 ```Get-Help``` 后跟需要帮助的 PowerShell 命令。

## <a name="create-alert-rules-in-powershell"></a>在 PowerShell 中创建警报规则
1. 登录 Azure。   

    ```PowerShell
    Connect-AzureRmAccount

    ```
2. 获取可供你使用的订阅列表。 确认正在使用正确的订阅。 如果不是，请使用 `Get-AzureRmSubscription` 的输出转到正确的订阅。

    ```PowerShell
    Get-AzureRmSubscription
    Get-AzureRmContext
    Set-AzureRmContext -SubscriptionId <subscriptionid>
    ```
3. 使用以下命令列出有关资源组的现有规则：

   ```PowerShell
   Get-AzureRmAlertRule -ResourceGroup <myresourcegroup> -DetailedOutput
   ```
4. 若要创建一条规则，首先需要有以下几条重要信息。

    - 要为其设置警报的资源的资源 ID。
    - 可用于该资源的指标定义。

     获取资源 ID 的一种方法是使用 Azure 门户。 假设已创建该资源，请在门户中选中它。 然后，在下一个边栏选项卡的“设置”部分中，选择“属性”。 **资源 ID** 是下一个边栏选项卡中的字段。 
     
     还可以使用 [Azure 资源浏览器](https://resources.azure.com/)获取资源 ID。

     以下是 Web 应用的示例资源 ID：

     ```
     /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename
     ```

     可以使用 `Get-AzureRmMetricDefinition` 来查看针对特定资源的所有指标定义的列表：

     ```PowerShell
     Get-AzureRmMetricDefinition -ResourceId <resource_id>
     ```

     以下示例会生成一张表，其中包含指标名称和该指标的单位：

     ```PowerShell
     Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit

     ```
     若要获取 Get-AzureRmMetricDefinition 的可用选项的完整列表，请运行 `Get-Help Get-AzureRmMetricDefinition -Detailed`。
5. 以下示例设置了一个有关网站资源的警报。 如果其持续 5 分钟收到任何流量以及 5 分钟没收到任何流量，将触发该警报。

    ```PowerShell
    Add-AzureRmMetricAlertRule -Name myMetricRuleWithWebhookAndEmail -Location "East US" -ResourceGroup myresourcegroup -TargetResourceId /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename -MetricName "BytesReceived" -Operator GreaterThan -Threshold 2 -WindowSize 00:05:00 -TimeAggregationOperator Total -Description "alert on any website activity"

    ```
6. 若要在警报触发时创建 Webhook 或发送电子邮件，请先创建电子邮件或 Webhook。 然后，立即创建后跟 -Actions 标记的规则，如以下示例所示。 不能将 Webhook 或电子邮件与已创建的规则相关联。

    ```PowerShell
    $actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com
    $actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri https://www.contoso.com?token=mytoken

    Add-AzureRmMetricAlertRule -Name myMetricRuleWithWebhookAndEmail -Location "East US" -ResourceGroup myresourcegroup -TargetResourceId /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename -MetricName "BytesReceived" -Operator GreaterThan -Threshold 2 -WindowSize 00:05:00 -TimeAggregationOperator Total -Actions $actionEmail, $actionWebhook -Description "alert on any website activity"
    ```

7. 查看各个规则以验证警报是否已正确创建。

    ```PowerShell
    Get-AzureRmAlertRule -Name myMetricRuleWithWebhookAndEmail -ResourceGroup myresourcegroup -DetailedOutput

    Get-AzureRmAlertRule -Name myLogAlertRule -ResourceGroup myresourcegroup -DetailedOutput
    ```
8. 删除警报。 这些命令将删除先前在本文中创建的规则。

    ```PowerShell
    Remove-AzureRmAlertRule -ResourceGroup myresourcegroup -Name myrule
    Remove-AzureRmAlertRule -ResourceGroup myresourcegroup -Name myMetricRuleWithWebhookAndEmail
    Remove-AzureRmAlertRule -ResourceGroup myresourcegroup -Name myLogAlertRule
    ```

## <a name="next-steps"></a>后续步骤
* [获取 Azure 监视概述](monitoring-overview.md)，包括可收集和监视的信息的类型。
* 了解如何[在警报中配置 Webhook](insights-webhooks-alerts.md)。
* 了解如何[针对活动日志事件配置警报](monitoring-activity-log-alerts.md)。
* 详细了解 [Azure 自动化 Runbook](../automation/automation-starting-a-runbook.md)。
* 获取[收集诊断日志概述](monitoring-overview-of-diagnostic-logs.md)以收集有关服务的详细高频率指标。
* 获取[指标集合概述](insights-how-to-customize-monitoring.md)以确保服务可用且响应迅速。
