---
title: "使用 Azure Application Insights 中的用户流分析用户导航模式 | Microsoft Docs"
description: "分析用户在 Web 应用的页面和功能之间导航的方式。"
services: application-insights
documentationcenter: 
author: numberbycolors
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 08/02/2017
ms.author: cfreeman
ms.openlocfilehash: d17ed3dff08f00a1d6a2108608e42b29f95fbd84
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2017
---
# <a name="analyze-user-navigation-patterns-with-user-flows-in-application-insights"></a>使用 Application Insights 中的用户流分析用户导航模式

![Application Insights 用户流工具](./media/app-insights-usage-flows/flows.png)

用户流工具可视化显示用户在网站的页面和功能之间导航的方式。 它非常适合解释以下问题，例如：
* 用户如何从网站上的某个页面进行导航？
* 用户在网站页面上单击了哪些内容？
* 网站中用户流失最多的地方在哪里？
* 是否存在用户反复重复同一操作的位置？

用户流工具从你指定的初始页面视图或事件开始。 给定此页面视图或自定义事件，用户流显示用户在会话后、两个步骤后等立即发送的页面视图和自定义事件。 不同粗细的线显示用户遵循每条路径的次数。 特殊的“会话结束”节点显示有多少用户在上一个节点之后没有发送页面视图或自定义事件，突出显示用户可能离开网站的位置。



> [!NOTE]
> Application Insights 资源必须包含页面视图或自定义事件才能使用用户流工具。 [了解如何使用 Application Insights JavaScript SDK 将应用设置为自动收集页面访问次数](app-insights-javascript.md)。
> 
> 

## <a name="start-by-choosing-an-initial-page-view-or-custom-event"></a>首先选择初始页面视图或自定义事件

![为用户流选择初始事件](./media/app-insights-usage-flows/flows-initial-event.png)

要使用用户流工具开始回答问题，请选择初始页面视图或自定义事件作为可视化的起点：
1. 单击“用户在...之后的动作？”标题中的链接， 或单击“编辑”按钮。 
2. 从“初始事件”下拉列表中选择页面视图或自定义事件。
3. 单击“创建图形”。

可视化的“步骤 1”列显示了用户在初始事件之后最常见的操作，自上而下按频率从高到低排列。 “步骤 2”和随后的列显示了用户之后的操作，创建了用户在网站中导航的所有方式的图片。

默认情况下，用户流工具只会随机抽取网站最近 24 小时的页面视图和自定义事件。 可在“编辑”菜单中增加时间范围并更改性能平衡和随机抽样的精度。

如果某些页面视图和自定义事件与你无关，请单击要隐藏的节点上的“X”。 选择要隐藏的节点后，单击可视化下方的“创建图形”按钮。 若要查看已隐藏的所有节点，请单击“编辑”按钮，然后查看“已排除事件”部分。

如果预期在可视化中看到的页面视图或自定义事件丢失：
* 检查“编辑”菜单中的“已排除事件”部分。
* 使用“编辑”菜单中的“详细信息级别”控件，在可视化中包含较不频繁的事件。
* 如果用户未频繁发送预期的页面视图或自定义事件，请尝试在“编辑”菜单中增加可视化的时间范围。
* 确保将预期的页面视图或自定义事件设置为由网站源代码中的 Application Insights SDK 收集。 [详细了解如何收集自定义事件。](app-insights-api-custom-events-metrics.md)

如果要在可视化中查看更多步骤，请使用“编辑”菜单中的“步骤数”滑块。

## <a name="after-visiting-a-page-or-feature-where-do-users-go-and-what-do-they-click"></a>用户访问页面或功能后去了哪里，又单击了什么？

![使用用户流，了解用户单击的位置](./media/app-insights-usage-flows/flows-one-step.png)

如果初始事件是页面视图，则可以通过可视化的第一列（“步骤 1”）快速了解用户在访问页面后紧接着执行了哪些操作。 尝试在用户流可视化旁的窗口中打开你的网站。 将你对用户如何与页面进行交互的期望与“步骤 1”列中的事件列表进行比较。 通常，页面上对你的团队来说看似无关紧要的 UI 元素可能会是页面中最常用的。 这对于对网站进行设计改进而言可能是一个很好的起点。

如果初始事件是自定义事件，则第一列显示用户在执行该操作后所做的操作。 与页面视图一样，请考虑观察到的用户行为是否符合你团队的目标和期望。 例如，如果你选择的初始事件是“将物品添加到购物车”，请查看可视化中紧随其后是否出现了“前往结帐”和“完成购买”。 如果用户行为与你的期望大相径庭，请使用可视化来了解用户是如何被网站的当前设计所“困扰”的。

## <a name="where-are-the-places-that-users-churn-most-from-your-site"></a>网站中用户流失最多的地方在哪里？

观察在可视化的列中突出显示的“会话结束”节点，特别是在流的前期。 这意味着许多用户可能会在遵循上述页面和 UI 交互的路径之后从网站流失。 某些流失是可预料的（例如在电子商务网站上完成购买后），但通常流失是网站存在设计问题、性能不佳或其他可以改善的问题的征兆。

请记住，“会话结束”节点仅基于此 Application Insights 资源收集的遥测数据。 如果 Application Insights 未接收某些用户交互的遥测数据，则在用户流工具表示会话结束后，用户可能仍在通过这些方式与网站进行交互。

## <a name="are-there-places-where-users-repeat-the-same-action-over-and-over"></a>是否存在用户反复重复同一操作的位置？

查找可视化后续步骤中的被许多用户重复的页面视图或自定义事件。 这通常意味着用户在网站上执行重复操作。 如果发现重复，请考虑更改网站设计或添加新功能以减少重复。 例如，如果发现用户对表格元素的每一行执行重复操作，则添加批量编辑功能。

## <a name="common-questions"></a>常见问题

### <a name="why-do-steps-appear-disconnected"></a>为什么步骤显示断开连接？

![具有断连步骤的用户流](./media/app-insights-usage-flows/flows-disconnected.png)

如果用户流可视化中的步骤（列）断开连接，则意味着用户在两个步骤之间采用的路径没有足够频繁到需要显示出来。 若要在可视化中显示较不频繁的事件，以便步骤显示连接，请调整“编辑”菜单中的“详细信息级别”滑块。

### <a name="does-the-initial-event-represent-the-first-time-the-event-appears-in-a-session-or-any-time-it-appears-in-a-session"></a>初始事件表示事件首次出现在会话中，还是出现在会话中的任意时刻？

可视化上的初始事件仅表示用户在会话期间首次发送该页面视图或自定义事件。 如果用户可以在会话中多次发送初始事件，则“步骤 1”列仅显示用户在初始事件的第一个实例之后的行为，而不是所有实例。

## <a name="next-steps"></a>后续步骤

* [使用情况概述](app-insights-usage-overview.md)
* [用户、会话和事件](app-insights-usage-segmentation.md)
* [保留](app-insights-usage-retention.md)
* [向应用添加自定义事件](app-insights-api-custom-events-metrics.md)