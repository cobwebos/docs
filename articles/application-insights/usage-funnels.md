---
title: "Azure Application Insights 漏斗图"
description: "了解如何使用漏斗图来发现客户与你的应用程序的交互方式。"
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: mbullwin
ms.openlocfilehash: 0396c59d9d95ab71f0af04029d87afbb6e47dc35
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/18/2017
---
# <a name="discover-how-customers-are-using-your-application-with-the-application-insights-funnels"></a>使用 Application Insights 漏斗图了解客户如何使用你的应用程序

了解客户体验对你的业务而言至关重要。 如果你的应用程序涉及多个阶段，那么需要知道是否大多数客户正在完成整个进程，或者他们是否即将在某个时间点结束进程。 Web 应用程序中通过一系列步骤完成的进度被称为“漏斗图”。 Application Insights 漏斗图可用于深入了解你的用户，并监视分步转换率。 

## <a name="create-your-funnel"></a>创建漏斗图
在创建漏斗图之前，需要决定你想要回答的问题。 例如，你可能想要知道多少个用户正在查看主页、查看客户配置文件和创建票证。 在此示例中，Fabrikam Fiber 公司的所有者想要知道成功创建客户票证的客户的百分比。

以下是他们创建漏斗图所执行的步骤。

1. 单击“漏斗图”工具上的“新建”按钮。
1. 从“时间范围”下拉列表中选择“过去 90 天”的时间范围。 选择“我的漏斗图”或“共享漏斗图”
1. 从“步骤 1”下拉列表中选择“索引”事件。 
1. 从“步骤 2”下拉列表中选择“客户”事件。
1. 从“步骤 3”下拉列表中选择“创建”事件。
1. 向漏斗图中添加一个名称，然后单击“保存”。

下图演示“漏斗图”工具生成的数据。 Fabrikam 所有者从此处可以看到，在过去 90 天内，54.3% 访问了主页的客户创建了客户票证。 他们还可以看到 2700 个客户从主页来到索引，这可能指示刷新问题。


![包含数据的漏斗图工具](./media/app-insights-understand-usage-patterns/funnel1.png)

### <a name="funnel-features"></a>漏斗图功能
1. 如果对应用进行采样，将看到采样横幅。 单击该横幅将打开指示如何关闭采样的上下文窗格。 
2. 可以将漏斗图导出到 [Power BI](app-insights-export-power-bi.md)。
3. 单击某一步骤可在右侧获取更深入的信息。 
4. 历史转换显示过去 90 天内的转换。 
5. 从漏斗图转到用户工具可更好地了解用户。 每个步骤都提供精选的用户筛选器。 

## <a name="next-steps"></a>后续步骤
  * [使用情况概述](app-insights-usage-overview.md)
  * [用户、会话和事件](app-insights-usage-segmentation.md)
  * [保留](app-insights-usage-retention.md)
  * [工作簿](app-insights-usage-workbooks.md)
  * [添加用户上下文](app-insights-usage-send-user-context.md)
  * [导出到 Power BI](app-insights-export-power-bi.md)

