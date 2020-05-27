---
title: Azure Application Insights 漏斗图
description: 了解如何使用漏斗图来发现客户与你的应用程序的交互方式。
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 07/17/2017
ms.reviewer: mbullwin
ms.openlocfilehash: 09c1899004bd93d0f2365c3bbc000c37459e8d89
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/22/2020
ms.locfileid: "83797834"
---
# <a name="discover-how-customers-are-using-your-application-with-application-insights-funnels"></a>使用 Application Insights 漏斗图了解客户如何使用你的应用程序

了解客户体验对你的业务而言至关重要。 如果你的应用程序涉及多个阶段，那么需要知道是否大多数客户正在完成整个进程，或者他们是否即将在某个时间点结束进程。 Web 应用程序中通过一系列步骤完成的进度被称为“漏斗图”。 Azure Application Insights 漏斗图可用于深入了解你的用户，并监视分步转换率。 

## <a name="create-your-funnel"></a>创建漏斗图
在创建漏斗图之前，需要决定想要回答的问题。 例如，你可能想要知道多少个用户正在查看主页、查看客户配置文件和创建票证。 在此示例中，Fabrikam Fiber 公司的所有者想要知道成功创建客户票证的客户的百分比。

以下是他们创建漏斗图所执行的步骤。

1. 在 Application Insights 漏斗图工具中，选择“新建”。
1. 从“时间范围”下拉菜单中，选择“最近 90 天”。  选择“我的漏斗图”或“共享漏斗图”。 
1. 从“步骤 1”下拉列表中选择“索引”。 
1. 从“步骤 2”列表中选择“客户”。 
1. 从“步骤 3”列表中选择“创建”。 
1. 向漏斗图中添加一个名称，然后选择“保存”。

以下屏幕截图显示漏斗图工具生成的数据类型示例。 Fabrikam 所有者从此处可以看到，在过去 90 天内，54.3% 访问了主页的客户创建了客户票证。 他们还可以查看 2,700 个客户从主页进入索引。 这可能表示刷新问题。


![包含数据的漏斗图工具屏幕截图](media/usage-funnels/funnel1.png)

### <a name="funnels-features"></a>漏斗图功能
上面的屏幕截图包括五个突出显示的区域。 这些是漏斗图的功能。 以下列表更详细说明了屏幕截图中的每个相应区域：
1. 如果对应用进行采样，将看到采样横幅。 选择标题栏打开上下文窗格，其中说明了如何关闭采样。 
2. 可以将漏斗图导出到 [Power BI](../../azure-monitor/app/export-power-bi.md )。
3. 选择一个步骤以在右侧查看更多详细信息。 
4. 历史转换图显示过去 90 天内的兑换率。 
5. 通过访问用户工具更好地了解用户。 可在每个步骤中使用筛选器。 

## <a name="next-steps"></a>后续步骤
  * [使用情况概述](usage-overview.md)
  * [用户、会话和事件](usage-segmentation.md)
  * [保留](usage-retention.md)
  * [工作簿](../../azure-monitor/platform/workbooks-overview.md)
  * [添加用户上下文](usage-send-user-context.md)
  * [导出到 Power BI](../../azure-monitor/app/export-power-bi.md )

