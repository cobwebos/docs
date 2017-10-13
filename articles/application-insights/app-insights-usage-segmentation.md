---
title: "在 Azure Application Insights 中进行用户、会话和事件分析 | Microsoft docs"
description: "对 Web 应用的用户进行人口统计学分析。"
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
ms.author: bwren
ms.openlocfilehash: b154a01d1690bff4950ebc1ff5a5b89894d4d111
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="users-sessions-and-events-analysis-in-application-insights"></a>在 Application Insights 中进行用户、会话和事件分析

查明人们何时使用 Web 应用，他们对哪些页面最感兴趣，用户在哪里以及他们使用什么浏览器和操作系统。 使用 [Azure Application Insights](app-insights-overview.md) 分析业务和使用情况遥测

## <a name="get-started"></a>入门

如果在 Application Insights 门户的“用户”、“会话”或“事件”边栏选项卡中没有看到数据，请[了解如何开始使用“使用情况”工具](app-insights-usage-overview.md)。

## <a name="the-users-sessions-and-events-segmentation-tool"></a>用户、会话和事件分段工具

三个使用情况边栏选项卡使用相同的工具从三个视角对来自 Web 应用的遥测进行切片和切块。 通过对数据进行筛选和拆分，可以洞察不同页面和功能的相对使用情况。

* **“用户”工具**：多少人使用了应用及其功能。  将使用浏览器 cookie 中存储的匿名 ID 对用户进行计数。 使用不同浏览器或计算机的单个用户会被计为多个用户。
* **“会话”工具**：用户活动的多少会话包括了应用的特定页面和功能。 会话是在用户处于不活动状态半小时后或者在连续使用 24 小时后进行计数的。
* **“事件”工具**：应用的特定页面和功能是以何频率使用的。 当浏览器加载了应用中的页面时会计入一次页面查看，前提是已[检测到此操作](app-insights-javascript.md)。 

    自定义事件表示应用中发生的某个事件的一次出现，通常是一项用户交互，例如单击按钮或某项任务完成。 可以在应用中插入代码来[生成自定义事件](app-insights-api-custom-events-metrics.md#trackevent)。

![“使用情况”工具](./media/app-insights-usage-segmentation/users.png)

## <a name="querying-for-certain-users"></a>查询特定用户 

通过在“用户”工具的顶部调整查询选项来探究不同的用户组： 

* 使用者：选择自定义事件和页面视图。 
* 期间：选择一个时间范围。 
* 依据：选择如何划分数据的存储段，可以按时间段或按其他属性（例如浏览器或城市）。 
* 拆分依据：选择对数据进行拆分或分段时要依据的属性。 
* 添加筛选器：将查询限制到特定的用户、会话或事件（根据其属性，例如浏览器或城市）。 
 
## <a name="saving-and-sharing-reports"></a>保存和共享报表 
可以将用户报表保存为在“我的报表”部分中专供你使用的报表，或者将其保存为在“共享报表”部分中可供对此 Application Insights 资源具有访问权限的其他所有人访问的共享报表。  
 
在保存报表或编辑其属性时，选择“当前相对时间范围”可保存带有持续刷新数据的报表，其最近刷新时间与当前时间会相差一个固定的时间量。  
 
选择“当前绝对时间范围”可保存具有固定数据集的报表。 请记住，Application Insights 中的数据仅存储 90 天，因此，如果具有绝对时间范围的报表自保存后已过去了 90 多天，则报表会显示为空。 
 
## <a name="example-instances"></a>示例实例

“示例实例”部分显示与当前查询匹配的少数个体用户、会话或事件的相关信息。 除了聚合之外，考虑并探究个体的行为可以洞察人们使用应用的实际方式。 
 
## <a name="insights"></a>洞察力 

“洞察力”边栏选项卡显示具有共同属性的大型用户群集。 这些群集可以揭示人们对应用的使用方式方面的惊人趋势。 例如，所有使用应用的人中大约有 40% 都使用单项功能。  


## <a name="next-steps"></a>后续步骤
- 若要启用使用体验，请首先发送[自定义事件](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-api-custom-events-metrics#trackevent)或[页面视图](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views)。
- 如果已经发送自定义事件或页面视图，请浏览使用情况工具，了解用户如何使用服务。
    - [漏斗图](usage-funnels.md)
    - [保留](app-insights-usage-retention.md)
    - [用户流](app-insights-usage-flows.md)
    - [工作簿](app-insights-usage-workbooks.md)
    - [添加用户上下文](app-insights-usage-send-user-context.md)

