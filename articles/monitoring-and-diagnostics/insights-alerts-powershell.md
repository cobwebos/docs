---
title: "使用 PowerShell 为 Azure 服务创建警报 |Microsoft Docs"
description: "使用 PowerShell 创建 Azure 警报，可在满足指定条件时触发通知或自动化。"
author: rboucher
manager: carolz
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: d26ab15b-7b7e-42a9-81c8-3ce9ead5d252
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2016
ms.author: robb
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: db8ed8980335e2af9654bfe56b4e4c5807674040


---
# <a name="use-powershell-to-create-alerts-for-azure-services"></a>使用 PowerShell 为 Azure 服务创建警报
> [!div class="op_single_selector"]
> * [门户](insights-alerts-portal.md)
> * [PowerShell](insights-alerts-powershell.md)
> * [CLI](insights-alerts-command-line-interface.md)
> 
> 

## <a name="overview"></a>概述
本文演示如何使用 PowerShell 设置 Azure 警报。  

可以根据监视指标或事件接收 Azure 服务的警报。

* **指标值** - 指定指标的值超过在任一方向分配的阈值时，将触发警报。 也就是说，在以下两种情况下会触发警报：首先是满足条件时，然后是满足条件后，不再满足条件时。    
* **活动日志事件** - 每个事件发生，或仅当出现一定次数的事件时，触发警报。

可配置警报，使警报触发时执行以下操作：

* 向服务管理员和共同管理员发送电子邮件通知
* 将电子邮件发送到指定的其他电子邮件。
* 调用 Webhook
* 开始执行 Azure Runbook（仅从 Azure 门户）

可使用以下项配置并获取有关警报规则的信息

* [Azure 门户](insights-alerts-portal.md)
* [PowerShell](insights-alerts-powershell.md)
* [命令行接口 (CLI)](insights-alerts-command-line-interface.md)
* [Azure 监视器 REST API](https://msdn.microsoft.com/library/azure/dn931945.aspx)

有关其他信息，始终可以键入 ```get-help``` 以及需要帮助的 PowerShell 命令。

## <a name="create-alert-rules-in-powershell"></a>在 PowerShell 中创建警报规则
1. 登录 Azure。   
   
    ```PowerShell
    Login-AzureRmAccount
   
    ```
2. 获取可用订阅的列表。 验证正在使用的正确订阅。 如果没有，则使用 `Get-AzureRmSubscription` 中的输出将其设置为正确订阅。
   
    ```PowerShell
    Get-AzureRmSubscription
    Get-AzureRmContext
    Set-AzureRmContext -SubscriptionId <subscriptionid>
    ```
3. 若要列出有关资源组的现有规则，可使用以下命令：
   
   ```PowerShell
   Get-AzureRmAlertRule -ResourceGroup <myresourcegroup> -DetailedOutput
   ```
4. 若要创建一条规则，首先需要有以下几条重要信息。 
   
   * 要为其设置警报的资源的**资源 ID**
   * 资源可用的**指标定义**
     
     获取资源 ID 的一种方法是使用 Azure 门户。 假设已创建该资源，在门户中选中它。 然后在下一个边栏选项卡中，选择“设置”分区下的“属性”。 资源 ID 是下一个边栏选项卡中的字段。 另一种方法是使用 [Azure Resource Explorer](https://resources.azure.com/)（Azure 资源浏览器）。
     
     Web 应用的示例资源 ID 是
     
     ```
     /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename
     ```
     
     可以使用 `Get-AzureRmMetricDefinition` 来查看针对特定资源的所有指标定义的列表。
     
     ```PowerShell
     Get-AzureRmMetricDefinition -ResourceId <resource_id>
     ```
     
     以下示例会生成一张表，其中包含该指标名称和单位。
     
     ```PowerShell
     Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
     
     ```
     Get-AzureRmMetricDefinition 的可用选项的完整列表可通过运行 Get-MetricDefinitions 获取。
5. 以下示例设置了一个关于网站资源的警报。 如果其持续 5 分钟收到任何流量以及 5 分钟没收到任何流量，将触发该警报。
   
    ```PowerShell
    Add-AzureRmMetricAlertRule -Name myMetricRuleWithWebhookAndEmail -Location "East US" -ResourceGroup myresourcegroup -TargetResourceId /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename -MetricName "BytesReceived" -Operator GreaterThan -Threshold 2 -WindowSize 00:05:00 -TimeAggregationOperator Total -Description "alert on any website activity"
   
    ```
6. 若要在警报触发时创建 webhook 或发送电子邮件，首先要创建电子邮件和/或 webhook。 然后立即创建后跟 -Actions 标记的规则，如下面的示例中所示。 如果已通过 PowerShell 创建规则，将无法关联 webhook 或电子邮件。

    ```PowerShell
    $actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com
    $actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri https://www.contoso.com?token=mytoken

    Add-AzureRmMetricAlertRule -Name myMetricRuleWithWebhookAndEmail -Location "East US" -ResourceGroup myresourcegroup -TargetResourceId /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename -MetricName "BytesReceived" -Operator GreaterThan -Threshold 2 -WindowSize 00:05:00 -TimeAggregationOperator Total -Actions $actionEmail, $actionWebhook -Description "alert on any website activity"
    ```


1. 若要创建在活动日志中特定条件下触发的警报，可使用以下形式的命令
   
    ```PowerShell
    $actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com
    $actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri https://www.contoso.com?token=mytoken
   
    Add-AzureRmLogAlertRule -Name myLogAlertRule -Location "East US" -ResourceGroup myresourcegroup -OperationName microsoft.web/sites/start/action -Status Succeeded -TargetResourceGroup resourcegroupbeingmonitored -Actions $actionEmail, $actionWebhook
    ```
   
    -OperationName 与活动日志中事件类型相对应。 示例包括 *Microsoft.Compute/virtualMachines/delete* 和 *microsoft.insights/diagnosticSettings/write*。
   
    可以使用 PowerShell 命令 [Get AzureRmProviderOperation](https://msdn.microsoft.com/library/mt603720.aspx) 来获取可能 operationNames 的列表。 或者，可以使用 Azure 门户查询活动日志以及查找要为其设置警报的特定过去操作。 在友好名称的图形日志视图中显示操作。 查看该条目的 JSON，并拉取 OperationName 值。   
2. 验证是否已通过查看单个规则正确创建警报。
   
    ```PowerShell
    Get-AzureRmAlertRule -Name myMetricRuleWithWebhookAndEmail -ResourceGroup myresourcegroup -DetailedOutput
   
    Get-AzureRmAlertRule -Name myLogAlertRule -ResourceGroup myresourcegroup -DetailedOutput
    ```
3. 删除警报。 这些命令将删除在本文前面创建的规则。
   
    ```PowerShell
    Remove-AzureRmAlertRule -ResourceGroup myresourcegroup -Name myrule
    Remove-AzureRmAlertRule -ResourceGroup myresourcegroup -Name myMetricRuleWithWebhookAndEmail
    Remove-AzureRmAlertRule -ResourceGroup myresourcegroup -Name myLogAlertRule
    ```

## <a name="next-steps"></a>后续步骤
* [获取 Azure 监视概述](monitoring-overview.md)，包括可收集和监视的信息的类型。
* 了解[在警报中配置 Webhook](insights-webhooks-alerts.md)的详细信息。
* 了解关于 [Azure 自动化 Runbook](../automation/automation-starting-a-runbook.md) 的详细信息。
* 获取[收集诊断日志概述](monitoring-overview-of-diagnostic-logs.md)以收集有关服务的详细高频率指标。
* 获取[指标集合概述](insights-how-to-customize-monitoring.md)以确保你的服务可用且响应迅速。




<!--HONumber=Nov16_HO3-->


