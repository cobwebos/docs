---
title: Marketplace Insights-Microsoft 商业市场
description: 访问 marketplace web analytics 的摘要，使你能够在 Microsoft AppSource 和 Azure Marketplace 中衡量客户参与情况。
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/16/2019
ms.openlocfilehash: cecff7347189c5e5b964e10439297bce405810b7
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82857922"
---
# <a name="marketplace-insights-dashboard-in-partner-center"></a>合作伙伴中心的 Marketplace Insights 仪表板

本文提供了有关合作伙伴中心的 Marketplace Insights 仪表板的信息。 此仪表板显示 marketplace web analytics 的摘要，使发布者能够度量 marketplace 店面： Microsoft AppSource 和 Azure Marketplace 上列出的相应产品详细信息页的客户参与情况。

## <a name="marketplace-insights-dashboard"></a>市场见解仪表板

若要访问合作伙伴中心的**Marketplace Insights 仪表板**，请打开 "商业市场" 下的 "**[分析" 选项卡](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)**。

您可以查看以下各项的图形表示形式：  

- [Marketplace Insights 摘要](#marketplace-insights-summary)
- [地域访问](#page-visits-by-geography)  
- [页面访问与独特的访问者趋势](#page-visits-versus-unique-visitors-trend)
- [通过 CTAs 与独特的访问者进行操作](#call-to-action-versus-unique-visitors-with-ctas)
- [页面访问和按产品/服务调用操作](#page-visits-and-calls-to-action-by-offers)
- [调用操作百分比趋势](#call-to-action-percentage-trend)
- [页面访问和引用域对操作的调用](#page-visits-and-calls-to-action-by-referral-domains)
- [Marketplace Insights 详细信息表](#marketplace-insights-details-table)

>[!NOTE]
> 有关分析术语的详细定义，请参阅[商业 marketplace 分析的常见问题和术语](./faq-terminology.md)。

### <a name="insights-dashboard-layout"></a>见解仪表板布局

可以通过多种方式查看 Marketplace 指标：

- 店面选项卡
- 页面筛选器
- 日期筛选器

**店面选项卡**：可以通过 AppSource & Azure Marketplace "选项卡单独查看产品/服务的指标。 从右侧的 "产品/服务" 下拉列表中选择产品/服务，以查看所选产品/服务的指标的可视化效果。 默认情况下，选择所有产品/服务。

![合作伙伴中心见解仪表板产品下拉列表](./media/insights-offer-dropdown.png)

**Insights 页面筛选器**：这些筛选器应用于产品/服务（产品详细信息页）级别。 您可以选择多个筛选器来查看要查看的条件。 此筛选器适用于整个 Marketplace Insights 部分，其中包括图表和详细信息。

![合作伙伴中心见解仪表板页面筛选器](./media/insights-page-filter.png)

- 仅为在所选日期范围内具有页面访问的产品/服务列出产品/服务名称。  
- 每个筛选器选项的默认选择是 "全部"
- 应用的筛选器显示所做选择的选择的计数。 对于默认的 "所有" 选择，将不显示应用的筛选器。

![已应用合作伙伴中心见解筛选器](./media/insights-page-filter-two.png)

**Insights 日期筛选器**：此筛选器适用于整个 Marketplace Insights 部分。 筛选器可以包含预先确定的日期范围或自定义的日期范围。

![合作伙伴中心见解日期筛选器](./media/insights-date-range.png)

## <a name="marketplace-insights-summary"></a>Marketplace Insights 摘要

Marketplace insights 摘要部分显示了所选日期范围内的**页面访问**次数、**操作调用**和**唯一访问者**。

### <a name="page-visits"></a>页面访问

此数字表示所选日期范围内 "产品/服务" 页面上不同用户会话的计数。 红色/绿色百分比指示器表示页面访问的增长百分比。 趋势图表示页面访问的月数。

### <a name="unique-visitors"></a>唯一访问者

此数字表示在 "页面筛选器" 中选择的产品/服务的所选日期范围内的不同访问者计数。 已访问一个或多个产品详细信息页的访问者将被视为一个唯一的访问者。

### <a name="call-to-action"></a>行动号召

此数字表示 "产品/服务" 页（产品详细信息页）上的 "操作" 按钮的 "**调用**次数"。 如果选择 "**立即获取**"、"**免费试用**"、"**与我联系**" 和 "**测试驱动器**" 按钮，将对**操作**进行计数。

![合作伙伴中心见解对操作摘要的调用](./media/insights-summary.png)

## <a name="page-visits-by-geography"></a>地域访问

下面的热度地图显示了**根据客户所在国家/地区**的**页面访问**数、**操作调用**和唯一访问者。 较高的页面访问由较暗的地图颜色表示，较低的页面访问由较浅的地图颜色表示。

![合作伙伴中心见解地理分布](./media/insights-geography.png)

热度地图包括以下功能：

- 热度地图有一个补充网格，可用于查看**页面访问**的详细信息、对**操作的调用**和特定位置中的**唯一访问者**;如果需要，可以缩放到特定位置。  
- "**国家/地区**" 是客户在所选日期范围内报告页面访问的所有国家/地区的计数。
- 您可以搜索并选择网格中的国家/地区以放大地图中的位置。 通过在地图上选择 "**主页**"，还原到原始视图。

## <a name="page-visits-versus-unique-visitors-trend"></a>页面访问与独特的访问者趋势

下面的列表示每月页访问的计数，它们显示在 Y 轴上（图表左侧的轴）。 趋势线表示唯一访问者的每月趋势，在 "商店： Azure Marketplace" 和 "AppSource" 中发布的产品/服务，它显示在辅助 Y 轴（图表右侧的轴）上。

![合作伙伴中心见解页面访问与独特的访问者趋势](./media/insights-page-vists-unique-visitors.png)

## <a name="call-to-action-versus-unique-visitors-with-ctas"></a>通过 CTAs 与独特的访问者进行操作

堆积列表示对操作（CTA）的每月调用数，按 CTA 类型（**立即获取**、"**联系我**" 和 "**免费试用**"）进行细分，并绘制在 Y 轴上（页面左侧的轴）。 趋势线表示具有 CTAs 的唯一访问者的每月趋势，此趋势显示在 Azure Marketplace 中发布的产品和 AppSource 中的辅助 Y 轴（在图表右侧的轴上）。

![合作伙伴中心见解通过 CTAs 与独特的访问者进行操作](./media/insights-call-to-action-unique-visitors.png)

## <a name="page-visits-and-calls-to-action-by-offers"></a>页面访问和按产品对操作的调用

外部饼图根据你在 marketplace 中发布并在筛选器中选择的产品/服务来表示**页面访问**的细分。 内部图表示对相同产品的操作细目的**调用**。

![合作伙伴中心见解页访问，并按产品/服务调用操作](./media/insights-page-visits-and-cta-by-offer.png)

## <a name="call-to-action-percentage-trend"></a>调用操作百分比趋势

对**操作百分比趋势的调用**显示在 marketplace 中发布的产品/服务的 CTA 百分比。 CTA% = （CTAs/page 就诊） * 100。

![合作伙伴中心见解调用操作百分比趋势](./media/insights-call-to-action-percentage-trend.png)

## <a name="page-visits-and-calls-to-action-by-referral-domains"></a>页面访问和引用域对操作的调用

下图显示了前50个引用域。 选择特定的引用域将显示页面访问的每月趋势，并在右侧的图表上调用操作。

![合作伙伴中心见解页面访问和通过推荐域和活动进行操作](./media/insights-page-visits-call-to-actions.png)

## <a name="marketplace-insights-details-table"></a>Marketplace Insights 详细信息表

此表提供了页面访问的列表视图，以及按日期排序的所选产品/服务的操作。

![合作伙伴中心见解详细信息表](./media/insights-details-page.png)

- 如果记录计数小于1000，则可以将数据提取到 CSV 文件中。
- 如果记录计数超过1000，则导出的数据将在接下来的30天内异步置于下载页中。
- 可以应用筛选器来显示你感兴趣的数据。 可以按产品/服务名称和市场活动名称来筛选数据。  

## <a name="next-steps"></a>后续步骤

- 有关合作伙伴中心商用 marketplace 中可用的分析报表的概述，请参阅[合作伙伴中心的专业市场分析](./analytics.md)。
- 有关为你的产品/服务汇总市场活动的聚合数据的图形、趋势和值，请参阅[商业 marketplace 分析中的摘要仪表板](./summary-dashboard.md)。
- 有关以图形和可下载格式显示的订单的信息，请参阅[商业 marketplace 分析中的 "订单" 仪表板](./orders-dashboard.md)。
- 对于虚拟机（VM）提供使用情况和计量计费指标，请参阅[商业 marketplace 分析中的使用情况仪表板](./usage-dashboard.md)。
- 有关客户的详细信息，包括增长趋势，请参阅[商业 marketplace 分析中的客户仪表板](./customer-dashboard.md)。
- 有关过去30天内下载请求的列表，请参阅[商业 marketplace 分析中的下载仪表板](./downloads-dashboard.md)。
- 若要查看有关 Azure Marketplace 和 AppSource 上的产品/服务的客户反馈的合并视图，请参阅[商业 marketplace 分析中的评级和评论仪表板](./ratings-reviews.md)。
- 有关商业 marketplace 分析的常见问题和全面的数据术语字典，请参阅[商业市场分析的常见问题和术语](./faq-terminology.md)。
