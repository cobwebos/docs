---
title: "在 Azure Application Insights 中使用交互式工作簿调查和共享使用情况数据 | Microsoft docs"
description: "对 Web 应用的用户进行人口统计学分析。"
services: application-insights
documentationcenter: 
author: numberbycolors
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 06/12/2017
ms.author: mbullwin
ms.openlocfilehash: 3edaefa942b834a070c55fe28daf60c74ea9f59d
ms.sourcegitcommit: adf6a4c89364394931c1d29e4057a50799c90fc0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/09/2017
---
# <a name="investigate-and-share-usage-data-with-interactive-workbooks-in-application-insights"></a>在 Application Insights 中使用交互式工作簿调查和共享使用情况数据

工作簿将 [Azure Application Insights](app-insights-overview.md) 数据可视化、[Analytics 查询](app-insights-analytics.md)和文本融入交互式文档。 有权访问相同 Azure 资源的其他团队成员可编辑工作簿。 这意味着用于创建工作簿的查询和控件可供阅读工作簿的其他人使用，使其易于浏览、扩展和检查错误。

工作簿对以下情况非常有用，例如：

* 当你事先不知道感兴趣的指标时，浏览应用的使用情况：用户数量、保有率、转换率等。与 Application Insights 中的其他使用情况分析工具不同，工作簿可以结合多个类型的可视化效果和分析，非常适合这种自由探索。
* 通过显示主要交互的用户计数和其他指标，向团队成员说明新发布的功能的作用。
* 在应用中与团队的其他成员共享 A/B 实验的结果。 可以用文本解释实验的目标，然后展示用于评估实验的每个使用情况指标和 Analytics 查询，以及说明每个指标是否高于或低于目标的标注。
* 结合数据、文本说明和后续步骤讨论，报告故障对应用使用的影响，从而防止未来发生故障。

> [!NOTE]
> Application Insights 资源必须包含页面访问次数或自定义事件才能使用工作簿。 [了解如何使用 Application Insights JavaScript SDK 将应用设置为自动收集页面访问次数](app-insights-javascript.md)。
> 
> 

## <a name="editing-rearranging-cloning-and-deleting-workbook-sections"></a>编辑、重新排列、克隆和删除工作簿部分

工作簿是由各个部分组成的：可独立编辑的使用情况可视化效果、图表、表、文本或 Analytics 查询结果。

若要编辑工作簿某个部分的内容，请单击下方工作簿部分右侧的“编辑”按钮。

![Application Insights 工作簿部分编辑控件](./media/app-insights-usage-workbooks/editing-controls.png)

1. 完成编辑某个部分后，点击该部分左下角的“完成编辑”。

2. 创建某个部分的副本，请单击“克隆此部分”图标。 创建副本是迭代查询的绝佳方式，不会丢失以前的迭代。

3. 要上移工作簿中的某个部分，请单击“上移”或“下移”图标。

4. 若要永久删除某个部分，请单击“删除”图标。

## <a name="adding-usage-data-visualization-sections"></a>添加使用情况数据可视化效果部分

工作簿提供四种类型的内置使用情况分析可视化效果。 每一种都解答一个有关应用使用情况的常见问题。 要添加除这四个部分之外的表格和图表，请添加 Analytics 查询部分（请参阅下文）。

若要将用户、会话、事件或保留期部分添加到工作簿中，请使用工作簿底部或任何部分底部的“添加用户”按钮或其他相应按钮。

![工作簿中的用户部分](./media/app-insights-usage-workbooks/users-section.png)

“用户”部分解答“有多少用户查看了某些页面或使用了网站的某些功能？”

“会话”部分解答“用户用了多少次会话来查看某些页面或使用网站的某些功能？”

“事件”部分解答“用户查看某些页面或使用网站某些功能达多少次？”

这三种部分类型中的每一种都提供了相同的控件集和可视化效果集：

* [了解有关编辑用户、会话和事件部分的详细信息](app-insights-usage-segmentation.md)
* 使用位于各部分顶部的“显示图表”、“显示网格”、“显示见解”和“这些用户的示例”和复选框，切换主图表、直方图网格、自动见解和示例用户可视化效果。

![工作簿中的保留期部分](./media/app-insights-usage-workbooks/retention-section.png)

“保留期”部分解答“在某天或某周查看了某些页面或使用了某些功能的人中，在其后一天或一周再次查看或使用的人数？”

* [了解有关编辑“保留期”部分的详细信息](app-insights-usage-retention.md)
* 使用该部分顶部的“显示整体保留期图表”复选框来切换可选的整体保留期图表。

## <a name="adding-application-insights-analytics-sections"></a>添加 Application Insights Analytics 查询

![工作簿中的 Analytics 查询](./media/app-insights-usage-workbooks/analytics-section.png)

若要向工作簿添加 Application Insights Analytics 查询部分，使用工作簿底部或任何部分底部的“添加 Analytics 查询”按钮。

使用 Analytics 查询部分可以将对 Application Insights 数据的任意查询添加到工作簿中。 这种灵活性意味着 Analytics 查询部分（而不是上面列出的四个部分：用户、会话、事件和保留期）用于解答有关网站的问题。例如以下问题：

* 使用率下降的同一时段内，网站引发了多少异常？
* 用户查看某个页面的页面加载时间分布是什么样的？
* 有多少用户在网站上查看了某组页面，而没有查看其它组页面？ 这有助于了解是否有使用不同站点功能子集的用户群集（在 Log Analytics 查询语言中使用具有 `kind=leftanti` 修饰符的 `join` 运算符）。

使用 [Log Analytics 查询语言参考](https://docs.loganalytics.io/)详细了解如何编写查询。

## <a name="adding-text-and-markdown-sections"></a>添加文本和 Markdown 部分

为工作簿添加标题、说明和注释有助于叙述一组表和图表。 工作簿中的文本部分支持用于设置文本格式的 [Markdown 语法](https://daringfireball.net/projects/markdown/)，如标题、粗体、斜体和点符列表。

若要将文本部分添加到工作簿，使用工作簿底部或任何部分底部的“添加文本”按钮。

## <a name="saving-and-sharing-workbooks-with-your-team"></a>保存并与团队共享工作簿

无论是在私有的“我的报表”部分中，还是在有权访问 Application Insights 资源的每个人均可访问的“共享报表”部分中，工作簿都保存在 Application Insights 内。 若要查看资源中的所有工作簿，请单击操作栏中的“打开”按钮。

共享目前在“我的报表”中的工作簿：

1. 在操作栏中，单击“打开”
2. 单击要共享的工作簿旁的“...”按钮
3. 单击“移到共享报表”。

若要通过链接或电子邮件共享工作簿，请单击操作栏中的“共享”。 请记住，链接的收件人需要在 Azure 门户中访问此资源才能查看该工作簿。 若要进行编辑，收件人至少需要资源的参与者权限。

将工作簿的链接固定到 Azure 仪表板：

1. 在操作栏中，单击“打开”
2. 单击需固定的工作簿旁的“...”按钮
3. 单击“固定到仪表板”。

## <a name="next-steps"></a>后续步骤

## <a name="next-steps"></a>后续步骤
- 若要启用使用体验，请首先发送[自定义事件](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent)或[页面视图](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views)。
- 如果已经发送自定义事件或页面视图，请浏览使用情况工具，了解用户如何使用服务。
    - [用户、会话、事件](app-insights-usage-segmentation.md)
    - [漏斗图](usage-funnels.md)
    - [保留](app-insights-usage-retention.md)
    - [用户流](app-insights-usage-flows.md)
    - [添加用户上下文](app-insights-usage-send-user-context.md)
    
