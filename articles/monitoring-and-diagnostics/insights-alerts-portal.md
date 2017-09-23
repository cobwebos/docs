---
title: "为 Azure 服务创建警报 - Azure 门户 | Microsoft 文档"
description: "满足指定的条件时触发电子邮件、通知、调用网站 URL (Webhook) 或自动化。"
author: rboucher
manager: carmonm
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: f7457655-ced6-4102-a9dd-7ddf2265c0e2
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/23/2016
ms.author: robb
ms.translationtype: Human Translation
ms.sourcegitcommit: f41fbee742daf2107b57caa528e53537018c88c6
ms.openlocfilehash: 745a9c016bd037f1051025a2c5a468c3935e4550
ms.contentlocale: zh-cn
ms.lasthandoff: 03/31/2017

---
# <a name="create-metric-alerts-in-azure-monitor-for-azure-services---azure-portal"></a>在 Azure Monitor 中为 Azure 服务创建指标警报 - Azure 门户
> [!div class="op_single_selector"]
> * [门户](insights-alerts-portal.md)
> * [PowerShell](insights-alerts-powershell.md)
> * [CLI](insights-alerts-command-line-interface.md)
>
>

## <a name="overview"></a>概述
本文演示如何使用 Azure 门户设置 Azure 指标警报。   

可以根据监视指标或事件接收 Azure 服务的警报。

* **指标值** - 指定指标的值超过在任一方向分配的阈值时，将触发警报。 也就是说，在以下两种情况下会触发警报：首先是满足条件时，然后是满足条件后，不再满足条件时。    
* **活动日志事件** - 发生每个事件，或仅当出现特定事件时触发警报。 若要深入了解活动日志警报，请[单击此处](monitoring-activity-log-alerts.md)

可配置指标警报，使警报触发时执行以下操作：

* 向服务管理员和共同管理员发送电子邮件通知
* 将电子邮件发送到指定的其他电子邮件。
* 调用 Webhook
* 开始执行 Azure Runbook（仅从 Azure 门户）

可使用以下项配置指标并获取有关指标警报规则的信息

* [Azure 门户](insights-alerts-portal.md)
* [PowerShell](insights-alerts-powershell.md)
* [命令行接口 (CLI)](insights-alerts-command-line-interface.md)
* [Azure 监视器 REST API](https://msdn.microsoft.com/library/azure/dn931945.aspx)

## <a name="create-an-alert-rule-on-a-metric-with-the-azure-portal"></a>使用 Azure 门户创建指标的警报规则
1. 在此[门户](https://portal.azure.com/)，查找想要监视的资源并选中它。

2. 在“监视”部分下，选择“警报”或“警报规则”。 对于不同的资源，文本和图标可能会略有不同。  

    ![监视](./media/insights-alerts-portal/AlertRulesButton.png)

3. 选择“添加通知”命令，并填写字段。

    ![添加警报](./media/insights-alerts-portal/AddAlertOnlyParamsPage.png)

4. **命名**警报规则，并选择也在通知电子邮件中显示的“说明”。

5. 选择想要监视的“指标”为该指标选择一个“条件”和“阈值”。 还选择了触发警报前指标规则必须满足的时间**段**。 例如，如果使用时间段"PT5M"，且警报针对 CPU 高于 80% 的情况，则 CPU 持续高于 80% 达到 5 分钟时触发警报。 第一次触发后，CPU 5 分钟内持续低于 80% 时将再次触发警报。 每 1 分钟对 CPU 进行一次测量。   

6. 如果触发警报时希望向管理员和共同管理员发送电子邮件，则选择“向所有者发送电子邮件...”。

7. 触发警报时，如果希望其他电子邮件收到通知，请将其添加到“其他管理员电子邮件”字段下。 用分号隔开多个电子邮件 - *email@contoso.com;email2@contoso.com*

8. 触发警报时，如果希望调用有效的 URI，请将其放入“Webhook”字段。

9. 如果使用 Azure 自动化时，则触发警报时可选择要运行的 Runbook。

10. 警报创建完成后，选择“确定”。   

几分钟后，警报将处于活动状态，并按前面所述进行触发。

## <a name="managing-your-alerts"></a>管理你的警报
创建警报后，可选择它并：

* 查看显示指标的阈值和前一天实际值的关系图。
* 编辑或删除其。
* 如果想要暂时停止或恢复接收该警报的通知，可**禁用**或**启用**它。

## <a name="next-steps"></a>后续步骤
* [获取 Azure 监视概述](monitoring-overview.md)，包括可收集和监视的信息的类型。
* 了解[在警报中配置 Webhook](insights-webhooks-alerts.md)的详细信息。
* 详细了解[配置活动日志事件的警报](monitoring-activity-log-alerts.md)。
* 了解关于 [Azure 自动化 Runbook](../automation/automation-starting-a-runbook.md) 的详细信息。
* 获取[诊断日志概述](monitoring-overview-of-diagnostic-logs.md)收集有关服务的详细高频率指标。
* 获取[指标集合概述](insights-how-to-customize-monitoring.md)以确保你的服务可用且响应迅速。

