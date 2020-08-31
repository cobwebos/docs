---
title: Marketplace Insights-Microsoft 商用 marketplace、Microsoft AppSource 和 Azure Marketplace
description: 访问市场 Web 分析摘要，它可以让你衡量客户在 Microsoft AppSource 和 Azure 市场中的参与度。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 07/22/2019
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: 1a645a333db9b24005639f4adbb2913a2b887b66
ms.sourcegitcommit: 420c30c760caf5742ba2e71f18cfd7649d1ead8a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2020
ms.locfileid: "89055662"
---
# <a name="marketplace-insights-dashboard-in-partner-center"></a>合作伙伴中心内的市场见解仪仪表板

本文提供有关合作伙伴中心内的市场见解仪表板的信息。 此仪表板显示 marketplace web analytics 的摘要，使发布者能够度量商业 marketplace 在线商店： Microsoft AppSource 和 Azure Marketplace 中列出的相应产品详细信息页的客户参与情况。

## <a name="marketplace-insights-dashboard"></a>市场见解仪表板

要访问合作伙伴中心内的“市场见解仪表板”，请打开商业市场下的[“分析”选项卡](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary) 。

你可以查看以下各项的图形表示形式：  

- [市场见解摘要](#marketplace-insights-summary)
- [按地理位置列出的页面访问次数](#page-visits-by-geography)  
- [页面访问次数与唯一访问者数量趋势](#page-visits-versus-unique-visitors-trend)
- [通过 CTAs (CTA) 与唯一访问者的操作进行调用](#call-to-action-versus-unique-visitors-with-ctas)
- [页面访问次数和按产品/服务列出的行动号召单击次数](#page-visits-and-calls-to-action-by-offers)
- [行动号召单击次数百分比趋势](#call-to-action-percentage-trend)
- [页面访问次数和按推荐域列出的行动号召单击次数](#page-visits-and-calls-to-action-by-referral-domains)
- [市场见解详细信息表](#marketplace-insights-details-table)

用户访问 Azure Marketplace 上的产品/服务和合作伙伴中心的 AppSource 和报表之间的最大延迟为48小时。

>[!NOTE]
> 有关分析术语的详细定义，请参阅[适用于商业市场分析的常见问题和术语](./faq-terminology.md)。

### <a name="insights-dashboard-layout"></a>见解仪表板布局

以各种方式查看商业市场指标：

- 在线商店选项卡
- 页面筛选器
- 日期筛选器

**在线商店选项卡**：可以通过 AppSource & Azure Marketplace "选项卡单独查看产品/服务的指标。 从右侧的产品/服务下拉列表中选择产品/服务，以查看所选产品/服务指标的可视化效果。 默认情况下，所有产品/服务均处于选中状态。

![合作伙伴中心见解仪表板产品/服务下拉列表](./media/insights-offer-dropdown.png)

**“见解”页筛选器**：这些筛选器应用于产品/服务（产品详细信息页）级别。 你可以为要查看的条件选择多个筛选器。 此筛选器应用于整个市场见解部分，包括图表和详细信息。

![合作伙伴中心见解“仪表板”页筛选器](./media/insights-page-filter.png)

- 仅会列出在所选日期范围内具有页面访问次数的产品/服务。  
- 每个筛选器选项的默认选择都是“全部”
- 应用的筛选器显示所选内容的选择计数。 不会为默认“所有”所选内容显示应用的筛选器。

![应用合作伙伴中心“见解”筛选器的效果](./media/insights-page-filter-two.png)

**“见解日期”筛选器**：此筛选器适用于整个市场见解部分。 筛选器可以包括预先确定的日期范围或自定义的日期范围。

![合作伙伴中心“见解日期”筛选器](./media/insights-date-range.png)

## <a name="marketplace-insights-summary"></a>市场见解摘要

Marketplace Insights 摘要部分显示了所选日期范围内的 **页面访问**次数、 **操作调用**和 **唯一访问者** 。

>[!NOTE]
>Marketplace Insights 仪表板提供点击流数据，不应与潜在客户目标终结点中生成的潜在顾客关联。

### <a name="page-visits"></a>页面访问次数

此数字表示所选日期范围内产品/服务页（产品详细信息页）上不同用户会话的计数。 红色/绿色百分比指标表示页面访问次数的增长百分比。 趋势图表示逐月的页面访问次数。

### <a name="unique-visitors"></a>唯一访问者数量

此数字表示页面筛选器中选择的产品/服务在所选日期范围内的不同访问者计数。 访问过一个或多个产品详细信息页面的访问者将被视为一名唯一访问者。

### <a name="call-to-action"></a>行动号召

此数字表示在产品/服务页面（产品详细信息页）上完成的行动号召按钮单击计数。 当用户选择 "**立即获取**"、"**免费试用**"、"**与我联系**" 或 "**测试驱动器**" 按钮时，将对**操作**进行计数。

![合作伙伴中心见解“行动号召”摘要](./media/insights-summary.png)

## <a name="page-visits-by-geography"></a>按地理位置列出的页面访问次数

下面的热度地图显示“页面访问次数”、“行动号召单击次数”和“根据客户所在国家/地区列出的唯一访问者数量”  。 较高的页面访问次数以较暗的地图颜色表示，较低的页面访问次数以较浅的地图颜色表示。

![合作伙伴中心见解“地理分布”](./media/insights-geography.png)

热度地图具有以下功能：

- 热度地图有一个补充网格，可用于查看特定位置中的“页面访问次数”、“行动号召单击次数”和“唯一访问者数量”的详细信息；如果需要，你可以放大特定位置  。  
- “国家/地区分布”是在所选日期范围内报告客户页面访问次数的所有国家/地区数。
- 你可以在网格中搜索并选择国家/地区以在地图中放大到该位置。 可以通过选择地图上的“主页”，还原到原始视图。

## <a name="page-visits-versus-unique-visitors-trend"></a>页面访问次数与唯一访问者数量趋势

下面的柱形表示每月的页面访问计数，这些访问次数显示在 Y 轴（图表左侧的轴）上。 趋势线表示唯一访问者的每月趋势，在图表) 右侧的辅助 Y 轴 (轴上显示，对于在线商店中发布的产品： Azure Marketplace 和 AppSource。

![合作伙伴中心见解“页面访问次数与唯一访问者数量趋势”](./media/insights-page-vists-unique-visitors.png)

## <a name="call-to-action-versus-unique-visitors-with-ctas"></a>行动号召单击次数与单击 CTA 的唯一访问者数量

堆积柱形表示按 CTA 类型（“立即获取”、“与我联系”和“免费试用”）细分的每月行动号召 (CTA) 单击次数，绘制在 Y 轴（页面左侧的轴）上  。 趋势线表示单击 CTA 的唯一访问者数量的每月趋势，该趋势显示在辅助 Y 轴（图表右侧的轴）上，适用的对象是你在 Azure 市场和 AppSource 中发布的产品/服务。

![合作伙伴中心见解“行动号召单击次数与单击 CTA 的唯一访问者数量”](./media/insights-call-to-action-unique-visitors.png)

## <a name="page-visits-and-calls-to-action-by-offers"></a>页面访问次数和按产品/服务列出的行动号召单击次数

外饼图表示基于你在市场中发布并在筛选器中选择的“页面访问次数”的明细。 内部图表表示针对相同的产品/服务的“行动号召单击次数”的细分。

![合作伙伴中心见解“页面访问次数和按产品/服务列出的行动号召单击次数”](./media/insights-page-visits-and-cta-by-offer.png)

## <a name="call-to-action-percentage-trend"></a>行动号召单击次数百分比趋势

“行动号召单击次数百分比趋势”显示针对市场中发布的产品/服务的 CTA 单击次数百分比。 CTA % =（CTA 单击次数/页面访问次数）* 100。

![合作伙伴中心见解“行动号召单击次数百分比趋势”](./media/insights-call-to-action-percentage-trend.png)

## <a name="page-visits-and-calls-to-action-by-referral-domains"></a>页面访问次数和按推荐域列出的行动号召单击次数

下图显示了排名前 50 位的推荐域。 选择特定的推荐域会在图表右侧显示页面访问次数和行动号召单击次数的每月趋势。

![合作伙伴中心见解“页面访问次数和按推荐域及营销活动列出的行动号召单击次数”](./media/insights-page-visits-call-to-actions.png)

## <a name="marketplace-insights-details-table"></a>市场见解详细信息表

此表提供页面访问次数以及所选产品/服务按日期排序的行动号召单击次数的列表视图。

![合作伙伴中心见解详细信息表](./media/insights-details-page.png)

- 如果记录的条数少于 1000，则可以将数据提取到 CSV 文件中。
- 如果记录的条数超过 1000，则导出的数据在接下来的 30 天会异步放置在下载页面中。
- 按产品名称和活动名称筛选数据，以显示你感兴趣的数据。

## <a name="next-steps"></a>后续步骤

- 若要大致了解合作伙伴中心商业市场内可用的分析报表，请参阅[合作伙伴中心内的商业市场分析](./analytics.md)。
- 有关为产品/服务汇总市场活动的聚合数据的图表、趋势和值，请参阅[商业市场分析中的“摘要”仪表板](./summary-dashboard.md)。
- 有关采用图形和可下载格式显示的订单的信息，请参阅[商业市场分析中的“订单”仪表板](./orders-dashboard.md)。
- 有关虚拟机 (VM) 产品/服务使用情况和按流量计费指标的信息，请参阅[商业市场分析中的“使用情况”仪表板](./usage-dashboard.md)。
- 有关客户的详细信息（包括增长趋势），请参阅[商业市场分析中的“客户”仪表板](./customer-dashboard.md)。
- 若要获取过去 30 天内的下载请求列表，请参阅[商业市场分析中的“下载”仪表板](./downloads-dashboard.md)。
- 若要查看客户对 Azure 市场和 AppSource 中产品/服务的反馈的综合视图，请参阅[商业市场分析中的“评级和评论”仪表板](./ratings-reviews.md)。
- 有关商业市场分析的常见问题解答以及数据术语的综合词典，请参阅[商业市场分析的常见问题解答和术语](./faq-terminology.md)。
