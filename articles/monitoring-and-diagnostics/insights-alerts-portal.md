---
title: 使用 Azure 门户为 Azure 服务创建经典警报 | Microsoft Docs
description: 满足指定的条件时触发电子邮件或通知、或调用网站 URL (Webhook) 或自动化。
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/23/2016
ms.author: robb
ms.component: alerts
ms.openlocfilehash: d0e5512eb3f963898ded6d7155f8b75cfb6ef911
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/20/2018
ms.locfileid: "36287423"
---
# <a name="use-the-azure-portal-to-create-classic-metric-alerts-in-azure-monitor-for-azure-services"></a>使用 Azure 门户在 Azure Monitor 中为 Azure 服务创建经典指标警报 

> [!div class="op_single_selector"]
> * [门户](insights-alerts-portal.md)
> * [PowerShell](insights-alerts-powershell.md)
> * [CLI](insights-alerts-command-line-interface.md)
>

> [!NOTE]
> 本文介绍了如何创建旧式经典指标警报。 Azure Monitor 现在支持[较新的指标警报](monitoring-near-real-time-metric-alerts.md)。 


本文演示如何使用 Azure 门户设置 Azure 经典指标警报。 

可以根据 Azure 服务的指标接收警报，也可以接收有关 Azure 中发生的事件的警报。

* **指标值**：当指定指标的值超过在任一方向分配的阈值时，将触发警报。 也就是说，当条件先是满足以及之后不再满足该条件时，警报都会触发。    

* **活动日志事件**：发生每个事件，或当出现特定事件时可触发警报。 详细了解[活动日志警报](monitoring-activity-log-alerts.md)。

可配置经典指标警报，使警报触发时执行以下操作：

* 向服务管理员和共同管理员发送电子邮件通知。
* 将电子邮件发送到指定的其他电子邮件地址。
* 调用 Webhook。
* 开始执行 Azure Runbook（仅从 Azure 门户）。

可以从以下位置配置和获取有关经典指标警报规则的信息： 

* [Azure 门户](insights-alerts-portal.md)
* [PowerShell](insights-alerts-powershell.md)
* [Azure CLI](insights-alerts-command-line-interface.md)
* [Azure 监视器 REST API](https://msdn.microsoft.com/library/azure/dn931945.aspx)

## <a name="create-an-alert-rule-on-a-metric-with-the-azure-portal"></a>使用 Azure 门户创建指标的警报规则
1. 在[门户](https://portal.azure.com/)中，找到要监视的资源，然后选择它。

2. 在“监视”部分中，选择“警报(经典)”。 对于不同的资源，文本和图标可能会略有不同。 如果在此处未找到“警报(经典)”，则它可能位于“警报”或“警报规则”中。

    ![监视](./media/insights-alerts-portal/AlertRulesButton.png)

3. 选择“添加指标警报(经典)”命令，并填写字段。

    ![添加警报](./media/insights-alerts-portal/AddAlertOnlyParamsPage.png)

4. **命名**警报规则。 然后选择也在通知电子邮件中显示的“说明”。

5. 选择要监视的指标。 然后为该指标选择“条件”和“阈值”值。 还选择触发警报前指标规则必须满足的时间段。 例如，如果使用时间段“过去 5 分钟”，且警报针对 CPU 高于 80% 的情况，则 CPU 持续高于 80% 达到 5 分钟时将触发警报。 第一次触发后，CPU 5 分钟内持续低于 80% 时会再次触发警报。 CPU 指标度量每分钟进行一次。

6. 如果希望在触发警报时管理员和共同管理员收到电子邮件通知，则选择“向所有者发送电子邮件...”。

7. 如果希望在触发警报时向其他电子邮件发送通知，请将其添加到“其他管理员电子邮件”字段。 使用分号分隔多个电子邮件，格式如下：*email@contoso.com; email2@contoso.com*

8. 如果希望在触发警报时调用有效的 URI，请将其放入“Webhook”字段中。

9. 如果使用 Azure 自动化时，则可选择在触发警报时要运行的 Runbook。

10. 选择“确定”以创建警报。   

几分钟后，警报将处于活动状态，并按前面所述进行触发。

## <a name="manage-your-alerts"></a>管理警报
创建警报后，可以选择它并执行以下任务之一：

* 查看显示指标阈值和前一天实际值的关系图。
* 编辑或删除其。
* 如果想要暂时停止或恢复接收该警报的通知，可**禁用**或**启用**它。

## <a name="next-steps"></a>后续步骤
* [获取 Azure 监视概述](monitoring-overview.md)，包括可收集和监视的信息的类型。
* 详细了解[新型指标警报](monitoring-near-real-time-metric-alerts.md)。
* 了解[在警报中配置 Webhook](insights-webhooks-alerts.md)的详细信息。
* 详细了解如何[配置针对活动日志事件的警报](monitoring-activity-log-alerts.md)。
* 详细了解 [Azure 自动化 Runbook](../automation/automation-starting-a-runbook.md)。
* 获取[诊断日志概述](monitoring-overview-of-diagnostic-logs.md)，并收集有关服务的详细高频率指标。
* 获取[指标收集概述](insights-how-to-customize-monitoring.md)，以确保服务可用且响应迅速。
