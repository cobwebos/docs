---
title: "使用跨平台命令行接口 (CLI) 为 Azure 服务创建警报 | Microsoft Docs"
description: "使用命令行接口创建 Azure 警报，可在满足指定条件时触发通知或自动化。"
author: rboucher
manager: carolz
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 5c6a2d27-7dcc-4f89-8752-9bb31b05ff35
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2016
ms.author: robb
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: b1bb722726fd44972887fdcff2b33a15725914d2


---
# <a name="use-the-cross-platform-command-line-interface-cli-to-create-alerts-for-azure-services"></a>使用跨平台命令行接口 (CLI) 为 Azure 服务创建警报
> [!div class="op_single_selector"]
> * [门户](insights-alerts-portal.md)
> * [PowerShell](insights-alerts-powershell.md)
> * [CLI](insights-alerts-command-line-interface.md)
> 
> 

## <a name="overview"></a>概述
本文演示如何使用命令行接口 (CLI) 设置 Azure 警报。

> [!NOTE]
> 自 2016 年 9 月 25 日起，“Azure Insights”更名为 Azure 监视器。 但是，命名空间及其下命令仍包含“insights”。
> 
> 

可以根据监视指标或事件接收 Azure 服务的警报。

* **指标值** - 指定指标的值超过在任一方向分配的阈值时，将触发警报。 也就是说，在以下两种情况下会触发警报：首先是满足条件时，然后是满足条件后，不再满足条件时。    
* **活动日志事件** - 每个事件发生，或仅当出现一定次数的事件时，触发警报。

可配置警报，使警报触发时执行以下操作：

* 向服务管理员和共同管理员发送电子邮件通知
* 将电子邮件发送到指定的其他电子邮件。
* 调用 Webhook
* 开始执行 Azure Runbook（当前仅从 Azure 门户）

可使用以下项配置并获取有关警报规则的信息

* [Azure 门户](insights-alerts-portal.md)
* [PowerShell](insights-alerts-powershell.md)
* [命令行接口 (CLI)](insights-alerts-command-line-interface.md)
* [Azure 监视器 REST API](https://msdn.microsoft.com/library/azure/dn931945.aspx)

可以随时通过键入命令并在结尾处放置 -help 来接收命令的帮助。 例如：

    ```console
    azure insights alerts -help
    azure insights alerts actions email create -help
    ```

## <a name="create-alert-rules-using-the-cli"></a>使用 CLI 创建警报规则
1. 执行先决条件并登录到 Azure。 请参阅 [Azure 监视器 CLI 示例](insights-cli-samples.md)。 简单地说，安装 CLI 并运行这些命令。 通过它们可进行登录，查看正在使用的订阅，并为运行 Azure 监视器命令做好准备。

    ```console
    azure login
    azure account show
    azure config mode arm

    ```

1. 若要列出资源组的现有规则，请使用以下形式，**azure insights alerts rule list** *[options] &lt;resourceGroup&gt;*
   
   ```console
   azure insights alerts rule list myresourcegroupname
   
   ```
2. 若要创建一条规则，首先需要有以下几条重要信息。
   
   * 要为其设置警报的资源的**资源 ID**
   * 资源可用的**指标定义**
     
     获取资源 ID 的一种方法是使用 Azure 门户。 假设已创建该资源，在门户中选中它。 然后在下一个边栏选项卡中，选择“设置”分区下的“属性”。 *资源 ID* 是下一个边栏选项卡中的字段。 另一种方法是使用 [Azure Resource Explorer](https://resources.azure.com/)（Azure 资源浏览器）。
     
     Web 应用的示例资源 ID 是
     
     ```console
     /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename
     ```
     
     若要获取上一个资源示例的这些指标的可用指标和单位列表，请使用以下 CLI命令：  
     
     ```console
     azure insights metrics list /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename PT1M
     ```
     
     PT1M 是可用度量的粒度（1分钟间隔）。 使用不同的粒度可以提供不同的指标选项。
3. 若要创建基于指标的警报规则，请使用以下形式的命令：
   
    **azure insights alerts rule metric set** *[options] &lt;ruleName&gt; &lt;location&gt; &lt;resourceGroup&gt; &lt;windowSize&gt; &lt;operator&gt; &lt;threshold&gt; &lt;targetResourceId&gt; &lt;metricName&gt; &lt;timeAggregationOperator&gt;*
   
    以下示例设置了一个关于网站资源的警报。 如果其持续 5 分钟收到任何流量以及 5 分钟没收到任何流量，将触发该警报。
   
    ```console
    azure insights alerts rule metric set myrule eastus myreasourcegroup PT5M GreaterThan 2 /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename BytesReceived Total
   
    ```
4. 若要在警报触发时创建 webhook 或发送电子邮件，首先要创建电子邮件和/或 webhook。 然后立即创建规则。 如果已使用 CLI 创建规则，将无法关联 webhook 或电子邮件。
   
    ```console
    azure insights alerts actions email create --customEmails myemail@contoso.com
   
    azure insights alerts actions webhook create https://www.contoso.com
   
    azure insights alerts rule metric set myrulewithwebhookandemail eastus myreasourcegroup PT5M GreaterThan 2 /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename BytesReceived Total
    ```
5. 若要创建在活动日志中特定条件下触发的警报，可使用以下形式：
   
    **insights alerts rule log set** *[options] &lt;ruleName&gt; &lt;location&gt; &lt;resourceGroup&gt; &lt;operationName&gt;*
   
    例如
   
    ```console
    azure insights alerts rule log set myActivityLogRule eastus myresourceGroupName Microsoft.Storage/storageAccounts/listKeys/action
    ```
   
    operationName 与活动日志中条目的事件类型相对应。 示例包括 Microsoft.Compute/virtualMachines/delete 和 microsoft.insights/diagnosticSettings/write。
   
    可以使用 PowerShell 命令 [Get AzureRmProviderOperation](https://msdn.microsoft.com/library/mt603720.aspx) 来获取可能 operationNames 的列表。 或者，可以使用 Azure 门户查询活动日志以及查找要为其设置警报的特定过去操作。 在友好名称的图形日志视图中显示操作。 查看该条目的 JSON，并拉取 OperationName 值。   
6. 可以验证是否已通过查看单个规则正确创建警报。
   
    ```console
    azure insights alerts rule list myresourcegroup --ruleName myrule
    ```
7. 若要删除规则，请使用以下格式的命令：
   
    **insights alerts rule delete** [options] &lt;resourceGroup&gt; &lt;ruleName&gt;
   
    这些命令将删除在本文前面创建的规则。
   
    ```console
    azure insights alerts rule delete myresourcegroup myrule
    azure insights alerts rule delete myresourcegroup myrulewithwebhookandemail
    azure insights alerts rule delete myresourcegroup myActivityLogRule
    ```

## <a name="next-steps"></a>后续步骤
* [获取 Azure 监视概述](monitoring-overview.md)，包括可收集和监视的信息的类型。
* 了解[在警报中配置 Webhook](insights-webhooks-alerts.md)的详细信息。
* 了解关于 [Azure 自动化 Runbook](../automation/automation-starting-a-runbook.md) 的详细信息。
* 获取[收集诊断日志概述](monitoring-overview-of-diagnostic-logs.md)以收集有关服务的详细高频率指标。
* 获取[指标集合概述](insights-how-to-customize-monitoring.md)以确保你的服务可用且响应迅速。




<!--HONumber=Nov16_HO3-->


