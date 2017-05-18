---
title: "使用 Azure Application Insights 为 Web 应用程序进行用户留存情况分析 | Microsoft docs"
description: "有多少用户回归到你的应用？"
services: application-insights
documentationcenter: 
author: botatoes
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 05/03/2017
ms.author: cfreeman
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 17f6062537714fbef7c8509261ac4875f8a44b6e
ms.contentlocale: zh-cn
ms.lasthandoff: 05/10/2017

---

# <a name="user-retention-analysis-for-web-applications-with-application-insights"></a>使用 Azure Application Insights 为 Web 应用程序进行用户留存情况分析

[Azure Application Insights](app-insights-overview.md) 中的“留存情况”边栏选项卡可以帮助你分析有多少用户回归到你的应用，以及他们以何频率执行特定的任务或达成目标。 例如，如果你运行游戏网站，则可以将在输掉游戏后回归到网站的用户数与在获胜后回归的用户数进行比较。 这一信息可帮助你改进用户体验和业务策略。

## <a name="get-started"></a>入门

如果在 Application Insights 门户的“留存情况”边栏选项卡中没有看到数据，请[了解如何开始使用“使用情况”工具](app-insights-usage-overview.md)。

## <a name="the-retention-tool"></a>留存情况工具

![留存情况工具](./media/app-insights-usage-retention/retention.png)

a. “概述”页面始终位于该位置。 此外，你可以使用不同的参数设置保存已命名报表。 “保存为共享的”可以使报表可供对该资源具有访问权限的其他用户查看。

b. 默认情况下，图表会计入使用了从你的应用收到的任何自定义事件或页面视图的所有用户。 可以选择一个用户或一部分用户来重点关注特定的用户活动。 

c. 添加一个或多个基于属性的筛选器。 例如，可以重点关注特定国家/地区或区域中的用户。 在设置筛选器后单击“更新”。

d. “还原默认值”始终会清除自定义筛选器和事件筛选器。

e. 摘要图表显示了所选时间段内的总数。

f. 网格中显示了在特定时间段内回来重复执行所选操作的用户数。 每行都表示在所显示的时间段内执行了所选操作之一的一群用户。 行中的每个单元格显示了该用户群中有多少人在之后的某个时间段内至少回归了一次。 某些用户可能在多个时间段内都回归过。

在图表的时间范围内使用了该应用的每个人都表示在图表的一个确切行中。 每个用户都是在图表的时间范围内首次执行所选操作时被计入到时间段中。 因此，第一行中的数字通常比较大。


## <a name="use-business-events-to-track-retention"></a>使用业务事件跟踪留存情况

若要获得最有用的留存情况分析，请对表示重要业务活动的事件进行度量。 

例如，许多用户可能会打开应用中的页面，但是却不玩页面中显示的游戏。 因此，仅仅跟踪页面查看数将无法准确估计有多少人在玩过该游戏后又回来玩该游戏。 为了清晰了解回归玩家，你的应用应当在用户实际玩游戏时发送一个自定义事件。  

一种较好的做法是编写表示关键业务操作的自定义事件，并使用这些事件进行留存情况分析。 若要捕获游戏结果，你需要编写一行代码来向 Application Insights 发送一个自定义事件。 如果你采用网页代码或 Node.JS 编写该事件，则它类似于以下内容：

```JavaScript
    appinsights.trackEvent("won game");
```

如果采用 ASP.NET 服务器代码，则如下所示：

```C#
   telemetry.TrackEvent("won game");
```

[详细了解如何编写自定义事件](app-insights-api-custom-events-metrics.md#trackevent)。


## <a name="next-steps"></a>后续步骤

* [使用情况概述](app-insights-usage-overview.md)
* [用户和会话](app-insights-usage-segmentation.md)
* [编写自定义事件](app-insights-api-custom-events-metrics.md)


