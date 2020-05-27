---
title: 在 Azure Application Insights 中进行用户、会话和事件分析
description: 对 Web 应用的用户进行人口统计学分析。
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 01/24/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 10ea3c68695e7e4fb4950fbcc4a190e9f1d83f49
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/22/2020
ms.locfileid: "83797701"
---
# <a name="users-sessions-and-events-analysis-in-application-insights"></a>在 Application Insights 中进行用户、会话和事件分析

查明人们何时使用 Web 应用，他们对哪些页面最感兴趣，用户在哪里以及他们使用什么浏览器和操作系统。 使用 [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) 分析业务和使用情况遥测

![Application Insights 用户的屏幕截图](./media/usage-segmentation/0001-users.png)

## <a name="get-started"></a>入门

如果在 Application Insights 门户的“用户”、“会话”或“事件”边栏选项卡中没有看到数据，请[了解如何开始使用“使用情况”工具](usage-overview.md)。

## <a name="the-users-sessions-and-events-segmentation-tool"></a>用户、会话和事件分段工具

三个使用情况边栏选项卡使用相同的工具从三个视角对来自 Web 应用的遥测进行切片和切块。 通过对数据进行筛选和拆分，可以洞察不同页面和功能的相对使用情况。

* **“用户”工具**：多少人使用了应用及其功能。  将使用浏览器 cookie 中存储的匿名 ID 对用户进行计数。 使用不同浏览器或计算机的单个用户会被计为多个用户。
* **“会话”工具**：用户活动的多少会话包括了应用的特定页面和功能。 会话是在用户处于不活动状态半小时后或者在连续使用 24 小时后进行计数的。
* **“事件”工具**：应用的特定页面和功能是以何频率使用的。 当浏览器加载了应用中的页面时会计入一次页面查看，前提是已[检测到此操作](../../azure-monitor/app/javascript.md)。 

    自定义事件表示应用中发生的某个事件的一次出现，通常是一项用户交互，例如单击按钮或某项任务完成。 可以在应用中插入代码来[生成自定义事件](../../azure-monitor/app/api-custom-events-metrics.md#trackevent)。

## <a name="querying-for-certain-users"></a>查询特定用户

通过在“用户”工具的顶部调整查询选项来探究不同的用户组：

* 显示：选择要分析的一组用户。
* 使用者：选择自定义事件和页面视图。
* 期间：选择一个时间范围。
* 依据：选择如何划分数据的存储段，可以按时间段或按其他属性（例如浏览器或城市）。
* 拆分依据：选择对数据进行拆分或分段时要依据的属性。 
* 添加筛选器：将查询限制到特定的用户、会话或事件（根据其属性，例如浏览器或城市）。 
 
## <a name="saving-and-sharing-reports"></a>保存和共享报表 
可以将用户报表保存为在“我的报表”部分中专供你使用的报表，或者将其保存为在“共享报表”部分中可供对此 Application Insights 资源具有访问权限的其他所有人访问的共享报表。

若要共享“用户”、“会话”或“事件”报表的链接，请单击工具栏中的“共享”  ，然后复制该链接。

若要共享“用户”、“会话”或“事件”报表中的数据副本，请单击工具栏中的“共享”  ，然后单击“Word 图标”  使用该数据创建一个 Word 文档。 或者，单击主图表上方的“Word 图标”  。

## <a name="meet-your-users"></a>了解用户

“了解用户”  部分显示与当前查询匹配的五个示例用户的相关信息。 除了聚合之外，考虑并探究个体的行为可以洞察人们使用应用的实际方式。

## <a name="next-steps"></a>后续步骤

- 若要启用使用体验，请首先发送[自定义事件](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent)或[页面视图](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views)。
- 如果已经发送自定义事件或页面视图，请浏览使用情况工具，了解用户如何使用服务。
    - [漏斗图](usage-funnels.md)
    - [保留](usage-retention.md)
    - [用户流](usage-flows.md)
    - [工作簿](../../azure-monitor/platform/workbooks-overview.md)
    - [添加用户上下文](usage-send-user-context.md)
