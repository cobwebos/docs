---
title: "Azure Application Insights 漏斗图"
description: "了解如何使用漏斗图来发现客户与你的应用程序的交互方式。"
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: bwren
ms.translationtype: HT
ms.sourcegitcommit: 0aae2acfbf30a77f57ddfbaabdb17f51b6938fd6
ms.openlocfilehash: 2b25ffd844507074a8467b1135b1331feb9d3ed7
ms.contentlocale: zh-cn
ms.lasthandoff: 08/09/2017

---

# <a name="discover-how-customers-are-using-your-application-with-the-application-insights-funnels"></a>使用 Application Insights 漏斗图了解客户如何使用你的应用程序

了解客户体验对你的业务而言至关重要。 如果你的应用程序涉及多个阶段，那么需要知道是否大多数客户正在完成整个进程，或者他们是否即将在某个时间点结束进程。 Web 应用程序中通过一系列步骤完成的进度被称为“漏斗图”。 Application Insights 漏斗图可用于深入了解你的用户，并监视分步转换率。 

## <a name="get-started-with-the-funnels-blade"></a>开始使用漏斗图边栏选项卡
了解有关漏斗图的最简单的方法是遍历示例。 下图演示了电子商务业务所有者用于了解客户与其 Web 应用程序的交互方式所需的步骤。  

### <a name="create-your-funnel"></a>创建漏斗图
在创建漏斗图之前，需要决定你想要回答的问题。 例如，你可能想要了解正在查看你的主页的客户中，有多少客户会点击一个广告。 在本示例中，Fabrikam Fiber 公司的所有者希望了解上个月内，向购物车添加商品后进行购买的客户百分比。

以下是他们创建漏斗图所执行的步骤。

1. 单击“漏斗图”边栏选项卡上的“新建”按钮。
1. 从“时间范围”下拉菜单选择“上个月”的时间范围。 
1. 从“步骤 1”下拉菜单选择“产品页”事件。 
1. 从“步骤 2”下拉菜单选择“添加到购物车”事件。
1. 从“步骤 3”下拉菜单选择“单击购买”事件。
1. 向漏斗图中添加一个名称，然后单击“保存”。

下图演示漏斗图边栏选项卡生成的数据。 Fabrikam 所有者从此处可以看到，上周内，有 22.7% 的客户在将商品添加到其购物车后完成了购买。 他们还可以看到，1% 的客户在访问产品页前单击了一则广告，并且 20% 的客户在完成购买后进行了注销。


![包含数据的漏斗图边栏选项卡](./media/app-insights-understand-usage-patterns/funnel1.png)

## <a name="next-steps"></a>后续步骤
  * [使用情况概述](app-insights-usage-overview.md)
  * [用户、会话和事件](app-insights-usage-segmentation.md)
  * [保留](app-insights-usage-retention.md)
  * [工作簿](app-insights-usage-workbooks.md)
  * [添加用户上下文](app-insights-usage-send-user-context.md)

