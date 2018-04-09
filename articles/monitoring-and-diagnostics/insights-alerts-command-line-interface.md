---
title: 为 Azure 服务创建警报 - 跨平台 CLI | Microsoft 文档
description: 满足指定的条件时触发电子邮件、通知、调用网站 URL (Webhook) 或自动化。
author: rboucher
manager: carmonm
editor: ''
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
ms.openlocfilehash: 9ea7da35acefc139625e71904c8aa1b01b87e4df
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2018
---
# <a name="create-classic-metric-alerts-in-azure-monitor-for-azure-services---cross-platform-cli"></a>在 Azure Monitor 中为 Azure 服务创建经典指标警报 - 跨平台 CLI
> [!div class="op_single_selector"]
> * [门户](insights-alerts-portal.md)
> * [PowerShell](insights-alerts-powershell.md)
> * [CLI](insights-alerts-command-line-interface.md)
>
>

## <a name="overview"></a>概述
> [!NOTE]
> 本文介绍了如何创建较早的经典指标警报。 Azure Monitor 现在支持[较新、更好的指标警报](monitoring-near-real-time-metric-alerts.md)。 这些警报可监视多个指标，并允许对维度指标发出警报。 即将推出支持较新指标警报的 CLI。
>
>

本文介绍了如何使用跨平台命令行接口 (CLI) 设置 Azure 经典指标警报。

> [!NOTE]
> 自 2016 年 9 月 25 日起，“Azure Insights”更名为 Azure 监视器。 但是，命名空间及其下命令仍包含“insights”。
>
>

可以根据监控指标或事件接收 Azure 服务的警报。

* **指标值** - 指定指标的值超过在任一方向分配的阈值时，将触发警报。 也就是说，当首次满足条件时，以及之后不再满足条件时，都会触发此警报。    
* **活动日志事件** - 发生每个事件，或仅当出现特定事件时触发警报。 若要深入了解活动日志警报，请[单击此处](monitoring-activity-log-alerts.md)

可配置经典指标警报，使警报触发时执行以下操作：

* 向服务管理员和共同管理员发送电子邮件通知
* 将电子邮件发送到指定的其他电子邮件。
* 调用 Webhook
* 开始执行 Azure Runbook（当前仅从 Azure 门户）

可使用以下项配置和获取有关经典指标警报规则的信息

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

2. 若要列出资源组的现有规则，请使用以下形式，**azure insights alerts rule list** *[options] &lt;resourceGroup&gt;*

   ```console
   azure insights alerts rule list myresourcegroupname

   ```
3. 若要创建一条规则，首先需要有以下几条重要信息。
  * 要为其设置警报的资源的**资源 ID**
  * 资源可用的**指标定义**

     获取资源 ID 的一种方法是使用 Azure 门户。 假设已创建该资源，在门户中选中它。 然后，在下一个边栏选项卡中，选择“设置”部分下的“属性”。 *资源 ID* 是下一个边栏选项卡中的字段。 另一种方法是使用 [Azure Resource Explorer](https://resources.azure.com/)（Azure 资源浏览器）。

     Web 应用的示例资源 ID 是

     ```console
     /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename
     ```

     若要获取上一个资源示例的这些指标的可用指标和单位列表，请使用以下 CLI命令：  

     ```console
     azure insights metrics list /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename PT1M
     ```

     PT1M 是可用度量的粒度（1分钟间隔）。 使用不同的粒度可以提供不同的指标选项。
4. 若要创建基于指标的警报规则，请使用以下形式的命令：

    **azure insights alerts rule metric set** *[options] &lt;ruleName&gt; &lt;location&gt; &lt;resourceGroup&gt; &lt;windowSize&gt; &lt;operator&gt; &lt;threshold&gt; &lt;targetResourceId&gt; &lt;metricName&gt; &lt;timeAggregationOperator&gt;*

    以下示例设置了一个关于网站资源的警报。 如果其持续 5 分钟收到任何流量以及 5 分钟没收到任何流量，将触发该警报。

    ```console
    azure insights alerts rule metric set myrule eastus myreasourcegroup PT5M GreaterThan 2 /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename BytesReceived Total

    ```
5. 若要在经典指标警报触发时创建 webhook 或发送电子邮件，首先要创建电子邮件和/或 webhook。 然后立即创建规则。 如果已使用 CLI 创建规则，将无法关联 webhook 或电子邮件。

    ```console
    azure insights alerts actions email create --customEmails myemail@contoso.com

    azure insights alerts actions webhook create https://www.contoso.com

    azure insights alerts rule metric set myrulewithwebhookandemail eastus myreasourcegroup PT5M GreaterThan 2 /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename BytesReceived Total
    ```

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
* 详细了解[配置活动日志事件的警报](monitoring-activity-log-alerts.md)。
* 了解关于 [Azure 自动化 Runbook](../automation/automation-starting-a-runbook.md) 的详细信息。
* 获取[收集诊断日志概述](monitoring-overview-of-diagnostic-logs.md)以收集有关服务的详细高频率指标。
* 获取[指标集合概述](insights-how-to-customize-monitoring.md)以确保服务可用且响应迅速。
