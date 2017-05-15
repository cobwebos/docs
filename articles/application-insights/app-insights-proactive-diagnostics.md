---
title: "Azure Application Insights 中的智能检测 | Microsoft Docs"
description: "Application Insights 执行应用遥测的自动深入分析，并在有潜在问题时发出警告。"
services: application-insights
documentationcenter: windows
author: rakefetj
manager: douge
ms.assetid: 2eeb4a35-c7a1-49f7-9b68-4f4b860938b2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 10/31/2016
ms.author: awills
ms.translationtype: Human Translation
ms.sourcegitcommit: 7c4d5e161c9f7af33609be53e7b82f156bb0e33f
ms.openlocfilehash: 0331a20d60fd856c0d852b26f125c47a3614f1ad
ms.contentlocale: zh-cn
ms.lasthandoff: 05/04/2017


---
# <a name="smart-detection-in-application-insights"></a>Application Insights 中的智能检测
 当你的 Web 应用程序中存在潜在性能问题时，智能检测会自动向你发出警告。 它会对应用发送至 [Application Insights](app-insights-overview.md) 的遥测数据执行主动分析。 如果失败率中存在骤升或者客户端或服务器性能中存在异常模式，你将收到警报。 此功能不需要任何配置。 它将在应用程序发送足够的遥测时运行。

可以从接收的电子邮件和“智能检测”边栏选项卡中访问智能检测警报。

## <a name="review-your-smart-detections"></a>查看智能检测
可通过以下两种方式发现检测：

* **收到来自 Application Insights 的电子邮件**。 下面是一个典型示例：
  
    ![电子邮件警报](./media/app-insights-proactive-diagnostics/03.png)
  
    单击大按钮以在门户中打开更多详细信息。
* 应用“概述”边栏选项卡上的“智能检测”磁贴会显示最近警报的计数。 单击磁贴以查看最近的警报列表。

![查看最近的检测](./media/app-insights-proactive-diagnostics/04.png)

选择警报以查看其详细信息。

## <a name="what-problems-are-detected"></a>检测到哪些问题？
有三种检测类型：

* [智能检测 - 失败异常](app-insights-proactive-failure-diagnostics.md)。 我们使用机器学习，通过与负载和其他因素相关为应用设置预期的失败请求速率。 如果失败率超出预期的包络线，我们将发送警报。
* [智能检测 - 性能异常](app-insights-proactive-performance-diagnostics.md)。 如果操作的响应时间或依赖关系的持续时间比历史基线慢，或者如果我们在响应时间或页面加载时间中识别到异常模式，你会收到通知。   
* [智能检测 - Azure 云服务问题](https://azure.microsoft.com/blog/proactive-notifications-on-cloud-service-issues-with-azure-diagnostics-and-application-insights/)。 如果应用托管在 Azure 云服务并且角色实例出现启动故障、频繁回收或运行时崩溃，你将收到警报。

（每个通知中的帮助链接可转到相关文章。）

## <a name="video"></a>视频

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>后续步骤
这些诊断工具可帮助你检查应用中的遥测：

* [指标资源管理器](app-insights-metrics-explorer.md)
* [搜索资源管理器](app-insights-diagnostic-search.md)
* [分析 - 功能强大的查询语言](app-insights-analytics-tour.md)

智能检测是完全自动执行的。 但或许你想要设置更多一些警报？

* [手动配置的指标警报](app-insights-alerts.md)
* [可用性 Web 测试](app-insights-monitor-web-app-availability.md) 


