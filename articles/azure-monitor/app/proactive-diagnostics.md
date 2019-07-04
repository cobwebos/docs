---
title: Azure Application Insights 中的智能检测 | Microsoft Docs
description: Application Insights 执行应用遥测的自动深入分析，并在有潜在问题时发出警告。
services: application-insights
documentationcenter: windows
author: mrbullwinkle
manager: carmonm
ms.assetid: 2eeb4a35-c7a1-49f7-9b68-4f4b860938b2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: mbullwin
ms.openlocfilehash: 8ee2dea364253d871d5624242d15d8a81ab6f08f
ms.sourcegitcommit: c63e5031aed4992d5adf45639addcef07c166224
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2019
ms.locfileid: "67465898"
---
# <a name="smart-detection-in-application-insights"></a>Application Insights 中的智能检测
 智能检测自动向你发出警告潜在的性能问题和失败在 web 应用程序中的异常。 它会对应用发送至 [Application Insights](../../azure-monitor/app/app-insights-overview.md) 的遥测数据执行主动分析。 如果失败率中存在骤升或者客户端或服务器性能中存在异常模式，将收到警报。 此功能不需要任何配置。 它会在应用程序发送足够的遥测时运行。

您可以访问检测从你收到的电子邮件和智能检测边栏选项卡中颁发的智能检测。

## <a name="review-your-smart-detections"></a>查看智能检测
可通过以下两种方式发现检测：

* **收到来自 Application Insights 的电子邮件**。 下面是一个典型示例：
  
    ![电子邮件警报](./media/proactive-diagnostics/03.png)
  
    单击大按钮以在门户中打开更多详细信息。
* **在智能检测边栏选项卡**Application Insights 中。 选择**智能检测**下**调查**菜单以查看最近的检测的列表。

![查看最近的检测](./media/proactive-diagnostics/04.png)

选择检测以查看其详细信息。

## <a name="what-problems-are-detected"></a>检测到哪些问题？
智能检测会检测并向通知有关各种问题，例如：

* [智能检测 - 失败异常](../../azure-monitor/app/proactive-failure-diagnostics.md)。 我们使用机器学习，通过与负载和其他因素相关为应用设置预期的失败请求速率。 如果失败率超出预期的包络线，我们将发送警报。
* [智能检测 - 性能异常](../../azure-monitor/app/proactive-performance-diagnostics.md)。 如果操作的响应时间或依赖关系的持续时间比历史基线慢，或者如果我们在响应时间或页面加载时间中识别到异常模式，会收到通知。   
* 常规性能下降和问题，如[跟踪下降](https://docs.microsoft.com/azure/azure-monitor/app/proactive-trace-severity)，[内存泄漏](https://docs.microsoft.com/azure/azure-monitor/app/proactive-potential-memory-leak)，[异常容量增加异常](https://docs.microsoft.com/azure/azure-monitor/app/proactive-exception-volume)和[安全反模式](https://docs.microsoft.com/azure/azure-monitor/app/proactive-application-security-detection-pack).

（每个通知中的帮助链接可转到相关文章。）

## <a name="smart-detection-email-notifications"></a>智能检测电子邮件通知

所有的智能检测规则，但规则标记为除外_预览版_，默认情况下找到检测时发送电子邮件通知配置。

可以通过打开智能检测“设置”  边栏选项卡并选择规则（这将打开“编辑规则”  边栏选项卡）来为特定的智能检测规则配置电子邮件通知。

另外，还可以使用 Azure 资源管理器模板来更改配置。 有关更多详细信息，请参阅[使用 Azure 资源管理器模板管理 Application Insights 智能检测规则](https://docs.microsoft.com/azure/azure-monitor/app/proactive-arm-config)。

## <a name="video"></a>视频

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>后续步骤
这些诊断工具可帮助检查应用中的遥测数据：

* [指标资源管理器](../../azure-monitor/app/metrics-explorer.md)
* [搜索资源管理器](../../azure-monitor/app/diagnostic-search.md)
* [分析 - 功能强大的查询语言](../../azure-monitor/log-query/get-started-portal.md)

智能检测是完全自动执行的。 但是或许你想要设置更多的警报？

* [手动配置的指标警报](../../azure-monitor/app/alerts.md)
* [可用性 Web 测试](../../azure-monitor/app/monitor-web-app-availability.md) 

