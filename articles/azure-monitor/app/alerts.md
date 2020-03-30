---
title: 在 Azure 应用程序见解中设置警报
description: 响应速度变慢、发生异常以及 Web 应用中出现其他性能或用法变化时接收通知。
ms.topic: conceptual
ms.date: 01/23/2019
ms.reviewer: lagayhar
ms.subservice: alerts
ms.openlocfilehash: 28fd59556a586b85a6d3caf188d9e02c11d31e3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295080"
---
# <a name="set-alerts-in-application-insights"></a>在 Application Insights 中设置警报

当 Web 应用中的性能或用法指标发生变化时，[Azure Application Insights][start] 可发出警报。 

Application Insights 在[各种平台][platforms]上监视实时应用，帮助诊断性能问题和了解使用模式。

有多种类型的警报：

* [**指标警报**](../../azure-monitor/platform/alerts-metric-overview.md)告诉您指标在某些时间段内何时超过阈值 -例如响应时间、异常计数、CPU 使用率或页面视图。
* [**日志警报**](../../azure-monitor/platform/alerts-unified-log.md)用于描述警报，其中的警报信号基于自定义的 Kusto 查询。
* [**Web 测试**][availability]：当站点在 Internet 上不可用或响应缓慢时，它会向用户发送通知。 [了解详情][availability]。
* [**主动诊断**](../../azure-monitor/app/proactive-diagnostics.md)：由系统自动配置，通知出现了异常的性能模式。

## <a name="how-to-set-an-exception-alert-using-custom-log-search"></a>如何使用自定义日志搜索设置异常警报

本部分介绍如何设置基于查询的异常警报。 本示例假设我们希望在过去 24 小时失败率超过 10% 时发出警报。

1. 在 Azure 门户中转到你的 Application Insights 资源。
2. 在左侧的“配置”下单击“警报”。****

    ![在左侧的“配置”下单击“警报”](./media/alerts/1appinsightalert.png)

3. 在“警报”选项卡的顶部选择“新建警报规则”。****

     ![在“警报”选项卡的顶部单击“新建警报规则”](./media/alerts/2createalert.png)

4. 此时应会自动选择你的资源。 若要设置条件，请单击“添加条件”。****

    ![单击“添加条件”](./media/alerts/3addcondition.png)

5. 在“配置信号逻辑”选项卡中选择“自定义日志搜索”。****

    ![单击“自定义日志搜索”](./media/alerts/4customlogsearch.png)

6. 在“自定义日志搜索”选项卡上的“搜索查询”框中输入查询。 本示例使用以下 Kusto 查询。
    ```kusto
    let percentthreshold = 10;
    let period = 24h;
    requests
    | where timestamp >ago(period)
    | summarize requestsCount = sum(itemCount)
    | project requestsCount, exceptionsCount = toscalar(exceptions | where timestamp >ago(period) | summarize sum(itemCount))
    | extend exceptionsRate = toreal(exceptionsCount)/toreal(requestsCount) * 100
    | where exceptionsRate > percentthreshold

    ```

    ![在搜索查询框中键入查询](./media/alerts/5searchquery.png)
    
    > [!NOTE]
    > 还可将这些步骤应用于其他类型的基于查询的警报。 可在此 [Kusto 入门文档](https://docs.microsoft.com/azure/kusto/concepts/)或此 [SQL to Kusto 速查表](https://docs.microsoft.com/azure/kusto/query/sqlcheatsheet)中详细了解 Kusto 查询语言

7. 在“警报逻辑”下，选择逻辑是基于结果数还是指标度量值。 然后选择条件（大于、等于、小于）和阈值。 更改这些值时，可以注意到条件预览句子会发生变化。 本示例使用“等于”。

    ![在“警报逻辑”下，选择根据所选依据和条件提供的选项，然后键入阈值](./media/alerts/6alertlogic.png)

8. 在“评估依据”下，设置时段和频率。 此处的时段必须与在上述查询中输入的时段值匹配。 然后单击 **"完成**"。

    ![在底部设置时段和频率，然后单击“完成”](./media/alerts/7evaluate.png)

9. 现在，我们看到创建的条件和每月估计费用。 在“[操作组](../platform/action-groups.md)”下，可以创建新组，或选择现有的组。 如果需要，可以自定义操作。

    ![单击“操作组”下的“选择”或“创建”按钮](./media/alerts/8actiongroup.png)

10. 最后，请添加警报详细信息（警报规则名称、说明、严重性）。 完成后，单击底部的“创建警报规则”。****

    ![在“警报详细信息”下键入警报规则名称，编写说明，然后选择严重性](./media/alerts/9alertdetails.png)

## <a name="how-to-unsubscribe-from-classic-alert-e-mail-notifications"></a>如何取消订阅经典警报电子邮件通知

本部分适用于**经典可用性警报**、**经典 Application Insights 指标警报**和**经典失败异常警报**。

如果存在以下任一情况，你收到这些经典警报的电子邮件通知：

* 你的电子邮件地址已列在警报规则设置中的“通知电子邮件收件人”字段内。

* 用于将电子邮件通知发送到持有特定订阅角色的用户的选项已激活，而你正好持有该特定 Azure 订阅的相应角色。

![警报通知屏幕截图](./media/alerts/alert-notification.png)

为了更好地控制安全和隐私，我们一般建议在“通知电子邮件收件人”字段中显式指定经典警报的通知收件人。**** 用于向持有特定角色的所有用户发送通知的选项是为了实现后向兼容而提供的。

若要取消订阅警报规则生成的电子邮件通知，请从“通知电子邮件收件人”字段中删除你的电子邮件地址。****

如果你的电子邮件地址未显式列出，我们建议禁用该选项以自动通知特定角色的所有成员，并在“警报规则收件人”字段中列出需要接收该警报规则的通知的所有用户电子邮件。

## <a name="who-receives-the-classic-alert-notifications"></a>谁会收到（经典）警报通知？

本节仅适用于经典警报，并将帮助优化警报通知以确保只有预期的接收人能收到通知。 要了解有关[经典警报](../platform/alerts-classic.overview.md)和新警报体验之间的区别的详细信息，请参阅[警报概述文章](../platform/alerts-overview.md)。 要控制新警报体验中的警报通知，请使用[操作组](../platform/action-groups.md)。

* 建议将经典警报通知用于特定接收人。

* 对于有关任何 Application Insights 指标（包括可用性指标）的警报，如果启用“批/组”复选框选项，则会发送给订阅中具有所有者、参与者或读者角色的用户****。 实际上，可以访问包含 Application Insights 资源在内的订阅的所有用户均会收到通知__。

> [!NOTE]
> 如果当前使用“批/组”复选框选项并禁用它，则无法还原更改****。

如果需要根据用户角色通知用户，请使用新的警报体验/近实时警报。 使用[操作组](../platform/action-groups.md)，可以为具有任何参与者/所有者/读者角色（未融合为单一选项）的用户配置电子邮件通知。

## <a name="automation"></a>自动化
* [使用 PowerShell 自动设置警报](../../azure-monitor/app/powershell-alerts.md)
* [使用 Webhook 自动响应警报](../../azure-monitor/platform/alerts-webhooks.md)

## <a name="see-also"></a>请参阅
* [可用性 Web 测试](../../azure-monitor/app/monitor-web-app-availability.md)
* [自动设置警报](../../azure-monitor/app/powershell-alerts.md)
* [主动诊断](../../azure-monitor/app/proactive-diagnostics.md) 

<!--Link references-->

[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[client]: ../../azure-monitor/app/javascript.md
[platforms]: ../../azure-monitor/app/platforms.md
[roles]: ../../azure-monitor/app/resources-roles-access-control.md
[start]: ../../azure-monitor/app/app-insights-overview.md

