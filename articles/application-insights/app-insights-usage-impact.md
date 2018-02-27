---
title: "在 Azure Application Insights 中使用 Impact | Microsoft docs"
description: "分析不同的属性对应用部件的转换率造成的潜在影响。"
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 01/25/2018
ms.author: mbullwin ; daviste
ms.openlocfilehash: d76db02647ce878343f60fc84cf063c5b7833438
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/09/2018
---
# <a name="impact-analysis-with-application-insights"></a>Application Insights 中的 Impact 分析功能

Impact 可以分析加载时间和其他属性对应用的各个部件的转换率造成的影响。 更准确地说，Impact 可以发现**页面视图**的**任何维度**、**自定义事件**或**请求**对不同**页面视图**或**自定义事件**造成的影响。 

![Impact 工具](./media/app-insights-usage-impact/0001-impact.png)

## <a name="still-not-sure-what-impact-does"></a>仍不确定 Impact 的作用？

举例来说，假如团队中有人在争论站点某个部件的缓慢运行是否导致用户退出站点，则 Impact 就是解决争论的终极工具。 尽管用户能够容忍一定程度的缓慢，但 Impact 可以提供见解，让你知道如何最好地对优化措施和性能做出平衡，以将用户转换率最大化。

不过，分析性能只是 Impact 的一部分功能。 由于 Impact 支持自定义事件和维度，只需单击几下鼠标，就能解答用户浏览器的选择与不同转换率之间的相关性等问题。

![浏览器转换屏幕截图](./media/app-insights-usage-impact/0004-browsers.png)

> [!NOTE]
> Application Insights 资源必须包含页面视图或自定义事件才能使用 Impact 工具。 [了解如何使用 Application Insights JavaScript SDK 将应用设置为自动收集页面访问次数](app-insights-javascript.md)。 另请注意，由于分析的是相关性，因此样本大小非常重要。
>
>

## <a name="is-page-load-time-impacting-how-many-people-convert-on-my-page"></a>页面加载时间是否影响了页面上的人员转换数量？

若要开始使用 Impact 工具解答问题，请选择初始页面视图、自定义事件或请求。

![Impact 工具](./media/app-insights-usage-impact/0002-dropdown.png)

1. 从“针对页面视图”下拉列表中选择一个页面视图。
2. 将“分析依据”下拉列表中的选择保留为“持续时间”（在此上下文中，“持续时间”是“页面加载时间”的别名）。
3. 在“影响以下对象的使用”下拉列表中，选择一个自定义事件。 此事件应该对应于在步骤 1 中选择的页面视图的 UI 元素。

![结果的屏幕截图](./media/app-insights-usage-impact/0003-results.png)

在此情况下，随着“产品页面”加载时间的增加，“单击‘购买产品’”的转换率下降。 根据上面的分布，可将最佳页面加载持续时间 3.5 秒指定为目标，以实现潜在的 55% 转换率。 可将加载时间降到 3.5 秒以下的其他性能改进措施目前与带来的其他转换优势没有关联。

## <a name="what-if-im-tracking-page-views-or-load-times-in-custom-ways"></a>是否能以自定义的方式跟踪页面视图或加载时间？

Impact 支持标准和自定义的属性与度量值。 可以任意使用这两种方式。 如果不使用持续时间，可以使用基于主要和次要事件的筛选器来获取更具体的结果。

## <a name="do-users-from-different-countries-or-regions-convert-at-different-rates"></a>来自不同国家或地区的用户是否以不同的比率转换？

1. 从“针对页面视图”下拉列表中选择一个页面视图。
2. 在“分析依据”下拉列表中选择“国家或地区”
3. 在“影响以下对象的使用”下拉列表中，选择与在步骤 1 中选择的页面视图中的 UI 元素对应的自定义事件。

在本例中，结果不再像第一个示例中一样拟合到持续 X 轴模型中， 而是显示类似于分段漏斗图的可视化效果。 按“用法”排序可以根据国家/地区查看自定义事件转换的变体。


## <a name="how-does-the-impact-tool-calculate-these-conversion-rates"></a>Impact 工具如何计算这些转换率？

Impact 工具在幕后依赖于 [皮尔逊相关系数] (https://en.wikipedia.org/wiki/Pearson_correlation_coefficient)。 结果是在 -1 与 1 之间计算的，-1 表示零相关，1 表示正相关。

简单而言，Impact Analysis 工作原理的分解如下：

让 _A_ = 在第一个下拉列表中选择的主要页面视图/自定义事件/请求。 （**针对页面视图**）。

让 _B_ = 选择的次要页面视图/自定义事件（**影响以下对象的使用**）。

Impact 会查找选定时间范围内所有用户会话的样本。 对于每个会话，它会查找出现的每个 _A_。

然后，根据以下两个条件之一，将会话分解成两种不同的子会话：

- 转换后的子会话包括以 _B_ 事件结尾的会话，并包含 _B_ 前面发生的所有 _A_ 事件。
- 如果所有 _A_ 事件是在未发生终结性 _B_ 的情况下发生的，则发生未转换的子会话。

Impact 最终计算方式根据是按指标还是按维护分析而异。 使用指标时，将求子会话中所有 _A_ 的平均值。 使用维度时，每个 _A_ 的值为 _B_ 的赋值贡献 _1/N_，其中 _N_ 是子会话中 _A_ 的数目。

## <a name="next-steps"></a>后续步骤

- 若要启用使用体验，请首先发送[自定义事件](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent)或[页面视图](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views)。
- 如果已经发送自定义事件或页面视图，请浏览使用情况工具，了解用户如何使用服务。
    - [漏斗图](usage-funnels.md)
    - [保留](app-insights-usage-retention.md)
    - [用户流](app-insights-usage-flows.md)
    - [工作簿](app-insights-usage-workbooks.md)
    - [添加用户上下文](app-insights-usage-send-user-context.md)