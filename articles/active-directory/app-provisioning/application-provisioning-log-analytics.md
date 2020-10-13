---
title: 了解预配如何与 Azure Active Directory 中 Azure Monitor 日志集成。
description: 了解预配如何与 Azure Active Directory 中 Azure Monitor 日志集成。
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: conceptual
ms.workload: identity
ms.date: 10/12/2020
ms.author: kenwith
ms.reviewer: arvinh,luleon
ms.openlocfilehash: 4e14bd6365ce53d98d6e0b0d1f2601ff3b3e59b4
ms.sourcegitcommit: 83610f637914f09d2a87b98ae7a6ae92122a02f1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2020
ms.locfileid: "91997197"
---
# <a name="understand-how-provisioning-integrates-with-azure-monitor-logs"></a>了解预配如何与 Azure Monitor 日志集成

预配与 Azure Monitor 日志和 Log Analytics 集成。 借助 Azure 监视，你可以执行创建工作簿（也称为仪表板）的操作，将预配日志存储30天以上，并创建自定义查询和警报。 本文介绍如何将设置日志与 Azure Monitor 日志集成。 若要详细了解预配日志的工作原理，请参阅 [设置日志](../reports-monitoring/concept-provisioning-logs.md)。

## <a name="enabling-provisioning-logs"></a>启用预配日志

你应该已熟悉 Azure 监视和 Log Analytics。 如果没有，请跳过了解相关信息，然后返回了解应用程序预配日志。 若要了解有关 Azure 监视的详细信息，请参阅 [Azure Monitor 概述](../../azure-monitor/overview.md)。 若要详细了解 Azure Monitor 日志和 Log Analytics，请参阅 [Azure Monitor 中的日志查询概述](../../azure-monitor/log-query/log-query-overview.md)。

配置 Azure 监视后，可以启用应用程序预配的日志。 选项位于 " **诊断设置** " 页上。

:::image type="content" source="media/application-provisioning-log-analytics/diagnostic-settings.png" alt-text="访问诊断设置" lightbox="media/application-provisioning-log-analytics/diagnostic-settings.png":::

:::image type="content" source="media/application-provisioning-log-analytics/enable-log-analytics.png" alt-text="访问诊断设置" lightbox="media/application-provisioning-log-analytics/enable-log-analytics.png":::

> [!NOTE]
> 如果刚刚预配了工作区，可能需要一段时间才能向其发送日志。 如果收到一条错误消息，指出订阅未注册为使用 *microsoft insights* ，请在几分钟后返回。
 
## <a name="understanding-the-data"></a>了解数据
预配的基础数据流几乎完全相同。 Azure Monitor 日志获取与 Azure 门户 UI 和 Azure API 几乎相同的流。 在下表中所述的日志字段中只有几个 **差异** 。 若要了解有关这些字段的详细信息，请参阅 [List provisioningObjectSummary](https://docs.microsoft.com/graph/api/provisioningobjectsummary-list?view=graph-rest-beta&tabs=http&preserve-view=true)。

|Azure Monitor 日志   |Azure 门户 UI   |Azure API |
|----------|-----------|------------|
|errorDescription |reason |resultDescription |
|status |resultType |resultType |
|activityDateTime |TimeGenerated |TimeGenerated |


## <a name="azure-monitor-workbooks"></a>Azure Monitor 工作簿

Azure Monitor 工作簿提供了一个灵活的数据分析画布。 它们还提供了在 Azure 门户中创建丰富视觉对象报表的功能。 若要了解详细信息，请参阅 [Azure Monitor 工作簿概述](../../azure-monitor/platform/workbooks-overview.md)。

应用程序预配附带一组预建的工作簿。 您可以在 "工作簿" 页上找到它们。 若要查看数据，需确保填充 (timeRange、jobID、appName) 的所有筛选器。 你还需要确保已预配应用，否则日志中不会包含任何数据。

:::image type="content" source="media/application-provisioning-log-analytics/workbooks.png" alt-text="访问诊断设置" lightbox="media/application-provisioning-log-analytics/workbooks.png":::

:::image type="content" source="media/application-provisioning-log-analytics/report.png" alt-text="访问诊断设置" lightbox="media/application-provisioning-log-analytics/report.png":::

## <a name="custom-queries"></a>自定义查询

可以在 Azure 仪表板上创建自定义查询并显示数据。 若要了解如何操作，请参阅 [创建和共享 Log Analytics 数据的仪表板](../../azure-monitor/log-query/get-started-queries.md)。 此外，请务必查看 [Azure Monitor 中的日志查询的概述](../../azure-monitor/log-query/log-query-overview.md)。

下面是一些用于开始应用程序预配的示例。

根据源系统中的 ID 查询用户 a 的日志：
```kusto
AADProvisioningLogs
| extend SourceIdentity = parse_json(SourceIdentity)
| where tostring(SourceIdentity.Id) == "49a4974bb-5011-415d-b9b8-78caa7024f9a"
```

按 ErrorCode 汇总计数：
```kusto
AADProvisioningLogs
| summarize count() by ErrorCode = ResultSignature
```

按操作汇总每天事件计数：
```kusto
AADProvisioningLogs
| where TimeGenerated > ago(7d)
| summarize count() by Action, bin(TimeGenerated, 1d)
```

采用100事件和项目键属性：
```kusto
AADProvisioningLogs
| extend SourceIdentity = parse_json(SourceIdentity)
| extend TargetIdentity = parse_json(TargetIdentity)
| extend ServicePrincipal = parse_json(ServicePrincipal)
| where tostring(SourceIdentity.identityType) == "Group"
| project tostring(ServicePrincipal.Id), tostring(ServicePrincipal.Name), ModifiedProperties, JobId, Id, CycleId, ChangeId, Action, SourceIdentity.identityType, SourceIdentity.details, TargetIdentity.identityType, TargetIdentity.details, ProvisioningSteps
|take 100
```

## <a name="custom-alerts"></a>自定义警报

Azure Monitor 使你可以配置自定义警报，以便可以收到有关与预配相关的关键事件的通知。 例如，你可能想要在失败高峰收到警报。 如果禁用或删除，可能会出现高峰。 可能需要发出警报的另一个示例是，缺少任何预配，这表明出现了问题。

若要了解有关警报的详细信息，请参阅 [响应带有 Azure Monitor 警报的事件](../../azure-monitor/learn/tutorial-response.md)。

当出现故障高峰时发出警报。 将 jobID 替换为应用程序的 jobID。

:::image type="content" source="media/application-provisioning-log-analytics/alert1.png" alt-text="访问诊断设置" lightbox="media/application-provisioning-log-analytics/alert1.png":::

可能存在导致预配服务停止运行的问题。 使用以下警报来检测在给定时间间隔内没有预配事件的时间。

:::image type="content" source="media/application-provisioning-log-analytics/alert2.png" alt-text="访问诊断设置" lightbox="media/application-provisioning-log-analytics/alert2.png":::

禁用或删除中的峰值时发出警报。

:::image type="content" source="media/application-provisioning-log-analytics/alert3.png" alt-text="访问诊断设置" lightbox="media/application-provisioning-log-analytics/alert3.png":::


## <a name="community-contributions"></a>社区参与

我们要为应用程序设置查询和仪表板采用开源和基于社区的方法。 如果你构建了你认为其他人会发现有用的查询、警报或工作簿，请务必将其发布到 [AzureMonitorCommunity GitHub](https://github.com/microsoft/AzureMonitorCommunity)存储库。 然后，使用链接向我们发送一封电子邮件。 我们会查看并将其发布到服务，以便其他人也能受益。 可以通过 provisioningfeedback@microsoft.com 联系我们。

## <a name="next-steps"></a>后续步骤

- [Log analytics](../reports-monitoring/howto-analyze-activity-logs-log-analytics.md)
- [Azure Monitor 日志查询入门](../../azure-monitor/log-query/get-started-queries.md)
- [在 Azure 门户中创建和管理器警报组](../../azure-monitor/platform/action-groups.md)
- [安装和使用用于 Azure Active Directory 的日志分析视图](../reports-monitoring/howto-install-use-log-analytics-views.md)
- [预配日志 API](https://docs.microsoft.com/graph/api/resources/provisioningobjectsummary?view=graph-rest-beta.md&preserve-view=true)
