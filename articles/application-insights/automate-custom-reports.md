---
title: 使用 Azure Application Insights 数据自动化自定义报表
description: 使用 Azure Application Insights 数据自动化自定义每日/每周/每月报表
services: application-insights
documentationcenter: ''
author: sdash
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: sdash
ms.openlocfilehash: 804e8c7a43d1ab16d11b6075be44599b33b46a3e
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/11/2018
ms.locfileid: "34072654"
---
# <a name="automate-custom-reports-with-azure-application-insights-data"></a>使用 Azure Application Insights 数据自动化自定义报表

定期报表有助于团队了解其业务关键型服务的状况。 开发人员、DevOps/SRE 团队及其管理人员可以通过自动化报表获得可靠的见解，而无需每个人都登录门户，从而提高工作效率。 此类报表还有助于识别可能不会触发任何警报规则的延迟、负载或故障率的逐渐增加趋势。

每个企业都有其独特的报告需求，例如： 

* 指标的特定百分位数聚合，或报告中的自定义指标。
* 为不同的受众提供不同的每日、每周和每月数据汇总报表。
* 按区域或环境等自定义属性进行分段。 
* 将一些 AI 资源集中在一个报告中，即使它们可能位于不同的订阅或资源组等。
* 包含已发送给选择性受众的敏感指标的单独报告。
* 向可能无权访问门户资源的利益干系人提供的报表。

> [!NOTE] 
> 每周 Application Insights 摘要电子邮件不允许任何自定义，并且将基于下面列出的自定义选项中止。 最后一封每周摘要电子邮件将于 2018 年 6 月 11 日发送。 请配置以下选项之一以获取类似的自定义报表（使用下面建议的查询）。

## <a name="to-automate-custom-report-emails"></a>自动化自定义报表电子邮件

可以[采用编程方式查询 Application Insights](https://dev.applicationinsights.io/) 数据，按计划生成自定义报表。 以下选项有助于快速开始：

* [使用 Microsoft Flow 自动化报表](app-insights-automate-with-flow.md)
* [使用逻辑应用自动化报表](automate-with-logic-apps.md)
* 在监视方案中使用“Application Insights 计划摘要”[Azure Function](https://azure.microsoft.com/services/functions/) 模板。 此函数使用 SendGrid 传递电子邮件。 

    ![Azure Function 模板](./media/automate-custom-reports/azure-function-template.png)

## <a name="sample-query-for-a-weekly-digest-email"></a>每周摘要电子邮件的示例查询
以下查询显示每周摘要电子邮件（如报表）的跨多个数据集联接情况。 根据需要对其进行自定义，并将其用于上面列出的选项，自动化每周报告。   

```AIQL
let period=7d;
requests
| where timestamp > ago(period)
| summarize Row = 1, TotalRequests = sum(itemCount), FailedRequests = sum(toint(success == 'False')),
    RequestsDuration = iff(isnan(avg(duration)), '------', tostring(toint(avg(duration) * 100) / 100.0))
| join (
dependencies
| where timestamp > ago(period)
| summarize Row = 1, TotalDependencies = sum(itemCount), FailedDependencies = sum(success == 'False'),
    DependenciesDuration = iff(isnan(avg(duration)), '------', tostring(toint(avg(duration) * 100) / 100.0))
) on Row | join (
pageViews
| where timestamp > ago(period)
| summarize Row = 1, TotalViews = sum(itemCount)
) on Row | join (
exceptions
| where timestamp > ago(period)
| summarize Row = 1, TotalExceptions = sum(itemCount)
) on Row | join (
availabilityResults
| where timestamp > ago(period)
| summarize Row = 1, OverallAvailability = iff(isnan(avg(toint(success))), '------', tostring(toint(avg(toint(success)) * 10000) / 100.0)),
    AvailabilityDuration = iff(isnan(avg(duration)), '------', tostring(toint(avg(duration) * 100) / 100.0))
) on Row
| project TotalRequests, FailedRequests, RequestsDuration, TotalDependencies, FailedDependencies, DependenciesDuration, TotalViews, TotalExceptions, OverallAvailability, AvailabilityDuration
```

  
## <a name="next-steps"></a>后续步骤

- 详细了解如何创建 [Analytics 查询](app-insights-analytics-using.md)。
- 详细了解[采用编程方式查询 Application Insights 数据](https://dev.applicationinsights.io/)
- 了解有关[逻辑应用](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps)的详细信息。
- 详细了解 [Microsoft Flow](https://ms.flow.microsoft.com)。


