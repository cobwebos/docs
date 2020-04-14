---
title: 合作伙伴中心商业市场分析中的市场洞察仪表板
description: 访问市场 Web 分析摘要，使发布者能够在 AppSource 和 Azure 应用商店中衡量客户参与度。
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/16/2019
ms.openlocfilehash: 05389f0aea7e2637dd28281236e7fd7ec2b412fd
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/13/2020
ms.locfileid: "81251701"
---
# <a name="marketplace-insights-dashboard-in-commercial-marketplace-analytics"></a>商业市场分析中的市场洞察仪表板

本文在合作伙伴中心中的"市场洞察"仪表板上提供了信息。 此仪表板显示市场 Web 分析摘要，使发布者能够针对市场网店上列出的相应产品详细信息页面（AppSource 和 Azure 应用商店）衡量客户参与度。

## <a name="marketplace-insights-dashboard"></a>市场见解仪表板

要访问合作伙伴中心中的 **"市场洞察"仪表板**，请打开"商业市场"下的**["分析"选项卡](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)**。

您可以查看以下项目的图形表示形式：  

- [市场洞察摘要](#marketplace-insights-summary)
- [按地理位置划分的页面访问](#page-visits-by-geography)  
- [页面访问与独特的访问者趋势](#page-visits-versus-unique-visitors-trend)
- [行动号召与具有 CTA 的独特访问者](#call-to-action-versus-unique-visitors-with-ctas)
- [按优惠进行页面访问和行动呼吁](#page-visits-and-calls-to-action-by-offers)
- [行动号召百分比趋势](#call-to-action-percentage-trend)
- [按引用域进行的页面访问和行动调用](#page-visits-and-calls-to-action-by-referral-domains)
- [市场洞察详细信息表](#marketplace-insights-details-table)

>[!NOTE]
> 有关分析术语的详细定义，请参阅[商业市场分析的常见问题和术语](./faq-terminology.md)。

### <a name="insights-dashboard-layout"></a>见解仪表板布局

您可以通过多种方式查看应用商店指标：

- 店面选项卡
- 页面筛选器
- 日期筛选器

**网店选项卡**：您可以通过 AppSource & Azure 应用商店选项卡单独查看产品/服务指标。 从右侧的优惠下拉列表中选择产品/服务，以查看所选产品/服务指标的可视化效果。 默认情况下，将选择所有优惠。

![合作伙伴中心见解仪表板提供下拉列表](./media/insights-offer-dropdown.png)

**见解页筛选器**：这些筛选器在产品/服务（产品详细信息页）级别应用。 您可以为要查看的条件选择多个筛选器。 此筛选器适用于整个应用商店洞察部分，包括图表和详细信息。

![合作伙伴中心见解仪表板页面筛选器](./media/insights-page-filter.png)

- 优惠名称仅列出在所选日期范围内具有页面访问次数的优惠。  
- 每个筛选器选项的默认选择为"全部"
- 应用的筛选器显示所选内容的选择计数。 对于默认的"全部"选择，不会显示应用的筛选器。

![应用合作伙伴中心见解筛选器](./media/insights-page-filter-two.png)

**见解日期筛选器**：此筛选器适用于整个应用商店洞察部分。 筛选器可以包括预先确定的日期范围或自定义日期范围。

![合作伙伴中心洞察日期筛选器](./media/insights-date-range.png)

## <a name="marketplace-insights-summary"></a>市场洞察摘要

"市场洞察摘要"部分显示所选日期范围的**页面访问**次数、**行动号召**量**和唯一访问者**数。

### <a name="page-visits"></a>页面访问

此数字表示所选日期范围的报价页（产品详细信息页）上不同用户会话的计数。 红色/绿色百分比指标表示页面访问量的增长百分比。 趋势图表示页面访问量的逐月数。

### <a name="unique-visitors"></a>独特的访客

此数字表示页面筛选器中选择的要约在所选日期范围内的不同访问者计数。 访问过一个或多个产品详细信息页面的访问者将被视为一个唯一访问者。

### <a name="call-to-action"></a>行动号召

此数字表示在产品/服务页面（产品详细信息页）上完成的 **"行动号召"** 按钮单击数。 当选择"**立即获取"、****免费试用**、**与我联系**和**测试驱动器**按钮时，将计算**行动**调用。

![合作伙伴中心洞察行动摘要](./media/insights-summary.png)

## <a name="page-visits-by-geography"></a>按地理位置划分的页面访问

下面的热图显示**页面访问**计数、**行动号召**次数和**根据客户国家/地区进行的独特访问者**。 较高的页面访问由较暗的地图颜色表示，较低的页面访问以较浅的地图颜色表示。

![合作伙伴中心洞察地理分布](./media/insights-geography.png)

热图包括以下功能：

- 热图有一个补充网格，用于查看特定位置**的页面访问**、**行动号召**和**唯一访问者**的详细信息;如果愿意，可以放大到特定位置。  
- **国家/地区分布**是客户在所选日期范围内报告页面访问情况的所有国家/地区的计数。
- 您可以在网格中搜索并选择国家/地区以放大地图中的位置。 通过在地图上选择 **"主页"，** 还原到原始视图。

## <a name="page-visits-versus-unique-visitors-trend"></a>页面访问与独特的访问者趋势

下面的列表示每月页面访问计数，这些访问显示在 Y 轴（图表左侧的轴）。 趋势线表示唯一访问者的每月趋势，显示在二级 Y 轴（图表右侧的轴）上，用于在网店：Azure 应用商店和 AppSource 上发布的产品。

![合作伙伴中心洞察页面访问与独特的访问者趋势](./media/insights-page-vists-unique-visitors.png)

## <a name="call-to-action-versus-unique-visitors-with-ctas"></a>行动号召与具有 CTA 的独特访问者

堆叠列表示按 CTA 类型（**立即获取**、**联系我**和自由**试用**）细分的每月行动调用 （CTA），并在 Y 轴（页面左侧的轴）上绘制。 趋势线表示具有 CTA 的唯一访问者的每月趋势，该趋势显示在在 Azure 应用商店和 AppSource 上发布的辅助 Y 轴（图表右侧的轴）上。

![合作伙伴中心洞察行动呼吁与具有 CTA 的独特访问者](./media/insights-call-to-action-unique-visitors.png)

## <a name="page-visits-and-calls-to-action-by-offers"></a>按优惠进行页面访问和行动呼吁

外饼图表示基于您在市场中发布并在筛选器中选择的优惠的**主页访问**细分。 内部图表表示相同优惠的操作**调用**细分。

![合作伙伴中心见解页面访问和按优惠要求采取行动](./media/insights-page-visits-and-cta-by-offer.png)

## <a name="call-to-action-percentage-trend"></a>行动号召百分比趋势

**"行动号召"百分比趋势**显示市场上发布的优惠的 CTA 百分比。 CTA % = （CTA/页面访问次数） = 100。

![合作伙伴中心洞察对行动百分比趋势的呼唤](./media/insights-call-to-action-percentage-trend.png)

## <a name="page-visits-and-calls-to-action-by-referral-domains"></a>按引用域进行的页面访问和行动调用

下图显示了前 50 个引用域。 选择特定的引用域会显示页面访问和右侧图表上的操作调用的每月趋势。

![合作伙伴中心见解页面访问和按推荐域和活动调用行动](./media/insights-page-visits-call-to-actions.png)

## <a name="marketplace-insights-details-table"></a>市场洞察详细信息表

此表提供页面访问的列表视图以及所选优惠按日期排序的操作调用。

![合作伙伴中心见解详细信息表](./media/insights-details-page.png)

- 如果记录计数小于 1000，则可以将数据提取到 CSV 文件。
- 如果记录计数超过 1000，则导出的数据将在未来 30 天内异步放置在下载页中。
- 可以应用筛选器来显示您感兴趣的数据。 数据可以通过产品/服务名称和市场活动名称进行筛选。  

## <a name="next-steps"></a>后续步骤

- 有关合作伙伴中心商业市场中可用的分析报告的概述，请参阅[合作伙伴中心中商业市场的分析](./analytics.md)。
- 有关汇总产品/服务市场活动的汇总数据的图表、趋势和值，请参阅[商业市场分析中的摘要仪表板](./summary-dashboard.md)。
- 有关您的订单以图形和可下载格式的信息，请参阅[商业市场分析中的订单仪表板](./orders-dashboard.md)。
- 有关虚拟机 （VM） 提供使用情况和按流量计费指标，请参阅[商业市场分析中的使用情况仪表板](./usage-dashboard.md)。
- 有关客户的详细信息（包括增长趋势），请参阅[商业市场分析中的客户仪表板](./customer-dashboard.md)。
- 有关过去 30 天的下载请求列表，请参阅[商业市场分析中的下载仪表板](./downloads-dashboard.md)。
- 要查看 Azure 应用商店和 AppSource 上产品/服务的客户反馈的合并视图，请参阅[商业市场分析中的评级和审核仪表板](./ratings-reviews.md)。
- 有关商业市场分析和数据术语综合词典的常见问题，请参阅[商业市场分析的常见问题和术语](./faq-terminology.md)。
