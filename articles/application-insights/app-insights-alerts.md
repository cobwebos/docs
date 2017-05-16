---
title: "在 Azure Application Insights 中设置警报 | Microsoft Docs"
description: "响应速度变慢、发生异常以及 Web 应用中出现其他性能或用法变化时接收通知。"
services: application-insights
documentationcenter: 
author: alancameronwills
manager: carmonm
ms.assetid: f8ebde72-f819-4ba5-afa2-31dbd49509a5
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: cfreeman
ms.translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 04965375fc94fc1aa8b1c48deb743bb1d0cf1c26
ms.contentlocale: zh-cn
ms.lasthandoff: 03/21/2017


---
# <a name="set-alerts-in-application-insights"></a>在 Application Insights 中设置警报
当 Web 应用中的性能或用法指标发生变化时，[Azure Application Insights][start] 可发出警报。 

Application Insights 在[各种平台][platforms]上监视实时应用，帮助诊断性能问题和了解使用模式。

有三种类型的警报：

* **指标警报**：某一指标（例如响应时间、异常计数、CPU 使用率或页面视图）超过某个阈值有一些时间时，此类警报会发出通知。 
* [**Web 测试**][availability]：当站点在 Internet 上不可用或响应缓慢时，它会向用户发送通知。 [了解详细信息][availability]。
* [**主动诊断**](app-insights-proactive-diagnostics.md)：由系统自动配置，通知出现了异常的性能模式。

本文重点介绍指标警报。

## <a name="set-a-metric-alert"></a>设置指标警报
打开“警报规则”边栏选项卡，然后使用“添加”按钮。 

![在“警报规则”边栏选项卡中，选择“添加警报”。 将应用设置为要测量的资源，提供警报的名称，然后选择指标。](./media/app-insights-alerts/01-set-metric.png)

* 在其他属性之前设置资源。 如果想要针对性能或用法指标设置警报，请**选择“(组件)”资源**。
* 为警报指定的名称必须在资源组中（不只是在应用程序中）唯一。
* 请仔细留意系统要求输入阈值所采用的单位。
* 如果选中“电子邮件所有者...”框，系统会通过电子邮件将警报发送到有权访问此资源组的每个人。 若要增加收件人的人数，请将他们添加到[资源组或订阅](app-insights-resources-roles-access-control.md)（而不是资源）。
* 如果指定“其他电子邮件”，系统会将警报发送到这些个人或组（无论是否选中“电子邮件所有者...”框）。 
* 如果已设置响应警报的 Web 应用，请设置 [Webhook 地址](../monitoring-and-diagnostics/insights-webhooks-alerts.md)。 当警报激活（即触发）时，以及警报得到解决时，系统会调用此地址。 （但请注意，查询参数不会以 Webhook 属性的形式传递。）
* 可以禁用或启用警报：使用边栏选项卡顶部的相应按钮即可。

*我看不到“添加警报”按钮。* 

* 使用的是组织帐户？ 如果对此应用程序资源拥有所有者或参与者访问权限，则可以设置警报。 请查看“访问控制”边栏选项卡。 [了解访问控制][roles]。

> [!NOTE]
> 在“警报”边栏选项卡中，可以看到已设置了一个警报：“主动诊断”。[](app-insights-proactive-failure-diagnostics.md) 这是一个自动警报，可监视一个特定的指标：请求失败率。 除非决定禁用主动警报，否则无需针对请求失败率设置自己的警报。 
> 
> 

## <a name="see-your-alerts"></a>查看警报
当警报在“非活动”和“活动”之间切换状态时，你将收到电子邮件。 

每个警报的当前状态显示在“警报规则”边栏选项卡中。

警报下拉列表中包含最近活动的摘要：

![警报下拉列表](./media/app-insights-alerts/010-alert-drop.png)

状态更改历史记录在活动日志中提供：

![在“概述”边栏选项卡中，单击“设置”、“审核日志”](./media/app-insights-alerts/09-alerts.png)

## <a name="how-alerts-work"></a>警报的工作原理
* 警报有三种状态：“永不激活”、“已激活”和“已解决”。 “已激活”表示指定的条件在上次评估时为 true。
* 警报状态更改时，将生成通知。 （如果警报条件在创建警报时已为 true，则在条件变为 false 之前，你可能收不到通知。）
* 如果已选中“电子邮件”框或提供了电子邮件地址，每条通知都会生成一封电子邮件。 也可以查看“通知”下拉列表。
* 每次出现指标都会评估警报，否则不会评估。
* 将评估聚合前一时间段的指标，然后将其与阈值进行比较，确定新状态。
* 选择的时间段指定了聚合指标的间隔。 它不影响评估警报的频率：评估频率取决于指标出现的频率。
* 如果在一段时间内特定指标的数据未出现，该间隙对警报评估以及指标资源管理器中的图表会产生不同的影响。 在指标资源管理器中，如果未看到数据的时间超过图表的采样间隔时间，图表将显示 0 值。 但是，基于相同指标的警报不会重新评估，并且警报的状态将保持不变。 
  
    当数据最终出现时，图表将跳回到非零值。 警报根据指定时间段的可用数据进行评估。 如果新数据点是该时间段内唯一可用的数据点，将根据该数据点聚合。
* 即使设置的时间段较长，警报也可能在警报与正常状态之间经常变动。 如果指标值徘徊在阈值附近，则可能发生这种情况。 阈值中没有滞后：过渡到警报状态时的值与过渡到正常状态时的值相同。

## <a name="what-are-good-alerts-to-set"></a>如何设置合理的警报？
这取决于应用程序。 一开始，最好不要设置太多指标。 花点时间查看应用运行时的指标图表，了解正常运行时的情况。 这可以帮助找到改善应用性能的方式。 然后再设置警报，在指标偏离正常区域时接收通知。 

常用的警报包括：

* [浏览器指标][client]（尤其是浏览器**页面加载时间**）非常适合用于 web 应用程序。 如果页面包含大量脚本，应该留意**浏览器异常**。 若要获取这些指标和警报，必须设置[网页监视][client]。
* **服务器响应时间**适合用于 Web 应用程序的服务器端。 还可以设置警报来注意此指标，确定高请求率是否不按比例变化：这可能表示应用资源不足。 
* **服务器异常** - 若要查看这些异常，必须执行一些[附加设置](app-insights-asp-net-exceptions.md)。

请记住，[主动故障率诊断](app-insights-proactive-failure-diagnostics.md)会自动监视应用响应请求的速率并提供故障代码。 

## <a name="automation"></a>自动化
* [使用 PowerShell 自动设置警报](app-insights-powershell-alerts.md)
* [使用 Webhook 自动响应警报](../monitoring-and-diagnostics/insights-webhooks-alerts.md)

## <a name="video"></a>视频

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="see-also"></a>另请参阅
* [可用性 Web 测试](app-insights-monitor-web-app-availability.md)
* [自动设置警报](app-insights-powershell-alerts.md)
* [主动诊断](app-insights-proactive-diagnostics.md) 

<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[client]: app-insights-javascript.md
[platforms]: app-insights-platforms.md
[roles]: app-insights-resources-roles-access-control.md
[start]: app-insights-overview.md


